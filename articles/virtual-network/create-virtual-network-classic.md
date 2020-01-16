---
title: Azure-beli virtuális hálózat (klasszikus) létrehozása több alhálózattal | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre virtuális hálózatot (klasszikus) több alhálózattal az Azure-ban.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 50054379a3032a368a10932e15396373a3817cff
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978914"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Virtuális hálózat (klasszikus) létrehozása több alhálózattal

> [!IMPORTANT]
> Az Azure két [különböző üzembe helyezési modellel](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendelkezik az erőforrások létrehozásához és használatához: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft a [Resource Manager](quick-create-portal.md) -alapú üzemi modell használatával javasolja a legtöbb új virtuális hálózat létrehozását.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan alapszintű Azure-beli virtuális hálózatot (klasszikus), amely külön nyilvános és magánhálózati alhálózatokat tartalmaz. Létrehozhat Azure-erőforrásokat, például a virtuális gépeket és a Cloud Servicest egy alhálózatban. A virtuális hálózatok (klasszikus) használatával létrehozott erőforrások kommunikálhatnak egymással, és a virtuális hálózathoz csatlakoztatott más hálózatokban lévő erőforrásokkal.

További információ az összes [virtuális hálózat](manage-virtual-network.md) és [alhálózat](virtual-network-manage-subnet.md) beállításairól.

> [!WARNING]
> Az Azure azonnal törli a virtuális hálózatokat (klasszikus), ha az [előfizetés le van tiltva](../cost-management-billing/manage/subscription-disabled.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). A virtuális hálózatok (klasszikus) törölve lesznek, függetlenül attól, hogy a virtuális hálózatban léteznek-e erőforrások. Ha később újra engedélyezi az előfizetést, a virtuális hálózatban található erőforrásokat újra létre kell hozni.

A virtuális hálózat (klasszikus) a [Azure Portal](#portal), az [Azure parancssori felület (CLI) 1,0](#azure-cli)vagy a [PowerShell](#powershell)használatával hozható létre.

## <a name="portal"></a>Portál

1. Egy böngészőben nyissa meg a [Azure Portal](https://portal.azure.com). Jelentkezzen be az [Azure-fiókjával](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kattintson az **erőforrás létrehozása** elemre a portálon.
3. A megjelenő **új** ablaktábla tetején található **Keresés a piactéren** mezőbe írja be a *virtuális hálózat* kifejezést. Kattintson a **virtuális hálózat** elemre, amikor megjelenik a keresési eredmények között.
4. A megjelenő **Virtual Network** ablaktáblán válassza a **klasszikus** **lehetőséget,** majd kattintson a **Létrehozás**gombra. 
5. A **virtuális hálózat létrehozása (klasszikus)** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás**gombra:

    |Beállítás|Value (Díj)|
    |---|---|
    |Név|myVNet|
    |Címtér|10.0.0.0/16|
    |Alhálózat neve|Nyilvános|
    |Alhálózati címtartomány|10.0.0.0/24|
    |Erőforráscsoport|Hagyja meg az **új kijelölt létrehozása** elemet, majd adja meg a **myResourceGroup**.|
    |Előfizetés és hely|Válassza ki az előfizetést és a helyet.

    Ha még nem ismeri az Azure-t, tekintse meg az [erőforráscsoportok](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)és [helyszínek](https://azure.microsoft.com/regions) (más néven *régiók*) című témakört.
4. A portálon csak egy alhálózat hozható létre virtuális hálózat létrehozásakor. Ebben az oktatóanyagban egy második alhálózatot hoz létre a virtuális hálózat létrehozása után. Lehet, hogy később internetről elérhető erőforrásokat hoz létre a **nyilvános** alhálózatban. Olyan erőforrásokat is létrehozhat, amelyek nem érhetők el az internetről a **privát** alhálózaton. A második alhálózat létrehozásához adja meg a **myVnet** kifejezést az oldal tetején található **Keresés erőforrások** mezőben. Kattintson a **myVnet** elemre, amikor megjelenik a keresési eredmények között.
5. Kattintson a megjelenő **virtuális hálózat létrehozása (klasszikus)** panel **alhálózatok** elemére (a **Beállítások** szakaszban).
6. Kattintson a **+ Hozzáadás** elemre a megjelenő **MyVnet-alhálózatok** ablaktáblán.
7. Az **alhálózat hozzáadása** ablaktáblán adja meg a **saját** **nevet a név** mezőben. Adja meg a **10.0.1.0/24** értéket a **címtartományból**.  Kattintson az **OK** gombra.
8. A **myVnet-alhálózatok** ablaktáblán láthatja a létrehozott **nyilvános** és **magánhálózati** alhálózatokat.
9. Nem **kötelező**: az oktatóanyag befejezése után érdemes lehet törölni a létrehozott erőforrásokat, hogy ne legyenek használatban a használati díjak:
    - A **myVnet** ablaktáblán kattintson az **Áttekintés** elemre.
    - Kattintson a **Törlés** ikonra a **myVnet** ablaktáblán.
    - A törlés megerősítéséhez kattintson az **Igen** gombra a **virtuális hálózat törlése** mezőben.

## <a name="azure-cli"></a>Azure parancssori felület (CLI)

1. [Telepítheti és konfigurálhatja az Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-t, vagy használhatja a Azure Cloud Shellon belül a CLI-t is. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Ha segítséget szeretne kérni a CLI-parancsokhoz, írja be a következőt: `azure <command> --help`. 
2. Egy CLI-munkamenetben jelentkezzen be az Azure-ba az alábbi paranccsal. Ha az alábbi mezőben a **kipróbálás** gombra kattint, megnyílik egy Cloud shell. A következő parancs beírása nélkül is bejelentkezhet az Azure-előfizetésbe:

    ```azurecli-interactive
    azure login
    ```

3. A parancssori felület Service Management módban való biztosításához írja be a következő parancsot:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Hozzon létre egy privát alhálózattal rendelkező virtuális hálózatot:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Hozzon létre egy nyilvános alhálózatot a virtuális hálózaton belül:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Tekintse át a virtuális hálózatot és az alhálózatokat:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. Nem **kötelező**: érdemes törölni az oktatóanyag befejezése után létrehozott erőforrásokat, hogy ne kelljen használati díjakat fizetnie:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Bár a parancssori felület használatával nem adhat meg egy erőforráscsoportot a virtuális hálózat (klasszikus) létrehozásához, az Azure egy *alapértelmezett-hálózatkezelés*nevű erőforráscsoporthoz hozza létre a virtuális hálózatot.

## <a name="powershell"></a>PowerShell

1. Telepítse a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) -modul legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítsa el a PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be az Azure-ba a `Add-AzureAccount` paranccsal.
4. Módosítsa a következő elérési utat és fájlnevet, ha szükséges, majd exportálja a meglévő hálózati konfigurációs fájlt:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Nyilvános és magánhálózati alhálózattal rendelkező virtuális hálózat létrehozásához használja a szövegszerkesztőt a hálózati konfigurációs fájlhoz tartozó **VirtualNetworkSite** elem hozzáadásához.

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

    Tekintse át a teljes [hálózati konfigurációs fájl sémáját](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importálja a hálózati konfigurációs fájlt:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > A módosított hálózati konfigurációs fájlok importálása a meglévő virtuális hálózatok (klasszikus) módosítását eredményezheti az előfizetésében. Győződjön meg arról, hogy csak az előző virtuális hálózatot adja hozzá, és hogy nem módosítja vagy nem távolítja el az előfizetéshez tartozó meglévő virtuális hálózatokat. 

7. Tekintse át a virtuális hálózatot és az alhálózatokat:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. Nem **kötelező**: érdemes törölni az oktatóanyag befejezése után létrehozott erőforrásokat, hogy ne kelljen használati díjat fizetnie. A virtuális hálózat törléséhez hajtsa végre a 4-6-es lépést újra, ezúttal távolítsa el az 5. lépésben hozzáadott **VirtualNetworkSite** elemet.
 
> [!NOTE]
> Bár nem adhat meg erőforráscsoportot a virtuális hálózat (klasszikus) létrehozásához a PowerShell használatával, az Azure egy *alapértelmezett hálózatkezelés*nevű erőforráscsoporthoz hozza létre a virtuális hálózatot.

---

## <a name="next-steps"></a>Következő lépések

- Az összes virtuális hálózat és alhálózat beállításával kapcsolatos további tudnivalókért lásd: [virtuális hálózatok kezelése](manage-virtual-network.md) és [virtuális hálózati alhálózatok kezelése](virtual-network-manage-subnet.md). A virtuális hálózatok és az alhálózatok használatának különböző lehetőségei vannak az éles környezetben, hogy megfeleljenek a különböző követelményeknek.
- Hozzon létre egy Windows vagy [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet, majd kapcsolódjon egy meglévő virtuális hálózathoz.
- Ha ugyanazon az Azure-helyen két virtuális hálózatot szeretne összekapcsolni, hozzon létre egy [virtuális hálózatot](create-peering-different-deployment-models.md) a virtuális hálózatok között. A virtuális hálózat (Resource Manager) egy virtuális hálózathoz (klasszikus) is használható, de két virtuális hálózat (klasszikus) közötti társítást nem lehet létrehozni.
- A virtuális hálózat összekapcsolása egy helyszíni hálózattal [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) áramkör használatával.
