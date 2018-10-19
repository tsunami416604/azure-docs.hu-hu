---
title: Az Azure Storage-tűzfalak és virtuális hálózatok konfigurálása |} A Microsoft Docs
description: A tárfiók rétegezett hálózati biztonságának konfigurálása.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/25/2017
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: 98972b0c52470e6a404090d993c21a47b11cd660
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427145"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Az Azure Storage-tűzfalak és virtuális hálózatok konfigurálása
Az Azure Storage lehetővé teszi, hogy a tárfiókok meghatározott engedélyezett hálózatok biztonságos többrétegű biztonsági modellt biztosít.  Ha a hálózati szabályok vannak beállítva, csak az engedélyezett hálózatok alkalmazások férhetnek hozzá egy tárfiókot.  Egy engedélyezett hálózatról hívásakor az alkalmazások továbbra is megfelelő jogosultságokkal (egy érvényes hozzáférési kulcs vagy SAS-token) a tárfiók eléréséhez szükséges.

> [!IMPORTANT]
> Ne tudják bekapcsolni a tűzfalszabályok a tárfiók letiltja a hozzáférést adataihoz, más Azure-szolgáltatásokból származó bejövő kérelmek.  Ez magában foglalja a portálról, írása, naplók, stb.  Azure-szolgáltatások a virtuális hálózaton belül működő számára hozzáférés engedélyezhető azáltal, hogy az alhálózat, a szolgáltatás példánya.  Nem működik a virtuális hálózaton belüli Azure-szolgáltatásokkal a tűzfal blokkolja.  Forgatókönyvek csak korlátozott számú keresztül engedélyezhető a [kivételek](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) alább ismertetett mechanizmus.  A portál eléréséhez kell ehhez a gépről megbízható modulokon belül (IP vagy virtuális hálózat), amely meg van adva.
>

## <a name="scenarios"></a>Forgatókönyvek
Storage-fiókok (beleértve az internetes forgalmat) minden hálózati forgalmat hozzáférését, alapértelmezés szerint konfigurálható.  Is lehet hozzáférést biztosítani a forgalom az adott Azure-beli virtuális hálózatok, amely lehetővé teszi az alkalmazások biztonságos hálózati határt hozhat létre.  Hozzáférés is adható a nyilvános internetes IP-címtartományok, adott interneten vagy a helyi ügyfelektől érkező kapcsolatokat engedélyezi.

Az összes hálózati protokollok, az Azure storage, beleértve a REST és az SMB hálózati szabályok életbe lépnek.  Az adatokat az eszközök, mint az Azure Portalon, a Storage Explorerben és AZCopy igényel explicit hálózati szabályok hozzáférés biztosítása, ha a hálózati szabályok vannak érvényben.

A hálózati szabályok alkalmazhatók a meglévő tárfiókok, vagy új Storage-fiókok létrehozása közben is alkalmazható.

Hálózati szabályok érvényesek, ha azok irányuló kérések tartatni.  SAS-tokeneket, amelyek egy adott IP-cím szolgáltatáshoz hozzáférést szolgálják a **korlát** a hozzáférési jogkivonat a jogosult azonban nem új hozzáférés biztosítása mellett konfigurált hálózati szabályok ehhez. 

Virtuálisgép-lemez forgalom (ideértve a csatlakoztatást és műveletek leválasztásához és lemez i/o) **nem** hálózati szabályok által érintett.  A lapblobok REST hozzáférést a hálózati szabályok védik.

Klasszikus tárfiókokat **nem** támogatja a tűzfalak és virtuális hálózatok.

Biztonsági mentés és visszaállítás a virtuális gépek nem felügyelt lemezek használata a storage-fiókok a alkalmazni a hálózati szabályok kivétel létrehozása leírtak szerint keresztül támogatott a [kivételek](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) című szakaszát.  Végezze el a tűzfalkivételek nem vonatkoznak a Managed Disks már kezeli az Azure.

## <a name="change-the-default-network-access-rule"></a>Módosítsa az alapértelmezett hálózati hozzáférési szabályt
Alapértelmezés szerint a tárfiókok bármely hálózati ügyfelek kapcsolatokat fogadjon.  A kiválasztott hálózatok való hozzáférés korlátozásához, először módosítania kell az alapértelmezett művelet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet a Kapcsolódás az Azure Storage lehetővé teszi az alkalmazások.  Beállítást az alapértelmezett hálózati szabály **megtagadása** blokkolja az összes hozzáférni az adatokhoz, kivéve, ha adott hálózati szabályok *megadásának* hozzáférést is érvényesek.  Győződjön meg arról, hozzáférést minden olyan engedélyezett hálózatok, hálózati szabályok segítségével, hogy megtagadja a hozzáférést az alapértelmezett szabály módosítása előtt.
>

#### <a name="azure-portal"></a>Azure Portal
1. Keresse meg a védeni kívánt tárfiókot.  

2. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.
3. Alapértelmezés szerint engedélyezi a hozzáférést, válassza a "Kijelölt hálózatok" hozzáférés engedélyezéséhez.  Ahhoz, hogy minden hálózati forgalmat, hogy engedélyezze a hozzáférést minden hálózatból válassza.
4. Kattintson a *mentése* alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell-lel](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).

2. Az alapértelmezett szabályt, a tárfiók állapota megjelenítéséhez.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint nem engedélyezi a hálózati hozzáférést.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint hálózati hozzáférés engedélyezéséhez.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. Az alapértelmezett szabályt, a tárfiók állapota megjelenítéséhez.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint nem engedélyezi a hálózati hozzáférést.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint hálózati hozzáférés engedélyezéséhez.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Egy virtuális hálózathoz való hozzáférés engedélyezése
Storage-fiókok beállítható úgy, hogy a hozzáférés engedélyezése csak az adott Azure virtuális hálózatok. 

Engedélyezésével egy [szolgáltatásvégpont](/azure/virtual-network/virtual-network-service-endpoints-overview) az Azure Storage a virtuális hálózaton belül, a forgalom biztosítva van az optimális útvonal az Azure Storage szolgáltatásba. Minden egyes kérelemmel is továbbítja az identitások, a virtuális hálózatot és alhálózatot.  A rendszergazdák ezt követően konfigurálhatja a hálózati szabályokat a tárfiók, amelyek lehetővé teszik a virtuális hálózatban lévő adott alhálózatokhoz érkező kérelmek.  Az ügyfelek kapnak a szabályokban a hálózaton keresztüli hozzáférés továbbra is az adatok a Storage-fiók a engedélyezési követelmények teljesítéséhez.

Minden tárfiók legfeljebb 100, amely összekapcsolható a virtuális hálózati szabályok támogathat [IP-hálózati szabályok](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Virtuális hálózati régióban.
Általánosságban elmondható végpontok közötti virtuális hálózatok és a szolgáltatáspéldány ugyanazon Azure-régióban működik.  A Szolgáltatásvégpontok az Azure Storage használata esetén ez a hatókör kibővült a [párosított régióra](/azure/best-practices-availability-paired-regions).  Ez lehetővé teszi folytonosságát egy regionális feladatátvétel, valamint a példányok írásvédett georedundáns tárolás (RA-GRS) való zavartalan hozzáférés biztosítása során.  Hálózati szabályok, amelyek hozzáférést biztosítanak a virtuális hálózatról egy storage-fiókhoz is hozzáférést tetszőleges RA-GRS-példányra.

Vészhelyreállítás tervezése egy regionális kimaradás során, ha a párosított régióban található virtuális hálózatok előre kell kiépítenie. Az Azure Storage-Szolgáltatásvégpontok engedélyezni kell, és hozzáférés biztosítása a ezeket a másodlagos virtuális hálózatokat a hálózati szabályok kell alkalmazni a georedundáns tárolás fiókok.

> [!NOTE]
> A Szolgáltatásvégpontok nem vonatkoznak a forgalmat a virtuális hálózat és a kijelölt régiópárra a régión kívül.  Hálózati szabályokat ad hozzáférést a virtuális hálózatok, Storage-fiókok csak akkor alkalmazható, az elsődleges régióban a Storage-fiók vagy a kijelölt párosított régióban található virtuális hálózatok esetében.
>

### <a name="required-permissions"></a>Szükséges engedélyek
Annak érdekében, hogy a alkalmazni egy virtuális hálózati szabályt egy tárfiókot, a felhasználó engedéllyel kell rendelkeznie *alhálózathoz csatlakozzon szolgáltatás* az alhálózatokra.  Ez az engedély szerepel a *Tárfiók-közreműködő* beépített szerepkör és egyéni szerepkör-definíciók is hozzáadhatók.

Storage-fiók és a virtuális hálózatok hozzáférést **előfordulhat, hogy** eltérő előfizetésben, de ezen előfizetések ugyanahhoz az Azure Active Directory-bérlőhöz részének kell lennie.

### <a name="managing-virtual-network-rules"></a>A virtuális hálózati szabályok kezelése
Storage-fiókok virtuális hálózati szabályok az Azure portal, PowerShell vagy CLIv2 felügyelhető.

#### <a name="azure-portal"></a>Azure Portal
1. Keresse meg a védeni kívánt tárfiókot.  
2. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.
3. Győződjön meg arról, hogy úgy döntött, hogy engedélyezze a hozzáférést a "Kijelölt hálózatok".
4. Hozzáférést biztosít egy virtuális hálózathoz, az új hálózati szabály az "A virtuális hálózatok" kattintson a "Meglévő hozzáadása" Válasszon egy meglévő virtuális hálózatot és alhálózatot, majd kattintson *Hozzáadás*.  Hozzon létre egy új virtuális hálózatot, és hozzáférés biztosítása, kattintson a *új hozzáadása*, adja meg az új virtuális hálózat létrehozása, és kattintson a szükséges információk *létrehozás*.

> [!NOTE]
> Ha az Azure Storage-szolgáltatásvégpont nincs korábban konfigurálva a kiválasztott virtuális hálózat és alhálózatok, akkor ez a művelet részeként konfigurálható.
>

5. Egy virtuális hálózat vagy alhálózat szabály eltávolításához kattintson a "...", a virtuális hálózat vagy az alhálózat a helyi menü megnyitásához, majd kattintson a "Remove".
6. Kattintson a *mentése* alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell-lel](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).
2. A virtuális hálózati szabályok listája
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Szolgáltatásvégpont engedélyezése egy meglévő virtuális hálózatot és alhálózatot az Azure Storage-hoz
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Hálózati szabály hozzáadása egy virtuális hálózatot és alhálózatot.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Egy hálózati szabály eltávolítása egy virtuális hálózatot és alhálózatot.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) letiltva, vagy a hálózati szabályok nem lesz hatása.
>

#### <a name="cliv2"></a>CLIv2
1. [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. A virtuális hálózati szabályok listája
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Szolgáltatásvégpont engedélyezése egy meglévő virtuális hálózatot és alhálózatot az Azure Storage-hoz
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Hálózati szabály hozzáadása egy virtuális hálózatot és alhálózatot.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Egy hálózati szabály eltávolítása egy virtuális hálózatot és alhálózatot. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) letiltva, vagy a hálózati szabályok nem lesz hatása.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférést biztosít egy internetes IP-címtartomány
Tárfiókok beállíthatók úgy, hogy engedélyezi a hozzáférést a nyilvános internetről adott IP-címtartományok.  Ez a konfiguráció lehetővé teszi, hogy bizonyos internetes szolgáltatások és a helyszíni hálózatokkal való hozzáférést kell biztosítani, míg az általános internetes forgalom le van tiltva.

Megengedett címtartományok interneten adatokkal együtt adható meg [CIDR-jelölés](https://tools.ietf.org/html/rfc4632) formájában *16.17.18.0/24* vagy az egyes IP címek például *16.17.18.19* .

> [!NOTE]
> Kis címtartományok használatával "/ 31" vagy "/ 32" előtag méretei nem támogatottak.  Ezek a tartományok egyedi IP-cím szabályok használatával kell konfigurálni.
>

Csak engedélyezett IP-hálózati szabályok **nyilvános interneten** IP-címeket.  IP-címtartományok, magánhálózatok számára fenntartott (ahogyan az az [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) IP-szabályok nem engedélyezettek.  Magánhálózatokat közé tartozik a címek kezdődő *10.\** , *172.16.\**   -  *172.31.\**, és *192.168.\** .

Vegye figyelembe, hogy IP-hálózati szabályok nem lesz hatással az ugyanazon Azure-régióban a Storage-fiók érkező kérések a.  A virtuális hálózati szabályok használatával ugyanabban a régióban kérések engedélyezése.

Jelenleg csak az IPV4-cím támogatott.

Minden tárfiók legfeljebb 100 kombinálható IP hálózati szabályokat támogathat [virtuális hálózati szabályok](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Hozzáférés a helyszíni hálózatok konfigurálása
Annak érdekében, hogy a hozzáférést a helyszíni hálózat egy IP-hálózati szabállyal a tárfiókhoz, meg kell adnia az internetkapcsolattal rendelkező, a hálózat által használt IP-címeket.  Segítségért forduljon a rendszergazdához.

Ha a hálózat csatlakozik az Azure-hálózat használatával [ExpressRoute](/azure/expressroute/expressroute-introduction), minden egyes Kapcsolatcsoportok úgy van konfigurálva, két nyilvános IP-címekkel rendelkező, a Microsoft Edge Microsoft Serviceshez hasonlóan az Azure Storage használatával valócsatlakozáshozhasznált[Azure nyilvános társviszony-létesítés](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Ahhoz, hogy a kapcsolatcsoport az Azure Storage-kommunikációt, a nyilvános IP-címeit a Kapcsolatcsoportok IP-hálózati szabályokat kell létrehozni.  Annak érdekében, hogy az ExpressRoute-kapcsolatcsoport nyilvános IP-címek keresése [nyisson egy támogatási jegyet az expressroute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon keresztül.


### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése
IP-hálózati szabályok storage-fiókok az Azure portal, PowerShell vagy CLIv2 felügyelhető.

#### <a name="azure-portal"></a>Azure Portal
1. Keresse meg a védeni kívánt tárfiókot.  
2. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.
3. Győződjön meg arról, hogy úgy döntött, hogy engedélyezze a hozzáférést a "Kijelölt hálózatok".
4. Adja meg az IP-címtartomány egy internetes hozzáférést, adja meg az IP-címet vagy címtartományt a tűzfalat, címtartomány (CIDR formátumban).
5. Az IP-hálózati szabály eltávolításához kattintson a Kuka ikonra a hálózati szabály mellett.
6. Kattintson a *mentése* alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell-lel](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).
2. IP-hálózati szabályok listája.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Adja hozzá az egyes IP-cím hálózati szabályt.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Adjon hozzá egy IP-címtartomány hálózati szabályt.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Távolítsa el az egyes IP-cím hálózati szabályt. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Távolítsa el az IP-címtartomány hálózati szabályt.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) letiltva, vagy a hálózati szabályok nem lesz hatása.
>

#### <a name="cliv2"></a>CLIv2
1. [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. IP-hálózati szabályok listája
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Adja hozzá az egyes IP-cím hálózati szabályt.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Adjon hozzá egy IP-címtartomány hálózati szabályt.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Távolítsa el az egyes IP-cím hálózati szabályt.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Távolítsa el az IP-címtartomány hálózati szabályt.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) letiltva, vagy a hálózati szabályok nem lesz hatása.
>

## <a name="exceptions"></a>Kivételek
A hálózati szabályok engedélyezheti a biztonságos hálózati konfigurációja a legtöbb forgatókönyvhöz, bár vannak bizonyos esetekben, ahol kivételek rendelkeznie kell a teljes funkció engedélyezéséhez.  Storage-fiókok is konfigurálhatók, kivételek, a megbízható Microsoft-szolgáltatások és a Storage analytics adatokhoz való hozzáférést.

### <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások
Bizonyos Microsoft-szolgáltatások, amelyek a Storage-fiókok kezelése, amelyek nem adható hozzáférés hálózati jogcímszabályai hálózatokról működnek. 

Ahhoz, hogy az ilyen típusú szolgáltatást, hogy helyesen működik, engedélyezheti, hogy a megbízható Microsoft-szolgáltatások megkerülése a hálózati szabályok készletét. Ezek a szolgáltatások fogja használni a tárfiók eléréséhez erős hitelesítés.

Ha a "Megbízható Microsoft-szolgáltatások" kivétel engedélyezve van, a következő szolgáltatások (az előfizetés regisztrálásakor) a Storage-fiók hozzáféréssel rendelkeznek:

|Szolgáltatás|Erőforrás-szolgáltató neve|Cél|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Hajtsa végre a biztonsági mentéshez és helyreállításhoz, a nem felügyelt lemezek az iaas-beli virtuális gépeken. (nem felügyelt lemezek esetén szükséges). [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Egyéni rendszerkép létrehozása és az összetevő telepítését.  [További információk](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|A Blob Storage esemény-közzététel engedélyezése.  [További információk](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Az Event Hubs Capture adatok archiválása.  [További](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Azure-hálózatok|Microsoft.Networking|Store és a hálózati forgalom naplóinak elemzése.  [További információk](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Monitor|Microsoft.Insights| Lehetővé teszi, hogy a figyelési adatok egy biztonságos storaage fiókba írásos [további](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security#monitoring-and-secured-Azure-storage-and-networks).|
|


### <a name="storage-analytics-data-access"></a>Storage analytics-adatok elérése
Bizonyos esetekben a diagnosztikai naplók és mérőszámok olvasási hozzáférést szükség a a hálózathatáron kívül.  A hálózati szabályok alóli kivételek is megadható, hogy a tárolási fiók naplófájlok, tabulky metrik vagy mindkettő írásvédett. [További tudnivalók a storage analytics használata.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Kivételek kezelése
Hálózati kivételeit, annak az Azure portal, PowerShell vagy Azure CLI-vel kezelhető v2.

#### <a name="azure-portal"></a>Azure Portal
1. Keresse meg a védeni kívánt tárfiókot.  
2. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.
3. Győződjön meg arról, hogy úgy döntött, hogy engedélyezze a hozzáférést a "Kijelölt hálózatok".
4. A kivételek válassza ki a kivételek kíván biztosítani.
5. Kattintson a *mentése* alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell-lel](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).
2. A storage-fiók hálózati szabályok esetében a kivételek jelennek meg.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. A storage-fiók hálózati szabályok alóli kivételek konfigurálása.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Távolítsa el a storage-fiók hálózati szabályok alóli kivételek.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) letiltva, vagy a kivételek eltávolítása nem lesz hatása.
>

#### <a name="cliv2"></a>CLIv2
1. [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. A storage-fiók hálózati szabályok esetében a kivételek jelennek meg.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. A storage-fiók hálózati szabályok alóli kivételek konfigurálása.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Távolítsa el a storage-fiók hálózati szabályok alóli kivételek.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) letiltva, vagy a kivételek eltávolítása nem lesz hatása.
>

## <a name="next-steps"></a>További lépések
További információ az Azure hálózati Szolgáltatásvégpontok [Szolgáltatásvégpontok](/azure/virtual-network/virtual-network-service-endpoints-overview).

Tájékozódjon részletesebben az Azure Storage biztonsági [Azure Storage biztonsági útmutatóját](storage-security-guide.md).
