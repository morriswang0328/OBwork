
nRF52 Hardware Drivers 研究
===
###### tags: `Nordic nRF52832`

[TOC]

:::success
狀態：本文編輯中
:::

# 參考資料
## [Datasheet](https://infocenter.nordicsemi.com/pdf/nRF52832_PS_v1.4.pdf)

## [SDK 15.3.0官網說明檔](https://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.sdk5.v15.3.0%2Findex.html)
 
![](https://i.imgur.com/w6NbT6R.png)



# 功能

## Timer & LEDs by Timer example by SDK

### 電路圖

![](https://i.imgur.com/TIKjyEW.png)


### The Timer Example demonstrates the use of the timer peripherals. This example replaces the less accurate busy delay in the Blinky Example with a more accurate hardware timer based delay.

When the application starts, some GPIO pins are configured as outputs to drive the LEDs. The applications then loops while toggling the state of one of the LEDs every 500 milliseconds.

You can find the source code and the project file of the example in the following folder: <InstallFolder>\examples\peripheral\timer

Testing
Test the Timer Example application by performing the following steps:

Compile and program the application.
Observe that the LEDs state is changed every 500 milliseconds.

```c=
int main(void)
{
    /* Time(in ms) between consecutive compare events. */
    uint32_t time_ms = 500; 
    uint32_t time_ticks;
    uint32_t err_code = NRF_SUCCESS;

    /* Configure all leds on board. */    
    bsp_board_leds_init();// = bsp_board_init(BSP_INIT_LEDS)
    /* inside bsp_board_leds_init()
    uint32_t i;
    for (i = 0; i < LEDS_NUMBER; ++i)
    {
        nrf_gpio_cfg_output(m_board_led_list[i]);
    }
    bsp_board_leds_off();    
    */

    /* set gpio for LEDs:     nrf_gpio_cfg_output(m_board_led_list[i]) @ nrf_gpio.h    
    
    __STATIC_INLINE void nrf_gpio_cfg_output(uint32_t pin_number)
    {
        nrf_gpio_cfg(    // Register: GPIO_PIN_CNF
            pin_number,//LED_1~4 = 17~20
            NRF_GPIO_PIN_DIR_OUTPUT, // .bit0=(1UL)
            NRF_GPIO_PIN_INPUT_DISCONNECT, // .bit1=(1UL)
            NRF_GPIO_PIN_NOPULL, // .bit3..2=(0UL)    
            NRF_GPIO_PIN_S0S1,// .bit10..8=(0UL)  
            NRF_GPIO_PIN_NOSENSE    //bit17..16=(0UL) 
            );
    }    
    
    */
    
    ***/


    /* Configure TIMER_LED for generating simple light effect - leds on board will invert his state one after the other. */
    nrf_drv_timer_config_t timer_cfg = NRF_DRV_TIMER_DEFAULT_CONFIG;
    err_code = nrf_drv_timer_init(&TIMER_LED, &timer_cfg, timer_led_event_handler);
    APP_ERROR_CHECK(err_code);

    time_ticks = nrf_drv_timer_ms_to_ticks(&TIMER_LED, time_ms);

    nrf_drv_timer_extended_compare(
         &TIMER_LED, NRF_TIMER_CC_CHANNEL0, time_ticks, NRF_TIMER_SHORT_COMPARE0_CLEAR_MASK, true);

    nrf_drv_timer_enable(&TIMER_LED);

    while (1)
    {
        __WFI();
    }
}

```
### Register 設定解讀

#### 開啟TIMER
@sdk_config.h
```c=
//==========================================================
// <e> NRFX_TIMER_ENABLED - nrfx_timer - TIMER periperal driver
//==========================================================
#ifndef NRFX_TIMER_ENABLED
#define NRFX_TIMER_ENABLED 1
#endif

// <q> TIMER0_ENABLED  - Enable TIMER0 instance

#ifndef TIMER0_ENABLED
#define TIMER0_ENABLED 1
#endif
```

@nrfx_timer.h
根據 NRFX_TIMER0_ENABLED 數量決定要開的IDX
enum {
#if NRFX_CHECK(NRFX_TIMER0_ENABLED)
    NRFX_TIMER0_INST_IDX,
#endif
#if NRFX_CHECK(NRFX_TIMER1_ENABLED)
    NRFX_TIMER1_INST_IDX,
#endif
#if NRFX_CHECK(NRFX_TIMER2_ENABLED)
    NRFX_TIMER2_INST_IDX,
#endif
#if NRFX_CHECK(NRFX_TIMER3_ENABLED)
    NRFX_TIMER3_INST_IDX,
#endif
#if NRFX_CHECK(NRFX_TIMER4_ENABLED)
    NRFX_TIMER4_INST_IDX,
#endif
    NRFX_TIMER_ENABLED_COUNT
};


#### 設定 LED 的 GPIO
```
├── bsp_board_init(BSP_INIT_LEDS) @ main.c
│   └── bsp_board_leds_init() @ board.c    
│       └── nrf_gpio_cfg_output @ nrf_gpio.c      
│           └── nrf_gpio_cfg @ nrf_gpio.c

```

#### err_code = nrf_drv_timer_init(&TIMER_LED, &timer_cfg, timer_led_event_handler);  內容分析
```
typedef struct
{
    NRF_TIMER_Type * p_reg;            ///< Pointer to the structure with TIMER peripheral instance registers.
    uint8_t          instance_id;      ///< Driver instance index.
    uint8_t          cc_channel_count; ///< Number of capture/compare channels.
} nrfx_timer_t;


typedef struct
{
    nrf_timer_frequency_t frequency;          ///< Frequency.
    nrf_timer_mode_t      mode;               ///< Mode of operation.
    nrf_timer_bit_width_t bit_width;          ///< Bit width.
    uint8_t               interrupt_priority; ///< Interrupt priority.
    void *                p_context;          ///< Context passed to interrupt handler.
} nrfx_timer_config_t;


typedef void (* nrfx_timer_event_handler_t)(nrf_timer_event_t event_type,
                                            void            * p_context);
                                            
                                            
typedef struct
{
    nrfx_timer_event_handler_t handler;
    void *                     context;
    nrfx_drv_state_t           state;
} timer_control_block_t;
                                            

```
### 引數3: timer_led_event_handler

* ### event_type是一個enum, 在main裡面設定case XXX_COMPARE0去執行Timer觸發時要執行的功能

* ### 本例子因為只有開Timer0，所以其他的case就不寫

@main.c
```
void timer_led_event_handler(nrf_timer_event_t event_type, void* p_context)
{
    static uint32_t i;
    uint32_t led_to_invert = ((i++) % LEDS_NUMBER);

    switch (event_type)
    {
        case NRF_TIMER_EVENT_COMPARE0:
            bsp_board_led_invert(led_to_invert);
            break;

        default:
            //Do nothing.
            break;
    }
}

```

### LED輪流切換

@board.c 
```
void bsp_board_led_invert(uint32_t led_idx)
{
    ASSERT(led_idx < LEDS_NUMBER);
    nrf_gpio_pin_toggle(m_board_led_list[led_idx]);
}

__STATIC_INLINE void nrf_gpio_pin_toggle(uint32_t pin_number)
{
    NRF_GPIO_Type * reg        = nrf_gpio_pin_port_decode(&pin_number);
    uint32_t        pins_state = reg->OUT;

    reg->OUTSET = (~pins_state & (1UL << pin_number));
    reg->OUTCLR = (pins_state & (1UL << pin_number));
}


```

##### nrf_drv_timer_init() = nrfx_timer_init() 

```c=
nrfx_err_t nrfx_timer_init(nrfx_timer_t const * const  p_instance,
                           nrfx_timer_config_t const * p_config,
                           nrfx_timer_event_handler_t  timer_event_handler)
{
    timer_control_block_t * p_cb = &m_cb[p_instance->instance_id];
//#ifdef SOFTDEVICE_PRESENT
    //NRFX_ASSERT(p_instance->p_reg != NRF_TIMER0);
//#endif
    NRFX_ASSERT(p_config);
    NRFX_ASSERT(timer_event_handler);

    nrfx_err_t err_code;

    if (p_cb->state != NRFX_DRV_STATE_UNINITIALIZED)
    {
        err_code = NRFX_ERROR_INVALID_STATE;
        NRFX_LOG_WARNING("Function: %s, error code: %s.",
                         __func__,
                         NRFX_LOG_ERROR_STRING_GET(err_code));
        return err_code;
    }

    /* Warning 685: Relational operator '<=' always evaluates to 'true'"
     * Warning in NRF_TIMER_IS_BIT_WIDTH_VALID macro. Macro validate timers resolution.
     * Not necessary in nRF52 based systems. Obligatory in nRF51 based systems.
     */

    /*lint -save -e685 */

    NRFX_ASSERT(NRF_TIMER_IS_BIT_WIDTH_VALID(p_instance->p_reg, p_config->bit_width));

    //lint -restore

    p_cb->handler = timer_event_handler;
    p_cb->context = p_config->p_context;

    uint8_t i;
    for (i = 0; i < p_instance->cc_channel_count; ++i)
    {
        nrf_timer_event_clear(p_instance->p_reg,
                              nrf_timer_compare_event_get(i));
    }

    NRFX_IRQ_PRIORITY_SET(nrfx_get_irq_number(p_instance->p_reg),
        p_config->interrupt_priority);
    NRFX_IRQ_ENABLE(nrfx_get_irq_number(p_instance->p_reg));

    nrf_timer_mode_set(p_instance->p_reg, p_config->mode);
    nrf_timer_bit_width_set(p_instance->p_reg, p_config->bit_width);
    nrf_timer_frequency_set(p_instance->p_reg, p_config->frequency);

    p_cb->state = NRFX_DRV_STATE_INITIALIZED;

    err_code = NRFX_SUCCESS;
    NRFX_LOG_INFO("Function: %s, error code: %s.",
                  __func__,
                  NRFX_LOG_ERROR_STRING_GET(err_code));
    return err_code;
}
```


``` c=
typedef struct
{
    nrf_timer_frequency_t frequency;          ///< Frequency.
    nrf_timer_mode_t      mode;               ///< Mode of operation.
    nrf_timer_bit_width_t bit_width;          ///< Bit width.
    uint8_t               interrupt_priority; ///< Interrupt priority.
    void *                p_context;          ///< Context passed to interrupt handler.
} nrfx_timer_config_t;

#define NRFX_TIMER_DEFAULT_CONFIG                                                    \
{                                                                                    \
    .frequency          = (nrf_timer_frequency_t)NRFX_TIMER_DEFAULT_CONFIG_FREQUENCY,\
    .mode               = (nrf_timer_mode_t)NRFX_TIMER_DEFAULT_CONFIG_MODE,          \
    .bit_width          = (nrf_timer_bit_width_t)NRFX_TIMER_DEFAULT_CONFIG_BIT_WIDTH,\
    .interrupt_priority = NRFX_TIMER_DEFAULT_CONFIG_IRQ_PRIORITY,                    \
    .p_context          = NULL                                                       \
}

```



## Nordic 的 I2C = TWI two-wire interface

https://infocenter.nordicsemi.com/index.jsp?topic=%2Fcom.nordic.infocenter.nrf52832.ps.v1.1%2Ftwi.html




> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


