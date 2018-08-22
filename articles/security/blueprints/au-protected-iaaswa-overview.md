---
title: Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás Ausztráliában védett
description: Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás Ausztráliában védett
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/16/2018
ms.author: meladie
ms.openlocfilehash: 2179398cdd79db99540ce219b8f4eae24f0eca39
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246135"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás védett Ausztráliában

## <a name="overview"></a>Áttekintés

Az Azure biztonsági és megfelelőségi terv szolgáltatás (IaaS) környezetként, a gyűjtemény, tárolási és kormányzati AU-védelemmel ellátott adatokat a céljainak megfelelő fiókokból való használatra alkalmas, amely az infrastruktúra központi telepítésére vonatkozó útmutatást nyújt. az ausztrál kormányzati információk biztonsági manuális (ISM) az Australian Signals Directorate (ASD) által előállított. Ez a megoldás egy gyakori a referenciaarchitektúra bemutatja, és segít a kormányzati bizalmas adatok biztonságos, előírásoknak megfelelő és többrétegű környezetben megfelelő kezeléséhez bemutatásához.

Ez a referencia-architektúra, megvalósítási útmutató és veszélyforrások elleni modellje alapját vállalják, a saját tervezési és a rendszer akkreditációs eljárások, hogy az ügyfelek így az ASD-kompatibilis módon az Azure számítási feladatok üzembe helyezéséhez. Előfordulhat, hogy ügyfél dönt egy Azure VPN Gateway átjárón vagy ExpressRoute összevont szolgáltatásokhoz és helyszíni erőforrások integrálása Azure-erőforrások. Ügyfelek figyelembe kell venni a biztonsági megvalósításáról a helyszíni erőforrásokhoz. További konfigurációra szükség a követelmények teljesítéséhez, hogy milyen ügyről van minden ügyfél megvalósítása alapján változhatnak.

ASD-megfelelőség eléréséhez megköveteli, hogy egy Information Security regisztrált kockázatokat naplózza a rendszer. Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
Ez a megoldás az SQL Server-háttérrendszerét az IaaS-webalkalmazás referenciaarchitektúra üzembe helyezi. Az architektúra egy webes réteg, adatrétegbeli, Active Directory infrastruktúrával, az Application Gateway és terheléselosztó tartalmaz. A webes és az adatrétegekhez az üzembe helyezett virtuális gépek rendelkezésre állási csoportban vannak konfigurálva, és az SQL Server-példányok úgy vannak konfigurálva, egy Always On rendelkezésre állási csoportban a magas rendelkezésre állás érdekében. Virtuális gépek tartományhoz csatlakoztatva, és az Active Directory-csoportházirendek segítségével érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén. Felügyeleti bástyagazdagép biztonságos kapcsolatot biztosít a rendszergazdák számára a hozzáférés üzembe helyezett erőforrásokat.

Az architektúra egy biztonságos hibrid környezetben, amely kiterjeszti a helyszíni hálózatot az Azure-bA web-alapú számítási feladatok biztonságosan a vállalati felhasználók a szervezet helyi magánhálózat és az internetről elérhető, így biztosítható. A helyszíni megoldások esetén egy elszámoltathatóvá és a biztonság, a műveletek és a megfelelőség biztonságáért felelős.

A megoldásban lévő Azure-erőforrások egy helyszíni hálózat vagy felhőkörnyezetekből közös elhelyezési létesítményből csatlakozó (pl. a Canberra CDC) használatával az Azure VPN Gateway IPSec VPN-kapcsolaton keresztül, vagy expressroute-on keresztül kapcsolódhat... A döntés használják a VPN a besorolású, az átvitt adatok és a hálózati elérési út szem előtt kell elvégezni. Ügyfelek, amellyel nagy méretű, alapvető fontosságú számítási feladataikat a big data-követelményekhez figyelembe kell venni az ExpressRoute használatával privát hálózati kapcsolatot az Azure-szolgáltatások hibrid hálózati architektúra. Talál útmutatást és javaslatokat tartalmaz további tájékoztatást talál a kapcsolódási mechanizmusok az Azure-bA.

Az Azure Active Directory összevonási használandó, így a felhasználók hitelesítéséhez a helyszíni hitelesítő adatok használatával, és egy helyszíni Active Directory összevonási szolgáltatások infrastruktúra használatával a felhőbeli erőforrások elérését. Active Directory összevonási szolgáltatások egyszerűsített, biztonságos identitás-összevonási és webes egyszeri bejelentkezést biztosító funkciókkal lehetővé a hibrid környezet biztosíthat. Tekintse meg a további részletek az Azure Active Directory beállítása útmutatás és javaslatok szakaszban.

A megoldás az Azure Storage-fiókok, amelyek a felhasználók beállíthatják az inaktív adatok bizalmas mivoltát a Storage Service Encryption segítségével. Az Azure rugalmasságot a felhasználó a kiválasztott régióban lévő adatok három példányban tárolja. Rugalmas régiópárok helyezik üzembe az Azure-régiók és földrajzi georedundáns tárolás biztosítja, hogy a második régióban három másolat, valamint az adatok replikálja. Ez megakadályozza a kedvezőtlen esemény adatvesztést eredményez az ügyfél elsődleges helyen.

A fokozott biztonság érdekében ebben a megoldásban az összes erőforrás egy erőforráscsoportba tartozó Azure Resource Manageren keresztül felügyelt. Az Azure Active Directory szerepköralapú hozzáférés-vezérlés szabályozására szolgál az üzembe helyezett erőforrások és a kulcsok Azure Key vaultban. A fájlrendszer állapotának az Azure Security Center és az Azure Monitor használatával felügyelik. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése és a fájlrendszer állapotának megjelenítése egyetlen, könnyen navigálható-irányítópulton. Az Azure Application Gateway van konfigurálva, a tűzfal megelőzés üzemmódban, és a TLS 1.2-es verziójával forgalmat igényel.

![IaaS-webalkalmazás AU-védelemmel ellátott referenciaarchitektúra](images/au-protected-iaaswa-architecture.png?raw=true "IaaS webalkalmazás AU-védelemmel ellátott architekturális diagramja") 

Ez a megoldás a következő Azure-szolgáltatásokat használ. További részleteket a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Rendelkezésre állási csoportok
    - (1) az SQL-fürtcsomópont
    - (1) webkiszolgáló és IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) a webalkalmazási tűzfal
        - Tűzfalmód: megelőzése
        - Set-szabály: OWASP 3.0
        - Figyelő portja: 443
- Az Azure Felhőbeli tanúsító
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Log Analytics
- Azure Storage
- Azure-alapú virtuális gépek
    - (1) felügyeleti/megerősített (Windows Server 2016 Datacenter)
    - (2) az SQL Server-fürt csomópontjának (Windows Server 2016-on futó SQL Server 2017)
    - (2) webkiszolgáló és IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) a hálózati biztonsági csoportok
    - Azure Network Watcher
- Recovery Services-tároló

Ez a megoldás, amely a nem hitelesített szolgáltatást a védett besorolás szerint az ausztrál Kibertámadások biztonsági központ (ascs-t) Azure-szolgáltatásokat tartalmazza. Ez a referenciaarchitektúra szereplő összes szolgáltatás igazolta ascs-t, a terjesztést korlátozó feltételek korlátozása jelölők (DLM) szintjén. A Microsoft javasolja, hogy ügyfelei, tekintse át a közzétett biztonsági és auditálási jelentéseket kapcsolódó Azure-szolgáltatásokról, és azok kockázatkezelési keretrendszert segítségével megállapítható, hogy azok belső akkreditáció és használat mellett-e az Azure Service a Védett besorolást.

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely lehetővé teszi a felhasználók hozzáférhessenek a környezetben telepített erőforrás. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni, a hálózati biztonsági csoport tagja.

Ez a megoldás létrehoz egy virtuális gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak

### <a name="virtual-network"></a>Virtuális hálózat

Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: Ez a megoldás üzembe helyez egy másik alhálózatot, adatbázis-alhálózat, az Active Directory-alhálózathoz és felügyeleti alhálózaton egy virtuális gépen belüli – rendelkező erőforrásokat. Alhálózatok logikailag elválasztott korlátozhatja, hogy csak szükséges rendszer- és felügyeleti funkciók, az alhálózatok közötti adatforgalom az egyes alhálózatokra alkalmazott hálózati biztonsági csoport szabályait.

Tekintse meg a konfiguráció a [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) üzembe helyezett ezzel a megoldással. Szervezetek számára a fent fájl szerkesztésével állíthatja a hálózati biztonsági csoportok [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) alapján.

Dedikált hálózati biztonsági csoport minden egyes alhálózatban van:
- 1 hálózati biztonsági csoport az Application Gateway (LBNSG)
- 1 hálózati biztonsági csoportot a bástyagazdagép (MGTNSG)
- 1 hálózati biztonsági csoportot az SQL Server-kiszolgálók és a Felhőbeli tanúsító (SQLNSG)
- 1 hálózati biztonsági csoportot a webes réteg (WEBNSG)

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure alapértelmezés szerint minden kommunikáció és az Azure datacentres titkosítja. 

Védett adatok továbbításához a vásárlói kezelésű hálózatok az architektúra egy VPN-átjárót konfigurált, IPSec-et az interneten vagy az ExpressRoute használ.

Emellett minden tranzakció az Azure-bA az Azure felügyeleti portálján keresztül felhasználásával a TLS 1.2-es HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra a titkosítás, az adatbázis naplózási és más intézkedéseket az inaktív adatok védi.

**Az Azure Storage**: rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít a szervezeti biztonsági kötelezettségeit és megfelelőségi követelmények az ausztrál kormány ISM által meghatározott adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: [az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az adatlemezek kötettitkosítását adja meg a Windows BitLocker funkcióját használja. A megoldás integrálható az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az SQL Server**: az SQL Server-példányt használja a következő adatbázis biztonsági intézkedéseket:

-   [Az SQL Server-naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) nyomon követi az adatbázisok eseményeit, és írja őket az auditnaplók.
-   [Transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) hajtja végre a valós idejű titkosításához és visszafejtéséhez az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra adatok inaktív védelme érdekében. Transzparens adattitkosítás biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák elérését, adatbázis-kiszolgálók, amíg a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [Titkosított oszlopokat](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Adatok titkosításának engedélyezése után csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók hozzáférést a kulcsokhoz hozzáférhet egyszerű szöveges adatokat.
- [Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) korlátozza a bizalmas adatok maszkolása az adatokat nem kiemelt jogosultságú felhasználók vagy alkalmazások által. Dinamikus adatmaszkolás automatikusan potenciálisan bizalmas adatok felderítéséhez, és javasolja a alkalmazni lehessen a megfelelő maszkokkal. Ez csökkenti a hozzáférés úgy, hogy a bizalmas adatok nem létezik az adatbázis jogosulatlan hozzáférést az nyújtanak segítséget. **Dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma módosításával ügyfelek felelőssége.**

### <a name="identity-management"></a>Identitáskezelés

Ügyfelek módosulását használják a helyszíni Active Directory összevonási szolgáltatásokban összevonni kívánt [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), azaz a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. [Az Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integrálható a helyszíni címtárakat az Azure Active Directoryval. Ebben a megoldásban lévő összes felhasználó számára az Azure Active Directory-fiókok szükséges. Az összevonási jelentkezzen be a felhasználók jelentkezzen be az Azure Active Directory és az a helyszíni hitelesítő adatok használatával az Azure erőforrásokban való hitelesítéshez.

Továbbá a következő Azure Active Directory-képességekkel segíti az Azure-beli adatokhoz való hozzáférés kezelése:
- Az alkalmazás-hitelesítést az Azure Active Directory használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a felhasználóknak frissíteniük kell a munkája elvégzéséhez csak olyan mértékű hozzáférést biztosítson a minden részletre kiterjedő hozzáférési engedélyek megadása. Ahelyett, hogy minden felhasználó ugyanolyan korlátlan jogosultságot ad az Azure-erőforrások, a rendszergazdák engedélyezhetik csak bizonyos adatok eléréséhez szükséges műveleteket. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, akik hozzáférhetnek bizonyos adatokat a. Az Azure Active Directory Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet érintő lehetséges biztonsági résekről&#39;s identitásokat, konfigurálja az automatikus válaszok egy szervezet kapcsolódó észlelt gyanús tevékenységek&#39;s identitások , és megvizsgálja a gyanús események a problémák megoldásához a megfelelő műveletet.

**Az Azure multi-factor Authentication**: identitások védelme érdekében meg kell valósítani a multi-factor authentication szolgáltatás. [Az Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) könnyen használható, méretezhető és megbízható megoldás, amely egy második védelme érdekében a felhasználók hitelesítési módszert kínál. Az Azure multi-factor Authentication a felhő hatékonyságát és integrálható a helyszíni Active Directory és az egyéni alkalmazások. Ez a védelem ki van bővítve, nagy teljesítményű, üzleti szempontból alapvető fontosságú feladatokhoz.

### <a name="security"></a>Biztonság

**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az ügyfelek védelme és az ilyen adatok elérése:

- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulok által védett összes kulcsok a Key Vaultban. A kulcs típusa a hardveres biztonsági modullal védett 2048-bites RSA-kulcsot.
- Minden felhasználó és identitások kapnak a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.
- A megoldás integrálva van az Azure Key Vault IaaS virtuális gépek lemeztitkosítási kulcsokat és titkos kulcsok kezeléséhez.

**Javítások kezelése**: Ez a referenciaarchitektúra részeként üzembe helyezett Windows virtuális gépek úgy vannak konfigurálva, az automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint. Ez a megoldás is magában foglalja a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen frissített telepítések hozható létre a javítás virtuális gépekhez szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára biztosítja a valós idejű védelem funkció, amely alapján azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható riasztásokkal Ha az ismert kártevő vagy nemkívánatos szoftverek megpróbálják telepíteni vagy futtatni védett virtuális gépeken.

**Az Azure Security Center**: A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), ügyfelek is központilag alkalmazása és a számítási feladatok biztonsági házirendek kezelése, korlátozhatja a fenyegetéseknek való kitettséget, felismeri és elháríthatja a támadásokat. Az Azure Security Center ezenkívül meglévő konfigurációk az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét és adatok védelme érdekében fér hozzá.

Az Azure Security Center észlelési képességek széles használatával ügyfeleket a környezetük célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center készletével rendelkezik [biztonsági riasztások az előre meghatározott](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), amely vannak aktiválódik, ha a fenyegetések vagy gyanús tevékenységek. [Egyéni riasztási szabályok](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) az Azure Security Center lehetővé teszi ügyfeleink számára a környezetből már begyűjtött adatok alapján új biztonsági riasztásokat definiálhat.

Az Azure Security Center itt rangsorolt biztonsági riasztások és incidensek, így egyszerűbb a vásárlók megtalálhatják és a potenciális biztonsági problémákat. A [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre a rendszer minden egyes észlelt fenyegetés incidensmegoldási csapat segítség vizsgálatában és védekezhet a fenyegetések.

Ezenkívül ez a referenciaarchitektúra használja a [biztonságirés-értékelési](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) az Azure Security Centerben. Konfigurálása után a partnerügynök (pl. Qualys) jelenti a biztonsági adatok a partner felügyeleti platformjára. Ugyanakkor a partner felügyeleti platformjára biztosít biztonsági résekre vonatkozó és állapotmonitorozási adatokat vissza az Azure Security Center lehetővé teszi az ügyfelek számára, hogy gyorsan azonosíthatja a sebezhető virtuális gépeket.

**Az Azure Application Gateway**: az architektúra csökkenti a biztonsági rések használatával az Azure Application Gateway webalkalmazási tűzfal konfigurálása, és az OWASP szabálykészletben engedélyezve kockázatát. További képességek:

- [Vége a közötti SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL-alapú Kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (megelőzés üzemmód)
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételei](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: A megoldás összes virtuális gépet helyez üzembe egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek rendelkezésre állás javítása érdekében több elkülönített hardverfürt között legyenek elosztva. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. Recovery Services-tárolóval ügyfelek is helyreállíthat fájlokat és mappákat IaaS virtuális gépről a teljes virtuális gép visszaállítása nélkül engedélyezése a gyorsabb helyreállítást.

**Felhőbeli tanúsító**: [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) egy feladatátvevő fürt kvórum tanúsítójának a Windows Server 2016, amely Azure-t az egyeztetési pontként típusú. A Felhőbeli tanúsító – más kvórumtanúsítókhoz, például szavazattal rendelkezik, és részt vehet a kvórumszámításokban, de a standard szintű, nyilvánosan elérhető Azure Blob Storage használ. Ezzel elkerülhető a felesleges karbantartást a nyilvános felhőben üzemeltetett virtuális gépek.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

Azure-szolgáltatások széles körben system és a felhasználói tevékenység, valamint a rendszerállapot jelentkezzen be:
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure Storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat. Az összes diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek.

**Log Analytics**: ezeket a naplókat a rendszer összevont [Log Analytics](https://azure.microsoft.com/services/log-analytics/) feldolgozási, tárolására és-irányítópult jelentéseit. Követően az adatok felépítéséről különböző adattípusokhoz, amely elemzett összes adat össze az eredeti forrástól függetlenül lehetővé teszi külön táblába. Ezen túlmenően az Azure Security Center integrálható a Log Analytics ami lehetővé teszi az ügyfelek számára a Log Analytics-lekérdezések használata a biztonsági események adatainak eléréséhez és más szolgáltatások származó adatokat kombinálni.

A következő Log Analytics [felügyeleti megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Ez az architektúra egy része szerepel:
-   [Az Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapot-ellenőrzés megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját tartalmazza.
- [SQL-értékeléssel](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): az SQL Health Check megoldás a kockázat és kiszolgálói környezetek állapotát értékeli a rendszeres időközönkénti, és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok rangsorolt listáját.
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az Agent Health megoldás hány ügynök van telepítve, és a földrajzi elosztás, valamint hány ügynök, amely nem válaszol és működési adatokat küld be, amely az ügynökök számát jelenti.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): az ügyfél az összes Azure-előfizetések az Azure-Tevékenységnaplók elemzésének segíti az Activity Log Analytics megoldás.

**Az Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, fut, és kezeli a runbookok. Ebben a megoldásban a runbookok naplók gyűjtése az Azure SQL Server segítségével. Az Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek számára, hogy könnyen azonosíthassa a változtatásokat a környezetben.

**Az Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény, biztonság fenntartására és a trendek azonosítására által lehetővé teszi a cégeknek naplózása, riasztásokat hozhat létre, és archiválja az adatokat, többek között API-hívások nyomon követése az Azure-ban az erőforrásokat.

[Az Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Azure Network Watcherrel figyelheti, diagnosztizálhatja, megtekintheti a metrikákat, és engedélyezheti vagy tilthatja le a naplókat a további erőforrások az Azure-beli virtuális hálózathoz eszközöket biztosít.  Közösségének entitások meg kell valósítania a Network Watcher flow-naplók az NSG-ket és virtuális gépekhez. Ezek a naplók, amely csak a biztonsági naplók vannak tárolva, és a tárfiókhoz való hozzáférést kell biztosítani, a szerepkör alapú hozzáférés-vezérlés dedikált tárfiókban kell tárolni.

## <a name="threat-model"></a>Fenyegetések modellezése

A referenciaarchitektúra az adatfolyam-diagram érhető el az [letöltése](https://aka.ms/au-protected-iaaswa-tm) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![IaaS-webalkalmazás AU-védelemmel ellátott Threat modell](images/au-protected-iaaswa-threat-model.png?raw=true "IaaS webalkalmazás AU-védelemmel ellátott Threat modellek diagramja")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja

Ez a megfelelőségi dokumentáció alapján platformon és elérhető a Microsoft-szolgáltatásokhoz a Microsoft által előállított. A számos különböző rendszereit, mert ez a dokumentáció általánosított módszert biztosít a csak az Azure-környezetben üzemeltetett megoldás. Ügyfelek előfordulhat, hogy azonosítsa és alternatív termékek és-szolgáltatások a saját környezetek és az üzleti eredmények működési alapján. Ügyfeleket a helyszíni erőforrások kiválasztása a biztonsági és a helyszíni erőforrások műveletek kell oldania. A dokumentált megoldás testre szabható által az ügyfelek számára az adott helyszíni és a biztonsági követelményeinek.

A [Azure biztonsági és megfelelőségi terv – AU-PROTECTED vevő felelőssége mátrix](https://aka.ms/au-protected-crm) AU-védelemmel ellátott által igényelt összes biztonsági vezérlő sorolja fel. Ez a mátrix részletezi a Microsoft, az ügyfél felelőssége végrehajtására az egyes vezérlőelemek-e, vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi terv – AU-PROTECTED IaaS webes kérelem végrehajtása mátrix](https://aka.ms/au-protected-iaaswa-cim) nyújt információt, amelyre a vezérlők AU-védelemmel ellátott szolgálhatók ki az IaaS webalkalmazás-architektúra, többek között Hogyan végrehajtása megfelel egyes az érintett vezérlő részletes leírása.

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
- [Jelszóvisszaíró letiltása](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Eszközvisszaírás letiltása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- Hagyja bejelölve az alapértelmezett beállításokat az [véletlen törlések megakadályozása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) és [automatikus frissítés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.
- Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.
- Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.
- Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
- Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
