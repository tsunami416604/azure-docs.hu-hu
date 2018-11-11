---
title: Azure biztonsági és megfelelőségi terv - GDPR Data warehouse-bA
description: Azure biztonsági és megfelelőségi terv - GDPR Data warehouse-bA
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: d1099b813e84cd4885b011dec205a1e631fc6599
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250713"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Azure biztonsági és megfelelőségi terv: GDPR Data warehouse-bA

## <a name="overview"></a>Áttekintés
Az általános adatvédelmi rendelet (GDPR) tartalmazza az információk gyűjtése, tárolása és személyes adatokat, beleértve a hogyan szervezetek azonosítása és a személyes adatok védelme, átláthatóság követelmények kezelésére, észlelése és jelentés segítségével számos követelmények személyes adatok illetéktelen behatolásokat, és train adatvédelmi személyzet és a többi alkalmazott. Az általános adatvédelmi rendelet biztosít az egyének személyes adataik nagyobb mértékben vezérelheti, és számos új kötelezettségeket szervezeteknek, amelyek gyűjtése, kezelni vagy személyes adatok elemzése ró. Az általános adatvédelmi rendelet új szabályokat áruk kínáló szervezetek számára ír elő, és személyek az Európai Unió (EU), vagy ha a szolgáltatás adatokat gyűjthet és elemezhet EU-polgárokkal kapcsolatos vannak kötve. Az általános adatvédelmi rendelet vonatkozik, függetlenül attól, ahol a szervezet megtalálható.

A Microsoft Azure tervezett az iparágvezető biztonsági és adatvédelmi módszerek kidolgozásában adataihoz a felhőben, beleértve a kategóriákat, az általános adatvédelmi rendelet által azonosított a személyes adatok védelme érdekében. A Microsoft [szerződési feltételek](https://aka.ms/Online-Services-Terms) Microsoft véglegesítenie kell a processzorok követelményeinek.

Az Azure biztonsági és megfelelőségi terv üzembe helyezése az Azure-ban, amely segítséget nyújt az általános adatvédelmi rendelet követelményeinek, a data warehouse architektúrája útmutatást nyújt. Ebben a megoldásban, amelyben ügyfeleink megfelel bizonyos biztonsági és megfelelőségi követelmények módszereket mutatja be, és az ügyfelek létrehozása és konfigurálása saját adattárház-megoldások az Azure-ban alapjaként szolgál. Ügyfelek Ez a referenciaarchitektúra használatához, és kövesse a Microsoft [négy lépésből álló folyamat](https://aka.ms/gdprebook) az adatvédelmi rendeletnek való megfelelőség útjuk:
1. Észlelés: Azonosítsa, mely személyes adatok állnak rendelkezésre, és helyét.
2. Kezelése: Hogyan személyes adatok szabályozása használt és elérhető.
3. Védelme: Biztonsági vezérlők megelőzését, észlelését és válaszadás a biztonsági rések és az adatok hoz létre.
4. Jelentés: Szükséges dokumentáció és kérelmek kezeléséhez, és illetéktelen behatolás értesítések.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál, és nem használható-éles környezetben van. Vegye figyelembe a következőket:
- Az architektúra egy alapkonfiguráció segítenek az ügyfeleknek a számítási feladatok üzembe helyezéséhez az Azure-bA a GDPR-kompatibilis módon biztosít.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
A megoldás egy nagy teljesítményű és biztonságos felhőalapú adattárház, amely referenciaarchitektúra biztosít. Ez az architektúra két külön szinten vannak: ahol importált adatokkal, az egyik tárolja, és egy fürtözött SQL-környezetben, és a egy másik készítve az Azure SQL Data warehouse, ha az adatok betöltése egy ETL-eszköz használatával (pl. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-lekérdezések) feldolgozás céljából. Miután az Azure SQL Data Warehouse tároljuk, analytics futtathat nagy méretű.

Az Azure kínál a különböző jelentéskészítési és elemzési szolgáltatások, az ügyfél számára. Ez a megoldás gyors létrehozása a jelentések az Azure SQL Data Warehouse-ból az SQL Server Reporting Services (SSRS) tartalmaz. SQL forgalmat titkosítja a rendszer SSL-lel azáltal, önaláírt tanúsítványokat. Ajánlott eljárásként az Azure a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.

Az Azure SQL Data Warehouse az adatokat oszlopos szerkezetű jelentősen csökkenti a tárolási költségeket a lekérdezési teljesítmény javítása közben formátumú relációs tábláival tárolódik.  Használati követelményeitől függően Azure SQL Data Warehouse számítási erőforrások méretezése felfelé vagy lefelé vagy kapcsolja ki teljesen esetén nem igénylő számítási erőforrások nincs aktív folyamatok.

SQL terheléselosztó kezeli az SQL-forgalmat, biztosítva a nagy teljesítményű. Ez a referenciaarchitektúra összes virtuális gépet egy rendelkezésre állási csoport magas rendelkezésre állású és vész-helyreállítási funkciók egy AlwaysOn rendelkezésre állási csoportban konfigurált SQL Server-példányok üzembe helyezhet.

A data warehouse referenciaarchitektúra egy Active Directory (AD) réteg az architektúra belüli erőforrások felügyeletéhez is tartalmaz. Az Active Directory-alhálózathoz lehetővé teszi, hogy könnyen bevezetési alatt egy nagyobb AD erdő struktúra, így folyamatos működése érdekében a környezetben, akkor is, ha a nagyobb erdőben való hozzáférés nem érhető el. Az összes virtuális gép tartományhoz csatlakoztatva az Active Directory-réteghez, és az Active Directory csoportházirend segítségével érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén.

A virtuális gépek felügyeleti bástyagazdagép, biztonságos kapcsolatot biztosít a rendszergazdák erőforrások eléréséhez az üzembe helyezett funkcionál. Az adatok betöltődtek, a felügyeleti megerősített gazdagépen keresztül, az átmeneti területre. **Azure azt javasolja, hogy a VPN- vagy Azure ExpressRoute kapcsolat a felügyeleti és az adatok importálása a referencia architektúra alhálózatban.**

![Adatraktár-GDPR architekturális diagramja](images/gdpr-datawarehouse-architecture.png?raw=true "Data Warehouse az általános adatvédelmi rendelet architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletei a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

-   Azure-alapú virtuális gépek
    - (1) Bástyagazdagép
    -   (2) az active Directory-tartományvezérlő
    -   (2) az SQL Server-fürt csomópontjának
    -   (1) az SQL Server tanúsító
-   Rendelkezésre állási csoportok
    - Az Active Directory-tartományvezérlők
    - SQL-fürtcsomópontok és a tanúsító
-   Virtual Network
    - (4) alhálózatok
    -   (4) a hálózati biztonsági csoportok
- SQL Data Warehouse
- SQL Server Reporting Services
- Az Azure SQL-terheléselosztó
- Azure Active Directory
- Recovery Services-tároló
- Azure Key Vault
- Log Analytics
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) van egy vállalati adatok adattárház (EDW), amely ötvözi a nagymértékben párhuzamos feldolgozási (MPP), gyorsan összetett lekérdezések futtatása több petabájtnyi adat, így a felhasználók hatékony azonosíthatja a személyes adatokat. Felhasználók használhatják a egyszerű PolyBase T-SQL-lekérdezések big Data típusú adatok importálása az SQL Data Warehouse-ba, és a teljesítmény MPP nyújtotta lehetőségeket kiaknázva nagy teljesítményű elemzés futtatása felhasználását.

**Az SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) gyors létrehozása a jelentések biztosít a táblák, diagramokat, térképeket, mérőműszerek, mátrixokban és több Azure SQL Data Warehouse számára.

**A Data Catalog**: [a Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) teszi az adatforrásokat könnyen felfedezhetővé és értelmezhetővé teszi azon felhasználók számára, aki kezelheti az adatokat. Általános adatforrások regisztrálva, címkézett és -e keresni a személyes adatok. A data marad, a meglévő hely, de a metaadatok másolatát bekerül a Data Catalog szolgáltatást, az adatforrás helye mutató hivatkozásokkal együtt. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely lehetővé teszi a felhasználók hozzáférhessenek a környezetben telepített erőforrás. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni a hálózati biztonsági csoport (NSG).

Ez a megoldás létrehoz egy virtuális gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Log Analytics-bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak

### <a name="virtual-network"></a>Virtuális hálózat
Ez a referenciaarchitektúra egy privát virtuális hálózat a 10.0.0.0/16 címteret az határozza meg.

**Hálózati biztonsági csoportok**: [NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák (ACL), amelyek engedélyezik vagy megtagadják a forgalmat a Vneten belül. Az NSG-ket egy alhálózatot vagy az egyes Virtuálisgép-szintű forgalom védelmére használható. Az alábbi NSG-k léteznek:
  - Egy NSG-t (SQL Server-fürtöket, az SQL Server tanúsító és SQL terheléselosztó) az Adatréteg számára
  - A felügyeleti bástyagazdagép az NSG-t
  - Egy NSG-t az Active Directory
  - Egy NSG-t az SQL Server Reporting Services

Az NSG-k mindegyike rendelkezik az adott portokhoz és protokollokhoz meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - A log Analytics csatlakozik a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: minden egyes alhálózathoz társítva a megfelelő NSG-t.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra használatával több mértéket, beleértve a titkosítást és -adatbázis naplózási szolgáltatásával az inaktív adatok védi.

**Az Azure Storage**: rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít a szervezeti biztonsági kötelezettségeit, és az általános adatvédelmi rendelet által meghatározott megfelelőségi követelmények támogatásához a személyes adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az operációs rendszer és az adatlemezek kötettitkosítást biztosít a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az Azure SQL Database**: az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   SQL-adatbázis használatára van konfigurálva [transzparens adattitkosítási (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós időben titkosítja és fejti vissza az adatbázist, az azokhoz kapcsolódó biztonsági mentési, és a rest-tranzakciós naplófájlokat: adatok védelme érdekében. TDE biztosítja, hogy a személyes adatok tárolása nem lett bárki hozzáférhet.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták.
-   [Mindig titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas személyes adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- A [Extended Properties](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) szolgáltatás használható lemondásához a feldolgozást az érintettek, mivel lehetővé teszi a felhasználók egyéni tulajdonságokat adhat az adatbázis-objektumok és adatok megjelölheti "Kifutott" az, hogy az alkalmazáslogika támogatása a társított személyes adatok feldolgozását.
- [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználóknak a feldolgozási lemondásához adatokhoz való hozzáférés korlátozása a házirendek meghatározása érdekében.
- [Az SQL Database dinamikus adatok maszkolása (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) által a nem kiemelt jogosultságú felhasználók vagy alkalmazások, adatok maszkolása korlátozza a bizalmas személyes adatok. DDM automatikusan potenciálisan bizalmas adatok felderítéséhez, és javasolja a alkalmazni lehessen a megfelelő maszkokkal. Ez segít a feltételeknek megfelelő GDPR védelemre, valamint a hozzáférés csökkentése, hogy azt nem létezik az adatbázis jogosulatlan hozzáférést, a személyes adatok azonosítását. **Megjegyzés: A felhasználók be kell tartaniuk az adatbázisséma DDM beállításait kell.**

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure-beli személyes adatokhoz való hozzáférés kezeléséhez képességeket biztosítják:
-   [Az Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az aad-ben, beleértve a felhasználók az SQL-adatbázis elérésére.
-   Hitelesítés az alkalmazás az AAD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis oszloptitkosítás AAD használ az alkalmazás az Azure SQL Database hitelesítéséhez. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a felhasználóknak frissíteniük kell a munkája elvégzéséhez csak olyan mértékű hozzáférést biztosítson a minden részletre kiterjedő hozzáférési engedélyek megadása. Helyett az Azure-erőforrások minden felhasználó ugyanolyan korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos műveleteket, a személyes adatok eléréséhez. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, akik hozzáférhetnek a bizonyos adatokat, például a személyes adatok.  AAD Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [AAD-Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket, és megvizsgálja a gyanús incidensek, a problémák megoldásához a megfelelő műveletet.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az ügyfelek személyes adatainak és az ilyen adatokhoz való hozzáférés védelme:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulokban (HSM) által védett összes kulcsok a Key Vaultban. A kulcs típusa egy HSM által védett 2048-bites RSA-kulcsot.
- Minden felhasználó és identitások megkapják az RBAC használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.

**Javítások kezelése**: Ez a referenciaarchitektúra részeként üzembe helyezett Windows virtuális gépek úgy vannak konfigurálva, az automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen frissített telepítések hozható létre a javítás virtuális gépekhez szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára biztosítja a valós idejű védelem funkció, amely alapján azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható riasztásokkal Ha az ismert kártevő vagy nemkívánatos szoftverek megpróbálják telepíteni vagy futtatni védett virtuális gépeken.

**Biztonsági riasztások**: [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) forgalom figyelésére, naplók gyűjtése és elemzése a fenyegetések adatforrások használatát teszi lehetővé. Ezenkívül az Azure Security Center fér hozzá az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét, és a személyes adatok védelme érdekében a meglévő konfigurációt. Az Azure Security Center tartalmaz egy [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) a rendszer minden egyes észlelt fenyegetés, amelyek segítik az incidensmegoldás csapatokat a vizsgálatban és elhárításban.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állású**: Server számítási feladatainak szerint vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure-beli virtuális gépek magas rendelkezésre állás biztosítása érdekében. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális Gépet, így gyorsabb helyreállítást visszaállítása nélkül.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat
[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) biztosít széles körű naplózását, a rendszer és a felhasználói tevékenységek, valamint a helyrendszer állapotát. A [Log Analytics](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetekben.
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók a Windows rendszer-eseménynaplói és az Azure Blob storage, táblák és üzenetsor-naplók tartalmazzák.
- **Bejelentkezés archiváló**: minden diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek. Ezek a naplók csatlakozhat Azure Log Analytics feldolgozási, tárolására és-irányítópult jelentéseit.

Ezenkívül a következő Log Analytics-megoldások jelennek meg, ez az architektúra részeként:
-   [Az AD Assessmenthez](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapot-ellenőrzés megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
-   [Kártevőirtók felmérése](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldás kártevő szoftverek, a fenyegetések és a védelem állapota kapcsolatos jelentések.
-   [Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): az Azure Automation megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és auditálás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonsági és auditálási irányítópultja az erőforrások biztonsági állapotát egy magas szintű betekintést nyújt a metrikák azáltal, hogy a biztonsági tartományok, a jelentős problémák, a észlelések, a fenyegetésekkel kapcsolatos Tudásbázis és a gyakori biztonsági lekérdezések.
-   [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): az Update Management megoldás lehetővé teszi, hogy az ügyfél felügyeleti operációs rendszer biztonsági frissítéseket, beleértve egy elérhető frissítések állapotát és a szükséges frissítések telepítése.
-   [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Azure Tevékenységnaplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti az Activity Log Analytics megoldás.
-   [A változáskövetés](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A Change Tracking megoldás lehetővé teszi az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/gdprDWdfd) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![Adatraktár-GDPR fenyegetések modellezése](images/gdpr-datawarehouse-threat-model.png?raw=true "Data Warehouse az általános adatvédelmi rendelet fenyegetések modellezése")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
A [Azure biztonsági és megfelelőségi terv – a GDPR vevő felelőssége mátrix](https://aka.ms/gdprCRM) vezérlő és a processzor felelősségi körébe tartozik az összes általános adatvédelmi rendelet cikk tartalmazza. Vegye figyelembe, hogy az Azure-szolgáltatásokhoz, egy ügyfél általában a vezérlő és a Microsoft úgy működik, mint a processzor.

A [Azure biztonsági és megfelelőségi terv - GDPR Data Warehouse megvalósítási mátrix](https://aka.ms/gdprDW) melyik általános adatvédelmi rendelet a cikkek a data warehouse architektúrája, beleértve a részletes leírását, hogy a tárgyalt információkkal megvalósítás az egyes kezelt cikk kielégíti.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) biztonságos kapcsolat a data warehouse referenciaarchitektúra részeként üzembe helyezett erőforrások kell konfigurálni. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-Site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél távközlési szolgáltató közvetlen kapcsolatot, az adatok nem az interneten keresztül továbbítani tartalmaz, és ezért nem érhető el.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Kinyerési, átalakítási-betöltési (ETL) folyamat
[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) adatok betöltése az Azure SQL Data Warehouse-bA egy külön ETL nélkül vagy eszköz importálása. A PolyBase lehetővé teszi, hogy a T-SQL-lekérdezések adatokhoz való hozzáférését. A Microsoft üzleti intelligencia és elemzés verem, valamint kompatibilis SQL Server, a harmadik felektől származó eszközök a polybase-zel használható.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése és kiépítése a környezet implementálására csoporthoz való hozzáférést. Az AAD-ben meglévő Windows Server Active Directory integrálhatók [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Ügyfelek egy meglévő AAD-be való elősegítsék a telepített Active Directory-infrastruktúrát (tartományvezérlők) is, azáltal, hogy a telepített Active Directory-infrastruktúra egy AAD-erdőhöz tartozó altartományban.

### <a name="optional-services"></a>Választható szolgáltatások
Az Azure sokféle szolgáltatást nyújt a tárolási és formátumú és formázatlan adatok átmeneti kínál. Ez a referenciaarchitektúra ügyfél követelményeitől függően is hozzáadhatók a következő szolgáltatásokat:
-   [Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőszolgáltatás, amely a hibrid kinyerési, átalakítási-betöltési (ETL), a kivonat-terhelés-átalakítási (ELT), és adatintegrációs projektek. Az Azure Data Factory nyomkövetési és keresse meg a személyes adatok képességekkel rendelkezik, többek között a vizualizációt, és monitorozási eszközökkel, amikor érkeznek be az adatok, és ahol származik. Azure Data Factoryt használó ügyfelek létrehozhatók és ütemezhetők a nevű folyamatok különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok. Ezek a folyamatok lehetővé teszi ügyfeleink számára egyaránt belső és külső forrásból származó adatokat. Ügyfelek, majd fel, és a kimeneti adatok átalakítása adattárakban, mint az Azure SQL Data warehouse-bA.
- Ügyfelek úgy végezheti el a strukturálatlan adatok [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), amely lehetővé teszi, hogy a rögzítést, típusú és méretű, típusú, és a betöltési sebesség egyetlen helyen történő műveleti és felderítési elemzés céljából.  Azure Data Lake esetében képességeket, amelyek lehetővé teszik a kivonása és adatok átalakítását. Az Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis, és jól integrálható más Azure-szolgáltatások például az Azure SQL Data warehouse-bA.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
