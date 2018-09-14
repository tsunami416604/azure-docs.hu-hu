---
title: Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás GDPR
description: Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás GDPR
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: eefbf4686e5bdc9e53f063221104a6af9817fb50
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576315"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Azure biztonsági és megfelelőségi terv – IaaS-webalkalmazás GDPR

## <a name="overview"></a>Áttekintés
Az általános adatvédelmi rendelet (GDPR) tartalmazza az információk gyűjtése, tárolása és személyes adatokat, beleértve a hogyan szervezetek azonosítása és a személyes adatok védelme, átláthatóság követelmények kezelésére, észlelése és jelentés segítségével számos követelmények személyes adatok illetéktelen behatolásokat, és train adatvédelmi személyzet és a többi alkalmazott. Az általános adatvédelmi rendelet biztosít az egyének személyes adataik nagyobb mértékben vezérelheti, és számos új kötelezettségeket szervezeteknek, amelyek gyűjtése, kezelni vagy személyes adatok elemzése ró. Az általános adatvédelmi rendelet új szabályokat áruk kínáló szervezetek számára ír elő, és személyek az Európai Unió (EU), vagy ha a szolgáltatás adatokat gyűjthet és elemezhet EU-polgárokkal kapcsolatos vannak kötve. Az általános adatvédelmi rendelet vonatkozik, függetlenül attól, ahol a szervezet megtalálható.

A Microsoft Azure tervezett az iparágvezető biztonsági és adatvédelmi módszerek kidolgozásában adataihoz a felhőben, beleértve a kategóriákat, az általános adatvédelmi rendelet által azonosított a személyes adatok védelme érdekében. A Microsoft [szerződési feltételek](http://aka.ms/Online-Services-Terms) Microsoft véglegesítenie kell a processzorok követelményeinek.

Az Azure biztonsági és megfelelőségi terv-infrastruktúra telepítése egy szolgáltatás (IaaS) megfelelő az internetre irányuló egyszerű webalkalmazások környezetként útmutatást nyújt. Ez a megoldás, amelyben ügyfeleink megfelel a GDPR meghatározott biztonsági és megfelelőségi követelményeinek módszereket mutatja be, és az ügyfelek létrehozása és konfigurálása saját IaaS webes alkalmazás megoldások az Azure-ban alapjaként szolgál. Ügyfelek Ez a referenciaarchitektúra használatához, és kövesse a Microsoft [négy lépésből álló folyamat](https://aka.ms/gdprebook) az adatvédelmi rendeletnek való megfelelőség útjuk:
1. Észlelés: Azonosítsa, mely személyes adatok állnak rendelkezésre, és helyét.
2. Kezelése: Hogyan személyes adatok szabályozása használt és elérhető.
3. Védelme: Biztonsági vezérlők megelőzését, észlelését és válaszadás a biztonsági rések és az adatok hoz létre.
4. Jelentés: Szükséges dokumentáció és kérelmek kezeléséhez, és illetéktelen behatolás értesítések.

Ez a referenciaarchitektúra társított megvalósítási útmutató és fenyegetések modellezése célja, hogy az ügyfelek számára az adott követelményekhez a felmerülő ismeretekkel szolgál, és nem használható-éles környezetben van. Vegye figyelembe a következőket:
- Az architektúra egy alapkonfiguráció segítenek az ügyfeleknek a számítási feladatok üzembe helyezéséhez az Azure-bA a GDPR-kompatibilis módon biztosít.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelést bármely megoldás használatával létrehozott ebben az architektúrában követelmények eltérőek lehetnek, hogy milyen ügyről van minden ügyfél megvalósítása alapján.

## <a name="architecture-diagram-and-components"></a>Architektúra és összetevők
Ez a megoldás az SQL Server-háttérrendszerét az IaaS-webalkalmazás referenciaarchitektúra üzembe helyezi. Az architektúra egy webes réteg, adatrétegbeli, Active Directory infrastruktúrával, az Application Gateway és terheléselosztó tartalmaz. A webes és az adatrétegekhez az üzembe helyezett virtuális gépek rendelkezésre állási csoportban vannak konfigurálva, és az SQL Server példányai vannak konfigurálva a magas rendelkezésre állás érdekében egy AlwaysOn rendelkezésre állási csoportban. Virtuális gépek tartományhoz csatlakoztatva, és az Active Directory-csoportházirendek segítségével érvényesíti a biztonsági és megfelelőségi konfigurációk az operációs rendszer szintjén. Felügyeleti bástyagazdagép biztonságos kapcsolatot biztosít a rendszergazdák számára a hozzáférés üzembe helyezett erőforrásokat. **Az Azure használatát javasolja, hogy a felügyeleti és az adatok importálása a referencia architektúra alhálózatában VPN vagy ExpressRoute-kapcsolat beállítása.**

![Az általános adatvédelmi rendelet architekturális diagramja IaaS webes Applicaiton](images/gdpr-iaaswa-architecture.png?raw=true "IaaS webes Applicaiton az általános adatvédelmi rendelet architekturális diagramja")

Ez a megoldás a következő Azure-szolgáltatásokat használ. Az üzembe helyezési architektúra részletek találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakaszban.

- Azure-alapú virtuális gépek
    - (1) felügyeleti/megerősített (Windows Server 2016 Datacenter)
    - (2) az active Directory-tartományvezérlőhöz (Windows Server 2016 Datacenter)
    - (2) az SQL Server-fürt csomópontjának (Windows Server 2016-on futó SQL Server 2017)
    - (2) webkiszolgáló és IIS (Windows Server 2016 Datacenter)
- Rendelkezésre állási csoportok
    - (1) az active Directory-tartományvezérlők
    - (1) az SQL-fürtcsomópont
    - (1) webkiszolgáló és IIS
- Azure Virtual Network
    - ((1) /16 virtuális hálózatok
    - (5) /24 alhálózatok
    - DNS-beállítások mindkét tartományvezérlőn vannak beállítva
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway engedélyezve
        - Tűzfalmód: megelőzése
        - Set-szabály: OWASP 3.0
        - Figyelő: 443-as port
- Azure Storage
    - (7) georedundáns storage-fiókok
- Felhőbeli tanúsító
- Recovery Services-tároló
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Az Operations Management Suite (OMS)
- Azure Security Center

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
A következő szakaszt az üzembe helyezés és a megvalósítás elemek részletei.

**Bástyagazdagép**: A bástyagazdagép a központi hely, amely lehetővé teszi a felhasználók hozzáférhessenek a környezetben telepített erőforrás. A bástyagazdagép üzembe helyezett erőforrásokhoz való biztonságos kapcsolatot nyújt azáltal, hogy csak nyilvános IP-címekről érkező távoli forgalmat a biztonságos elemek listájához. Távoli asztali (RDP) forgalmat lehetővé teszik, hogy a forgalom forrását kell definiálni a hálózati biztonsági csoport (NSG).

Ez a megoldás létrehoz egy virtuális gépet egy tartományhoz csatlakoztatott megerősített gazdagépként az alábbi konfigurációkkal:
-   [A kártevőirtó bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS-bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Az Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az Azure Key Vaulttal
-   Egy [automatikus leállítási házirend](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) csökkenthető a fogyasztása a virtuális gépi erőforrások, amikor nincs használatban
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és egyéb titkos adatait, amely el van különítve a futó operációs rendszert a védett környezetben futnak

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra egy 10.200.0.0/16 címtere a privát virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: Ez a megoldás üzembe helyez egy másik alhálózatot, adatbázis-alhálózat, az Active Directory-alhálózathoz és felügyeleti alhálózaton egy virtuális gépen belüli – rendelkező erőforrásokat. Alhálózatok logikailag elválasztott korlátozhatja, hogy csak szükséges rendszer- és felügyeleti funkciók, az alhálózatok közötti adatforgalom az egyes alhálózatokra alkalmazott hálózati biztonsági csoport szabályait.

Tekintse meg a konfiguráció a [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) üzembe helyezett ezzel a megoldással. Szervezetek számára a hálózati biztonsági csoportok fent a fájl szerkesztésével állíthatja [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) alapján.

Minden egyes alhálózatban a dedikált hálózati biztonsági csoport (NSG) rendelkezik:
- Az Application Gateway (LBNSG) 1 NSG
- 1 NSG bástyagazdagép (MGTNSG)
- 1 NSG-t elsődleges és tartalék tartományvezérlők (ADNSG)
- SQL Server-kiszolgálók és a Felhőbeli tanúsító (SQLNSG) 1 NSG
- 1 NSG webes réteg (WEBNSG)

### <a name="data-in-transit"></a>Az átvitt adatok
Az Azure és az Azure adatközpontok bemenő kommunikáció alapértelmezés szerint titkosítja. Emellett az Azure Portalon az Azure Storage összes tranzakció HTTPS-kapcsolaton keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra használatával több mértéket, beleértve a titkosítást és -adatbázis naplózási szolgáltatásával az inaktív adatok védi.

**Az Azure Storage**: rest-követelményeket, a titkosított adatok kielégítése érdekében minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [a Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít a szervezeti biztonsági kötelezettségeit, és az általános adatvédelmi rendelet által meghatározott megfelelőségi követelmények támogatásához a személyes adatok biztonságos megőrzésében.

**Az Azure Disk Encryption**: az Azure Disk Encryption Windows IaaS virtuális gépek lemezeinek titkosított szolgál. [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) az operációs rendszer és az adatlemezek kötettitkosítást biztosít a Windows BitLocker funkcióját használja. A megoldás integrálva van az Azure Key Vault segítségével szabályozhatja, és kezelhetik a lemeztitkosítási kulcsokat.

**Az SQL Server**: az SQL Server-példányt használja a következő adatbázis biztonsági intézkedéseket:
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
-   [Az Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ez a megoldás az összes felhasználó jönnek létre az aad-ben, ideértve az SQL Server hozzáférő felhasználók.
-   Hitelesítés az alkalmazás az AAD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ezenkívül az adatbázis oszloptitkosítás AAD használ az SQL Server alkalmazás hitelesítéséhez. További információkért lásd: hogyan [bizalmas adatok védelme az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a felhasználóknak frissíteniük kell a munkája elvégzéséhez csak olyan mértékű hozzáférést biztosítson a minden részletre kiterjedő hozzáférési engedélyek megadása. Helyett az Azure-erőforrások minden felhasználó ugyanolyan korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos műveleteket, a személyes adatok eléréséhez. Az előfizetés-rendszergazda előfizetéshez való hozzáférés korlátozódik.
- [AAD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi az ügyfeleknek minimalizálása érdekében a felhasználóknak a számát, aki hozzáféréssel rendelkezik az egyes erőforrásokhoz.  AAD Privileged Identity Management rendszergazdái a felderítését, korlátozását és emelt szintű identitások és azok erőforrásokhoz való hozzáférésének figyelése. Ez a funkció is használható igény szerinti – igény rendszergazdai hozzáférés kényszerítésére.
- [AAD-Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait kapcsolatos észlelt gyanús tevékenységeket, és megvizsgálja a gyanús incidensek, a problémák megoldásához a megfelelő műveletet.
- Egy üzembe helyezett IaaS Active Directory-példány üzembe helyezett IaaS virtuális gépek operációsrendszer-szintű Identitáskezelés biztosít.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok felügyeletéhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-képességek segítségével az ügyfelek személyes adatainak és az ilyen adatokhoz való hozzáférés védelme:
- Speciális hozzáférési szabályzatok kell alapon vannak konfigurálva.
- A Key Vault hozzáférési szabályzatok a minimálisan szükséges engedélyeket kulcsok és titkos kulcsok vannak meghatározva.
- Összes kulcsot és titkos kulcsok a Key Vaultban van lejárati dátumát.
- Speciális hardveres biztonsági modulokban (HSM) által védett összes kulcsok a Key Vaultban. A kulcs típusa egy HSM által védett 2048-bites RSA-kulcsot.
- Minden felhasználó és identitások megkapják az RBAC használatával minimálisan szükséges engedélyeket.
- Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
- A szükséges kapcsolatok engedélyezett titkosítási műveletek kulcsok korlátozódnak.
- A megoldás integrálva van az Azure Key Vault IaaS virtuális gépek lemeztitkosítási kulcsokat és titkos kulcsok kezeléséhez.

**Javítások kezelése**: Ez a referenciaarchitektúra részeként üzembe helyezett Windows virtuális gépek úgy vannak konfigurálva, az automatikusan frissítéseket kapjanak a Windows Update szolgáltatás alapértelmezés szerint. Ez a megoldás is magában foglalja az OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást, amelyen frissített telepítések hozható létre a javítás virtuális gépekhez szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára biztosítja a valós idejű védelem funkció, amely alapján azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható riasztásokkal Ha az ismert kártevő vagy nemkívánatos szoftverek megpróbálják telepíteni vagy futtatni védett virtuális gépeken.

**Biztonsági riasztások**: [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) forgalom figyelésére, naplók gyűjtése és elemzése a fenyegetések adatforrások használatát teszi lehetővé. Ezenkívül az Azure Security Center fér hozzá az Azure-szolgáltatások konfigurációs és szolgáltatási javaslatok javíthatja biztonsági helyzetét, és a személyes adatok védelme érdekében a meglévő konfigurációt. Az Azure Security Center tartalmaz egy [fenyegetésfelderítési jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) a rendszer minden egyes észlelt fenyegetés, amelyek segítik az incidensmegoldás csapatokat a vizsgálatban és elhárításban.

**Az Application Gateway**: az architektúra csökkenti a használatával az Application Gateway webalkalmazási tűzfal (WAF) a biztonsági réseket, és az OWASP szabálykészletben engedélyezve van. További képességek:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL-alapú Kiszervezéshez](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le [a TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF mód)
- [Megelőzés üzemmód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) az OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételei](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) nyújtanak további védelmet és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is biztosít.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: A megoldás összes virtuális gépet helyez üzembe egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek rendelkezésre állás javítása érdekében több elkülönített hardverfürt között legyenek elosztva. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén teljesíti a 99,95 %-os Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és védelmet biztosít az összes konfiguráció az Azure Virtual Machines ebben az architektúrában. Recovery Services-tárolóval, ügyfelek is fájlok és mappák visszaállítása az IaaS virtuális gépről a teljes virtuális Gépet, így gyorsabb helyreállítást visszaállítása nélkül.

**Felhőbeli tanúsító**: [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) egy feladatátvevő fürt kvórum tanúsítójának a Windows Server 2016, amely Azure-t az egyeztetési pontként típusú. A Felhőbeli tanúsító – más kvórumtanúsítókhoz, például szavazattal rendelkezik, és részt vehet a kvórumszámításokban, de a standard szintű, nyilvánosan elérhető Azure Blob Storage használ. Ezzel elkerülhető a felesleges karbantartást a nyilvános felhőben üzemeltetett virtuális gépek.

### <a name="logging-and-auditing"></a>Naplózás és vizsgálat

OMS biztosít széles körű naplózását, a rendszer és a felhasználói tevékenységek, valamint a helyrendszer állapotát. Az OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetekben.
- **A Tevékenységnaplók**: [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) adjon meg egy előfizetéshez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. A Tevékenységnaplók segítségével határozza meg a műveletet kezdeményező, az eseményt, és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott az összes napló tartalmazza. Ezek a naplók például a Windows rendszer-eseménynaplói, az Azure Storage-naplók, a Key Vault-naplók és az Application Gateway hozzáférés és a tűzfal a naplókat.
- **Napló Archiválás**: minden diagnosztikai naplók írni egy központosított, titkosított csatornákon történik az Azure storage-fiókját archiválási. A megőrzési felhasználó által konfigurálható, mentése és 730 nap között, a megőrzési a szervezet konkrét követelményeinek. Ezek a naplók csatlakozhat Azure Log Analytics feldolgozási, tárolására és-irányítópult jelentéseit.

Ezenkívül a következő OMS-megoldások jelennek meg, ez az architektúra részeként:
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

A data folyamat diagramja (DFD) Ez a referenciaarchitektúra érhető el az [letöltése](https://aka.ms/gdprIaaSdfd) vagy alább találja. Ez a modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, ha a végzett módosítások.

![Az általános adatvédelmi rendelet fenyegetések modellezése IaaS webes Applicaiton](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS webes Applicaiton az általános adatvédelmi rendelet fenyegetések modellezése")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentációja

A [Azure biztonsági és megfelelőségi terv - GDPR vevő felelőssége mátrix](https://aka.ms/gdprCRM) vezérlő és a processzor felelősségi körébe tartozik az összes általános adatvédelmi rendelet cikk tartalmazza. Vegye figyelembe, hogy az Azure-szolgáltatásokhoz, egy ügyfél általában a vezérlő és a Microsoft úgy működik, mint a processzor.

A [Azure biztonsági és -megfelelőségi terv - GDPR IaaS webes kérelem végrehajtása mátrix](https://aka.ms/gdprIaaSweb) melyik általános adatvédelmi rendelet a cikkek az IaaS-webalkalmazás architektúra, beleértve a részletes leírását tárgyalt információkkal a hogyan végrehajtására az egyes kezelt cikk kielégíti.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfigurálni kell a biztonságos kapcsolatot az IaaS-webalkalmazás referenciaarchitektúra részeként üzembe helyezett erőforrásokat. Megfelelően állítja be a VPN-t vagy ExpressRoute, az ügyfelek hozzáadhatja egy védelmi réteget biztosít adatokat átvitel közben.

Az Azure-ral biztonságos VPN-alagút alkalmazásával egy helyszíni hálózat és a egy Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozható létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy egy titkosított kapcsolatot, az ügyfél hálózati és az Azure között lévő biztonságosan "csatorna" információkat. Site-to-Site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. A [IPsec-alagút módú](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást, mint egy titkosítási mechanizmus használatban van.

Forgalom a VPN-alagút haladnak át a helyek közötti VPN-nel az interneten, mert a Microsoft kínál egy másik, még biztonságosabb kapcsolat lehetőséget. Az Azure ExpressRoute dedikált WAN Azure-ban és a egy helyszíni helyhez vagy egy Exchange-szolgáltató közötti kapcsolat. Az ExpressRoute-kapcsolatok az interneten keresztül halad, mivel a kapcsolatok az interneten keresztül ajánlja fel a további megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál. Továbbá mivel ez az ügyfél távközlési szolgáltató közvetlen kapcsolatot, az adatok nem az interneten keresztül továbbítani tartalmaz, és ezért nem érhető el.

Ajánlott eljárások végrehajtásához egy biztonságos hibrid hálózatot, amely kiterjeszti a helyszíni hálózatot az Azure-bA [elérhető](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY KÖTELEZŐ GARANCIÁT A DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓRA VONATKOZÓAN. Ez a dokumentum biztosított "as-van." Információk és vélemények ebben a dokumentumban URL-CÍMEK és más internetes webhelyekre utaló hivatkozások értesítés nélkül változhatnak. Ez a dokumentum olvasásakor ügyfelek felelősségére használja.  
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások a szellemi tulajdonhoz ügyfelek.  
- Ügyfelek másolhatja és használhatja ezt a dokumentumot belső, hivatkozási célokból.  
- Bizonyos ajánlások a dokumentum eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.  
- Ez az architektúra célja, hogy az ügyfelek számára az adott követelményekhez beállítása alapjaként szolgálja ki, és nem használható-éles környezetben van.
- Ez a dokumentum referenciaként fejlesztik, és nem használható minden olyan eszközt, amely szerint egy ügyfél megfelel a megadott megfelelőségi követelmények és előírások meghatározásához. Ügyfelek jóváhagyott megvalósítás az ügyfeleknél a szervezet jogi támogatást kell kérnie.
