---
title: Azure-SSIS Integration Runtime csatlakoztatása virtuális hálózathoz
description: Ismerje meg, hogyan csatlakozhat egy Azure-SSIS-integrációs futásidőhez egy Azure virtuális hálózathoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 4819eaf2a65cf542029cf36f262d0cea5be75f2e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521941"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS Integration Runtime csatlakoztatása virtuális hálózathoz

Ha az Azure Data Factoryban használja az SQL Server Integration Services (SSIS) szolgáltatást, a következő esetekben kell csatlakoznia az Azure-SSIS-integrációs futásidejéhez (IR) egy Azure virtuális hálózathoz:

- Az Azure-SSIS-kapcsolatkezelő hálózaton futó SSIS-csomagok ból helyszíni adattárakhoz szeretne csatlakozni anélkül, hogy konfigurálna vagy kezelne egy saját üzemeltetésű infravörös hitelesítést proxyként. 

- SSIS katalógus-adatbázist (SSISDB) szeretne üzemeltetni egy Azure SQL-adatbázisban, IP tűzfalszabályokkal/virtuális hálózati szolgáltatás végpontokkal, vagy egy felügyelt példányt magánvégfelhasználóval. 

- Az Azure-SSIS IR-n futó SSIS-csomagok virtuális hálózati szolgáltatásvégpontjaival konfigurált Azure-erőforrásokhoz szeretne csatlakozni.

- Az Azure-SSIS IR-n futó SSIS-csomagokból IP-tűzfalszabályokkal konfigurált adattárakhoz/erőforrásokhoz szeretne csatlakozni.

A Data Factory lehetővé teszi, hogy csatlakozzon az Azure-SSIS IR-hez egy klasszikus üzembe helyezési modellvagy az Azure Resource Manager telepítési modell segítségével létrehozott virtuális hálózathoz.

> [!IMPORTANT]
> A klasszikus virtuális hálózat elavult, ezért használja az Azure Resource Manager virtuális hálózat helyett.  Ha már használja a klasszikus virtuális hálózatot, a lehető leghamarabb váltson át az Azure Resource Manager virtuális hálózatra.

Az [Azure-SQL Server Integration Services (SSIS) integrációs futásidejű (IR) konfigurálása egy virtuális hálózati oktatóanyaghoz való csatlakozáshoz](tutorial-deploy-ssis-virtual-network.md) az Azure Portalon keresztül a minimális lépéseket mutatja. Ez a cikk kibővíti az oktatóanyagot, és ismerteti az összes választható feladatot:

- Ha virtuális hálózatot használ (klasszikus).
- Ha saját nyilvános IP-címeket hoz az Azure-SSIS IR-hez.
- Ha saját DNS-kiszolgálót használ.
- Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton.
- Ha az Azure ExpressRoute vagy a felhasználó által meghatározott útvonal (UDR).
- Ha testre szabott Azure-SSIS IR.If you use customized Azure-SSIS IR.
- Ha az Azure Powershell kiépítése.

## <a name="access-to-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárakhoz

Ha az SSIS-csomagok hozzáférnek a helyszíni adattárakhoz, csatlakozhat az Azure-SSIS ir-hez egy virtuális hálózathoz, amely a helyszíni hálózathoz csatlakozik. Vagy konfigurálhatja és kezelheti a saját üzemeltetésű ir proxyként az Azure-SSIS IR. További információ: [A saját üzemeltetésű infravörös kapcsolat konfigurálása az Azure-SSIS ir proxyjaként](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)című témakörben található. 

Amikor az Azure-SSIS ir-t egy virtuális hálózathoz csatlakozik, ne feledje az alábbi fontos pontokat: 

- Ha nincs virtuális hálózat csatlakozik a helyszíni hálózathoz, először hozzon létre egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) az Azure-SSIS IR-hez való csatlakozáshoz. Ezután konfiguráljon egy helyek közötti [VPN-átjáró-kapcsolatot](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) vagy [ExpressRoute-kapcsolatot](../expressroute/expressroute-howto-linkvnet-classic.md) a virtuális hálózatról a helyszíni hálózatra. 

- Ha egy Azure Resource Manager virtuális hálózat már csatlakozik a helyszíni hálózathoz ugyanazon a helyen, mint az Azure-SSIS IR, csatlakozhat az infravörös, hogy a virtuális hálózat. 

- Ha egy klasszikus virtuális hálózat már csatlakozik a helyszíni hálózathoz az Azure-SSIS IR-től eltérő helyen, létrehozhat egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) az Azure-SSIS IR-hez való csatlakozáshoz. Ezután konfigurálja a [klasszikus-Azure Resource Manager virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat. 
 
- Ha egy Azure Resource Manager virtuális hálózat már csatlakozik a helyszíni hálózathoz az Azure-SSIS IR-től eltérő helyen, először létrehozhat egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) az Azure-SSIS IR-hez való csatlakozáshoz. Ezután konfigurálja az Azure Resource Manager-to-Azure Resource Manager virtuális hálózati kapcsolatot. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Az SSIS-katalógus üzemeltetése az SQL Database-ben

Ha az SSIS-katalógust egy virtuális hálózati szolgáltatás-végpontokkal rendelkező Azure SQL-adatbázisban üzemelteti, győződjön meg arról, hogy csatlakozik az Azure-SSIS IR-hez ugyanahhoz a virtuális hálózathoz és alhálózathoz.

Ha az SSIS-katalógust egy magánvégfelhasználói ponttal rendelkező felügyelt példányban üzemelteti, győződjön meg arról, hogy csatlakozik az Azure-SSIS IR-hez ugyanahhoz a virtuális hálózathoz, de a felügyelt példánytól eltérő alhálózatban. Az Azure-SSIS ir-hez való csatlakozáshoz a felügyelt példánytól eltérő virtuális hálózathoz való csatlakozáshoz javasoljuk a virtuális hálózati társviszony-létesítést (amely csak ugyanabban a régióban van), vagy a virtuális hálózatés a virtuális hálózat között lévő kapcsolat. További információt az [Alkalmazás csatlakoztatása az Azure SQL Database felügyelt példányához](../sql-database/sql-database-managed-instance-connect-app.md)című témakörben talál.

## <a name="access-to-azure-services"></a>Hozzáférés az Azure-szolgáltatásokhoz

Ha az SSIS-csomagok hozzáférnek a [virtuális hálózati szolgáltatás végpontjait](../virtual-network/virtual-network-service-endpoints-overview.md) támogató Azure-erőforrásokhoz, és biztosítani szeretné a hozzáférést ezekhez az erőforrásokhoz az Azure-SSIS IR-ből, csatlakozhat az Azure-SSIS IR-hez egy virtuális hálózati végpontokhoz konfigurált virtuális hálózati alhálózathoz, majd hozzáadhat egy virtuális hálózati szabályt a megfelelő Azure-erőforrásokhoz, hogy lehetővé tegye a hozzáférést ugyanabból az alhálózatból.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Az IP tűzfalszabály által védett adatforrásokhoz való hozzáférés

Ha az SSIS-csomagok olyan adattárakhoz/erőforrásokhoz férnek hozzá, amelyek csak meghatározott statikus nyilvános IP-címeket engedélyeznek, és biztosítani szeretné a hozzáférést ezekhez az erőforrásokhoz az Azure-SSIS IR-ből, akkor az Azure-SSIS IR saját [nyilvános IP-címeit](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) is létrehozhatja, miközben csatlakozik egy virtuális hálózathoz, majd hozzáadhat egy IP-tűzfalszabályt a megfelelő erőforrásokhoz, hogy hozzáférést biztosítson az ip-címekről.

A virtuális hálózat minden esetben csak az Azure Resource Manager telepítési modelljén keresztül telepíthető.

A következő szakaszok további részleteket tartalmaznak. 

## <a name="virtual-network-configuration"></a>Virtuális hálózat konfigurációja

Állítsa be a virtuális hálózatot az alábbi követelményeknek megfelelően: 

- Győződjön `Microsoft.Batch` meg arról, hogy egy regisztrált szolgáltató az Azure-SSIS IR-t üzemeltető virtuális hálózati alhálózat előfizetése alatt. Ha klasszikus virtuális hálózatot használ, csatlakozzon `MicrosoftAzureBatch` az adott virtuális hálózat klasszikus virtuálisgép-közreműködői szerepköréhez is. 

- Ellenőrizze, hogy rendelkezik-e a szükséges engedélyekkel. További információt az Engedélyek beállítása című témakörben [talál.](#perms)

- Válassza ki a megfelelő alhálózatot az Azure-SSIS IR üzemeltetéséhez. További információt [az Alhálózat kiválasztása](#subnet)című témakörben talál. 

- Ha saját nyilvános IP-címeket hoz az Azure-SSIS IR-hez, [olvassa el a Statikus nyilvános IP-címek kiválasztása című témakört.](#publicIP)

- Ha saját DNS-kiszolgálót használ a virtuális hálózaton, olvassa el [a DNS-kiszolgáló beállítása című témakört.](#dns_server) 

- Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton, olvassa el [az NSG beállítása](#nsg)című témakört. 

- Ha Az Azure ExpressRoute vagy a felhasználó által meghatározott útvonal (UDR), [lásd: Azure ExpressRoute használata vagy UDR.](#route) 

- Győződjön meg arról, hogy a virtuális hálózat erőforrás-csoportja (vagy a nyilvános IP-címek erőforráscsoportja, ha saját nyilvános IP-címeket hoz létre és törölhet) hozhat létre és törölhet bizonyos Azure hálózati erőforrásokat. További információt [az Erőforráscsoport beállítása](#resource-group)című témakörben talál. 

- Ha az Azure-SSIS IR egyéni beállításában leírtak szerint testreszabja az [Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)IR-t, az Azure-SSIS IR-csomópontjai privát IP-címeket kapnak a 172.16.0.0 és 172.31.255.255 közötti előre meghatározott tartományból. Ezért győződjön meg arról, hogy a virtuális vagy helyszíni hálózatok privát IP-címtartományai nem ütköznek ezzel a hellyel.

Ez az ábra az Azure-SSIS IR szükséges kapcsolatait mutatja be:

![Azure-SSIS integrációs modul](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Engedélyek beállítása

Az Azure-SSIS infravörös szolgáltatást létrehozó felhasználónak a következő engedélyekkel kell rendelkeznie:

- Ha az SSIS ir-t egy Azure Resource Manager virtuális hálózathoz csatlakozik, két lehetősége van:

  - Használja a beépített hálózati közreműködői szerepkört. Ez a szerepkör a _\* Microsoft.Network/permission,amely_ a szükségesnél sokkal nagyobb hatókörrel rendelkezik.

  - Hozzon létre egy egyéni szerepkört, amely csak a szükséges _Microsoft.Network/virtualNetworks/\*/join/action_ engedélyt tartalmazza. Ha azt is szeretné, hogy a saját nyilvános IP-címeket az Azure-SSIS IR, miközben csatlakozik egy Azure Resource Manager virtuális hálózat, kérjük, a _Microsoft.Network/publicIPAddresses/*/join/action_ engedélyt is a szerepkörben.

- Ha csatlakozik az SSIS-kapcsolatszolgáltatáshoz egy klasszikus virtuális hálózathoz, javasoljuk, hogy használja a beépített klasszikus virtuálisgép-közreműködői szerepkört. Ellenkező esetben meg kell adnia egy egyéni szerepkört, amely tartalmazza a virtuális hálózathoz való csatlakozás engedélyezését.

### <a name="select-the-subnet"></a><a name="subnet"></a>Az alhálózat kijelölése

Alhálózat kiválasztásakor: 

- Ne válassza ki a GatewaySubnet egy Azure-SSIS IR üzembe helyezéséhez. Virtuális hálózati átjárók számára van elkülönben. 

- Győződjön meg arról, hogy a kiválasztott alhálózat elegendő címterülettel rendelkezik az Azure-SSIS ir használatához. Hagyja meg a rendelkezésre álló IP-címeket legalább az infravörös csomópont számának legalább kétszeresére. Az Azure az egyes alhálózatokon belül lefoglal néhány IP-címet. Ezek a címek nem használhatók. Az alhálózatok első és utolsó IP-címei a protokollmegfelelőség hez vannak fenntartva, és további három cím használatos az Azure-szolgáltatásokhoz. További információ: [Vannak-e korlátozások az IP-címek használatára ezeken az alhálózatokon belül?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Ne használjon olyan alhálózatot, amelyet kizárólag más Azure-szolgáltatások foglalnak el (például SQL Database által felügyelt példány, App Service és így tovább). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>A statikus nyilvános IP-címek kiválasztása

Ha saját statikus nyilvános IP-címeket szeretne biztosítani az Azure-SSIS IR számára, miközben egy virtuális hálózathoz csatlakozik, győződjön meg arról, hogy azok megfelelnek az alábbi követelményeknek:

- Pontosan két nem használt is, amelyek még nem társított más Azure-erőforrásokat kell biztosítani. Az extra lesz használva, amikor rendszeresen frissítjük az Azure-SSIS IR.The extra one will be used when we periodly upgrade your Azure-SSIS IR. Vegye figyelembe, hogy egy nyilvános IP-cím nem osztható meg az aktív Azure-SSIS irs között.

- Mindkettőnek szabványos típusú statikusnak kell lennie. További részletekért tekintse meg [a nyilvános IP-cím sk-jait.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)

- Mindkettőnek rendelkeznie kell DNS-névvel. Ha nem adott meg DNS-nevet azok létrehozásakor, ezt megteheti az Azure Portalon.

![Azure-SSIS integrációs modul](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Nekik és a virtuális hálózatnak ugyanabban az előfizetésben és ugyanabban a régióban kell lennie.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>A DNS-kiszolgáló beállítása 
Ha saját DNS-kiszolgálóját kell használnia az Azure-SSIS IR által csatlakozott virtuális hálózatban a privát állomásnév feloldásához, győződjön meg `<your storage account>.blob.core.windows.net`arról, hogy a globális Azure-állomásneveket is fel tudja oldani (például egy Azure Storage nevű blobot). 

Az egyik javasolt megközelítés az alábbi: 

-   Konfigurálja az egyéni DNS-t az Azure DNS-nek küldött kérelmek továbbítására. A feloldatlan DNS-rekordokat továbbíthatja az Azure rekurzív feloldóinak IP-címére (168.63.129.16) a saját DNS-kiszolgálóján. 

További információt a [Saját DNS-kiszolgálót használó Névfeloldás](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)című témakörben talál. 

> [!NOTE]
> Használjon teljesen minősített tartománynevet (FQDN) a saját állomásnevéhez, `<your_private_server>.contoso.com` például használja a `<your_private_server>`helyett, mivel az Azure-SSIS IR nem fogja automatikusan hozzáférni a saját DNS-utótagot.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>NSG beállítása
Ha nsg-t kell megvalósítania az Azure-SSIS IR által használt alhálózathoz, engedélyezze a bejövő és kimenő forgalmat a következő portokon keresztül: 

-   **Az Azure-SSIS IR bejövő követelménye**

| Irány | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
|---|---|---|---|---|---|---|
| Bejövő | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (ha csatlakozik az IR egy Resource Manager virtuális hálózat) <br/><br/>10100, 20100, 30100 (ha csatlakozik az IR egy klasszikus virtuális hálózat)| A Data Factory szolgáltatás ezeket a portokat használja az Azure-SSIS ir csomópontjaival való kommunikációhoz a virtuális hálózatban. <br/><br/> Függetlenül attól, hogy létrehoz-e egy alhálózati szintű NSG-t, a Data Factory mindig konfigurál egy NSG-t az Azure-SSIS IR-t üzemeltető virtuális gépekhez csatlakoztatott hálózati csatolókártyák (NIC-k) szintjén. Az adott NIC-szintű NSG csak a Data Factory IP-címeiből érkező bejövő forgalmat engedélyezi a megadott portokon. Még akkor is, ha megnyitja ezeket a portokat az internetes forgalom az alhálózati szinten, az IP-címek, amelyek nem Data Factory IP-címek blokkolva van a hálózati adapter szintjén. |
| Bejövő | TCP | CorpNetSaw között | * | VirtualNetwork | 3389 | (Nem kötelező) Ez a szabály csak akkor szükséges, ha a Microsoft támogatója megkéri az ügyfelet, hogy nyissa meg a speciális hibaelhárítást, és közvetlenül a hibaelhárítás után lezárható. **A CorpNetSaw** szolgáltatáscímke csak a Microsoft vállalati hálózatának munkaállomásai számára teszi lehetővé a távoli asztal használatát. Ez a szolgáltatáscímke nem választható ki a portálról, és csak az Azure PowerShell en vagy az Azure CLI-n keresztül érhető el. <br/><br/> NSG hálózati hálózati szintű szinten a 3389-es port alapértelmezés szerint nyitva áll, és lehetővé tesszük a 3389-es port vezérlését nsg alhálózati szinten, eközben az Azure-SSIS IR alapértelmezés szerint nem engedélyezte a 3389-es kimenő portot a Windows tűzfalszabályban minden infravörös csomóponton a védelem érdekében. |
||||||||

-   **Az Azure-SSIS IR kimenő követelménye**

| Irány | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
|---|---|---|---|---|---|---|
| Kimenő | TCP | VirtualNetwork | * | AzureCloud | 443 | Az Azure-SSIS-ir csomópontjai a virtuális hálózatban ezt a portot használják az Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs eléréséhez. |
| Kimenő | TCP | VirtualNetwork | * | Internet | 80 | (Nem kötelező) Az Azure-SSIS-ir csomópontjai a virtuális hálózatban ezen a porton keresztül letölthetik a visszavont tanúsítványok listáját az internetről. Ha blokkolja ezt a forgalmat, teljesítmény-visszaminősítést tapasztalhat az infravörös szolgáltatás indításakor, és elveszítheti a tanúsítvány-visszavonási lista tanúsítványhasználatának ellenőrzését. Ha bizonyos teljes tartománynokra szeretné szűkíteni a célhelyet, olvassa el **az Azure ExpressRoute vagy az UDR használata** című szakaszt.|
| Kimenő | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | (Nem kötelező) Ez a szabály csak akkor szükséges, ha az Azure-SSIS IR csomópontjai a virtuális hálózatban az SQL Database-kiszolgáló által üzemeltetett SSISDB-hez férnek hozzá. Ha az SQL Database Server kapcsolatházirendje **az átirányítás**helyett **proxy,** akkor csak az 1433-as portra van szükség. <br/><br/> Ez a kimenő biztonsági szabály nem alkalmazható a felügyelt példány által üzemeltetett SSISDB-re a virtuális hálózatban vagy a privát végpontokkal konfigurált Azure Database-kiszolgálón. |
| Kimenő | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Nem kötelező) Ez a szabály csak akkor szükséges, ha az Azure-SSIS IR csomópontjai a virtuális hálózatban egy SSISDB-t érnek el, amelyet a felügyelt példány üzemeltet a virtuális hálózatban vagy a privát végpontokkal konfigurált Azure Database-kiszolgálón. Ha az SQL Database Server kapcsolatházirendje **az átirányítás**helyett **proxy,** akkor csak az 1433-as portra van szükség. |
| Kimenő | TCP | VirtualNetwork | * | Storage | 445 | (Nem kötelező) Ez a szabály csak akkor szükséges, ha az Azure Files-ban tárolt SSIS-csomagot szeretné végrehajtani. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Azure ExpressRoute vagy UDR használata
Ha meg szeretné vizsgálni az Azure-SSIS IR-ből érkező kimenő forgalmat, az Azure-SSIS IR-ről kezdeményezett forgalmat az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kényszerítési bújtatásán keresztül (bgp-útvonal (0.0.0.0/0, a virtuális hálózat) vagy a hálózati virtuális berendezés (NVA) szolgáltatásba továbbíthatja tűzfalként vagy [Azure tűzfalon](https://docs.microsoft.com/azure/firewall/) keresztül [az UDRs-en](../virtual-network/virtual-networks-udr-overview.md)keresztül. 

![NVA-forgatókönyv az Azure-SSIS IR-hez](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Meg kell tennie az alábbiakban a dolgokat, hogy az egész forgatókönyv működik
   -   Az Azure Batch-kezelési szolgáltatások és az Azure-SSIS IR közötti bejövő forgalom nem irányítható tűzfalberendezésen keresztül.
   -   A tűzfalberendezésnek engedélyeznie kell az Azure-SSIS IR által igényelt kimenő forgalmat.

Az Azure Batch felügyeleti szolgáltatások és az Azure-SSIS IR közötti bejövő forgalom nem irányítható tűzfalberendezésre, különben a forgalom aszimmetrikus útválasztási probléma miatt megszakad. Az útvonalakat meg kell határozni a bejövő forgalomhoz, hogy a forgalom ugyanúgy válaszolhasson vissza, ahogyan bejött. Konkrét UDR-eket határozhat meg az Azure Batch felügyeleti szolgáltatások és az Azure-SSIS ir közötti forgalom irányításához a következő ugrástípussal, **mint internet.**

Ha `UK South` például az Azure-SSIS ir-kapcsolat a webhelyen található, és meg szeretné vizsgálni az Azure Tűzfalon `BatchNodeManagement.UKSouth` keresztülérkező kimenő forgalmat, akkor először a [szolgáltatáscímkék IP-tartományletöltési hivatkozásától](https://www.microsoft.com/download/details.aspx?id=56519) vagy a [Service Tag Discovery API-n](https://aka.ms/discoveryapi)keresztül kapja meg a szolgáltatáscímkék IP-címkéjének IP-tartománylistáját. Ezután alkalmazza a következő UD-eket a kapcsolódó IP-tartomány útvonalakhoz a következő ugrástípussal **internetként,** valamint a 0.0.0.0/0 útvonalat a következő ugrástípussal **virtuális berendezésként.**

![Az Azure Batch UDR-beállításai](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Ez a megközelítés további karbantartási költségekkel jár. Rendszeresen ellenőrizze az IP-tartományt, és adjon hozzá új IP-tartományokat az UDR-hez, hogy elkerülje az Azure-SSIS IR törését. Javasoljuk, hogy havonta ellenőrizze az IP-tartományt, mert amikor az új IP megjelenik a szolgáltatási címkében, az IP még egy hónapot vesz igénybe. 

Az UDR-szabályok beállításának megkönnyítése érdekében a következő Powershell-parancsfájl futtatásával vehet fel UDR-szabályokat az Azure Batch felügyeleti szolgáltatásokhoz:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

A tűzfalberendezés, amely lehetővé teszi a kimenő forgalmat, engedélyeznie kell a kimenő portok alatt megegyezik a követelmény nsg kimenő szabályok.
-   443-as port, amely nek az Azure Cloud-szolgáltatások célja.

    Ha az Azure Firewall, megadhatja a hálózati szabály Az AzureCloud service tag. A többi típusú tűzfal esetében egyszerűen engedélyezheti a célhelyet a 443-as porthoz, vagy engedélyezheti az Azure-környezet típusa alapján az alábbi teljes tartománynnokat:
    | Azure környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li><b>Azure Data Factory (kezelés)</b></li><li style="list-style-type:none"><ul><li>\*frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (kezelés)</b></li><li style="list-style-type:none"><ul><li>\*.blob.core.windows.net</li><li>\*table.core.windows.net</li></ul></li><li><b>Azure Container-beállításjegyzék (egyéni telepítés)</b></li><li style="list-style-type:none"><ul><li>\*azurecr.io</li></ul></li><li><b>Eseményközpont (naplózás)</b></li><li style="list-style-type:none"><ul><li>\*servicebus.windows.net</li></ul></li><li><b>Microsoft naplózási szolgáltatás (belső használat)</b></li><li style="list-style-type:none"><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (kezelés)</b></li><li style="list-style-type:none"><ul><li>\*frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (kezelés)</b></li><li style="list-style-type:none"><ul><li>\*blob.core.usgovcloudapi.net</li><li>\*table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container-beállításjegyzék (egyéni telepítés)</b></li><li style="list-style-type:none"><ul><li>\*azurecr.us</li></ul></li><li><b>Eseményközpont (naplózás)</b></li><li style="list-style-type:none"><ul><li>\*servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft naplózási szolgáltatás (belső használat)</b></li><li style="list-style-type:none"><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (kezelés)</b></li><li style="list-style-type:none"><ul><li>\*frontend.datamovement.azure.cn.</li></ul></li><li><b>Azure Storage (kezelés)</b></li><li style="list-style-type:none"><ul><li>\*blob.core.chinacloudapi.cn</li><li>\*table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container-beállításjegyzék (egyéni telepítés)</b></li><li style="list-style-type:none"><ul><li>\*azurecr.cn</li></ul></li><li><b>Eseményközpont (naplózás)</b></li><li style="list-style-type:none"><ul><li>\*servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft naplózási szolgáltatás (belső használat)</b></li><li style="list-style-type:none"><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul>

    Ami az Azure Storage, az Azure Container Registry és az Event Hub teljes tartományszámait illeti, a következő szolgáltatásvégpontok engedélyezését is engedélyezheti a virtuális hálózathoz, hogy az ezekre a végpontokra irányuló hálózati forgalom az Azure gerinchálózatán keresztül haladjon át, ahelyett, hogy a tűzfalberendezésre irányítanák:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   80-as port, amelynek célállomása crl letöltési hely.

    Az alábbiakban engedélyezheti a visszavont tanúsítványok (tanúsítvány-visszavonási lista) tanúsítványainak letöltési webhelyeit az Azure-SSIS infravörös kezelési célokra:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Ha eltérő visszavonási tanúsítvánnyal rendelkező tanúsítványokat használ, javasoljuk, hogy azokat is tartalmazza. Ezt a [tanúsítvány-visszavonási listából](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)olvasható.

    Ha letiltotta ezt a forgalmat, az Azure-SSIS ir indításakor teljesítmény-visszaminősítést tapasztalhat, és elveszítheti a tanúsítvány-visszavonási lista ellenőrzésére való képességét, amely biztonsági szempontból nem ajánlott.

-   Port 1433, 11000-11999 a cél, mint az Azure SQL (csak akkor szükséges, ha a csomópontok az Azure-SSIS IR a virtuális hálózat ban egy SSISDB üzemelteti az SQL Database-kiszolgáló).

    Ha az Azure Firewall, megadhatja a hálózati szabály az Azure SQL Service Tag, különben engedélyezheti a cél adott azure sql URL-t a tűzfalberendezésben.

-   Port 445 cél azure storage-ként (csak akkor szükséges, ha az Azure Files tárolt SSIS-csomagot hajt végre).

    Ha az Azure Firewall, megadhatja a hálózati szabály storage service tag, különben engedélyezheti a cél, mint adott azure fájl tárolási URL-jét a tűzfalberendezésben.

> [!NOTE]
> Az Azure SQL és Storage esetében, ha a virtuális hálózati szolgáltatás végpontjait az alhálózaton konfigurálja, akkor az Azure-SSIS IR és az Azure SQL közötti forgalom ugyanabban a régióban \ Az Azure Storage ugyanabban a régióban vagy a párosított régióban közvetlenül a Microsoft Azure gerinchálózatára lesz irányítva a tűzfalberendezés helyett.

Ha nincs szüksége az Azure-SSIS IR kimenő forgalmának ellenőrzésére, egyszerűen alkalmazhat útvonalat az összes forgalom kényszerítéséhez a következő ugrástípusú **internetre:**

-   Egy Azure ExpressRoute-forgatókönyvben 0.0.0.0/0 útvonalat alkalmazhat a következő ugrástípussal **internetként** az Azure-SSIS IR-t tartalmazó alhálózaton. 
-   NVA-forgatókönyv esetén módosíthatja az Azure-SSIS IR-t a következő ugrástípustól virtuális **berendezésként** az **internetre**üzemeltető alhálózaton alkalmazott meglévő 0.0.0.0/0 útvonalat.

![Útvonal hozzáadása](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Adja meg az útvonalat a következő ugrás típusú **Internet** nem jelenti azt, hogy az összes forgalom az interneten keresztül fog menni. Mindaddig, amíg a cél cím az Azure egyik szolgáltatása, az Azure irányítja a forgalmat közvetlenül a szolgáltatás az Azure gerinchálózatán keresztül, ahelyett, hogy a forgalmat az internetre.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>Az erőforráscsoport beállítása

Az Azure-SSIS ir kell létrehoznia bizonyos hálózati erőforrások at ugyanabban az erőforráscsoportban, mint a virtuális hálózat. Ezek a források a következők:
- Egy Azure load balancer, * \<guid>-azurebatch-cloudserviceloadbalancer*névvel.
- Egy Azure nyilvános * \<IP-cím, guid>-azurebatch-cloudservicepublicip*névvel.
- Hálózati munkahelyi biztonsági csoport * \<GUId>-azurebatch-cloudservicenetworksecuritygroup névvel.* 

> [!NOTE]
> Most már hozhat saját statikus nyilvános IP-címeket az Azure-SSIS IR. Ebben a forgatókönyvben csak az Azure terheléselosztót és a hálózati biztonsági csoportot hozunk létre ugyanazon erőforráscsoport ban, mint a statikus nyilvános IP-címek a virtuális hálózat helyett.

Ezek az erőforrások az Azure-SSIS IR indításakor jönnek létre. Az Azure-SSIS ir leállításakor törlődnek. Ha saját statikus nyilvános IP-címeket hoz az Azure-SSIS IR-hez, a saját statikus nyilvános IP-címei nem törlődnek az Azure-SSIS IR leállításakor. Az Azure-SSIS-ir leállításának letiltása érdekében ne használja fel újra ezeket a hálózati erőforrásokat a többi erőforrásban.

Győződjön meg arról, hogy nincs erőforrás-zárolás az erőforráscsoport/előfizetés, amelyhez a virtuális hálózat/a statikus nyilvános IP-címek tartoznak. Ha írásvédett/törlési zárolást állít be, az Azure-SSIS ir indítása és leállítása sikertelen lesz, vagy nem válaszol.

Győződjön meg arról, hogy nem rendelkezik olyan Azure-szabályzattal, amely megakadályozza, hogy a következő erőforrások létrejönnek az erőforráscsoport/előfizetés alatt, amelyhez a virtuális hálózat/a statikus nyilvános IP-címek tartoznak: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Győződjön meg arról, hogy az előfizetés erőforráskvótája elegendő a fenti három hálózati erőforráshoz. Pontosabban a virtuális hálózatban létrehozott minden egyes Azure-SSIS-kapcsolati kapcsolathoz két ingyenes kvótát kell lefoglalnia a fenti három hálózati erőforrás mindegyikéhez. Az azure-SSIS-ir rendszeres időközönkénti frissítésekesetén az extra egy kvóta lesz felhasználva.

### <a name="faq"></a><a name="faq"></a>Gyik

- Hogyan védhetem meg az Azure-SSIS IR-ben a bejövő kapcsolathoz elérhető nyilvános IP-címet? El lehet távolítani a nyilvános IP-címet?
 
  Jelenleg egy nyilvános IP-cím automatikusan létrejön, amikor az Azure-SSIS IR csatlakozik egy virtuális hálózathoz. Rendelkezünk egy NIC-szintű NSG-vel, amely lehetővé teszi, hogy csak az Azure Batch felügyeleti szolgáltatások csatlakozzanak az Azure-SSIS IR-hez. A bejövő védelemhez alhálózati szintű NSG-t is megadhat.

  Ha nem szeretné, hogy nyilvános IP-cím legyen elérhető, fontolja meg [egy saját üzemeltetésű ir proxyként konfigurálása az Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) helyett csatlakozik az Azure-SSIS IR egy virtuális hálózathoz, ha ez vonatkozik a forgatókönyv.
 
- Hozzáadhatom az Azure-SSIS IR nyilvános IP-címét az adatforrásaim tűzfalának engedélyezési listájához?

  Most már hozhat saját statikus nyilvános IP-címeket az Azure-SSIS IR. Ebben az esetben hozzáadhatja az IP-címeket az adatforrások tűzfalának engedélyezési listájához. Az alábbi lehetőségeket is figyelembe veheti az Azure-SSIS ir-ből való adatelérés biztosításához a forgatókönyvtől függően:

  - Ha az adatforrás a helyszínen van, miután egy virtuális hálózatot csatlakoztatt a helyszíni hálózathoz, és csatlakozott az Azure-SSIS ir-hez a virtuális hálózati alhálózathoz, ezután hozzáadhatja az alhálózat privát IP-címtartományát a tűzfal adatforrásának engedélyezési listájához.
  - Ha az adatforrás olyan Azure-szolgáltatás, amely támogatja a virtuális hálózati szolgáltatás végpontjait, konfigurálhat egy virtuális hálózati szolgáltatás végpontját a virtuális hálózati alhálózaton, és csatlakozhat az Azure-SSIS ir-hez az adott alhálózathoz. Ezután hozzáadhat egy virtuális hálózati szabályt az adott alhálózattal az adatforrás tűzfalához.
  - Ha az adatforrás nem Azure-beli felhőszolgáltatás, az UDR segítségével statikus nyilvános IP-címen keresztül irányíthatja a kimenő forgalmat az Azure-SSIS IR-ről egy NVA/Azure tűzfalra. Ezután hozzáadhatja az NVA/Azure tűzfal statikus nyilvános IP-címét az adatforrás tűzfalengedélyezési listájához.
  - Ha a fenti lehetőségek egyike sem felel meg az igényeinek, fontolja meg [egy saját üzemeltetésű infravörös hitelesítéscsoport konfigurálását az Azure-SSIS IR proxyjaként.](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) Ezután hozzáadhatja a saját üzemeltetésű infravörös modult tartalmazó számítógép statikus nyilvános IP-címét az adatforrás tűzfalának engedélyezési listájához.

- Miért kell két statikus nyilvános címet megadnom, ha sajátot szeretnék hozni az Azure-SSIS IR-hez?

  Az Azure-SSIS ir rendszeresen automatikusan frissül. Új csomópontok jönnek létre a frissítés során, és a régiek törlődnek. Az állásidő elkerülése érdekében azonban a régi csomópontok nem törlődnek, amíg az újak nem állnak készen. Így a régi csomópontok által használt első statikus nyilvános IP-cím nem adható ki azonnal, és szükségünk van a második statikus nyilvános IP-címre az új csomópontok létrehozásához.

- Saját statikus nyilvános IP-címeket hoztam az Azure-SSIS IR-hez, de miért nem fér hozzá az adatforrásaimhoz?

  - Ellenőrizze, hogy a két statikus nyilvános IP-cím is fel került-e a tűzfal adatforrások engedélyezési listájára. Minden alkalommal, amikor az Azure-SSIS IR frissítése, a statikus nyilvános IP-cím között az Ön által hozott kettő között. Ha csak az egyiket adja hozzá az engedélyezési listához, az Azure-SSIS ir-hez való adathozzáférés megszakad a frissítés után.
  - Ha az adatforrás egy Azure-szolgáltatás, ellenőrizze, hogy konfigurálta-e a virtuális hálózati szolgáltatás végpontjai. Ebben az esetben az Azure-SSIS ir és az adatforrás közötti forgalom az Azure-szolgáltatások által kezelt privát IP-címek használatára vált, és saját statikus nyilvános IP-címek hozzáadása a tűzfal engedélyezési listájához az adatforráshoz nem lép érvénybe.

## <a name="azure-portal-data-factory-ui"></a>Azure portal (Data Factory felhasználói felület)

Ez a szakasz bemutatja, hogyan csatlakozhat egy meglévő Azure-SSIS-ir-hez egy virtuális hálózathoz (klasszikus vagy Azure Resource Manager) az Azure Portal és a Data Factory felhasználói felületének használatával. 

Mielőtt csatlakozna az Azure-SSIS ir-hez a virtuális hálózathoz, megfelelően konfigurálnia kell a virtuális hálózatot. Kövesse a virtuális hálózat (klasszikus vagy Az Azure Resource Manager) típusára vonatkozó szakasz lépéseit. Ezután kövesse a harmadik szakaszban leírt lépéseket az Azure-SSIS-ir virtuális hálózathoz való csatlakozásához. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager virtuális hálózat konfigurálása

A portál használatával konfigurálhatja az Azure Resource Manager virtuális hálózat, mielőtt megpróbál csatlakozni egy Azure-SSIS IR hozzá.

1. Indítsa el a Microsoft Edge-et vagy a Google Chrome-ot. Jelenleg csak ezek a webböngészők támogatják a Data Factory felhasználói felületét. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

1. Válassza az **További szolgáltatások lehetőséget.** Szűrje ki és válassza a **Virtuális hálózatok lehetőséget.** 

1. Szűrje ki és válassza ki a virtuális hálózatot a listában. 

1. A **Virtuális hálózat** lapon válassza a **Tulajdonságok lehetőséget.** 

1. A **RESOURCE ID** másolásgombjának kiválasztásával másolja a virtuális hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote-ban vagy egy fájlban. 

1. A bal oldali menüben válassza az **Alhálózatok**lehetőséget. Győződjön meg arról, hogy az elérhető címek száma nagyobb, mint az Azure-SSIS IR csomópontjai. 

1. Ellenőrizze, hogy az Azure Batch-szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja az Azure Batch-szolgáltatót. Ha már rendelkezik Egy Azure Batch-fiókkal az előfizetésében, az előfizetés regisztrálva van az Azure Batch-hez. (Ha létrehozza az Azure-SSIS IR-t a Data Factory portálon, az Azure Batch-szolgáltató automatikusan regisztrálva lesz.) 

   1. Az Azure Portalbal a bal oldali menüben válassza az **Előfizetések**lehetőséget. 

   1. Válassza ki előfizetését. 

   1. A bal oldalon válassza az **Erőforrás-szolgáltatók**lehetőséget, és ellenőrizze, hogy a **Microsoft.Batch** regisztrált szolgáltató-e. 

   ![A "Regisztrált" státusz visszaigazolása](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja a **Microsoft.Batch a** listában, regisztrálja, [hozzon létre egy üres Azure Batch-fiókot](../batch/batch-account-create-portal.md) az előfizetésben. Később törölheti. 

### <a name="configure-a-classic-virtual-network"></a>Klasszikus virtuális hálózat konfigurálása

A portál használatával konfigurálhatja a klasszikus virtuális hálózat, mielőtt megpróbál csatlakozni egy Azure-SSIS IR hozzá. 

1. Indítsa el a Microsoft Edge-et vagy a Google Chrome-ot. Jelenleg csak ezek a webböngészők támogatják a Data Factory felhasználói felületét. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

1. Válassza az **További szolgáltatások lehetőséget.** Szűrje és válassza a **Virtuális hálózatok (klasszikus)** lehetőséget. 

1. Szűrje ki és válassza ki a virtuális hálózatot a listában. 

1. A **Virtuális hálózat (klasszikus)** lapon válassza a **Tulajdonságok lehetőséget.** 

   ![Klasszikus virtuális hálózati erőforrás azonosítója](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. A **RESOURCE ID** másolásgombjának kiválasztásával másolja a klasszikus hálózat erőforrásazonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote-ban vagy egy fájlban. 

1. A bal oldali menüben válassza az **Alhálózatok**lehetőséget. Győződjön meg arról, hogy az elérhető címek száma nagyobb, mint az Azure-SSIS IR csomópontjai. 

   ![A virtuális hálózatban elérhető címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Csatlakozzon a **MicrosoftAzureBatch-hez** a virtuális hálózat **klasszikus virtuálisgép-közreműködői** szerepköréhez. 

   1. A bal oldali menüben válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd a **Szerepkör-hozzárendelések** lapot. 

       !["Hozzáférés-vezérlés" és "Hozzáadás" gombok](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

   1. A **Szerepkör-hozzárendelés hozzáadása** lap **Szerepkör**területén válassza a **Klasszikus virtuálisgép-közreműködő**lehetőséget. A **Kijelölés** mezőbe illessze be a **ddbf3205-c6bd-46ae-8127-60eb9363864 elemet,** majd válassza a **Microsoft Azure Batch** elemet a keresési eredmények listájából. 

       ![Keresési eredmények a "Szerepkör-hozzárendelés hozzáadása" lapon](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. A beállítások mentéséhez és a lap bezárásához válassza a **Mentés** gombot. 

       ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Ellenőrizze, hogy a **Microsoft Azure Batch** megjelenik-e a közreműködők listájában. 

       ![Az Azure Batch-hozzáférés megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ellenőrizze, hogy az Azure Batch-szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja az Azure Batch-szolgáltatót. Ha már rendelkezik Egy Azure Batch-fiókkal az előfizetésében, az előfizetés regisztrálva van az Azure Batch-hez. (Ha létrehozza az Azure-SSIS IR-t a Data Factory portálon, az Azure Batch-szolgáltató automatikusan regisztrálva lesz.) 

   1. Az Azure Portalbal a bal oldali menüben válassza az **Előfizetések**lehetőséget. 

   1. Válassza ki előfizetését. 

   1. A bal oldalon válassza az **Erőforrás-szolgáltatók**lehetőséget, és ellenőrizze, hogy a **Microsoft.Batch** regisztrált szolgáltató-e. 

   ![A "Regisztrált" státusz visszaigazolása](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja a **Microsoft.Batch a** listában, regisztrálja, [hozzon létre egy üres Azure Batch-fiókot](../batch/batch-account-create-portal.md) az előfizetésben. Később törölheti. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Csatlakozzon az Azure-SSIS ir-hez egy virtuális hálózathoz

Miután konfigurálta az Azure Resource Manager virtuális hálózatát vagy a klasszikus virtuális hálózatot, csatlakozhat az Azure-SSIS ir-hez a virtuális hálózathoz:

1. Indítsa el a Microsoft Edge-et vagy a Google Chrome-ot. Jelenleg csak ezek a webböngészők támogatják a Data Factory felhasználói felületét. 

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali menüben az **Adatgyárak**lehetőséget. Ha nem látható **az Adatgyárak** menüben, válassza a **További szolgáltatások**lehetőséget, majd a **INTELLIGENCE + ANALYTICS** szakaszban válassza az **Adatgyárak**lehetőséget. 

   ![Adatgyárak listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Válassza ki az adat-gyár az Azure-SSIS IR a listában. Megjelenik az adatgyár kezdőlapja. Válassza a **Szerzői & figyelő** csempét. A Data Factory felhasználói felülete egy külön lapon jelenik meg. 

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. A Data Factory felhasználói felületén váltson a **Szerkesztés** lapra, válassza a **Kapcsolatok**lehetőséget, és váltson az **Integrációs futtatási lapra.** 

   !["Integrációs futásidők" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Ha az Azure-SSIS-ir fut, az **integrációs futtatóhek** listájában a Műveletek oszlopban válassza a Stop gombot az Azure-SSIS IR.If your Azure-SSIS IR is running, in the Integration Runtimes list, in the **Actions** column, select the **Stop** button for your Azure-SSIS IR. Az Azure-SSIS ir nem szerkeszthető, amíg le nem állítja. 

   ![Az infravörös szolgáltatás leállítása](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Az **integrációs futásidők** listájában a **Műveletek** oszlopban válassza az Azure-SSIS IR **Szerkesztés** gombját. 

   ![Az integrációs futásidő szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Az integrációs futásidejű beállítási panelen a **Tovább** gomb kiválasztásával haladjon végig az **Általános beállítások** és az **SQL-beállítások** szakaszon. 

1. A **Speciális beállítások** szakaszban: 

   1. Jelölje be a **Virtuális hálózat kiválasztása az Azure-SSIS-integrációs futásidejű csatlakozásához, engedélyezze az ADF számára bizonyos hálózati erőforrások létrehozását, és szükség esetén hozza meg saját statikus nyilvános IP-címeit** jelölőnégyzetet. 

   1. **Előfizetés esetén**válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

   1. A **Hely**beállításnál az integrációs futásidő ugyanazon helye van kiválasztva.

   1. A **Típus mezőben**válassza ki a virtuális hálózat típusát: klasszikus vagy Az Azure Resource Manager. Azt javasoljuk, hogy válasszon egy Azure Resource Manager virtuális hálózat, mert a klasszikus virtuális hálózatok hamarosan elavult.

   1. A **Virtuálishálózat neve mezőbe**válassza ki a virtuális hálózat nevét. Az Azure SQL Database-kiszolgálóvirtuális hálózati szolgáltatás végpontjaival vagy az SSISDB üzemeltetéséhez saját végponttal rendelkező felügyelt példányhoz használt példánynak kell lennie. Vagy a helyszíni hálózathoz csatlakoztatott nak kell lennie. Ellenkező esetben bármilyen virtuális hálózat, hogy a saját statikus nyilvános IP-címeket az Azure-SSIS IR.

   1. Az **Alhálózat neve csoportban**válassza ki a virtuális hálózat alhálózatának nevét. Az SSISDB üzemeltetéséhez az Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatásvégpontokkal használt nak kell lennie. Vagy egy másik alhálózat, mint a felügyelt példány a saját végpont ssisdb üzemeltetéséhez használt. Ellenkező esetben bármelyik alhálózat, hogy a saját statikus nyilvános IP-címeket az Azure-SSIS IR.

   1. Jelölje be a **Statikus nyilvános IP-címek kigyűjtése az Azure-SSIS-integrációs futásidejű** höz jelölőnégyzetet, és válassza ki, hogy szeretné-e saját statikus nyilvános IP-címeket hozni az Azure-SSIS IR-hez, hogy engedélyezhesse őket a tűzfalon az adatforrások számára.

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      1. Az **első statikus nyilvános IP-cím,** válassza ki az első statikus nyilvános IP-címet, amely megfelel az Azure-SSIS IR [követelményeinek.](#publicIP) Ha nem rendelkezik ilyennel, kattintson **az Új** hivatkozás létrehozása statikus nyilvános IP-címek létrehozásához az Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.
      
      1. A **második statikus nyilvános IP-cím,** válassza ki a második statikus nyilvános IP-címet, amely megfelel az Azure-SSIS IR [követelményeinek.](#publicIP) Ha nem rendelkezik ilyennel, kattintson **az Új** hivatkozás létrehozása statikus nyilvános IP-címek létrehozásához az Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.

   1. Válassza **a Virtuálishálózat-érvényesítés lehetőséget.** Ha az ellenőrzés sikeres, válassza a **Continue (Folytatás) lehetőséget.** 

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Az **Összefoglaló** szakaszban tekintse át az Azure-SSIS ir összes beállítását. Ezután válassza **a Frissítés**lehetőséget.

1. Indítsa el az Azure-SSIS ir-t az Azure-SSIS IR **Műveletek** oszlopában a **Start** gombra kattintva. Körülbelül 20–30 percet vesz igénybe a virtuális hálózathoz csatlakozó Azure-SSIS IR indítása. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>A változók meghatározása

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása

Mielőtt csatlakozhatna az Azure-SSIS ir-hez egy virtuális hálózathoz, konfigurálnia kell a virtuális hálózatot. Ha automatikusan konfigurálni szeretné az Azure-SSIS infravörös hálózati hálózati jogosultsági adatait a virtuális hálózathoz való csatlakozáshoz, adja hozzá a következő parancsfájlt:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Hozzon létre egy Azure-SSIS IR-t, és csatlakozzon egy virtuális hálózathoz

Létrehozhat egy Azure-SSIS-ir-t, és egyidejűleg csatlakozhat egy virtuális hálózathoz. A teljes parancsfájlt és utasításokat az [Azure-SSIS ir létrehozása című témakörben találja.](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Csatlakozás meglévő Azure-SSIS-ir-hez virtuális hálózathoz

Hozzon [létre egy Azure-SSIS IR](create-azure-ssis-integration-runtime.md) cikk bemutatja, hogyan hozhat létre egy Azure-SSIS IR és csatlakozzon egy virtuális hálózathoz ugyanabban a parancsfájlban. Ha már rendelkezik Azure-SSIS IR-vel, kövesse az alábbi lépéseket a virtuális hálózathoz való csatlakozáshoz: 
1. Állítsa le az Azure-SSIS ir. 
1. Konfigurálja az Azure-SSIS infravörös szolgáltatást a virtuális hálózathoz való csatlakozáshoz. 
1. Indítsa el az Azure-SSIS ir. 

### <a name="stop-the-azure-ssis-ir"></a>Az Azure-SSIS ir leállítása

Le kell állítania az Azure-SSIS IR-t, mielőtt csatlakozhatna egy virtuális hálózathoz. Ez a parancs felszabadítja az összes csomópontját, és leállítja a számlázást:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Virtuális hálózati beállítások konfigurálása az Azure-SSIS infravörös csatlakozásához

Az Azure-SSIS által csatlakozó virtuális hálózat beállításainak konfigurálásához használja ezt a parancsfájlt: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Az Azure-SSIS ir konfigurálása

Ha csatlakozni szeretne az Azure-SSIS ir-hez egy virtuális hálózathoz, futtassa a `Set-AzDataFactoryV2IntegrationRuntime` következő parancsot: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Az Azure-SSIS ir indítása

Az Azure-SSIS IR elindításához futtassa a következő parancsot: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Ez a parancs 20-30 percet vesz igénybe.

## <a name="next-steps"></a>További lépések

Az Azure-SSIS IR szolgáltatásról az alábbi cikkekben talál további információt: 
- [Az Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk általános általános információkat tartalmaz az irs, beleértve az Azure-SSIS IR. 
- [Oktatóanyag: SSIS-csomagok telepítése az Azure-ba.](tutorial-create-azure-ssis-runtime-portal.md) Ez az oktatóanyag lépésenkénti útmutatást nyújt az Azure-SSIS IR létrehozásához. Az Azure SQL Database az SSIS-katalógus üzemeltetéséhez használja. 
- [Hozzon létre egy Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Ez a cikk kibontja az oktatóanyagot. Utasításokat ad az Azure SQL Database virtuális hálózati szolgáltatás végpontokkal vagy felügyelt példány használatával kapcsolatban egy virtuális hálózatban az SSIS-katalógus üzemeltetéséhez. Bemutatja, hogyan csatlakozhat az Azure-SSIS IR-hez egy virtuális hálózathoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kaphat információt az Azure-SSIS IR.This article shows you how to get information about your Azure-SSIS IR. A visszaadott információk állapotleírását tartalmazza. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan állíthatja le, indíthatja el vagy törölheti az Azure-SSIS IR.This article shows you how to stop, start, or delete your Azure-SSIS IR. Azt is bemutatja, hogyan skálázhatja ki az Azure-SSIS IR csomópontok hozzáadásával.