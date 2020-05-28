---
title: Azure Automation-adatok kezelése
description: Ez a cikk a Azure Automation adatkezelésének fogalmait tartalmazza, beleértve az adatmegőrzést és a biztonsági mentést.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835206"
---
# <a name="management-of-azure-automation-data"></a>Azure Automation-adatok kezelése

Ez a cikk számos témakört tartalmaz az adatAzure Automation-környezetekben történő kezeléséhez.

## <a name="data-retention"></a>Adatmegőrzés

Amikor töröl egy erőforrást a Azure Automationban, a rendszer a végleges eltávolítás előtt több napra megőrzi a naplózási célokat. Ebben az időszakban nem tekintheti meg és nem használhatja az erőforrást. Ez a szabályzat a törölt Automation-fiókhoz tartozó erőforrásokra is vonatkozik.

A következő táblázat összefoglalja a különböző erőforrások adatmegőrzési szabályzatát.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |Egy fiók a felhasználó törlése után 30 nappal véglegesen el lesz távolítva. |
| Eszközök |Egy eszköz a felhasználó törlése után 30 nappal véglegesen el lesz távolítva, vagy 30 nappal azután, hogy egy felhasználó töröl egy olyan fiókot, amely tartalmazza az adategységet. |
| DSC-csomópontok |A DSC-csomópontok véglegesen el lettek távolítva egy Automation-fiókból a Windows PowerShellben Azure Portal vagy a [Regisztráció törlése-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) parancsmag használatával. Egy csomópontot is véglegesen eltávolít 30 nappal azután, hogy a felhasználó törli a csomópontot tároló fiókot. |
| Feladatok |A rendszer töröl egy feladatot, és véglegesen eltávolítja azt a módosítás után 30 nappal, például a feladatok befejeződése után, leáll vagy felfüggesztve. |
| Modulok |Egy modul a felhasználó törlése után 30 nappal véglegesen el lesz távolítva, vagy 30 nappal azután, hogy a felhasználó törli a modult tartalmazó fiókot. |
| Csomópont-konfigurációk/MOF-fájlok |Egy régi csomópont-konfiguráció véglegesen el lesz távolítva egy új csomópont-konfiguráció generálása után 30 nappal. |
| Csomópont-jelentések |Az adott csomópontra vonatkozó új jelentés létrehozása után a Node-jelentés 90 nappal véglegesen törlődik. |
| Runbookok |Egy runbook véglegesen el lett távolítva 30 nappal azután, hogy egy felhasználó törli az erőforrást, vagy 30 nappal azután, hogy egy felhasználó törli az erőforrást tároló fiókot. |

Az adatmegőrzési szabályzat minden felhasználóra érvényes, és jelenleg nem szabható testre. Ha azonban hosszabb ideig kell megtartania az adatokra vonatkozó adatmegőrzést, [Azure Automation feladataikat Azure monitor naplókhoz továbbíthatja](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Az adatbiztonsági mentés

Ha töröl egy Automation-fiókot az Azure-ban, a fiókban lévő összes objektum törlődik. Az objektumok közé tartoznak a runbookok, a modulok, a konfigurációk, a beállítások, a feladatok és az eszközök. A fiók törlése után nem állíthatók helyre. A következő információk segítségével biztonsági másolatot készíthet az Automation-fiók tartalmáról a törlés előtt.

### <a name="runbooks"></a>Runbookok

A runbookok a Windows PowerShell Azure Portal vagy a [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) parancsmagjának használatával exportálhatja parancsfájl-fájlokba. Ezeket a parancsfájlokat egy másik Automation-fiókba importálhatja, ahogy azt a [Azure Automation Runbookok kezelése](manage-runbooks.md)című szakaszban tárgyaljuk.

### <a name="integration-modules"></a>Integrációs modulok

Azure Automationból nem exportálhatja az integrációs modulokat. Ezeket az Automation-fiókon kívül is elérhetővé kell tenni.

### <a name="assets"></a>Eszközök

Azure Automation-eszközök nem exportálhatók: tanúsítványok, kapcsolatok, hitelesítő adatok, ütemtervek és változók. Ehelyett a Azure Portal és az Azure-parancsmagokkal is megjegyezheti az eszközök részleteit. Ezután ezekkel az adatokkal létrehozhat olyan eszközöket, amelyeket egy másik Automation-fiókba importált runbookok használ.

A titkosított változók vagy a hitelesítő adatok jelszava mezők a parancsmagok használatával nem olvashatók be. Ha nem ismeri ezeket az értékeket, lekérheti őket egy runbook. A változók értékeinek lekéréséhez lásd: [változó eszközök Azure Automationban](shared-resources/variables.md). A hitelesítő adatok értékének beolvasásával kapcsolatos további tudnivalókért tekintse meg a [hitelesítő adatok a Azure Automationban](shared-resources/credentials.md)című témakört.

 ### <a name="dsc-configurations"></a>DSC-konfigurációk

A DSC-konfigurációkat a Windows PowerShell Azure Portal vagy [export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) parancsmagjának használatával is exportálhatja a parancsfájlok fájljaiba. Ezeket a konfigurációkat egy másik Automation-fiókban is importálhatja és használhatja.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikálás Azure Automation

A Geo-replikáció a Azure Automation fiókokban szabványos. A fiók beállításakor ki kell választania egy elsődleges régiót. A belső Automation geo-Replication szolgáltatás automatikusan hozzárendel egy másodlagos régiót a fiókhoz. A szolgáltatás ezután folyamatosan biztonsági mentést készít az elsődleges régióból a másodlagos régióba. Az elsődleges és másodlagos régiók teljes listája megtalálható az [üzletmenet folytonossága és a vész-helyreállítási (BCDR): Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)között. 

Az Automation geo-Replication szolgáltatás által létrehozott biztonsági másolat az Automation-eszközök, konfigurációk és hasonlók teljes másolata. Ez a biztonsági másolat akkor használható, ha az elsődleges régió leáll, és elveszíti az adatvesztést. Abban az esetben, ha az elsődleges régió adatainak elvesztése nem valószínű, a Microsoft megkísérli a helyreállítást. Ha a vállalat nem tudja helyreállítani az elsődleges adatokat, automatikus feladatátvételt használ, és az Azure-előfizetésén keresztül tájékoztatja Önt a helyzetről. 

Az Automation geo-Replication szolgáltatás nem érhető el közvetlenül a külső ügyfelek számára, ha van regionális hiba. Ha az Automation-konfigurációt és a runbookok a regionális hibák során szeretné fenntartani:

1. Válasszon ki egy másodlagos régiót, amely párosítva van az elsődleges Automation-fiók földrajzi régiójával.

2. Hozzon létre egy Automation-fiókot a másodlagos régióban.

3. Az elsődleges fiókban a runbookok fájlba exportálja.

4. Importálja a runbookok az Automation-fiókjába a másodlagos régióban.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a Azure Automation található biztonságos eszközökről, tekintse meg a biztonságos adategységek [titkosítása Azure Automation-ben](automation-secure-asset-encryption.md)című témakört.
* További információ a Geo-replikációról: [Active geo-replikáció létrehozása és használata](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).