---
title: Azure Automation-adatok kezelése
description: Ez a cikk egy Azure Automation-környezet kezelése több témaköröket tartalmazza.  Jelenleg magában foglalja az adatok megőrzése és az Azure Automation vész-helyreállítási az Azure Automationben biztonsági mentéséről.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b9245a0a81958f1044ad5be6f5448e086ba54636
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="managing-azure-automation-data"></a>Azure Automation-adatok kezelése
Ez a cikk egy Azure Automation-környezet kezelése több témaköröket tartalmazza.

## <a name="data-retention"></a>Adatmegőrzés
Ha töröl egy erőforrást az Azure Automationben, véglegesen eltávolításuk előtt naplózási célokra 90 napig őrzi meg.  Lásd: nem, és ebben az időszakban az erőforrás használatára.  Ez a házirend vonatkozik tartozó törölnek automation-fiók is.

Azure Automation szolgáltatásbeli automatikusan törli, és véglegesen törli a 90 napnál régebbi feladatok.

A következő táblázat összefoglalja az adatmegőrzési különböző erőforrások.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |Véglegesen törli a 90 nap után a felhasználó által a fiókot törölték. |
| Objektumok |Véglegesen törli a 90 nap után az eszköz törlése a felhasználó vagy a fiókot, amely tárolja a felhasználó az eszköz törlése után 90 nappal. |
| Modulok |A modul a felhasználó törlése után 90 nappal vagy számított 90 napon belül a fiókot, amely rendelkezik a modul törlése a felhasználó véglegesen törli. |
| Runbookok |Véglegesen törli a 90 nap után a felhasználó törlése az erőforrás vagy számított 90 napon belül a fiókot, amelyet az erőforrás törlése a felhasználó rendelkezik. |
| Feladatok |Törölt és véglegesen eltávolított 90 nap után utolsó módosítás alatt. Ennek oka az lehet, miután a feladat befejeződött, leállt, vagy fel van függesztve. |
| Csomópont-konfigurációk/MOF-fájlok |Régi csomópont-konfigurálás 90 nap után létrejön egy új csomópont-konfiguráció véglegesen törlődnek. |
| A DSC-csomópontok |Véglegesen törli a 90 nap után a csomópont regisztrációját az Azure portál használatával Automation-fiók vagy a [Unregister-AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) a Windows PowerShell parancsmag. Csomópontok is véglegesen eltávolítja a fiókot, amely tartalmazza a csomópontot a felhasználó törlése után 90 nappal. |
| Csomópont-jelentések |Véglegesen törli a 90 nap után egy új jelentés készül az adott csomópont |

Az adatmegőrzési házirend minden felhasználójára vonatkoznak, és jelenleg nem szabható testre.

Azonban ha megőrizhetők az adatok egy hosszabb ideig van szüksége, továbbíthatja a runbook Log Analytics-feladat naplókat.  További információkért tekintse át [Azure Automation-feladat adatok továbbítására Naplóelemzési](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Az Azure Automation biztonsági mentése
A Microsoft Azure automation-fiók törlésekor a fiók összes objektum törlődnek, beleértve a runbookok, modulok, konfigurációk, beállítások, feladatok és eszközök. Az objektumok nem állítható helyre, miután a fiókot törölték.  Az alábbi információk segítségével biztonsági mentés a tartalmát az automation-fiók törlése előtt. 

### <a name="runbooks"></a>Runbookok
A runbookok exportálása parancsfájlok vagy az Azure portál használatával vagy a [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) a Windows PowerShell parancsmag.  Ezek a parancsfájlok importálhatja egy másik automation-fiók leírtaknak megfelelően [létrehozása vagy importálása egy Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Integrációs modulok
Azure Automation integrációs modulok nem exportálható.  Gondoskodnia kell arról, hogy elérhetők, kívül az automation-fiók.

### <a name="assets"></a>Objektumok
Nem lehet exportálni [eszközök](https://msdn.microsoft.com/library/dn939988.aspx) Azure Automation.  Az Azure portál használatával kell vegye figyelembe a változók, a hitelesítő adatokat, a tanúsítványok, a kapcsolatok és a ütemezések részleteit.  Ezután manuálisan kell létrehozni minden egy másik automation alkalmazásba importált runbookok által használt eszközök.

Használhat [Azure-parancsmagokkal](https://msdn.microsoft.com/library/dn690262.aspx) beolvasni részleteit titkosítatlan eszközök, és menti azokat a jövőben, vagy ezzel egyenértékű eszközöket egy másik automation-fiókban létrehozni.

A titkosított változók vagy a jelszó mező parancsmagokkal hitelesítő adatok értéke nem olvasható be.  Ha nem tudja ezeket az értékeket, majd azokat egy runbook használatával kérheti le a [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) és [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) tevékenységeket.

Azure Automation tanúsítványok nem exportálható.  Gondoskodnia kell arról, hogy elérhetők-e a tanúsítványok Azure-on kívüli.

### <a name="dsc-configurations"></a>DSC-konfigurációk
A beállítások exportálhatók arra a parancsfájlok vagy az Azure portál használatával vagy a [Export-AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) a Windows PowerShell parancsmag. Ezek a konfigurációk importálja, majd egy másik automation-fiókot használni.

## <a name="geo-replication-in-azure-automation"></a>Az Azure Automationben georeplikáció
Georeplikáció, az Azure Automation-fiók, standard biztonsági másolatot készít fiók adatait más földrajzi régióban a redundancia érdekében. Választhat egy elsődleges régióban, a fiók beállításakor, és ezután egy másodlagos régióban nem rendel hozzá automatikusan. A másodlagos adatok, az elsődleges régióban, átmásolva folyamatosan frissítjük adatvesztés esetén.  

A következő táblázat a rendelkezésre álló elsődleges és másodlagos régióban párosítása.

| Elsődleges | Másodlagos |
| --- | --- |
| USA déli középső régiója |USA északi középső régiója |
| USA keleti régiója 2 |USA középső régiója |
| Nyugat-Európa |Észak-Európa |
| Délkelet-Ázsia |Kelet-Ázsia |
| Kelet-Japán |Nyugat-Japán |

A valószínűtlen esemény, hogy egy elsődleges régió adatok nem vesztek el a Microsoft megpróbálja végezze el a helyreállítást. Ha az elsődleges adatok nem állítható helyre, majd elvégzi a földrajzi-feladatátvételt, és az érintett ügyfeleket értesítést fog kapni erről a előfizetéssel.

