---
title: Azure Security and Compliance Blueprint IaaS-webalkalmazás az ausztráliai védelemmel
description: Azure Security and Compliance Blueprint IaaS-webalkalmazás az ausztráliai védelemmel
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 1cceecba59b4cd1a70fc6f152020757e137f4d45
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778985"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Azure Security and Compliance Blueprint IaaS-webalkalmazás az ausztráliai védelemmel

## <a name="overview"></a>Áttekintés
Ez a Azure Security and Compliance Blueprint útmutatást nyújt egy olyan infrastruktúra-(IaaS-) környezet telepítéséhez, amely alkalmas az AU-védelemmel ellátott kormányzati adatok gyűjtésére, tárolására és lekérésére, amelyek megfelelnek a következő céloknak: az ausztrál kormány információs biztonsági kézikönyve (ISM), amelyet az Australian Signals Igazgatósága (ASD) állít elő. Ez a terv egy közös hivatkozási architektúrát mutat be, és segít bemutatni a bizalmas kormányzati adatok megfelelő kezelését biztonságos, megfelelő, többrétegű környezetben.

Ez a hivatkozási architektúra, a megvalósítási útmutató és a veszélyforrás-modell egy alapot biztosít az ügyfeleknek a saját tervezési és rendszer-akkreditációs folyamataik elvégzéséhez, így az ügyfelek ASD-kompatibilis módon telepíthetik az Azure-beli számítási feladatokat. Az ügyfelek dönthetnek úgy, hogy egy Azure-VPN Gateway vagy ExpressRoute-t implementálnak az összevont szolgáltatások használatára és a helyszíni erőforrások Azure-erőforrásokkal való integrálására. Az ügyfeleknek figyelembe kell venniük a helyszíni erőforrások használatának biztonsági következményeit. A követelmények teljesítéséhez további konfigurációra van szükség, mivel ezek az ügyfelek különböző implementációinak sajátosságai alapján változhatnak.

Az ASD-Compliance megvalósítása megköveteli, hogy az Information Security által regisztrált bíráló naplózza a rendszerét. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Ez a megoldás egy IaaS webalkalmazás hivatkozási architektúráját telepíti SQL Server háttérrel. Az architektúra tartalmaz egy webes szintet, adatszintet, Active Directory infrastruktúrát, Application Gateway és Load Balancer. A webes és az adatszinten üzembe helyezett virtuális gépek egy rendelkezésre állási csoportban vannak konfigurálva, és a magas rendelkezésre állás érdekében SQL Server példányok egy always on rendelkezésre állási csoportba vannak konfigurálva. A virtuális gépek tartományhoz csatlakoznak, és Active Directory csoportházirendek segítségével kényszerítheti ki a biztonsági és megfelelőségi konfigurációkat az operációs rendszer szintjén. A felügyeleti megerősített gazdagépek biztonságos kapcsolatot biztosítanak a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez.

Az architektúra olyan biztonságos hibrid környezetet biztosíthat, amely kiterjeszti a helyszíni hálózatot az Azure-ba, lehetővé téve a webes munkaterhelések biztonságos elérését a szervezet helyi hálózatán vagy az internetről érkező vállalati felhasználók számára. A helyszíni megoldások esetében az ügyfél a biztonság, a működés és a megfelelőség minden aspektusa számára egyaránt felelős és felelős.

Az ebben a megoldásban foglalt Azure-erőforrások csatlakozni tudnak egy helyszíni hálózathoz vagy adatközpontot-létesítési létesítményhez (pl. CDC a Canberra-ban) az Azure VPN Gateway vagy a ExpressRoute használatával. A VPN használatára vonatkozó döntést a továbbított adatok és a hálózati elérési út besorolásával kell elvégezni. A nagy léptékű, kritikus fontosságú számítási feladatokat végző ügyfeleinknek big data követelményekkel rendelkező hibrid hálózati architektúrát kell fontolóra venniük a ExpressRoute használatával az Azure-szolgáltatásokhoz való magánhálózati kapcsolathoz. Az Azure kapcsolati mechanizmusaival kapcsolatos további részletekért tekintse meg az útmutató és javaslatok szakaszt.

A Azure Active Directoryekkel való összevonással lehetővé teheti a felhasználók számára a helyszíni hitelesítő adatokkal történő hitelesítést, és a felhőben lévő összes erőforrás elérését helyszíni Active Directory összevonási szolgáltatások (AD FS) infrastruktúra használatával. A Active Directory összevonási szolgáltatások (AD FS) egyszerűsített, biztonságos identitás-összevonást és webes egyszeri bejelentkezési képességeket biztosíthat ehhez a hibrid környezethez. További részletekért tekintse meg az útmutató és javaslatok szakaszt Azure Active Directory a telepítőt.

A megoldás Azure Storage-fiókokat használ, amelyekkel az ügyfelek a Storage Service Encryption használatával kezelhetik az inaktív adatok titkosságát. Az Azure a rugalmasság érdekében három példányban tárolja az adatmennyiséget az ügyfél kiválasztott régiójában. Az Azure-régiók rugalmas régiókban vannak üzembe helyezve, és a földrajzi redundáns tárolás biztosítja, hogy az adatküldés a második régióba is kerüljön három másolattal. Ez megakadályozza, hogy az ügyfél elsődleges adathelyénél negatív esemény kerüljön adatvesztés miatt.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. Azure Active Directory szerepköralapú hozzáférés-vezérléssel szabályozható a telepített erőforrásokhoz és kulcsokhoz való hozzáférés a Azure Key Vaultban. A rendszer állapotát Azure Security Center és Azure Monitor figyeli. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítéséhez és a rendszerállapot megjelenítéséhez egyetlen, könnyen navigálható irányítópulton. Az Azure Application Gateway a megelőzési módban tűzfalként van konfigurálva, és legalább 1,2-es verziójú TLS-forgalmat igényel.

![IaaS webalkalmazás az au által védett hivatkozási architektúrához](images/au-protected-iaaswa-architecture.png?raw=true "IaaS webalkalmazás az au által védett hivatkozási architektúra diagramhoz") 

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. További részletek az [üzembe helyezési architektúra](#deployment-architecture) szakaszban olvashatók.

- Rendelkezésre állási csoportok
    - (1) SQL-fürtcsomópontok
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) webalkalmazási tűzfal
        - Tűzfal mód: megelőzés
        - Szabály beállítása: OWASP 3,0
        - Figyelő portja: 443
- Azure-beli Felhőbeli tanúsító
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Monitor-naplók
- Azure Storage
- Azure Virtual Machines
    - (1) felügyelet/megerősített (Windows Server 2016 Datacenter)
    - (2) SQL Server fürtcsomópont (SQL Server 2017 a Windows Server 2016 rendszeren)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) hálózati biztonsági csoportok
    - Azure Network Watcher
- Recovery Services-tároló

Ez a terv olyan Azure-szolgáltatásokat tartalmaz, amelyek nem lettek hitelesítve az ausztrál Cyber Security Center (ASCS) által védett besorolásban való használatra. Az ebben a hivatkozási architektúrában található összes szolgáltatást a ASCS a terjesztési korlátozó jelölők (DLM) szintjén tanúsította. A Microsoft azt javasolja, hogy az ügyfelek az ezen Azure-szolgáltatásokhoz kapcsolódó közzétett biztonsági és auditálási jelentéseket tekintsék át, és használják a kockázatkezelési keretrendszert annak megállapítására, hogy az Azure-szolgáltatás alkalmas-e a belső akkreditálásra, és hogy a Védett besorolás.

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra
A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Megerősített gazdagép**: A megerősített gazdagép az egyetlen belépési pont, amely lehetővé teszi, hogy a felhasználók hozzáférhessenek az üzembe helyezett erőforrásokhoz ebben a környezetben. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat engedélyezi biztonságos listán. A távoli asztal (RDP) forgalmának engedélyezéséhez a hálózati biztonsági csoportban meg kell határozni a forgalom forrását.

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) a Azure Key Vault használatával
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuális gépek erőforrásainak felhasználását, ha nincs használatban

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: Ez a megoldás az erőforrásokat egy különálló webes alhálózattal, adatbázis-alhálózattal, Active Directory alhálózattal és egy virtuális hálózaton belüli felügyeleti alhálózattal telepíti. Az alhálózatokat az egyes alhálózatokon alkalmazott hálózati biztonsági csoportokra vonatkozó szabályok logikailag elkülönítik az alhálózatok közötti adatforgalom korlátozása érdekében, hogy csak a rendszer-és felügyeleti funkciókhoz szükségesek legyenek.

Tekintse meg a megoldással üzembe helyezett [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) konfigurációját. A szervezetek úgy konfigurálhatják a hálózati biztonsági csoportokat, hogy a fenti [dokumentáció](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) használatával útmutatóként szerkesztik a fenti fájlt.

Az alhálózatok mindegyike dedikált hálózati biztonsági csoporttal rendelkezik:
- 1 hálózati biztonsági csoport a Application Gatewayhoz (LBNSG)
- 1 hálózati biztonsági csoport a megerősített gazdagéphez (MGTNSG)
- 1 hálózati biztonsági csoport az SQL-kiszolgálók és a Felhőbeli tanúsító (SQLNSG) számára
- 1 hálózati biztonsági csoport webes rétegek számára (WEBNSG)

### <a name="data-in-transit"></a>Átvitt adatok
Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok és az összes kommunikációját. 

Az ügyfél tulajdonában lévő hálózatokból érkező védett adatok esetében az architektúra az internetet vagy a ExpressRoute használja az IPSEC-sel konfigurált VPN Gateway.

Emellett az Azure felügyeleti portálon keresztül az Azure-ba irányuló összes tranzakció a TLS 1,2-et használó HTTPS-en keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption)használ. Ez segít megvédeni és megőrizni a szervezeti biztonsági kötelezettségvállalások és a megfelelőségi követelmények támogatásával kapcsolatos, az ausztráliai kormányzati ISM-szerződésben meghatározott követelményeket.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**SQL Server**: A SQL Server példány a következő adatbázis-biztonsági mértékeket használja:
-   [SQL Server naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) nyomon követi az adatbázis eseményeit, és beírja azokat a naplókba.
-   Az [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok valós idejű titkosítását és visszafejtését hajtja végre a további információk védelme érdekében. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   A [titkosított oszlopok](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg az adatbázis-rendszeren belüli egyszerű szövegként. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- A [dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A dinamikus adatmaszkolás automatikusan képes észlelni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. Ez segít a hozzáférés csökkentésében, hogy a bizalmas adatok ne lépjék ki az adatbázist jogosulatlan hozzáférés útján. **Az ügyfelek feladata a dinamikus adatmaszkolási beállítások módosítása az adatbázis-sémájuk betartásához.**

### <a name="identity-management"></a>Identitáskezelés
Az ügyfelek a helyszíni Active Directory összevont szolgáltatásokat használhatják a összevonása a [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), amely a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. A [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) Azure Active Directoryokkal integrálja a helyszíni címtárakat. A megoldás minden felhasználójának Azure Active Directory fiókra van szüksége. Az összevonási bejelentkezéssel a felhasználók bejelentkezhetnek Azure Active Directoryba, és a helyszíni hitelesítő adatok használatával hitelesíthetők az Azure-erőforrásokkal.

Emellett a következő Azure Active Directory képességek segítenek az Azure-környezetben tárolt adathozzáférések kezelésében:
- Az alkalmazáshoz való hitelesítés Azure Active Directory használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Az [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet&#39;s identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet&#39;s identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidenseket, hogy tegye meg a megfelelő lépéseket a megoldásához.

**Azure multi-Factor Authentication**: Az identitások elleni védelem érdekében meg kell valósítani a többtényezős hitelesítést. Az [Azure multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) egy könnyen használható, méretezhető és megbízható megoldás, amely második hitelesítési módszert biztosít a felhasználók számára. Az Azure multi-Factor Authentication a felhő hatékonyságát használja, és integrálható a helyszíni Active Directory és az egyéni alkalmazásokkal. Ez a védelem nagy mennyiségű, kritikus fontosságú forgatókönyvekre terjed ki.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen jellegű adatvédelemben és hozzáférésben:

- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa egy 2048 bites RSA-kulcsot tartalmazó hardveres biztonsági modul.
- Minden felhasználó és identitás a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kap.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.
- A megoldás integrálva van Azure Key Vault a IaaS virtuális gép lemez-titkosítási kulcsainak és titkainak kezeléséhez.

**Javítási felügyelet**: Az ebben a hivatkozási architektúrában üzembe helyezett Windows-beli virtuális gépek alapértelmezés szerint úgy vannak konfigurálva, hogy Windows Update szolgáltatásból fogadják az automatikus frissítéseket. Ez a megoldás tartalmazza azt a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást is, amelyen keresztül a frissített központi telepítések létrehozhatók a virtuális gépek szükség szerinti javításához.

**Kártevők elleni védelem**: A Virtual Machines rendszerhez készült [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) olyan valós idejű védelmi képességet biztosít, amely lehetővé teszi a vírusok, kémprogramok és más kártékony szoftverek azonosítását és eltávolítását, amelyekkel konfigurálható riasztások állíthatók be, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik Telepítsen vagy futtasson védett virtuális gépeken.

**Azure Security Center**: A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center [előre meghatározott biztonsági riasztásokkal](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center [Egyéni riasztási szabályai](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.

A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos [fenyegetési intelligenciáról szóló jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.

Ez a hivatkozási architektúra emellett a sebezhetőségi [felmérést](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) is felhasználja Azure Security Centerban. A konfigurálást követően a partner ügynök (például a Qualys) biztonsági réseket küld a partner felügyeleti platformjának. A partner felügyeleti platformja pedig visszaigazolja a biztonsági réseket és az állapot-figyelési adatvédelmet Azure Security Center, így az ügyfelek gyorsan azonosíthatják a sebezhető virtuális gépeket.

**Azure Application Gateway**: Az architektúra csökkenti a biztonsági rések kockázatát egy olyan Azure-Application Gateway használatával, amely konfigurálva van egy webalkalmazási tűzfallal, és a OWASP szabályrendszert engedélyezve van. A további funkciók a következők:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) -kiszervezés engedélyezése
- [A TLS 1.0-s és 1.1-es verziójának](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) letiltása
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzési mód)
- [Megelőzési mód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) OWASP 3,0-es szabályrendszert
- [Diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) engedélyezése
- [Egyéni állapot-mintavételek](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- A [Azure Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) további védelmet és értesítéseket biztosítanak. A Azure Security Center a hírnév rendszerét is biztosítja.

### <a name="business-continuity"></a>Az üzletmenet folytonossága
**Magas rendelkezésre állás**: A megoldás minden virtuális gépet üzembe helyez egy [rendelkezésre állási csoporton](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)belül. A rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek több elkülönített hardveres fürt között legyenek elosztva a rendelkezésre állás javítása érdekében. A tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető a 99,95%-os Azure SLA-val.

**Recovery Services**tároló: Az [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) -tároló a biztonsági mentési és az Azure-Virtual Machines összes konfigurációját védi ebben az architektúrában. A Recovery Services-tárolóval az ügyfelek a teljes virtuális gép visszaállítása nélkül állíthatják vissza a fájlokat és mappákat egy IaaS virtuális gépről.

**Felhőbeli tanúsító**: [](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) A Felhőbeli tanúsító a feladatátvevő fürt Kvórumának tanúsító típusa a Windows Server 2016-ben, amely az Azure-t használja választottbírósági pontként. A felhő tanúsító, mint bármely más kvórum, szavaz, és részt vehet a kvórum számításaiban, de a szabványos nyilvánosan elérhető Azure-Blob Storage használja. Ez kiküszöböli a nyilvános felhőben üzemeltetett virtuális gépek extra karbantartási terhelését.

### <a name="logging-and-auditing"></a>Naplózás és naplózás
Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységek naplói**: A [tevékenységek naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek.

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer adattípusonként külön táblába rendezi az adatokat, ez az eredeti forrástól függetlenül lehetővé teszi az adatok együttes elemzését. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) szerepelnek:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Activity log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók Azure SQL Serverból való gyűjtésében. Az Automation [change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosíthassák a környezet változásait.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Az Azure Network Watcher eszközeivel monitorozhatja és diagnosztizálhatja az erőforrásokat egy Azure virtuális hálózaton belül, illetve megtekintheti azok metrikáit, és engedélyezheti vagy letiltja azok naplóit.  A nemzetközösségi entitásoknak Network Watcher flow-naplókat kell alkalmazniuk a NSG és a Virtual Machineshoz. Ezeket a naplókat egy dedikált Storage-fiókban kell tárolni, amely csak a biztonsági naplókat tárolja, és a Storage-fiókhoz való hozzáférést a szerepköralapú hozzáférés-vezérléssel kell biztosítani.

## <a name="threat-model"></a>Veszélyforrások modellje
Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/au-protected-iaaswa-tm) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![IaaS webalkalmazás az au által védett veszélyforrások modelljéhez](images/au-protected-iaaswa-threat-model.png?raw=true "IaaS-webalkalmazás az au által védett veszélyforrások modelljének diagramja")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
Ezt a megfelelőségi dokumentációt a Microsoft a Microsoft által elérhető platformok és szolgáltatások alapján állítja elő. Az ügyfelek központi telepítésének széles választéka miatt ez a dokumentáció egy általános megközelítést biztosít a megoldáshoz, amelyet csak az Azure-környezetben üzemeltet. Az ügyfelek a saját működési környezetük és az üzleti eredmények alapján azonosítják és használhatják az alternatív termékeket és szolgáltatásokat. A helyszíni erőforrások használatára kiválasztott ügyfeleknek a helyszíni erőforrások biztonságával és műveleteivel kell foglalkoznia. A dokumentált megoldást az ügyfelek testre szabhatók az adott helyszíni és biztonsági követelmények kielégítése érdekében.

Az [Azure Security and Compliance Blueprint – au-Protected Customer felelősségi mátrix](https://aka.ms/au-protected-crm) FELSOROLJA az au által védett összes biztonsági vezérlőt. Ez a mátrix részletesen ismerteti, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

A [Azure Security and Compliance Blueprint – au-Protected IaaS webalkalmazás-implementációs mátrix](https://aka.ms/au-protected-iaaswa-cim) olyan információkat nyújt, amelyekkel az au-védelemmel ellátott vezérlők a IaaS webalkalmazási architektúrával foglalkoznak, beleértve a részletes leírását is hogyan teljesíti a megvalósítás az egyes kezelt vezérlők követelményeit.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
Besorolt információk esetén a biztonságos IPSec VPN-alagutat úgy kell konfigurálni, hogy biztonságosan kapcsolatot létesítsen a IaaS webalkalmazás-hivatkozási architektúrájának részeként üzembe helyezett erőforrásokkal. Az IPSec VPN megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos IPSec VPN-alagút Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-beli virtuális hálózat közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül végezhető el, és lehetővé teszi az ügyfeleknek, hogy az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" adatokat használjanak. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. 

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft privát kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között, és privát hálózatnak számít. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint az interneten keresztüli szokásos kapcsolatok. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adat nem az interneten keresztül történik, ezért nem lesz elérhető.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)ra. 

Az Internet vagy az Azure ExpressRoute védelme érdekében az ügyfeleknek az alábbi beállítások alkalmazásával kell konfigurálniuk az IPSec VPN-t:

• Az ügyfél VPN-kezdeményezőjét olyan SA élettartamra kell konfigurálni, amely nem haladja meg a 14400 másodpercet.
• Az ügyfél VPN-kezdeményezője le kell tiltania a IKEv1 agresszív üzemmódját.
• Az ügyfél VPN-kezdeményezője számára a tökéletes továbbítási titoktartást kell konfigurálni.
• A Customer VPN-kezdeményezőnek konfigurálnia kell a HMAC-SHA256 vagy újabb használatát.

A VPN-eszközök és az IPSec/IKE-paraméterek [](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) konfigurációs beállításai elérhetők véleményezésre.

### <a name="azure-active-directory-setup"></a>Azure Active Directory telepítő
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. Egy meglévő Windows Server-Active Directory [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)integrálható Azure Active Directoryba.

Emellett [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) lehetővé teszi az ügyfeleknek, hogy a helyszíni [Active Directory összevonási szolgáltatások (AD FS)]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) és a Azure Active Directory használatával konfigurálja az összevonást. Az összevonási bejelentkezés lehetővé teszi, hogy a felhasználók a helyi jelszavakkal jelentkezzenek be Azure Active Directory-alapú szolgáltatásba, és ne kelljen újra megadniuk a jelszavukat a vállalati hálózaton. A Active Directory összevonási szolgáltatások (AD FS) összevonásával telepítheti Active Directory összevonási szolgáltatások (AD FS) új telepítését, vagy megadhat egy meglévő telepítést egy Windows Server 2012 R2-farmban.

Ha meg szeretné akadályozni, hogy a besorolt adatok szinkronizálva legyenek Azure Active Directoryre, az ügyfelek a következő beállítások alkalmazásával korlátozhatják a Azure Active Directory replikált attribútumokat Azure Active Directory Connect:
- [Szűrés engedélyezése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Jelszó-kivonat szinkronizálásának letiltása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
- [Jelszó visszaírási letiltása](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Eszköz visszaírási letiltása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- Hagyja meg az alapértelmezett beállításokat a [véletlen törlés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) és az [automatikus frissítés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade) megakadályozása érdekében

## <a name="disclaimer"></a>Jogi nyilatkozat
- Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
- A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
- Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
- A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
- Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
- Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
