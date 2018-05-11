1. A telepítő másolja egy helyi mappába (például C:\Temp) azon a kiszolgálón, amelyet védeni kíván. Futtassa az alábbi parancsokat rendszergazdaként parancsot a parancssorba:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Szeretné telepíteni a mobilitási szolgáltatást, futtassa a következő parancsot:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. Most már az ügynök regisztrálva kell lennie a konfigurációs kiszolgálóval.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Parancssori argumentumok a mobilitási szolgáltatás telepítő

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Paraméter|Típus|Leírás|Lehetséges értékek|
|-|-|-|-|
|/ Szerepkör|Kötelező|Megadja, hogy a mobilitási szolgáltatás (MS) kell telepíteni, vagy MasterTarget (MT) kell telepíteni.|MS </br> FŐ CÉLKISZOLGÁLÓ|
|/InstallLocation|Optional|Hely, ahol telepítve van.|A számítógép bármely mappája|
|/ Platform|Kötelező|Adja meg azt a platformot, amelyre telepítve van. </br> </br>- **VMware**: használja ezt az értéket, ha a mobilitási szolgáltatás telepítése a futó virtuális gép *VMware vSphere ESXi-gazdagépek*, *Hyper-V-gazdagépek*, és *fizikai kiszolgálók*. </br> - **Azure**: használja ezt az értéket, ha az ügynök telepítése egy Azure IaaS virtuális gépen. | VMware </br> Azure|
|/ Beavatkozás nélküli|Optional|Meghatározza, hogy a telepítő futtatásához csendes módban.| –|

>[!TIP]
> A telepítési naplókban találhatók % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Parancssori argumentumok a mobilitási szolgáltatás regisztrációs

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Paraméter|Típus|Leírás|Lehetséges értékek|
  |-|-|-|-|
  |/CSEndPoint |Kötelező|A konfigurációs kiszolgáló IP-címe| Bármilyen érvényes IP-cím|
  |/PassphraseFilePath|Kötelező|A hozzáférési kód helye |Bármilyen érvényes UNC vagy helyi elérési útja|


>[!TIP]
> A Gazdagépügynök-konfigurálási naplók % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log alatt található.
