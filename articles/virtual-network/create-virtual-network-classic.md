---
title: Hozzon létre egy Azure virtuális hálózat (klasszikus) több alhálózattal rendelkező |} A Microsoft Docs
description: Útmutató az Azure-ban több alhálózattal rendelkező virtuális hálózat (klasszikus) létrehozása.
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
ms.date: 06/15/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: bede78782154e014d4ffad949f9ebd1a44eb2c4c
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35925349"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Több alhálózattal rendelkező virtuális hálózat (klasszikus) létrehozása

> [!IMPORTANT]
> Az Azure rendelkezik két [különböző üzemi modellek](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) számára az erőforrások létrehozásához és használatához: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy keresztül a legtöbb új virtuális hálózat létrehozása a [Resource Manager](quick-create-portal.md) üzemi modellt.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy alapszintű Azure virtuális hálózat (klasszikus), amely rendelkezik külön nyilvános és privát alhálózatra. Azure-erőforrások, például a virtuális gépek és felhőszolgáltatások egy alhálózat is létrehozhat. Virtuális hálózatok (klasszikus) létrehozott erőforrásokat kommunikálhatnak egymással, és az erőforrások a virtuális hálózathoz csatlakozó más hálózatokhoz.

További információ az összes [virtuális hálózat](manage-virtual-network.md) és [alhálózati](virtual-network-manage-subnet.md) beállításait.

> [!WARNING]
> Virtuális hálózatok (klasszikus) Azure azonnal törli amikor egy [előfizetés le van tiltva](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Virtuális hálózatok (klasszikus) törlődnek, függetlenül attól, hogy erőforrások szerepel-e a virtuális hálózat. Ha később újra engedélyezi az előfizetés, erőforrás, amely létezett az a virtuális hálózat létre kell hozni.

Létrehozhat egy virtuális hálózat (klasszikus) használatával a [az Azure portal](#portal), a [az Azure parancssori felület (CLI), 1.0-s](#azure-cli), vagy [PowerShell](#powershell).

## <a name="portal"></a>Portál

1. Egy webböngészőben nyissa meg a [az Azure portal](https://portal.azure.com). Jelentkezzen be a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kattintson a **erőforrás létrehozása** a portálon.
3. Adja meg *virtuális hálózati* a a **keresés a piactéren** tetején található mezőbe a **új** megjelenő panelen. Kattintson a **virtuális hálózati** amikor megjelenik a keresési eredmények között.
4. Válassza ki **klasszikus** a a **telepítési modell kiválasztása** párbeszédpanel a **virtuális hálózat** megjelenő panelen, majd kattintson **létrehozás**. 
5. Adja meg a következő értékeket a **virtuális hálózat létrehozása (klasszikus)** , majd rákattinthat **létrehozás**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVNet|
    |Címtér|10.0.0.0/16|
    |Alhálózat neve|Nyilvános|
    |Alhálózati címtartomány|10.0.0.0/24|
    |Erőforráscsoport|Hagyja **új létrehozása** kiválasztva, és adja meg **myResourceGroup**.|
    |Az előfizetést és helyet|Válassza ki az előfizetést és helyet.

    Ha most ismerkedik az Azure-ba, tudjon meg többet [erőforráscsoportok](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), és [helyek](https://azure.microsoft.com/regions) (más néven *régiók*).
4. A portál csak egy alhálózat is létrehozhat, egy virtuális hálózat létrehozásakor. Ebben az oktatóanyagban létrehozhat egy második alhálózatot a virtuális hálózat létrehozása után. Később létrehozhat az internetről elérhető erőforrásokhoz a **nyilvános** alhálózat. Akkor is létre erőforrásokat, amelyek nem érhetők el az internetről a **privát** alhálózat. A második alhálózat létrehozásához írja be **myVnet** a a **erőforrások keresése** az oldal tetején. Kattintson a **myVnet** amikor megjelenik a keresési eredmények között.
5. Kattintson a **alhálózatok** (a a **beállítások** szakaszt) a a **virtuális hálózat létrehozása (klasszikus)** megjelenő panelen.
6. Kattintson a **+ Hozzáadás** a a **myVnet – alhálózatok** megjelenő panelen.
7. Adja meg **privát** a **neve** a a **alhálózat hozzáadása** ablaktáblán. Adja meg **10.0.1.0/24** a **címtartomány**.  Kattintson az **OK** gombra.
8. Az a **myVnet – alhálózatok** panelen láthatja a **nyilvános** és **privát** létrehozott alhálózat.
9. **Nem kötelező**: Ez az oktatóanyag befejezésekor előfordulhat, hogy törölni kívánja a létrehozott erőforrásokat, így nem használati díjak merülhetnek fel:
    - Kattintson a **áttekintése** a a **myVnet** ablaktáblán.
    - Kattintson a **törlése** ikonra a **myVnet** ablaktáblán.
    - Kattintson a törlés megerősítéséhez **Igen** a a **virtuális hálózat törlése** mezőbe.

## <a name="azure-cli"></a>Azure CLI

1. Választhatja [telepítése és konfigurálása az Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy a parancssori felület az Azure Cloud Shell belül használja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. CLI-parancsok súgójának, írja be a `azure <command> --help`. 
2. A CLI-munkamenetben jelentkezzen be az Azure-bA a következő paranccsal. Ha rákattint **kipróbálás** az alábbi mezőbe, a Cloud Shell megnyílik. Bejelentkezhet az Azure-előfizetéshez, anélkül, hogy az alábbi parancs beírásával:

    ```azurecli-interactive
    azure login
    ```

3. Annak érdekében, hogy a parancssori felület szolgáltatásfelügyelet módban van, adja meg a következő parancsot:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Hozzon létre egy virtuális hálózaton egy privát alhálózatra:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Hozzon létre egy nyilvános alhálózatot a virtuális hálózaton belül:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Tekintse át a virtuális hálózat és alhálózatok:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Nem kötelező**: érdemes törölni a létrehozott erőforrásokat, ez az oktatóanyag befejezésekor, hogy ne használati díjak merülhetnek fel:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Bár nem adhat meg egy erőforráscsoportot, egy virtuális hálózat (klasszikus) létrehozása a parancssori felület használatával, az Azure nevű erőforráscsoportban létrehozza a virtuális hálózatot *alapértelmezett hálózati*.

## <a name="powershell"></a>PowerShell

1. Telepítse a PowerShell legújabb verzióját [Azure](https://www.powershellgallery.com/packages/Azure) modul. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítsa el egy PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be az Azure-ba a `Add-AzureAccount` paranccsal.
4. A következő elérési út és fájlnév, szükség esetén módosítsa, majd exportálhatja a meglévő hálózati konfigurációs fájlt:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Virtuális hálózat létrehozása a nyilvános és privát alhálózattal, bármely szövegszerkesztő segítségével adja hozzá a **VirtualNetworkSite** elem az a hálózati konfigurációs fájl.

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

    Tekintse át a teljes [hálózat konfigurációs sémáját](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importálja a hálózati konfigurációs fájlt:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Megváltozott hálózati konfigurációs fájl importálása okozhat a módosításokat a meglévő virtuális hálózatok (klasszikus) az előfizetésében. Győződjön meg arról, csak adja hozzá a korábbi virtuális hálózat és az, hogy ne módosítsa vagy bármely meglévő virtuális hálózatok eltávolítása az előfizetésből. 

7. Tekintse át a virtuális hálózat és alhálózatok:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Nem kötelező**: érdemes törölni a létrehozott erőforrásokat, ez az oktatóanyag befejezésekor, hogy ne használati díjak merülhetnek fel. A virtuális hálózat törlése, teljes körű lépések 4 – 6 újra, ez idő eltávolítása a **VirtualNetworkSite** az 5. lépésben hozzáadott elem.
 
> [!NOTE]
> Bár nem adhat meg egy erőforráscsoportot, egy virtuális hálózat (klasszikus) létrehozása a PowerShell-lel, az Azure nevű erőforráscsoportban létrehozza a virtuális hálózatot *alapértelmezett hálózati*.

---

## <a name="next-steps"></a>További lépések

- Minden virtuális hálózati és alhálózati beállítások kapcsolatos további információkért lásd: [virtuális hálózatok kezelése](manage-virtual-network.md) és [kezelheti a virtuális hálózat alhálózataiban](virtual-network-manage-subnet.md). A virtuális hálózatok és alhálózatok éles környezetben eltérő követelmények kielégítése érdekében számos lehetősége van.
- Hozzon létre egy [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy egy [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet, majd csatlakozzon egy meglévő virtuális hálózatot.
- Csatlakozzon a két virtuális hálózat azonos Azure-beli helyen, hozzon létre egy [virtuális hálózatok közötti társviszony](create-peering-different-deployment-models.md) a virtuális hálózatok között. Virtuális hálózat (Resource Manager) (klasszikus) virtuális hálózati társviszonyt, de nem hozhat létre (klasszikus) virtuális hálózatok közötti társviszonyt.
- Csatlakozhat a virtuális hálózat egy helyszíni hálózat egy [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kapcsolatcsoport.
