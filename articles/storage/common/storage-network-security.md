---
title: Az Azure Storage tűzfalak és a virtuális hálózatok konfigurálása |} Microsoft Docs
description: A tárfiók rétegezett hálózati biztonságának konfigurálása.
services: storage
documentationcenter: ''
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: 52d904e7a7e8e5d520d2abd799ef0ae7e99b9894
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192876"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Az Azure Storage tűzfalak és a virtuális hálózatok konfigurálása
Az Azure Storage lehetővé téve a storage-fiókok egy meghatározott engedélyezett hálózatok biztonságos többrétegű biztonsági modellt biztosít.  Ha hálózati szabályok úgy vannak konfigurálva, csak az engedélyezett hálózatokhoz alkalmazások férhet hozzá a tárfiók.  Egy engedélyezett hálózatból meghívásakor az alkalmazások továbbra is szükséges megfelelő jogosultságokkal (egy érvényes tárelérési kulccsal vagy SAS-token) a tárfiók eléréséhez szükséges.

> [!IMPORTANT]
> Bekapcsolja a tűzfalszabályok a tárfiók blokkolja a bejövő kérelmek adatok, beleértve a más Azure-szolgáltatásokhoz való hozzáférését.  Ez magában foglalja a portálon, írása naplói, stb.  A programban részt vevő szolgáltatásokhoz engedélyezheti újra funkció keresztül a [kivételek](#Exceptions) az alábbi szakasz.  A portál eléréséhez kellene ehhez belül a megbízható (IP vagy VNet), amely meg van adva a gépről.
>

## <a name="scenarios"></a>Forgatókönyvek
Storage-fiókok beállítható úgy, hogy megtagadja a hozzáférést (beleértve az internetes forgalmat) minden hálózati forgalom alapértelmezés szerint.  Is lehet hozzáférést biztosítani a forgalom az adott Azure virtuális hálózatok, lehetővé téve az alkalmazások biztonságos hálózathatár.  Hozzáférés a nyilvános interneten is kaphatnak IP-címtartományok, az adott internet vagy a helyi ügyfelektől kapcsolatainak engedélyezésére.

Az összes hálózati protokollok, az Azure storage, beleértve a REST és az SMB hálózati szabályokat érvényesíti.  Az adatok elérését az eszközök, például az Azure-portálon Tártallózó, és az AZCopy igényelnek hozzáférést, ha a hálózati szabályok kényszerített explicit hálózati szabályok.

Hálózati szabályok alkalmazhatók a meglévő vagy új tárfiók létrehozása közben is alkalmazható.

Ha a hálózati szabályok vonatkoznak, azok minden olyan kérelem esetében érvényesek lesznek.  Hozzáférést egy adott IP-cím szolgáltatás SAS-tokenje osztja ki az **korlát** a hozzáférési jogkivonat a jogosult azonban nem új hozzáférést meghaladja a beállított hálózati szabályok tegye. 

Virtuális gép lemezét forgalom (beleértve a csatlakoztatási és műveletek leválasztásához és lemez IO) **nem** hálózati-szabályok.  Lapblobokat REST hozzáférést hálózati szabályok védik.

Klasszikus tárfiókokat **nem** támogatja a tűzfalak és a virtuális hálózatok.

Biztonsági mentése és visszaállítása a virtuális gépek használata a nem felügyelt lemezek tárfiókokban hálózati szabálya támogatott keresztül létrehoz egy kivételt, ahogy a [kivételek](/storage/common/storage-network-security#exceptions) című szakaszát.  Tűzfalkivételek alkalmazhatók nem felügyelt lemezzel már kezeli őket az Azure-ban.

## <a name="change-the-default-network-access-rule"></a>Az alapértelmezett hálózati hozzáférési szabály módosítása
Alapértelmezés szerint a storage-fiókok a hálózaton lévő ügyfelek kapcsolatokat fogadjon.  A kijelölt hálózatokhoz való hozzáférés korlátozásához, először módosítania kell az alapértelmezett művelet.

> [!WARNING]
> Hálózati szabályok módosítása hatással lehet az Azure Storage kapcsolódhat az alkalmazások.  Beállítás az alapértelmezett hálózati szabály **megtagadása** összes blokkolja az adatok való hozzáférést, kivéve, ha adott hálózati szabályok *biztosítása* hozzáférést is érvényesek.  Győződjön meg arról, hozzáférést biztosít a hálózati szabályok segítségével, hogy megtagadja a hozzáférést az alapértelmezett szabály módosítása előtt engedélyezett hálózatokhoz.
>

#### <a name="azure-portal"></a>Azure Portal
1. Nyissa meg a védeni kívánt tárfiókot.  

2. Kattintson a beállítások menü nevű **tűzfalak és a virtuális hálózatok**.
3. Alapértelmezés szerint engedélyezi a hozzáférést, válassza a "Kijelölt hálózatok" engedélyezése.  Minden hálózati forgalom engedélyezéséhez válassza a "Minden hálózati" hozzáférést.
4. Kattintson a *mentése* a módosítások alkalmazásához.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).

2. Az alapértelmezett szabályt, a tárfiók állapotának megjelenítése.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Állítsa be az alapértelmezett szabály, amely alapértelmezés szerint nem engedélyezik a hálózati hozzáférést.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Állítsa be az alapértelmezett szabályt, hogy a hálózati hozzáférést alapértelmezés szerint.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Telepítse az Azure CLI 2.0](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. Az alapértelmezett szabályt, a tárfiók állapotának megjelenítése.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Állítsa be az alapértelmezett szabály, amely alapértelmezés szerint nem engedélyezik a hálózati hozzáférést.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Állítsa be az alapértelmezett szabályt, hogy a hálózati hozzáférést alapértelmezés szerint.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Egy virtuális hálózati hozzáférés engedélyezése
Storage-fiókok beállítható úgy, hogy csak az adott Azure virtuális hálózatok engedélyezi a hozzáférést. 

Engedélyezése egy [szolgáltatásvégpont](/azure/virtual-network/virtual-network-service-endpoints-overview) az Azure Storage a virtuális hálózaton belül, forgalom biztosított az optimális útvonal az Azure Storage szolgáltatásba. A virtuális hálózat és az alhálózati identitását is továbbít minden egyes kérelemmel.  Rendszergazdák ezt követően konfigurálhatja a hálózati szabályok a tárfiók, amelyek lehetővé teszik a kérelmek fogadását az adott alhálózatokat a virtuális hálózat.  Ügyfelek számára biztosítani, ezek a hálózati szabályok hozzáférésének továbbra is engedélyezési követelményeinek teljesítése érdekében a Storage-fiók adatainak eléréséhez.

Minden tárfiók legfeljebb 100 virtuális hálózati szabályok kombinálható támogathat [IP hálózati szabályok](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Virtuális hálózati elérhető régiók
Általában a végpontok közötti virtuális hálózatok és a szolgáltatáspéldány azonos Azure-régióban működik.  Végpontok az Azure Storage használata esetén ez a hatókör kibővült a [párosított régió](/azure/best-practices-availability-paired-regions).  Ez lehetővé teszi, hogy folytonosságát egy regionális feladatátvétel, valamint a írásvédett georedundáns reduntant tárolás (RA-GRS) példányok seemless hozzáférés során.  A virtuális hálózati hozzáférési jogot a storage-fiók hálózati szabályokat is hozzáférést bármely RA-GRS-példányra.

Tervezésekor vész-helyreállítási regionális kimaradás során, a virtuális hálózatok párosított régióban előre kell kiépíteni. Az Azure Storage szolgáltatás végpontok engedélyezni kell, és hozzáférés biztosítása a másik virtuális hálózatot a hálózati szabályok a georedundáns tárolás fiókokat alkalmazandó.

> [!NOTE]
> Végpontok nem vonatkoznak a forgalmat a virtuális hálózat és a kijelölt régió pár kívül.  Hozzáférés biztosítása a virtuális hálózatok Storage-fiókokra hálózati szabályok csak a virtuális hálózatok a tárfiók elsődleges régióban, vagy a kijelölt párosított régióban kell alkalmazni.
>

### <a name="required-permissions"></a>Szükséges engedélyek
Ahhoz, hogy a virtuális hálózati szabályt egy tárfiókba, a felhasználó engedéllyel kell rendelkeznie *alhálózathoz csatlakozzon szolgáltatás* hozzáadni kívánt alhálózatok.  Ez az engedély megtalálható a *tárolási fiók közreműködői* beépített szerepkör és egyéni szerepkör-definíciók is hozzáadhatók.

A tárfiók és a virtuális hálózatok hozzáférést **előfordulhat, hogy** különböző előfizetésekhez, de ezeket az előfizetéseket a azonos Azure Active Directory-bérlő részének kell lennie.

### <a name="managing-virtual-network-rules"></a>Virtuális hálózati szabályok kezelése
Virtuális hálózati szabályok storage-fiókok az Azure portálon, a PowerShell vagy CLIv2 keresztül kezelheti.

#### <a name="azure-portal"></a>Azure Portal
1. Nyissa meg a védeni kívánt tárfiókot.  
2. Kattintson a beállítások menü nevű **tűzfalak és a virtuális hálózatok**.
3. Győződjön meg arról, hogy engedélyezi a hozzáférést a "Kijelölt hálózatok" választott.
4. Hozzáférést biztosít egy virtuális hálózatban egy új hálózati szabálynak, a "Virtuális hálózatokat", kattintson a "Meglévő hozzáadása" Válasszon egy már meglévő virtuális hálózatot és -alhálózatok, majd kattintson *Hozzáadás*.  Hozzon létre egy új virtuális hálózatot, és a hozzáférés engedélyezése, kattintson a *új hozzáadása*, az új virtuális hálózat létrehozásához, és kattintson a szükséges információkat *létrehozása*.

> [!NOTE]
> Ha az Azure Storage szolgáltatásvégpont korábban még nem lett konfigurálva a kiválasztott virtuális hálózat és-alhálózatok, ez a művelet részeként beállítható.
>

5. Egy virtuális hálózathoz vagy alhálózathoz szabály eltávolításához kattintson a "...", a virtuális hálózat vagy az alhálózat a helyi menü megnyitásához, és kattintson az "Eltávolítás".
6. Kattintson a *mentése* a módosítások alkalmazásához.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).
2. Virtuális hálózati szabályok listája
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Az Azure Storage egy meglévő virtuális hálózat és alhálózat engedélyezéséhez a szolgáltatási végpont
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Vegyen fel egy hálózati egy virtuális hálózati és alhálózati.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. A virtuális hálózati és alhálózati távolítsa el a szabályokat.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Ügyeljen arra, hogy [állítani az alapértelmezett szabályt](#change-the-default-network-access-rule) megtagadás, vagy a hálózati szabályok hatástalan lesz.
>

#### <a name="cliv2"></a>CLIv2
1. [Telepítse az Azure CLI 2.0](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. Virtuális hálózati szabályok listája
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Az Azure Storage egy meglévő virtuális hálózat és alhálózat engedélyezéséhez a szolgáltatási végpont
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Vegyen fel egy hálózati egy virtuális hálózati és alhálózati.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. A virtuális hálózati és alhálózati távolítsa el a szabályokat. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [állítani az alapértelmezett szabályt](#change-the-default-network-access-rule) megtagadás, vagy a hálózati szabályok hatástalan lesz.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Engedélyezheti a hozzáférést az internetről érkező IP-címtartomány
Storage-fiókok beállítható úgy, hogy engedélyezi a hozzáférést a megadott nyilvános internetről IP-címtartományok.  Ez a konfiguráció lehetővé teszi, hogy a megadott internet alapú szolgáltatások és a helyszíni hálózatokhoz való hozzáférést kell biztosítani, amíg általános internetes forgalom le van tiltva.

Internet címtartomány segítségével megadható engedélyezett [CIDR-jelöléssel](https://tools.ietf.org/html/rfc4632) formájában *16.17.18.0/24* vagy, az egyes IP címek például *16.17.18.19* .

> [!NOTE]
> Kis használatával címtartományai "/ 31" vagy "/ 32" előtag méretek nem támogatottak.  Ezek a tartományok egyedi IP-cím szabályok úgy kell konfigurálni.
>

IP-hálózati szabályok csak az engedélyezett **nyilvános internet** IP-címeket.  (Az RFC 1918 meghatározott) magánhálózatok számára fenntartott IP-címtartományok IP-szabályok nem engedélyezettek.  Magánhálózatok tartalmaznak kezdődő címek *10.\** , *172.16.\** , és *192.168.\** .

Csak az IPV4-címet jelenleg támogatottak.

Minden tárfiók legfeljebb 100 kombinálható IP hálózati szabályok támogathat [virtuális hálózati szabályok](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>A hozzáférést a helyszíni hálózatokhoz konfigurálása
Ahhoz, hogy a helyszíni hálózatokhoz a tárfiók egy IP-hálózati szabállyal hozzáférés biztosításához meg kell adnia az internet felől elérhető a hálózaton használt IP-címeket.  Kérje a rendszergazda segítségét.

Ha a hálózat csatlakozik az Azure-hálózatot használó [ExpressRoute](/azure/expressroute/expressroute-introduction), minden áramkör van konfigurálva a két nyilvános IP-címekkel rendelkező, a Microsoft Edge Microsoft Azure Storage például használó Services valókapcsolódáshozhasznált[Az azure nyilvános Társviszony](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Azure Storage és a kapcsolatcsoport közötti kommunikáció engedélyezéséhez létre kell hoznia IP hálózati szabályok a Kapcsolatcsoportok nyilvános IP-címét.  Ahhoz, hogy az ExpressRoute-kapcsolatcsoportot nyilvános IP-címek keresése [nyisson egy támogatási jegy ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure-portálon.


### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése
IP-hálózati szabályok storage-fiókok az Azure portálon, a PowerShell vagy a CLIv2 felügyelhető.

#### <a name="azure-portal"></a>Azure Portal
1. Nyissa meg a védeni kívánt tárfiókot.  
2. Kattintson a beállítások menü nevű **tűzfalak és a virtuális hálózatok**.
3. Győződjön meg arról, hogy engedélyezi a hozzáférést a "Kijelölt hálózatok" választott.
4. Adja meg az IP-címtartomány egy internet hozzáférés, adja meg az IP-cím vagy a címtartomány a tűzfal, címtartomány (CIDR formátumban).
5. Egy IP-hálózati szabály eltávolításához kattintson a "...", a szabály a helyi menü megnyitásához, és kattintson az "Eltávolítás".
6. Kattintson a *mentése* a módosítások alkalmazásához.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).
2. IP-hálózati szabályok listája
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Vegye fel a szabályokat az egyes IP-címet.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Vegyen fel egy hálózati az IP-címtartományt.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Távolítsa el a szabályokat az egyes IP-címet. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Távolítsa el az IP-címtartományok hálózati szabályt.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Ügyeljen arra, hogy [állítani az alapértelmezett szabályt](#change-the-default-network-access-rule) megtagadás, vagy a hálózati szabályok hatástalan lesz.
>

#### <a name="cliv2"></a>CLIv2
1. [Telepítse az Azure CLI 2.0](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. IP-hálózati szabályok listája
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Vegye fel a szabályokat az egyes IP-címet.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Vegyen fel egy hálózati az IP-címtartományt.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Távolítsa el a szabályokat az egyes IP-címet.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Távolítsa el az IP-címtartományok hálózati szabályt.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [állítani az alapértelmezett szabályt](#change-the-default-network-access-rule) megtagadás, vagy a hálózati szabályok hatástalan lesz.
>

## <a name="exceptions"></a>Kivételek
Amíg a hálózati szabályok engedélyezheti a legtöbb esetben biztonságos hálózati konfigurációját, vannak bizonyos esetekben, ahol kivételek meg kell adni a teljes funkció engedélyezéséhez.  Storage-fiókok konfigurálható kivételek megbízható Microsoft számára, és a tárolási analitikai adatok elérésére.

### <a name="trusted-microsoft-services"></a>Meghatalmazott Microsoft-szolgáltatások
Egyes Microsoft-szolgáltatások tárfiókok együttműködő működnie hálózatokban, amelyek nem engedélyezheti a hálózati szabályok. 

Ahhoz, hogy a szolgáltatást a kívánt módon működik, engedélyezheti, hogy a hálózati szabályok elkerülésére megbízható Microsoft-szolgáltatások készlete. Ezek a szolgáltatások fogja használni a tárfiók eléréséhez erős hitelesítés.

Ha a "Megbízható Microsoft-szolgáltatások" kivétel engedélyezve van, a következő szolgáltatásokat (ha az előfizetés regisztrálva) a tárfiók hozzáférési engedéllyel:

|Szolgáltatás|Erőforrás-szolgáltató neve|Cél|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Hajtsa végre a biztonsági mentések és a nem felügyelt lemezek visszaállítások az infrastruktúra-szolgáltatási virtuális gépeket. (nem felügyelt lemezek esetén szükséges). [További információk](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Egyéni lemezkép létrehozásának és az összetevő-telepítést.  [További információk](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|A Blob Storage esemény közzétételének engedélyezése.  [További információk](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Az Event Hubs rögzítése archiválja.  [További](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Azure-hálózatok|Microsoft.Networking|Tárolásához és hálózati forgalmi naplók elemzése.  [További információk](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Storage analytics adatok elérése
Bizonyos esetekben diagnosztikai naplók és a metrikák olvasási hozzáférést kell biztosítania a hálózati határon kívül.  A hálózati szabályok alóli kivételek engedélyezhetők olvasás-tárolási fiók naplófájlokat, metrikák táblák vagy mindkettőt. [További információ a tárolási analitika használata.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Kivételek kezelése
Hálózati szabálykivételek felügyelhető az Azure portálon, a PowerShell vagy az Azure CLI v2.

#### <a name="azure-portal"></a>Azure Portal
1. Nyissa meg a védeni kívánt tárfiókot.  
2. Kattintson a beállítások menü nevű **tűzfalak és a virtuális hálózatok**.
3. Győződjön meg arról, hogy engedélyezi a hozzáférést a "Kijelölt hálózatok" választott.
4. Csoportban kivételeket, a kivételek kíván biztosítani.
5. Kattintson a *mentése* a módosítások alkalmazásához.

#### <a name="powershell"></a>PowerShell
1. Telepítse a legújabb [Azure PowerShell](/powershell/azure/install-azurerm-ps) és [bejelentkezési](/powershell/azure/authenticate-azureps).
2. Megjeleníti a kivételeket a tárolási fiók hálózati szabályok.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. A tárolási fiók hálózati szabályok alól kivételek konfigurálása.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Távolítsa el a tárolási fiók hálózati szabályok alól kivételek.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [állítani az alapértelmezett szabályt](#change-the-default-network-access-rule) megtagadás, vagy a kivételek eltávolítása nem lesz hatása.
>

#### <a name="cliv2"></a>CLIv2
1. [Telepítse az Azure CLI 2.0](/cli/azure/install-azure-cli) és [bejelentkezési](/cli/azure/authenticate-azure-cli).
2. Megjeleníti a kivételeket a tárolási fiók hálózati szabályok.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. A tárolási fiók hálózati szabályok alól kivételek konfigurálása.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Távolítsa el a tárolási fiók hálózati szabályok alól kivételek.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Ügyeljen arra, hogy [állítani az alapértelmezett szabályt](#change-the-default-network-access-rule) megtagadás, vagy a kivételek eltávolítása nem lesz hatása.
>

## <a name="next-steps"></a>További lépések
További tudnivalók az Azure hálózati szolgáltatás végpontok [Szolgáltatásvégpontok](/azure/virtual-network/virtual-network-service-endpoints-overview).

Az Azure Storage biztonsági mélyebben Dig [Azure Storage biztonsági útmutató](storage-security-guide.md).
