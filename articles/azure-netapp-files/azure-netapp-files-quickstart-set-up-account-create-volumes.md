---
title: 'Rövid útmutató: Az Azure NetApp-fájlok és az NFS-kötet beállítása'
description: Rövid útmutató – Bemutatja, hogyan állíthatja be gyorsan az Azure NetApp-fájlokat, és hogyan hozhat létre kötetet.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 12/01/2019
ms.openlocfilehash: fc7f13fb7ffe1667aaeaa4a3cc1916c6049a98c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75551641"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Rövid útmutató: Az Azure NetApp-fájlok beállítása és NFS-kötet létrehozása 

Ez a cikk bemutatja, hogyan állíthatja be gyorsan az Azure NetApp-fájlokat, és hogyan hozhat létre kötetet. 

Ebben a rövid útmutatóban a következő elemeket állíthatja be:

- Regisztráció az Azure NetApp-fájlokra és a NetApp-erőforrás-szolgáltatóra
- NetApp-fiók
- A kapacitás medence
- NFS-kötet az Azure NetApp-fájlokhoz

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek 

> [!IMPORTANT] 
> Hozzáférést kell biztosítani az Azure NetApp Files szolgáltatáshoz.  A szolgáltatáshoz való hozzáférés kéréséhez tekintse meg az [Azure NetApp Files várólisták beküldéselapot.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)  A folytatás előtt meg kell várnia az Azure NetApp Files csapatának hivatalos visszaigazoló e-mail-címét. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Regisztráció az Azure NetApp-fájlokra és a NetApp-erőforrás-szolgáltatóra

> [!NOTE]
> A regisztrációs folyamat eltarthat egy ideig.
>

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Portál használatával történő regisztrációs lépésekhez nyisson meg egy Cloud Shell-munkamenetet a fentiek szerint, és kövesse az alábbi Azure CLI-lépéseket:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ez az útmutató cikk az Azure PowerShell-modul Az 2.6.0-s vagy újabb verzióját igényli. Az aktuális verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha szeretné, használhatja a Cloud Shell-konzolt egy PowerShell-munkamenetben.

1. Egy PowerShell parancssorban (vagy PowerShell Cloud Shell-munkamenetben) adja meg az Azure NetApp-fájlok engedélyezési listájára jogosult előfizetést:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Regisztrálja az Azure-erőforrás-szolgáltatót:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>NetApp-fiók létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Az Azure Portal keresőmezőjébe írja be az **Azure NetApp-fájlok kifejezést,** majd válassza ki az **Azure NetApp-fájlok elemet** a megjelenő listából.

      ![Azure NetApp-fájlok kiválasztása](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Új NetApp-fiók létrehozásához kattintson a **+ Hozzáadás** gombra.

     ![Új NetApp-fiók létrehozása](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Az Új NetApp-fiók ablakban adja meg a következő információkat: 
   1. Adja meg a fióknevét a **myaccount1-hez.** 
   2. Válassza ki előfizetését.
   3. Új erőforráscsoport létrehozásához válassza az **Új létrehozása** lehetőséget. Adja meg a **myRG1** értéket az erőforráscsoport nevéhez. Kattintson az **OK** gombra. 
   4. Válassza ki a fiók helyét.  

      ![Új NetApp-fiók ablak](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Erőforráscsoport ablak](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Az új NetApp-fiók létrehozásához kattints a **Létrehozás** gombra.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Definiáljon néhány változót, hogy a többi példában hivatkozhassunk rájuk:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > A támogatott régiók listáját a [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) című részében tájékszerezheti.
    > A parancssori eszközeink által támogatott régiónév megszerzéséhez használja a`Get-AzLocation | select Location`
    >

1. Hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal:

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Azure NetApp-fájlok fiók létrehozása a [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) paranccsal:
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiáljon néhány változót, hogy a többi példában hivatkozhassunk rájuk:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > A támogatott régiók listáját a [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) című részében tájékszerezheti.
    > A parancssori eszközeink által támogatott régiónév megszerzéséhez használja a`az account list-locations -query "[].{Region:name}" --out table`
    >

2. Hozzon létre egy új erőforráscsoportot az [az csoport létrehozása](/cli/azure/group#az-group-create) paranccsal:

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Hozzon létre Azure NetApp Files fiókot [az az netappfiles fiók létrehozási](/cli/azure/netappfiles/account#az-netappfiles-account-create) paranccsal:
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Az Azure NetApp-fájlok kezelése panelen válassza ki netapp-fiókját (**myaccount1**).

    ![NetApp-fiók kiválasztása](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. A NetApp-fiók Azure NetApp-fájlok kezelése panelen kattintson **a Kapacitáskészletek**elemre.

    ![Kattintson a Kapacitáskészletek elemre.](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Kattintson **a + Készletek hozzáadása gombra.** 

    ![Kattintson a Készletek hozzáadása gombra.](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Adja meg a kapacitáskészlet adatait: 
    1. Adja meg **a mypool1-et** készletnévként.
    2. Válassza a **Prémium lehetőséget** a szolgáltatási szinthez. 
    3. Adja meg a **4-et (TiB)** a készlet méretként. 

5. Kattintson az **OK** gombra.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Néhány új változó meghatározása későbbi használatra

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Új kapacitáskészlet létrehozása a [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool) segítségével

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Néhány új változó meghatározása későbbi használatra

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Új kapacitáskészlet létrehozása az [az netappfiles készlet létrehozása](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) segítségével 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>NFS-kötet létrehozása az Azure NetApp-fájlokhoz

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A NetApp-fiók Azure NetApp-fájlok kezelése panelen kattintson a **Kötetek**elemre.

    ![Kattintson a Kötetek elemre](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Kattintson a Kötetek hozzáadása gombra.](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. A Kötet létrehozása ablakban adja meg a kötet adatait: 
   1. Adja meg a **myvol1** kötet nevét. 
   2. Válassza ki a kapacitáskészletet (**mypool1**).
   3. Használja a kvóta alapértelmezett értékét. 
   4. A virtuális hálózat csoportban kattintson az **Új létrehozása** új Azure virtuális hálózat (Vnet) elemre.  Ezután töltse ki a következő adatokat:
       * Adja meg **a myvnet1** nevet a virtuális hálózat neveként.
       * Adja meg a beállítás címterületét, például 10.7.0.0/16
       * Adja meg **a myANFsubnet** alhálózati nevet.
       * Adja meg az alhálózati címtartományt, például 10.7.0.0/24. A dedikált alhálózat nem osztható meg más erőforrásokkal.
       * Válassza ki a **Microsoft.NetApp/volumes elemet** az alhálózati delegáláshoz.
       * Kattintson **az OK** gombra a virtuális hálózat létrehozásához.
   5. Az alhálózatban válassza ki az újonnan létrehozott virtuális hálózatot (**myvnet1**) delegált alhálózatként.

      ![Kötetablak létrehozása](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Virtuális hálózati ablak létrehozása](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Kattintson **a Protokoll**gombra, majd hajtsa végre a következő műveleteket: 
    * Válassza az **NFS-t** a kötet protokolltípusaként.  
    * Írja be **a myfilepath1-et** a kötet exportálási útvonalának létrehozásához használt fájlelérési útként.  
    * Válassza ki a kötet NFS verzióját (**NFSv3** vagy **NFSv4.1).**  
      Tekintse meg az NFS-verziókkal kapcsolatos [szempontokat](azure-netapp-files-create-volumes.md#considerations) és [gyakorlati tanácsokat.](azure-netapp-files-create-volumes.md#best-practice) 
      
  ![NFS protokoll megadása a rövid útmutatóhoz](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Kattintson az **Áttekintés + létrehozás** elemre.

    ![Ablak áttekintése és létrehozása](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Tekintse át a kötet adatait, majd kattintson a **Létrehozás gombra.**  
    A létrehozott kötet megjelenik a Kötetek panelen.

    ![Kötet létrehozva](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Hozzon létre egy alhálózati delegálást a "Microsoft.NetApp/volumes" számára a [New-AzDelegation](/powershell/module/az.network/new-azdelegation) paranccsal.

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) paranccsal.

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Hozza létre a virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) paranccsal.
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Hozza létre a kötetet a [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) paranccsal.
   
    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Néhány változó definiálása későbbi használatra.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Hozzon létre virtuális hálózatot alhálózat nélkül az [az hálózati virtuális hálózat create](/cli/azure/network/vnet#az-network-vnet-create) paranccsal.
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Delegált alhálózat létrehozása [az az hálózati virtuális hálózat létrehozási](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) parancsával.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Hozza létre a kötetet az [az netappfiles kötet létrehozási](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) parancsával.
   
    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha elkészült, és ha szeretné, törölheti az erőforráscsoportot. Az erőforráscsoport törlésének művelete visszafordíthatatlan.  

> [!IMPORTANT]
> Az erőforráscsoportokon belüli összes erőforrás véglegesen törlődik, és nem állítható vissza. 

1. Az Azure Portal keresőmezőjébe írja be az **Azure NetApp-fájlok kifejezést,** majd válassza ki az **Azure NetApp-fájlok elemet** a megjelenő listából.

2. Az előfizetések listájában kattintson a törölni kívánt erőforráscsoportra (myRG1). 

    ![Navigálás erőforráscsoportokra](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Az erőforráscsoport lapon kattintson az **Erőforráscsoport törlése gombra.**

    ![Erőforráscsoport törlése](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Ekkor megnyílik egy ablak, mely figyelmezteti az erőforráscsoporttal törölt erőforrásokról.

4. Írja be az erőforráscsoport nevét (myRG1) annak megerősítéséhez, hogy véglegesen törölni szeretné az erőforráscsoportot és az összes benne lévő erőforrást, majd kattintson a **Törlés gombra.**

    ![Erőforráscsoport törlése](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ha elkészült, és ha szeretné, törölheti az erőforráscsoportot. Az erőforráscsoport törlésének művelete visszafordíthatatlan.  

> [!IMPORTANT]
> Az erőforráscsoportokon belüli összes erőforrás véglegesen törlődik, és nem állítható vissza.

1. Az erőforráscsoport törlése az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal.
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha elkészült, és ha szeretné, törölheti az erőforráscsoportot. Az erőforráscsoport törlésének művelete visszafordíthatatlan.  

> [!IMPORTANT]
> Az erőforráscsoportokon belüli összes erőforrás véglegesen törlődik, és nem állítható vissza.

1. Erőforráscsoport törlése az [az csoport törlési](/cli/azure/group#az-group-delete) parancsával.
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [Kötetek kezelése az Azure NetApp-fájlokkal](azure-netapp-files-manage-volumes.md)  
