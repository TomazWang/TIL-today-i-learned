
# CHT 4G can't connect

## Basic check
1. Check is data usage is `On`
2. Check VPN is not set to anything
3. Check `Airplane mode` is currently `Off`
4. Go to cellular network setting > Network operators > Choose automatically


## APN reset
1. Find cellular network setting in Setting.
2. Find `Access Point Names` setting.
3. If your phone is connecting `CHT MSS(emome)`, than there's the problem.
4. Select an apn `internet` or create a new APN preset
5. To create a APN preset, click `+` icon at upper right.
6. Set name whatever you like. (Let's say "CHT internet")
7. **Set APN to `internet`**
8. Make sure MCC is set to `466`
9. Make sure MNC is set to `92` as well
10. Back APN setting, select the preset you just set.



