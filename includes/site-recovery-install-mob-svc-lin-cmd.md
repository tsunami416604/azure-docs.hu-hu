1. A telepítő másolja egy helyi mappába (például /tmp) azon a kiszolgálón, amelyet védeni kíván. A terminálban a következő parancsokat:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Szeretné telepíteni a mobilitási szolgáltatást, futtassa a következő parancsot:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Telepítésének befejezése után a mobilitási szolgáltatás szerepelnie kell a konfigurációs kiszolgálón. Futtassa a mobilitási szolgáltatás regisztrálása a konfigurációs kiszolgáló a következő parancsot:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Mobilitási szolgáltatás installer parancssorát.

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Paraméter|Típus|Leírás|Lehetséges értékek|
|-|-|-|-|
|-r |Kötelező|Megadja, hogy a mobilitási szolgáltatás (MS) kell telepíteni, vagy MasterTarget (MT) kell telepíteni.|MS </br> MT|
|-d |Optional|Hely, ahol telepítve van.|/usr/local/ASR|
|-v|Kötelező|Adja meg azt a platformot, amelyre telepítve van. </br> </br>- **VMware**: használja ezt az értéket, ha a mobilitási szolgáltatás telepítése a futó virtuális gép *VMware vSphere ESXi-gazdagépek*, *Hyper-V-gazdagépek*, és *fizikai kiszolgálók*. </br> - **Azure**: használja ezt az értéket, ha az ügynök telepítése egy Azure IaaS virtuális gépen.| VMware </br> Azure|
|-k|Optional|Meghatározza, hogy a telepítő futtatásához csendes módban.| –|


#### <a name="mobility-service-configuration-command-line"></a>Mobilitási szolgáltatás konfigurációs parancssor

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Paraméter|Típus|Leírás|Lehetséges értékek|
|-|-|-|-|
|-i |Kötelező|IP-címe a konfigurációs kiszolgálón|Bármilyen érvényes IP-cím|
|-P |Kötelező|Menti a fájlt, ha a kapcsolat hozzáférési kód fájl teljes elérési útja|Bármilyen érvényes mappa|
