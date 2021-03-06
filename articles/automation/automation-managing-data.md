---
title: Adatbiztonság Azure Automation
description: Ebből a cikkből megtudhatja, hogyan védi a Azure Automation az adatvédelmet, és gondoskodik az adatok védelméről.
services: automation
ms.subservice: shared-capabilities
ms.date: 07/20/2020
ms.topic: conceptual
ms.openlocfilehash: 610c2050150a533e246bc74ed7750ce87f7cf617
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87004647"
---
# <a name="management-of-azure-automation-data"></a>Azure Automation-adatok kezelése

Ez a cikk számos olyan témakört tartalmaz, amelyekből megtudhatja, hogyan védi és biztosítja az adatvédelmet egy Azure Automation környezetben.

## <a name="tls-12-enforcement-for-azure-automation"></a>TLS 1,2 kényszerítés a Azure Automation

A Azure Automation felé irányuló adatforgalom biztosításához határozottan javasoljuk, hogy konfigurálja a Transport Layer Security (TLS) 1,2 használatát. Az alábbi lista azokat a módszereket vagy ügyfeleket sorolja fel, amelyek HTTPS protokollon keresztül kommunikálnak az Automation szolgáltatással:

* Webhook-hívások

* Hibrid Runbook-feldolgozók, beleértve a Update Management és a Change Tracking és a leltár által felügyelt gépeket.

* DSC-csomópontok

A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**. A 2020. szeptembertől kezdődően megkezdjük a TLS 1,2 és a titkosítási protokoll újabb verzióinak betartatását.

Nem ajánlott explicit módon beállítani az ügynököt úgy, hogy csak a TLS 1,2-et használja, kivéve, ha ez nem feltétlenül szükséges, mivel ez a platform szintű biztonsági funkciókat is lehetővé teszi, amelyekkel automatikusan észlelhetők és kihasználhatják az elérhetővé váló újabb biztonságosabb protokollokat, például a TLS 1,3.

További információ a Windows és Linux rendszerhez készült Log Analytics ügynökkel való TLS 1,2-támogatásról, amely a hibrid Runbook-feldolgozói szerepkörtől függ: [log Analytics ügynök áttekintése – TLS 1,2](..//azure-monitor/platform/log-analytics-agent.md#tls-12-protocol). 

### <a name="platform-specific-guidance"></a>Platform-specifikus útmutató

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux-disztribúciók általában az [OpenSSL](https://www.openssl.org) -t használják a TLS 1,2 támogatásához.  | Ellenőrizze az OpenSSL- [changelog](https://www.openssl.org/news/changelog.html) , hogy az OpenSSL verziója támogatott-e.|
| Windows 8,0 – 10 | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak megerősítéséhez, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings)használja.  |
| Windows Server 2012 – 2016 | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings) használja-e |
| Windows 7 SP1 és Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Az engedélyezésével kapcsolatos részletekért tekintse meg a [Transport Layer Security (TLS) beállításjegyzék-beállítások](/windows-server/security/tls/tls-registry-settings) lapját.  |

## <a name="data-retention"></a>Adatmegőrzés

Amikor töröl egy erőforrást a Azure Automationban, a rendszer a végleges eltávolítás előtt több napra megőrzi a naplózási célokat. Ebben az időszakban nem tekintheti meg és nem használhatja az erőforrást. Ez a szabályzat a törölt Automation-fiókhoz tartozó erőforrásokra is vonatkozik.

A következő táblázat összefoglalja a különböző erőforrások adatmegőrzési szabályzatát.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |Egy fiók a felhasználó törlése után 30 nappal véglegesen el lesz távolítva. |
| Objektumok |Egy eszköz a felhasználó törlése után 30 nappal véglegesen el lesz távolítva, vagy 30 nappal azután, hogy egy felhasználó töröl egy olyan fiókot, amely tartalmazza az adategységet. Az eszközök közé tartoznak a változók, az ütemtervek, a hitelesítő adatok, a tanúsítványok, a Python 2 csomagok és a kapcsolatok. |
| DSC-csomópontok |A DSC-csomópontok véglegesen el lettek távolítva egy Automation-fiókból a Windows PowerShellben Azure Portal vagy a [Regisztráció törlése-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) parancsmag használatával. Egy csomópontot is véglegesen eltávolít 30 nappal azután, hogy a felhasználó törli a csomópontot tároló fiókot. |
| Feladatok |A rendszer töröl egy feladatot, és véglegesen eltávolítja azt a módosítás után 30 nappal, például a feladatok befejeződése után, leáll vagy felfüggesztve. |
| Modulok |Egy modul a felhasználó törlése után 30 nappal véglegesen el lesz távolítva, vagy 30 nappal azután, hogy a felhasználó törli a modult tartalmazó fiókot. |
| Csomópont-konfigurációk/MOF-fájlok |Egy régi csomópont-konfiguráció véglegesen el lesz távolítva egy új csomópont-konfiguráció generálása után 30 nappal. |
| Csomópont-jelentések |Az adott csomópontra vonatkozó új jelentés létrehozása után a Node-jelentés 90 nappal véglegesen törlődik. |
| Runbookok |Egy runbook véglegesen el lett távolítva 30 nappal azután, hogy egy felhasználó törli az erőforrást, vagy 30 nappal azután, hogy egy felhasználó törli az erőforrást tároló fiókot. |

Az adatmegőrzési szabályzat minden felhasználóra érvényes, és jelenleg nem szabható testre. Ha azonban hosszabb ideig kell megtartania az adatokra vonatkozó adatmegőrzést, [Azure Automation feladataikat Azure monitor naplókhoz továbbíthatja](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Az adatbiztonsági mentés

Ha töröl egy Automation-fiókot az Azure-ban, a fiókban lévő összes objektum törlődik. Az objektumok közé tartoznak a runbookok, a modulok, a konfigurációk, a beállítások, a feladatok és az eszközök. A fiók törlése után nem állíthatók helyre. A következő információk segítségével biztonsági másolatot készíthet az Automation-fiók tartalmáról a törlés előtt.

### <a name="runbooks"></a>Runbookok

A runbookok a Windows PowerShell Azure Portal vagy a [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) parancsmagjának használatával exportálhatja parancsfájl-fájlokba. Ezeket a parancsfájlokat egy másik Automation-fiókba importálhatja, ahogy azt a [Azure Automation Runbookok kezelése](manage-runbooks.md)című szakaszban tárgyaljuk.

### <a name="integration-modules"></a>Integrációs modulok

Azure Automationból nem exportálhatja az integrációs modulokat. Ezeket az Automation-fiókon kívül is elérhetővé kell tenni.

### <a name="assets"></a>Objektumok

Azure Automation-eszközök nem exportálhatók: tanúsítványok, kapcsolatok, hitelesítő adatok, ütemtervek és változók. Ehelyett a Azure Portal és az Azure-parancsmagokkal is megjegyezheti az eszközök részleteit. Ezután ezekkel az adatokkal létrehozhat olyan eszközöket, amelyeket egy másik Automation-fiókba importált runbookok használ.

A titkosított változók vagy a hitelesítő adatok jelszava mezők a parancsmagok használatával nem olvashatók be. Ha nem ismeri ezeket az értékeket, lekérheti őket egy runbook. A változók értékeinek lekéréséhez lásd: [változó eszközök Azure Automationban](shared-resources/variables.md). A hitelesítő adatok értékének beolvasásával kapcsolatos további tudnivalókért tekintse meg a [hitelesítő adatok a Azure Automationban](shared-resources/credentials.md)című témakört.

### <a name="dsc-configurations"></a>DSC-konfigurációk

A DSC-konfigurációkat a Windows PowerShell Azure Portal vagy [export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0) parancsmagjának használatával is exportálhatja a parancsfájlok fájljaiba. Ezeket a konfigurációkat egy másik Automation-fiókban is importálhatja és használhatja.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikálás Azure Automation

A Geo-replikáció a Azure Automation fiókokban szabványos. A fiók beállításakor ki kell választania egy elsődleges régiót. A belső Automation geo-Replication szolgáltatás automatikusan hozzárendel egy másodlagos régiót a fiókhoz. A szolgáltatás ezután folyamatosan biztonsági mentést készít az elsődleges régióból a másodlagos régióba. Az elsődleges és másodlagos régiók teljes listája megtalálható az [üzletmenet folytonossága és a vész-helyreállítási (BCDR): Azure párosított régiók](../best-practices-availability-paired-regions.md)között.

Az Automation geo-Replication szolgáltatás által létrehozott biztonsági másolat az Automation-eszközök, konfigurációk és hasonlók teljes másolata. Ez a biztonsági másolat akkor használható, ha az elsődleges régió leáll, és elveszíti az adatvesztést. Abban az esetben, ha az elsődleges régió adatainak elvesztése nem valószínű, a Microsoft megkísérli a helyreállítást. Ha a vállalat nem tudja helyreállítani az elsődleges adatokat, automatikus feladatátvételt használ, és az Azure-előfizetésén keresztül tájékoztatja Önt a helyzetről.

Az Automation geo-Replication szolgáltatás nem érhető el közvetlenül a külső ügyfelek számára, ha van regionális hiba. Ha az Automation-konfigurációt és a runbookok a regionális hibák során szeretné fenntartani:

1. Válasszon ki egy másodlagos régiót, amely párosítva van az elsődleges Automation-fiók földrajzi régiójával.

2. Hozzon létre egy Automation-fiókot a másodlagos régióban.

3. Az elsődleges fiókban a runbookok fájlba exportálja.

4. Importálja a runbookok az Automation-fiókjába a másodlagos régióban.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a Azure Automation található biztonságos eszközökről, tekintse meg a biztonságos adategységek [titkosítása Azure Automation-ben](automation-secure-asset-encryption.md)című témakört.

* További információ a Geo-replikációról: [Active geo-replikáció létrehozása és használata](../azure-sql/database/active-geo-replication-overview.md).
