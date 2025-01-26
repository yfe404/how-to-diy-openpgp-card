 clone GNUK release/2.2
 and submodule chopstx release/2.5

 see patches to see how to add a confirm button.
 DONT PATCH DIRECTLY I have the done the patch in a hurry and it probably
 won't work but all the changes should be there



 sudo /usr/bin/openocd -f /usr/share/openocd/scripts/interface/stlink-v2.cfg -f /usr/share/openocd/scripts/target/stm32f1x.cfg


 while the above is running:

 cat tmp.network | netcat 127.0.0.1 4444

 where tmp.network contains:

```
reset halt
stm32f1x unlock 0
reset halt
stm32f1x unlock 0
reset halt
flash erase_sector 0 0 127
flash write_bank 0 /home/yfe/Documents/gnuk/src/build/gnuk.bin 0
reset
exit
```

replace the path with yours
