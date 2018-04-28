---
title: Hozzon létre egy Azure virtuális hálózat (klasszikus), több alhálózattal |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy virtuális hálózat (klasszikus), több alhálózattal az Azure-ban.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: c41a1057bb87c70362477d221b69ca3f5137dec4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Hozzon létre egy virtuális hálózat (klasszikus), több alhálózattal

> [!IMPORTANT]
> Két Azure tartozik [különböző üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) az erőforrások létrehozására és kezelésére vonatkozó: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy a legtöbb új virtuális hálózat létrehozása a [erőforrás-kezelő](quick-create-portal.md) üzembe helyezési modellben.

Ebben az oktatóanyagban útmutató alapszintű Azure virtuális hálózat létrehozása (klasszikus), amely rendelkezik a nyilvános és titkos különálló alhálózatokat. Azure-erőforrások, például a virtuális gépek és felhőszolgáltatások az alhálózat hozhat létre. Létrehozott virtuális hálózatok (klasszikus) erőforrásokhoz kommunikálhatnak egymással, és más hálózatok, virtuális hálózathoz csatlakozó erőforrásokkal.

További információk [virtuális hálózati](manage-virtual-network.md) és [alhálózati](virtual-network-manage-subnet.md) beállításait.

> [!WARNING]
> Virtuális hálózatok (klasszikus) Azure azonnal törli amikor egy [előfizetés le van tiltva](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Virtuális hálózatok (klasszikus) törlődnek, függetlenül attól, hogy erőforrásokat szerepel-e a virtuális hálózat. Ha később újra engedélyezi az előfizetés, a virtuális hálózat már létező erőforrásokat kell újból létre kell hozni.

Létrehozhat egy virtuális hálózat (klasszikus) használatával a [Azure-portálon](#portal), a [Azure parancssori felület (CLI) 1.0](#azure-cli), vagy [PowerShell](#powershell).

## <a name="portal"></a>Portál

1. A böngészőben, válassza a [Azure-portálon](https://portal.azure.com). Jelentkezzen be a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kattintson a **hozzon létre egy erőforrást** a portálon.
3. Adja meg *virtuális hálózati* a a **keresése a piactéren** tetején lévő mezőbe a **új** ablaktábla akkor jelenik meg. Kattintson a **virtuális hálózati** amikor megjelenik a keresési eredmények között.
4. Válassza ki **klasszikus** a a **telepítési modell kiválasztása** párbeszédpanel a **virtuális hálózati** ablaktábla akkor jelenik meg, majd kattintson **létrehozása**. 
5. Adja meg a következő értékeket a a **virtuális hálózat létrehozása (klasszikus)** majd rákattinthat **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVNet|
    |Címtér|10.0.0.0/16|
    |Alhálózat neve|Nyilvános|
    |Alhálózati címtartomány|10.0.0.0/24|
    |Erőforráscsoport|Hagyja **hozzon létre új** kiválasztva, és írja be **myResourceGroup**.|
    |Egyes előfizetésekhez és helyekhez|Válassza ki az egyes előfizetésekhez és helyekhez.

    Ha most ismerkedik az Azure-ba, további információ [erőforráscsoportok](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), és [helyek](https://azure.microsoft.com/regions) (más néven *régiók*).
4. A portál csak egy alhálózat is létrehozhat, ha a virtuális hálózat létrehozása. Ebben az oktatóanyagban létrehoz egy második alhálózatot a virtuális hálózat létrehozása után. Később létrehozhat az internetről elérhető erőforrások a **nyilvános** alhálózat. Is létrehozhat, amelyek nem érhetők el az interneten lévő erőforrásokat a **titkos** alhálózat. A második alhálózat létrehozásához adja meg a **myVnet** a a **keresési erőforrások** be az oldal tetején. Kattintson a **myVnet** amikor megjelenik a keresési eredmények között.
5. Kattintson a **alhálózatok** (a a **beállítások** szakaszban) a a **virtuális hálózat létrehozása (klasszikus)** megjelenő panelen.
6. Kattintson a **+ Hozzáadás** a a **myVnet - alhálózatok** ablaktábla akkor jelenik meg.
7. Adja meg **titkos** a **neve** a a **alhálózat hozzáadása** ablaktáblán. Adja meg **10.0.1.0/24** a **-címtartományt**.  Kattintson az **OK** gombra.
8. A a **myVnet - alhálózatok** panelen megtekintheti a **nyilvános** és **titkos** létrehozott alhálózatokat.
9. **Nem kötelező**: Ez az oktatóanyag befejezése után előfordulhat, hogy törölni kívánja az erőforrásokat, amelyek hozott létre, így használati költségek:
    - Kattintson a **áttekintése** a a **myVnet** ablaktáblán.
    - Kattintson a **törlése** ikonra a **myVnet** ablaktáblán.
    - A törlés megerősítéséhez kattintson **Igen** a a **virtuális hálózati Delete** mezőbe.

## <a name="azure-cli"></a>Azure CLI

1. Megadhat [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy a CLI-t az Azure-felhő rendszerhéj belül használja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Segítség kérése parancssori felület parancsait, írja be a következőt `azure <command> --help`. 
2. A CLI-munkamenetben jelentkezzen be az Azure az alábbi paranccsal. Ha **kipróbálás** az alábbi mezőbe, a felhő rendszerhéj megnyitása. Bejelentkezhet az Azure-előfizetéshez, anélkül, hogy az alábbi parancs beírásával:

    ```azurecli-interactive
    azure login
    ```

3. A parancssori felület szolgáltatásfelügyelet módban van, adjon meg a következő parancsot:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Virtuális hálózat létrehozása a saját alhálózattal:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Hozzon létre egy nyilvános alhálózatot a virtuális hálózaton belül:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Tekintse át a virtuális hálózat és az alhálózatok:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Nem kötelező**: Előfordulhat, hogy valóban törli a létrehozott Ez az oktatóanyag befejezésekor, hogy a használati költségek erőforrások:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Bár nem adhat meg egy erőforráscsoportot a virtuális hálózat (klasszikus) létrehozása a parancssori felület használatával, Azure hoz létre a virtuális hálózat nevű erőforráscsoport *alapértelmezett-hálózat*.

## <a name="powershell"></a>PowerShell

1. Telepítse a legújabb verzióját a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modul. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítson el egy PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be az Azure-ba a `Add-AzureAccount` paranccsal.
4. A következő elérési út és fájlnév, szükség esetén módosítsa, majd a hálózati konfigurációs fájlok exportálása:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. A virtuális hálózat létrehozása a nyilvános és titkos alhálózat, bármilyen szövegszerkesztővel hozzáadása a **VirtualNetworkSite** elem a hálózati konfigurációs fájl.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Tekintse át a teljes [hálózati konfigurációs fájl séma](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. A hálózati konfigurációs fájl importálása:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Megváltozott hálózati konfigurációs fájlok importálása (klasszikus) az előfizetéshez létező virtuális hálózatok módosításai okozhatják. Csak adja hozzá a korábbi virtuális hálózatot, és módosítsa vagy távolítsa el a meglévő virtuális hálózatok az előfizetésből nem biztosítására. 

7. Tekintse át a virtuális hálózat és az alhálózatok:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Nem kötelező**: Előfordulhat, hogy valóban törli az erőforrásokat, amelyek létrehozott Ez az oktatóanyag befejezésekor, hogy a használati költségek. A virtuális hálózat törlése teljes lépések 4 – 6 újra, az idő eltávolítása a **VirtualNetworkSite** 5 lépésben hozzáadott elemet.
 
> [!NOTE]
> Bár nem adhat meg egy erőforráscsoportot, egy virtuális hálózat (klasszikus) létrehozása a PowerShell használatával, Azure hoz létre a virtuális hálózat nevű erőforráscsoport *alapértelmezett-hálózat*.

---

## <a name="next-steps"></a>További lépések

- Minden virtuális hálózati és alhálózati beállításaival kapcsolatos további tudnivalókért lásd: [virtuális hálózatok kezeléséhez](manage-virtual-network.md) és [kezelheti a virtuális hálózati alhálózat](virtual-network-manage-subnet.md). Számos közül a virtuális hálózatok és alhálózatok éles környezetben különbözőek a követelmények teljesítéséhez.
- Bejövő és kimenő forgalmat szűréséhez, létrehozása és alkalmazása [hálózati biztonsági csoportok](virtual-networks-nsg.md) alhálózatokra.
- Hozzon létre egy [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy egy [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet, majd csatlakozzon egy létező virtuális hálózatot.
- Csatlakozás Azure ugyanazon a helyen lévő két virtuális hálózatok, hozzon létre egy [virtuális hálózati társviszony-létesítés](create-peering-different-deployment-models.md) a virtuális hálózatok között. A virtuális hálózat (klasszikus) is egyenrangú a egy virtuális hálózat (Resource Manager), de nem hozható létre társviszony-létesítés (klasszikus) virtuális hálózatok között.
- A virtuális hálózati csatlakozás egy helyszíni hálózat segítségével egy [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) körön.
