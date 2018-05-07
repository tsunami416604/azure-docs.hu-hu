---
title: Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások automatizálás
description: Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások automatizálás
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 28da21ad962b29810a3c4dace9fba8b1a96799f1
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások automatizálás

## <a name="overview"></a>Áttekintés

A [Szövetségi kockázat és engedélyezési felügyeleti Program (FedRAMP)](https://www.fedramp.gov), egy Egyesült államokbeli kormányzati kiterjedő program, amely a biztonsági értékelést, engedélyezési és folyamatos figyelés szabványosított megközelítését ismerteti a felhő termékeket és szolgáltatások. Az Azure biztonsági és megfelelőségi tervezetének Automation útmutatással szolgál a központi telepítésben FedRAMP-kompatibilis infrastruktúra egy egy egyszerű Internet felé néző webes alkalmazáshoz megfelelő szolgáltató (IaaS) környezetben is. Ez a megoldás automatizálja az üzembe helyezési és konfigurálási egy közös referencia-architektúrában, amelyben ügyfelek megfelel konkrét biztonsági és megfelelőségi követelmények és szolgál az ügyfelek számára a build alapjaként módon, amely tartalmazza az Azure-erőforrások és a saját megoldások konfigurálása az Azure-on. A megoldás a FedRAMP nagy alaptervből, NIST SP 800-53 alapján szabályozza egy részhalmazát valósítja meg. FedRAMP nagy követelmények és a megoldás kapcsolatos további információkért lásd: [FedRAMP magas követelmények – magas szintű áttekintés](fedramp-iaaswa-controls-overview.md). ***Megjegyzés: Ez a megoldás Azure Government telepíti.***

Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben. Erre a környezetre módosítás nélkül az alkalmazások központi telepítése nincs teljesen teljesítik a FedRAMP nagy alapterv követelményeinek. Vegye figyelembe a következőket:
- Ezen architektúra FedRAMP megfelelő módon használja a Azure ügyfelek alapterv.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi vizsgálata használatával készített ebbe az architektúrába követelmények változhatnak megoldások a részletekről az egyes ügyfelek általi alapján. 

Nézze meg ezt a gyors áttekintést a megoldás működéséről, [videó](https://aka.ms/fedrampblueprintvideo) foglalja össze, és a központi telepítés bemutatására.

Kattintson a [Itt](https://aka.ms/fedrampblueprintrepo) üzembe helyezési utasítások.

## <a name="solution-components"></a>Megoldás-összetevők

Az Azure biztonsági és megfelelőségi tervezetének Automation automatikusan telepíti az infrastruktúra-szolgáltatási webes alkalmazás referencia-architektúrában előre beállított biztonsági vezérlőkkel ügyfelek FedRAMP követelményeknek való megfelelés eléréséhez. A megoldás Azure Resource Manager-sablonok és az erőforrás üzembe helyezési és konfigurációs útmutató PowerShell-parancsfájlok áll. Kísérő [megfelelőségi dokumentáció](#compliance-documentation) biztosított, biztonsági vezérlő öröklési Azure és a telepített erőforrások és a konfigurációkat, amely megfelel-e NIST SP 800-53 biztonsági vezérlők jelző, ezáltal engedélyezése gyorsított kötelezettségek szervezetek.

## <a name="architecture-diagram"></a>Architektúradiagram

Ez a megoldás egy referencia-architektúrában, az infrastruktúra-szolgáltatási webalkalmazás egy adatbázis-háttérrendszerrel telepíti. Az architektúra egy webes réteghez, az adatok réteg, Active Directory-infrastruktúrát, Alkalmazásátjáró, és a belső terheléselosztót. A webes és adatok rétegek telepített virtuális gépek rendelkezésre állási csoportok vannak konfigurálva, és az SQL Server-példányokat a magas rendelkezésre állású AlwaysOn rendelkezésre állási csoportban vannak konfigurálva. Virtuális gépek a tartományhoz, és az Active Directory-csoportházirendek segítségével az operációs rendszer szintjén és megfelelőségi beállítások kényszerítése. A felügyeleti jumpbox (megerősített állomás) biztonságos kapcsolatot biztosít a rendszergazdák központilag telepített hozzáférését az erőforrásokhoz.

![helyettesítő szöveg](images/fedramp-iaaswa-architecture.png?raw=true "Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások automatizálás")

A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

* **Azure Virtual Machines**
    - (1) felügyeleti/megerősített (Windows Server 2016 Datacenter)
    - (2) az active Directory-tartományvezérlőhöz (Windows Server 2016 Datacenter)
    - (2) az SQL Server-fürt csomópontjának (SQL Server 2016-os Windows Server 2012 R2 rendszeren)
    - (1) az SQL Server tanúsító (Windows Server 2016 Datacenter)
    - (2) webkiszolgáló/IIS (Windows Server 2016 Datacenter)
* **Rendelkezésre állási csoportok**
    - (1) az active Directory-tartományvezérlők
    - (1) SQL fürtcsomópontok és a tanúsító
    - (1) webkiszolgáló/IIS
* **Azure Virtual Network**
    - ((1) /16 virtuális hálózatok
    - (5) /24 alhálózatok
    - DNS-beállítások mindkét tartományvezérlőn van beállítva
* **Azure Load Balancer**
    - (1) SQL terheléselosztó
* **Az Azure alkalmazás átjáró**
    - (1) az Alkalmazásátjáró WAF engedélyezve
      - Tűzfal-mód: megelőzése
      - Szabálykészlet: OWASP 3.0
      - Figyelő: 443-as Port
* **Azure Storage**
    - (7) georedundáns storage-fiókok
* **Azure Backup**
    - (1) a recovery Services-tároló
* **Azure Key Vault**
    - (1) kulcstároló
* **Azure Active Directory**
* **Azure Resource Manager**
* **Azure Log Analytics**
    - (1) a Naplóelemzési munkaterület
* **Azure Automation**
    - (1) automation-fiók

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

Az alábbi szakasz részletesen fejlesztése és megvalósítása elemek.

### <a name="network-segmentation-and-security"></a>Hálózati szegmentálást és biztonság

#### <a name="application-gateway"></a>Application Gateway

Az architektúra csökkenti a biztonsági réseket Alkalmazásátjáró webalkalmazási tűzfal (waf-ot), és a OWASP szabálykészletben engedélyezve van. További funkciók a következők:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Engedélyezése [SSL kiürítése](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Tiltsa le a [TLS 1.0 és 1.1 verzió](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF mód)
- [Megelőző módja](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) rendelkező OWASP 3.0 szabálykészletben

#### <a name="virtual-network"></a>Virtuális hálózat

Az architektúra egy címterében 10.200.0.0/16 titkos virtuális hálózat határozza meg.

#### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Ez a megoldás egy külön webkiszolgáló alhálózati, adatbázis alhálózati, Active Directory-alhálózathoz és felügyeleti alhálózaton belül egy virtuális hálózati architektúrát erőforrások telepíti. Alhálózatok logikailag elválasztott hálózati biztonsági csoport szabályokat az egyes alhálózatok korlátozzák a forgalmat, amely csak szükséges rendszer- és felügyeleti funkciókat az alhálózatok között.

Tekintse meg a konfigurációs [hálózati biztonsági csoportok](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) Ez a megoldás üzembe helyezéséhez. A szervezetek konfigurálhatja a hálózati biztonsági csoportok fent fájl szerkesztésével [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) útmutatóként.

Egyes alhálózatok rendelkezik egy dedikált hálózati biztonsági csoport (NSG):
- Az Alkalmazásátjáró (LBNSG) 1 NSG
- 1 NSG Jumpbox (MGTNSG)
- Az elsődleges és tartalék tartományvezérlő (ADNSG) 1 NSG
- Az SQL Server-kiszolgálók és a tanúsító fájlmegosztás (SQLNSG) 1 NSG
- 1 NSG a webes réteg (WEBNSG)

#### <a name="subnets"></a>Alhálózatok

Az egyes alhálózatokon nem a megfelelő NSG tartozik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra inaktív adatok több titkosítási mértékek használatával védi.

#### <a name="azure-storage"></a>Azure Storage

Az összes tárfiók használata adatokat nyugalmi titkosítási követelmények teljesítéséhez [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>SQL Database

SQL-adatbázis használatára van konfigurálva [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), amely végrehajtja a valós idejű titkosítási és visszafejtési adatainak és naplókönyvtárainak fájlok aktívan információk védelme érdekében. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Az Azure Disk Encryption titkosított Windows IaaS virtuális gépek lemezeit szolgál. [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows operációs rendszer és az adatlemezek kötettitkosítást biztosít a BitLocker-szolgáltatás. A megoldás integrálva van az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok kezeléséhez.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

[Naplófájl Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) rendszer és felhasználói tevékenységek, valamint a rendszerállapot-részletes naplózást végez. 

- **Tevékenységi naplóit:**[tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) Észreveheti az olyan erőforrást az előfizetésében a végrehajtott műveletek.
- **Diagnosztikai naplók:**[diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplófájlt. Ezek a naplók tartalmazzák a Windows rendszer-eseménynaplói, az Azure storage naplókat, Key Vault-naplók és Alkalmazásátjáró hozzáférés és a tűzfalon naplókat.
- **Napló archiválás:** Azure tevékenységi naplóit, és diagnosztikai naplók csatlakozhat az Azure Naplóelemzés szolgáltatáshoz feldolgozási, tárolására és dashboarding. Ez felhasználó által konfigurálható mentése 730 nap szervezet-specifikus megőrzési követelményeinek megfelelően.

### <a name="secrets-management"></a>Titkos kulcsok kezelése

A megoldás az Azure Key Vault használ a kulcsok és titkos kulcsok kezeléséhez.

- [Az Azure Key Vault](https://azure.microsoft.com/services/key-vault/) segítségével megakadályozhatja a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos. 
- A megoldás integrálva van az Azure Key Vault infrastruktúra-szolgáltatási virtuális gép lemez-titkosítási kulcsok és titkos kulcsok kezeléséhez.

### <a name="identity-management"></a>Identitáskezelés

A következő technológiákat identitás biztosítása a felügyeleti képességek az Azure környezetben.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás.
- Egy ügyfél telepített webalkalmazás-hitelesítés használatával végezheti el az Azure AD. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) lehetővé teszi, hogy pontosan célzott hozzáférés-kezelés az Azure-bA. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként, és az erőforrásokhoz való hozzáférés korlátozható a felhasználói szerepkör alapján.
- Egy telepített IaaS Active Directory példánnyal üzembe helyezett infrastruktúra-szolgáltatási virtuális gépeknél operációsrendszer-szintű identitás-felügyeletet biztosít.
   
### <a name="compute-resources"></a>Számítási erőforrások

#### <a name="web-tier"></a>Webes réteg

A megoldás webes réteg virtuális gépeihez telepíti egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Rendelkezésre állási készletek győződjön meg arról, hogy a virtuális gépek rendelkezésre állásának javítása érdekében több elkülönített hardver fürt különböző pontjain.

#### <a name="database-tier"></a>Adatbázis-rétegből

A megoldás adatbázis réteg virtuális gépein, mint a rendelkezésre állási csoport a telepíti egy [AlwaysOn rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Az Always On rendelkezésre állási csoport funkciót a magas rendelkezésre állású és vész-helyreállítási lehetőségeket biztosít. 

#### <a name="active-directory"></a>Active Directory

A megoldás által telepített összes virtuális gépet a tartományhoz, és az Active Directory-csoportházirendek segítségével az operációs rendszer szintjén és megfelelőségi beállítások kényszerítése. Active Directory virtuális gépek rendelkezésre állási csoport lettek telepítve.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (megerősített gazdagép)

A felügyeleti jumpbox (megerősített állomás) biztonságos kapcsolatot biztosít a rendszergazdák központilag telepített hozzáférését az erőforrásokhoz. A felügyeleti alhálózathoz, ahol a jumpbox virtuális gép is található társított NSG lehetővé teszi, hogy a kapcsolatok csak a 3389-es TCP-port RDP. 

### <a name="malware-protection"></a>Kártevők elleni védekezés

[A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) a virtuális gépek, amelyek segítségével azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek valós idejű védelem-képességet biztosít, konfigurálható riasztást küld, ha ismert kártevő vagy nemkívánatos szoftver próbálja meg telepíteni vagy futtatni védett virtuális gépet.

### <a name="patch-management"></a>A javítások

Az Azure biztonsági és megfelelőségi tervezetének automatizálás által telepített Windows virtuális gépek automatikus frissítések kap a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is telepíti az Azure Automation-megoldást, amelyen keresztül a központi telepítést is létrehozható kiszolgálókra történő központi telepítéséhez javítások Windows szükség esetén.

### <a name="operations-management"></a>Operatív ügyek

#### <a name="log-analytics"></a>Log Analytics

[Naplófájl Analytics](https://azure.microsoft.com/services/log-analytics/) egy szolgáltatás, amely lehetővé teszi, hogy gyűjtése és elemzése az Azure-erőforrások által létrehozott adatok és a helyszíni környezetben.

#### <a name="management-solutions"></a>Felügyeleti megoldások

A következő megoldások előre telepítve van a megoldás részeként:
- [AD-elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Kártevőirtók felmérése](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Biztonság és naplózás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL-elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Az ügynök állapota](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Változáskövetés](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

### <a name="customer-responsibility-matrix"></a>Ügyfél felelősségi mátrix

A [ügyfél feladatkörei mátrix](https://aka.ms/blueprinthighcrm) (Excel-munkafüzet) felsorolja a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix jelöli, minden vezérlő (vagy ellenőrzési alrész), hogy implementációja osztott vezérlő feladata a Microsoft, az ügyfél vagy a kettő között megosztott. 

### <a name="control-implementation-matrix"></a>Ellenőrzés végrehajtása mátrix

A [vezérlés megvalósítása mátrix](https://aka.ms/blueprintwacim) (Excel-munkafüzet) felsorolja a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. A mátrix jelöli, az egyes vezérlő (vagy ellenőrzési alrész), amely van kijelölve egy ügyfél-osztott az ügyfél feladatkörei mátrixban, 1.) Ha a tervezetének automation valósítja meg a vezérlő, valamint (2) egy leírása hogyan végrehajtása igazodik a vezérlő requirement(s). Ez a tartalom is rendelkezésre áll [Itt](fedramp-iaaswa-controls-overview.md).

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az Azure biztonsági és megfelelőségi tervezetének Automation JSON-konfigurációs fájlok és az Azure erőforrások telepítése Azure Resource Manager API szolgáltatás által kezelt PowerShell-parancsfájlok áll. Részletes üzembe helyezési utasítások érhetők el [Itt](https://aka.ms/fedrampblueprintrepo). ***Megjegyzés: Ez a megoldás Azure Government telepíti.***

#### <a name="quickstart"></a>Első lépések
1. Klónozza vagy letöltése [ez](https://aka.ms/fedrampblueprintrepo) GitHub-tárházban történő helyi munkaállomáson.

2. A központi telepítés előtti PowerShell-parancsprogrammal: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Az alábbi gombra kattintva, jelentkezzen be az Azure-portálra, adja meg a szükséges ARM-sablon paramétereket, és kattintson **beszerzési**.

    [![Üzembe helyezés az Azure-ban](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Jogi nyilatkozat

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.  
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.  
- Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.  
- Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.  
- Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
- Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
