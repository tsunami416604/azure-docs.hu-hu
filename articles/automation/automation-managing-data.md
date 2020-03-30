---
title: Azure Automation-adatok kezelése
description: Ez a cikk több témakört tartalmaz egy Azure Automation-környezet kezeléséhez.  Jelenleg tartalmazza az azure-automatizálási vész-helyreállítási rendszer adatmegőrzési és biztonsági mentését az Azure Automationben.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132899"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-adatok kezelése

Ez a cikk több témakört tartalmaz egy Azure Automation-környezet kezeléséhez.

## <a name="data-retention"></a>Adatmegőrzés

Amikor töröl egy erőforrást az Azure Automationben, az 30 napig megmarad naplózási célokra, mielőtt véglegesen eltávolítané. Ez idő alatt nem láthatja és nem használhatja az erőforrást. Ez a házirend a törölt automatizálási fiókhoz tartozó erőforrásokra is vonatkozik.

Az Azure Automation automatikusan törli és véglegesen eltávolítja a 30 napnál régebbi feladatokat.

Az alábbi táblázat a különböző erőforrások adatmegőrzési szabályát foglalja össze.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |30 nappal a fiók felhasználó általi törlése után véglegesen eltávolítva. |
| Objektumok |30 nappal az eszköz felhasználó általi törlése után, vagy 30 nappal azt követően, hogy a felhasználó törli az eszközt tartalmazó fiókot. |
| Modulok |30 nappal azután, hogy a felhasználó törölte a modult, vagy 30 nappal azt követően, hogy a felhasználó törli a modult tartalmazó fiókot. |
| Runbookok |30 nappal az erőforrás felhasználó általi törlése után, vagy 30 nappal azt követően, hogy a felhasználó törölte az erőforrást tartalmazó fiókot. |
| Feladatok |Az utolsó módosítás után 30 nappal törölve és véglegesen eltávolítva. Ez a feladat befejezése, leállítása vagy felfüggesztése után történhet. |
| Csomópontkonfigurációk/MOF-fájlok |A régi csomópontkonfiguráció véglegesen eltávolításra kerül 30 nappal az új csomópontkonfiguráció létrehozása után. |
| DSC-csomópontok |30 nappal azután, hogy a csomópont nincs regisztrálva az Automation-fiókból az Azure Portal vagy az [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) parancsmag a Windows PowerShellben. A csomópontok at is véglegesen eltávolítja 30 nappal azt követően, hogy a felhasználó törli a csomópontot tartalmazó fiókot. |
| Csomópontjelentések |90 nappal az adott csomópontra vonatkozó új jelentés létrehozása után 90 nappal véglegesen eltávolítva |

Az adatmegőrzési szabály minden felhasználóra vonatkozik, és jelenleg nem szabható testre.

Ha azonban hosszabb ideig kell megőriznie az adatokat, továbbíthatja a runbook-feladatnaplókat az Azure Monitor-naplókba. További információkért tekintse [át az Azure Automation-feladat adatainak továbbítását az Azure Monitor naplóiba.](automation-manage-send-joblogs-log-analytics.md)

## <a name="backing-up-azure-automation"></a>Az Azure Automation biztonsági mentése

Ha töröl egy automatizálási fiókot a Microsoft Azure-ban, a fiók összes objektuma törlődik, beleértve a runbookokat, modulokat, konfigurációkat, beállításokat, feladatokat és eszközöket. Az objektumok a fiók törlése után nem állíthatók helyre.  Az alábbi információk segítségével biztonsági másolatot készíthet az automatizálási fiók tartalmáról a törlés előtt.

### <a name="runbooks"></a>Runbookok

A runbookok parancsfájlokba exportálhatók az Azure Portalon vagy a [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) parancsmagban a Windows PowerShellben. Ezek a parancsfájlok importálhatók egy másik automatizálási fiókba, ahogy azt a [Runbook létrehozása vagy importálása](/previous-versions/azure/dn643637(v=azure.100))című részben tárgyaljuk.

### <a name="integration-modules"></a>Integrációs modulok

Az Azure Automation integrációs moduljai nem exportálhatók. Győződjön meg arról, hogy az automatizálási fiókon kívül érhetők el.

### <a name="assets"></a>Objektumok

Az Azure Automationből nem exportálhat [eszközöket.](/previous-versions/azure/dn939988(v=azure.100))  Az Azure Portal használatával meg kell jegyeznie a változók, hitelesítő adatok, tanúsítványok, kapcsolatok és ütemezések részleteit.  Ezután manuálisan létre kell hoznia minden olyan eszközt, amelyet a runbookok használnak, és amelyeket egy másik automatizálásba importál.

[Az Azure-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) segítségével lekérheti a titkosítatlan eszközök részleteit, és vagy menti őket későbbi használatra, vagy hozzon létre egyenértékű eszközöket egy másik automatizálási fiókban.

A titkosított változók vagy a hitelesítő adatok jelszómezője parancsmagokkal nem olvasható be. Ha nem ismeri ezeket az értékeket, majd lekérheti őket egy runbooka [get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) és [get-automationpscredential](/previous-versions/azure/dn940015(v=azure.100)) tevékenységek használatával.

Az Azure Automation tanúsítványai nem exportálhatók. Győződjön meg arról, hogy a tanúsítványok az Azure-on kívül érhetők el.

### <a name="dsc-configurations"></a>DSC-konfigurációk

A konfigurációkat parancsfájlfájlokba exportálhatja az Azure Portalon vagy az [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) parancsmagban a Windows PowerShellben. Ezek a konfigurációk importálhatók és használhatók egy másik automatizálási fiókban.

## <a name="geo-replication-in-azure-automation"></a>Georeplikáció az Azure Automationben

Az Azure Automation-fiókokban szokásos georeplikáció biztonsági másolatot ad a fiókadatokról egy másik földrajzi régióba a redundancia érdekében. A fiók beállításakor kiválaszthatja az elsődleges régiót, majd automatikusan hozzárendel egy másodlagos régiót. Az elsődleges régióból másolt másodlagos adatok adatvesztés esetén folyamatosan frissülnek.  

Az alábbi táblázat a rendelkezésre álló elsődleges és másodlagos régiópárosításokat mutatja be.

| Elsődleges | Másodlagos |
| --- | --- |
| USA déli középső régiója |USA északi középső régiója |
| USA 2. keleti régiója |USA középső régiója |
| Nyugat-Európa |Észak-Európa |
| Délkelet-Ázsia |Kelet-Ázsia |
| Kelet-Japán |Nyugat-Japán |

Abban a valószínűtlen esetben, ha egy elsődleges régió adatai elvesznek, a Microsoft megpróbálja helyreállítani azokat. Ha az elsődleges adatok nem állíthatók helyre, majd a földrajzi feladatátvétel történik, és az érintett ügyfelek értesítést kapnak erről az előfizetésükön keresztül.
