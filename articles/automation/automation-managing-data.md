---
title: Azure Automation-adatok kezelése
description: Ez a cikk több témakört tartalmaz egy Azure Automation-környezet kezeléséhez.  Jelenleg tartalmazza az azure-automatizálási vész-helyreállítási rendszer adatmegőrzési és biztonsági mentését az Azure Automationben.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984657"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-adatok kezelése

Ez a cikk több témakört tartalmaz az Azure Automation-környezetben az adatok kezeléséhez.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="data-retention"></a>Adatmegőrzés

Amikor töröl egy erőforrást az Azure Automationben, az a végleges eltávolítás előtt több napig megmarad naplózási célokra. Ez idő alatt nem láthatja és nem használhatja az erőforrást. Ez a házirend a törölt Automation-fiókhoz tartozó erőforrásokra is vonatkozik.

Az alábbi táblázat a különböző erőforrások adatmegőrzési szabályát foglalja össze.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |A fiók véglegesen eltávolításra kerül 30 nappal azután, hogy a felhasználó törölte azt. |
| Objektumok |Az eszköz véglegesen eltávolításra kerül 30 nappal azután, hogy a felhasználó törölte, vagy 30 nappal azt követően, hogy a felhasználó törli az eszközt tartalmazó fiókot. |
| DSC-csomópontok |A DSC-csomópont véglegesen törlődik 30 nappal azután, hogy nem regisztrált egy Automation-fiók az Azure Portalon vagy a [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) parancsmag a Windows PowerShellben. A csomópont is véglegesen törlődik 30 nappal azután, hogy a felhasználó törli a csomópontot tartó fiókot. |
| Feladatok |Egy feladat törlődik, és véglegesen eltávolítja 30 nappal a módosítás után, például a feladat befejezése után, leáll, vagy felfüggesztették. |
| Modulok |A modul véglegesen törlődik 30 nappal azután, hogy a felhasználó törli azt, vagy 30 nappal azt követően, hogy a felhasználó törli a modult tartalmazó fiókot. |
| Csomópontkonfigurációk/MOF-fájlok |Egy régi csomópontkonfiguráció véglegesen eltávolításra kerül 30 nappal az új csomópontkonfiguráció létrehozása után. |
| Csomópontjelentések |A csomópontjelentést a rendszer 90 nappal az adott csomóponthoz létrehozott új jelentés létrehozása után véglegesen eltávolítja. |
| Runbookok |A runbook véglegesen törlődik 30 nappal azután, hogy a felhasználó törli az erőforrást, vagy 30 nappal azt követően, hogy a felhasználó törli az erőforrást tartalmazó fiókot. |

Az adatmegőrzési szabály minden felhasználóra vonatkozik, és jelenleg nem szabható testre. Ha azonban hosszabb ideig kell megőriznie az adatokat, [továbbíthatja az Azure Automation-feladatadatait az Azure Monitor naplóiba.](automation-manage-send-joblogs-log-analytics.md)

## <a name="data-backup"></a>Adatok biztonsági mentése

Ha töröl egy Automation-fiókot az Azure-ban, a fiók összes objektuma törlődik. Az objektumok közé runbookok, modulok, konfigurációk, beállítások, feladatok és eszközök. A fiók törlése után nem lehet visszaőket visszavonni. Az alábbi információk segítségével biztonsági másolatot lehet fésülni az Automation-fiók tartalmáról a törlés előtt.

### <a name="runbooks"></a>Runbookok

A runbookok parancsfájlokba exportálhatók az Azure Portalon vagy a [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) parancsmagban a Windows PowerShellben. Ezeket a parancsfájlokat importálhatja egy másik Automation-fiókba, ahogy azt az [Azure Automation runbookok kezelése](manage-runbooks.md)ismerteti.

### <a name="integration-modules"></a>Integrációs modulok

Az Azure Automation integrációs moduljai nem exportálhatók. Az Automation-fiókon kívül is elérhetővé kell tennie őket.

### <a name="assets"></a>Objektumok

Az Azure Automation-eszközök nem exportálhatók: tanúsítványok, kapcsolatok, hitelesítő adatok, ütemezések és változók. Ehelyett használhatja az Azure Portalon és az Azure-parancsmagok megjegyzése ezen eszközök részleteit. Ezután használja ezeket az adatokat, hogy hozzon létre olyan eszközöket, amelyek által használt runbookok, amelyek importálják egy másik Automation-fiókba.

A titkosított változók vagy a hitelesítő adatok jelszómezői nem olvashatók be parancsmagokkal. Ha nem ismeri ezeket az értékeket, bekérheti őket egy runbookban. A változóértékek beolvasása az [Azure Automation változóeszközeicímű témakörben](shared-resources/variables.md)található. A hitelesítő adatok értékeinek beolvasásáról az [Azure Automation hitelesítő adatokról](shared-resources/credentials.md)szóló témakörben talál további információ.

 ### <a name="dsc-configurations"></a>DSC-konfigurációk

A DSC-konfigurációk parancsfájlfájlokba exportálhatók az Azure Portalon vagy a Windows PowerShell [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) parancsmagjában. Ezeket a konfigurációkat importálhatja és használhatja egy másik Automation-fiókban.

## <a name="geo-replication-in-azure-automation"></a>Georeplikáció az Azure Automationben

A georeplikáció az Azure Automation-fiókokban szokásos. A fiók beállításakor elsődleges régiót választ. A belső automation georeplikációs szolgáltatás automatikusan másodlagos régiót rendel a fiókhoz. A szolgáltatás ezután folyamatosan biztonsági másolatot ad a fiókadatokról az elsődleges régióból a másodlagos régióba. Az elsődleges és másodlagos régiók teljes listája megtalálható [az Üzletmenet folytonossága és a vészhelyreállítás (BCDR): Azure Paired Regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Az Automation georeplikációs szolgáltatás által létrehozott biztonsági másolat az Automation-eszközök, konfigurációk és hasonlók teljes másolata. Ez a biztonsági mentés akkor használható, ha az elsődleges régió leáll, és elveszíti az adatokat. Abban a valószínűtlen esetben, ha egy elsődleges régió adatai elvesznek, a Microsoft megpróbálja helyreállítani azokat. Ha a vállalat nem tudja helyreállítani az elsődleges adatokat, automatikus feladatátvételt használ, és az Azure-előfizetésen keresztül tájékoztatja a helyzetről. 

Az Automation georeplikációs szolgáltatás nem érhető el közvetlenül a külső ügyfelek számára, ha regionális hiba van. Ha regionális hibák esetén meg szeretné őrizni az Automation-konfigurációt és a runbookokat:

1. Válasszon ki egy másodlagos régiót az elsődleges Automation-fiók földrajzi régiójával párosítva.

2. Hozzon létre egy Automation-fiókot a másodlagos régióban.

3. Az elsődleges fiókban exportálja a runbookokat parancsfájlfájlként.

4. Importálja a runbookokat az Automation-fiókba a másodlagos régióban.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a biztonságos eszközökről az Azure Automationben, olvassa [el a Biztonságos eszközök titkosítása az Azure Automationben című témakört.](automation-secure-asset-encryption.md)

* A georeplikációról az Aktív [georeplikáció létrehozása és használata](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)című témakörben talál további tudnivalót.