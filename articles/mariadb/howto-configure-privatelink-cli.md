---
title: Privát hivatkozás - Azure CLI - Azure Database for MariaDB
description: Megtudhatja, hogy miként konfigurálhatja a MariaDB Azure Database privát hivatkozását az Azure CLI-től
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371800"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Privát kapcsolat létrehozása és kezelése a MariaDB-hez a CLI használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőköve. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek) privát módon kommunikáljanak a privát kapcsolaterőforrásaival. Ebben a cikkben megtudhatja, hogyan használhatja az Azure CLI-t egy virtuális gép létrehozásához egy Azure virtuális hálózatban és egy Azure Database for MariaDB-kiszolgálóegy Azure-beli privát végponthasználatával.

> [!NOTE]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for MariaDB támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató útmutatón való átlépéshez a következőkre van szükség:

- [Egy Azure-adatbázis a MariaDB-kiszolgálóhoz.](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy az Azure CLI-t helyileg telepíti és használja, ez a rövid útmutató az Azure CLI 2.0.28-as vagy újabb verzióját kell használnia. A telepített verzió megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a program `az --version` A telepítési vagy frissítési információkért tekintse meg az [Azure CLI telepítése](/cli/azure/install-azure-cli) című témakört.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Bármely erőforrás létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westeurope* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot [az hálózati virtuális hálózat létrehozása.](/cli/azure/network/vnet) Ez a példa létrehoz egy alapértelmezett virtuális hálózat nevű *myVirtualNetwork* egy alhálózat nevű *mySubnet:*

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Alhálózati magánvégpont-házirendek letiltása 
Az Azure erőforrásokat telepít egy virtuális hálózaton belüli alhálózatba, ezért létre kell hoznia vagy frissítenie kell az alhálózatot a magánvégpont-hálózati házirendek letiltásához. A *mySubnet* nevű alhálózati konfiguráció frissítése [az az hálózati virtuális hálózat alhálózati frissítésével:](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Virtuális gép létrehozása 
Hozzon létre egy virtuális gép az vm létrehozása. Amikor a rendszer kéri, adjon meg egy jelszót a virtuális gép bejelentkezési hitelesítő adataiként használandó jelszót. Ez a példa létrehoz egy *myVm*nevű virtuális gép: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Jegyezze fel a virtuális gép nyilvános IP-címét. Ezt a címet fogja használni, hogy csatlakozzon a virtuális géphez az internetről a következő lépésben.

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása 
Hozzon létre egy Azure Database for MariaDB az az mariadb server create paranccsal. Ne feledje, hogy a MariaDB-kiszolgáló nevének egyedinek kell lennie az Azure-ban, ezért cserélje le a zárójelben lévő helyőrző értéket a saját egyedi értékére: 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Vegye figyelembe, hogy ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` a MariaDB kiszolgálóazonosítója hasonló a Következő lépésben a MariaDB-kiszolgáló azonosítójához. 

## <a name="create-the-private-endpoint"></a>A privát végpont létrehozása 
Hozzon létre egy privát végpontot a MariaDB-kiszolgálóhoz a virtuális hálózatban: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>A privát DNS-zóna konfigurálása 
Hozzon létre egy privát DNS-zónát a MariDB kiszolgálótartományhoz, és hozzon létre társítási kapcsolatot a virtuális hálózattal. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Az ügyfél DNS-beállításában lévő teljes tartományna nem oldódik fel a beállított privát IP-címre. Be kell állítania egy DNS-zónát a konfigurált teljes tartománynna számára az [itt](../dns/dns-operations-recordsets-portal.md)látható módon.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Csatlakozzon a vm *myVm* az internetről az alábbiak szerint:

1. A portál keresősávjában írja be a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Csatlakozás** gomb kiválasztása után megnyílik **a Csatlakozás a virtuális géphez.**

1. Válassza **az RDP-fájl letöltése lehetőséget.** Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg a *downloaded.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha tanúsítványfigyelmeztetést kap, válassza az **Igen** vagy **a Folytatás**lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>A MariaDB-kiszolgáló elérése a virtuális gépről

1. A *myVM*távoli asztalán nyissa meg a PowerShellt.

2. Írja be a  `nslookup mydemoserver.privatelink.mariadb.database.azure.com` (igen) kifejezést. 

    A következőhöz hasonló üzenetet fog kapni:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Tesztelje a MariaDB-kiszolgáló privát kapcsolatát bármely elérhető ügyfél használatával. Az alábbi példában a [MySQL Workbench-et](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) használtam a művelethez.

4. Az **Új kapcsolat ban**adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kapcsolat neve| Válassza ki a kívánt kapcsolatnevét.|
    | Gazdanév | *mydemoserver.privatelink.mariadb.database.azure.com* kijelölése |
    | Felhasználónév | Adja meg *username@servername* a felhasználónevet úgy, ahogy az a MariaDB-kiszolgáló létrehozása során biztosított. |
    | Jelszó | Adja meg a MariaDB-kiszolgáló létrehozása során megadott jelszót. |
    ||

5. Válassza **a Kapcsolat tesztelése** vagy **az OK lehetőséget.**

6. (Opcionálisan) Adatbázisok tallózása a bal oldali menüből és A MariaDB adatbázisból származó adatok létrehozása vagy lekérdezése

8. Zárja be a távoli asztali kapcsolatot a myVm-mel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha már nincs rá szükség, az az csoport törlésével eltávolíthatja az erőforráscsoportot és az összes erőforrást: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>További lépések
További információ [az Azure privát végpontjáról](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
