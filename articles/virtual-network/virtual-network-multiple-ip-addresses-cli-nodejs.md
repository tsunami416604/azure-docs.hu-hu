---
title: "Virtuális gép több IP-címek az Azure CLI 1.0 |} Microsoft Docs"
description: "Megtudhatja, hogyan több IP-címek hozzárendelése a virtuális gépek az Azure CLI 1.0 |} Erőforrás-kezelő."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Több IP-címek hozzárendelése az Azure CLI 1.0 használata virtuális gépek

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk ismerteti (VM) virtuális gép létrehozása az Azure Resource Manager deployment használatával az Azure CLI 1.0 használatával. Több IP-cím nem lehet hozzárendelni a klasszikus üzembe helyezési modell használatával létrejött erőforrásokat. Az Azure üzembe helyezési modellel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellel megértéséhez](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címekkel rendelkező virtuális gép létrehozása

Hajthatja végre ezt a feladatot az Azure CLI 1.0 (Ez a cikk) vagy a [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md). A következő lépések bemutatják, hogyan több IP-címmel, például virtuális gép létrehozásához a forgatókönyvben leírtak szerint. Módosítsa a változó nevét és IP cím típusát a végrehajtásához szükség szerint.

1. Telepítheti és konfigurálhatja az Azure CLI 1.0 lépéseit a [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk, és jelentkezzen be a Azure fiók a `azure-login` parancsot.

2. Hozzon létre egy erőforráscsoportot:
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. Virtuális hálózat létrehozása:

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. A virtuális hálózati alhálózat létrehozása:

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. Hozzon létre egy tárfiókot a virtuális gép számára. A következő parancs futtatása előtt cserélje le a *mystorageaccount* egyedi névvel. A nevének egyedinek kell lennie a Azure között:

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. A hálózati adapter IP-konfigurációk létrehozása és az IP-konfiguráció hozzárendelése a hálózati adaptert. Hozzáadása, eltávolítása vagy módosítsa a beállításokat, szükség szerint. A következő konfigurációk a forgatókönyv leírása olvasható:

    **IPConfig-1**

    Adja meg a további parancsoknál, amelyek létrehozásához:

    - A nyilvános IP-cím erőforrás egy statikus nyilvános IP-cím
    - Egy hálózati Adaptert, azt a nyilvános IP-cím és a hozzá statikus magánhálózati IP-cím hozzárendelése.
    
    Cserélje le *mypublicdns* , amelynek az Azure-hely egyedi neve.

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > Nyilvános IP-címek névleges díjat kell. IP-cím árazással kapcsolatos további tudnivalókért olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. Nyilvános IP-címek egy előfizetésben használható száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

    **IPConfig-2**

     Adja meg a következő parancsok futtatásával hozzon létre egy új nyilvános IP-cím erőforrás és egy új IP-konfiguráció egy statikus nyilvános IP-címet és egy statikus magánhálózati IP-cím:
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    Adja meg a következő parancsok futtatásával hozzon létre egy IP-konfiguráció hozzá statikus magánhálózati IP-cím és a nyilvános IP-cím:

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >Bár ez a cikk összes IP-konfigurációk rendel hozzá egyetlen hálózati adapter, is rendelhet több IP-konfigurációk a hálózati adapterek virtuális gépen. Több hálózati adapterrel rendelkező virtuális gép létrehozása, olvassa el a hozza létre a cikk több hálózati adapterrel rendelkező virtuális gépeknél.

7. Linux rendszerű virtuális gép létrehozása 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    Módosítsa a Virtuálisgép-méretet szabványos DS2 v2, például egyszerűen adja hozzá a következő tulajdonság `--vm-size Standard_DS3_v2` számára a `azure vm create` parancsot a 6.

8. Adja meg a hálózati adapter és a társított IP-konfiguráció megtekintéséhez a következő parancsot:

    ```azurecli
    azure network nic show --resource-group $RgName --name myNic1
    ```
9. A privát IP-címek hozzáadása a virtuális gép operációs rendszer, az operációs rendszernek a lépések végrehajtásával a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát.

## <a name="add"></a>IP-címek hozzáadása a virtuális gépek

További privát és nyilvános IP-címeket adhat hozzá egy meglévő hálózati adapterre a következő lépések végrehajtásával. A példák épül a [forgatókönyv](#Scenario) a cikkben.

1. Nyissa meg az Azure parancssori felület, és hajtsa végre a fennmaradó lépéseit ebben a szakaszban egy CLI-munkameneten belül. Ha még nem rendelkezik Azure CLI telepítése és konfigurálása, hajtsa végre a a [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a következő cikket, és jelentkezzen be az Azure-fiókjával.

2. Hajtsa végre a következő részeiből, a követelmények alapján:

    - **Adja hozzá a magánhálózati IP-cím**
    
        A magánhálózati IP-cím hozzáadása a hálózati adapter, létre kell hoznia IP-konfigurációt az alábbi parancs segítségével. A statikus cím az alhálózat nem használt címnek kell lennie.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        Tetszőleges számú konfigurációk összes kívánt beállítást, egyedi konfigurációs nevek és privát IP-címek használata (a statikus IP-címekkel rendelkező konfigurációk) létrehozása.

    - **A nyilvános IP-cím hozzáadása**
    
        A nyilvános IP-cím jelenik meg egy új IP-konfigurációt vagy egy meglévő IP-konfiguráció társításával. Hajtsa végre a következő szakaszok, egyikében összes kívánt beállítást.

        > [!NOTE]
        > Nyilvános IP-címek névleges díjat kell. IP-cím árazással kapcsolatos további tudnivalókért olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. Nyilvános IP-címek egy előfizetésben használható száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.
        >

        **Társítsa az erőforrást egy új IP-konfiguráció**
    
        Egy nyilvános IP-címet ad hozzá egy új IP-konfiguráció, amikor is hozzá kell magánhálózati IP-cím, mert az összes IP-konfiguráció magánhálózati IP-címnek kell rendelkeznie. Egy meglévő nyilvános IP-cím erőforrás hozzáadása, vagy hozzon létre egy újat. Hozzon létre egy újat, írja be a következő parancsot:

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

        Új IP-konfiguráció létrehozása hozzá statikus magánhálózati IP-cím és a társított *myPublicIP3* nyilvános IP-cím erőforrás címet, adja meg a következő parancsot:

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **Társítsa az erőforrást egy meglévő IP-konfiguráció**

        A nyilvános IP-cím erőforrás csak lehet társítva, amely még nincs társított IP-konfigurációt. Segítségével meghatározhatja, hogy rendelkezik-e IP-konfigurációt tartozó nyilvános IP-cím a következő parancs beírásával:

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        Keresse meg az utánuk az IPConfig-3 visszaadott kimenet hasonló:

        ```         
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        Mivel a **nyilvános IP-cím** oszlopában *IpConfig-3* van üres, nem nyilvános IP-cím erőforrás jelenleg társítva. Egy meglévő nyilvános IP-cím erőforrás hozzáadása IpConfig-3, vagy hozzon létre egyet a következő parancsot írja be:

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        Adja meg a következő parancsot a meglévő IP-konfiguráció a nyilvános IP-cím erőforrás hozzárendelni *IPConfig-3*:
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. Tekintse meg a magánhálózati IP-címek és a nyilvános IP-cím erőforrás hozzárendelt hálózati adapter a következő parancs beírásával:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      A visszaadott kimenete az alábbihoz hasonló:
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. Adja hozzá a magánhálózati IP-címek utasításait követve hozzáadni a hálózati Adaptert a virtuális gép operációs rendszerre a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-címeket az operációs rendszer.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
