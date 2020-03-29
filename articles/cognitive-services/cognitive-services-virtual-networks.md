---
title: Virtuális hálózatok
titleSuffix: Azure Cognitive Services
description: Konfigurálja a cognitive Services-erőforrások réteges hálózati biztonságát.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371222"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Az Azure Cognitive Services virtuális hálózatainak konfigurálása

Az Azure Cognitive Services egy réteges biztonsági modellt biztosít. Ez a modell lehetővé teszi, hogy a Cognitive Services-fiókok biztonságos sá a hálózatok egy adott részhalmazát. Ha a hálózati szabályok konfigurálva vannak, csak a megadott hálózatokon keresztül adatokat kérő alkalmazások férhetnek hozzá a fiókhoz. Az erőforrásokhoz való hozzáférést a kérelemszűréssel korlátozhatja. Csak meghatározott IP-címekről, IP-tartományokból vagy az [Azure virtuális hálózatok](../virtual-network/virtual-networks-overview.md)alhálózataiból származó kérelmek engedélyezése. Ha érdekli ez az ajánlat, előzetes hozzáférést kell [kérnie.](https://aka.ms/cog-svc-vnet-signup)

Egy alkalmazás, amely hozzáfér a Cognitive Services-erőforrás, amikor a hálózati szabályok érvényben lévő engedélyezést igényel. Az engedélyezés t az [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) hitelesítő adatai vagy egy érvényes API-kulcs támogatja.

> [!IMPORTANT]
> A Cognitive Services-fiók tűzfalszabályainak bekapcsolása alapértelmezés szerint blokkolja a bejövő adatkérelmeket. A kérelmek átengedése érdekében az alábbi feltételek egyikének kell teljesülnie:
> * A kérelemnek egy Azure virtuális hálózaton (VNet) működő szolgáltatásból kell származnia a cél Cognitive Services-fiók engedélyezett alhálózati listáján. A virtuális hálózatról származó kérelmek végpontját a Cognitive Services-fiók [egyéni altartományaként](cognitive-services-custom-subdomains.md) kell beállítani.
> * Vagy a kérelemnek az IP-címek engedélyezett listájáról kell származnia.
>
> A letiltott kérelmek közé tartoznak a más Azure-szolgáltatásokból, az Azure Portalról, a naplózási és metrikaszolgáltatásokból származó kérések, és így tovább.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Forgatókönyvek

A Cognitive Services-erőforrás biztonságossá tétele érdekében először konfiguráljon egy szabályt, amely alapértelmezés szerint megtagadja az összes hálózat (beleértve az internetes forgalmat is) forgalmához való hozzáférést. Ezután konfigurálja azokat a szabályokat, amelyek hozzáférést biztosítanak az adott virtuális hálózatokból érkező forgalomhoz. Ez a konfiguráció lehetővé teszi, hogy hozzon létre egy biztonságos hálózati határ az alkalmazások számára. A szabályokat úgy is beállíthatja, hogy hozzáférést biztosítson a forgalomhoz bizonyos nyilvános internetes IP-címtartományokból, lehetővé téve az adott internetes vagy helyszíni ügyfelek kapcsolatait.

A hálózati szabályok az Azure Cognitive Services összes hálózati protokollján érvénybe lépnek, beleértve a REST-et és a WebSocket-t is. Adatok eléréséhez eszközök használatával, például az Azure tesztkonzolok, explicit hálózati szabályokat kell konfigurálni. Hálózati szabályokat alkalmazhat a meglévő Cognitive Services-erőforrásokra, vagy amikor új Cognitive Services-erőforrásokat hoz létre. A hálózati szabályok alkalmazása után minden kérelemre érvényes.

## <a name="supported-regions-and-service-offerings"></a>Támogatott régiók és szolgáltatási ajánlatok

Az alábbiakban felsorolt cognitive Services virtuális hálózati támogatása az *USA középső régiójára,* az USA déli régiójára, az USA keleti *régiójára,* az USA *nyugati* *régiójára,* *Észak-Európára,* *Észak-Afrikára,* *Nyugat-Európára, Közép-Indiára,* *Kelet-Keletre, Az* *USA nyugati régiójára*és az *USA Virginiai Államkormányának* Azure régióira korlátozódik. *Central India* Ha a szolgáltatásajánlat nem szerepel itt, nem támogatja a virtuális hálózatokat.

> [!div class="checklist"]
> * [Anomaly Detector](./anomaly-detector/index.yml)
> * [Számítógépes látás](./computer-vision/index.yml)
> * [Tartalommoderátor](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Arcfelismerés](./face/index.yml)
> * [Form Recognizer](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizer](./personalizer/index.yml)
> * [Szövegelemzés](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

Az alábbiakban felsorolt Cognitive Services virtuális hálózati támogatása az *USA középső régiójára,* az *USA déli régiójára, az USA keleti* *régiójára,* az USA *nyugati régiójára, a* *Globális*régióra és az USA *virginiai kormányának* Azure-régióira korlátozódik.
> [!div class="checklist"]
> * [Fordító szövege](./translator/index.yml)

## <a name="service-tags"></a>Szolgáltatáscímkék
A fenti szolgáltatások virtuális hálózati szolgáltatásvégpontjainak támogatása mellett a Cognitive Services támogatja a kimenő hálózati szabályok konfigurációjának szolgáltatáscímkéjét is. A cognitiveServicesServices service tag a következő szolgáltatásokat tartalmazza.
> [!div class="checklist"]
> * [Anomaly Detector](./anomaly-detector/index.yml)
> * [Számítógépes látás](./computer-vision/index.yml)
> * [Tartalommoderátor](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Arcfelismerés](./face/index.yml)
> * [Form Recognizer](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizer](./personalizer/index.yml)
> * [Szövegelemzés](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Fordító szövege](./translator/index.yml)
> * [Beszédszolgáltatás](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Az alapértelmezett hálózati hozzáférési szabály módosítása

Alapértelmezés szerint a Cognitive Services-erőforrások bármely hálózaton lévő ügyfelektől fogadnak el kapcsolatokat. A kijelölt hálózatok hozzáférésének korlátozásához elsőként módosítania kell az alapértelmezett műveletet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet az alkalmazások Azure Cognitive Serviceshez való csatlakozási képességére. Az alapértelmezett hálózati szabály **megtagadása** letiltja az adatokhoz való összes hozzáférést, kivéve, ha a hozzáférést **biztosító** konkrét hálózati szabályokat is alkalmazza. Ügyeljen arra, hogy az alapértelmezett szabály hozzáférés-megtagadásra való módosítása előtt engedélyezze a hálózati szabályokat alkalmazó engedélyezett hálózatokhoz való hozzáférést. Ha engedélyezi a helyszíni hálózat IP-címeinek listázását, mindenképpen adja hozzá az összes lehetséges kimenő nyilvános IP-címet a helyszíni hálózatról.

### <a name="managing-default-network-access-rules"></a>Alapértelmezett hálózati hozzáférési szabályok kezelése

A Cognitive Services-erőforrások alapértelmezett hálózati hozzáférési szabályait az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül kezelheti.

# <a name="azure-portal"></a>[Azure-portál](#tab/portal)

1. Nyissa meg a biztonságoskognitív szolgáltatások erőforrást.

1. Válassza a Virtuális **hálózat**nevű **ERŐFORRÁS-GAZDÁLKODÁS** menüt .

   ![Virtuális hálózat beállítás](media/vnet/virtual-network-blade.png)

1. Ha alapértelmezés szerint meg szeretné tagadni a hozzáférést, válassza a **kijelölt hálózatokról**való hozzáférést. A **Kiválasztott hálózatok** beállítás egyedül, a konfigurált **virtuális hálózatok** vagy **címtartományok** nélkül – minden hozzáférés gyakorlatilag megtagadva. Ha minden hozzáférés megtagadva, a Cognitive Services erőforrás t akar felhasználni a kérelmek nem engedélyezettek. Az Azure Portal, az Azure PowerShell vagy az Azure CLI továbbra is használható a Cognitive Services-erőforrás konfigurálásához.
1. Ha minden hálózatról engedélyezni szeretné a forgalmat, engedélyezze a hozzáférést az **Összes hálózatnak**.

   ![Virtuális hálózatok megtagadása](media/vnet/virtual-network-deny.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Telepítse az [Azure PowerShellt,](/powershell/azure/install-az-ps) és [jelentkezzen be,](/powershell/azure/authenticate-azureps)vagy válassza **a Próbálja ki**lehetőséget.

1. A Cognitive Services-erőforrás alapértelmezett szabályának állapotának megjelenítése.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Állítsa be az alapértelmezett szabályt a hálózati hozzáférés alapértelmezés szerint iszinójának megtagadására.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Állítsa be az alapértelmezett szabályt, hogy alapértelmezés szerint engedélyezze a hálózati hozzáférést.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be,](/cli/azure/authenticate-azure-cli)vagy válassza **a Próbálja ki**lehetőséget.

1. A Cognitive Services-erőforrás alapértelmezett szabályának állapotának megjelenítése.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Állítsa be az alapértelmezett szabályt a hálózati hozzáférés alapértelmezés szerint iszinójának megtagadására.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Állítsa be az alapértelmezett szabályt, hogy alapértelmezés szerint engedélyezze a hálózati hozzáférést.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Hozzáférés megadása virtuális hálózatról

A Cognitive Services-erőforrások konfigurálása csak bizonyos alhálózatokból való hozzáférés engedélyezéséhez konfigurálható. Az engedélyezett alhálózatok tartozhatnak egy virtuális hálózat ugyanabban az előfizetésben, vagy egy másik előfizetés, beleértve egy másik Azure Active Directory-bérlőhöz tartozó előfizetések.

Engedélyezze a [szolgáltatásvégpontot](../virtual-network/virtual-network-service-endpoints-overview.md) az Azure Cognitive Services számára a virtuális hálózaton belül. A szolgáltatásvégpont a virtuális hálózatról az Azure Cognitive Services szolgáltatáshoz vezető optimális útvonalon keresztül irányítja a forgalmat. Az alhálózat és a virtuális hálózat identitásait is továbbítja az egyes kérelmek. A rendszergazdák ezután konfigurálhatják a Cognitive Services-erőforrás hálózati szabályait, amelyek lehetővé teszik a virtuális hálózat adott alhálózataiból érkező kérelmek fogadását. Az ezeken a hálózati szabályokon keresztül hozzáféréssel rendelkező ügyfeleknek továbbra is meg kell felelniük a Cognitive Services-erőforrás engedélyezési követelményeinek az adatok eléréséhez.

Minden Cognitive Services-erőforrás legfeljebb 100 virtuális hálózati szabályt támogat, amelyek [ip-hálózati szabályokkal](#grant-access-from-an-internet-ip-range)kombinálhatók.

### <a name="required-permissions"></a>Szükséges engedélyek

Virtuális hálózati szabály kognitív szolgáltatások erőforrásra való alkalmazásához a felhasználónak rendelkeznie kell a hozzáadott alhálózatok megfelelő engedélyekkel. A szükséges engedély az alapértelmezett *közreműködői* szerepkör, vagy a *Cognitive Services közreműködői* szerepkör. A szükséges engedélyek egyéni szerepkör-definíciókhoz is hozzáadhatók.

Cognitive Services-erőforrás és a virtuális hálózatok hozzáférési lehet a különböző előfizetések, beleértve az előfizetések, amelyek egy másik Azure AD-bérlő része.

> [!NOTE]
> A más Azure Active Directory-bérlő részét tartalmazó virtuális hálózatok alhálózataihoz hozzáférést biztosító szabályok konfigurálása jelenleg csak a Powershell, a CLI és a REST API-k on keresztül támogatott. Ezek a szabályok nem konfigurálhatók az Azure Portalon keresztül, bár megtekinthetők a portálon.

### <a name="managing-virtual-network-rules"></a>Virtuális hálózati szabályok kezelése

A Cognitive Services-erőforrások virtuális hálózati szabályai az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül kezelhetők.

# <a name="azure-portal"></a>[Azure-portál](#tab/portal)

1. Nyissa meg a biztonságoskognitív szolgáltatások erőforrást.

1. Válassza a Virtuális **hálózat**nevű **ERŐFORRÁS-GAZDÁLKODÁS** menüt .

1. Ellenőrizze, hogy a **kijelölt**hálózatokról engedélyezi-e a hozzáférést.

1. Ha meglévő hálózati szabállyal szeretne hozzáférést biztosítani egy virtuális hálózathoz, a **Virtuális hálózatok**csoportban válassza a Meglévő virtuális **hálózat hozzáadása**lehetőséget.

   ![Meglévő virtuális hálózat hozzáadása](media/vnet/virtual-network-add-existing.png)

1. Válassza a **Virtuális hálózatok** és **alhálózatok beállításokat,** majd az **Engedélyezés**lehetőséget.

   ![Meglévő virtuális hálózat részleteinek hozzáadása](media/vnet/virtual-network-add-existing-details.png)

1. Új virtuális hálózat létrehozásához és elérésének engedélyezéséhez válassza **az Új virtuális hálózat hozzáadása**lehetőséget.

   ![Új virtuális hálózat hozzáadása](media/vnet/virtual-network-add-new.png)

1. Adja meg az új virtuális hálózat létrehozásához szükséges információkat, majd válassza a **Létrehozás gombot.**

   ![Virtuális hálózat létrehozása](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Ha az Azure Cognitive Services szolgáltatásvégpontja korábban nem volt konfigurálva a kiválasztott virtuális hálózathoz és alhálózatokhoz, konfigurálhatja azt a művelet részeként.
    >
    > Jelenleg csak az azonos Azure Active Directory-bérlőhöz tartozó virtuális hálózatok jelennek meg a szabály létrehozása során. Egy másik bérlőhöz tartozó virtuális hálózat alhálózatához való hozzáférés engedélyezéséhez használja a Powershell, cli vagy REST API-kat.

1. Virtuális hálózati vagy alhálózati szabály eltávolításához válassza **a ...** lehetőséget a virtuális hálózat vagy alhálózat helyi menüjének megnyitásához, majd az **Eltávolítás gombra.**

   ![Virtuális hálózat eltávolítása](media/vnet/virtual-network-remove.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Telepítse az [Azure PowerShellt,](/powershell/azure/install-az-ps) és [jelentkezzen be,](/powershell/azure/authenticate-azureps)vagy válassza **a Próbálja ki**lehetőséget.

1. Virtuális hálózati szabályok listázása.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Szolgáltatásvégpont engedélyezése az Azure Cognitive Services számára egy meglévő virtuális hálózaton és alhálózaton.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Hálózati szabály hozzáadása virtuális hálózathoz és alhálózathoz.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Ha egy másik Azure AD-bérlőhöz tartozó virtuális hálózatal rendelkező alhálózathoz szeretne hálózati szabályt hozzáadni, használjon egy teljesen minősített **VirtualNetworkResourceId paramétert** a "/subscription/subscription-ID/resourceGroups/resourceGroup/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" formában.

1. Virtuális hálózat és alhálózat hálózati szabályának eltávolítása.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be,](/cli/azure/authenticate-azure-cli)vagy válassza **a Próbálja ki**lehetőséget.

1. Virtuális hálózati szabályok listázása.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Szolgáltatásvégpont engedélyezése az Azure Cognitive Services számára egy meglévő virtuális hálózaton és alhálózaton.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Hálózati szabály hozzáadása virtuális hálózathoz és alhálózathoz.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Ha egy másik Azure AD-bérlőhöz tartozó virtuális hálózatal rendelkező alhálózathoz szeretne szabályt hozzáadni, használjon teljesen minősített alhálózati azonosítót a "/subscription/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" formában.
    > 
    > Az **előfizetési** paraméter segítségével lekérheti egy másik Azure AD-bérlőhöz tartozó virtuális hálózat alhálózati azonosítóját.

1. Virtuális hálózat és alhálózat hálózati szabályának eltávolítása.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Ügyeljen arra, hogy az alapértelmezett szabályt **megtagadásra** [állítsa be,](#change-the-default-network-access-rule) vagy a hálózati szabályoknak nincs hatása.

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférés megadása internetes IP-tartományból

Beállíthatja, hogy a Cognitive Services-erőforrások lehetővé tegyék a hozzáférést adott nyilvános internetes IP-címtartományokból. Ez a konfiguráció hozzáférést biztosít bizonyos szolgáltatásokhoz és helyszíni hálózatokhoz, hatékonyan blokkolva az általános internetes forgalmat.

Adja meg az engedélyezett internetcímtartományokat [cidr jelöléssel](https://tools.ietf.org/html/rfc4632) az űrlapon `16.17.18.0/24` vagy egyedi IP-címekként, például `16.17.18.19`.

   > [!Tip]
   > A "/31" vagy "/32" előtagméreteket használó kis címtartományok nem támogatottak. Ezeket a tartományokat egyedi IP-címszabályok kal kell konfigurálni.

Az IP-hálózati szabályok csak **nyilvános internetes** IP-címek esetén engedélyezettek. A magánhálózatok számára fenntartott IP-címtartományok (az [1918-as RFC-ben](https://tools.ietf.org/html/rfc1918#section-3)meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózatok közé `10.*`tartoznak a kezdő címek , `172.16.*`  -  `172.31.*`és `192.168.*`a .

   > [!NOTE]
   > Az IP-hálózati szabályok nincsenek hatással az ugyanabból az Azure-régióból származó kérelmekre, mint a Cognitive Services-erőforrás. [A virtuális hálózati szabályok segítségével](#grant-access-from-a-virtual-network) engedélyezheti az azonos régiós kérelmeket.

Jelenleg csak az IPV4-címek támogatottak. Minden Cognitive Services-erőforrás legfeljebb 100 IP-hálózati szabályt támogat, amelyek [virtuális hálózati szabályokkal](#grant-access-from-a-virtual-network)kombinálhatók.

### <a name="configuring-access-from-on-premises-networks"></a>Hozzáférés konfigurálása helyszíni hálózatokról

Ahhoz, hogy a helyszíni hálózatokból hozzáférést biztosítson a Cognitive Services-erőforráshoz egy IP-hálózati szabállyal, azonosítania kell a hálózat által használt internetfelé néző IP-címeket. Segítségért forduljon a hálózati rendszergazdához.

Ha a helyszíni [ExpressRoute-ot](../expressroute/expressroute-introduction.md) nyilvános társviszony-létesítéshez vagy a Microsoft társviszony-létesítéséhez használja, azonosítania kell a NAT IP-címeket. Nyilvános társviszony-létesítéshez alapértelmezés szerint minden ExpressRoute-kapcsolati kapcsolat két NAT IP-címet használ. Mindegyik az Azure szolgáltatásforgalmára vonatkozik, amikor a forgalom belép a Microsoft Azure hálózati gerinchálózatába. Microsoft társviszony-létesítés esetén a használt NAT IP-címek vagy a szolgáltató által megadott vagy megadott ügyfél. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése

A Cognitive Services-erőforrások IP-hálózati szabályai az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül kezelhetők.

# <a name="azure-portal"></a>[Azure-portál](#tab/portal)

1. Nyissa meg a biztonságoskognitív szolgáltatások erőforrást.

1. Válassza a Virtuális **hálózat**nevű **ERŐFORRÁS-GAZDÁLKODÁS** menüt .

1. Ellenőrizze, hogy a **kijelölt**hálózatokról engedélyezi-e a hozzáférést.

1. Az internetes IP-tartományhoz való hozzáférés engedélyezéséhez írja be az IP-címet vagy címtartományt [(CIDR formátumban)](https://tools.ietf.org/html/rfc4632)a **Tűzfal** > **címtartománya**mezőbe. Csak érvényes nyilvános IP(nem fenntartott) címek fogadhatók el.

   ![IP-tartomány hozzáadása](media/vnet/virtual-network-add-ip-range.png)

1. IP-hálózati szabály eltávolításához jelölje <span class="docon docon-delete x-hidden-focus"></span> ki a címtartomány melletti kuka ikont.

   ![IP-tartomány törlése](media/vnet/virtual-network-delete-ip-range.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Telepítse az [Azure PowerShellt,](/powershell/azure/install-az-ps) és [jelentkezzen be,](/powershell/azure/authenticate-azureps)vagy válassza **a Próbálja ki**lehetőséget.

1. Ip-hálózati szabályok listázása.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Hálózati szabály hozzáadása egyéni IP-címhez.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Hálózati szabály hozzáadása IP-címtartományhoz.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Adott IP-cím hálózati szabályának eltávolítása.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be,](/cli/azure/authenticate-azure-cli)vagy válassza **a Próbálja ki**lehetőséget.

1. Ip-hálózati szabályok listázása.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Hálózati szabály hozzáadása egyéni IP-címhez.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Hálózati szabály hozzáadása IP-címtartományhoz.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Adott IP-cím hálózati szabályának eltávolítása.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP-címtartomány hálózati szabályának eltávolítása.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Ügyeljen arra, hogy az alapértelmezett szabályt **megtagadásra** [állítsa be,](#change-the-default-network-access-rule) vagy a hálózati szabályoknak nincs hatása.

## <a name="next-steps"></a>További lépések

* Fedezze fel a különböző [Azure Cognitive Services-szolgáltatásokat](welcome.md)
* További információ az [Azure virtuális hálózati szolgáltatás végpontjairól](../virtual-network/virtual-network-service-endpoints-overview.md)