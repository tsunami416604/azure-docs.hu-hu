---
title: Azure biztonsági és megfelelőségi tervezetének - GDPR tartozó adatraktár
description: Azure biztonsági és megfelelőségi tervezetének - GDPR tartozó adatraktár
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Azure biztonsági és megfelelőségi tervezetének: GDPR tartozó adatraktár

## <a name="overview"></a>Áttekintés (klasszikus)
Az általános adatok védelmi szabályozás (GDPR) információk gyűjtésére, tárolására és személyes információt, beleértve a hogyan szervezetek azonosítása és személyes adatok védelmét, átláthatóság követelmények teljesítése, észlelése és jelentés segítségével számos követelményeket tartalmaz személyes adatok behatolás és vonat adatvédelmi csoporthoz és a többi alkalmazott. A GDPR egyéni felhasználók számára a személyes adataik nagyobb ellenőrzést ad, és számos új kötelezettségeket a szervezeteknek, amelyek gyűjtése, kezelése vagy személyes adatok elemzése ró. A GDPR ró a szervezetek, termékek kínáló új szabályokat, és az Európai Unió, illetve hogy vállalatoknál szolgáltatások összegyűjti és elemzi az adatok EU lakosai kötve. A GDPR helyétől függetlenül kikényszeríti a szervezetek található vonatkozik.

A Microsoft Azure úgy iparágvezető biztonsági intézkedéseket, és az adatvédelmi szabályzatáról a felhőben, beleértve a személyes adatok a GDPR által azonosított kategóriákat adatok védelme. A Microsoft [szerződési feltételek](http://aka.ms/Online-Services-Terms) Microsoft véglegesítése processzorok követelményeinek.

Az Azure biztonsági és megfelelőségi tervezetének központi telepítéséhez, amely segítséget nyújt a GDPR követelményeinek a Azure-ban a data warehouse architektúrája útmutatást. Ez a megoldás azt mutatja be, amelyben az ügyfelek bizonyos biztonsági és megfelelőségi követelményeknek megfelel módon, és build és a saját data warehouse-megoldások konfigurálása az Azure-ügyfelek számára alapjaként szolgál. Az ügyfelek használják a referencia-architektúrában, és kövesse a Microsoft [négy lépésből álló folyamat](https://aka.ms/gdprebook) a GDPR megfelelőségi útjuk:
1. Észlelés: Azonosítása, mely személyes adat és helyét.
2. Kezelése: Szabályozására, hogy személyes adatok használt és elérhető.
3. Védelem: Létesíteni megakadályozása, észlelésében és kezelésében biztonsági réseket és adatszivárgáshoz biztonsági vezérlők.
4. Jelentés: Szükséges dokumentáció és kérelmek kezeléséhez, és értesítések megsértése.

A referencia-architektúrában, a kapcsolódó üzembe helyezési útmutatóban és a fenyegetések modellezése célja, hogy az ügyfelek számára a megadott követelményeknek való igazításának lehetősége alapjaként szolgálnak, és nem használható mint – egy termelési környezetben. Vegye figyelembe a következőket:
- Az architektúra munkaterhelések telepítése az Azure-bA GDPR megfelelő módon ügyfelek alapterv.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelése használatával készített ebbe az architektúrába követelmények változhatnak megoldások a részletekről az egyes ügyfelek általi alapján.

## <a name="architecture-diagram-and-components"></a>Architektúrája és összetevői
A megoldás egy nagy teljesítményű és biztonságos felhőalapú adatraktára, amely egy referencia-architektúrában biztosít. Ebben az architektúrában van két külön adat szintet: egy adott adatok importálása, tárolt, és egy fürtözött SQL-környezetben, és egy másik készítve az Azure SQL Data warehouse Ha az adatok betöltése az ETL eszközzel (pl. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-lekérdezések) feldolgozásra. Amennyiben az adatokat az Azure SQL Data Warehouse tárolja, analytics a nagy léptékű futtathatja.

Az Azure számos különböző ügyfél-jelentéskészítés és elemzés szolgáltatáshoz kínál. Ez a megoldás az SQL Server Reporting Services (SSRS) gyors létrehozása az Azure SQL Data Warehouse jelentéseket tartalmazza. Az összes SQL-forgalom azáltal, hogy önaláírt tanúsítványokat SSL van titkosítva. Ajánlott eljárásként Azure azt javasolja, hogy egy megbízható hitelesítésszolgáltató használatát a biztonság növelése.

Az Azure SQL Data Warehouse adatainak oszlopos tárolást jelentősen csökkenti az adatok tárolási költségek a lekérdezési teljesítmény formátuma nem relációs táblákban tárolódik.  Használati követelményeitől függően Azure SQL Data Warehouse számítási erőforrásokat is méretezése felfelé vagy lefelé és le teljesen Ha nincs aktív folyamatok igénylő számítási erőforrásokat.

SQL terheléselosztó nagy teljesítményt biztosító SQL forgalmat kezeli. A referencia-architektúrában összes virtuális gépet üzembe helyezni a rendelkezésre állási készlet magas rendelkezésre állású és vész-helyreállítási lehetőségeket az AlwaysOn rendelkezésre állási csoportban konfigurált SQL Server-példánnyal.

A data warehouse referenciaarchitektúra egy Active Directory (AD) réteget architektúrájának erőforrások kezelését is. Az Active Directory-alhálózathoz lehetővé teszi, hogy könnyen elfogadása alatt egy nagyobb AD erdő struktúra, így a folyamatos működéshez a környezet még nem érhető el a nagyobb erdőben való hozzáférés esetén. Az összes virtuális gép tartományhoz az Active Directory-réteghez, és az Active Directory-csoportházirendek használatával kényszerítheti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén.

A virtuális gép lesz felügyeleti megerősített állomás, és biztonságos kapcsolatot biztosít a rendszergazdák központilag telepített hozzáférését az erőforrásokhoz. Az adatok betöltése az átmeneti területre, a felügyeleti megerősített gazdagépen keresztül. **Azure azt javasolja, hogy a referencia architektúra alhálózati történő felügyelete és az adatok importálása a VPN- vagy Azure ExpressRoute-kapcsolat beállítása.**

![Adatraktár-GDPR referencia architektúra diagramja](images/gdpr-datawarehouse-architecture.png?raw=true "adatraktár-GDPR referencia architektúra diagramja")

A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit szerepelnek a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

-   Azure-alapú virtuális gépek
    - (1) megerősített állomás
    -   (2) az active Directory-tartományvezérlő
    -   (2) az SQL Server-fürt csomópontjának
    -   (1) az SQL Server tanúsító
-   Rendelkezésre állási csoportok
    - Active Directory-tartományvezérlők
    - SQL fürtcsomópontok és a tanúsító
-   Virtual Network
    - (4) alhálózatok
    -   (4) hálózati biztonsági csoportok
- SQL Data Warehouse
- SQL Server Reporting Services szoftverben
- Az Azure SQL terheléselosztó
- Azure Active Directory
- Recovery Services-tároló
- Azure Key Vault
- Az Operations Management Suite (OMS)
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
Az alábbi szakasz részletesen üzembe helyezése és megvalósítás elemek.

**Az SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) van egy vállalati adatok adatraktár (EDW), amely kihasználja a nagymértékben párhuzamos feldolgozási (MPP) gyorsan összetett lekérdezések futtatása között adatmennyiségig, így a felhasználók hatékony azonosítani a személyes adatokat. Felhasználók egyszerű PolyBase-T-SQL-lekérdezések segítségével big Data típusú adatok importálása az SQL Data Warehouse, és felhasználja az MPP hatványa, nagy teljesítményű analytics futtatásához.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) biztosít a jelentések gyors létrehozása a táblák, diagramokat, térképeket, mérőműszer, oszlopcsoportosításokhoz és több Azure SQL Data Warehouse.

**A Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) könnyebben feltárhatóvá és értelmezhetővé teszi adatforrások a felhasználókat, akik az adatok kezeléséhez. Közös adatforrások regisztrálva, címkézett és a személyes adatok keres. A létező helyére, de a metaadatok másolatát adatok marad Data Catalog hozzáadódik az adatforrások helyére mutató hivatkozásokkal együtt. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

**Megerősített állomás**: A megerősített állomás a központi hely, amely lehetővé teszi a felhasználóknak ebben a környezetben telepített erőforrások elérését. A megerősített állomás csak átengedi a nyilvános IP-címekről távoli forgalmat biztonságos lista telepített erőforrások biztonságos kapcsolatot nyújt. Távoli asztal (RDP)-forgalmát engedélyezi, hogy a forgalom forrásának kell definiálni a hálózati biztonsági csoport (NSG).

Ez a megoldás létrehoz egy virtuális gépet a következő konfigurációk a tartományhoz csatlakoztatott megerősített gazdagépeként:
-   [Kártevőirtó-bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS-bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-bővítményt](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Azure Key Vault használatával
-   Egy [automatikus leállítási házirendet](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) használaton virtuálisgép-erőforrások fogyasztásának csökkentése érdekében
-   [A Windows Defender Credential őr](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és más titkos adatokat futtatni egy olyan védett környezetben, amely el van különítve a futó operációs rendszer

### <a name="virtual-network"></a>Virtuális hálózat
A referencia-architektúrában egy titkos virtuális hálózat rendelkező 10.0.0.0/16 a cím határozza meg.

**Hálózati biztonsági csoportok**: [NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmaz a hozzáférés-vezérlési listái (ACL), amely engedélyezi vagy megtagadja a forgalmat a Vneten belül. Az NSG-k segítségével biztonságos egy alhálózat vagy az egyes virtuális gép szintjén-forgalmat. A következő NSG-k érhetők el:
  - Egy NSG-t az Adatréteg (SQL Server-fürtök, SQL Server tanúsító és SQL terheléselosztó)
  - Egy NSG-t a felügyeleti megerősített állomás
  - Egy NSG-t az Active Directory
  - Egy NSG-t az SQL Server Reporting Services szoftverben

Az NSG-k, amelyek mindegyikének egyedi portokat és protokollokat nyissa meg, hogy a megoldás megfelelően és biztonságosan együttműködhet. Ezenkívül a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - OMS szolgáltatáshoz csatlakozik-e a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: az egyes alhálózatokon társítva a megfelelő NSG.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra védi az adatokat több mértéket, beleértve a titkosítást és az adatbázis naplózását történik.

**Az Azure Storage**: titkosított adatok rest követelmények teljesítéséhez összes [Azure Storage](https://azure.microsoft.com/services/storage/) használ [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít és megvédeni a személyes adatok szervezeti biztonsági kötelezettségeit, és a megfelelőségi követelményeket határozzák meg a GDPR támogatásához.

**Az Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows operációs rendszer és az adatlemezek kötettitkosítást biztosít a BitLocker-szolgáltatás. A megoldás integrálódik az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok kezeléséhez.

**Az Azure SQL Database**: az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD hitelesítési és engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) adatbázis-felhasználók és más Microsoft-szolgáltatásokban egyetlen központi helyen identitás kezelését teszi lehetővé.
-   [SQL-adatbázis naplózásának](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be Azure storage-fiók.
-   SQL-adatbázis használatára van konfigurálva [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok, és a rest-tranzakciós naplófájlok a következő információk védelme érdekében. TDE biztosítja, hogy a személyes adatok tárolása nem lett bárki hozzáférhet.
-   [Tűzfal-szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tagadni a hozzáférést minden adatbázis-kiszolgálók csak a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [A Fenyegetésészlelés SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy felderítésére és a lehetséges veszélyforrásokra választ, adja meg a biztonsági riasztások adatbázis gyanús tevékenységek, a potenciális biztonsági réseket, az SQL injektálási támadások és a rendellenes adatbázis-hozzáférési előforduló minták.
-   [Mindig a titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy személyes adatok soha nem jelenik meg az adatbázis rendszerében szövegként. Miután engedélyezte az adattitkosítást, csak az ügyfélalkalmazások vagy a kulcsoknak access app kiszolgálók hozzáférhet egyszerű szöveges adatokat.
- A [Extended Properties](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkciója használható megszüntetheti a feldolgozási érintettek, mivel lehetővé teszi a felhasználók egyéni tulajdonságok adatbázis-objektumok és adatok kódnak "Kifutott" támogatásához az alkalmazáslogikát, hogy megakadályozza a társított személyes adatok feldolgozása.
- [A sorszintű biztonsági](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy a felhasználók házirendek adatok feldolgozási megszüntetheti való hozzáférésének korlátozásához.
- [SQL adatbázis dinamikus adatok maszkolása (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a személyes adatok veszélyeztetettségének rendszerjogosultsággal nem rendelkező felhasználók vagy alkalmazások adatok maszkolása által. DDM automatikusan potenciálisan bizalmas információk felderítésére és a megfelelő maszkok alkalmazni kívánt javaslatot. Ezzel a megoldással a jogosult a GDPR protection, és csökkenti a hozzáférést úgy, hogy azt nem létezik az adatbázis keresztül jogosulatlan hozzáférés a személyes adatok azonosítása. **Megjegyzés: Az ügyfelek kell igazodnia kell az adatbázis-séma DDM beállításainak a módosítását.**

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure környezetben személyes adatokhoz való hozzáférés kezelése képességeket biztosítják:
-   [Az Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ebben a megoldásban minden felhasználó az aad-ben, beleértve a felhasználók az SQL-adatbázis elérésekor jönnek létre.
-   Hitelesítés az alkalmazás aad-ben alapján történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlop titkosítási AAD használ az alkalmazás az Azure SQL Database-hitelesítéséhez. További információkért lásd: hogyan [bizalmas adatokat az SQL-adatbázis védelme](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a minden részletre kiterjedő hozzáférési engedélyek megadását csak olyan mértékű hozzáférést a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Helyett az Azure-erőforrások minden felhasználó korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos műveletek személyes adatainak eléréséhez. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként.
- [Aad-ben Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfelek minimalizálása érdekében a felhasználók száma, akik hozzáférhetnek a bizonyos adatokat, például a személyes adatok.  Rendszergazdák AAD Privileged Identity Management segítségével ismerje meg, korlátozása, és a kiemelt jogosultságú identitások és azok erőforrásokhoz való hozzáférés figyelésére. Ez a funkció érvényesítését igény szerint közvetlenül az időponthoz kötött rendszergazdai hozzáférés szükség esetén is használható.
- [Az AAD-Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a potenciális biztonsági réseket egy szervezet identitásait érintő, konfigurálja az automatikus válaszokat ad egy szervezet identitásait kapcsolódó észlelt gyanús tevékenységek és pedig megvizsgálja a gyanús incidensek, a megfelelő művelettel hárítsa el őket.

### <a name="security"></a>Biztonsági
**Titkos kulcsok kezelése**: A megoldás az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok kezeléséhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi képességeket Azure Key Vault segít a felhasználóknak személyes adatok és az ezekhez az adatokhoz való hozzáférés védelmét:
- Speciális hozzáférési házirendek konfigurálása a szükséges alapul.
- Kulcstároló hozzáférési házirendeket a kulcsok és titkos minimálisan szükséges engedélyeket.
- Minden kulcsok és titkos kulcs tárolóban lévő állapottal rendelkezik lejáratának dátumát.
- A Key Vault minden kulcs speciális hardveres biztonsági modulokkal (HSM) védett. A kulcs HSM-védelemmel 2048 bites RSA-kulcs esetében.
- Minden felhasználó és identitások kapnak az RBAC használata minimálisan szükséges engedélyeket.
- Diagnosztikai naplókat a Key Vault legalább 365 nap megőrzési idővel rendelkező engedélyezve van.
- A kulcsok engedélyezett titkosítási műveleteket a meglévők közül szükséges korlátozódnak.

**Felügyeleti javítás**: Windows virtuális gépek telepítése a referencia-architektúrában részeként az automatikus frissítések kap a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja az OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatás, amelyen keresztül a frissített telepítések is létrehozható a következő virtuális gépek szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára lehetővé teszi a valós idejű védelem segítségével azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható a riasztások Ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni futtassa a védett virtuális gépet.

**Biztonsági riasztások**: [az Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) lehetővé teszi az ügyfelek felügyeljék a forgalmat, gyűjtését és adatforrások esetében fenyegetések elemzése. Emellett az Azure Security Center fér hozzá Azure-szolgáltatások adja meg a konfigurációs és szolgáltatási javaslatokat növelheti a biztonságot, és a személyes adatok védelme érdekében meglévő konfigurációját. Az Azure Security Center tartalmaz egy [az eszközintelligencia-jelentés fenyegetés](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) az egyes észlelt fenyegetés segítséget nyújt az incidensválasz-csoportok vizsgálja meg, és szervizelheti azokat a fenyegetéseket.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állású**: kiszolgáló-munkaterhelések vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure virtuális gépek magas rendelkezésre állásának biztosításához. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén a 99,95 % értekezlet Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és az összes konfiguráció Azure virtuális gépek ebben az architektúrában védelmét. A Recovery Services-tároló, az ügyfelek is fájlok és mappák visszaállítása egy infrastruktúra-szolgáltatási virtuális gép helyreállítása a teljes virtuális gép, gyorsabb visszaállítás engedélyezése nélkül.

### <a name="logging-and-auditing"></a>Naplózás és naplózás
[Az Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) rendszer és felhasználói tevékenységek, valamint a rendszerállapot-részletes naplózást végez. Az OMS [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetben.
- **Tevékenységi naplóit**: [tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) előfizetés erőforrásainak végrehajtott műveletek betekintést nyújtanak. Tevékenységi naplóit is meghatározásához, hogy egy művelet kezdeményező az előfordulási és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplók tartalmazzák. Ezek a naplók tartalmazzák a Windows rendszer-eseménynaplói és Azure Blob storage táblák és várólista naplókat.
- **Archiválás jelentkezzen**: minden diagnosztikai naplók írni egy központosított és titkosított Azure storage-fiókjához archiválási. A megőrzési felhasználó által konfigurálható, mentése 730 nap szervezet-specifikus megőrzési követelményeinek megfelelően. Ezek a naplók feldolgozása, tárolására és irányítópult reporting Azure Naplóelemzés csatlakozni.

Emellett a következő OMS-megoldások tartoznak ebbe az architektúrába részeként:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapotát ellenőrző megoldás a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza.
-   [Kártevőirtó Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldást jelent a kártevő szoftver, a fenyegetések és a védelmi állapot.
-   [Azure Automation szolgáltatásbeli](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és a naplózási](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonság és a naplózási irányítópult erőforrások biztonsági állapotát egy magas szintű betekintést biztosít a biztonsági tartományok, jelentős problémák, észlelések, fenyegetésfelderítési adataival és közös biztonsági lekérdezések metrikák megadásával.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): megoldás az SQL állapotának ellenőrzése a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A frissítés felügyeleti megoldás lehetővé teszi az operációs rendszer biztonsági frissítések, a rendelkezésre álló frissítések állapot és a kötelező frissítések telepítésének folyamatát, például felhasználói kezelését.
-   [Ügynök állapotfigyelő](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az ügyfélállapot-megoldást jelent, hány ügynök van telepítve, és a földrajzi eloszlása, valamint hány ügynökök, amelyek nem válaszoló és ügynökök, amelyek a működési adatok elküldése.
-   [Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A tevékenység Naplóelemzési megoldás segítséget nyújt a elemzése az Azure tevékenységi naplóit az ügyfél az összes Azure-előfizetések között.
-   [A változáskövetés](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A változáskövetési megoldás lehetővé teszi az ügyfelek azonosítását a változtatásokat a környezetben.

## <a name="threat-model"></a>Fenyegetések modellezése

Az adatfolyam-diagram a referenciaarchitektúra érhető el [letöltése](https://aka.ms/gdprDWdfd) vagy alatt található. Ez a modell segít megérteni a pontokat a rendszer infrastruktúra kockázatokkal módosításakor az ügyfelek.

![Adatraktár-GDPR fenyegetések modellezése](images/gdpr-datawarehouse-threat-model.png?raw=true "GDPR fenyegetések modellezése tartozó adatraktár")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure biztonsági és megfelelőségi tervezetének – GDPR ügyfél felelősségi mátrix](https://aka.ms/gdprCRM) felsorolja az összes GDPR cikkhez a tartományvezérlő és a processzor feladatkörök. Vegye figyelembe, hogy az Azure szolgáltatások esetében az ügyfél általában a tartományvezérlő és a Microsoft úgy működik, mint a processzor.

A [Azure biztonsági és megfelelőségi tervezetének - GDPR Data Warehouse megvalósítási mátrix](https://aka.ms/gdprDW) mely GDPR a cikkek által a data warehouse architektúrája, beleértve a részletes leírását, hogy a javított információival megvalósítási megfelel minden kezelt cikk.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN- és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságos kapcsolatot létrehozni a data warehouse referenciaarchitektúra részeként telepített erőforrások. Megfelelően állítja be a VPN- vagy ExpressRoute, az ügyfelek megteheti egy védelmi réteget biztosít adatokat az átvitel során.

Az Azure-ral biztonságos VPN-alagúton implementálásával egy a helyszíni hálózat és az Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozhatók létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek biztonságosan "tunnel" információkhoz belül az ügyfél hálózati és az Azure közötti egy titkosított kapcsolat. Telephelyek közötti VPN egy biztonságos, érett technológia, amely különböző méretű vállalatok által évtizedeken van telepítve. A [IPsec-bújtatásos mód](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást olyan titkosítási módszert használatban van.

Forgalom a VPN-alagút belül haladnak át a telephelyek közötti VPN az internethez, mert a Microsoft egy másik, a megoldás még biztonságosabbá kapcsolat lehetőséget is kínál. Az Azure ExpressRoute egy dedikált WAN Azure és egy helyszíni helyre vagy az Exchange-szolgáltató közötti kapcsolat. ExpressRoute-kapcsolatok az interneten keresztül halad, mivel ezeket a kapcsolatokat további megbízhatóságát, gyorsabb sebességű, kisebb késések fordulnak elő, és kínálnak nagyobb biztonságot nyújtana tipikus kapcsolatok az interneten keresztül. Továbbá mivel ez a közvetlen kapcsolat az ügyfél telekommunikációs szolgáltató, az adatok nem használható az interneten keresztül tartalmaz, és ezért nem érhető el azt.

Ajánlott eljárások a biztonságos hibrid hálózat, amely egy helyi hálózati kibővíti az Azure-bA [elérhető](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Kivonat-átalakítási-betöltési (ETL) folyamat
[A PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) adatok betöltése az Azure SQL Data Warehouse nincs szükség külön ETL vagy eszköz importálni. PolyBase-T-SQL lekérdezések adatokhoz való hozzáférés lehetővé teszi. A Microsoft üzleti intelligencia és elemzési verem, valamint külső eszközök kompatibilis az SQL Server, a polybase-zel használható.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) fontos a központi telepítés kezelése és a kiépítés a környezetben való interakció csoporthoz való hozzáférés. Meglévő Windows Server Active Directory integrálható az AAD-ben [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Az ügyfelek is nagy terhelést jelent a telepített Active Directory-infrastruktúrát (tartományvezérlők) egy meglévő aad-ben való azáltal, hogy a telepített Active Directory-infrastruktúra altartománya: az AAD-erdőt.

### <a name="optional-services"></a>Választható szolgáltatások
Azure services, elősegítve ezzel a tárolási és átmeneti formázott és formázatlan adatok választékát kínálja. Az alábbi szolgáltatások is hozzáadhatók a referencia-architektúrában, attól függően, hogy az ügyfél-követelmények:
-   [Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) egy felügyelt felhőszolgáltatás, amely a kivonat-átalakítási-betöltési (ETL), összetett hibrid készült integrációs projektek kivonat-betöltési-átalakítás (ELT), és adatokat. Az Azure Data Factory nyomkövetési, és keresse meg a személyes adatok képességekkel rendelkezik, beleértve a képi megjelenítés és figyelési eszközök azonosításához, amikor érkező adatokat, és ahol érkezett. Azure Data Factory használatával, az ügyfelek hozhat létre és folyamatok, amelyek különböző adattárolókhoz származó adatok nevű adatvezérelt munkafolyamatok ütemezni. Ezek a folyamatok lehetővé teszi az ügyfeleknek egyaránt belső és külső forrásból származó adatok. Az ügyfelek ezután feldolgozni, és kimeneti adatok átalakítása például az Azure SQL Data Warehouse adattárolókhoz.
- Az ügyfelek is készítheti a strukturálatlan adatok [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), amely lehetővé teszi az adatok bármilyen méretű, típusú, a rögzítés és adatfeldolgozást felgyorsíthatja a műveleti és felderítési jellegű egyetlen helyen.  Azure Data Lake képességeket, amelyek lehetővé teszik a kivonása és adatainak konvertálása rendelkezik. Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis, és jól integrálható az Azure SQL Data Warehouse például más Azure-szolgáltatásokkal.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.
 - Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.
 - Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
 - Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
