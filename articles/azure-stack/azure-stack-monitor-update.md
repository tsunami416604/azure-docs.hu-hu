---
title: "Használatával a privilegizált végpont Azure verem frissítések figyelése |} Microsoft Docs"
description: "Útmutató a rendszerjogosultságú végpont a figyelheti a frissítési állapot integrált Azure verem rendszerekhez."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: mabrigg
ms.openlocfilehash: 55688ad4959d59e41dca9be2d00011e1d41ebd8c
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Használatával a privilegizált végpont Azure verem frissítéseit

*A következőkre vonatkozik: Azure verem integrált rendszerek*

A privilegizált végpont Azure verem frissítési állapotának figyelésére, és folytatja a sikertelen frissítés esetén futtassa a legutóbbi sikeres lépésben használható. 

A következő új PowerShell-parancsmagok a kezeléséhez integrált Azure verem rendszerek 1710 frissítésének szerepelnek.

| Parancsmag  | Leírás  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | A jelenleg futó, befejezett, vagy sikertelen frissítés állapotának beolvasása. A magas szintű állapotának a frissítési műveletet, és az aktuális lépés és a megfelelő állapotát leíró XML-dokumentum tartalmazza. |
| `Get-AzureStackUpdateVerboseLog` | A részletes naplókat, a frissítés által létrehozott adja vissza. |
| `Resume-AzureStackUpdate` | Folytatja a sikertelen frissítés esetén a pontnál, amelyen nem sikerült. Bizonyos esetekben előfordulhat, hogy megoldás végrehajtásához, akkor a frissítés folytatása előtt.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Ellenőrizze, hogy a parancsmagok érhetők el
A parancsmagok olyan új Azure verem 1710 frissítőcsomag, mert a 1710 frissítési folyamat kell kapnia egy bizonyos mértékig, mielőtt a figyelési funkció érhető el. Általában a parancsmagok érhetők el, ha a felügyeleti portálon állapot azt jelzi, hogy a 1710 frissítés a **indítsa újra a tárolási állomások** lépés. Pontosabban, a parancsmag frissítés rendszerindításakor **. lépés: 2.6 - PrivilegedEndpoint frissítése engedélyezett lépés futtatása**.

Azt is meghatározhatja, hogy a parancsmagok érhetők el programozott módon a parancs listáját a következőtől: a rendszerjogosultságú végpont lekérdezésével. Ehhez futtassa az alábbi parancsokat a hardver életciklus gazdagépen vagy egy Rendszerjogosultságú hozzáférés munkaállomásról. Győződjön meg arról, hogy a kiemelt végpont megbízható gazdagép. További információkért lásd: 1. lépésében [hozzáférni a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. PowerShell-munkamenetet létrehozni bármely Azure verem környezetében ERCS virtuális gépet (*előtag*-ERCS01, *előtag*-ERCS02, vagy *előtag*-ERCS03). Cserélje le *előtag* együtt a virtuális gép előtag-karakterlánc, amely a környezetben.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Ha a rendszer kéri a hitelesítő adatokat, a &lt; *Azure verem tartomány*&gt;\cloudadmin, vagy olyan fiók, amely a CloudAdmins csoport tagja. Adja meg ugyanazt a jelszót a AzureStackAdmin tartományi rendszergazdai fiók a telepítés során megadott CloudAdmin fiók.

2. A privilegizált végpont használható parancsok teljes listájának. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Határozza meg, ha a kiemelt végpont frissült.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. A Microsoft.AzureStack.UpdateManagement modulhoz parancsok listáját.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Példa:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>A frissítés felügyeleti parancsmagok használata

> [!NOTE]
> A következő parancsokat a hardver életciklus gazdagépen vagy egy Rendszerjogosultságú hozzáférés munkaállomásról. Győződjön meg arról, hogy a kiemelt végpont megbízható gazdagép. További információkért lásd: 1. lépésében [hozzáférni a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>A privilegizált végponthoz kapcsolódni, és rendelje hozzá a munkamenet-változó

A következő parancsokat egy PowerShell-munkamenetet létrehozni bármely Azure verem környezetében ERCS virtuális gépet (*előtag*-ERCS01, *előtag*-ERCS02, vagy *előtag*-ERCS03), és egy munkamenet-változó hozzárendelni.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Ha a rendszer kéri a hitelesítő adatokat, a &lt; *Azure verem tartomány*&gt;\cloudadmin, vagy olyan fiók, amely a CloudAdmins csoport tagja. Adja meg ugyanazt a jelszót a AzureStackAdmin tartományi rendszergazdai fiók a telepítés során megadott CloudAdmin fiók.

### <a name="get-high-level-status-of-the-current-update-run"></a>Az aktuális frissítés futtató magas szintű állapotának beolvasása 

Ahhoz, hogy az aktuális frissítés futtató általános állapotát, a következő parancsokat: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

A lehetséges értékek:

- Fut
- Befejezve
- Sikertelen 
- Törölve

Ezek a parancsok a naprakész állapot megjelenítéséhez többször is futtathatja. Ne kelljen újra kapcsolatot létesíteni.

### <a name="get-the-full-update-run-status-with-details"></a>A futtatási állapot adatokkal teljes frissítésének letöltése 

A teljes frissítési menetet összefoglaló XML-karakterláncként kérheti le. A karakterlánc egy fájl írásához vizsgálatára, vagy alakítsa át az XML-dokumentum, és elemezni, a PowerShell használatával. A következő parancsot a jelenleg futó lépéseket hierarchikus listájának XML elemzi.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

A következő példában a legfelső szintű lépés (felhő frissítése) rendelkezik egy gyermek terv frissítése, és indítsa újra a tárolási gazdagépek. Azt mutatja, hogy a tároló állomások indítsa újra a tervet a Blob Storage szolgáltatás az egyik frissíti.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>A folyamat részletes napló beolvasása

A napló írhat vizsgálatra fájlba. Ez segít egy frissítési probléma diagnosztizálásához.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>A részletes naplózás aktívan megtekintése

Az aktívan nézet frissítése közben a részletes naplózás futtatása, és a legújabb bejegyzések Ugrás a következő parancsokat a munkamenet interaktív módban, illetve a napló megjelenítéséhez:

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
A napló 60 másodpercenként frissíti, és új tartalom (ha elérhető) kerül a konzol. 

Hosszan futó háttérfolyamatot, során a konzol kimeneti előfordulhat, hogy nem lehet írni a konzol egy kis ideig. Az interaktív kimeneti megszakításához nyomja le a Ctrl + C. 

### <a name="resume-a-failed-update-operation"></a>Egy frissítés sikertelen folytatása

Ha a frissítés sikertelen, folytathatja a frissítési menetben, ahol abbahagyta.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Hibaelhárítás

A privilegizált végpont összes ERCS virtuális gépet az Azure-verem környezetben érhető el. A kapcsolat nem egy magas rendelkezésre állású végpontra, mert tapasztalhatja alkalmi megszakításokat, figyelmeztető vagy hibaüzenetek. Ezek az üzenetek azt jelezheti, hogy a munkamenet meg lett szakítva, vagy hogy hiba történt a Dokumentumokat szolgáltatással folytatott kommunikáció. Ez az elvárt viselkedés. Próbálja megismételni a műveletet, néhány perc múlva, vagy kiemelt végpont új munkamenetet létrehozni egy más ERCS virtuális gépeket. 

## <a name="next-steps"></a>Következő lépések

- [Azure-készletben frissítések kezelése](azure-stack-updates.md) 


