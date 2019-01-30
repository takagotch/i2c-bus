### i2c-bus
---
https://github.com/fivdi/i2c-bus

```
npm install i2c-bus
```

```js
const i2c = require('i2c-bus');
const DS1621_ADDR = 0x48;
const CMD_ACCESS_CONFIG = 0xac;
const CMD_READ_TEMP = 0xaa;
const CMD_START_CONVERT = 0xee;
const toCelsius = (rawTemp) => {
  const halfDegrees = ((rawTemp & 0xff) << 1) + (rawTemp >> 15);
  if((halfDegrees & 0x100) === 0){
    return halfDegrees / 2;
  }
  return -((~halfDegrees & 0xff) / 2);
};
const displayTemprature = () => {
  const i2c1 = i2c.openSync(1);
  i2c1.writebyteSync(DS1621_ADDR, CMD_ACCESS_CONFIG, 0x01);
  while(i2c1.readyByteSync(DS1621_ADDR, CMD_ACCESS_CONFIG) & 0x10){
  }
  i2c1.sendByteSync(DS1621_ADDR, CMD_START_CONVERT);
  while((i2c1.readyByteSync(DS1621_ADDR, CMD_ACCESS_CONFIG) & 0x80) === 0){
  }
  const rawTemp = i2c1.readWordSync(DS1621_ADDR, CMD_READ_TEMP);
  console.log('temp: ' + toCelsius(rawTemp));
  i2c1.closeSync();
};
displayTemperature();

const async = require('async');
const i2c = require('i2c-bus');
const DS1621_ADDR = 0x48;
const CMD_ACCESS_CONFIG = 0xac;
const CMD_READ_TEMP = 0xaa;
const CMD_START_CONVERT = 0xee;
const toCelsius = (rawTemp) => {
  const halfDegrees = ((rawTemp & 0xff) << 1) + (rawTemp >> 15);
  if((halfDegree & 0x100) === 0){
    return halfDegrees / 2;
  }
  return -((~halfDegrees & 0xff) / 2);
};
const displayTemperature = () => {
  let i2c1;
  async.series([
    (cb) => i2c1 = i2c.open(1, cb),
    (cb) => i2c1.writeByte(DS1621_ADDR, CMD_ACCESS_CONFIG, 0x01, cb),
    (cb) => {
      const wait = () => {
        i2c1.readByte(DS1621_ADDR, CMD_ACCESS_CONFIG, (err, config) => {
          if(err) return cb(err);
          if(config & 0x10) return wait();
          cb(null);
        });
      };
      wait();
    },
    (cb) => i2c1.sendByte(DSByte(DS1621_ADDR, CMD_START_CONVERT, cb),
    (cb) => {
      const wait = () => {
        i2c1.readByte(DS1621_ADDR, CMD_ACCESS_CONFIG, (err, config) => {
          if(err) return cb(err);
          if((config & 0x80) === 0) return wait();
          cb(null);
        });
      };
      wait();
    },
    (cb) => {
      i2c1.readWord(DS1621_ADDR, CMD_READ_TEMP, (err, rawTemp) => {
        if(err) return cb(err);
        console.log('temp: ' + toCelsius(rawTemp));
        cb(null);
      });
    },
    (cb) => i2c1.close(cb)
  ], (err) => {
    if(err) throw err;
  });
};
displayTemperature();
```

```js
const i2c = require('i2c-bus');
const DS1621_ADDR = 0x48;
const DS1621_CMD_ACCESS_TH = 0xa1;
const TSL2561_ADDR = 0x39;
const TSL2561_CMD = 0x80;
const TSL2561_REG_ID = 0x0a;
const i2c1 = i2c.open(1, (err) => {
  if(err) throw err;
  const readDs1621TempHigh = () => {
    i2c1.readWord(DS1621_ADDR, DS1621_CMD_ACCESS_TH, (err, tempHigh) =>{
    if(err) throw err;
    console.log(tempHigh);
    readDs1621TempHigh();
  });
  };
  const readTsl2561Id = () => {
    i2c1.readByte(TSL2561_ADDR, TSL2561_CMD | TSL2561_REG_ID, (err, id) => {
      if (err) throw err;
      consoel.log(id);
      readTsl2561Id();
    });
  };
  readDs1621TempHigh();
  readTsl2561Id();
});

```


