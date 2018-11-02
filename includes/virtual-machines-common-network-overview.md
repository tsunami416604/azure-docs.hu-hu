---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/11/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 89d35197567c4bd44c5867dbb120d07e9f99d1e9
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50746425"
---
Azure virtuális gép létrehozásakor létre kell hoznia egy [virtuális hálózatot](../articles/virtual-network/virtual-networks-overview.md) (VNet), vagy egy meglévő VNetet kell használnia. Arról is döntenie kell, hogy a virtuális gépek milyen módon legyenek elérhetők a VNeten. Mindenképpen [készítsen tervet az erőforrások létrehozása előtt](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md), továbbá győződjön meg arról, hogy tisztában van a [hálózati erőforrások korlátaival](../articles/azure-subscription-service-limits.md#networking-limits).

Az alábbi ábrán a virtuális gépek web- és adatbázis-kiszolgálókként vannak ábrázolva. Az egyes virtuálisgép-csoportok a VNet különböző alhálózataihoz vannak rendelve.

![Azure virtuális hálózat](./media/virtual-machines-common-network-overview/vnetoverview.png)

Létrehozhat egy virtuális hálózat egy virtuális Gépet hoz létre, vagy beállíthatja a virtuális gép létrehozása előtt. A virtuális géppel folytatott kommunikáció támogatásához az alábbi erőforrásokat hozza létre:

- Hálózati illesztők
- IP-címek
- Virtuális hálózat és alhálózatok

Ezen alapvető erőforrások mellett az alábbi választható erőforrások használatát is érdemes fontolóra venni:

- Network security groups (Hálózati biztonsági csoportok)
- Terheléselosztók 

## <a name="network-interfaces"></a>Hálózati illesztők

A [hálózati adapter](../articles/virtual-network/virtual-network-network-interface.md) a virtuális gép és a virtuális hálózat (VNet) közötti kapcsolatot biztosítja. Egy virtuális gépnek legalább egy hálózati adapterrel kell rendelkeznie, de a létrehozott virtuális gép méretétől függően több ilyennel is rendelkezhet. Ismerje meg hány hálózati adaptert minden virtuális gép mérete támogatja a [Windows](../articles/virtual-machines/windows/sizes.md) vagy [Linux](../articles/virtual-machines/linux/sizes.md).

Virtuális gép létrehozása több hálózati adapterrel, és adja hozzá, vagy távolítsa el a hálózati adapterek a virtuális gépek az életciklus során. Több hálózati adapterrel lehetővé teszik a különböző alhálózatokon csatlakozhat és fogadásra forgalmat a legmegfelelőbb felületen keresztül történik egy virtuális Gépet.

Ha a virtuális gépet egy rendelkezésre állási csoporthoz adja hozzá, akkor a rendelkezésre állási csoporton belül minden virtuális gépnek egy vagy több hálózati adapterrel kell rendelkeznie. Az egynél több hálózati adapterrel rendelkező virtuális gépeknek nem kell azonos számú adapterrel rendelkezniük, de legalább kettővel igen.

A virtuális géphez csatlakoztatott hálózati adaptereknek a virtuális géppel megegyező helyen és előfizetésen belül kell lenniük. Az egyes hálózati adaptereket csatlakoztatni kell egy olyan virtuális hálózathoz, amely a hálózati adapterekkel megegyező Azure-helyen és -előfizetésen belül található. Az alhálózat egy virtuális gép csatlakozik-e a létrehozást követően módosítható, de a virtuális hálózat nem módosítható. Minden virtuális géphez csatlakoztatott hálózati adapterhez egy MAC-cím van rendelve, amely a virtuális gép törléséig változatlan marad.

Ez a táblázat egy hálózati adapter létrehozásának lehetséges módszereit sorolja fel.

| Módszer | Leírás |
| ------ | ----------- |
| Azure Portal | Amikor virtuális gépet hoz létre az Azure Portalon, automatikusan létrejön egy hálózati adapter is (külön létrehozott hálózati adapter nem használható). A portál csak egy hálózati adapterrel hoz létre virtuális gépeket. Ha egynél több hálózati adapterrel rendelkező virtuális gépet kíván létrehozni, akkor más módszert kell alkalmaznia. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | Használat [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) együtt a **- PublicIpAddressId** paraméterrel a korábban létrehozott nyilvános IP-cím az azonosító. |
| [Azure CLI](../articles/virtual-machines/linux/multiple-nics.md) | A korábban létrehozott nyilvános IP-cím azonosítójának megadásához használja [az network nic létrehozása](https://docs.microsoft.com/cli/azure/network/nic#create) az a **--public-ip-cím** paraméter. |
| [Sablon](../articles/virtual-network/template-samples.md) | Hálózati adapter sablon használatával történő üzembe helyezéséhez segítségképp használja a [nyilvános IP-címmel rendelkező virtuális hálózatban található hálózati adapter](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) sablonját. |

## <a name="ip-addresses"></a>IP-címek 

Az Azure-ban az alábbi [IP-cím](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)típusokat rendelheti egy hálózati adapterhez:

- **Nyilvános IP-címek** – Az internettel és a VNethez nem csatlakoztatott egyéb Azure-erőforrásokkal folytatott bejövő és kimenő (hálózati címfordítás (NAT) nélküli) kommunikációhoz használható. Nem kötelező nyilvános IP-címet rendelni egy hálózati adapterhez. Nyilvános IP-címek egy névleges díj rendelkezik, és egy maximális száma előfizetésenként használható.
- **Magánhálózati IP-címek** – A VNeten és a helyszíni hálózaton belüli, illetve az internettel folytatott (hálózati címfordítást (NAT) használó) kommunikációhoz használható. Legalább egy magánhálózati IP-címet hozzá kell rendelni egy virtuális géphez. Az Azure-on belüli hálózati címfordítással kapcsolatos további információkért olvassa el a [az Azure kimenő kapcsolatait](../articles/load-balancer/load-balancer-outbound-connections.md) ismertető cikket.

Nyilvános IP-címeket virtuális gépekhez vagy internetes elérésű terheléselosztókhoz rendelhet hozzá. Magánhálózati IP-címeket virtuális gépekhez vagy belső terheléselosztókhoz rendelhet hozzá. Az IP-címeket hálózati adapter használatával rendelheti hozzá a virtuális gépekhez.

Két módszer érhető el az IP-címek egy erőforráshoz történő lefoglalásához: a dinamikus vagy a statikus. Az alapértelmezett lefoglalási módszer a dinamikus, amelynek során az IP-cím nincs lefoglalva a létrehozása idején. Ehelyett az IP-címet akkor foglalja le a rendszer, amikor elindít egy leállított virtuális gépet, vagy létrehoz egy újat. Az IP-cím akkor szabadul fel, ha leállítja vagy törli a virtuális gépet. 

A lefoglalási módszert állíthatja statikusra is, hogy a virtuális géphez tartozó IP-címek változatlanok maradjanak. Ebben az esetben a rendszer azonnal hozzárendel egy IP-címet. Az IP-cím csak akkor szabadul fel, ha törli a virtuális gépet, vagy dinamikusra változtatja a lefoglalási módszert.
    
Ez a táblázat egy IP-cím létrehozásának lehetséges módszereit sorolja fel.

| Módszer | Leírás |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Alapértelmezés szerint a nyilvános IP-címek dinamikusak, a hozzájuk rendelt cím pedig a virtuális gép leállításakor vagy törlésekor változhat. Ha biztosítani szeretné, hogy a virtuális gép ugyanazt a nyilvános IP-címet használja, hozzon létre egy statikus nyilvános IP-címet. Alapértelmezés szerint virtuális gép létrehozásakor a portál dinamikus magánhálózati IP-címet rendel egy hálózati adapterhez. Módosíthatja az IP-címet a statikus, a virtuális gép létrehozása után.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | A Dynamic vagy Static beállításhoz használja a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) parancsot az **-AllocationMethod** paraméterrel. |
| [Azure CLI](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | A Dynamic vagy Static beállításhoz használja az [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) parancsot az **--allocation-method** paraméterrel. |
| [Sablon](../articles/virtual-network/template-samples.md) | Nyilvános IP-cím sablon használatával történő üzembe helyezéséhez segítségképp használja a [nyilvános IP-címmel rendelkező virtuális hálózatban található hálózati adapter](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) sablonját. |

A létrehozást követően a nyilvános IP-címet társíthatja egy virtuális géppel, ha hozzárendeli egy hálózati adapterhez.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok

Az alhálózat egy IP-címtartományt jelent a VNeten belül. A VNetet a rendszerezés és a biztonság érdekében több alhálózatra lehet osztani. Egy virtuális gép minden hálózati adaptere egy VNet egyetlen alhálózatához van csatlakoztatva. Egy VNeten belül az (ugyanazon vagy különböző) alhálózatokhoz csatlakoztatott hálózati adapterek további konfigurálás nélkül is tudnak egymással kommunikálni.

Egy VNet beállításakor meg kell adni a topológiát, beleértve az elérhető címtereket és alhálózatokat is. Ha a VNetet más VNetekhez vagy helyszíni hálózatokhoz szeretné csatlakoztatni, akkor olyan címtartományokat kell választania, amelyek nincsenek átfedésben. Az IP-címek privát jellegűek, és nem érhetők el az internetről, amely csak a nem átirányítható IP-címek, mint a 10.0.0.0/8, 172.16.0.0/12 vagy 192.168.0.0/16 igaz volt. Az Azure mostantól minden címtartományt a magánhálózati VNet IP-címtér részeként kezel, amely csak a VNeten belül, összekapcsolt VNetek között és a helyszíni helyről érhető el,. 

Ha olyan szervezetben dolgozik, amelyben valaki más felelős a belső hálózatokért, a címtér kiválasztása előtt egyeztessen az illetővel. Győződjön meg róla, hogy nincs átfedés, és tudassa vele, melyik teret kívánja használni, nehogy ő is ugyanazt az IP-címtartományt próbálja használni. 

Alapértelmezés szerint nincs biztonsági határ az alhálózatok között, hogy az ezekben lévő virtuális gépek kommunikálhassanak egymással. Beállíthat viszont hálózati biztonsági csoportokat (NSG), amelyek segítségével szabályozhatja az alhálózatokról és virtuális gépekről érkező, illetve az azokra irányuló forgalmat. 

Ez a táblázat egy VNet és alhálózatok létrehozásának lehetséges módszereit sorolja fel. 

| Módszer | Leírás |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/quick-create-portal.md) | Ha engedélyezi, hogy az Azure a virtuális gép létrehozáskor létrehozzon egy VNetet, akkor a név a VNetet tartalmazó erőforráscsoport nevéből és a **-vnet** elemből tevődik össze. A címtér a 10.0.0.0/24, a szükséges alhálózati név a **default**, az alhálózati címtartomány pedig a 10.0.0.0/24. |
| [Azure PowerShell](../articles/virtual-network/quick-create-powershell.md) | A [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmVirtualNetworkSubnetConfig) és a [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmVirtualNetwork) parancs használatával hozhat létre alhálózatot és VNetet. Azt is megteheti, hogy az [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) parancs használatával új alhálózatot ad egy meglévő VNethez. |
| [Azure CLI](../articles/virtual-network/quick-create-cli.md) | Az alhálózat és a VNet egyidejűleg jön létre. Adjon egy **--subnet-name** paramétert az alhálózat nevével rendelkező [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) parancshoz. |
| Sablon | Egy VNet és alhálózatok létrehozásának legegyszerűbb módja az, hogy egy meglévő sablont, például letöltési [virtuális hálózat két alhálózattal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), és módosítsa az igényeinek. |

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A [hálózati biztonsági csoport (NSG)](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) tartalmazza a hozzáférés-vezérlési (ACL) szabályok listáját, amelyek megszabják, hogy milyen típusú hálózati forgalom juthat el az alhálózatokhoz, a hálózati adapterekhez vagy mindkettőhöz. Az NSG-ket alhálózatokhoz vagy alhálózathoz csatlakoztatott hálózati adapterekhez lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuális gépre. Emellett egy adott hálózati adapterre irányuló forgalmat korlátozni is lehet azáltal, hogy egy NSG-t közvetlenül a hálózati adapterhez rendelnek.

Az NSG-k két szabálykészletet tartalmaznak: bejövőt és kimenőt. A szabály prioritásának az egyes készleten belül kell egyedinek lennie. Minden szabály rendelkezik protokolltulajdonságokkal, forrás- és célport-tartományokkal, címelőtagokkal, forgalomiránnyal, prioritással és hozzáférési típussal. 

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat. 

Amikor hálózati adapterhez társít egy NSG-t, a rendszer az NSG hálózatelérési szabályait csak a hálózati adapterre alkalmazza. Ha egy több hálózati adapterrel rendelkező virtuális gépben a rendszer az NSG-t csak az egyik hálózati adapterre alkalmazza, az nem lesz hatással a többi hálózati adapter forgalmára. Különböző NSG-ket társíthat egy hálózati adapterhez (vagy virtuális géphez, az üzembe helyezési modelltől függően) és az alhálózathoz, amelyhez hozzá van kötve a hálózati adapter vagy a virtuális gép. A prioritást a forgalom iránya határozza meg.

A virtuális gépek és a VNet tervezésekor az NSG-re vonatkozóan is [készítsen tervet](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md).

Ez a táblázat egy hálózati biztonsági csoport létrehozásának lehetséges módszereit sorolja fel.

| Módszer | Leírás |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/tutorial-filter-network-traffic.md) | Amikor virtuális gépet hoz létre az Azure Portalon, automatikusan létrejön egy NSG is, amelyet a rendszer a portál által létrehozott hálózati adapterhez rendel. Az NSG neve a virtuális gép nevéből és az **-nsg** elemből tevődik össze. Az NSG egy 1000-es prioritású bejövő szabályt, egy RDP beállítású szolgáltatást, egy TCP beállítású protokollt, egy 3389 beállítású portot és egy Engedélyezés beállítású műveletet tartalmaz. Ha bármilyen egyéb bejövő forgalmat kíván engedélyezni a virtuális gépre, további szabályokat kell az NSG-hez adnia. |
| [Azure PowerShell](../articles/virtual-network/tutorial-filter-network-traffic.md) | Használja a [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmNetworkSecurityRuleConfig) parancsot, és adja meg a szükséges szabályra vonatkozó információkat. Az NSG létrehozásához használja a [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmNetworkSecurityGroup) parancsot. Az alhálózathoz tartozó NSG konfigurálásához használja a [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig) parancsot. Az NSG a VNethez történő hozzáadásához használja a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) parancsot. |
| [Azure CLI](../articles/virtual-network/tutorial-filter-network-traffic-cli.md) | Az [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) parancs használatával hozhatja létre először az NSG-t. Az [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) parancs használatával adhat szabályokat az NSG-hez. Az [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#update) parancs használatával adhatja az NSG-t az alhálózathoz. |
| [Sablon](../articles/virtual-network/template-samples.md) | Hálózati biztonsági csoport sablon használatával történő üzembe helyezéséhez segítségképp használja a [hálózati biztonsági csoport létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) sablont. |

## <a name="load-balancers"></a>Terheléselosztók

Az [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. A Load Balancer konfigurálható a virtuális gépekre [beérkező internetes forgalom](../articles/load-balancer/load-balancer-internet-overview.md) vagy a [virtuális gépek közötti VNet-forgalom elosztására](../articles/load-balancer/load-balancer-internal-overview.md). A terheléselosztó a helyszíni számítógépek és virtuális gépek közötti forgalom elosztására is képes egy létesítmények közötti hálózatban, illetve továbbítani tudja a külső forgalmat egy adott virtuális gépre.

A terheléselosztó leképezi a terheléselosztó nyilvános IP-címe és portja, illetve a virtuális gép magánhálózati IP-címe és portja közötti bejövő és kimenő forgalmat.

Terheléselosztó létrehozásakor figyelembe kell vennie az alábbi konfigurációs elemeket is:

- **Előtérbeli IP-konfiguráció** – A terheléselosztó egy vagy több előtérbeli IP-címet, vagy más néven virtuális IP-címet (VIP) tartalmazhat. Ezek az IP-címek szolgálnak a forgalom bemeneti pontjaként.
- **Háttérbeli címkészlet** – Ahhoz a hálózati adapterhez rendelt IP-címek, amelyen a terhelés eloszlik.
- **NAT-szabályok** – Meghatározza, hogy a forgalom hogyan haladjon át az IP-n, és hogyan történjen az elosztása a háttérbeli IP-re.
- **Terheléselosztó szabályai** – Leképezi egy adott előtérbeli IP és port kombinációját háttérbeli IP-címek és portok kombinációjára. Egyetlen terheléselosztó több terheléselosztási szabállyal is rendelkezhet. Minden szabály egy virtuális gépekhez társított előtérbeli IP és port, illetve háttérbeli IP és port kombinációja.
- **[Mintavételezők](../articles/load-balancer/load-balancer-custom-probe-overview.md)** – Figyelik a virtuális gépek állapotát. Ha egy mintavételező nem válaszol, a terheléselosztó nem küld új kapcsolatokat a nem megfelelő állapotú virtuális gépre. A meglévő kapcsolatok nem változnak, az újakat pedig a kifogástalan állapotú virtuális gépekre küldi.

Ez a táblázat egy internetkapcsolattal rendelkező terheléselosztó létrehozásának lehetséges módszereit sorolja fel.

| Módszer | Leírás |
| ------ | ----------- |
| Azure Portal | Jelenleg nem lehet internetkapcsolattal rendelkező terheléselosztót létrehozni az Azure Portal használatával. |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md) | A korábban létrehozott nyilvános IP-cím azonosítójának megadásához használja [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerFrontendIpConfig) együtt a **- PublicIpAddress** paraméter. A háttércímkészlet konfigurációjának létrehozásához használja a [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerBackendAddressPoolConfig) parancsot. A létrehozott előtérbeli IP-konfigurációhoz rendelt bejövő NAT-szabályok létrehozásához használja a [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerInboundNatRuleConfig) parancsot. A szükséges mintavételezők létrehozásához használja a [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerProbeConfig) parancsot. A terheléselosztó konfigurációjának létrehozásához használja a [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerRuleConfig) parancsot. A terheléselosztó létrehozásához használja a [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) parancsot.|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | Az első terheléselosztó konfigurációjának létrehozásához használja az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) parancsot. A korábban létrehozott nyilvános IP-cím hozzáadásához használja az [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) parancsot. A háttércímkészlet konfigurációjának hozzáadásához használja az [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) parancsot. NAT-szabályok hozzáadásához használja az [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) parancsot. A terheléselosztó szabályainak hozzáadásához használja az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) parancsot. A mintavételezők hozzáadásához használja az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) parancsot. |
| [Sablon](../articles/load-balancer/load-balancer-get-started-internet-arm-template.md) | Terheléselosztó sablon használatával történő üzembe helyezéséhez segítségképp használja a [2 virtuális gép, egy terheléselosztó és NAT-szabályok a terheléselosztón történő konfigurálását](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-natrules) biztosító sablont. |
    
Ez a táblázat egy belső terheléselosztó létrehozásának lehetséges módszereit sorolja fel.

| Módszer | Leírás |
| ------ | ----------- |
| Azure Portal | Jelenleg nem lehet belső terheléselosztót létrehozni az Azure Portal használatával. |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Magánhálózati IP-cím a hálózat alhálózatán történő megadásához használja a [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerFrontendIpConfig) parancsot a **-PrivateIpAddress** paraméterrel. A háttércímkészlet konfigurációjának létrehozásához használja a [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerBackendAddressPoolConfig) parancsot. A létrehozott előtérbeli IP-konfigurációhoz rendelt bejövő NAT-szabályok létrehozásához használja a [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerInboundNatRuleConfig) parancsot. A szükséges mintavételezők létrehozásához használja a [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerProbeConfig) parancsot. A terheléselosztó konfigurációjának létrehozásához használja a [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/New-AzureRmLoadBalancerRuleConfig) parancsot. A terheléselosztó létrehozásához használja a [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) parancsot.|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Az első terheléselosztó konfigurációjának létrehozásához használja az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) parancsot. A magánhálózati IP-cím meghatározásához használja az [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) parancsot a **--private-ip-address** paraméterrel. A háttércímkészlet konfigurációjának hozzáadásához használja az [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) parancsot. NAT-szabályok hozzáadásához használja az [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) parancsot. A terheléselosztó szabályainak hozzáadásához használja az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) parancsot. A mintavételezők hozzáadásához használja az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) parancsot.|
| [Sablon](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | Terheléselosztó sablon használatával történő üzembe helyezéséhez segítségképp használja a [2 virtuális gép, egy terheléselosztó és NAT-szabályok a terheléselosztón történő konfigurálását](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) biztosító sablont. |

## <a name="vms"></a>Virtuális gépek

A virtuális gépeket létre lehet hozni ugyanabban a VNetben, és magánhálózati IP-címek használatával képesek csatlakozni egymáshoz. Akkor is képesek csatlakozni, ha külön alhálózatokon vannak – anélkül, hogy átjárót kellene konfigurálni vagy nyilvános IP-címeket kellene használni. Ha virtuális gépeket szeretne helyezni egy VNetbe, először hozza létre a VNetet, majd a virtuális gép létrehozásakor rendelje azt a VNethez és az alhálózathoz. A virtuális gépek az üzembe helyezés vagy az indítás során kérik le a hálózati beállításaikat.  

A virtuális gépek az üzembe helyezésükkor kapnak IP-címet. Ha több virtuális gépet helyez üzembe egy VNetben vagy alhálózatban, akkor a rendszerindításkor kapnak IP-címet. Statikus IP-címet is hozzárendelhet egy virtuális géphez. Ha statikus IP-címet foglal le, érdemes egy adott alhálózatot használni véletlenül egy másik virtuális gép újbóli felhasználása a statikus IP-cím elkerülése érdekében.  

Ha létrehoz egy virtuális gépet, majd később áttelepítené egy VNetbe, akkor a művelet nem egyszerűen csak a konfiguráció módosításából áll. A virtuális gépet ismét üzembe kell helyeznie a VNetben. Az ismételt üzembe helyezés legegyszerűbb módja, ha törli a virtuális gépet, a hozzá csatolt lemezeket viszont nem, majd újra létrehozza a virtuális gépet a VNetben az eredeti lemezek használatával. 

Ez a táblázat virtuális gépek VNetben való létrehozásának lehetséges módszereit sorolja fel.

| Módszer | Leírás |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md) | A korábban említett hálózati beállítások használatával hoz létre virtuális gépet egyetlen hálózati adapterrel. Több hálózati adapterrel rendelkező virtuális gép létrehozásához más módszert kell alkalmaznia. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | Az [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) parancsot alkalmazza a korábban létrehozott hálózati adapter a virtuális gép konfigurációjához történő hozzáadásához. |
| [Azure CLI](../articles/virtual-machines/linux/create-cli-complete.md) | Hozzon létre, és a egy virtuális gép kapcsolódni egy virtuális hálózat, alhálózat és hálózati Adaptert, amely az egyes lépések, hozhat létre. |
| [Sablon](../articles/virtual-machines/windows/ps-template.md) | Virtuális gép sablon használatával történő üzembe helyezéséhez segítségképp használja a [windowsos virtuális gépek leegyszerűsített üzembe helyezésére](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) szolgáló sablont. |

## <a name="next-steps"></a>További lépések
Az Azure virtuális hálózatok kezelése virtuális gépek virtuális Gépre jellemző lépéseiért lásd: a [Windows](../articles/virtual-machines/windows/tutorial-virtual-network.md) vagy [Linux](../articles/virtual-machines/linux/tutorial-virtual-network.md) oktatóanyagok.

Nincsenek is oktatóanyagok a virtuális gépek terheléselosztása és magas rendelkezésre állású alkalmazásokat hozhat létre [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) vagy [Linux](../articles/virtual-machines/linux/tutorial-load-balancer.md).

- Ismerje meg a [felhasználó által megadott útvonalak és az IP-továbbítás](../articles/virtual-network/virtual-networks-udr-overview.md) konfigurálásának módját. 
- Ismerje meg a [virtuális hálózatok közötti kapcsolatok](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) konfigurálásának módját.
- Ismerje meg az [útvonalak hibaelhárításának](../articles/virtual-network/diagnose-network-routing-problem.md) módját.
