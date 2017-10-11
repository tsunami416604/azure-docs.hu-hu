1. A telepítő másolja egy helyi mappába (például /tmp) azon a kiszolgálón, amelyet védeni kíván. A terminálban a következő parancsokat:
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Szeretné telepíteni a mobilitási szolgáltatást, futtassa a következő parancsot:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Telepítés befejezése után kell a mobilitási szolgáltatás és a konfigurációs kiszolgáló regisztrálva. A következő parancsot a mobilitási szolgáltatás regisztrálása a konfigurációs kiszolgáló.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Mobilitási szolgáltatások telepítőjének parancssori

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Paraméter|Típus|Leírás|Lehetséges értékek|
|-|-|-|-|
|-r |Kötelező|Megadja, hogy kell telepíteni a mobilitási szolgáltatás (MS), vagy MasterTarget(MT) kell telepíteni.|MS </br> FŐ CÉLKISZOLGÁLÓ|
|-d |Optional|Hely, ahol a mobilitási szolgáltatás telepítve lesz|/usr/local/ASR|
|-v|Kötelező|Adja meg a platform, amelyen a mobilitási szolgáltatás található első </br> </br>- **VMware** : használja ezt az értéket, ha egy virtuális gépen futó mobilitási szolgáltatás telepít *VMware vSphere ESXi-gazdagépek*, *Hyper-V-gazdagépek* és *Phsyical kiszolgálók* </br> - **Azure** : használja ezt az értéket, ha telepíti az ügynököt egy Azure IaaS virtuális Gépen| VMware </br> Azure|
|-k|Optional|Meghatározza, hogy a telepítő futtatásához csendes módban| N/A|


#### <a name="mobility-service-configuration-command-line"></a>Parancssori mobilitási szolgáltatás konfigurációja

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Paraméter|Típus|Leírás|Lehetséges értékek|
|-|-|-|-|
|-i |Kötelező|IP-címe a konfigurációs kiszolgálón|Bármilyen érvényes IP-cím|
|-P |Kötelező|A kapcsolat jelszava mentett tartalmazó fájl teljes elérési út|Bármilyen érvényes mappa|
