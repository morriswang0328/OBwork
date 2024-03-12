
Nordic G-EBike 檔案夾架構研究
===
###### tags: `Nordic nRF52832`, `G-EBike`


## 檔案結構

### 第一層
![](https://i.imgur.com/WzIrOmc.png)

### SDK:FW
![](https://i.imgur.com/QCitmPH.png)

```
 .
 ├── components     # 包含了Nordic自己开发的SDK源代码
 │   └── 802_15_4     # Low Rate Wireless personal area network
 │   └── ble             # BLE
 │   └── boards          # 開發板
 │   └── custom_api      # 專案自定義的api
 │   └── custom_ble_cmd  # 專案自定義與BLE相關功能
 │   └── custom_util     # 專案自定義功能
 │       └── common_defines.h
 │       └── util.c/h    #  
 │   └── ble          # BLE
 │   └── ble          # BLE
 │   └── ble          # BLE
 │   └── ble          # BLE
 │   └── ble          # BLE
 
 ├── config         # nrfx drivers files
 │   └── include     # nrfx drivers headers
 │   └── src         # nrfx drivers sources
 ├── examples             # Hardware Access Layer files
 ├── external             # Nordic MDK files
 ├── external tools             # Nordic SoC related files
 ├── integration    # Templates of nrfx integration files
 └── modules    # Templates of nrfx integration files

```


### nrfx Overview: module內只有一個
![](https://i.imgur.com/5SdWauo.png)

* nrfx is a standalone set of drivers for peripherals present in Nordic Semiconductor's SoCs. 
* It originated as an extract from the nRF5 SDK.
* The intention was to provide drivers that can be used in various environments without the necessity to integrate other parts of the SDK into them.
* For the user's convenience, the drivers come with the MDK package. 
* This package contains definitions of register structures and bitfields for all supported SoCs, as well as startup and initialization files for them.

#### Supported SoCs

* nRF51 Series: nRF52810, nRF52811, nRF52832, nRF52840, nRF9160

#### Directories

```
 .
 ├── doc             # Project documentation files
 ├── drivers         # nrfx drivers files
 │   └── include     # nrfx drivers headers
 │   └── src         # nrfx drivers sources
 ├── hal             # Hardware Access Layer files
 ├── mdk             # Nordic MDK files
 ├── soc             # Nordic SoC related files
 └── templates       # Templates of nrfx integration files
```

#### Generating documentation

* nrfx documentation is available in the `doc\html` folder of the release package.
* You can also generate documentation yourself from the source code. 
* To do it, install doxygen and run one of the scripts: `generate_html_doc.bat` or `generate_html_doc.sh`.
* Generated documentation will be stored in the `doc\html` directory. Use `index.html` to open it.



## 程式架構分析

```c=
main()
{

  // Initialization
  Init();

  for (;;) 
  {
    idle_state_handle();
  }

}

```

```c=
void Init()
{

    NRF_POWER->DCDCEN = 0;  // set Register value

    /***
    <Description>
    
    ***/
    //log_init();   ==    
    ret_code_t err_code = NRF_LOG_INIT(NULL);
    APP_ERROR_CHECK(err_code);
    //NRF_LOG_DEFAULT_BACKENDS_INIT(); ==
    nrf_log_default_backends_init();
   
    /***
    <Description>
    init encoder state,raw angle, gas gauge votage 
    and enable g sensor activity regonition
    ***/
    //system_init(); ==
    Flash_manager_init();  // empty inside
    // now PCB_TYPE = PCB_TYPE_TRP, so hide settings below
    //#if (PCB_TYPE == PCB_TYPE_EVB)
    //system_state.batt_vol = 2.3;
    //#endif
    rd_char.param.fw_ver = FW_VER;
    rd_char.param.conn_state |= DEVICE_TYPE_RD;
    
    /*** 
    <Description> 
    Read flash page: system & gear  
    ***/
/***
typedef union {
  uint8_t data_arr[40];
  struct
  {
    FLASH_ADDR_t self;
    FLASH_ADDR_t btn;
    FLASH_ADDR_t fd;
    FLASH_ADDR_t rd;
    FLASH_SYSTEM_CONFIG_t config;
  }param;

}FLASH_PAGE_SYSTEM_t;

static FLASH_PAGE_SYSTEM_t flash_system_manager;
// flash_gear_manager.param.gap_no
static FLASH_PAGE_GEAR_t flash_gear_manager = {
  .param.gap_no = DEFAULT_GAP_NO
};
***/

    Flash_get_page_system_info(&flash_system_manager);
    Flash_get_page_gear_info(&flash_gear_manager);


    /*** 
    <Description> 
    Read 4 bytes data from FLASH_UICR_ADDR to flash_uicr_fw_ver    
    ***/
    Flash_get_data_from_addr(flash_uicr_fw_ver, 4, (uint32_t*)(FLASH_UICR_ADDR));

    // this method cannot be used for canbus update fw, 
    // so manually modify fw version for now.
    flash_uicr_fw_ver[0] = (FW_VER>>8) & 0x00FF; // high byte
    flash_uicr_fw_ver[1] = FW_VER & 0x00FF;  // low byte    

    /*** 
    <Description>
    Read default offset and user offset from flash
    ***/
/***
typedef union {
  uint8_t arr[20];
  struct
  {
    uint16_t fw_ver;
    uint8_t  conn_state;
    uint8_t  gear_idx;
    uint16_t batt;
    uint8_t sensor_state;
    uint8_t encoder_state;
    uint16_t default_offset;
    uint16_t user_offset;
    uint16_t raw;
    uint16_t cal;
    uint16_t shift_period;
    uint8_t reset_power;
  } param;
}FD_RD_CHAR_t;
FD_RD_CHAR_t rd_char = {0};
***/

rd_char.param.default_offset = flash_gear_manager.param.offsets.param.default_value;
    rd_char.param.user_offset = flash_gear_manager.param.offsets.param.user_value;

    rs_manager.is_paired = flash_system_manager.param.btn.param.is_paired;
    memcpy(rs_manager.address, flash_system_manager.param.btn.param.address, 6);

    fd_manager.is_paired = flash_system_manager.param.fd.param.is_paired;
    memcpy(fd_manager.address, flash_system_manager.param.fd.param.address, 6);
                
    /***

    ***/
    timers_init();
    button_init();
    Shift_init(&shift_task_manager);
    //load gear index in flash
    Led_manager_init(&led_config);
    power_management_init();
    ble_stack_init();
    gap_params_init();
    gatt_init();

    sd_ble_gap_addr_get(&self_id_addr);
    set_led_state(LED_STATE_RG_FLASH);  
    nrf_gpio_cfg_output(M_RST);
    nrf_gpio_pin_set(M_RST);
    
    Encoder_init(&encoder_manager);
    // Init twis.
    device_twis_init_test(m_twis, mcu_twis_config,twis_event_handler);
    // Read averaged Battery Voltage 
    ADC_get_avg_value(&rd_char.param.batt);
    ADC_conv_raw_to_volt(rd_char.param.batt, &system_state.batt_vol);
    
    // Set Gap Table
    gear_set_gap_table_from_flash();
    
    // Encoder Re-Init
    Encoder_enable();
    nrf_delay_ms(ENCODER_POWER_ON_DELAY_MS);
    system_update_encoder_g_sensor();
    Encoder_disable();
    
    // search current gear idx
    gear_search_and_update_gear_idx();
    Set_char_value(rd_manager.conn_handle, RD_CHAR_5_HANDLE, rd_char.arr, CHAR5_VALUE_LEN);
    
    // CAN_Init
    can_init();    
    
}
```

---


> 未來的你一定會感謝現在努力的自己. [name=]

:::info
**Find this document incomplete?** Leave a comment!
:::


