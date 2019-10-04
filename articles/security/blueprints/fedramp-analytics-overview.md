---
title: Azure Security and Compliance Blueprint-Analytics a FedRAMP
description: Azure Security and Compliance Blueprint-Analytics a FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 9850c5f064815315db6f85a931e7e175d605dcc1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257587"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure Security and Compliance Blueprint: FedRAMP-elemzés

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) egy olyan Egyesült Államok kormányzati szintű program, amely szabványosított megközelítést biztosít a biztonság értékeléséhez, engedélyezéséhez és a felhőalapú termékek és szolgáltatások folyamatos monitorozásához. Ez a Azure Security and Compliance Blueprint útmutatást nyújt egy olyan Microsoft Azure elemzési architektúra megvalósításához, amely segít a FedRAMP magas szintű vezérlők részhalmazának megvalósításában. Ez a megoldás útmutatást nyújt az Azure-erőforrások általános hivatkozási architektúrához való üzembe helyezéséhez és konfigurálásához, és bemutatja, hogy az ügyfelek milyen módon tudják kielégíteni a konkrét biztonsági és megfelelőségi követelményeket, és alapként szolgálnak az ügyfelek számára saját analitikai megoldásaikat hozhat létre és konfigurálhat az Azure-ban.

Ez a hivatkozási architektúra, a társított vezérlés megvalósítási útmutatói és a veszélyforrási modellek arra szolgálnak, hogy az ügyfelek számára alapvető fontosságúak legyenek, és ne használják éles környezetben. Ha módosítás nélkül helyezi üzembe az alkalmazást a környezetbe, nem elegendő a FedRAMP magas alapkonfigurációjának követelményeinek teljes kielégítéséhez. Vegye figyelembe a következőket:
- Az architektúra olyan alapkonfigurációt biztosít, amellyel az ügyfelek FedRAMP-kompatibilis módon telepíthetik az Azure-beli számítási feladatokat.
- Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

Ez a megoldás egy elemzési platformot biztosít, amely alapján az ügyfelek létrehozhatják saját elemzési eszközeiket. A hivatkozási architektúra olyan általános használati esetet vázol fel, amelyben az ügyfelek az SQL/adat-rendszergazda vagy az operatív adatok frissítésein keresztül, egy operatív felhasználón keresztül adatokat küldenek a tömeges adatimportáláshoz. A munkafolyamatok mindkét [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) belefoglalják az adatSQL Databaseba való adatimportálást. Az Azure Functionst az Azure Portalon keresztül kell konfigurálni, hogy az egyes ügyfelek saját elemzési igényeihez egyedi importálási feladatokat kezeljen.

Microsoft Azure számos jelentéskészítési és elemzési szolgáltatást kínál az ügyfél számára; Ez a megoldás azonban Azure Analysis Servicest tartalmaz a Azure SQL Database segítségével, hogy gyorsan böngészhet az adatkezelésen, és gyorsabb eredményeket nyújtson az ügyféladatok intelligens modellezésével. Az Azure Analytics Services a gépi tanulás egyik formája, amely a lekérdezések sebességének növelésére szolgál az adathalmazok közötti új kapcsolatok felfedezésével. Ha az adatok több statisztikai függvényen keresztül lettek kitanítva, legfeljebb 7 további lekérdezési készlet (8 összesen, beleértve az ügyfél-kiszolgálót is) szinkronizálható ugyanazzal a táblázatos modellel a lekérdezési munkaterhelés elosztása és a válaszadási idő csökkentése érdekében.

A továbbfejlesztett elemzési és jelentéskészítési szolgáltatásokhoz az SQL-adatbázisok oszlopcentrikus indexekkel konfigurálhatók. Az Azure Analytics-szolgáltatások és az SQL-adatbázisok egyaránt méretezhetők akár akár le, akár le is állíthatók. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Az ajánlott eljárás az, hogy az Azure megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.

Miután a rendszer feltölti az adatfeldolgozást az Azure SQL Databaseba, és Azure Analysis Services által tanítja, azt az operatív felhasználó és az SQL/adatkezelő is megemészti a Power BI. Power BI intuitív módon jeleníti meg az adatokat, és több adatkészletben is összegyűjti az információkat, hogy jobban megtekintse a képet. A magas fokú alkalmazkodóképesség és a Azure SQL Database egyszerű integrálása biztosítja, hogy az ügyfelek az üzleti igényeknek megfelelően konfigurálják a forgatókönyvek széles körét.

A teljes megoldás egy Azure Storage-ra épül, amelyet az ügyfelek az Azure Portalról konfigurálnak. Az Azure Storage a Storage Service Encryption segítségével titkosítja az összes adatát, hogy megőrizze az inaktív adatok titkosságát.  A földrajzi redundáns tárolás (GRS) biztosítja, hogy az ügyfél elsődleges adatközpontjában az adatvesztés hiánya miatt a rendszer nem eredményez adatvesztést, mivel a rendszer egy másik, több száz kilométer távolságban található helyet tárol.

A fokozott biztonság érdekében ez az architektúra Azure Active Directoryokkal és Azure Key Vaultekkel felügyeli az erőforrásokat. A rendszer állapotának figyelése Azure Monitoron keresztül történik. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítéséhez és a rendszerállapot megjelenítéséhez egyetlen, könnyen navigálható irányítópulton.

Azure SQL Database általában SQL Server Management Studio (SSMS) használatával történik, amely egy olyan helyi gépről fut, amely a Azure SQL Database biztonságos VPN-vagy ExpressRoute-kapcsolaton keresztüli elérésére van konfigurálva. **Az Azure javasolja a VPN-vagy Azure ExpressRoute-kapcsolat konfigurálását a felügyeleti és adatimportálási szolgáltatásokhoz a hivatkozási architektúra erőforráscsoporthoz.**

![A FedRAMP-referenciák architektúrájának diagramja](images/fedramp-analytics-reference-architecture.png?raw=true "A FedRAMP-referenciák architektúrájának diagramja")

### <a name="roles"></a>Szerepkörök
Az elemzési terv három általános felhasználói típussal rendelkező forgatókönyvet vázol fel: az operatív felhasználót, az SQL/adatkezelőt és a rendszermérnöket. Az Azure szerepköralapú Access Control (RBAC) lehetővé teszi a pontos hozzáférés-kezelés megvalósítását a beépített egyéni szerepkörök használatával. A [szerepköralapú Access Control](../../role-based-access-control/role-assignments-portal.md) konfigurálásához és az [előre definiált szerepkörök](../../role-based-access-control/built-in-roles.md)megvalósításához és végrehajtásához rendelkezésre álló erőforrások.

#### <a name="systems-engineer"></a>Rendszermérnök
A rendszermérnök az Azure-hoz készült ügyfél-előfizetést használja, és az Azure Portalon konfigurálja a megoldás üzembe helyezését.

#### <a name="sqldata-administrator"></a>SQL-/adatkezelő
Az SQL/adat rendszergazdája létrehozza a tömeges adatimportálási funkciót és az operatív adatfrissítési függvényt az Azure SQL Database-be való feltöltéshez. Az SQL/adat rendszergazdája nem vállal felelősséget az adatbázis összes operatív adatfrissítésében, de az Power BIon keresztül megtekintheti az összes adatát.

#### <a name="operational-user"></a>Operatív felhasználó
Az operatív felhasználó rendszeresen frissíti az adatfeldolgozást, és a napi adatgenerálás tulajdonosa. Az operatív felhasználó a Power BIon keresztül is értelmezi az eredményeket.

### <a name="azure-services"></a>Azure-szolgáltatások

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei az [üzembe helyezési architektúra](#deployment-architecture) szakaszban találhatók.
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (naplók)
- Azure Storage
- ExpressRoute/VPN Gateway
- Power BI-irányítópult

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz részletesen ismerteti a fejlesztési és megvalósítási elemeket.

![helyettesítő szöveg](images/fedramp-analytics-components.png?raw=true "Elemzés a FedRAMP Components diagramhoz")

**Azure functions**: [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) a legtöbb programozási nyelv használatával a felhőben kis mennyiségű kód futtatására szolgáló megoldások. A megoldás funkciói az Azure Storage-nal együttműködve automatikusan lekérik a vásárlói adatok felhőbe való integrálását, így megkönnyítve az integrációt más Azure-szolgáltatásokkal. A függvények egyszerűen méretezhetők, és csak a futtatásuk után járnak költséggel.

**Azure Analysis Service**: Az [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) vállalati adatmodellezést és integrációt biztosít az Azure adatplatform-szolgáltatásaival. Az Azure Analysis Service nagy mennyiségű adattal felgyorsítja a böngészést azáltal, hogy több forrásból származó adatokkal egyetlen adatmodellbe egyesít.

**Power bi**: A [Power bi](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) elemzési és jelentéskészítési funkciókat biztosít az ügyfeleknek az adatfeldolgozási erőfeszítéseik jobb megismerésére.

### <a name="networking"></a>Hálózat
**Hálózati biztonsági csoportok**: A [NSG](../../virtual-network/virtual-network-vnet-plan-design-arm.md) úgy vannak beállítva, hogy az üzembe helyezett erőforrásokra és szolgáltatásokra irányuló forgalmat kezeljék. A hálózati biztonsági csoportok egy megtagadási alapértelmezett sémára vannak beállítva, és csak az előre konfigurált Access Control listán (ACL) található forgalmat engedélyezik.

Mindegyik NSG meg van nyitva bizonyos portok és protokollok, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk is engedélyezve vannak az egyes NSG:
  - A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
  - [Azure monitor naplók](../../azure-monitor/insights/azure-networking-analytics.md) a NSG diagnosztikai naplóihoz vannak csatlakoztatva.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Adatreplikálás** Azure Government két lehetőséget kínál az [adatreplikálásra](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Az adatreplikálás alapértelmezett beállítása a **geo-redundáns tárolás (GRS)** , amely aszinkron módon tárolja az ügyféladatokat az elsődleges régión kívüli különálló adatközpontban. Ez biztosítja az adathelyreállítást az elsődleges adatközponthoz tartozó összes veszteségi esemény során.
 - A **helyileg redundáns tárolást (LRS)** az Azure Storage-fiók használatával is konfigurálhatja. A LRS olyan tárolási méretezési egységen belül replikálja az adatmennyiséget, amely ugyanabban a régióban található, mint amelyben az ügyfél létrehozza a fiókját. Az összes adattal párhuzamosan replikálódik, így biztosítva, hogy a rendszer ne veszítse el a biztonsági mentési adatmennyiséget az elsődleges tárolási skálázási egység meghibásodásakor.

**Azure Storage** A titkosított adatoknak a nyugalmi követelmények kielégítése érdekében az ebben a viszonyítási architektúrában üzembe helyezett összes szolgáltatás kihasználja az Azure Storage-t, amely az adatok [Storage Service Encryptionekkel](../../storage/common/storage-service-encryption.md)való [tárolását](https://azure.microsoft.com/services/storage/)

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) a Windows BitLocker szolgáltatásával biztosítja a kötetek titkosítását az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database** A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:
-   Az [AD-hitelesítés és-engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásának kezelését egy központi helyen.
-   Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A SQL Database [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatára van konfigurálva, amely az adatok és naplófájlok valós idejű titkosítását és visszafejtését hajtja végre a további információk védelme érdekében. A TDE biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL-veszélyforrások észlelése](../../sql-database/sql-database-threat-detection.md) lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
-   [Always encrypted oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szövegként az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
-   [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) a hivatkozási architektúra telepítése után végezhető el. Az ügyfeleknek módosítaniuk kell a dinamikus adatmaszkolási beállításokat az adatbázis-sémájuk betartásához.

### <a name="logging-and-audit"></a>Naplózás és naplózás
A [Azure monitor](../../azure-monitor/overview.md) a figyelési adatok (például a tevékenységek naplói, metrikák és diagnosztikai adatok) teljes megjelenítését állítja elő, így az ügyfelek teljes rendszerállapot-képet hozhatnak létre.  
[Azure monitor naplók](../azure-security-disk-encryption-overview.md) széles körű naplózást biztosítanak a rendszer és a felhasználók tevékenységéről, valamint a rendszer állapotáról. Az Azure-ban és a helyszíni környezetekben található erőforrások által generált adatokat gyűjti és elemzi.
- **Tevékenységek naplói**: A [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-események rendszernaplóit és az Azure Blob Storage, Tables és üzenetsor-naplókat tartalmazzák.
- **Tűzfal naplófájljai**: A Application Gateway teljes körű diagnosztikai és hozzáférési naplókat biztosít. A WAF-kompatibilis Application Gateway erőforrásai számára elérhetők a tűzfalak.
- **Napló archiválása**: Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást, amely a megadott megőrzési időtartam 2 nap. Ezek a naplók Azure Monitor naplókat csatlakoznak a feldolgozáshoz, tároláshoz és irányítópult-jelentéskészítéshez.

Emellett az alábbi figyelési megoldások is részét képezik ennek az architektúrának:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A Azure Automation megoldás tárolja, futtatja és felügyeli a runbookok.
-   [Security and Audit](../../security-center/security-center-intro.md): A Security and Audit irányítópult magas szintű betekintést nyújt az erőforrások biztonsági állapotára azáltal, hogy mérőszámokat biztosít a biztonsági tartományokra, a jelentős problémákra, az észlelésekre, a fenyegetések felderítésére és az általános biztonsági lekérdezésekre.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
-   [Azure-tevékenység naplói](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

### <a name="identity-management"></a>Identitáskezelés
-   Az alkalmazáshoz való hitelesítés az Azure AD használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Emellett az adatbázis oszlopának titkosítása az Azure AD használatával hitelesíti az alkalmazást Azure SQL Database. További információ: a [bizalmas adatok védelme SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezet identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidensek, hogy megtegye a megfelelő lépéseket a megoldásához.
-   Az [Azure szerepköralapú Access Control (RBAC)](../../role-based-access-control/role-assignments-portal.md) lehetővé teszi a célzott hozzáférés-kezelést az Azure-hoz. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.

Ha többet szeretne megtudni a Azure SQL Database biztonsági funkcióinak használatáról, tekintse meg a [contoso Clinic bemutató alkalmazás](https://github.com/Microsoft/azure-sql-security-sample) mintáját.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="expressroute-and-vpn"></a>ExpressRoute és VPN
A [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a biztonságos VPN-alagutat úgy kell konfigurálni, hogy biztonságosan létesítsen kapcsolatot az adatelemzési hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. A ExpressRoute vagy a VPN megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

### <a name="azure-active-directory-setup"></a>Azure Active Directory telepítő
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. Egy meglévő Windows Server-Active Directory [négy kattintással](../../active-directory/hybrid/how-to-connect-install-express.md)integrálható a HRE. Az ügyfelek az üzembe helyezett Active Directory infrastruktúrát (tartományvezérlőket) egy meglévő HRE is összeállíthatják azáltal, hogy az üzembe helyezett Active Directory-infrastruktúrát egy HRE-erdő altartományának teszi.

### <a name="additional-services"></a>További szolgáltatások
#### <a name="iaas---vm-considerations"></a>IaaS – virtuális gépekkel kapcsolatos megfontolások
Ez a Pásti-megoldás nem tartalmaz Azure IaaS virtuális gépeket. Egy ügyfél létrehozhat egy Azure-beli virtuális gépet, amely sok ilyen Pásti-szolgáltatást futtat. Ebben az esetben az üzletmenet folytonosságát és Azure Monitor naplóit a következő esetekben lehet kihasználni:

##### <a name="business-continuity"></a>Az üzletmenet folytonossága
- **Magas rendelkezésre állás**: A kiszolgálói munkaterhelések egy [rendelkezésre állási](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) csoportba vannak csoportosítva, így biztosítva az Azure-beli virtuális gépek magas rendelkezésre állását. A tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető a 99,95%-os Azure SLA-val.

- **Recovery Services**tároló: Az [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) -tároló a biztonsági mentési és az Azure-Virtual Machines összes konfigurációját védi ebben az architektúrában. Recovery Services-tárolóval az ügyfelek a teljes virtuális gép visszaállítása nélkül állíthatják vissza a fájlokat és mappákat egy IaaS virtuális gépről, így a gyorsabb visszaállítási idő is megadható.

##### <a name="monitoring-solutions"></a>Monitorozási megoldások
-   [Ad Assessment](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Az antimalware-megoldás a kártevők, fenyegetések és védelem állapotáról nyújt jelentéseket.
-   [Update Management](../../automation/automation-update-management.md): Az Update Management megoldás lehetővé teszi az operációs rendszer biztonsági frissítéseinek az ügyfelek általi felügyeletét, beleértve az elérhető frissítések állapotát, valamint a szükséges frissítések telepítésének folyamatát.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Change Tracking](../../automation/change-tracking.md): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosítsák a környezet változásait.

##### <a name="security"></a>Biztonság
- **Kártevők elleni védelem**: A Virtual Machines rendszerhez készült [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) olyan valós idejű védelmi képességet biztosít, amely lehetővé teszi a vírusok, kémprogramok és más kártékony szoftverek azonosítását és eltávolítását, amelyekkel konfigurálható riasztások állíthatók be, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik Telepítsen vagy futtasson védett virtuális gépeken.
- **Javítási felügyelet**: Az ebben a hivatkozási architektúrában üzembe helyezett Windows-beli virtuális gépek alapértelmezés szerint úgy vannak konfigurálva, hogy Windows Update szolgáltatásból fogadják az automatikus frissítéseket. Ez a megoldás tartalmazza azt a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást is, amelyen keresztül a frissített központi telepítések létrehozhatók a virtuális gépek szükség szerinti javításához.

#### <a name="azure-commercial"></a>Azure kereskedelmi
Bár ez az adatelemzési architektúra nem az Azure-beli [kereskedelmi](https://azure.microsoft.com/overview/what-is-azure/) környezetbe való telepítéshez készült, hasonló célkitűzések érhetők el az ebben a hivatkozási architektúrában ismertetett szolgáltatásokkal, valamint az elérhető további szolgáltatásokkal is. csak az Azure-beli kereskedelmi környezetben. Vegye figyelembe, hogy az Azure-beli kereskedelmi szolgáltatás egy FedRAMP DÖF P-ATO-t tart fenn a mérsékelt hatás szintjén, amely lehetővé teszi a kormányzati szervek és partnerek számára, hogy mérsékelten bizalmas adatokat telepítsenek a felhőbe az Azure kereskedelmi környezetének kihasználásával.

Az Azure Commercial számos elemzési szolgáltatást kínál a nagy mennyiségű adattal kapcsolatos elemzések elvégzéséhez:
-   [Azure Machine learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio)a [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/)egy összetevője egy prediktív elemzési modellt fejleszt ki egy vagy több adatforrásból. A statisztikai függvények több iteráción keresztül használhatók egy olyan hatékony modell létrehozásához, amelyet az alkalmazások, például a Power BI képesek használni.
-   A [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) lehetővé teszi bármilyen méretű, típusú és betöltési sebességű adatrögzítést egyetlen helyen az operatív és a felderítő elemzéshez. A Azure Data Lake Store kompatibilis a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével, és szépen integrálható más Azure-szolgáltatásokkal.

## <a name="threat-model"></a>Veszélyforrások modellje

A viszonyítási architektúra adatáramlási diagramja (DFD) [letölthető](https://aka.ms/blueprintanalyticsthreatmodel) , vagy a következő címen érhető el:

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure Security and Compliance Blueprint – FedRAMP nagy ügyfél-felelősségi mátrix](https://aka.ms/blueprinthighcrm) felsorolja a FedRAMP magas alapkonfigurációja által igényelt összes biztonsági ellenőrzést. Hasonlóképpen, a [Azure Security and Compliance Blueprint – FedRAMP mérsékelt ügyfél-felelősségi mátrix](https://aka.ms/blueprintanalyticscimmod) felsorolja az FedRAMP mérsékelt alapkonfigurációhoz szükséges összes biztonsági ellenőrzést. Mindkét dokumentum részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő között megosztva van-e.

A [Azure Security and Compliance Blueprint-FedRAMP High Control implementációs mátrix](https://aka.ms/blueprintanalyticscimhigh) és a [Azure Security and Compliance Blueprint-FedRAMP mérsékelt vezérlés megvalósítási mátrixa](https://aka.ms/blueprintanalyticscimmod) olyan információkat biztosít, amelyekkel a az egyes FedRAMP elemzési architektúrája, beleértve a részletes leírását, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
