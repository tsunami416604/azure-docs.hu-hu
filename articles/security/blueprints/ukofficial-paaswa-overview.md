---
title: Azure biztonsági és megfelelőségi terv – a PaaS webes alkalmazás üzemeltetése Egyesült Királyság hivatalos számítási feladatokhoz
description: Azure biztonsági és megfelelőségi terv – a PaaS webes alkalmazás üzemeltetése Egyesült Királyság hivatalos számítási feladatokhoz
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: f465a6e6cb0642f64670a8d2727c939f91bad134
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617249"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure biztonsági és megfelelőségi terv: PaaS webes alkalmazások üzemeltetése Egyesült Királyság hivatalos számítási feladatokhoz

## <a name="azure-security-and-compliance-blueprints"></a>Azure biztonsági és megfelelőségi tervek

Az Azure tervezetek útmutató dokumentumok és architektúrák felhőalapú megoldásokat kínálnak, amelyek akkreditáció vagy megfelelőségi követelményekre rendelkező forgatókönyvek az automation sablonokból állnak. Azure tervezetek gyűjteményei útmutatást és automatizálási sablon, amely lehetővé teszi ügyfeleink számára a Microsoft Azure segítségével kiterjeszthető minden olyan további követelmények teljesítéséhez foundation architektúrájának kiépítés üzleti céljainak kézbesítésének gyorsítását.

## <a name="overview"></a>Áttekintés

Az Azure biztonsági és megfelelőségi terv nyújt útmutatást és automatizálási szkriptek, hogy a Microsoft Azure [platformszolgáltatás (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) üzemeltetett megfelelő feladat elvégezhető a besorolt webalkalmazás-architektúra mint [UK-OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Ehhez a besoroláshoz magában foglalja a legtöbb létrehozott vagy az állami szektor által feldolgozott adatokat. Ez magában foglalja a rutinszerű üzleti tevékenységét és a szolgáltatások, mely Ha elveszett, ellopott vagy a média, ezek közül néhány következményekkel járhat káros közzétett. A hivatalos besorolást a tipikus threat profilja nincs ugyanúgy zajlik, mint egy privát üzleti, akik értékes adatokat és szolgáltatásokat biztosít. Egyesült Királyság hivatalos helyesen, Egyesült Királyság kormányának adatok és szolgáltatások fenyegetést vagy a támadók a biztonsági sérülés elleni védelemre a szükséges korlátozott funkciók és erőforrások például (de nem korlátozódik az) hactivists, single-issue nyomás csoportok, nyomozói újságíróknak teljes mértékben az egyes támadók, és a legtöbb büntetőjogi egyének és csoportok.

Ez a megoldás által az Egyesült Királyság nemzeti Kibertámadások biztonsági központ (NCSC) felülvizsgálták, és illeszkedik az NCSC 14 Felhőbiztonsági irányelveinek.

Az architektúra az Azure [szolgáltatásként nyújtott platformon](https://azure.microsoft.com/overview/what-is-paas/) , hogy olyan környezetet, amely lehetővé teszi, hogy elkerülje a költségeket és összetettséget, hogy a szoftverlicencek, az alkalmazás háttér-infrastruktúra összetevői és közbenső vagy a fejlesztői eszközöket, és egyéb erőforrásokat. Ügyfelek kezelése az alkalmazások és szolgáltatások, általa fejlesztett, üzleti értéket, továbbítása, míg a Microsoft Azure kezeli a többi Azure-erőforrások például a virtuális gépek, tárolási és hálózatkezelési, több üzembe kellene összpontosítani a [megosztása felelősség](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) az infrastruktúra-felügyelettel az Azure platform be. [Az Azure App Services](https://azure.microsoft.com/services/app-service/) automatikus méretezés, magas rendelkezésre állást kínál, támogatja a Windows és Linux rendszerű, és lehetővé teszi az automatikus telepítéseket a GitHub, Visual Studio Team Services vagy bármely Git-tárház alapértelmezés szerinti szolgáltatásként. App Services használatával, a fejlesztők anélkül infrastruktúra kezelése üzleti értéket a összpontosíthat. Lehetséges, előzmények nélküli új alkalmazást szeretne létrehozni a Java, PHP, Node.js, Python, HTML vagy C# webes vagy áttelepíteni meglévő felhő vagy a helyi webalkalmazások Azure App Services (bár alapos megfelelő gondossággal, valamint tesztelési megerősítéséhez a teljesítmény megadása kötelező).

Ez a megoldás összpontosít a kiépítés egy biztonságos alaprendszerrel [szolgáltatásként nyújtott platformon](https://azure.microsoft.com/overview/what-is-paas/) nyilvános és a is vissza felhasználói webes felület. Ezt a tervrajz forgatókönyvre is figyelembe veszi az Azure használatát üzemeltetett szolgáltatások webes, ahol egy nyilvános felhasználói biztonságosan elküldése, megtekintheti, és bizalmas adatkezelés; is, hogy vissza office vagy a kormányzati operátor biztonságosan, amely a nyilvános felhasználó elküldte a bizalmas adatokat képes feldolgozni. Ebben a forgatókönyvben a használati esetek lehetnek:

- Egy adóbevallást, küldjön egy kormányzati operátor szerinti szűrése feldolgozása a Küldés; felhasználó
- A felhasználó keresztül egy webalapú alkalmazás, szolgáltatás ellenőrzése, és a szolgáltatás; kézbesítése háttérrendszerhez felhasználóként vagy
- Egy felhasználói keresést és a nyilvános tartomány megtekintése segít a kormányzati szolgáltatás szoftvertermékekkel kapcsolatos információk.

Használatával [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sablonok és az Azure parancssori felület parancsfájlokat, a tervezet helyez üzembe egy környezetet, amely megfelel, az Egyesült Királyság nemzeti Kibertámadások biztonsági központ (NCSC) 14 [Felhőbiztonsági irányelveinek](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) és az internetes biztonság (CIS) központ [kritikus fontosságú biztonsági vezérlők](https://www.cisecurity.org/critical-controls.cfm). A NCSC javasolja azok Felhőbiztonsági irányelveinek lehet ügyfelek használják, a szolgáltatás biztonsági tulajdonságainak kiértékelése és annak megértésében, az osztálynak a felelős az ügyfél és a szolgáltató között. A Microsoft közzétett ezeket az alapelveket segítségével jobban megismerheti a felosztás feladatok mindegyike az adatokat. Ez az architektúra és a megfelelő Azure Resource Manager-sablonok a Microsoft tanulmány által támogatott [UK 14 Felhőbeli biztonsági vezérlők használatával a Microsoft Azure felhőalapú](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ez az architektúra a NCSC felülvizsgálták, és illeszkedik az Egyesült Királyság NCSC 14 Felhőbiztonsági irányelveinek, ezáltal lehetővé téve az állami szektor szervezetek gyorsított képesek megfelelni a megfelelőségi kötelezettségeket használatával a felhőalapú szolgáltatások globális és az Egyesült Királyság a Microsoft Azure felhőbe. Ez a sablon üzembe helyezi az a számítási infrastruktúra. Alkalmazáskód és a támogató üzleti szint és az adatok szint szoftvert kell telepíteni és konfigurálni az ügyfelek általi. Részletes üzembe helyezési utasítások [Itt](https://aka.ms/ukofficial-paaswa-repo/).

Ez a megoldás egy olyan foundation architektúra. Ügyfeleink a tervezet használhatja alapjaként hivatalos besorolási web-alapú számítási feladatai és bontsa ki a sablonok és erőforrások követelményeknek. Ez a megoldás a vizualizációtervezés épül a [UK hatósági háromrétegű IaaS webalkalmazások tervezetet](https://aka.ms/ukofficial-iaaswa) mindkét felhasználóknak kínált, [infrastruktúra-szolgáltatás (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) és PaaS megvalósításának lehetőségei web-alapú számítási feladatok futtatásához.

Ez a megoldás üzembe helyezéséhez Azure-előfizetésre szükség. Ha nem rendelkezik Azure-előfizetéssel, regisztrálhat gyorsan és egyszerűen díjmentesen: Ismerkedés az Azure-ral. Kattintson a [Itt](https://aka.ms/ukofficial-paaswa-repo/) üzembe helyezési utasítások.

## <a name="architecture-and-components"></a>Architektúra és összetevők

Ez a megoldás egy webalkalmazás-üzemeltetési megoldás, amely támogatja az Egyesült Királyság hivatalos számítási feladatokat az Azure felhőalapú környezetben biztosít. Az architektúra, amely kihasználja az Azure platformot, egy szolgáltatásfunkciók biztonságos környezetet kínál. A környezeten belül a két App Service web apps szolgáltatásban üzembe helyezett (egy nyilvános felhasználók számára) és a egy háttérrendszerhez rendelkező felhasználók számára, egy API-alkalmazás szinten a webes előtér az üzleti szolgáltatásokat biztosít. Az Azure SQL Database felügyelt relációs adatok tárolására az alkalmazás van telepítve. Ezeket az összetevőket a platform kívül, és ezek az összetevők közötti kapcsolat révén a TLS 1.2 adatok biztosítására átviteli adatvédelem, az Azure Active Directory által hitelesített hozzáféréssel rendelkező van titkosítva.

![PaaS webes alkalmazások üzemeltetése az Egyesült Királyság hivatalos számítási feladatok architekturális diagramja](images/ukofficial-paaswa-architecture.png?raw=true "PaaS webes alkalmazások üzemeltetése az Egyesült Királyság hivatalos számítási feladatok architekturális diagramja")

Részeként az üzembe helyezési architektúrája, biztonságos tároló üzembe helyezése, figyelés és naplózás, egységes biztonsági felügyeletet & komplex veszélyforrások elleni védelem és felügyeleti képességeket is telepített, győződjön meg arról, hogy az ügyfél rendelkezik az összes, a szükséges eszközök a megoldás saját környezetükben figyeléséhez és védelméhez.

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletei a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Azure Active Directory
- Felügyeltszolgáltatás-identitás
- App Service
- Webalkalmazás
- API-alkalmazás
- Azure DNS
- Key Vault
- Azure Monitor
- Application Insights
- Log Analytics
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

### <a name="security"></a>Biztonság

#### <a name="identity-and-authentication"></a>Identitás és hitelesítés

Ez a megoldás biztosítja, hogy erőforrásokhoz való hozzáférés védelme – címtár- és identitáskezelési szolgáltatásokat. Ez az architektúra lehetővé teszi a teljes mértékben [identitása, mint a biztonsági határt](https://docs.microsoft.com/azure/security/security-paas-deployments#identity-as-the-primary-security-perimeter). 

A következő technológiákat biztosítja identitás az eszközkezelési funkciókat az Azure-környezetben:

- [Az Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. A megoldás összes felhasználó jöttek létre az Azure Active Directoryban, beleértve a felhasználók az SQL-adatbázis elérésére.
- A webalkalmazás és a felügyelet az Azure-erőforrások hozzáférési szereplő hitelesítés az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Oszloptitkosítás adatbázis az Azure AD az Azure SQL Database az alkalmazás hitelesítéséhez. További információkért lásd: [Always Encrypted: bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- A webalkalmazáshoz használt állampolgári nyilvános hozzáférés van konfigurálva. A fióklétrehozás és a hitelesítés az active directory segítségével vagy a közösségi hálózati Identitásszolgáltatók engedélyezéséhez [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) integrálhatók, ha szükséges.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a potenciális biztonsági réseket és a kockázatos fiókok ajánlásokkal növelése érdekében a szervezet identitásait biztonsági állapotát, konfigurálja a észlelt automatikus válaszok gyanús tevékenységeket a szervezet identitásait, kapcsolódó, és megvizsgálja a gyanús események és vesz igénybe a problémák megoldásához szükséges lépéseket.
- [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) lehetővé teszi, hogy pontosan fókuszban lévő hozzáférés-kezelés az Azure-hoz. Előfizetéshez való hozzáférés korlátozva, az előfizetés rendszergazdája, és az Azure Key Vault hozzáférés csak kulcskezelő hozzáférést igényelnek a felhasználók korlátozódik.
- Keresztül kihasználva [Azure Active Directory feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) ügyfelek kényszerítheti a további biztonsági ellenőrzéseket, alkalmazások vagy felhasználók a környezetükben például hely, eszköz-, állapot és bejelentkezési meghatározott feltételek alapján való hozzáférés a kockázat.
- [Az Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model) alkalmazás-tervezés – ajánlott eljárások kombinálva, DDoS-támadások, az mindig forgalomfigyelést, és valós idejű csökkenti a hálózati szintű gyakori támadások elleni védelmet biztosít. PaaS architektúrával platform-szintű DDoS protection az átlátható az ügyfél és a platform beépített, de fontos megjegyezni, hogy az alkalmazás biztonsági tervezési felelős az ügyfél rejlik.

#### <a name="data-in-transit"></a>Az átvitt adatok

Adatok kívül az átvitel során, és az Azure-összetevők közötti védje [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), amely használatával szimmetrikusan titkosítja a közös titkos kulcsot alapján, hogy kommunikáció titkosításához utazás a hálózaton keresztül. Alapértelmezés szerint hálózati forgalom használatával lett biztonságossá téve a TLS 1.2.

#### <a name="security-and-malware-protection"></a>Biztonság és a kártevők elleni védelem

[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) központi az Azure-erőforrások biztonsági állapotát jeleníti meg. Egy pillanat alatt ellenőrizheti, hogy a megfelelő biztonsági ellenőrzések, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.

[Az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) egy személyre szabott felhőalapú tanácsadó, amely segítséget nyújt az Azure-környezetek optimalizálására vonatkozó ajánlott eljárásokat követve. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

[A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) egy valós idejű védelem képesség, amellyel a vírusok, kémprogramok és más kártevők azonosításában és. Ez alapértelmezés szerint telepítve van az alapul szolgáló PaaS virtuálisgép-infrastruktúra és az Azure-hálót biztosítása a vásárlók számára transzparens módon kezeli

### <a name="paas-services-in-this-blueprint"></a>Ez a megoldás a PaaS-szolgáltatások

#### <a name="azure-app-service"></a>Azure App Service

Az Azure Web Apps egy teljes körűen felügyelt webes webalkalmazás fejlesztett infrastruktúra kezelése nélkül a Java, PHP, Node.js, Python, HTML és C# környezetet üzemeltetési biztosít. Automatikus méretezést biztosít, és magas rendelkezésre állás érdekében támogatja a Windows és Linux egyaránt, és lehetővé teszi, hogy az automatikus telepítéseket a [Visual Studio Team Services](https://azure.microsoft.com/services/visual-studio-team-services/) vagy bármely egyéb Git-alapú adattárból.

App Service [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/TrustCenter/) és hitelesítheti a felhasználókat a [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) vagy közösségi bejelentkezés ([Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication), [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication), [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication), és [Microsoft hitelesítési](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication).

Alapszintű, Standard és prémium csomag éles számítási feladatokra, és dedikált virtuálisgép-példányokon. Egyes példányok több alkalmazást és tartományt is támogathatnak. App services is támogatási [IP-címkorlátozások](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) , szükség esetén a megbízható IP-címek forgalmának biztonságossá tétele és is [Felügyeltszolgáltatás-identitás](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) egyéb PaaS-szolgáltatások, például a biztonságoskapcsolat[ Key Vault](https://azure.microsoft.com/services/key-vault/) és [az Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Ha további biztonsági szükség az izolált csomag az alkalmazások privát, dedikált Azure-környezetben üzemelteti, és az alkalmazásokhoz, amelyeknél a biztonságos kapcsolat a helyszíni hálózat, vagy további teljesítmény és skálázhatóság ideális.

Ez a sablon a következő App Service-szolgáltatásokat telepíti:

- [Standard szintű](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) Csomagszint App Service-ben
- Több webalkalmazás [üzembe helyezési pontok](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing): fejlesztési, előzetes verzió, QA, foglalja annak és természetesen éles (alapértelmezett tárhely).
- [Felügyeltszolgáltatás-identitás](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) csatlakozni [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (Ez a hozzáférés biztosítására is használható [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Integráció a [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) teljesítményének figyelése
- [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- A metrika [riasztások](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

Az SQL Database általános célú, felügyelt relációsadatbázis-szolgáltatás a Microsoft Azure-ban, amely egyebek mellett relációs, JSON-, térbeli és XML-struktúrákat is támogat. Ajánlatok az SQL Database felügyelt egyetlen SQL-adatbázisok, felügyelt SQL-adatbázisok egy [rugalmas készlet](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool), és az SQL [felügyelt példányok](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (nyilvános előzetes verzióban elérhető). Szolgáltatás [dinamikusan méretezhető teljesítményt])https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) lehetőségeket biztosít, mint például [oszlopcentrikus indexek](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) mélységű elemzéshez és jelentéskészítéshez, és [memóriabeli OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) extreme számára tranzakciós feldolgozást kínál. A Microsoft zökkenőmentesen kezeli az SQL kódbázis karbantartását és frissítését, és teljesen átveszi az alapul szolgáló infrastruktúra kezelését.

Ez a megoldás az Azure SQL Database

Az Azure SQL Database-példány a következő adatbázis biztonsági intézkedések használja:

- [Kiszolgálószintű és adatbázisszintű tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), vagy a Kiszolgálókezelő [virtuális hálózati Szolgáltatásvégpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) használatával [virtuális hálózati szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) segít védekezni az Azure SQL Database és az Azure Data Warehouse a fenyegetés kártevő szándékú tevékenységek. Valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra inaktív azt végez anélkül, hogy a módosításokat.
- [Az Azure AD-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication), adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen identitása központilag kezelheti. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűsíti az engedélyek kezelését.
- Az adatbázis-felügyelethez az Azure Active Directory használata
- [Auditnaplók](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) tárfiókokba
- A metrika [riasztások](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) a sikertelen adatbázis-kapcsolatok
- [Az SQL Fenyegetésészlelés](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Mindig titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

A Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) egy Microsoft által felügyelt felhőszolgáltatás, amely magas rendelkezésre álló, biztonságos, tartós, méretezhető és redundáns tárolást tesz lehetővé. Az Azure Storage a Blob Storage, File Storage és Queue Storage szolgáltatásokat tartalmazza.

#### <a name="azure-storage-in-this-blueprint"></a>Ez a megoldás az Azure Storage

Ez a sablon a következő Azure Storage-összetevőket használja:

- [A Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Csak a HTTPS-kapcsolatok engedélyezése

#### <a name="data-at-rest"></a>Inaktív adat

Keresztül [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) Azure Storage tárterületre írt összes adat titkosítva van segítségével 256 bites AES-titkosítás, az egyik rendelkezésre álló legerősebb blokktitkosító. A Microsoft által felügyelt titkosítási kulcsok SSE technológiával is használhatja, vagy használhatja [saját titkosítási kulcsok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Storage-fiókok védelméről is [virtuális hálózati Szolgáltatásvégpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) használatával [virtuális hálózati szabályok](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Azure Storage védelmével kapcsolatos részletes információk található a [biztonsági útmutató](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Titkos kódok kezelése

#### <a name="azure-key-vault"></a>Azure Key Vault

[A Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) alkalmazás kulcsok és titkos kulcsok győződjön meg arról, hogy azok nem érhetők el biztonságossá tételére szolgál, harmadik fél által létrehozottat. A Key Vaultot nem a felhasználói jelszavak tárolására tervezték. Lehetővé teszi, hogy hozzon létre több biztonságos tároló, amelyeket kulcstartóknak nevezünk. Ezeket a tárolókat hardveres biztonsági modulok (HSM-ek) védik. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. Az Azure Key Vault kezelni tudja a Transport Layer Security- (TLS-) tanúsítványok kérelmezését és megújítását, biztosítva a tanúsítvány életciklusának megbízható kezelési szolgáltatásához szükséges funkciókat.

#### <a name="azure-key-vault-in-this-blueprint"></a>Ez a megoldás az Azure Key Vault

- A tárelérési kulcs, rendelkezik olvasási hozzáférést nyújtani a [Felügyeltszolgáltatás-identitás](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) a ügyfél felé néző webes alkalmazás
- Az SQL Server adatbázis jelszót tartalmazza (a külön tárolóban)
- Diagnosztikai naplózás

### <a name="monitoring-logging-and-audit"></a>Monitorozás, naplózás és naplózása

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) egy szolgáltatás az Azure-ban, amely segít összegyűjteni és elemezni a felhőben lévő erőforrások által létrehozott adatokat és a helyszíni környezetekben.

#### <a name="log-analytics-in-this-blueprint"></a>Ez a megoldás a log Analytics

- SQL-felmérés
- A Key Vault-diagnosztika
- Application Insights-kapcsolat
- Azure-tevékenységnapló

#### <a name="application-insights"></a>Application Insights

[Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Használt figyelése élő webalkalmazások azt fogja automatikusan felismeri a teljesítményanomáliákat, elemezheti a teljesítményt, diagnosztizálhatja a hibákat, és megérteni a felhasználók hogyan használják az alkalmazással. Az Application Insights is üzembe helyezhetők a platformokon, beleértve a .NET, Node.js és a j2ee-kiszolgálókon, a helyileg üzemeltetett vagy a felhőben. Emellett a DevOps folyamattal is integrálható, és eszközök széles köréhez rendelkezik kapcsolódási ponttal.

#### <a name="application-insights-in-this-blueprint"></a>Ez a megoldás az Application Insights

Ez a sablon a következő Application Insights-összetevőket használja:

- Application Insights irányítópult helyenként (operátor, ügyfél és API-t)

#### <a name="azure-activity-logs"></a>Azure-Tevékenységnaplók

[Azure-tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) az előfizetések vezérlősík eseményeket naplózza. A tevékenységnapló használatával megadhatja, hogy a "mit, ki, és mikor" írási műveletek (PUT, POST, DELETE) tett erőforrásokra az előfizetésben. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is.

#### <a name="azure-monitor"></a>Azure Monitor

[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) lehetővé teszi, hogy alapvető metrikákat, a vizsgálati naplók és a diagnosztikai naplók gyűjteményét azáltal, hogy az Azure-szolgáltatásokhoz figyelés. Az Azure Monitor alapszintű infrastruktúrametrikákat és -naplókat biztosít a legtöbb Microsoft Azure-szolgáltatásról.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/ukofficial-paaswa-tm) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![PaaS webes alkalmazások üzemeltetése Egyesült Királyság hivatalos számítási feladatok threat modell](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS webes alkalmazások üzemeltetése az Egyesült Királyság hivatalos számítási feladatok fenyegetések modellezése")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC Felhőbiztonsági irányelveinek megfelelőségi dokumentációja

A Királyi kereskedelmi szolgáltatást (egy működik, kereskedelmi és beszerzési javítható a kormányzati hivatal) megújítani a Microsoft releváns enterprise cloud services – G-Cloud 6-os verziójának besorolása kiterjedő az ajánlatokat a hivatalos szintjén. Az Azure és a G-Cloud részletei megtalálhatók a [Azure Egyesült Királyságbeli G-Cloud biztonsági értékelés összegzése](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Ez a megoldás igazodnak-e a 14 felhőbiztonsági irányelveinek, amelyek szerepelnek a NCSC [Felhőbiztonsági irányelveinek](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) segítségével olyan környezetet, amely támogatja a számítási feladatok, Egyesült Királyság hivatalos besorolva.

A [Azure biztonsági és megfelelőségi terv – Egyesült Királyság hivatalos vevő felelőssége mátrix](https://aka.ms/ukofficial-crm) (Excel-munkafüzetét) összes 14 felhőbiztonsági irányelveinek sorolja fel, és minden elve (vagy elv alrészben) azt jelzi, hogy a elve megvalósítása a Microsoft, az ügyfél felelőssége, vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi terv – a PaaS webes alkalmazást az Egyesült Királyság hivatalos elvének megvalósítása mátrix](https://aka.ms/ukofficial-paaswa-pim) (Excel-munkafüzet) felsorolja az összes 14 felhőbiztonsági irányelveinek, és azt jelzi, hogy, minden egyes elve (vagy elv alrészben) az ügyfél feladatkörei mátrix, az ügyfél felelőssége, hogy van kijelölve, 1.) Ha a tervezet elve és (2) leírását, hogyan illeszkedik az megvalósítása az elv blokkolására végeznek.  

Továbbá a Cloud Security Alliance (CSA) közzététele a felhőalapú vezérlő mátrix ügyfelek támogatása a kiértékelés a felhőszolgáltatók és a kérdéseket, amelyeket a felhőalapú szolgáltatásokhoz való áthelyezése előtt kell megválaszolni azonosításához. Reagálva a Microsoft Azure CSA konszenzus értékelés kezdeményezés kérdőív választ ([CSA-RÓL](https://www.microsoft.com/TrustCenter/Compliance/CSA)), amely azt ismerteti, hogyan Microsoft szünteti meg a javasolt alapelveket.

## <a name="third-party-assessment"></a>Külső értékelése

Ez a megoldás által az Egyesült Királyság nemzeti Kibertámadások biztonsági központ (NCSC) ellenőrzött és igazodnak-e a NCSC 14 biztonsági alapelvei

Az automation-sablonokat a Microsoft-partner, és az Egyesült Királyság ügyfél sikeres egység Azure Felhőmegoldás-fejlesztő mérnök csapata által tesztelt [Ampliphae](http://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az Azure biztonsági és megfelelőségi tervezet Automation JSON konfigurációs fájloknak és a PowerShell-szkriptek üzembe helyezéséhez az Azure-erőforrások Azure Resource Manager API szolgáltatás által kezelt áll. Részletes üzembe helyezési utasítások [Itt](https://aka.ms/ukofficial-paaswa-repo).

Három módszer adtak meg a központi telepítése; Egy egyszerű "express" [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) gyorsan felépíthetők a teszt alkalmas környezet; egy paraméteres [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) megközelítés nagyobb számítási környezetek; konfigurációját és az Azure Portalon -alapú üzembe helyezés, ahol az operátor adhatja meg az üzembe helyezéshez megadott paraméterek az Azure Portalon keresztül. 

1.  Klónozás vagy letöltés [ez](https://aka.ms/ukofficial-paaswa-repo) a helyi munkaállomáson GitHub-adattárban.
2.  Felülvizsgálat [1. módszer: Azure parancssori felület 2 (Express verzió)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) , és hajtsa végre a megadott parancsokat.
3.  Felülvizsgálat [metódus 1a: Azure parancssori felület 2 (konfigurálása a telepítési szkript argumentumai keresztül)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) , és hajtsa végre a megadott parancsok
4.  Felülvizsgálat [2. módszer: Azure Portal központi telepítési folyamat](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) , és hajtsa végre a listán szereplő parancsok

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="api-management"></a>API Management

[Az Azure API Management](https://azure.microsoft.com/services/api-management/) biztonság, szabályozás és vezérlők történő közzétételére, proxy és API-k védelme további réteget biztosít az API App Service elé használható.

### <a name="azure-b2c"></a>Az Azure B2C-vel

[Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) előfordulhat, hogy a felhasználók regisztrálása, hozzon létre egy azonosítót, és engedélyezze az engedélyezési és hozzáférés-vezérlés a nyilvános webes alkalmazás vezérlőként kell végrehajtani.

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
- Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
- Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
- Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
- Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
