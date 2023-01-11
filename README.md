# ÁNYK-nem-indul-összeomlik
ÁNYK nem indul/összeomlik

![image](https://user-images.githubusercontent.com/95906104/211758572-d289b6f4-fae3-457d-af10-75c6297866ff.png)

# Hibaüzenet elolvasása
Amennyiben az ÁNYK program nem indul el, hanem csak egy fekete ablak ugrik fel amely szinte azonnal be is záródik, így a kiírt hibakód elolvasására sincs lehetőség, hajtsa végre a következő lépések.
1. Keresse meg az ÁNYK programfájljait tartalmazó mappát.
Legegyszerübben úgy teheti meg, ha az asztalon levő ikonra jobb egérgombbal kattint és ott a megjelenő lehetőségek közül a "Fálj helyének megnyitása"-ra kattint. Ekkor egy mappa fog megnyilni amelyben a lenti képen látható fájlok lesznek láthatók. Ha nincs ikon az asztalán, vagy a FÁjl helyének megnyitását követően nem a lenti fájlokat tartalmazó mappa nyílik meg, akkor pedig keresse meg számítógépén az "abevjava" elnevezésű mappát. Nálam alapértelmezetten a következő helyre települt: ```C:\Users\Public\abevjava```

![image](https://user-images.githubusercontent.com/95906104/211759534-2f4a0336-37e4-4b6d-b76e-0cbd78c32ec4.png)
*Ányk programfájljait tartalmazó mappa tartalma*

2. Módosítsa a programot indító fájl kódját, hogy az ÁNYK következő indításakor ne záródjon be a fekete, hibajelző ablak.
Keresse meg a ```abevjava_start.bat``` elnevzésű fájlt. Ez felel az ÁNYK indításáért. Kattintson jobb gombbal a fájlra, majd a lehetőségek közül válassza ki a szerkesztést. Ha felugrik egy ablak amely arra kéri, hogy válassza ki, hogy mely programmal szeretné szerkeszteni a fájlt akkor válassza ki a Jegyzettömböt.
Ha sikerült akkor a következőt, illetve ehhez hasonlót fog látni:
```
@IF EXIST "C:/Users/Public/abevjava/jre/bin/" (SET ABEV_JAVA_HOME="C:/Users/Public/abevjava/jre/bin/")
@CALL .\setenv.bat
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee %MEMORY_OPTS% -jar boot.jar
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee %MEMORY_OPTS% %TUNING_OPTS% %RUN_OPTS% -DentityExpansionLimit=128000 -jar abevjava.jar cfg=cfg.enyk %1 %2 %3
```
A szöveg után Enterrel kezdjen új sort, és oda írja be a kövezkető szót: ```PAUSE```
Ez felel majd azért, hogy a hibaüzenet megjelenítő ablak ne záródjon be azonnal.
Valahogy így kell kinéznie a kódnak:

```
@IF EXIST "C:/Users/Public/abevjava/jre/bin/" (SET ABEV_JAVA_HOME="C:/Users/Public/abevjava/jre/bin/")
@CALL .\setenv.bat
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee %MEMORY_OPTS% -jar boot.jar
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee %MEMORY_OPTS% %TUNING_OPTS% %RUN_OPTS% -DentityExpansionLimit=128000 -jar abevjava.jar cfg=cfg.enyk %1 %2 %3
PAUSE
```
Mentse a fájlt.
Próbálja elindítani újra a fájlt, most már el fogja tudni olvasni a hibaüzenetet.


# Memóriahiány hiba
```Error occurred during initialization of VM
Could not reserve enough space for object heap```

A hiba magyarázata technikai nyelven:
```
Ez a hibaüzenet általában azt jelzi, hogy a rendszeren nincs elegendő memória a Java Virtual Machine (JVM) futtatásához. A JVM-nek bizonyos mennyiségű összefüggő memóriára van szüksége ahhoz, hogy létre tudja hozni az objektumhalmazt, és ha ez a memória nem áll rendelkezésre, a JVM nem tud elindulni.

A probléma megoldásához megpróbálhatja növelni a JVM számára kiosztott memória mennyiségét a Java futtatható programnak átadott parancssori opciók módosításával.
```
Érthetőbb nyelven: Valami miatt az ÁNYK-nak nem áll rendelkezésére elegendő memória. Ennek orvoslására a fent vázolt módon - a ```abevjava_start.bat``` fájlban történő kód átírásával - kell eljárnunk.

Megoldás:
Az ```abevjava_start.bat``` fájlban módosítsa a használandó memória méretét.
Az alábbi kódban cserélje le mindkét helyen ```%MEMORY_OPTS%``` kódot a következőre ```-Xms512m -Xmx1024m```. A kód jelentése: 512-vel fog indulni a program és ha ez nem elégséges neki akkor egészen 1024 megabájtnyit is használhat belőle.
```
@IF EXIST "C:/Users/Public/abevjava/jre/bin/" (SET ABEV_JAVA_HOME="C:/Users/Public/abevjava/jre/bin/")
@CALL .\setenv.bat
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee %MEMORY_OPTS% -jar boot.jar
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee %MEMORY_OPTS% %TUNING_OPTS% %RUN_OPTS% -DentityExpansionLimit=128000 -jar abevjava.jar cfg=cfg.enyk %1 %2 %3
```

A kicserélés után ehhez hasonlóan kell kinéznie a kódnak:
```
@IF EXIST "C:/Users/Public/abevjava/jre/bin/" (SET ABEV_JAVA_HOME="C:/Users/Public/abevjava/jre/bin/")
@CALL .\setenv.bat
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee -Xms512m -Xmx1024m -jar boot.jar
@%ABEV_JAVA_HOME%java -XX:+IgnoreUnrecognizedVMOptions --add-modules=java.se.ee -Xms512m -Xmx1024m %TUNING_OPTS% %RUN_OPTS% -DentityExpansionLimit=128000 -jar abevjava.jar cfg=cfg.enyk %1 %2 %3
```
