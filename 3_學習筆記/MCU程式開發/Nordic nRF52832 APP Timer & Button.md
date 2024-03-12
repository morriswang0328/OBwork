
Nordic nRF52832 APP Timer & Button
===
###### tags: `Nordic nRF52832`, `G-EBike`

[TOC]

:::success
狀態：本文編輯中，Button待增加
:::

# 參考資料

<font size=5, color=blue>

- APP TIMER: 使用Real Time Clock(RTC)作為APP Timer
- [APP Timer](https://infocenter.nordicsemi.com/topic/com.nordic.infocenter.sdk5.v15.3.0/group__app__timer.html)

</font><br>


# 主程式

@main.c
```c=
  timers_init();
  //-->
  static void timers_init(void) {
  // Initialize timer module.
  ret_code_t err_code = app_timer_init();
  APP_ERROR_CHECK(err_code);

  err_code = app_timer_create(&system_task_timer_id, APP_TIMER_MODE_REPEATED, system_task_timer_handler);
  APP_ERROR_CHECK(err_code);


  // system_reset_timer_id
  err_code = app_timer_create(&system_reset_timer_id, APP_TIMER_MODE_SINGLE_SHOT, system_reset_timer_handler);
  APP_ERROR_CHECK(err_code);

  //custom_output_timer_id

  err_code = app_timer_create(&custom_output_timer_id, APP_TIMER_MODE_SINGLE_SHOT, custom_output_timer_handler);
  APP_ERROR_CHECK(err_code);

  err_code = app_timer_create(&custom_output_timer2_id, APP_TIMER_MODE_REPEATED, custom_output_timer_handler2);
  APP_ERROR_CHECK(err_code);

  //shift_timer_id
  err_code = app_timer_create(&shift_timer_id, APP_TIMER_MODE_REPEATED, shift_task_timer_handler);
  APP_ERROR_CHECK(err_code);

  //canspi_timer_id

#if (CANSPI_ENABLED)
  err_code = app_timer_create(&canspi_timer_id, APP_TIMER_MODE_REPEATED, can_task_timer_handler);
  APP_ERROR_CHECK(err_code);
#endif
}  
  
```

# app_timer_init() @app_timer.c

<font size=5, color=black>根據 system_task_manager.task 切換</font><br>

```c=
ret_code_t app_timer_init(void)
{
    // Stop RTC to prevent any running timers from expiring (in case of reinitialization)
    rtc1_stop();

    // Initialize operation queue
    m_op_queue.first           = 0;
    m_op_queue.last            = 0;
    m_op_queue.size            = APP_TIMER_CONFIG_OP_QUEUE_SIZE+1;

    mp_timer_id_head            = NULL;
    m_ticks_elapsed_q_read_ind  = 0;
    m_ticks_elapsed_q_write_ind = 0;

    #if APP_TIMER_WITH_PROFILER
    m_max_user_op_queue_utilization   = 0;
    #endif

    // Sa SW Interrupt 0
    NVIC_ClearPendingIRQ(SWI_IRQn);// SWI_IRQ_n(0)
    NVIC_SetPriority(SWI_IRQn, SWI_IRQ_PRI);// SWI_IRQ_PRI=6
    NVIC_EnableIRQ(SWI_IRQn);

    rtc1_init(APP_TIMER_CONFIG_RTC_FREQUENCY);// APP_TIMER_CONFIG_RTC_FREQUENCY = 0
    /*
    static void rtc1_init(uint32_t prescaler)
    {
        NRF_RTC1->PRESCALER = prescaler;// =0
        NVIC_SetPriority(RTC1_IRQn, RTC1_IRQ_PRI);//RTC1_IRQ_PRI=6
    }
    */
    m_ticks_latest = rtc1_counter_get();

    return NRF_SUCCESS;
}
```  

# app_timer_create() @app_timer.c

<font size=5, color=black></font><br>

```c=
err_code = app_timer_create(&system_task_timer_id, APP_TIMER_MODE_REPEATED, system_task_timer_handler);
  APP_ERROR_CHECK(err_code);
```

```c=
ret_code_t app_timer_create(app_timer_id_t const *      p_timer_id,
                            app_timer_mode_t            mode,
                            app_timer_timeout_handler_t timeout_handler)
{
    // Check state and parameters
    VERIFY_MODULE_INITIALIZED();

    if (timeout_handler == NULL)
    {
        return NRF_ERROR_INVALID_PARAM;
    }
    
    if (p_timer_id == NULL)
    {
        return NRF_ERROR_INVALID_PARAM;
    }
    
    if (((timer_node_t*)*p_timer_id)->is_running)
    {
        return NRF_ERROR_INVALID_STATE;
    }

    timer_node_t * p_node     = (timer_node_t *)*p_timer_id;
    p_node->is_running        = false;
    p_node->mode              = mode;
    p_node->p_timeout_handler = timeout_handler;
    return NRF_SUCCESS;

}
``` 
## system_task_timer_handler

<font size=5, color=black>根據 system_task_manager.task 切換</font><br>


```c=
void system_task_timer_handler(void* p_context) {
  switch (system_task_manager.task) {
    case SYS_TASK_IDLE:
      NRF_LOG_INFO("SYS_TASK_IDLE");
      app_timer_stop(system_task_timer_id);
      break;  // case SYS_TASK_IDLE:

    case SYS_TASK_AUTO_DISCONNECT: {
      //NRF_LOG_INFO("SYS_TASK_AUTO_DISCONNECT %d", system_task_manager.cnt);
      system_task_manager.cnt++;
      if (nrf_gpio_pin_read(APP_INT_1) == G_SENSOR_ON_PIN_STATE) {
        system_task_manager.cnt = 0;
      }

      if (system_task_manager.cnt == AUTO_DISCONNECTION_PERIOD) {
        if (rs_manager.is_connected) {
          disconnect(rs_manager.conn_handle, NULL);
        }

        if (fd_manager.is_connected) {
          disconnect(fd_manager.conn_handle, NULL);
        }

        system_task_manager.task = SYS_TASK_IDLE;
        //app_timer_stop(system_task_timer_id);
      }

      break;
    }

    case SYS_TASK_SHIFTING:
      NRF_LOG_INFO("system_task_manager.task SYS_TASK_SHIFTING");
      break;
      
    default:
      NRF_LOG_INFO("system_timer_handler unused task %d", system_task_manager.task);
      break;

  }  // end of switch( system_task_manager.task )

}  //  system_timer_handler

```

## system_reset_timer_handler

<font size=5, color=red>好像沒執行任何東西?!</font><br>

```c=
void system_reset_timer_handler(void* p_context) {
  APP_ERROR_CHECK(0x01);
}

/**@brief Macro for calling error handler function if supplied error code any other than NRF_SUCCESS.
 *
 * @param[in] ERR_CODE Error code supplied to the error handler.
 */
#define APP_ERROR_CHECK(ERR_CODE)                           
do                                                     
{                                                       
    const uint32_t LOCAL_ERR_CODE = (ERR_CODE);         
    if (LOCAL_ERR_CODE != NRF_SUCCESS)                 
    {                                                   
        APP_ERROR_HANDLER(LOCAL_ERR_CODE);             
    }                                                  
} while (0)


```
## custom_output_timer_handler

<font size=5, color=black>馬達控制相關</font><br>

```c=
void custom_output_timer_handler(void* p_context) {
  NRF_LOG_INFO("Shift_set_output %d %d\r\n", OUTPUT_DIR_NONE, 0);
  //Motor_set_output(OUTPUT_DIR_NONE, 0);
  Motor_set_output_test(MOTOR_COAST, 0);

  NRF_LOG_INFO("Motor_stop");
  Motor_stop();

  motor_manager.is_moving = false;

  nrf_delay_ms(100);

  NRF_LOG_INFO("Motor_close");
  Motor_close();

  system_update_encoder_g_sensor();

  Encoder_disable();

  #if (CANSPI_ENABLED)
  can_notify_rd_state();

  if (can_trp_cmd_enabled) {
    nrf_delay_us(500);
    can_notify_encoder_info();
    nrf_delay_us(500);
    can_notify_rd_shift_info();
  }
  #endif

  set_led_state(LED_STATE_RED_OFF);

}  // custom_output_timer_handler

```

## custom_output_timer_handler2

<font size=5, color=black>Encoder控制
<說明>


</font><br>

```c=
void custom_output_timer_handler2(void* p_context) {

  uint16_t *p_cnt = (uint16_t *)p_context;
  *p_cnt = *p_cnt - 2;
  NRF_LOG_INFO("custom_output_timer_handler2 cnt %d", *p_cnt);

  Encoder_read(I2C_CMD_ENCODER_RAW_ANGLE, i2c_task_manager_1.rx_buffer, 2);
  rd_char.param.raw = (i2c_task_manager_1.rx_buffer[0] << 8) + i2c_task_manager_1.rx_buffer[1];
  rd_char.param.cal = Encoder_cal_angle(rd_char.param.raw, rd_char.param.user_offset);

  uint8_t i = 0;

  for(i=0; i< 11; i++){
    if(abs(rd_char.param.cal - gear_positions[i]) < 10){
      //can_notify_encoder_info();
    }
  }

  if (*p_cnt == 0) {
    app_timer_stop(custom_output_timer2_id);

    Motor_stop();
    Motor_close();

    motor_manager.is_moving = false;

    Encoder_disable();

    set_led_state(LED_STATE_RED_OFF);
  }

}  // custom_output_timer_handler


```

> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


