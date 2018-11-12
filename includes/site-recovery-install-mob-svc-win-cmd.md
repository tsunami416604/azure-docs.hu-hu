---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 65477f62af80511a73307204c2a6f4b5e0f409d6
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019148"
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

| Paraméter|Type (Típus)|Leírás|Lehetséges értékek|
|-|-|-|-|
|/ Role|Kötelező|Megadja, hogy a mobilitási szolgáltatás (MS) telepítve kell lennie, vagy MasterTarget (MT) telepítve kell lennie.|MS </br> MT|
|/InstallLocation|Választható|Hely, ahol a mobilitási szolgáltatás telepítve van.|A számítógép bármely mappája|
|És platformok|Kötelező|Meghatározza a platformot, amelyre telepítve van a mobilitási szolgáltatást. </br> </br>- **VMware**: használja ezt az értéket, ha egy virtuális gépen futó mobilitási szolgáltatás telepítése *VMware vSphere ESXi-gazdagépek*, *Hyper-V-gazdagépek*, és *fizikai kiszolgálók*. </br> - **Azure**: használja ezt az értéket, ha az ügynök telepítése az Azure IaaS virtuális gépen. | VMware </br> Azure|
|/ Csendes|Választható|Megadja, hogy a telepítő futtatásához csendes módban.| –|

>[!TIP]
> A telepítési naplókban találhatók % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>A mobilitási szolgáltatás regisztrációs parancssori argumentumok

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Paraméter|Type (Típus)|Leírás|Lehetséges értékek|
  |-|-|-|-|
  |/CSEndPoint |Kötelező|A konfigurációs kiszolgáló IP-címe| Bármely érvényes IP-cím|
  |/PassphraseFilePath|Kötelező|A hozzáférési kód helyét |Bármely érvényes UNC vagy helyi elérési útja|


>[!TIP]
> Az ügynök konfigurációs naplók % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log területen találhatók.
