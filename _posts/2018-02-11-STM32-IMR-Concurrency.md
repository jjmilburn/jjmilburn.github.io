---
layout: post
title: STM32 Interrupt Mask Register Concurrency
---

How not to concurrently modify the IMR register on an STM32

-----

While auditing code for interrupt-related problems recently, an experienced colleague noted the possibility of
concurrently modifying the [Interrupt Mask Register](http://www.st.com/resource/en/reference_manual/dm00091010.pdf) (IMR, see section 11.3.1) on an STM32 target.

This register is used to enable or disable interrupts, and in the project in question, this functionality is used
to enable a software-filtering of noise on external hardware interrupts.  Specifically, if the interrupt fires on
an external GPIO pin rising/falling edge, a task is scheduled to check the GPIO pin level after a certain period elapses, and the IMR is used to mask/disable the interrupt.

However, there are multiple interrupts which will modify the IMR register, e.g.:

```
(Inside lower-priority interrupt 'A' handler)
EXTI->IMR &= ~PINOUT_EXTERNAL_HARDWARE_INTERRUPT_A;
```

```
(Inside higher-priority interrupt 'B' handler)
EXTI->IMR &= ~PINOUT_EXTERNAL_HARDWARE_INTERRUPT_B;
```

If the bitwise `&=` operation on the IMR was an atomic operation, this wouldn't be a concern.  However, examining the assembler code indicated that this was not the case. For the sake of discussion, lets assume interrupt A is the least-significant bit of the IMR register, and interrupt B is the next most significant bit:


* IMR: 0b0001 == 'Interrupt A' bit
* IMR: 0b0010 == 'Interrupt B' bit

And, lets assume the initial IMR state is that both A and B are active and the IMR is not masked, as below:

* IMR Initial State: 0b0011 == Interrupts A and B both 'active'/unmasked

While *unlikely*, it is theoretically possible that the following scenario occurs:

0. IMR is initially set such that both interrupts A and B are active (`IMR=0b0011`)
1. Interrupt A fires, and stores the value of `EXTI->IMR` to a temporary register. It then performs a bitwise ANDoperation on the temporary register using the value of `~PINOUT_EXTERNAL_HARDWARE_INTERRUPT_A`.  This temporary register takes on the value `0b0010`, masking interrupt "A".
2. Before Interrupt A completes operation and stores the result back into `EXTI->IMR`, higher-priority interrupt B fires, and stores the value of `EXTI->IMR` to a temporary register.  Note that this temporary value is `IMR=0b0011`, since the modification intended by interrupt A has not yet been stored back in the hardware `EXTI->IMR` register.
3. Interrupt B handler continues to execute, and masks out the "B" interrupt.  The resulting value of the IMR register is:

`IMR == 0b0001`

Representing a state where the "A" interrupt is not masked, but the "B" interrupt is.  Now, the interrupt B handler returns, and interrupt A handler resumes execution.
4) Interrupt A handler stores the value of it's calculation from step (1) into `EXTI-IMR`, setting the final value to:

`EXTI->IMR == 0b0010`.

So, instead of the intended result of `EXTI->IMR == 0b0000` (both interrupts masked), we end up with interrupt B unmasked, and interrupt A masked.  To prevent this, we can instead use a (mutex)[https://barrgroup.com/Embedded-Systems/How-To/RTOS-Mutex-Semaphore] to prevent other interrupts from activating while the critical `EXTI->IMR` modification is occurring.  In our code, we accomplish this using the following:

```
mutex_start();
EXTI->IMR &= ~PINOUT_EXTERNAL_HARDWARE_INTERRUPT_A;
mutex_end();
``` 

Where `mutex_start/end` are functions which can be used to bound critical code sections, and prevent any interrupts from activating during the execution of that code.
