---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9fe3b66de83ebc2cd0bf3a56a45456668c069191
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105136"
---
1. Másolja a telepítőt egy helyi mappába (például C:\Temp) a kiszolgálón, amely számára védelmet kíván. Futtassa az alábbi parancsokat rendszergazdaként parancsot a parancssorba:

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. A mobilitási szolgáltatás telepítéséhez futtassa a következő parancsot:

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```
3. Most már az ügynök regisztrálva kell lennie a konfigurációs kiszolgálóval.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="mobility-service-installer-command-line-arguments"></a>A mobilitási szolgáltatás telepítőjének parancssori argumentumok

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Paraméter|Typo|Leírás|Lehetséges értékek|
|-|-|-|-|
|/Role|Kötelező|Megadja, hogy a mobilitási szolgáltatás (MS) telepítve kell lennie, vagy MasterTarget (MT) telepítve kell lennie.|MS </br> MT|
|/InstallLocation|Optional|Hely, ahol a mobilitási szolgáltatás telepítve van.|A számítógép bármely mappája|
|/Platform|Kötelező|Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. </br> </br>- **VMware**: Használja ezt az értéket, ha egy virtuális gépen futó mobilitási szolgáltatás telepítése *VMware vSphere ESXi-gazdagépek*, *Hyper-V-gazdagépek*, és *fizikai kiszolgálók*. </br> - **Azure**: Akkor használja ezt az értéket, ha ügynököt telepít egy Azure IaaS virtuális gépen. | VMware </br> Azure|
|/Silent|Optional|Megadja, hogy a telepítő futtatásához csendes módban.| –|

>[!TIP]
> A telepítési naplókban találhatók % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>A mobilitási szolgáltatás regisztrációs parancssori argumentumok

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Paraméter|Typo|Leírás|Lehetséges értékek|
  |-|-|-|-|
  |/CSEndPoint |Kötelező|A konfigurációs kiszolgáló IP-címe| Bármely érvényes IP-cím|
  |/PassphraseFilePath|Kötelező|A hozzáférési kód helyét |Bármely érvényes UNC vagy helyi elérési útja|


>[!TIP]
> Az ügynök konfigurációs naplók % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log területen találhatók.
