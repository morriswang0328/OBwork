Microchip dsPIC33 I2C study
===

下列兩個function是從i2c2.h擷取
```c=

#define MCHP24AA512_RETRY_MAX       100  // define the retry count
#define MCHP24AA512_ADDRESS         0x50 // slave device address
#define MCHP24AA512_DEVICE_TIMEOUT  50   // define slave timeout 


uint8_t MCHP24AA512_Read( uint16_t address,
                          uint8_t *pData,
                          uint16_t nCount)
{
    I2C2_MESSAGE_STATUS status;
    uint8_t     writeBuffer[3];
    uint16_t    retryTimeOut, slaveTimeOut;
    uint16_t    counter;
    uint8_t     *pD;

    pD = pData;

    for (counter = 0; counter < nCount; counter++)
    {

        // build the write buffer first
        // starting address of the EEPROM memory
        writeBuffer[0] = (address >> 8);// high address
        writeBuffer[1] = (uint8_t)(address); // low address

        // Now it is possible that the slave device will be slow.
        // As a work around on these slaves, the application can
        // retry sending the transaction
        retryTimeOut = 0;
        slaveTimeOut = 0;

        while(status != I2C2_MESSAGE_FAIL)
        {
            // write one byte to EEPROM (2 is the count of bytes to write)
            I2C2_MasterWrite(writeBuffer,
                             2,
                             MCHP24AA512_ADDRESS,
                             &status);

            // wait for the message to be sent or status has changed.
            while(status == I2C2_MESSAGE_PENDING)
            {
                // add some delay here

                // timeout checking
                // check for max retry and skip this byte
                if (slaveTimeOut == MCHP24AA512_DEVICE_TIMEOUT)
                    return (0);
                else
                    slaveTimeOut++;
            }

            if (status == I2C2_MESSAGE_COMPLETE)
                break;

            // if status is  I2C2_MESSAGE_ADDRESS_NO_ACK,
            //               or I2C2_DATA_NO_ACK,
            // The device may be busy and needs more time for the last
            // write so we can retry writing the data, this is why we
            // use a while loop here

            // check for max retry and skip this byte
            if (retryTimeOut == MCHP24AA512_RETRY_MAX)
                break;
            else
                retryTimeOut++;
        }

        if (status == I2C2_MESSAGE_COMPLETE)
        {
            // this portion will read the byte from the memory location.
            retryTimeOut = 0;
            slaveTimeOut = 0;

            while(status != I2C2_MESSAGE_FAIL)
            {
                // write one byte to EEPROM (2 is the count of bytes to write)
                I2C2_MasterRead(pD,
                                1,
                                MCHP24AA512_ADDRESS,
                                &status);

                // wait for the message to be sent or status has changed.
                while(status == I2C2_MESSAGE_PENDING)
                {
                    // add some delay here

                    // timeout checking
                    // check for max retry and skip this byte
                    if (slaveTimeOut == MCHP24AA512_DEVICE_TIMEOUT)
                        return (0);
                    else
                        slaveTimeOut++;
                }

                if (status == I2C2_MESSAGE_COMPLETE)
                    break;

                // if status is  I2C2_MESSAGE_ADDRESS_NO_ACK,
                //               or I2C2_DATA_NO_ACK,
                // The device may be busy and needs more time for the last
                // write so we can retry writing the data, this is why we
                // use a while loop here

                // check for max retry and skip this byte
                if (retryTimeOut == MCHP24AA512_RETRY_MAX)
                    break;
                else
                    retryTimeOut++;
            }
        }

        // exit if the last transaction failed
        if (status == I2C2_MESSAGE_FAIL)
        {
            return(0);
            break;
        }

        pD++;
        address++;

    }
    return(1);

}

```


```c
  
uint8_t EMULATED_EEPROM_Read(uint16_t slaveDeviceAddress,
                             uint16_t dataAddress,
                             uint8_t *pData,
                             uint16_t nCount)
{
    I2C2_MESSAGE_STATUS status;
    I2C2_TRANSACTION_REQUEST_BLOCK readTRB[2];
    uint8_t     writeBuffer[3];
    uint16_t    timeOut, slaveTimeOut;

    // this initial value is important
    status = I2C2_MESSAGE_PENDING;

    // build the write buffer first
    // starting address of the EEPROM memory
    writeBuffer[0] = (dataAddress >> 8);     // high address
    writeBuffer[1] = (uint8_t)(dataAddress); // low low address

    // we need to create the TRBs for a random read sequence to the EEPROM
    // Build TRB for sending address
    I2C2_MasterWriteTRBBuild(&readTRB[0],
                             writeBuffer,
                             2,
                             slaveDeviceAddress);
    // Build TRB for receiving data
    I2C2_MasterReadTRBBuild(&readTRB[1],
                            pData,
                            nCount,
                            slaveDeviceAddress);

    timeOut = 0;
    slaveTimeOut = 0;

    while(status != I2C2_MESSAGE_FAIL)
    {
        // now send the transactions
        I2C2_MasterTRBInsert(2, readTRB, &status);

        // wait for the message to be sent or status has changed.
        while(status == I2C2_MESSAGE_PENDING)
        {
            // add some delay here

            // timeout checking
            // check for max retry and skip this byte
            if (slaveTimeOut == SLAVE_I2C_GENERIC_SLAVE_TIMEOUT)
                return (0);
            else
                slaveTimeOut++;
        }

        if (status == I2C2_MESSAGE_COMPLETE)
            break;

        // if status is  I2C2_MESSAGE_ADDRESS_NO_ACK,
        //               or I2C2_DATA_NO_ACK,
        // The device may be busy and needs more time for the last
        // write so we can retry writing the data, this is why we
        // use a while loop here

        // check for max retry and skip this byte
        if (timeOut == SLAVE_I2C_GENERIC_RETRY_MAX)
            return (0);
        else
            timeOut++;

    }
    return (1);

}   

```