---
title: Azure biztonsági és megfelelőségi terv – PaaS-webalkalmazás Ausztráliában védett
description: Azure biztonsági és megfelelőségi terv – PaaS-webalkalmazás Ausztráliában védett
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: c17f16ce796c9f296facd69c18de4effc7ff5258
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440981"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Azure biztonsági és megfelelőségi terv – PaaS-webalkalmazás Ausztráliában védett

## <a name="overview"></a>Áttekintés

Az Azure biztonsági és megfelelőségi terv platformszolgáltatási (PaaS) környezetként, a gyűjtemény, tárolási és kormányzati AU-védelemmel ellátott adatokat a céljainak megfelelő fiókokból való használatra alkalmas platform központi telepítésére vonatkozó útmutatást nyújt a Ausztrál kormányzati információk biztonsági manuális (ISM) az Australian Signals Directorate (ASD) által előállított. Ez a megoldás egy gyakori a referenciaarchitektúra bemutatja, és segít a kormányzati bizalmas adatok biztonságos, előírásoknak megfelelő és többrétegű környezetben megfelelő kezeléséhez bemutatásához.

Ez a referencia-architektúra, megvalósítási útmutató és veszélyforrások elleni modellje alapját vállalják, a saját tervezési és a rendszer akkreditációs eljárások, hogy az ügyfelek így az ASD-kompatibilis módon az Azure számítási feladatok üzembe helyezéséhez. Előfordulhat, hogy ügyfél dönt egy Azure VPN Gateway átjárón vagy ExpressRoute összevont szolgáltatásokhoz és helyszíni erőforrások integrálása Azure-erőforrások. Ügyfelek figyelembe kell venni a biztonsági megvalósításáról a helyszíni erőforrásokhoz. További konfigurációra szükség a követelmények teljesítéséhez, hogy milyen ügyről van minden ügyfél megvalósítása alapján változhatnak.

ASD-megfelelőség eléréséhez megköveteli, hogy egy Information Security regisztrált kockázatokat naplózza a rendszer. Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
Ez a megoldás egy referencia-architektúra biztosít egy PaaS-webalkalmazás, egy Azure SQL Database-háttérrendszerrel. A webalkalmazás elkülönített Azure App Service-környezet, amely egy Azure-adatközpontot egy privát, dedikált környezetben üzemeltetett. A környezet elosztja a forgalmat a webes alkalmazás az Azure által kezelt virtuális gépek között. Minden webes alkalmazás kapcsolat szükséges a TLS 1.2-es verziójával rendelkező. Ez az architektúra a hálózati biztonsági csoportok, egy Application Gateway, az Azure DNS-ben és a Load Balancer is tartalmaz.

Az architektúra egy biztonságos hibrid környezetben, amely kiterjeszti a helyszíni hálózatot az Azure-bA web-alapú számítási feladatok biztonságosan a vállalati felhasználók a szervezet helyi magánhálózat és az internetről elérhető, így biztosítható. A helyszíni megoldások esetén egy elszámoltathatóvá és a biztonság, a műveletek és a megfelelőség minden aspektusát felelős.

Az Azure-erőforrások a megoldásban lévő csatlakozhat egy helyszíni hálózat vagy felhőkörnyezetekből közös elhelyezési létesítményből csatlakozó (pl. a Canberra CDC) keresztül egy VPN-átjáró használatával IPSec VPN és expressroute-on keresztül. A döntés használják a VPN a besorolású, az átvitt adatok és a hálózati elérési út szem előtt kell elvégezni. Ügyfelek, amellyel nagy méretű, alapvető fontosságú számítási feladataikat a big data-követelményekhez figyelembe kell venni az ExpressRoute használatával privát hálózati kapcsolatot az Azure-szolgáltatások hibrid hálózati architektúra. Tekintse meg a [útmutatást és javaslatokat](#guidance-and-recommendations) szakasz további tájékoztatást talál a kapcsolódási mechanizmusok az Azure-bA.

Az Azure Active Directory összevonási használandó, így a felhasználók hitelesítéséhez a helyszíni hitelesítő adatok használatával, és egy helyszíni Active Directory összevonási szolgáltatások infrastruktúra használatával a felhőbeli erőforrások elérését. Active Directory összevonási szolgáltatások egyszerűsített, biztonságos identitás-összevonási és webes egyszeri bejelentkezést biztosító funkciókkal lehetővé a hibrid környezet biztosíthat. Tekintse meg a [útmutatást és javaslatokat](#guidance-and-recommendations) további részletek az Azure Active Directory beállítása a következő szakaszban.

A megoldás az Azure Storage-fiókok, amelyek a felhasználók beállíthatják az inaktív adatok bizalmas mivoltát a Storage Service Encryption segítségével. Az Azure rugalmasságot a felhasználó a kiválasztott régióban lévő adatok három példányban tárolja. Rugalmas régiópárok helyezik üzembe az Azure-régiók és földrajzi georedundáns tárolás biztosítja, hogy a második régióban három másolat, valamint az adatok replikálja. Ez megakadályozza a kedvezőtlen esemény adatvesztést eredményez az ügyfél elsődleges helyen.

A fokozott biztonság érdekében ebben a megoldásban az összes Azure-erőforrások kezelése egy erőforráscsoportot az Azure Resource Manageren keresztül. Az Azure Active Directory szerepköralapú hozzáférés-vezérlés szabályozására szolgál az üzembe helyezett erőforrások és a kulcsok Azure Key vaultban. A fájlrendszer állapotának az Azure Security Center és az Azure Monitor használatával felügyelik. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése és a fájlrendszer állapotának megjelenítése egyetlen, könnyen navigálható-irányítópulton. Az Azure Application Gateway van konfigurálva, a tűzfal megelőzés üzemmódban, és tiltja a forgalmat, amely nem a TLS 1.2-es verzió vagy újabb. A megoldás révén az Azure Application Service Environment v2-környezetet elkülöníteni a webes szint nem több-bérlős környezetben.

![PaaS Web Application AU-védelemmel ellátott referenciaarchitektúra](images/au-protected-paaswa-architecture.png?raw=true "PaaS webalkalmazás AU-védelemmel ellátott architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. További részleteket a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Application Gateway
    - Web application firewall (Webalkalmazási tűzfal)
        - Tűzfalmód: megelőzése
        - Szabálykészlet: OWASP
        - Figyelő portja: 443
- Application Insights
- Azure Active Directory
- Azure-alkalmazás Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Az Azure Monitor naplóira
- Azure Virtual Network
    - (1) /16 hálózati
    - (4) /24 hálózatok
    - Network security groups (Hálózati biztonsági csoportok)
- Network security groups (Hálózati biztonsági csoportok)
- Recovery Services-tároló
- Azure Web App

Ez a megoldás, amely a nem hitelesített szolgáltatást a védett besorolás szerint az ausztrál Kibertámadások biztonsági központ (ascs-t) Azure-szolgáltatásokat tartalmazza. A Microsoft javasolja, hogy ügyfelei, tekintse át a közzétett biztonsági és auditálási jelentéseket kapcsolódó Azure-szolgáltatásokról, és azok kockázatkezelési keretrendszert segítségével megállapítható, hogy azok belső akkreditáció és használat mellett-e az Azure Service a Védett besorolást.

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Az Azure Resource Manager**: [Az Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi az ügyfelek számára, hogy a megoldás egy csoportként erőforrásokat. Az ügyfelek központi telepítése, frissítése, vagy törölje az összes erőforrását egyetlen, koordinált műveletben lévő megoldás. Ügyfelek telepítéshez egy sablont használ, és amely különböző, például tesztelési, átmeneti és éles környezetben is képes működni. A Resource Manager biztosít a biztonsági, naplózási és címkézési szolgáltatásokat, hogy az ügyfelek az erőforrások kezelésében a telepítést követően.

**Bástyagazdagép**: A megerősített állomás bejegyzést, amely lehetővé teszi a felhasználóknak a ebben a környezetben üzembe helyezett erőforrásokhoz való hozzáférést a hibaérzékeny pont. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni, a hálózati biztonsági csoport tagja.

Ez a megoldás létrehoz egy virtuális gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban

**App Service Environment v2**: A [Azure App Service Environment-környezet](https://docs.microsoft.com/azure/app-service/environment/intro) egy App Service-funkció, amely a biztonságos futtatása nagy méretű App Service-alkalmazások teljesen elkülönített és dedikált környezetet biztosít.

App Service Environment-környezetek legyenek különítve, csak az egyetlen ügyfél előfizetéséhez alkalmazások futtatására és mindig egy virtuális hálózatban üzemelnek. Mindkét alkalmazás bejövő és kimenő hálózati forgalom részletesen szabályozhatja az ügyfél rendelkezik, és az alkalmazások nagy sebességű, biztonságos kapcsolatot létesíthet virtuális hálózatokon keresztül kapcsolódjanak a helyi vállalati erőforrásokhoz.

Ez az architektúra App Service Environment-környezetek használatának engedélyezése a következő vezérlőket/konfigurációk:

- Az izolált szolgáltatáscsomag használata App Service Environment-környezetek kell konfigurálni
    - Konfigurálja úgy a másik App Service-környezetek alkalmazások, különböző besorolások
- Egy biztonságos Azure virtuális hálózaton belüli üzemeltethet, és a hálózati biztonsági szabály
- App Service Environment egy önaláírt belső load balancer tanúsítványt a HTTPS-kommunikációra konfigurálva. Ajánlott eljárásként a Microsoft a fokozott biztonság egy megbízható hitelesítésszolgáltatótól használatát javasolja.
- [Belső terheléselosztási üzemmód](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (3 mód)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Változás [TLS-titkosítás](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Vezérlő [bejövő forgalom N írási/olvasási portok](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Webalkalmazási tűzfal – korlátozzák az adatokat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Lehetővé teszi [Azure SQL Database-forgalom](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Web App**: [Az Azure App Service](https://docs.microsoft.com/azure/app-service/) lehetővé teszi ügyfeleink számára hozhat létre és üzemeltethet webalkalmazásokat az általuk választott programozási nyelven infrastruktúra kezelése nélkül. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus üzembe helyezéseket a GitHub, az Azure DevOps Services vagy bármely egyéb Git-adattár használatával.

### <a name="virtual-network"></a>Virtual Network
Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) tartalmazza a hozzáférés-vezérlési listák, amelyek engedélyezik vagy megtagadják a forgalmat egy virtuális hálózaton belül. Hálózati biztonsági csoportok segítségével biztonságos forgalom egy alhálózatot vagy különálló virtuális gép szintjén. Létezik a következő hálózati biztonsági csoportok:
- az Application Gateway 1 hálózati biztonsági csoport
- 1 hálózati biztonsági csoportot az App Service Environment-környezet
- az Azure SQL Database 1 hálózati biztonsági csoport
- 1 hálózati biztonsági csoportot a bástyagazdagép

A hálózati biztonsági csoportok rendelkezik bizonyos portokat és protokollokat nyissa meg, hogy a megoldás működhet, biztonságos és megfelelően. Emellett a következő konfigurációk engedélyezve vannak az egyes hálózati biztonsági csoport:

  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - Az Azure Monitor naplóira csatlakozik a [hálózati biztonsági csoport diagnosztikai](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Alhálózatok**: Minden egyes alhálózathoz társítva a megfelelő hálózati biztonsági csoporttal.

**Azure DNS**: A Domain Name System, vagy a DNS-beli felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét. [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) DNS-tartományok egy üzemeltetési szolgáltatás, amely Azure-infrastruktúra névfeloldáshoz. Az Azure-ban tartományt üzemeltet, felhasználók ugyanazon hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal más Azure-szolgáltatások DNS-rekordok is kezelheti. Az Azure DNS privát DNS-tartományokat is támogatja.

**Az Azure Load Balancer**: [Az Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) lehetővé teszi az alkalmazások méretezése és magas rendelkezésre állású szolgáltatások létrehozása. Load Balancer bejövő, valamint a kimenő forgatókönyveket teszi lehetővé, és alacsony késleltetésű, nagy teljesítményű, és akár több milliónyi összes TCP és UDP-alkalmazás méretezhető.

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. 

Védett adatok továbbításához a vásárlói kezelésű hálózatok az architektúra az Azure az interneten vagy az ExpressRoute egy konfigurált IPSEC VPN-átjáróval.

Emellett minden tranzakció az Azure-bA az Azure felügyeleti portálján a TLS 1.2-es verzió használatával HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage**: Rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít a szervezeti biztonsági kötelezettségeit és megfelelőségi követelmények ausztrál kormányzati ISM által meghatározott adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az adatlemezek kötettitkosítását adja meg a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Azure SQL Database**: Az Azure SQL Database-példány a következő adatbázis biztonsági intézkedések használja:
-   [Az Active Directory-hitelesítés és engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) lehetővé teszi az identitáskezelést adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen.
-   [Az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, egy Azure storage-fiókban.
-   Az Azure SQL Database használatára van konfigurálva [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós időben titkosítja és fejti vissza az adatbázist, az azokhoz kapcsolódó biztonsági mentési, és a rest-tranzakciós naplófájlokat: adatok védelme érdekében. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Az SQL Fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy az észlelés és válasz a lehetséges veszélyforrásokra, bekövetkezésük pillanatában észlelhessék a biztonsági riasztásokat a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférés minták. Az SQL Fenyegetésészlelési riasztásokat az integrálható [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely tartalmazza a gyanús tevékenység részleteit, és a javasolt művelet vizsgálata, valamint a fenyegetés.
-   [Mindig titkosított oszlopokat](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok maszkolása az adatokat nem kiemelt jogosultságú felhasználók vagy alkalmazások által. Dinamikus adatmaszkolás automatikusan potenciálisan bizalmas adatok felderítéséhez, és javasolja a alkalmazni lehessen a megfelelő maszkokkal. Ez csökkenti a hozzáférés úgy, hogy a bizalmas adatok nem létezik az adatbázis jogosulatlan hozzáférést az nyújtanak segítséget. Ügyfelek módosítania kell a dinamikus adatmaszkolási beállítások be kell tartaniuk az adatbázissémát.

### <a name="identity-management"></a>Identitáskezelés
Ügyfelek módosulását használják a helyszíni Active Directory összevonási szolgáltatásokban összevonni kívánt [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), azaz a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. [Az Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integrálható a helyszíni címtárakat az Azure Active Directoryval. Ebben a megoldásban lévő összes felhasználó számára szükséges Azure Active Directory-fiókok, beleértve az Azure SQL Database hozzáférő felhasználók. Az összevonási jelentkezzen be a felhasználók jelentkezzen be az Azure Active Directory és az a helyszíni hitelesítő adatok használatával az Azure erőforrásokban való hitelesítéshez.

Továbbá a következő Azure Active Directory-képességekkel segíti az Azure-beli adatokhoz való hozzáférés kezelése:
- Az alkalmazás-hitelesítést az Azure Active Directory használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis oszloptitkosítás hitelesítheti az alkalmazást az Azure SQL Database, az Azure Active Directory. További információkért lásd: hogyan [bizalmas adatok védelme az Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a felhasználóknak frissíteniük kell a munkája elvégzéséhez csak olyan mértékű hozzáférést biztosítson a minden részletre kiterjedő hozzáférési engedélyek megadása. Ahelyett, hogy minden felhasználó ugyanolyan korlátlan jogosultságot ad az Azure-erőforrások, a rendszergazdák engedélyezhetik csak bizonyos adatok eléréséhez szükséges műveleteket. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, akik hozzáférhetnek bizonyos adatokat a. Az Azure Active Directory Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket és folytat gyanús incidensek a megfelelő műveletet a problémák megoldásához.

**Az Azure multi-factor Authentication**: Identitások védelme érdekében a multi-factor authentication kell végrehajtani. [Az Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) könnyen használható, méretezhető és megbízható megoldás, amely egy második védelme érdekében a felhasználók hitelesítési módszert kínál. Az Azure multi-factor Authentication a felhő hatékonyságát és integrálható a helyszíni Active Directory és az egyéni alkalmazások. Ez a védelem ki van bővítve, nagy teljesítményű, üzleti szempontból alapvető fontosságú feladatokhoz.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az adatok védelme ügyfelek:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulok által védett összes kulcsok a Key Vaultban. A kulcs típusa a hardveres biztonsági modullal védett 2048-bites RSA-kulcsot.
- Minden felhasználó és identitások kapnak a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.

**Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. Az Azure Security Center ezenkívül meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében fér hozzá.

Az Azure Security Center észlelési képességek széles használatával ügyfeleket a környezetük célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenységek. [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) az Azure Security Center lehetővé teszi ügyfeleink számára a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat definiálhat.

Az Azure Security Center itt rangsorolt biztonsági riasztások és incidensek, így egyszerűbb a vásárlók megtalálhatják és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés incidensmegoldási csapat segítség vizsgálatában és védekezhet a fenyegetések.

**Az Application Gateway**: Az architektúra csökkenti a biztonsági rések használatával az Application Gateway webalkalmazási tűzfal, és az OWASP szabálykészletben engedélyezve kockázatát. További képességek:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL-alapú Kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzés üzemmód)
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételei](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure Storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek.

**Az Azure Monitor naplóira**: Ezeket a naplókat a rendszer összevont [naplózza az Azure Monitor](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Az adatgyűjtés után a rendszer adattípusonként külön táblába rendezi az adatokat, ez az eredeti forrástól függetlenül lehetővé teszi az adatok együttes elemzését. Az Azure Security Center ezenkívül lehetővé teszi az ügyfelek számára, hogy a biztonsági események adatainak eléréséhez, és adatokat más szolgáltatásokból való összevonásához Kusto-lekérdezések használata az Azure Monitor naplóira integrálható.

A következő Azure [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Az Active Directory állapotának ellenőrzése megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics megoldás az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti.

**Az Azure Automation**: [Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban lévő runbookok segítségével naplók gyűjtése az Azure SQL Database-ből. Az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**: [Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és azonosíthatja a trendeket lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, beleértve a saját Azure-erőforrások API-hívások nyomon követése.

Az Azure Network Watcher: [Az Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) eszközeivel monitorozása, diagnosztizálása, megtekintheti a metrikákat, és engedélyezheti vagy tilthatja le a naplókat a további erőforrások az Azure-beli virtuális hálózathoz.  Közösségének entitások meg kell valósítania a Network Watcher flow-naplók az NSG-ket és virtuális gépekhez. Ezek a naplók, amely csak a biztonsági naplók vannak tárolva, és a tárfiókhoz való hozzáférést kell biztosítani, a szerepkör alapú hozzáférés-vezérlés dedikált tárfiókban kell tárolni.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/au-protected-paaswa-tm) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![PaaS-webalkalmazás AU-PROTECTED Threat modell](images/au-protected-paaswa-threat-model.png?raw=true "PaaS webes alkalmazás a veszélyforrások elleni AU-védelemmel ellátott modellek diagramja")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja
Ez a megfelelőségi dokumentáció alapján platformon és elérhető a Microsoft-szolgáltatásokhoz a Microsoft által előállított. A számos különböző rendszereit, mert ez a dokumentáció általánosított módszert biztosít a csak az Azure-környezetben üzemeltetett megoldás. Ügyfelek előfordulhat, hogy azonosítsa és alternatív termékek és-szolgáltatások a saját környezetek és az üzleti eredmények működési alapján. Ügyfeleket a helyszíni erőforrások kiválasztása a biztonsági és a helyszíni erőforrások műveletek kell oldania. A dokumentált megoldás testre szabható által az ügyfelek számára az adott helyszíni és a biztonsági követelményeinek.

A [Azure biztonsági és megfelelőségi terv - AU-PROTECTED vevő felelőssége mátrix](https://aka.ms/au-protected-crm) sorolja fel az AU-Prot. által igényelt összes biztonsági vezérlő Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és -megfelelőségi terv - AU-PROTECTED PaaS webes kérelem végrehajtása mátrix](https://aka.ms/au-protected-paaswa-cim) nyújt információt, amelyre a vezérlők AU-védelemmel ellátott szolgálhatók ki a PaaS webalkalmazás-architektúra, többek között Hogyan végrehajtása megfelel egyes az érintett vezérlő részletes leírása.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute

A bizalmas adatok biztonságos IPSec VPN-alagutat kell kell konfigurálni, hogy biztonságos kapcsolatot az IaaS webalkalmazás referenciaarchitektúrája részeként üzembe helyezett erőforrásokkal. Megfelelően állítja be az IPSec virtuális Magánhálózaton, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat az átvitel során.

Az Azure-ral biztonságos IPSec VPN-alagút implementálásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ez a kapcsolat végrehajthatók lesznek az interneten keresztül, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. 

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft egy privát kapcsolat lehetőséget kínál. Az Azure ExpressRoute dedikált Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat, és egy magánhálózaton számít. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a ezeket a kapcsolatokat az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak és biztonságosabbak kisebb a késésük. Továbbá mivel ez az ügyfél távközlési szolgáltató közvetlen kapcsolatot, az adatok nem az interneten keresztül továbbítani tartalmaz, és ezért nem érhető el.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Osztályozott adatainak védelmében, hogy az internetről vagy Azure ExpressRoute használatával, ügyfelek úgy, hogy alkalmazza a következő beállításokat kell konfigurálnia az IPSec VPN:

• Az ügyfél VPN-kezdeményező nem 14400 másodpercnél nagyobb SA élettartama kell konfigurálni.
• Az ügyfél VPN-kezdeményező le kell tiltania IKEv1 agresszív.
• Az ügyfél VPN-kezdeményező teljes továbbítási titkosítása kell konfigurálnia.
• Az ügyfél VPN kezdeményező HMAC-SHA256 algoritmust, vagy nagyobb használatát kell beállítania.

Beállítási lehetőségei a VPN-eszközök és IPSec / IKE-paraméterek [elérhető](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) felülvizsgálatra.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen a központi telepítés kezelése és kiépítése a környezet implementálására csoporthoz való hozzáférést. Egy meglévő Windows Server Active Directory integrálhatók az Azure Active Directory- [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Ezenkívül [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) lehetővé teszi a felhasználóknak való összevonást a helyszíni [Active Directory összevonási szolgáltatások]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) és az Azure Active Directory. Az összevonási jelentkezzen be ügyfelei engedélyezhetik a felhasználók számára, hogy jelentkezzen be az Azure Active Directory-alapú szolgáltatásokat a helyszíni jelszavukat, és ne kelljen újra adja meg a jelszavukat a vállalati hálózaton lévő. Az összevonási lehetőség az Active Directory összevonási szolgáltatások használatával, telepíthet egy új Active Directory összevonási szolgáltatások telepítését, vagy megadhat egy meglévő telepítését egy Windows Server 2012 R2-farmban.

Megakadályozni adatok szinkronizálása az Azure Active Directoryhoz, ügyfelek korlátozhatja az Azure Active Directory Connect között az alábbi beállítások alkalmazásával lesznek replikálva, az Azure Active Directory attribútumok:

- [Szűrés engedélyezése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [A Jelszókivonat-szinkronizálás letiltása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Jelszóvisszaíró letiltása](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Eszközvisszaírás letiltása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Hagyja bejelölve az alapértelmezett beállításokat az [véletlen törlések megakadályozása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) és [automatikus frissítés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
 - Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
 - Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
 - Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
