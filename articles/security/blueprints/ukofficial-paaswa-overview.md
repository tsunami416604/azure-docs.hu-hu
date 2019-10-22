---
title: Azure Security and Compliance Blueprint – az Egyesült Királyság hivatalos számítási feladatait üzemeltető webalkalmazások üzemeltetése
description: Azure Security and Compliance Blueprint – az Egyesült Királyság hivatalos számítási feladatait üzemeltető webalkalmazások üzemeltetése
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 1f6eeea85a348bb8e88a387fa0fc6bed55e41a5e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "71262782"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure Security and Compliance Blueprint: az Egyesült Királyság hivatalos számítási feladataihoz tartozó Péter webalkalmazás üzemeltetése

## <a name="azure-security-and-compliance-blueprints"></a>Azure biztonsági és megfelelőségi tervek

Az Azure-tervrajzok olyan útmutató dokumentumokból és automatizálási sablonokból állnak, amelyek felhőalapú architektúrákat helyeznek üzembe olyan forgatókönyvekhez, amelyek akkreditációs vagy megfelelőségi követelményekkel rendelkeznek. Az Azure tervrajzai olyan útmutatók és automatizálási sablonok gyűjteményei, amelyek lehetővé teszik, hogy Microsoft Azure ügyfelek felgyorsítsák üzleti céljaik megvalósítását egy olyan Foundation-architektúra kiépítésével, amely további követelmények teljesítése érdekében bővíthető.

## <a name="overview"></a>Áttekintés

Ez a Azure Security and Compliance Blueprint útmutatást és automatizálási parancsfájlokat biztosít egy olyan Microsoft Azure [platformként](https://azure.microsoft.com/overview/what-is-paas/) szolgáltatott webalkalmazás-architektúra biztosításához, amely az [Egyesült Királyság hivatalos tagjaként besorolt munkaterhelések kezelésére alkalmas. ](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Ez a biztonsági besorolás magában foglalja az állami szektor által létrehozott vagy feldolgozott információk többségét. Ez magában foglalja az olyan rutin üzleti műveleteket és szolgáltatásokat, amelyek elvesztése, ellopása vagy közzététele az adathordozón történik, amelyek némelyike káros következményekkel járhat. A hivatalos besorolásra jellemző fenyegetési profil sokkal ugyanaz, mint egy üzleti vállalkozás, amely értékes információkat és szolgáltatásokat nyújt. Az Egyesült Királyság hivatalos feladata, hogy az Egyesült Királyság kormányzati adatait vagy szolgáltatásait a fenyegetésekkel szemben, illetve a korlátozott képességekkel és erőforrásokkal, például (de nem kizárólag) hactivists, egyszeri kiállítható nyomástartó csoportokkal, tényfeltáró újságírókkal, az illetékes egyéni hackerek, valamint a bűnözői személyek és csoportok többsége.

Ezt a tervet a brit nemzeti Cyber Security Center (NCSC) felülvizsgálta, és a NCSC 14 Felhőbeli biztonsági alapelvekhez igazodik.

Az architektúra az Azure [platformot használja szolgáltatásként](https://azure.microsoft.com/overview/what-is-paas/) , amely lehetővé teszi az ügyfeleknek, hogy elkerüljék a szoftveres licencek megvásárlásának költségét és összetettségét, a mögöttes alkalmazás-infrastruktúra és a köztes alkalmazások felügyeletét, valamint a fejlesztői eszközök és egyéb erőforrások. Az ügyfelek felügyelik az általuk fejlesztett alkalmazásokat és szolgáltatásokat, és az üzleti értékek megvalósítására összpontosítanak, míg a Microsoft Azure kezeli a többi Azure-erőforrást, például a virtuális gépeket, a tárolást és a hálózatkezelést, így többek [között a ](../fundamentals/paas-deployments.md)az infrastruktúra felügyeletének felelőssége az Azure platformon. Az [Azure app Services](https://azure.microsoft.com/services/app-service/) automatikus méretezést, magas rendelkezésre állást, támogatja a Windowst és a Linuxot, és lehetővé teszi a GitHub, az Azure DevOps vagy bármely git-tárház automatikus üzembe helyezését alapértelmezett szolgáltatásként. A App Services használatával a fejlesztők az infrastruktúra kezelése nélkül koncentrálhat az üzleti értékek megvalósítására. Új Java-, PHP-, Node. js-, Python-, HTML-vagy C# webalkalmazások hozhatók létre, illetve a meglévő felhő vagy helyszíni webalkalmazások áttelepíthetők az Azure app Servicesba (bár a teljesítmény megerősítését szolgáló alapos átvilágítás és tesztelés kötelező).

Ez a terv egy biztonságos alapplatform kiépítésére koncentrál, amely a nyilvános és a Back-Office-felhasználók számára [szolgáltatásként](https://azure.microsoft.com/overview/what-is-paas/) szolgáló webes kezelőfelület. Ez a terv kialakítási forgatókönyve az Azure által üzemeltetett webes szolgáltatások használatát veszi figyelembe, ahol a nyilvános felhasználók biztonságosan küldhetnek, tekinthetnek és kezelhetnek bizalmas adatokat; azt is megteheti, hogy egy Back Office vagy Government-kezelő biztonságosan feldolgozza a nyilvános felhasználó által küldött bizalmas adatokat. Ebben a forgatókönyvben a következő esetekben használhatók:

- Az adóbevallást Beküldő felhasználó, aki a beküldést feldolgozó kormányzati kezelővel rendelkezik;
- Egy webalapú alkalmazáson keresztül szolgáltatást kérő felhasználó, amely egy Back-Office felhasználót érvényesít és kézbesíti a szolgáltatást; vagy
- A nyilvános tartományra vonatkozó súgó a kormányzati szolgáltatással kapcsolatos információkat kérő felhasználó.

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) -sablonok és az Azure parancssori felületi parancsfájlok használatával a terv egy olyan környezetet helyez üzembe, amely az Egyesült Királyság nemzeti Cyber Security Center (NCSC) 14 [Felhőbeli biztonsági alapelveihez](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) és a Center for internethez igazodik Biztonsági (CIS) [kritikus biztonsági vezérlők](https://www.cisecurity.org/critical-controls.cfm). A NCSC javasolja, hogy az ügyfelek a felhő biztonsági alapelveit használják a szolgáltatás biztonsági tulajdonságainak kiértékelésére, valamint az ügyfél és a szállító közötti felelősség megosztásának megismerésére. A Microsoft információt adott az egyes elvekről, hogy jobban megértse a felelősségek megosztását. Ezt az architektúrát és a hozzá tartozó Azure Resource Manager sablonokat a Microsoft tanulmánya, az [Egyesült királyságbeli felhőhöz tartozó 14 Felhőbeli biztonsági vezérlő támogatja Microsoft Azure használatával](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ezt az architektúrát felülvizsgálta a NCSC, és összhangban van az Egyesült Királyság NCSC 14 Felhőbeli biztonsági alapelveivel, így lehetővé téve a közszektor szervezetei számára, hogy gyorsan nyomon kövessék a megfelelőségi követelményeknek való megfelelést a felhőalapú szolgáltatások globális és az Egyesült királyságbeli használatával Microsoft Azure felhő. Ez a sablon telepíti az infrastruktúrát a munkaterhelés számára. Az alkalmazás kódját és az üzleti szintek és az adatszinteket támogató szoftvereket az ügyfeleknek kell telepíteniük és konfigurálniuk. A részletes üzembe helyezési utasítások [itt](https://aka.ms/ukofficial-paaswa-repo/)érhetők el.

Ez a terv egy Foundation-architektúra. Ügyfeleink a webes munkaterhelések hivatalos besorolásának alapjaként használhatják ezt a tervrajzot, és a saját igényeik szerint bővíthetik a sablonokat és az erőforrásokat. Ez a tervezet az [Egyesült királyságbeli, harmadik rétegbeli IaaS web Applications tervezet](https://aka.ms/ukofficial-iaaswa) alapelveire épül, és lehetővé tenné ügyfeleinknek a webalapú munkaterhelések üzemeltetésére szolgáló [infrastruktúra-szolgáltatás (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) és a Pásti-megvalósítási döntések meghozatalát.

A terv üzembe helyezéséhez Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, bármikor regisztrálhat gyorsan és egyszerűen díjmentesen: az Azure használatának első lépései. Kattintson [ide](https://aka.ms/ukofficial-paaswa-repo/) az üzembe helyezési utasításokhoz.

## <a name="architecture-and-components"></a>Architektúra és összetevők

Ez a terv egy webalkalmazás-üzemeltetési megoldást nyújt egy olyan Azure-beli felhőalapú környezetben, amely támogatja az Egyesült Királyság hivatalos munkaterheléseit. Az architektúra olyan biztonságos környezetet biztosít, amely szolgáltatásként biztosítja az Azure platformot. A környezeten belül két App Service webalkalmazás van üzembe helyezve (egyet a nyilvános felhasználók és az egyik a Back-Office felhasználók számára), egy API-alkalmazási réteggel, amely biztosítja az üzleti szolgáltatások webes kezelőfelületét. Az alkalmazáshoz egy Azure SQL Database felügyelt, összeállított adattárolóként van telepítve. Ezekhez az összetevőkhöz a platformon kívülről és az összetevők közötti kapcsolat titkosítása a TLS 1,2-en keresztül történik, hogy biztosítható legyen az adatok átvitele a forgalom védelme érdekében, Azure Active Directory által hitelesített hozzáféréssel.

![Pásti webalkalmazás-üzemeltetés az Egyesült királyságbeli hivatalos munkaterhelések hivatkozási architektúrájának diagramja](images/ukofficial-paaswa-architecture.png?raw=true "Pásti webalkalmazás-üzemeltetés az Egyesült királyságbeli hivatalos munkaterhelések hivatkozási architektúrájának diagramja")

Az üzembe helyezési architektúra részeként a biztonságos tárolás, a figyelés & naplózás, az egységes biztonsági felügyelet & a komplex veszélyforrások elleni védelem, valamint a felügyeleti képességek is üzembe helyezhetők, így biztosítva, hogy az ügyfelek rendelkezzenek az összes szükséges eszközzel a megoldás környezetének védelme és figyelése.

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei az [üzembe helyezési architektúra](#deployment-architecture) szakaszban találhatók.

- Azure Active Directory
- App Service
- Web App
- API App
- Azure DNS
- Key Vault
- Azure Monitor (naplók)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

### <a name="security"></a>Biztonság

#### <a name="identity-and-authentication"></a>Identitás és hitelesítés

Ez a terv biztosítja, hogy az erőforrásokhoz való hozzáférés a címtár-és Identitáskezelő szolgáltatásokon keresztül legyen védve. Ez az architektúra teljes mértékben kihasználja [az identitást, mint a biztonsági területet](../fundamentals/paas-deployments.md). 

Az alábbi technológiák az Azure-környezet Identitáskezelés-kezelési funkcióit biztosítják:

- [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelési szolgáltatása. A megoldás összes felhasználója Azure Active Directory-ban lett létrehozva, beleértve a SQL Databasehoz hozzáférő felhasználókat is.
- Az Azure AD használatával végezheti el a hitelesítést az Azure-erőforrások felügyeletére szolgáló webalkalmazáshoz és hozzáféréshez. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- Az adatbázis oszlopának titkosítása az Azure AD használatával hitelesíti az alkalmazást Azure SQL Database. További információ [: Always encrypted: bizalmas adatok védelme SQL Databaseokban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Az állampolgárok felé néző webalkalmazás nyilvános hozzáférésre van konfigurálva. Ha engedélyezni szeretné a fiókok létrehozását és hitelesítését az Active Directoryn vagy a közösségi hálózati identitás-szolgáltatókon keresztül, [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) szükség esetén integrálható.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a potenciális biztonsági réseket és a kockázatos fiókokat, javaslatokat tesz a szervezete identitásának biztonsági helyzetének növelésére, az észlelt gyanús automatikus válaszokat konfigurálja. a szervezete identitásával kapcsolatos műveletek, valamint a gyanús incidensek kivizsgálására és a szükséges műveletek elvégzésére.
- Az [Azure szerepköralapú Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) lehetővé teszi az Azure-hoz való, pontosan célzott hozzáférés-vezérlést. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik, és Azure Key Vault hozzáférés csak azokra a felhasználókra korlátozódik, akiknek kulcskezelő hozzáférésre van szükségük.
- A [Azure Active Directory feltételes hozzáférési](../../active-directory/active-directory-conditional-access-azure-portal.md) ügyfelek általi kihasználása a környezet alkalmazásaihoz vagy felhasználóihoz való hozzáférésre vonatkozó további biztonsági ellenőrzéseket kényszeríti ki bizonyos feltételek, például a hely, az eszköz, az állapot és a bejelentkezés kockázata alapján.
- [Azure DDoS Protection](../fundamentals/paas-deployments.md#security-advantages-of-a-paas-cloud-service-model) az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDOS-támadások ellen, a folyamatos forgalom figyelésével és a gyakori hálózati szintű támadások valós idejű enyhítésével. A "Péter" architektúrával az ügyfél és a platform szintjén a DDoS Protection átlátható, de fontos megjegyezni, hogy az alkalmazás biztonsági tervezési feladata az ügyfél.

#### <a name="data-in-transit"></a>Átvitt adatok

Az adatok átvitele kívülről és az Azure-összetevők között [Transport Layer Security/SSL (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption)használatával történik, amely egy közös titkos kulcson alapuló szimmetrikus titkosítást használ a kommunikáció titkosításához, amikor a hálózaton utaznak. Alapértelmezés szerint a hálózati forgalom biztonságos a TLS 1,2 használatával.

#### <a name="security-and-malware-protection"></a>Biztonság és kártevők elleni védelem

[Azure Security Center](https://azure.microsoft.com/services/security-center/) az Azure-erőforrások biztonsági állapotának központi nézetét biztosítja. Egy pillantással ellenőrizheti, hogy a megfelelő biztonsági vezérlők megfelelően vannak-e konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.

A [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) egy személyre szabott felhőalapú tanácsadó, amely az Azure-beli üzembe helyezések optimalizálására szolgáló ajánlott eljárások követésében nyújt segítséget. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

A [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) egy valós idejű védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb kártevő szoftvereket. Ez alapértelmezés szerint a rendszer a mögöttes Pásti virtuális gépek infrastruktúráján van telepítve, és az Azure-háló átlátható módon kezeli az ügyfelet.

### <a name="paas-services-in-this-blueprint"></a>Pásti-szolgáltatások ebben a tervben

#### <a name="azure-app-service"></a>Azure App Service

A Azure App Service teljes körűen felügyelt webes üzemeltetési környezetet biztosít a Java, a PHP, a Node. js Python, a C# HTML és az infrastruktúra kezelése nélkül fejlesztett webalkalmazásokhoz. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert, és lehetővé teszi az [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) vagy bármely git-alapú tárház automatizált üzembe helyezését.

A App Service [ISO, SoC és PCI szabványnak megfelelő](https://www.microsoft.com/TrustCenter/) , és a felhasználók hitelesítése [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) vagy közösségi bejelentkezéssel ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)és [Microsoft Authentication](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)).

Az alapszintű, standard és prémium csomagok éles számítási feladatokhoz használhatók, és dedikált virtuálisgép-példányokon futnak. Minden példány több alkalmazást és tartományt is támogat. Az App Services Emellett támogatja az [IP-címek korlátozásait](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) a megbízható IP-címekre irányuló adatforgalom biztonságossá tételéhez, ha szükséges, valamint az [Azure-erőforrások számára felügyelt identitásokat](https://docs.microsoft.com/azure/app-service/overview-managed-identity) is biztosít a más, például a [Key Vault](https://azure.microsoft.com/services/key-vault/) és az [Azure SQL-szolgáltatások Adatbázis](https://azure.microsoft.com/services/sql-database/). Ha további biztonságra van szükség, az elkülönített csomag egy privát, dedikált Azure-környezetben üzemelteti az alkalmazásokat, és ideális olyan alkalmazások számára, amelyek biztonságos kapcsolatot igényelnek a helyszíni hálózattal, illetve további teljesítménnyel és méretezéssel.

Ez a sablon a következő App Service szolgáltatásokat telepíti:

- [Standard szintű](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) App Service csomag szintje
- Több App Service [üzembe helyezési](https://docs.microsoft.com/azure/app-service/deploy-staging-slots)pont: dev, Preview, QA, ellenőrzését és Course Production (alapértelmezett tárolóhely).
- [Felügyelt identitások az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/app-service/overview-managed-identity) a [Azure Key Vaulthoz](https://azure.microsoft.com/services/key-vault/) való kapcsolódáshoz (ez a szolgáltatás a [Azure SQL Databasehoz](https://azure.microsoft.com/services/sql-database/) való hozzáférés biztosítására is használható. 
- Az [Azure Application Insights](../../azure-monitor/app/azure-web-apps.md) integrációja a teljesítmény figyeléséhez
- [Diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md) 
- Metrikus [riasztások](../../azure-monitor/app/alerts.md) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

Az SQL Database általános célú, felügyelt relációsadatbázis-szolgáltatás a Microsoft Azure-ban, amely egyebek mellett relációs, JSON-, térbeli és XML-struktúrákat is támogat. A SQL Database felügyelt önálló SQL-adatbázisokat, felügyelt SQL-adatbázisokat biztosít [rugalmas készletekben](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool), valamint SQL [felügyelt példányokat](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (nyilvános előzetes verzióban). [Dinamikusan méretezhető teljesítményt](../../sql-database/sql-database-purchase-models.md) nyújt és olyan lehetőségeket kínál, mint az [oszlopcentrikus indexelés](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) kivételes mélységű elemzéshez és jelentéskészítéshez, és a [memóriabeli OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) a kivételesen nagy teljesítményű tranzakció-feldolgozáshoz. A Microsoft zökkenőmentesen kezeli az SQL kódbázis karbantartását és frissítését, és teljesen átveszi az alapul szolgáló infrastruktúra kezelését.

Azure SQL Database ebben a tervben

A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:

- A [kiszolgáló-és adatbázis-szintű tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), illetve a [virtuális hálózati szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)használatával [Virtual Network szolgáltatási végpontokon](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) keresztül.
- Az [transzparens adattitkosítással](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) megvédheti Azure SQL Database és az Azure-adattárházat a rosszindulatú tevékenységek fenyegetésével szemben. Az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok valós idejű titkosítását és visszafejtését hajtja végre, anélkül, hogy az alkalmazás módosítására lenne szükség.
- Az [Azure ad-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)segítségével központilag kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásait egy központi helyen. A központi AZONOSÍTÓk kezelése egyetlen helyet biztosít az adatbázis-felhasználók felügyeletéhez, és egyszerűsíti az engedélyek kezelését.
- Azure Active Directory használata adatbázis-felügyelethez
- [Naplók naplózása](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) a Storage-fiókokba
- A sikertelen adatbázis-kapcsolatokra vonatkozó metrikai [riasztások](../../azure-monitor/app/alerts.md)
- [SQL-veszélyforrások észlelése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Oszlopok Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

A Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) egy Microsoft által felügyelt felhőalapú szolgáltatás, amely kiválóan elérhető, biztonságos, tartós, méretezhető és redundáns tárolást biztosít. Az Azure Storage a Blob Storage, File Storage és Queue Storage szolgáltatásokat tartalmazza.

#### <a name="azure-storage-in-this-blueprint"></a>Az Azure Storage ebben a tervben

Ez a sablon a következő Azure Storage-összetevőket használja:

- [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Csak HTTPS-kapcsolatok engedélyezése

#### <a name="data-at-rest"></a>Inaktív adatok

A [Storage Service encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) az Azure Storage-ba írt összes adatmennyiséget 256 bites AES-titkosítással titkosítja, amely az egyik legerősebb blokk titkosítási algoritmus. Használhatja a Microsoft által felügyelt titkosítási kulcsokat az SSE-mel, vagy használhat [saját titkosítási kulcsokat](../../storage/common/storage-encryption-keys-portal.md)is.

A Storage-fiókok [Virtual Network szolgáltatás-végpontokon](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) keresztül biztonságossá tehetik a [virtuális hálózati szabályok](https://docs.microsoft.com/azure/storage/common/storage-network-security)használatával.

Az Azure Storage biztonságossá tételével kapcsolatos részletes információkat a [biztonsági útmutatóban](https://docs.microsoft.com/azure/storage/common/storage-security-guide)találja.


### <a name="secrets-management"></a>Titkok kezelése

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) az alkalmazás kulcsai és titkos kulcsainak védelmére szolgál, hogy azok ne legyenek elérhetők harmadik felek számára. A Key Vaultot nem a felhasználói jelszavak tárolására tervezték. Lehetővé teszi, hogy több biztonságos tárolót hozzon létre, amelyek neve Vaults. Ezeket a tárolókat hardveres biztonsági modulok (HSM-ek) védik. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. Az Azure Key Vault kezelni tudja a Transport Layer Security- (TLS-) tanúsítványok kérelmezését és megújítását, biztosítva a tanúsítvány életciklusának megbízható kezelési szolgáltatásához szükséges funkciókat.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault ebben a tervben

- Tárolja a Storage-hozzáférési kulcsot, és olvasási jogosultsággal rendelkezik az ügyfél által megtekintett webalkalmazás [felügyelt identitása](https://docs.microsoft.com/azure/app-service/overview-managed-identity) számára.
- A SQL Server DBA-jelszót tárolja (külön tárolóban)
- Diagnosztika naplózása

### <a name="monitoring-logging-and-audit"></a>Figyelés, naplózás és naplózás

#### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Azure monitor a naplók](https://azure.microsoft.com/services/log-analytics/) egy Azure-szolgáltatás, amely segít összegyűjteni és elemezni az erőforrások által a felhőben és a helyszíni környezetekben létrehozott adatokat.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>A terv Azure Monitor naplófájljai

- SQL-elemzés
- Key Vault diagnosztika
- Application Insights-kapcsolatok
- Azure-tevékenység naplója

#### <a name="application-insights"></a>Application Insights

A [Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető Application Performance Management-(APM-) szolgáltatás, amely több platformon is használható webfejlesztőknek. Élő webalkalmazások figyelésére használatos, automatikusan észlelni fogja a teljesítménnyel kapcsolatos rendellenességeket, elemezheti a teljesítményt, diagnosztizálhatja a problémákat, és megismerheti, hogy a felhasználók hogyan használják az alkalmazást. A Application Insights olyan platformokon telepíthetők, mint a .NET, a Node. js és a Java EE, a helyszínen vagy a felhőben üzemeltetve. Emellett a DevOps folyamattal is integrálható, és eszközök széles köréhez rendelkezik kapcsolódási ponttal.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights ebben a tervben

Ez a sablon a következő Application Insights összetevőket használja:

- Application Insights irányítópult webhelyeken (operátor, ügyfél és API)

#### <a name="azure-activity-logs"></a>Azure-tevékenység naplói

Az [Azure-tevékenység naplójának](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) naplózása vezérlő – az előfizetések esetében a sík eseményei. A műveletnapló segítségével meghatározhatja a "mit, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az előfizetésében lévő erőforrásokon. Emellett megismerheti a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](../../azure-monitor/overview.md) lehetővé teszi az Azure-szolgáltatások alapszintű figyelését azáltal, hogy lehetővé teszi a metrikák, a tevékenységek naplói és a diagnosztikai naplók gyűjtését. Az Azure Monitor alapszintű infrastruktúrametrikákat és -naplókat biztosít a legtöbb Microsoft Azure-szolgáltatásról.

## <a name="threat-model"></a>Veszélyforrások modellje

Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/ukofficial-paaswa-tm) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![Pásti webalkalmazás-üzemeltetés az Egyesült Királyság hivatalos számítási feladatait érintő veszélyforrások modelljében](images/ukofficial-paaswa-threat-model.png?raw=true "Pásti webalkalmazás-üzemeltetés az Egyesült Királyság hivatalos számítási feladatait érintő veszélyforrások modelljében")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>A NCSC Cloud Security alapelvei – megfelelőségi dokumentáció

A Crown kereskedelmi szolgáltatás (egy ügynökség, amely a kormányzat kereskedelmi és beszerzési tevékenységének javítására dolgozik) megújította a Microsoft nagyvállalati szintű felhőalapú szolgáltatásainak a G-Cloud v6-ra való besorolását, amely a hivatalos szinten található összes ajánlatra kiterjed. Az Azure és a G-Cloud részletei az [Azure Egyesült királyságbeli G-Cloud Security Assessment összefoglalójában](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud)találhatók meg.

Ez a terv a NCSC [Cloud biztonsági](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) alapelvekben ismertetett 14 Felhőbeli biztonsági alapelvekhez igazodik, így biztosítva az Egyesült Királyság hivatalos feladatait támogató környezeteket.

Az [Azure Security and Compliance Blueprint – Egyesült Királyság hivatalos felhasználói felelősségi mátrixa](https://aka.ms/ukofficial-crm) (Excel-munkafüzet) a 14 Felhőbeli biztonsági alapelveket sorolja fel, és azt jelzi, hogy az egyes alapelvek (vagy alapelvek) esetében a megvalósítási elv a következő: a Microsoft, az ügyfél vagy a kettő közötti megosztás felelőssége.

A [Azure Security and Compliance Blueprint-Péter webalkalmazás az Egyesült Királyság hivatalos alapelveinek megvalósítási mátrixa](https://aka.ms/ukofficial-paaswa-pim) (Excel-munkafüzet) felsorolja a 14 Felhőbeli biztonsági alapelveket és azt, hogy az egyes alapelvek (vagy elvi részek) az ügyfél felelőssége az ügyfelek felelőssége mátrixban, 1) Ha a terv implementálja az alapelvet, és 2.) annak leírását, hogy a megvalósítás hogyan igazodik az alapvető követelmény (ek) hez.  

Emellett a Cloud Security Alliance (CSA) közzétette a Cloud Control matrixot, hogy támogassa az ügyfeleket a felhőalapú szolgáltatók kiértékelésében, valamint hogy azonosítsa azokat a kérdéseket, amelyeknek a Cloud Services szolgáltatásba való áttérés előtt válaszolniuk kell. Válaszként Microsoft Azure megválaszolta a CSA konszenzusos felmérési kezdeményezés kérdőívét ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), amely leírja, hogyan kezeli a Microsoft a javasolt alapelveket.

## <a name="third-party-assessment"></a>Harmadik féltől származó Értékelés

Ezt a tervet az Egyesült Királyság nemzeti Cyber biztonsági központja (NCSC) felülvizsgálta, és igazodik a NCSC 14 felhő biztonsági alapelveihez

Az Automation-sablonokat az Egyesült királyságbeli ügyfél-sikeres Azure Cloud Solution Architect csapata és a Microsoft partnerünk ( [Ampliphae](https://www.ampliphae.com/)) tesztelte.


## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Ez az Azure Security and Compliance Blueprint-automatizálás olyan JSON-konfigurációs fájlokból és PowerShell-parancsfájlokból áll, amelyeket a Azure Resource Manager API-szolgáltatása kezel, hogy erőforrásokat helyezzen üzembe az Azure-ban. A részletes üzembe helyezési utasítások [itt](https://aka.ms/ukofficial-paaswa-repo)érhetők el.

Három megközelítés van megadva az üzembe helyezéshez; Egy egyszerű "Expressz" [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) alkalmas a tesztkörnyezet gyors létrehozására. a paraméteres [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) megközelítés nagyobb konfigurációt biztosít a munkaterhelés-környezetekhez; valamint egy Azure Portal-alapú telepítés, amelyben az operátor megadhatja a telepítési paramétereket a Azure Portalon keresztül. 

1.  [A GitHub-](https://aka.ms/ukofficial-paaswa-repo) tárház klónozása vagy letöltése a helyi munkaállomásra.
2.  Tekintse át az [1. módszert: Azure CLI 2 (Express verzió)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) , és hajtsa végre a megadott parancsokat.
3.  Felülvizsgálati [módszer 1a: Azure CLI 2 (az üzemelő példány konfigurálása parancsfájl-argumentumokkal)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) és a megadott parancsok végrehajtása
4.  A [2. módszer áttekintése: Azure Portal telepítési folyamat](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) és a felsorolt parancsok végrehajtása

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="api-management"></a>API Management

Az [Azure API Management](https://azure.microsoft.com/services/api-management/) az api-app Service előtt használható, hogy további biztonsági rétegeket biztosítson a biztonság, a szabályozás és a vezérlők számára az API-k elérhetővé tétele érdekében.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) lehet olyan vezérlőként megvalósítani, amely lehetővé teszi a felhasználók számára, hogy regisztráljanak, identitást hozzanak létre, és engedélyezzék a nyilvános webes alkalmazás engedélyezési és hozzáférés-vezérlését.

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
- A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
- Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
- A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
- Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
- Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
