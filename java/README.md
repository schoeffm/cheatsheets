# Enable Flight Controll

1. Start _Mission Control_ by issuing `jmc`
    - you'll have access to the MBeanServer of the connected JVM
    - and you'll get the chance to profile your programm, if you ...
2. Start your program with enabled _FlightRecorder_ `java -XX:+UnlockCommercialFeatures -XX:+FlightRecorder <MAIN_CLASS>`
