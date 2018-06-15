---
title: Azure biztonsági és megfelelőségi tervezetének - IaaS-webalkalmazás GDPR
description: Azure biztonsági és megfelelőségi tervezetének - IaaS-webalkalmazás GDPR
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c418664fe94ee2801a24df59b9ef3451f3985cdb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162189"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Azure biztonsági és megfelelőségi tervezetének - IaaS-webalkalmazás GDPR

## <a name="overview"></a>Áttekintés (klasszikus)
Az általános adatok védelmi szabályozás (GDPR) információk gyűjtésére, tárolására és személyes információt, beleértve a hogyan szervezetek azonosítása és személyes adatok védelmét, átláthatóság követelmények teljesítése, észlelése és jelentés segítségével számos követelményeket tartalmaz személyes adatok behatolás és vonat adatvédelmi csoporthoz és a többi alkalmazott. A GDPR egyéni felhasználók számára a személyes adataik nagyobb ellenőrzést ad, és számos új kötelezettségeket a szervezeteknek, amelyek gyűjtése, kezelése vagy személyes adatok elemzése ró. A GDPR ró a szervezetek, termékek kínáló új szabályokat, és az Európai Unió, illetve hogy vállalatoknál szolgáltatások összegyűjti és elemzi az adatok EU lakosai kötve. A GDPR helyétől függetlenül kikényszeríti a szervezetek található vonatkozik.

A Microsoft Azure úgy iparágvezető biztonsági intézkedéseket, és az adatvédelmi szabályzatáról a felhőben, beleértve a személyes adatok a GDPR által azonosított kategóriákat adatok védelme. A Microsoft [szerződési feltételek](http://aka.ms/Online-Services-Terms) Microsoft véglegesítése processzorok követelményeinek.

Az Azure biztonsági és megfelelőségi tervezetének infrastruktúra készítéséhez egy szolgáltatási (IaaS) környezetben megfelelő-e egy egyszerű Internet felé néző webes alkalmazáshoz útmutatást. Ez a megoldás azt mutatja be, amelyben az ügyfelek megfelel a GDPR konkrét biztonsági és megfelelőségi követelmények módon, és build és a saját IaaS webes alkalmazás megoldások konfigurálása az Azure-ügyfelek számára alapjaként szolgál. Az ügyfelek használják a referencia-architektúrában, és kövesse a Microsoft [négy lépésből álló folyamat](https://aka.ms/gdprebook) a GDPR megfelelőségi útjuk:
1. Észlelés: Azonosítása, mely személyes adat és helyét.
2. Kezelése: Szabályozására, hogy személyes adatok használt és elérhető.
3. Védelem: Létesíteni megakadályozása, észlelésében és kezelésében biztonsági réseket és adatszivárgáshoz biztonsági vezérlők.
4. Jelentés: Szükséges dokumentáció és kérelmek kezeléséhez, és értesítések megsértése.

A referencia-architektúrában, a kapcsolódó üzembe helyezési útmutatóban és a fenyegetések modellezése célja, hogy az ügyfelek számára a megadott követelményeknek való igazításának lehetősége alapjaként szolgálnak, és nem használható mint – egy termelési környezetben. Vegye figyelembe a következőket:
- Az architektúra munkaterhelések telepítése az Azure-bA GDPR megfelelő módon ügyfelek alapterv.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelése használatával készített ebbe az architektúrába követelmények változhatnak megoldások a részletekről az egyes ügyfelek általi alapján.

## <a name="architecture-diagram-and-components"></a>Architektúrája és összetevői
Ez a megoldás egy referencia-architektúrában, az infrastruktúra-szolgáltatási webalkalmazás egy SQL Server-háttérrendszerrel telepíti. Az architektúra tartalmazza egy webes réteg, adatrétegbeli, Active Directory infrastruktúra, Application Gateway és terheléselosztóhoz. A webes és adatok rétegek telepített virtuális gépek rendelkezésre állási csoportok vannak konfigurálva, és az SQL Server-példányokat a magas rendelkezésre állású AlwaysOn rendelkezésre állási csoportban vannak konfigurálva. Virtuális gépek a tartományhoz, és az Active Directory-csoportházirendek segítségével az operációs rendszer szintjén és megfelelőségi beállítások kényszerítése. Egy felügyeleti megerősített állomás biztonságos kapcsolatot biztosít a rendszergazdák központilag telepített hozzáférését az erőforrásokhoz. **Azure azt javasolja, hogy a referencia architektúra alhálózati történő felügyelete és az adatok importálása a VPN- vagy ExpressRoute-kapcsolat beállítása.**

![IaaS webes Applicaiton GDPR referencia architektúra diagram](images/gdpr-iaaswa-architecture.png?raw=true "IaaS webes Applicaiton a GDPR referencia architektúra diagramja")

A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

- Azure-alapú virtuális gépek
    - (1) felügyeleti/megerősített (Windows Server 2016 Datacenter)
    - (2) az active Directory-tartományvezérlőhöz (Windows Server 2016 Datacenter)
    - (2) az SQL Server-fürt csomópontjának (Windows Server 2016 SQL Server 2017)
    - (2) webkiszolgáló/IIS (Windows Server 2016 Datacenter)
- Rendelkezésre állási csoportok
    - (1) az active Directory-tartományvezérlők
    - (1) az SQL-fürt csomópontjain
    - (1) webkiszolgáló/IIS
- Azure Virtual Network
    - ((1) /16 virtuális hálózatok
    - (5) /24 alhálózatok
    - DNS-beállítások mindkét tartományvezérlőn van beállítva
- Azure Load Balancer
- Azure Application Gateway
    - (1) az Alkalmazásátjáró WAF engedélyezve
        - tűzfal módban: megelőzése
        - Szabálykészlet: OWASP 3.0
        - figyelő: 443-as port
- Azure Storage
    - (7) georedundáns storage-fiókok
- Felhő tanúsító
- Recovery Services-tároló
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Az Operations Management Suite (OMS)
- Azure Security Center

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
Az alábbi szakasz részletesen üzembe helyezése és megvalósítás elemek.

**Megerősített állomás**: A megerősített állomás a központi hely, amely lehetővé teszi a felhasználóknak ebben a környezetben telepített erőforrások elérését. A megerősített állomás csak átengedi a nyilvános IP-címekről távoli forgalmat biztonságos lista telepített erőforrások biztonságos kapcsolatot nyújt. Távoli asztal (RDP)-forgalmát engedélyezi, hogy a forgalom forrásának kell definiálni a hálózati biztonsági csoport (NSG).

Ez a megoldás létrehoz egy virtuális gépet a következő konfigurációk a tartományhoz csatlakoztatott megerősített gazdagépeként:
-   [Kártevőirtó-bővítmény](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS-bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-bővítményt](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Azure Key Vault használatával
-   Egy [automatikus leállítási házirendet](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) használaton virtuálisgép-erőforrások fogyasztásának csökkentése érdekében
-   [A Windows Defender Credential őr](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) engedélyezve van, hogy a hitelesítő adatokat és más titkos adatokat futtatni egy olyan védett környezetben, amely el van különítve a futó operációs rendszer

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra egy címterében 10.200.0.0/16 titkos virtuális hálózat határozza meg.

**Hálózati biztonsági csoportok**: Ez a megoldás telepíti egy külön webkiszolgáló alhálózati, adatbázis alhálózati, Active Directory-alhálózathoz és felügyeleti alhálózaton belül egy virtuális hálózati architektúrát erőforrásokat. Alhálózatok logikailag elválasztott hálózati biztonsági csoport szabályokat az egyes alhálózatok korlátozzák a forgalmat, amely csak szükséges rendszer- és felügyeleti funkciókat az alhálózatok között.

Tekintse meg a konfigurációs [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) Ez a megoldás üzembe helyezéséhez. A szervezetek konfigurálhatja a hálózati biztonsági csoportok fent fájl szerkesztésével [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) útmutatóként.

Egyes alhálózatok rendelkezik egy dedikált hálózati biztonsági csoport (NSG):
- Az Alkalmazásátjáró (LBNSG) 1 NSG
- 1 NSG megerősített állomás (MGTNSG)
- Az elsődleges és tartalék tartományvezérlő (ADNSG) 1 NSG
- Az SQL Server-kiszolgálók és a felhő tanúsító (SQLNSG) 1 NSG
- 1 NSG a webes réteg (WEBNSG)

### <a name="data-in-transit"></a>Adatokat átvitel közben
Azure alapértelmezés szerint minden kommunikáció és az Azure adatközpontjaiban titkosítja. Emellett a az Azure portálon keresztül Azure Storage összes tranzakciója HTTPS használatával történik.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra védi az adatokat több mértéket, beleértve a titkosítást és az adatbázis naplózását történik.

**Az Azure Storage**: titkosított adatok rest követelmények teljesítéséhez összes [Azure Storage](https://azure.microsoft.com/services/storage/) használ [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ez segít és megvédeni a személyes adatok szervezeti biztonsági kötelezettségeit, és a megfelelőségi követelményeket határozzák meg a GDPR támogatásához.

**Az Azure Disk Encryption**: Azure Disk Encryption windowsos infrastruktúra-szolgáltatási virtuális gép lemezeinek titkosított szolgál. [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows operációs rendszer és az adatlemezek kötettitkosítást biztosít a BitLocker-szolgáltatás. A megoldás integrálva van az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok kezeléséhez.

**SQL Server**: az SQL Server-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD hitelesítési és engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) adatbázis-felhasználók és más Microsoft-szolgáltatásokban egyetlen központi helyen identitás kezelését teszi lehetővé.
-   [SQL-adatbázis naplózásának](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be Azure storage-fiók.
-   SQL-adatbázis használatára van konfigurálva [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), melyik teljesít valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok, és a rest-tranzakciós naplófájlok a következő információk védelme érdekében. TDE biztosítja, hogy a személyes adatok tárolása nem lett bárki hozzáférhet.
-   [Tűzfal-szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tagadni a hozzáférést minden adatbázis-kiszolgálók csak a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [A Fenyegetésészlelés SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy felderítésére és a lehetséges veszélyforrásokra választ, adja meg a biztonsági riasztások adatbázis gyanús tevékenységek, a potenciális biztonsági réseket, az SQL injektálási támadások és a rendellenes adatbázis-hozzáférési előforduló minták.
-   [Mindig a titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy személyes adatok soha nem jelenik meg az adatbázis rendszerében szövegként. Miután engedélyezte az adattitkosítást, csak az ügyfélalkalmazások vagy a kulcsoknak access rendelkező alkalmazáskiszolgáló hozzáférhet egyszerű szöveges adatokat.
- A [Extended Properties](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkciója használható megszüntetheti a feldolgozási érintettek, mivel lehetővé teszi a felhasználók egyéni tulajdonságok adatbázis-objektumok és adatok kódnak "Kifutott" támogatásához az alkalmazáslogikát, hogy megakadályozza a társított személyes adatok feldolgozása.
- [A sorszintű biztonsági](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy a felhasználók házirendek adatok feldolgozási megszüntetheti való hozzáférésének korlátozásához.
- [SQL adatbázis dinamikus adatok maszkolása (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a személyes adatok veszélyeztetettségének rendszerjogosultsággal nem rendelkező felhasználók vagy alkalmazások adatok maszkolása által. DDM automatikusan potenciálisan bizalmas információk felderítésére és a megfelelő maszkok alkalmazni kívánt javaslatot. Ezzel a megoldással a jogosult a GDPR protection, és csökkenti a hozzáférést úgy, hogy azt nem létezik az adatbázis keresztül jogosulatlan hozzáférés a személyes adatok azonosítása. **Megjegyzés: Az ügyfelek kell igazodnia kell az adatbázis-séma DDM beállításainak a módosítását.**

### <a name="identity-management"></a>Identitáskezelés
A következő technológiákat az Azure környezetben személyes adatokhoz való hozzáférés kezelése képességeket biztosítják:
-   [Az Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Ebben a megoldásban minden felhasználó az aad-ben, beleértve a felhasználók az SQL Server elérésekor jönnek létre.
-   Hitelesítés az alkalmazás aad-ben alapján történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlop titkosítási AAD hitelesítéséhez használja az SQL Server alkalmazást. További információkért lásd: hogyan [bizalmas adatokat az SQL-adatbázis védelme](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdák számára, hogy a minden részletre kiterjedő hozzáférési engedélyek megadását csak olyan mértékű hozzáférést a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Helyett az Azure-erőforrások minden felhasználó korlátlan engedélyeket ad, a rendszergazdák engedélyezhetik csak bizonyos műveletek személyes adatainak eléréséhez. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként.
- [Aad-ben a Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi, hogy a felhasználók számának minimalizálása érdekében az ügyfelek, akik bizonyos erőforrások elérésére.  Rendszergazdák AAD Privileged Identity Management segítségével ismerje meg, korlátozása, és a kiemelt jogosultságú identitások és azok erőforrásokhoz való hozzáférés figyelésére. Ez a funkció érvényesítését igény szerint közvetlenül az időponthoz kötött rendszergazdai hozzáférés szükség esetén is használható.
- [Az AAD-Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a potenciális biztonsági réseket egy szervezet identitásait érintő, konfigurálja az automatikus válaszokat ad egy szervezet identitásait kapcsolódó észlelt gyanús tevékenységek és pedig megvizsgálja a gyanús incidensek, a megfelelő művelettel hárítsa el őket.
- Egy telepített IaaS Active Directory példánnyal üzembe helyezett infrastruktúra-szolgáltatási virtuális gépeknél operációsrendszer-szintű identitás-felügyeletet biztosít.

### <a name="security"></a>Biztonsági
**Titkos kulcsok kezelése**: A megoldás az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok kezeléséhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi képességeket Azure Key Vault segít a felhasználóknak személyes adatok és az ezekhez az adatokhoz való hozzáférés védelmét:
- Speciális hozzáférési házirendek konfigurálása a szükséges alapul.
- Kulcstároló hozzáférési házirendeket a kulcsok és titkos minimálisan szükséges engedélyeket.
- Minden kulcsok és titkos kulcs tárolóban lévő állapottal rendelkezik lejáratának dátumát.
- A Key Vault minden kulcs speciális hardveres biztonsági modulokkal (HSM) védett. A kulcs HSM-védelemmel 2048 bites RSA-kulcs esetében.
- Minden felhasználó és identitások kapnak az RBAC használata minimálisan szükséges engedélyeket.
- Diagnosztikai naplókat a Key Vault legalább 365 nap megőrzési idővel rendelkező engedélyezve van.
- A kulcsok engedélyezett titkosítási műveleteket a meglévők közül szükséges korlátozódnak.
- A megoldás integrálva van az Azure Key Vault infrastruktúra-szolgáltatási virtuális gép lemez-titkosítási kulcsok és titkos kulcsok kezeléséhez.

**Felügyeleti javítás**: Windows virtuális gépek telepítése a referencia-architektúrában részeként az automatikus frissítések kap a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja az OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatás, amelyen keresztül a frissített telepítések is létrehozható a következő virtuális gépek szükség esetén.

**Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára lehetővé teszi a valós idejű védelem segítségével azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható a riasztások Ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni futtassa a védett virtuális gépet.

**Biztonsági riasztások**: [az Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) lehetővé teszi az ügyfelek felügyeljék a forgalmat, gyűjtését és adatforrások esetében fenyegetések elemzése. Emellett az Azure Security Center fér hozzá Azure-szolgáltatások adja meg a konfigurációs és szolgáltatási javaslatokat növelheti a biztonságot, és a személyes adatok védelme érdekében meglévő konfigurációját. Az Azure Security Center tartalmaz egy [az eszközintelligencia-jelentés fenyegetés](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) az egyes észlelt fenyegetés segítséget nyújt az incidensválasz-csoportok vizsgálja meg, és szervizelheti azokat a fenyegetéseket.

**Alkalmazásátjáró**: architektúrájának csökkenti a biztonsági réseket Alkalmazásátjáró használata a webalkalmazási tűzfal (waf-ot), és a OWASP szabálykészletben engedélyezve van. További funkciók a következők:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL kiürítése](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le a [TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF mód)
- [Megelőző módja](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) rendelkező OWASP 3.0 szabálykészletben
- Engedélyezése [diagnosztikai naplózás](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Egyéni állapot-mintavételi csomagjai](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) adja meg a további védelmi és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is tartalmaz.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állású**: A megoldás összes virtuális gépet telepít egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási készletek győződjön meg arról, hogy a virtuális gépek rendelkezésre állásának javítása érdekében több elkülönített hardver fürt különböző pontjain. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén a 99,95 % értekezlet Azure SLA-t.

**Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és az összes konfiguráció Azure virtuális gépek ebben az architektúrában védelmét. A Recovery Services-tároló, az ügyfelek is fájlok és mappák visszaállítása egy infrastruktúra-szolgáltatási virtuális gép helyreállítása a teljes virtuális gép, gyorsabb visszaállítás engedélyezése nélkül.

**A felhő tanúsító**: [felhő tanúsító](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) a feladatátvevő fürt kvórum tanúsítójának a Windows Server 2016-os, amely kihasználja az Azure-bA az egyeztetési pont típusa. A felhő tanúsító, mint bármely más kvórum tanúsító szavazás lekérdezi és részt vesznek a kvórumszámításokat, de a szabványos nyilvánosan elérhető Azure Blob Storage használ. Ez megszünteti az extra karbantartási növeli a nyilvános felhőben üzemeltetett virtuális gépeket.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

OMS rendszer és felhasználói tevékenységek, valamint a rendszerállapot kiterjedt naplózás biztosít. Az OMS [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetben.
- **Tevékenységi naplóit**: [tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) előfizetés erőforrásainak végrehajtott műveletek betekintést nyújtanak. Tevékenységi naplóit is meghatározásához, hogy egy művelet kezdeményező az előfordulási és állapot ideje.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplók tartalmazzák. Ezek a naplók tartalmazzák a Windows rendszer-eseménynaplói, Azure Storage naplókat, Key Vault-naplók és Alkalmazásátjáró hozzáférés és a tűzfalon naplók.
- **Napló Archiválás**: minden diagnosztikai naplók írni egy központosított és titkosított Azure storage-fiókjához archiválási. A megőrzési felhasználó által konfigurálható, mentése 730 nap szervezet-specifikus megőrzési követelményeinek megfelelően. Ezek a naplók feldolgozása, tárolására és irányítópult reporting Azure Naplóelemzés csatlakozni.

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

Az adatok folyamatábrája (DFD) a referenciaarchitektúra érhető el [letöltése](https://aka.ms/gdprIaaSdfd) vagy alatt található. Ez a modell segít megérteni a pontokat a rendszer infrastruktúra kockázatokkal módosításakor az ügyfelek.

![Infrastruktúra-szolgáltatási webes Applicaiton esetében fenyegetések modellezése GDPR](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS webes Applicaiton GDPR fenyegetés modell")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

A [Azure biztonsági és megfelelőségi tervezetének - GDPR ügyfél felelősségi mátrix](https://aka.ms/gdprCRM) felsorolja az összes GDPR cikkhez a tartományvezérlő és a processzor feladatkörök. Vegye figyelembe, hogy az Azure szolgáltatások esetében az ügyfél általában a tartományvezérlő és a Microsoft úgy működik, mint a processzor.

A [Azure biztonsági és megfelelőségi tervezetének - GDPR IaaS webes kérelem végrehajtása mátrix](https://aka.ms/gdprIaaSweb) tájékoztatást ad a mely GDPR az IaaS-webalkalmazás architektúra, például részletes leírást a cikkek tárgyalt a végrehajtására hogyan megfelel minden kezelt cikk.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok
### <a name="vpn-and-expressroute"></a>VPN- és ExpressRoute
Biztonságos VPN-alagúton vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni, hogy biztonságos kapcsolatot létrehozni a IaaS webes alkalmazás referencia-architektúrában részeként telepített erőforrások. Megfelelően állítja be a VPN- vagy ExpressRoute, az ügyfelek megteheti egy védelmi réteget biztosít adatokat az átvitel során.

Az Azure-ral biztonságos VPN-alagúton implementálásával egy a helyszíni hálózat és az Azure virtuális hálózat közötti virtuális magánhálózati kapcsolat hozhatók létre. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi az ügyfelek biztonságosan "tunnel" információkhoz belül az ügyfél hálózati és az Azure közötti egy titkosított kapcsolat. Telephelyek közötti VPN egy biztonságos, érett technológia, amely különböző méretű vállalatok által évtizedeken van telepítve. A [IPsec-bújtatásos mód](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) ezt a beállítást olyan titkosítási módszert használatban van.

Forgalom a VPN-alagút belül haladnak át a telephelyek közötti VPN az internethez, mert a Microsoft egy másik, a megoldás még biztonságosabbá kapcsolat lehetőséget is kínál. Az Azure ExpressRoute egy dedikált WAN Azure és egy helyszíni helyre vagy az Exchange-szolgáltató közötti kapcsolat. ExpressRoute-kapcsolatok az interneten keresztül halad, mivel ezeket a kapcsolatokat további megbízhatóságát, gyorsabb sebességű, kisebb késések fordulnak elő, és kínálnak nagyobb biztonságot nyújtana tipikus kapcsolatok az interneten keresztül. Továbbá mivel ez a közvetlen kapcsolat az ügyfél telekommunikációs szolgáltató, az adatok nem használható az interneten keresztül tartalmaz, és ezért nem érhető el azt.

Ajánlott eljárások a biztonságos hibrid hálózat, amely egy helyi hálózati kibővíti az Azure-bA [elérhető](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.  
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.  
- Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.  
- Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.  
- Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
- Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
