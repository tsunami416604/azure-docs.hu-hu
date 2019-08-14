---
title: Azure Security and Compliance Blueprint IaaS webalkalmazás FedRAMP számára
description: Azure Security and Compliance Blueprint IaaS webalkalmazás FedRAMP számára
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: e1d481c6019feebf3d62f0e23480f5572363869c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946845"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure Security and Compliance Blueprint: IaaS-webalkalmazás a FedRAMP

## <a name="overview"></a>Áttekintés

A [Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov) egy Egyesült államokbeli kormányzati program, amely szabványosított megközelítést biztosít a biztonság értékeléséhez, engedélyezéséhez és a felhőalapú termékek és szolgáltatások folyamatos monitorozásához. Ez a Azure Security and Compliance Blueprint Automation útmutatást nyújt a FedRAMP-kompatibilis infrastruktúra-szolgáltatás (IaaS) környezetének üzembe helyezéséhez, amely alkalmas egy egyszerű, internetes webalkalmazáshoz. Ez a megoldás automatizálja az Azure-erőforrások üzembe helyezését és konfigurálását egy közös hivatkozási architektúrához, amely megmutatja, hogy az ügyfelek milyen módon tudják kielégíteni a konkrét biztonsági és megfelelőségi követelményeket, és alapként szolgálnak az ügyfelek számára saját megoldások konfigurálása az Azure-ban. A megoldás a FedRAMP magas alapkonfigurációjának egy részhalmazát valósítja meg a NIST SP 800-53 alapján. A FedRAMP-követelményekkel és a megoldással kapcsolatos további információkért [](#compliance-documentation)tekintse meg a megfelelőségi dokumentációt.
> [!NOTE]
> Ez a megoldás központilag telepíti Azure Government.

Ez a Azure Security and Compliance Blueprint Automation automatikusan üzembe helyez egy IaaS-webalkalmazás-referenciát az előre konfigurált biztonsági ellenőrzésekkel, hogy az ügyfelek az FedRAMP követelményeinek megfelelően érjenek el. A megoldás Azure Resource Manager sablonokból és PowerShell-parancsfájlokból áll, amelyek az erőforrások üzembe helyezését és konfigurálását ismertetik.

Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben. Ha módosítás nélkül helyezi üzembe az alkalmazást a környezetbe, az nem elegendő ahhoz, hogy teljesen megfeleljen a FedRAMP magas alapkonfiguráció követelményeinek. Vegye figyelembe a következőket:
- Ez az architektúra olyan alapkonfigurációt biztosít, amellyel az ügyfelek FedRAMP-kompatibilis módon használhatják az Azure-t.
- Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi értékelésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

A megoldás működésének gyors áttekintéséhez tekintse meg ezt a [videót](https://aka.ms/fedrampblueprintvideo) , amely ismerteti az üzembe helyezést és annak bemutatását.

Kattintson [ide](https://aka.ms/fedrampblueprintrepo) az üzembe helyezési utasításokhoz.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői
Ez a megoldás egy IaaS webalkalmazás hivatkozási architektúráját telepíti SQL Server háttérrel. Az architektúra tartalmaz egy webes szintet, adatszintet, Active Directory infrastruktúrát, Application Gateway és Load Balancer. A webes és az adatrétegekbe telepített virtuális gépek egy rendelkezésre állási csoportban vannak konfigurálva, és a magas rendelkezésre állás érdekében SQL Server példányok egy AlwaysOn rendelkezésre állási csoportba vannak konfigurálva. A virtuális gépek tartományhoz csatlakoznak, és Active Directory csoportházirendek segítségével kényszerítheti ki a biztonsági és megfelelőségi konfigurációkat az operációs rendszer szintjén. A megerősített gazdagépek biztonságos kapcsolatot biztosítanak a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez. **Az Azure javasolja a VPN-vagy Azure ExpressRoute-kapcsolat konfigurálását a felügyeleti és adatimportálási szolgáltatásokhoz a hivatkozási architektúra alhálózatában.**

![IaaS webalkalmazás a FedRAMP hivatkozási architektúrájának diagramja](images/fedramp-iaaswa-architecture.png?raw=true "IaaS webalkalmazás a FedRAMP hivatkozási architektúrájának diagramja")

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei a [telepítési architektúra](#deployment-architecture) szakaszban találhatók.

- Azure Virtual Machines
    - (1) megerősített állomás (Windows Server 2016 Datacenter)
    - (2) Active Directory tartományvezérlő (Windows Server 2016 Datacenter)
    - (2) SQL Server fürtcsomópont (SQL Server 2017 a Windows Server 2016 rendszeren)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rendelkezésre állási csoportok
    - (1) Active Directory tartományvezérlők
    - (1) SQL-fürtcsomópontok
    - (1) Web/IIS
- Azure Virtual Network
    - (1)/16 virtuális hálózat
    - (5)/24 alhálózat
    - A DNS-beállítások mindkét tartományvezérlőre vannak beállítva
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway engedélyezve
        - tűzfal mód: megelőzés
        - szabály beállítása: OWASP 3,0
        - figyelő: 443-es port
- Azure Storage
    - (7) geo-redundáns Storage-fiókok
- Azure-beli Felhőbeli tanúsító
- Helyreállítási tár
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (naplók)

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

A következő szakasz részletesen ismerteti a fejlesztési és megvalósítási elemeket.

**Megerősített gazdagép**: A megerősített gazdagép az egyetlen olyan belépési pont, amely biztonságos kapcsolatot biztosít a rendszergazdák számára az üzembe helyezett erőforrások eléréséhez. A megerősített gazdagép NSG csak az 3389-es TCP-porton keresztüli kapcsolatokat engedélyezi az RDP-hez. Az ügyfelek tovább konfigurálhatják a megerősített gazdagépet a szervezeti rendszer korlátozási követelményeinek kielégítése érdekében.

### <a name="virtual-network"></a>Virtuális hálózat
Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: Ez a megoldás az erőforrásokat egy különálló webes alhálózattal, adatbázis-alhálózattal, Active Directory alhálózattal és egy virtuális hálózaton belüli felügyeleti alhálózattal telepíti. Az alhálózatokat az egyes alhálózatokon alkalmazott hálózati biztonsági csoportokra vonatkozó szabályok logikailag elkülönítik az alhálózatok közötti adatforgalom korlátozása érdekében, hogy csak a rendszer-és felügyeleti funkciókhoz szükségesek legyenek.

Tekintse meg a megoldással üzembe helyezett [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) konfigurációját. Az ügyfelek a fenti [dokumentáció](../../virtual-network/virtual-network-vnet-plan-design-arm.md) használatával is konfigurálhatják a hálózati biztonsági csoportokat.

Az alhálózatok mindegyike dedikált hálózati biztonsági csoporttal (NSG) rendelkezik:
- 1 NSG a következőhöz: Application Gateway (LBNSG)
- 1 NSG a megerősített gazdagéphez (MGTNSG)
- 1 NSG az elsődleges és a tartalék tartományvezérlők számára (ADNSG)
- 1 NSG az SQL-kiszolgálókhoz (SQLNSG)
- 1 NSG a webes rétegek számára (WEBNSG)

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó NSG.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra több titkosítási mérték használatával védi az inaktív adatok védelmét.

**Azure Storage**: Az inaktív adatok titkosítására vonatkozó követelmények teljesítéséhez minden Storage-fiók [Storage Service encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)használ.

**SQL Server**: A SQL Server [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)használatára van konfigurálva, amely az adatok és naplófájlok valós idejű titkosítását és visszafejtését hajtja végre a további információk védelme érdekében. A TDE biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.

Az ügyfelek az alábbi SQL Server biztonsági mértékeket is konfigurálhatják:
-   Az [AD-hitelesítés és-engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásának kezelését egy központi helyen.
-   Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
-   A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   Az [SQL](../../sql-database/sql-database-threat-detection.md) -veszélyforrások észlelése lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
-   [Always encrypted oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szövegként az adatbázis-rendszeren belül. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
-   [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) a hivatkozási architektúra telepítése után végezhető el. Az ügyfeleknek módosítaniuk kell a dinamikus adatmaszkolási beállításokat az adatbázis-sémájuk betartásához.

**Azure Disk Encryption**: A Azure Disk Encryption a Windows IaaS virtuálisgép-lemezek titkosítására szolgál. [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Azure Key Vault a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

### <a name="identity-management"></a>Identitáskezelés

Az alábbi technológiák az Azure-környezet Identitáskezelés-kezelési funkcióit biztosítják:
- [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelési szolgáltatása.
- Az ügyfél által üzembe helyezett webalkalmazások hitelesítése az Azure AD használatával végezhető el. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).  
- Az [Azure szerepköralapú Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) lehetővé teszi az Azure-hoz való, pontosan célzott hozzáférés-vezérlést. Az előfizetés-hozzáférés az előfizetés-adminisztrátorra korlátozódik, és az erőforrásokhoz való hozzáférés a felhasználói szerepkör alapján korlátozott lehet.
- A központilag telepített IaaS Active Directory példányok az operációs rendszer szintjén üzembe helyezett IaaS-alapú virtuális gépek számára biztosítanak Identitáskezelés-kezelést.

### <a name="security"></a>Biztonság
**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Azure Key Vault segítségével kezelheti a IaaS virtuális gép lemez-titkosítási kulcsait és a titkokat ehhez a hivatkozási architektúrához.

**Javítási felügyelet**: A Azure Security and Compliance Blueprint Automation által központilag telepített Windows rendszerű virtuális gépek alapértelmezés szerint úgy vannak konfigurálva, hogy Windows Update szolgáltatásból fogadják az automatikus frissítéseket. Ezzel a megoldással a Azure Automation megoldást is üzembe helyezheti, amelyen keresztül a frissítési központi telepítések a Windows rendszerű kiszolgálókon a szükséges javítások telepítéséhez hozhatók létre.

**Kártevők elleni védelem**: A Virtual Machines rendszerhez készült [Microsoft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) olyan valós idejű védelmi képességet biztosít, amely lehetővé teszi a vírusok, kémprogramok és más kártékony szoftverek azonosítását és eltávolítását, amelyekkel konfigurálható riasztások állíthatók be, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik Telepítsen vagy futtasson védett virtuális gépeken.

**Application Gateway**: Az architektúra csökkenti a biztonsági rések kockázatát a webalkalmazási tűzfallal (WAF) rendelkező Application Gateway és az engedélyezett OWASP-alapú szabályrendszert használatával. A további funkciók a következők:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL](../../application-gateway/create-ssl-portal.md) -kiszervezés engedélyezése
- [A TLS 1.0-s és 1.1-es verziójának](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) letiltása
- [Webalkalmazási tűzfal](../../application-gateway/waf-overview.md) (WAF mód)
- [Megelőzési mód](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) OWASP 3,0-es szabályrendszert

### <a name="business-continuity"></a>Az üzletmenet folytonossága

**Magas rendelkezésre állás**: A tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető a 99,95%-os Azure SLA-val. A megoldás üzembe helyezi az összes webes és adatsorozatú virtuális gépet egy [rendelkezésre állási](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)csoportban. A rendelkezésre állási csoportok biztosítják, hogy a virtuális gépek több elkülönített hardveres fürt között legyenek elosztva a rendelkezésre állás javítása érdekében. Emellett a megoldás a SQL Server virtuális gépeket egy rendelkezésre állási csoportban helyezi üzembe [AlwaysOn rendelkezésre állási csoportként](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Az Always On rendelkezésre állási csoport a magas rendelkezésre állást és a vész-helyreállítási képességeket biztosítja.

**Recovery Services**tároló: Az [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) -tároló a biztonsági mentési és az Azure-Virtual Machines összes konfigurációját védi ebben az architektúrában. Recovery Services-tárolóval az ügyfelek a teljes virtuális gép visszaállítása nélkül állíthatják vissza a fájlokat és mappákat egy IaaS virtuális gépről, így a gyorsabb visszaállítási idő is megadható.

**Felhőbeli tanúsító**: [](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) A Felhőbeli tanúsító a feladatátvevő fürt Kvórumának tanúsító típusa a Windows Server 2016-ben, amely az Azure-t használja választottbírósági pontként. A felhő tanúsító, mint bármely más kvórum, szavaz, és részt vehet a kvórum számításaiban, de a szabványos nyilvánosan elérhető Azure-Blob Storage használja. Ez kiküszöböli a nyilvános felhőben üzemeltetett virtuális gépek extra karbantartási terhelését.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

Azure Monitor naplók széles körű naplózást biztosítanak a rendszer és a felhasználók tevékenységéről, valamint a rendszer állapotáról. A [Azure monitor naplók](../azure-security-disk-encryption-overview.md) megoldás az Azure-ban és a helyszíni környezetekben található erőforrások által generált adatokat gyűjti és elemzi.

- **Tevékenységek naplói:**  A [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók:**  A [diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md) minden erőforrás által kibocsátott naplók. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak.
- **Napló archiválása:**  Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek. Ezek a naplók Azure Monitor naplókat csatlakoznak a feldolgozáshoz, tároláshoz és irányítópult-jelentéskészítéshez.

Emellett az alábbi figyelési megoldások is települnek ennek az architektúrának a részeként. Vegye figyelembe, hogy az ügyfél felelőssége, hogy konfigurálja ezeket a megoldásokat a FedRAMP biztonsági vezérlőkkel való összehangoláshoz:
-   [Ad Assessment](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Az antimalware-megoldás a kártevők, fenyegetések és védelem állapotáról nyújt jelentéseket.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A Azure Automation megoldás tárolja, futtatja és felügyeli a runbookok.
-   [Security and Audit](../../security-center/security-center-intro.md): A Security and Audit irányítópult magas szintű betekintést nyújt az erőforrások biztonsági állapotára azáltal, hogy mérőszámokat biztosít a biztonsági tartományokra, a jelentős problémákra, az észlelésekre, a fenyegetések felderítésére és az általános biztonsági lekérdezésekre.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
-   [Update Management](../../automation/automation-update-management.md): Az Update Management megoldás lehetővé teszi az operációs rendszer biztonsági frissítéseinek az ügyfelek általi felügyeletét, beleértve az elérhető frissítések állapotát, valamint a szükséges frissítések telepítésének folyamatát.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Azure-tevékenység naplói](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.
-   [Change Tracking](../../azure-monitor/platform/collect-activity-logs.md): A Change Tracking megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosítsák a környezet változásait.

**A Azure monitor**
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segít a felhasználóknak a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszik a szervezetek számára a naplózást, a riasztások létrehozását és az adatok archiválását, beleértve az ügyfelek Azure-erőforrásaiban

## <a name="threat-model"></a>Veszélyforrások modellje
Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/fedrampWAdfd) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![IaaS webalkalmazás a FedRAMP Threat modelhez](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS webalkalmazás a FedRAMP Threat modelhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

A [Azure Security and Compliance Blueprint-FedRAMP magas szintű vevői felelősségi mátrixa](https://aka.ms/blueprinthighcrm) felsorolja a FedRAMP magas alapkonfigurációja által igényelt összes biztonsági ellenőrzést. A mátrix azt jelzi, hogy az egyes vezérlőelemek megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

A [Azure Security and Compliance Blueprint-FedRAMP IaaS webalkalmazás magas szintű vezérlésének megvalósítási mátrixa](https://aka.ms/blueprintwacim) felsorolja a FedRAMP magas alapkonfigurációja által igényelt összes biztonsági ellenőrzést. A mátrix olyan információkat nyújt, amelyekkel a IaaS webalkalmazás-architektúrája szabályozza a vezérlőket, beleértve a részletes leírását is, hogy a megvalósítás hogyan teljesíti az egyes kezelt vezérlők követelményeit.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Ez az Azure Security and Compliance Blueprint-automatizálás olyan JSON-konfigurációs fájlokból és PowerShell-parancsfájlokból áll, amelyeket a Azure Resource Manager API-szolgáltatása kezel, hogy erőforrásokat helyezzen üzembe az Azure-ban. A részletes üzembe helyezési utasítások [itt](https://aka.ms/fedrampblueprintrepo)érhetők el.
> [!NOTE]
> Ez a megoldás központilag telepíti Azure Government.

#### <a name="quickstart"></a>Gyors üzembe helyezés
1. A GitHub- [](https://aka.ms/fedrampblueprintrepo) tárház klónozása vagy letöltése a helyi munkaállomásra.

2. Futtassa az üzembe helyezés előtti PowerShell-parancsfájlt: Azure-Blueprint/előtelepítés/Orchestration_InitialSetup. ps1.

3. Kattintson az alábbi gombra, jelentkezzen be a Azure Portalba, adja meg a szükséges ARM-sablon paramétereit, majd kattintson a **vásárlás**elemre.

    [![Üzembe helyezés az Azure-ban](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

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
