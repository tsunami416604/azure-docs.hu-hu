---
title: Azure Security and Compliance Blueprint – adattárház a NIST SP 800-171-hez
description: Azure Security and Compliance Blueprint – adattárház a NIST SP 800-171-hez
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 5a308dfd5467aecb8b0ff6c661de1cb8b1740590
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780735"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure Security and Compliance Blueprint – adattárház a NIST SP 800-171-hez

## <a name="overview"></a>Áttekintés
A 800-171-es [speciális kiadvány](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) a nem szövetségi információs rendszerekben és szervezeteknél található ellenőrzött, nem minősített információk (CUI-) védelmére vonatkozó irányelveket biztosít. A NIST SP 800-171 14 olyan biztonsági követelményt hoz létre, amely a CUI titkosságának védelmét védi.

Ez a Azure Security and Compliance Blueprint útmutatást nyújt az ügyfeleknek az adattárház-architektúra üzembe helyezéséhez az Azure-ban, amely a NIST SP 800-171 vezérlők egy részhalmazát valósítja meg. Ez a megoldás azt mutatja be, hogy az ügyfelek milyen módon tudják kielégíteni az egyes biztonsági és megfelelőségi követelményeket. Emellett alapként szolgál az ügyfelek számára saját adattárház-megoldások létrehozásához és konfigurálásához az Azure-ban.

Ez a hivatkozási architektúra, a társított megvalósítási útmutató és a veszélyforrás-modell arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez alkalmazkodva alapot biztosítson. Nem használhatók éles környezetben. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért. A követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Ez a megoldás egy olyan hivatkozási architektúrát biztosít, amely nagy teljesítményű és biztonságos felhőalapú adattárházat valósít meg. Ebben az architektúrában két különálló adatréteg található. Az egyik réteg az a hely, ahol az adatimportálás, tárolás és előkészítés fürtözött SQL-környezetben történik. Az SQL-adattárház egy másik szintje. Ezzel a szinttel az adatok betöltését egy kinyerési, átalakítási-betöltési (ETL) eszköz (például a [Base](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-lekérdezések) használatával tölti be a rendszer a feldolgozáshoz. Az adatSQL Data Warehouse tárolása után az Analytics nagy léptékben futhat.

Az Azure számos jelentéskészítési és elemzési szolgáltatást kínál az ügyfelek számára. Ez a megoldás az SQL-adattárházból származó jelentések gyors létrehozásához SQL Server Reporting Services tartalmaz. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Ajánlott eljárásként javasoljuk, hogy használjon megbízható hitelesítésszolgáltatót a fokozott biztonság érdekében.

A Azure SQL Data Warehouse az oszlopokat az oszlopos tárolással rendelkező, rokon táblákban tárolja. Ez a formátum jelentősen csökkenti az adattárolási költségeket, miközben javítja a lekérdezési teljesítményt. A használati követelményektől függően SQL Data Warehouse számítási erőforrások akár teljes mértékben fel-vagy leállíthatók, akár teljesen kikapcsolhatják, ha nincs aktív folyamat számítási erőforrásokra.

A SQL Server Load Balancer az SQL-forgalmat felügyeli, így biztosítva a nagy teljesítményt. Ebben a hivatkozási architektúrában az összes virtuális gép (VM) egy rendelkezésre állási csoportba helyezi a SQL Server példányokat egy always on rendelkezésre állási csoportban. Ez a konfiguráció magas rendelkezésre állást és vész-helyreállítási képességeket biztosít.

Ez az adattárház-hivatkozási architektúra az architektúrán belüli erőforrások kezelésére szolgáló Active Directory szintet is tartalmaz. A Active Directory alhálózat lehetővé teszi a könnyű áttelepítést egy nagyobb Active Directory erdő-struktúra alatt. Így a környezet folyamatosan működhet, még akkor is, ha a nagyobb erdőhöz való hozzáférés nem érhető el. Minden virtuális gép tartományhoz van csatlakoztatva a Active Directory szintjéhez. Active Directory Csoportházirendek használatával kényszerítik ki a biztonsági és megfelelőségi konfigurációkat az operációs rendszer szintjén.

A megoldás Azure Storage-fiókokat használ, amelyekkel az ügyfelek a Storage Service Encryption használatával kezelhetik az inaktív adatok titkosságát. Az Azure a rugalmasság érdekében három másolatot tárol az ügyfél kiválasztott adatközpontjában. A Geo-redundáns tárolás biztosítja, hogy a rendszer replikálja az adatmennyiséget egy több száz kilométerre lévő másodlagos adatközpontba, és az adatközpontban három másolatként tárolja őket. Ez a megoldás megakadályozza, hogy az ügyfél elsődleges adatközpontjában ne legyen adatvesztés.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. A Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozható a telepített erőforrásokhoz való hozzáférés. Ezek az erőforrások a Azure Key Vaultban lévő ügyfelek kulcsait tartalmazzák. A rendszer állapotát Azure Security Center és Azure Monitor figyeli. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítésére is konfigurálhatják. A rendszer állapota egyetlen irányítópulton jelenik meg, amely könnyen használható.

A virtuális gép felügyeleti megerősített gazdagépként szolgál. Biztonságos kapcsolatot biztosít a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez. Az adattárolási terület a felügyeleti megerősített gazdagépen keresztül töltődik be. *Javasoljuk, hogy konfigurálja a VPN-t vagy az Azure ExpressRoute-kapcsolatát a felügyeleti és adatimportálási szolgáltatásba a hivatkozási architektúra alhálózatában.*

![Adattárház a NIST SP 800-171 hivatkozási architektúrájának diagramja](images/nist171-dw-architecture.png "Adattárház a NIST SP 800-171 hivatkozási architektúrájának diagramja")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. További információ: [telepítési architektúra](#deployment-architecture) szakasz.

- Rendelkezésre állási csoportok
    - Active Directory tartományvezérlők
    - Fürtcsomópontok és tanúsító SQL Server
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor (naplók)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) megerősített állomás
    - (2) Active Directory tartományvezérlő
    - (2) SQL Server fürtcsomópont
    - (1) SQL Server tanúsító
- Azure Virtual Network
    - (1)/16 hálózat
    - (4)/24 hálózat
    - (4) hálózati biztonsági csoportok
- Helyreállítási tár
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Azure SQL Data Warehouse**: A [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) egy vállalati adattárház, amely nagy mértékben párhuzamos feldolgozást használ a petabájt összetett lekérdezések gyors futtatásához. A felhasználók egyszerű, Base T-SQL-lekérdezéseket használhatnak big data importálására az SQL-adattárházba, és a nagymértékben párhuzamos feldolgozás hatékonyságát a nagy teljesítményű elemzések futtatására is használhatja.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) lehetővé teszi a jelentések gyors létrehozását táblázatok, diagramok, térképek, mérőműszerek, mátrixok és más SQL Data Warehouseekhez.

**Azure Data Catalog**: A [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) megkönnyíti az adatforrások felderítését és értelmezését az azokat kezelő felhasználók számára. Az általános adatforrások regisztrálása, címkézése és adatkeresése is lehetséges. Az adatok a meglévő helyükön maradnak, de a metaadatok másolata Data Catalogba kerül. A rendszer az adatforrásra mutató hivatkozást tartalmaz. A metaadatok indexelve vannak, hogy minden adatforrás könnyen felderíthető legyen a keresésen keresztül. Az indexelés azt is lehetővé teszi, hogy a felhasználók megértsék a felderített felhasználókat.

**Megerősített gazdagép**: A megerősített gazdagép az a belépési pont, amelyet a felhasználók a környezetbe helyezett erőforrások elérésére használhatnak. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat biztonságos listaként engedélyezi. A távoli asztal forgalmának engedélyezéséhez meg kell határozni a forgalom forrását a hálózati biztonsági csoportban.

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Key Vault használatával.
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuálisgép-erőforrások felhasználását, ha nincs használatban.
-   A [Windows Defender hitelesítő adatainak](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) védelme engedélyezve van, így a hitelesítő adatok és egyéb Titkok a futó operációs rendszertől elkülönített védett környezetekben futnak.

### <a name="virtual-network"></a>Virtuális hálózat
Ez a hivatkozási architektúra a 10.0.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) A (NSG) olyan hozzáférés-vezérlési listát tartalmaz, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A NSG használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy adott virtuálisgép-szinten. A következő NSG léteznek:
  - Az adatcsomag NSG (SQL Server-fürtök, SQL Server tanúsító és SQL Load Balancer)
  - Egy NSG a felügyeleti megerősített gazdagéphez
  - Active Directory NSG
  - SQL Server Reporting Services NSG

A NSG minden egyes portja és protokollja nyitva van, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk is engedélyezve vannak az egyes NSG:
  - A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és egy Storage-fiókban tárolódnak.
  - A Azure Monitor naplók a [NSG diagnosztikaához](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)kapcsolódnak.

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó NSG.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra több mértéken keresztül védi a nyugalmi állapotban lévő adatok védelmét. Ezek a mértékek közé tartozik a titkosítás és az adatbázis naplózása.

**Azure Storage**: A titkosított adatokra vonatkozó követelmények teljesítése érdekében az [](https://azure.microsoft.com/services/storage/) összes tárterület [Storage Service Encryptiont](https://docs.microsoft.com/azure/storage/storage-service-encryption)használ. Ez a funkció segíti a szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásában lévő adatvédelmet és védelmét.

**Azure Disk Encryption**: A [lemezes titkosítás](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) a Windows BitLocker szolgáltatásával biztosítja a kötetek titkosítását az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: A SQL Database példány a következő adatbázis-biztonsági mértékeket használja:
-   [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
-   [SQL Database naplózás](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A SQL Database [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatára van konfigurálva. A szolgáltatás valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok között, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem vonatkoznak a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) -veszélyforrások észlelése lehetővé teszi az észlelést és a válaszadást a lehetséges fenyegetésekkel szemben. Biztonsági riasztásokat biztosít a gyanús adatbázis-tevékenységekről, a lehetséges sebezhetőségekről, az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról.
-   A [titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha nem egyszerű szövegként jelenjenek meg az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak a kulcsokhoz hozzáféréssel rendelkező ügyfélalkalmazások vagy kiszolgálóalkalmazások férhetnek hozzá az egyszerű szöveges adatértékekhez.
- Az adatalanyok feldolgozásának megszüntetéséhez [További tulajdonságok](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) is használhatók. A felhasználók egyéni tulajdonságokat adhatnak hozzá az adatbázis-objektumokhoz. Emellett a "megszűnt" értékkel is címkézik az adataikat, hogy támogassák az alkalmazás logikáját a kapcsolódó pénzügyi adatmennyiségek feldolgozásának megelőzése érdekében.
- A [sorok szintjének biztonsága](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználók számára, hogy szabályzatokat határozzanak meg az adathozzáférés korlátozásához a feldolgozás megszüntetéséhez.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok megjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A szolgáltatás automatikusan képes felderíteni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. A dinamikus adatmaszkolás segít csökkenteni a hozzáférést, hogy a bizalmas adatok jogosulatlan hozzáférés nélkül ne lépjenek ki az adatbázisból. *Az ügyfelek felelősek a beállítások módosításához az adatbázis-sémájuk betartásához.*

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:
-   Az [Azure ad](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. A megoldás minden felhasználója az Azure AD-ben jön létre, és tartalmazza az SQL Database-hez hozzáférő felhasználókat.
-   Az alkalmazáshoz való hitelesítés az Azure AD használatával történik. További információ: [alkalmazások integrálása az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Az adatbázis oszlopának titkosítása az Azure AD-t is használja az alkalmazás SQL Database való hitelesítéséhez. További információ: a [bizalmas adatok védelme SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Az [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) segítségével a rendszergazdák részletes hozzáférési engedélyeket adhatnak meg. Ezzel csak azt a hozzáférést biztosíthatják, ameddig a felhasználóknak el kell végezniük a munkájukat. Ahelyett, hogy minden felhasználó számára korlátlan hozzáférést adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az erőforrásokhoz és adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- A [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) az ügyfelek használhatják a bizonyos információkhoz (például adatokhoz) hozzáférő felhasználók számának minimalizálására. A rendszergazdák Azure AD Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági réseket. Automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletek észlelésére. Emellett a gyanús incidenseket is megvizsgálja, hogy megfelelő lépéseket tegyenek a megoldásához.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Key Vault segít megőrizni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. Az alábbi Key Vault-képességek segítenek az ügyfeleknek az adatvédelemben:
- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa egy hardveres biztonsági modul által védett 2048 bites RSA-kulcs.
- Minden felhasználó és identitás minimálisan szükséges engedélyeket kap a RBAC használatával.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Javítási felügyelet**: A jelen viszonyítási architektúra részeként telepített Windows rendszerű virtuális gépek alapértelmezés szerint a Windows Update szolgáltatásból származó automatikus frissítések fogadására vannak konfigurálva. Ez a megoldás tartalmazza azt a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást is, amelyen keresztül a frissített központi telepítések létrehozhatók a virtuális gépek szükség szerinti javításához.

**Kártevők elleni védelem**: A virtuális gépekhez készült [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) olyan valós idejű védelmi képességet biztosít, amely lehetővé teszi a vírusok, kémprogramok és más kártevő szoftverek azonosítását és eltávolítását. Az ügyfelek olyan riasztásokat állíthatnak be, amelyek az ismert kártékony vagy nemkívánatos szoftverek által a védett virtuális gépeken történő telepítésére vagy futtatására tett kísérleteket eredményeznek.

**Azure Security Center**: A [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetét célba vevő lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Security Center [előre meghatározott biztonsági riasztásokat](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) tartalmaz, amelyek akkor aktiválódnak, ha fenyegetés vagy gyanús tevékenység zajlik. Az ügyfelek [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) segítségével meghatározhatnak új biztonsági riasztásokat a környezetből már összegyűjtött adatok alapján.

A Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít. Security Center megkönnyíti az ügyfelek számára a potenciális biztonsági problémák felderítését és megoldását. Minden észlelt fenyegetéshez létrejön egy [fenyegetési intelligencia jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Az incidensek válaszait használó csapatok a jelentéseket a fenyegetések kivizsgálására és szervizelésére használhatják.

Ez a hivatkozási architektúra a Security Center [sebezhetőségi felmérési](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) képességét is használja. A konfigurálását követően a partner ügynök (például a Qualys) biztonsági réseket küld a partner felügyeleti platformjának. Ezután a partner felügyeleti platformja a biztonsági résekre vonatkozó és állapotmonitorozási adatokat küld vissza a Security Centernek. Az ügyfelek ezeket az információkat a sebezhető virtuális gépek gyors azonosítására használhatják.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állás**: A kiszolgálói munkaterhelések egy [rendelkezésre állási](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) csoportba vannak csoportosítva, így biztosítva a virtuális gépek magas rendelkezésre állását az Azure-ban. Legalább egy virtuális gép elérhető egy tervezett vagy nem tervezett karbantartási esemény során, amely megfelel az 99,95%-os Azure SLA-nak.

**Recovery Services**tároló: Az [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) -tároló a biztonsági mentési és a virtuális gépek összes konfigurációját védi ebben az architektúrában. Recovery Services-tárolóval az ügyfelek a teljes virtuális gép visszaállítása nélkül állíthatják vissza a fájlokat és mappákat egy IaaS virtuális gépről. Ez a folyamat felgyorsítja a visszaállítási időt.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységek naplói**: A [tevékenységek naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, a tárolási naplókat, a Key Vault a naplókat, valamint az Azure Application Gateway hozzáférés-és tűzfal-naplókat tartalmazzák. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A felhasználók a megőrzési időtartamot akár 730 napig is konfigurálhatják, hogy megfeleljenek a rájuk vonatkozó követelményeknek.

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adattípusokat Log Analytics munkaterületeken belül. Így az összes adatforrás együtt is elemezhető, az eredeti forrásától függetlenül. A Security Center Azure Monitor-naplókkal integrálódik. Az ügyfelek használhatnak Kusto-lekérdezéseket a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) szerepelnek:
-   [Active Directory értékelés](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát. A szolgáltatás a központilag telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékelés](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát. Az ügyfelek számára az üzembe helyezett kiszolgálói infrastruktúrára jellemző ajánlások rangsorolt listáját kínálja.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A Agent Health-megoldás azt jelenti, hogy hány ügynök van üzembe helyezve és a földrajzi eloszlása. Azt is jelenti, hogy hány ügynök nem válaszol, és az operatív adatküldő ügynökök száma.
-   [Activity log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: Az [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók SQL Databaseból való gyűjtésében. Az Automation [change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás segítségével könnyedén azonosíthatja a környezet változásait.

**Azure monitor**: A [figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek felismerésében. A szervezetek felhasználhatják a naplózásra, a riasztások létrehozására és az adatok archiválására. Emellett az Azure-erőforrásokban is nyomon követhetik az API-hívásokat.

## <a name="threat-model"></a>Veszélyforrások modellje

A viszonyítási architektúra adatáramlási diagramja [letölthető](https://aka.ms/nist171-dw-tm) , vagy itt található. Ez a modell segítheti az ügyfeleket abban, hogy megértsék a rendszerinfrastruktúra lehetséges kockázatait, amikor módosításokat végeznek.

![Adattárház a NIST SP 800-171 Threat modelhez](images/nist171-dw-threat-model.png "Adattárház a NIST SP 800-171 Threat modelhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure Security and Compliance Blueprint – NIST sp 800-171 Customer felelősségi mátrix](https://aka.ms/nist171-crm) FELSOROLJA a NIST SP 800-171 által igényelt összes biztonsági ellenőrzést. Ez a mátrix részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

Az [Azure Security and Compliance Blueprint-NIST sp 800-171 adattárház-vezérlő megvalósítási mátrixa](https://aka.ms/nist171-dw-cim) információkat nyújt arról, hogy az adatraktár-architektúra milyen NIST SP 800-171-vezérlőkre vonatkozik. Részletes leírást tartalmaz arról, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
A biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) úgy kell konfigurálni, hogy biztonságosan létesítsen kapcsolatot az adattárház-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy virtuális magánhálózati kapcsolatot egy helyszíni hálózat és egy Azure-beli virtuális hálózat között. Ez a kapcsolat az interneten keresztül zajlik. Az ügyfelek ezt a kapcsolatot az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkkal használhatják. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft még egy biztonságosabb kapcsolódási lehetőséget kínál. A ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. A ExpressRoute-kapcsolatok közvetlenül az ügyfél távközlési szolgáltatójának csatlakoznak. Ennek eredményeképpen az adat nem utazik az interneten keresztül, és nem teszi elérhetővé. Ezek a kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot nyújtanak, mint a szokásos kapcsolatok.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)ra.

### <a name="extract-transform-load-process"></a>Kinyerés – átalakítás – betöltési folyamat
[](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) A program az adatok SQL Data Warehouseba való betöltését külön ETL vagy importálási eszköz nélkül tudja betölteni. A Base lehetővé teszi, hogy a T-SQL-lekérdezéseken keresztül hozzáférjen az adatokhoz. A Microsoft Business Intelligence and Analysis stack és a külső gyártótól származó eszközök, amelyek kompatibilisek a SQL Serverekkel, a Base használatával használhatók.

### <a name="azure-ad-setup"></a>Azure AD-telepítés
Az [Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. A helyszíni Active Directory [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)integrálható az Azure ad-vel. Az ügyfelek az üzembe helyezett Active Directory infrastruktúrát (tartományvezérlőket) az Azure AD-be is tudják kötni. Ehhez a központilag telepített Active Directory infrastruktúra egy Azure AD-erdő altartománya legyen.

### <a name="optional-services"></a>Választható szolgáltatások
Az Azure számos szolgáltatást kínál a formázott és formázatlan adattárolási és-előkészítési támogatáshoz. Az ügyfélre vonatkozó követelmények alapján a következő szolgáltatások adhatók hozzá ehhez a hivatkozási architektúrához:
-   A [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőalapú szolgáltatás, amely összetett hibrid ETL-, kinyerhető terhelés-átalakítási és adatintegrációs projektekhez készült. Data Factory rendelkezik olyan képességekkel, amelyek segítenek a nyomkövetésben és az adatkeresésben. A vizualizációs és figyelési eszközök azonosítják, hogy mikor érkeztek meg és honnan származnak. Az ügyfelek olyan adatvezérelt munkafolyamatokat hozhatnak létre és ütemezhetnek, amelyek folyamatok, különböző adattárakból származó adatok betöltésére alkalmasak. A folyamatokat a belső és külső forrásokból származó adatok betöltésére használhatják. Az ügyfelek ezután feldolgozhatják és átalakítják az adatokat a kimenetbe adattárakba, például a SQL Data Warehouseba.
- A [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) strukturálatlan adatmennyiséget vehetnek igénybe a méret, a típus és a betöltési sebesség egyetlen helyen történő rögzítése az operatív és a felderítő elemzéshez. Azure Data Lake rendelkezik olyan képességekkel, amelyek lehetővé teszik az adatfeldolgozást és-átalakítást. A Data Lake Store a Hadoop-ökoszisztémában található legtöbb nyílt forráskódú összetevővel kompatibilis. Emellett szépen integrálható más Azure-szolgáltatásokkal, például SQL Data Warehouseokkal.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
