---
title: Az Azure Storage tűzfalainak és virtuális hálózatainak konfigurálása | Microsoft dokumentumok
description: Konfigurálja a tárfiók réteges hálózati biztonságát.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 77ad8579f31ce900a67e2ba3ddc53a5b034b6d42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454669"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Az Azure Storage tűzfalainak és virtuális hálózatainak konfigurálása

Az Azure Storage rétegzett biztonsági modellel rendelkezik. Ez a modell lehetővé teszi, hogy biztonságos sáé és szabályozhatja a hozzáférést a tárfiókok, amelyek az alkalmazások és a vállalati környezetek igény, a használt hálózatok típusa és részhalmaza alapján. Ha a hálózati szabályok konfigurálva vannak, csak a megadott hálózatokon keresztül adatokat kérő alkalmazások férhetnek hozzá a tárfiókhoz. Korlátozhatja a tárfiókhoz való hozzáférést a megadott IP-címekről, IP-tartományokból vagy az Azure virtuális hálózat (VNet) alhálózatainak listájáról származó kérelmekre.

A tárfiókok rendelkeznek egy nyilvános végpont, amely elérhető az interneten keresztül. Privát végpontokat is létrehozhat [a tárfiókhoz,](storage-private-endpoints.md)amely a virtuális hálózatról egy privát IP-címet rendel a tárfiókhoz, és egy privát kapcsolaton keresztül biztosítja a virtuális hálózat és a tárfiók közötti forgalmat. Az Azure storage tűzfal hozzáférést biztosít a tárfiók nyilvános végpontjához. A tűzfal segítségével is letilthatja az összes hozzáférést a nyilvános végponton keresztül, ha magánvégpontok használata esetén. A tárolótűzfal-konfiguráció lehetővé teszi bizonyos megbízható Azure platformszolgáltatások biztonságos elérését a tárfiók eléréséhez.

Egy olyan alkalmazás, amely akkor fér hozzá egy tárfiókhoz, amikor a hálózati szabályok érvényben vannak, továbbra is megfelelő engedélyt igényel a kérelemhez. Az engedélyezést az Azure Active Directory (Azure AD) blobok és várólisták hitelesítő adatai, érvényes fiókhozzáférési kulccsal vagy SAS-jogkivonattal támogatják.

> [!IMPORTANT]
> A tárfiók tűzfalszabályainak bekapcsolása alapértelmezés szerint blokkolja a bejövő adatkérelmeket, kivéve, ha a kérelmek egy Azure virtuális hálózaton (VNet) működő szolgáltatásból vagy az engedélyezett nyilvános IP-címekről származnak. A letiltott kérelmek közé tartoznak a más Azure-szolgáltatásokból, az Azure Portalról, a naplózási és metrikaszolgáltatásokból származó kérések, és így tovább.
>
> Hozzáférést biztosíthat a virtuális hálózaton belül működő Azure-szolgáltatásokhoz, ha engedélyezi a szolgáltatáspéldányt üzemeltető alhálózatból érkező forgalmat. Korlátozott számú forgatókönyvet is engedélyezhet az alábbiakban [ismertetett Kivételek](#exceptions) mechanizmuson keresztül. Adatok eléréséhez a tárfiókból az Azure Portalon keresztül, akkor kell egy gépen a megbízható határon (IP vagy virtuális hálózat) beállított.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Forgatókönyvek

A tárfiók biztonságossá tétele érdekében először konfigurálnia kell egy szabályt, amely alapértelmezés szerint megtagadja a nyilvános végponton lévő összes hálózat (beleértve az internetes forgalmat is) forgalmához való hozzáférést. Ezután konfigurálja azokat a szabályokat, amelyek hozzáférést biztosítanak az adott virtuális hálózatokból érkező forgalomhoz. A szabályokat úgy is beállíthatja, hogy hozzáférést biztosítson a forgalomhoz bizonyos nyilvános internetes IP-címtartományokból, lehetővé téve az adott internetes vagy helyszíni ügyfelek kapcsolatait. Ez a konfiguráció lehetővé teszi, hogy hozzon létre egy biztonságos hálózati határ az alkalmazások számára.

Kombinálhatja azokat a tűzfalszabályokat, amelyek lehetővé teszik a hozzáférést adott virtuális hálózatokból és nyilvános IP-címtartományokból ugyanazon a tárfiókon. A tárolási tűzfalszabályok alkalmazhatók a meglévő tárfiókokra, vagy új tárfiókok létrehozásakor.

A tárolási tűzfalszabályok egy tárfiók nyilvános végpontjára vonatkoznak. Nincs szükség tűzfal-hozzáférési szabályok, hogy a forgalom a tárfiók privát végpontjai. A privát végpont létrehozásának jóváhagyásának folyamata implicit hozzáférést biztosít a magánvégpontot tartalmazó alhálózatból érkező forgalomhoz.

A hálózati szabályok az Azure storage összes hálózati protokollján érvénybe lépnek, beleértve a REST-et és az SMB-t is. Az adatok eléréséhez az eszközök, például az Azure Portal, a Storage Explorer és az AZCopy használatával, explicit hálózati szabályokat kell konfigurálni.

A hálózati szabályok alkalmazása után minden kérelemre érvényes. Egy adott IP-címhez hozzáférést biztosító SAS-jogkivonatok a jogkivonat-tulajdonos hozzáférésének korlátozására szolgálnak, de nem biztosítanak új hozzáférést a konfigurált hálózati szabályokon túl.

A hálózati szabályok nem befolyásolják a virtuális gép lemezforgalmát (beleértve a csatlakoztatási és leválasztási műveleteket, valamint a lemez I/O-ját). A lapblobokhoz való REST-hozzáférést hálózati szabályok védik.

A klasszikus tárfiókok nem támogatják a tűzfalakat és a virtuális hálózatokat.

A nem felügyelt lemezek et a tárfiókokban a virtuális gépek biztonsági mentésére és visszaállítására alkalmazott hálózati szabályokkal egy kivétel használatával használhatja. Ezt a folyamatot a cikk [Kivételek](#exceptions) szakasza dokumentálja. A tűzfalkivételek nem alkalmazhatók a felügyelt lemezekkel, mivel azokat már az Azure kezeli.

## <a name="change-the-default-network-access-rule"></a>Az alapértelmezett hálózati hozzáférési szabály módosítása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező kapcsolatokat elfogadnak. A kijelölt hálózatok hozzáférésének korlátozásához elsőként módosítania kell az alapértelmezett műveletet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet az alkalmazás Azure Storage-hoz való kapcsolódására. Az alapértelmezett hálózati szabály **megtagadása** letiltja az adatokhoz való összes hozzáférést, kivéve, ha a hozzáférést **biztosító** konkrét hálózati szabályokat is alkalmazza. Ügyeljen arra, hogy az alapértelmezett szabály hozzáférés-megtagadásra való módosítása előtt engedélyezze a hálózati szabályokat alkalmazó engedélyezett hálózatokhoz való hozzáférést.

### <a name="managing-default-network-access-rules"></a>Alapértelmezett hálózati hozzáférési szabályok kezelése

A tárfiókok alapértelmezett hálózati hozzáférési szabályait az Azure Portalon, a PowerShellen vagy a CLIv2-n keresztül kezelheti.

#### <a name="azure-portal"></a>Azure portál

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a Beállítások menüre, **a Tűzfalak és a virtuális hálózatok parancsra.**

1. Ha alapértelmezés szerint meg szeretné tagadni a hozzáférést, válassza a **kijelölt hálózatokról**való hozzáférést. Ha minden hálózatról engedélyezni szeretné a forgalmat, engedélyezze a hozzáférést az **Összes hálózatnak**.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse az [Azure PowerShellt,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

1. A tárfiók alapértelmezett szabályának állapotának megjelenítése.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Állítsa be az alapértelmezett szabályt a hálózati hozzáférés alapértelmezés szerint iszinójának megtagadására.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Állítsa be az alapértelmezett szabályt, hogy alapértelmezés szerint engedélyezze a hálózati hozzáférést.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

1. A tárfiók alapértelmezett szabályának állapotának megjelenítése.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Állítsa be az alapértelmezett szabályt a hálózati hozzáférés alapértelmezés szerint iszinójának megtagadására.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Állítsa be az alapértelmezett szabályt, hogy alapértelmezés szerint engedélyezze a hálózati hozzáférést.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Hozzáférés megadása virtuális hálózatról

A tárfiókok konfigurálhatók úgy, hogy csak bizonyos alhálózatokból engedélyezze a hozzáférést. Az engedélyezett alhálózatok tartozhatnak egy virtuális hálózat ugyanabban az előfizetésben, vagy egy másik előfizetésben, beleértve egy másik Azure Active Directory-bérlőhöz tartozó előfizetések.

Engedélyezze [a szolgáltatásvégpontot](/azure/virtual-network/virtual-network-service-endpoints-overview) az Azure Storage számára a virtuális hálózaton belül. A szolgáltatásvégpont a virtuális hálózatról az Azure Storage-szolgáltatáshoz vezető optimális útvonalon keresztül irányítja a forgalmat. Az alhálózat és a virtuális hálózat identitásait is továbbítja az egyes kérelmek. A rendszergazdák ezután konfigurálhatják a tárfiók hálózati szabályait, amelyek lehetővé teszik a virtuális hálózat adott alhálózataitól érkező kérelmek fogadását. Az ezeken a hálózati szabályokon keresztül hozzáféréssel rendelkező ügyfeleknek továbbra is meg kell felelniük a tárfiók engedélyezési követelményeinek az adatok eléréséhez.

Minden tárfiók legfeljebb 100 virtuális hálózati szabályt támogat, amelyek [ip-hálózati szabályokkal](#grant-access-from-an-internet-ip-range)kombinálhatók.

### <a name="available-virtual-network-regions"></a>Elérhető virtuális hálózati régiók

Általában a szolgáltatásvégpontok működnek a virtuális hálózatok és az azonos Azure-régióban lévő szolgáltatáspéldányok között. Ha szolgáltatásvégpontokat használ az Azure Storage szolgáltatással, ez a hatókör a [párosított régiót](/azure/best-practices-availability-paired-regions)is magában foglalja. A szolgáltatásvégpontok lehetővé teszik a folytonosságot a regionális feladatátvétel és az írásvédett georedundáns tárolási (RA-GRS) példányok elérése során. A virtuális hálózatról egy tárfiókhoz való hozzáférést biztosító hálózati szabályok bármely RA-GRS-példányhoz is hozzáférést biztosítanak.

Vész-helyreállítási tervezésekor egy regionális kimaradás során, hozzon létre a virtuális hálózatok a párosított régióban előre. Engedélyezze a szolgáltatásvégpontok az Azure Storage,hálózati szabályok hozzáférést biztosít az alternatív virtuális hálózatok. Ezután alkalmazza ezeket a szabályokat a georedundáns tárfiókokra.

> [!NOTE]
> A szolgáltatásvégpontok nem vonatkoznak a virtuális hálózat és a kijelölt régiópár régióján kívüli forgalomra. Csak olyan hálózati szabályokat alkalmazhat, amelyek hozzáférést biztosítanak a virtuális hálózatokból a tárfiók elsődleges régiójában vagy a kijelölt párosított régióban lévő tárfiókokhoz.

### <a name="required-permissions"></a>Szükséges engedélyek

Virtuális hálózati szabály tárfiókra történő alkalmazásához a felhasználónak rendelkeznie kell a hozzáadott alhálózatokhoz szükséges engedélyekkel. A szükséges engedély a *Csatlakozás szolgáltatás egy alhálózathoz,* és szerepel a *storage-fiók közreműködője* beépített szerepkörben. Egyéni szerepkör-definíciókhoz is hozzáadható.

A tárfiók és a virtuális hálózatok hozzáférési lehet a különböző előfizetések, beleértve az előfizetések, amelyek egy másik Azure AD-bérlő része.

> [!NOTE]
> A más Azure Active Directory-bérlő részét tartalmazó virtuális hálózatok alhálózataihoz hozzáférést biztosító szabályok konfigurálása jelenleg csak a Powershell, a CLI és a REST API-k on keresztül támogatott. Ezek a szabályok nem konfigurálhatók az Azure Portalon keresztül, bár megtekinthetők a portálon.

### <a name="managing-virtual-network-rules"></a>Virtuális hálózati szabályok kezelése

A tárfiókok virtuális hálózati szabályai az Azure Portalon, a PowerShellen vagy a CLIv2-n keresztül kezelhetők.

#### <a name="azure-portal"></a>Azure portál

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a Beállítások menüre, **a Tűzfalak és a virtuális hálózatok parancsra.**

1. Ellenőrizze, hogy a **kijelölt**hálózatokról engedélyezi-e a hozzáférést.

1. Ha új hálózati szabállyal szeretne hozzáférést biztosítani egy virtuális hálózathoz, a **Virtuális hálózatok**csoportban kattintson a Meglévő virtuális **hálózatok hozzáadása**elemre, válassza a **Virtuális hálózatok** és **alhálózatok** lehetőséget, majd kattintson a **Hozzáadás**gombra. Új virtuális hálózat létrehozásához és elérésének engedélyezéséhez kattintson **az Új virtuális hálózat hozzáadása gombra.** Adja meg az új virtuális hálózat létrehozásához szükséges információkat, majd kattintson a **Létrehozás gombra.**

    > [!NOTE]
    > Ha az Azure Storage szolgáltatásvégpontja korábban nem volt konfigurálva a kiválasztott virtuális hálózathoz és alhálózatokhoz, konfigurálhatja azt a művelet részeként.
    >
    > Jelenleg csak az azonos Azure Active Directory-bérlőhöz tartozó virtuális hálózatok jelennek meg a szabály létrehozása során. Egy másik bérlőhöz tartozó virtuális hálózat alhálózatához való hozzáférés engedélyezéséhez használja a Powershell, cli vagy REST API-kat.

1. Virtuális hálózati vagy alhálózati szabály eltávolításához kattintson **a ...** gombra a virtuális hálózat vagy alhálózat helyi menüjének megnyitásához, majd kattintson az **Eltávolítás gombra.**

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse az [Azure PowerShellt,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

1. Virtuális hálózati szabályok listázása.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Engedélyezze a szolgáltatásvégpontot az Azure Storage számára egy meglévő virtuális hálózaton és alhálózaton.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Hálózati szabály hozzáadása virtuális hálózathoz és alhálózathoz.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Ha egy másik Azure AD-bérlőhöz tartozó virtuális hálózatal rendelkező alhálózathoz szeretne hálózati szabályt hozzáadni, használjon egy teljesen minősített **VirtualNetworkResourceId paramétert** a "/subscription/subscription-ID/resourceGroups/resourceGroup/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" formában.

1. Virtuális hálózat és alhálózat hálózati szabályának eltávolítása.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy az alapértelmezett szabályt **megtagadásra** [állítsa be,](#change-the-default-network-access-rule) vagy a hálózati szabályoknak nincs hatása.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

1. Virtuális hálózati szabályok listázása.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Engedélyezze a szolgáltatásvégpontot az Azure Storage számára egy meglévő virtuális hálózaton és alhálózaton.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Hálózati szabály hozzáadása virtuális hálózathoz és alhálózathoz.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Egy másik Azure AD-bérlőhöz tartozó virtuális hálózat alhálózatára vonatkozó szabály hozzáadásához használjon teljesen minősített alhálózati\<azonosítót\>a "/subscriptions/ subscription-ID\</resourceGroups/ resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<\>vNet-name /subnets/ subnets/\<subnet-name\>" formában.
    >
    > Az **előfizetési** paraméter segítségével lekérheti egy másik Azure AD-bérlőhöz tartozó virtuális hálózat alhálózati azonosítóját.

1. Virtuális hálózat és alhálózat hálózati szabályának eltávolítása.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy az alapértelmezett szabályt **megtagadásra** [állítsa be,](#change-the-default-network-access-rule) vagy a hálózati szabályoknak nincs hatása.

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférés megadása internetes IP-tartományból

Beállíthatja, hogy a tárfiókok lehetővé tegyék a hozzáférést bizonyos nyilvános internetes IP-címtartományokból. Ez a konfiguráció hozzáférést biztosít bizonyos internetalapú szolgáltatásokhoz és helyszíni hálózatokhoz, és blokkolja az általános internetes forgalmat.

Adja meg az engedélyezett internetcímtartományokat [CIDR jelöléssel](https://tools.ietf.org/html/rfc4632) *a 16.17.18.0/24* űrlapon vagy egyedi IP-címként, *például 16.17.18.19.*

   > [!NOTE]
   > A "/31" vagy "/32" előtagméreteket használó kis címtartományok nem támogatottak. Ezeket a tartományokat egyedi IP-címszabályok kal kell konfigurálni.

Az IP-hálózati szabályok csak **nyilvános internetes** IP-címek esetén engedélyezettek. A magánhálózatok számára fenntartott IP-címtartományok (az [1918-as RFC-ben](https://tools.ietf.org/html/rfc1918#section-3)meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózatok között _10.*_, _172.16.*_ - _172.31.*_ és _192.168.*_.

   > [!NOTE]
   > Az IP-hálózati szabályok nincsenek hatással az ugyanabból az Azure-régióból származó kérelmekre, mint a tárfiók. [A virtuális hálózati szabályok segítségével](#grant-access-from-a-virtual-network) engedélyezheti az azonos régiós kérelmeket.

  > [!NOTE]
  > A tárfiókkal azonos régióban telepített szolgáltatások privát Azure-IP-címeket használnak a kommunikációhoz. Így nem korlátozhatja a hozzáférést az adott Azure-szolgáltatások nyilvános bejövő IP-címtartomány alapján.

A tárolótűzfal-szabályok konfigurálása csak Az IPV4-címek támogatott.

Minden tárfiók legfeljebb 100 IP-hálózati szabályt támogat.

### <a name="configuring-access-from-on-premises-networks"></a>Hozzáférés konfigurálása helyszíni hálózatokról

Ahhoz, hogy a helyszíni hálózatokból hozzáférést biztosítson a tárfiókhoz egy IP-hálózati szabállyal, azonosítania kell a hálózat által használt internetfelé néző IP-címeket. Segítségért forduljon a hálózati rendszergazdához.

Ha az [ExpressRoute-ot](/azure/expressroute/expressroute-introduction) a helyiségeiből használja, nyilvános társviszony-létesítéshez vagy Microsoft-társviszony-létesítéshez, azonosítania kell a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. A Microsoft társviszony-létesítése esetén a használt NAT IP-címek vagy a szolgáltató által megadott vagy biztosított ügyfél. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése

A tárfiókok IP-hálózati szabályai az Azure Portalon, a PowerShellen vagy a CLIv2-n keresztül kezelhetők.

#### <a name="azure-portal"></a>Azure portál

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a Beállítások menüre, **a Tűzfalak és a virtuális hálózatok parancsra.**

1. Ellenőrizze, hogy a **kijelölt**hálózatokról engedélyezi-e a hozzáférést.

1. Az internetes IP-tartományhoz való hozzáférés engedélyezéséhez adja meg az IP-címet vagy címtartományt (CIDR formátumban) a **Tűzfal** > **címtartománya**mezőbe.

1. Ip-hálózati szabály eltávolításához kattintson a címtartomány melletti kuka ikonra.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse az [Azure PowerShellt,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

1. Ip-hálózati szabályok listázása.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Hálózati szabály hozzáadása egyéni IP-címhez.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Hálózati szabály hozzáadása IP-címtartományhoz.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Adott IP-cím hálózati szabályának eltávolítása.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy az alapértelmezett szabályt **megtagadásra** [állítsa be,](#change-the-default-network-access-rule) vagy a hálózati szabályoknak nincs hatása.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

1. Ip-hálózati szabályok listázása.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Hálózati szabály hozzáadása egyéni IP-címhez.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Hálózati szabály hozzáadása IP-címtartományhoz.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Adott IP-cím hálózati szabályának eltávolítása.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy az alapértelmezett szabályt **megtagadásra** [állítsa be,](#change-the-default-network-access-rule) vagy a hálózati szabályoknak nincs hatása.

## <a name="exceptions"></a>Kivételek

A hálózati szabályok segítségével biztonságos környezetet hozhat létre az alkalmazások és az adatok közötti kapcsolatokhoz a legtöbb esetben. Egyes alkalmazások azonban az Azure-szolgáltatásoktól függenek, amelyek nem különíthetők el egyedileg a virtuális hálózati vagy IP-címszabályok on keresztül. De az ilyen szolgáltatásokat kell biztosítani a tárolás, hogy a teljes alkalmazás működését. Ilyen esetekben használhatja a ***Megbízható Microsoft-szolgáltatások engedélyezése beállítást,*** hogy az ilyen szolgáltatások hozzáférhessenek az adatokhoz, naplókhoz vagy elemzésekhez.

### <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások

Egyes Microsoft-szolgáltatások olyan hálózatokból működnek, amelyek nem szerepelhetnek a hálózati szabályokban. Az ilyen megbízható Microsoft-szolgáltatások egy részhalmazának hozzáférést biztosíthat a tárfiókhoz, más alkalmazások hálózati szabályainak fenntartása mellett. Ezek a megbízható szolgáltatások ezután erős hitelesítést fognak használni a tárfiókhoz való biztonságos csatlakozáshoz. A Microsoft-szolgáltatások hoz két megbízhatósági hozzáférési módot engedélyeztünk.

- Egyes szolgáltatások erőforrásai, ha regisztrálva van **az előfizetésben,** hozzáférhetnek a tárfiók **ugyanabban az előfizetésben** a kiválasztott műveletek, például naplók írása vagy biztonsági mentés.
- Egyes szolgáltatások erőforrásai explicit hozzáférést biztosíthatnak a tárfiókhoz, ha **rbac-szerepkört rendelnek** a rendszer által hozzárendelt felügyelt identitáshoz.


Ha engedélyezi a **Megbízható Microsoft-szolgáltatások engedélyezése beállítást,** a következő szolgáltatások erőforrásai, amelyek ugyanabban az előfizetésben vannak regisztrálva, mint a tárfiók, a leírt műveletek korlátozott készletéhez kapnak hozzáférést:

| Szolgáltatás                  | Erőforrásszolgáltató neve     | Engedélyezett műveletek                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Futtassa a nem felügyelt lemezek biztonsági mentéseit és visszaállítását az IAAS virtuális gépeken. (nem szükséges a felügyelt lemezek). [További információ](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Lehetővé teszi az adatok importálását az Azure-ba a Data Box használatával. [További információ](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Egyéni lemezkép-létrehozás és műtermék-telepítés. [További információ](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Engedélyezze a Blob Storage eseményközzétételét, és engedélyezze az Event Grid számára a tárolási várólistákban való közzétételt. További információ a [blobtárolási eseményekről](/azure/event-grid/event-sources) és [a várólistákban való közzétételről.](/azure/event-grid/event-handlers) |
| Azure Event Hubs         | Microsoft.EventHub         | Archiválja az adatokat az Event Hubs Capture segítségével. [További információ.](/azure/event-hubs/event-hubs-capture-overview) |
| Azure File Sync          | Microsoft.StorageSync      | Lehetővé teszi, hogy a prem fájlkiszolgálót az Azure-fájlmegosztások gyorsítótárába alakítsa. Lehetővé teszi a több telephelyes szinkronizálást, a gyors vészhelyreállítást és a felhőoldali biztonsági mentést. [További információ](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Az új HDInsight-fürt alapértelmezett fájlrendszerének kezdeti tartalmának kiépítése. [További információ](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure importálásexportálás      | Microsoft.ImportExport     | Lehetővé teszi az adatok importálását az Azure-ba, és az adatok exportálását az Azure-ból az Importálás/exportálás szolgáltatás használatával. [További információ](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Lehetővé teszi a figyelési adatok biztonságos tárfiókba történő írását, beleértve az erőforrás-diagnosztikai naplókat, az Azure Active Directory bejelentkezési és naplózási naplóit, valamint a Microsoft Intune-naplókat. [További információ](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure hálózatkezelés         | Microsoft.Network          | Hálózati forgalmi naplók tárolása és elemzése. [További információ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Engedélyezze a replikációt az Azure IaaS virtuális gépek vész-helyreállítása érdekében tűzfal-kompatibilis gyorsítótár, forrás vagy céltárfiókok használata esetén.  [További információ](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

A **Megbízható Microsoft-szolgáltatások engedélyezése...** beállítás azt is lehetővé teszi, hogy az alábbi szolgáltatások egy adott példánya hozzáférjen a tárfiókhoz, ha explicit módon [rbac-szerepkört rendel](storage-auth-aad.md#assign-rbac-roles-for-access-rights) az adott erőforráspéldány [rendszeráltal hozzárendelt felügyelt identitásához.](../../active-directory/managed-identities-azure-resources/overview.md) Ebben az esetben a példány hozzáférési hatóköre megfelel a felügyelt identitáshoz rendelt RBAC szerepkörnek.

| Szolgáltatás                        | Erőforrásszolgáltató neve                 | Cél            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Lehetővé teszi, hogy a Cognitive Search-szolgáltatások hozzáférhessenek az indexeléshez, feldolgozáshoz és lekérdezéshez. |
| Azure Container Registry Tasks | Microsoft.ContainerRegistry/regisztika | Az ACR-feladatok tárolórendszerképek létrehozásakor hozzáférhetnek a tárfiókokhoz. |
| Azure Data Factory             | Microsoft.DataFactory/gyárak        | Lehetővé teszi a hozzáférést a tárfiókok hoz az ADF futásidejű keresztül. |
| Azure Data Share               | Microsoft.DataShare/fiókok           | Lehetővé teszi a hozzáférést a tárfiókok hoz keresztül adatmegosztás. |
| Azure Logic Apps               | Microsoft.Logic/munkafolyamatok              | Lehetővé teszi a logikai alkalmazások számára a tárfiókok elérését. [További információ](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning szolgáltatás | Microsoft.MachineLearningServices      | Az engedélyezett Azure Machine Learning-munkaterületek a kísérlet kimenetét, modelljeit és naplóit a Blob storage-ba írják, és elolvassák az adatokat. [További információ](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Lehetővé teszi az adatok importálását és exportálását adott SQL Database-példányokból a PolyBase használatával. [További információ](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Lehetővé teszi, hogy egy streamelési feladatból származó adatokat kell írni a Blob storage.Allows data from a streaming job to be written to Blob storage. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. [További információ](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft.Synapse/munkaterületek          | Lehetővé teszi az adatokhoz való hozzáférést az Azure Storage-ban a Synapse Analytics szolgáltatásból. |


### <a name="storage-analytics-data-access"></a>Tárolási elemzési adatok elérése

Bizonyos esetekben a diagnosztikai naplók és metrikák olvasásához való hozzáférés a hálózati határon kívülről szükséges. A megbízható szolgáltatások hozzáférésének konfigurálásakor a tárfiókhoz, engedélyezheti az olvasási hozzáférést a naplófájlok, metrikák táblák, vagy mindkettő. [További információ a tárterület-elemzésekkel való munkáról.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Kivételek kezelése

A hálózati szabály kivételeket az Azure Portalon, a PowerShellen vagy az Azure CLI v2-n keresztül kezelheti.

#### <a name="azure-portal"></a>Azure portál

1. Lépjen a megvédeni kívánt tárfiókra.

1. Kattintson a Beállítások menüre, **a Tűzfalak és a virtuális hálózatok parancsra.**

1. Ellenőrizze, hogy a **kijelölt**hálózatokról engedélyezi-e a hozzáférést.

1. A **Kivételek csoportban**válassza ki a megadni kívánt kivételeket.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse az [Azure PowerShellt,](/powershell/azure/install-Az-ps) és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

1. A tárfiók hálózati szabályainak kivételei megjelenítése.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Konfigurálja a tárfiók hálózati szabályai alóli kivételeket.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Távolítsa el a tárfiók hálózati szabályai alóli kivételeket.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) **megtagadja,** vagy a kivételek eltávolításának nincs hatása.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

1. A tárfiók hálózati szabályainak kivételei megjelenítése.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Konfigurálja a tárfiók hálózati szabályai alóli kivételeket.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Távolítsa el a tárfiók hálózati szabályai alóli kivételeket.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) **megtagadja,** vagy a kivételek eltávolításának nincs hatása.

## <a name="next-steps"></a>További lépések

További információ az Azure Network szolgáltatásvégpontjairól a [szolgáltatásvégpontokban.](/azure/virtual-network/virtual-network-service-endpoints-overview)

Az Azure Storage biztonsági útmutatójában mélyebbre áshat az [Azure Storage biztonsága című könyvben.](../blobs/security-recommendations.md)
