---
title: Azure Security and Compliance Blueprint IaaS webalkalmazás a PCI DSS
description: Azure Security and Compliance Blueprint IaaS webalkalmazás a PCI DSS
services: security
author: meladie
ms.assetid: 9c825380-2abe-4fdd-800c-59d1fed1780b
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: e8e9faa16474335463cbdccd9fe7b3d749be0f25
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781022"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-pci-dss"></a>Azure Security and Compliance Blueprint: IaaS webalkalmazás a PCI DSS

## <a name="overview"></a>Áttekintés

Ez a Azure Security and Compliance Blueprint útmutatást nyújt a Payment Card Industry adatbiztonsági szabványoknak (PCI DSS 3,2) megfelelő infrastruktúra-(IaaS-) környezetnek a begyűjtéséhez, tárolásához és lekéréséhez. kártyatulajdonos-adatkészletek. Egy közös hivatkozási architektúrát mutat be, és bemutatja a hitelkártya-adatmennyiség, a lejárati idő és az ellenőrző adatmennyiség megfelelő kezelését egy biztonságos, kompatibilis, többrétegű környezetben. Ez a terv egy teljes körű megoldást mutat be arra, hogy kielégítse a felhőalapú megközelítést használó szervezetek igényeit, hogy csökkentse az üzembe helyezés terheit és költségeit.

Ez a hivatkozási architektúra, a megvalósítási útmutató és a veszélyforrás-modell olyan alapot biztosít az ügyfeleknek, hogy megfeleljenek PCI DSS 3,2 követelményeinek. Ez a megoldás olyan alapkonfigurációt biztosít, amellyel az ügyfelek az Azure-ban PCI DSS 3,2-kompatibilis módon telepíthetik a számítási feladatokat. Ez a megoldás azonban nem használható éles környezetben, mert további konfigurálásra van szükség.

A PCI DSS-megfelelőség megvalósítása megköveteli, hogy egy akkreditált, minősített biztonsági értékelő (QSA) egy éles ügyfél-megoldást tanúsítson. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

Ez a megoldás egy IaaS webalkalmazás hivatkozási architektúráját telepíti SQL Server háttérrel. Az architektúra tartalmaz egy webes szintet, adatszintet, Active Directory infrastruktúrát, Application Gateway és Load Balancer. A webes és az adatszinten üzembe helyezett virtuális gépek egy rendelkezésre állási csoportban vannak konfigurálva, és a magas rendelkezésre állás érdekében SQL Server példányok egy always on rendelkezésre állási csoportba vannak konfigurálva. A virtuális gépek tartományhoz csatlakoznak, és Active Directory csoportházirendek segítségével kényszerítheti ki a biztonsági és megfelelőségi konfigurációkat az operációs rendszer szintjén. A felügyeleti megerősített gazdagépek biztonságos kapcsolatot biztosítanak a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez. **Az Azure azt javasolja, hogy VPN-vagy ExpressRoute-kapcsolaton keresztül konfigurálja a felügyeleti és az Adatimportálási kapcsolatokat a hivatkozási architektúra alhálózatára.**

![IaaS webalkalmazás a PCI DSS hivatkozási architektúrájának diagramja](images/pcidss-iaaswa-architecture.png "IaaS webalkalmazás a PCI DSS hivatkozási architektúrájának diagramja")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei a [telepítési architektúra](#deployment-architecture) szakaszban találhatók.

- Rendelkezésre állási csoportok
    - (1) Active Directory tartományvezérlők
    - (1) SQL-fürtcsomópontok
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) webalkalmazási tűzfal
        - Tűzfal mód: megelőzés
        - Szabály beállítása: OWASP 3,0
        - Figyelő portja: 443
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
    - (7) geo-redundáns Storage-fiókok
- Azure Virtual Machines
    - (1) felügyelet/megerősített (Windows Server 2016 Datacenter)
    - (2) Active Directory tartományvezérlő (Windows Server 2016 Datacenter)
    - (2) SQL Server fürtcsomópont (SQL Server 2017 a Windows Server 2016 rendszeren)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 hálózat
    - (5)/24 hálózat
    - (5) hálózati biztonsági csoportok
- Felhőbeli tanúsító
- Recovery Services-tároló

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Megerősített gazdagép**: A megerősített gazdagép az egyetlen belépési pont, amely lehetővé teszi, hogy a felhasználók hozzáférhessenek az üzembe helyezett erőforrásokhoz ebben a környezetben. A megerősített gazdagép biztonságos kapcsolódást biztosít a központilag telepített erőforrásokhoz azáltal, hogy csak a nyilvános IP-címekről érkező távoli forgalmat engedélyezi biztonságos listán. A távoli asztal (RDP) forgalmának engedélyezéséhez a hálózati biztonsági csoportban meg kell határozni a forgalom forrását.

Ez a megoldás egy virtuális gépet hoz létre tartományhoz csatlakozó megerősített gazdagépként a következő konfigurációkkal:
-   [Antimalware-bővítmény](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) a Azure Key Vault használatával
-   [Automatikus leállítási szabályzat](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) , amely csökkenti a virtuális gépek erőforrásainak felhasználását, ha nincs használatban
-   A [Windows Defender hitelesítőadat](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) -védelme engedélyezve van, hogy a hitelesítő adatok és egyéb Titkok a futó operációs rendszertől elkülönített védett környezetekben fussanak.

### <a name="virtual-network"></a>Virtuális hálózat

Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: Ez a megoldás az erőforrásokat egy különálló webes alhálózattal, adatbázis-alhálózattal, Active Directory alhálózattal és egy virtuális hálózaton belüli felügyeleti alhálózattal telepíti. Az alhálózatokat az egyes alhálózatokon alkalmazott hálózati biztonsági csoportokra vonatkozó szabályok logikailag elkülönítik az alhálózatok közötti adatforgalom korlátozása érdekében, hogy csak a rendszer-és felügyeleti funkciókhoz szükségesek legyenek.

Tekintse meg a megoldással üzembe helyezett [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) konfigurációját. A szervezetek úgy konfigurálhatják a hálózati biztonsági csoportokat, hogy a fenti [dokumentáció](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) használatával útmutatóként szerkesztik a fenti fájlt.

Az alhálózatok mindegyike dedikált hálózati biztonsági csoporttal rendelkezik:
- 1 hálózati biztonsági csoport a Application Gatewayhoz (LBNSG)
- 1 hálózati biztonsági csoport a megerősített gazdagéphez (MGTNSG)
- 1 hálózati biztonsági csoport elsődleges és tartalék tartományvezérlők számára (ADNSG)
- 1 hálózati biztonsági csoport az SQL-kiszolgálók és a Felhőbeli tanúsító (SQLNSG) számára
- 1 hálózati biztonsági csoport webes rétegek számára (WEBNSG)

### <a name="data-in-transit"></a>Átvitt adatok

Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok és az összes kommunikációját. Emellett a Azure Portal az Azure Storage-ba irányuló összes tranzakció HTTPS-n keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra több mértéken keresztül védi az inaktív adatok védelmét, beleértve a titkosítást és az adatbázis-naplózást is.

**Azure Storage**: A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption)használ. Ez segíti a kártyabirtokosok adatainak védelmét és védelmét a PCI DSS 3,2 által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával.

**Azure Disk Encryption**: A Azure Disk Encryption a Windows IaaS virtuálisgép-lemezek titkosítására szolgál. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Azure Key Vault a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**SQL Server**: A SQL Server példány a következő adatbázis-biztonsági mértékeket használja:
- [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
-   Az [SQL Database naplózása](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A SQL Database [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használatára van konfigurálva, amely valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, hogy megvédje az adatokat a nyugalmi állapotban. Transzparens adattitkosítás biztosít garanciát arra, hogy a tárolt kártyabirtokosok adatai nem érvényesek a jogosulatlan hozzáférésre.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) -veszélyforrások észlelése lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
-   [Always encrypted oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas kártyatulajdonos-adatok soha ne jelenjenek meg egyszerű szöveges formában az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- A [kiterjesztett tulajdonságok](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkció használatával felhagyhatja az adatalanyok feldolgozását, mivel lehetővé teszi a felhasználók számára, hogy egyéni tulajdonságokat adjanak az adatbázis-objektumokhoz, és a "megszűnt" címkével lássa el az adataikat az alkalmazás logikájának támogatásához, hogy megakadályozza a társított kártyatulajdonos-adatkészletek.
- A [sorok szintjének biztonsága](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználók számára, hogy szabályzatokat határozzanak meg az adathozzáférés korlátozásához a feldolgozás megszüntetéséhez.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas kártyabirtokosok adatmegjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A dinamikus adatmaszkolás automatikusan képes észlelni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. Ez segít azonosítani és csökkenteni a kártyatulajdonosi adatokat, hogy ne lépjenek ki az adatbázisból jogosulatlan hozzáférés útján. Az ügyfelek feladata a dinamikus adatmaszkolási beállítások módosítása az adatbázis-sémájuk betartásához.

### <a name="identity-management"></a>Identitáskezelés

Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft&#39;s több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás. A megoldás összes felhasználója Azure Active Directoryban jön létre, beleértve a Azure SQL Databasehoz hozzáférő felhasználókat is.
- Az alkalmazáshoz való hitelesítés Azure Active Directory használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlopának titkosítása a Azure Active Directory használatával hitelesíti az alkalmazást a Azure SQL Database. További információ: a [bizalmas adatok védelme Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Az [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a szervezet&#39;s identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet&#39;s identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidenseket, hogy tegye meg a megfelelő lépéseket a megoldásához.

### <a name="security"></a>Biztonság

**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen jellegű adatvédelemben és hozzáférésben:

- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa HSM-védelemmel ellátott 2048-bites RSA-kulcs.
- Minden felhasználó és identitás a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kap.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.
- A megoldás integrálva van Azure Key Vault a IaaS virtuális gép lemez-titkosítási kulcsainak és titkainak kezeléséhez.

**Javítási felügyelet**: Az ebben a hivatkozási architektúrában üzembe helyezett Windows-beli virtuális gépek alapértelmezés szerint úgy vannak konfigurálva, hogy Windows Update szolgáltatásból fogadják az automatikus frissítéseket. Ez a megoldás tartalmazza azt a [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatást is, amelyen keresztül a frissített központi telepítések létrehozhatók a virtuális gépek szükség szerinti javításához.

**Kártevők elleni védelem**: A Virtual Machines rendszerhez készült [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) olyan valós idejű védelmi képességet biztosít, amely lehetővé teszi a vírusok, kémprogramok és más kártékony szoftverek azonosítását és eltávolítását, amelyekkel konfigurálható riasztások állíthatók be, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik Telepítsen vagy futtasson védett virtuális gépeken.

**Azure Security Center**: A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center [előre meghatározott biztonsági riasztásokkal](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center [Egyéni riasztási szabályai](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.

A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos [fenyegetési intelligenciáról szóló jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.

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

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adatokat Log Analytics munkaterületeken belül minden adattípushoz, ami lehetővé teszi, hogy az összes adatokat együtt elemezze az eredeti forrástól függetlenül. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) szerepelnek:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Activity log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók Azure SQL Databaseból való gyűjtésében. Az Automation [change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosíthassák a környezet változásait.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure

## <a name="threat-model"></a>Veszélyforrások modellje

A viszonyítási architektúra adatáramlási diagramja (DFD) [letölthető](https://aka.ms/pcidss-iaaswa-tm) , vagy az alábbiakban található. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![IaaS webalkalmazás a PCI DSS hivatkozási architektúrájának diagramja](images/pcidss-iaaswa-threat-model.png "IaaS webalkalmazás a PCI DSS hivatkozási architektúrájának diagramja")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

A [Azure Security and Compliance Blueprint-PCI DSS ügyfél-felelősségi mátrix](https://aka.ms/pcidss-crm) felsorolja az összes PCI DSS 3,2 követelmény feladatait.

A [Azure Security and Compliance Blueprint-PCI DSS IaaS webalkalmazás-implementációs mátrixa](https://aka.ms/pcidss-iaaswa-cim) információt nyújt arról, hogy a IaaS webalkalmazás-architektúrája milyen PCI DSS 3,2-es követelményekkel foglalkozik, beleértve a részletes leírását is, hogy a a megvalósítás megfelel az egyes lefedett cikkek követelményeinek.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute

A biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) úgy kell konfigurálni, hogy biztonságosan hozzon létre kapcsolatot a IaaS webalkalmazás-hivatkozási architektúrájának részeként telepített erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-Virtual Network közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkat használjanak. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft egy másik, még biztonságosabb kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adat nem az interneten keresztül történik, ezért nem lesz elérhető.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)ra.

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
- A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
- Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
- A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
- Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
- Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.
