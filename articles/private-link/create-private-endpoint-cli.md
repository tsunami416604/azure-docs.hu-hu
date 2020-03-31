---
title: Rövid útmutató – Azure-beli privát végpont létrehozása az Azure CLI használatával
description: Az Azure privát végpontjának megismerése ebben a rövid útmutatóban
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: dbcb833e6f8b90cebd3d013e58168558bcd96827
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75459973"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Rövid útmutató: Hozzon létre egy privát végpontot az Azure CLI használatával
A privát végpont az Azure-beli privát kapcsolat alapvető építőköve. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek) privát módon kommunikáljanak a Private Link Resources szolgáltatással. Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre virtuális gépet egy virtuális hálózaton, egy SQL Database Server egy privát végpont ot az Azure CLI használatával. Ezután elérheti a virtuális gép, és biztonságosan hozzáférhet a privát kapcsolat erőforrás (egy privát Azure SQL Database-kiszolgáló ebben a példában). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy az Azure CLI-t helyileg telepíti és használja, ez a rövid útmutató az Azure CLI 2.0.28-as vagy újabb verzióját kell használnia. A telepített verzió megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a program `az --version` A telepítési vagy frissítési információkért tekintse meg az [Azure CLI telepítése](/cli/azure/install-azure-cli) című témakört.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Bármely erőforrás létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westcentralus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
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

## <a name="create-a-sql-database-server"></a>SQL Database Server létrehozása 
Sql Database Server létrehozása az AZ SQL Server create paranccsal. Ne feledje, hogy az SQL Server nevének egyedinek kell lennie az Azure-ban, ezért cserélje le a zárójelben lévő helyőrző értéket a saját egyedi értékére: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Ne feledje, hogy ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` az SQL Server-azonosító hasonló az SQL Server-azonosító következő lépésben való használatához. 

## <a name="create-the-private-endpoint"></a>A privát végpont létrehozása 
Hozzon létre egy privát végpontot az SQL Database-kiszolgálóhoz a virtuális hálózatban: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>A privát DNS-zóna konfigurálása 
Hozzon létre egy privát DNS-zónát az SQL Database kiszolgálótartományhoz, és hozzon létre társítási kapcsolatot a virtuális hálózattal. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Csatlakozzon a vm *myVm* az internetről az alábbiak szerint:

1. A portál keresősávjában írja be a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Csatlakozás** gomb kiválasztása után megnyílik **a Csatlakozás a virtuális géphez.**

1. Válassza **az RDP-fájl letöltése lehetőséget.** Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg a downloaded.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha tanúsítványfigyelmeztetést kap, válassza az **Igen** vagy **a Folytatás**lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server magántulajdonban a virtuális gépről

Ebben a szakaszban a virtuális gépről fog csatlakozni az SQL Database Server kiszolgálóhoz a privát végpont használatával.

 1. A *myVM*távoli asztalán nyissa meg a PowerShellt.
 2. Adja meg az  nslookup myserver.database.windows.net A következőhöz hasonló üzenetet fog kapni: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Az SQL Server Management Studio telepítése 
 4. A Csatlakozás a kiszolgálóhoz mezőbe írja be vagy jelölje ki ezt az információt: Kiszolgáló típusa: Válassza az Adatbázis-motor lehetőséget.
 Kiszolgálónév: Válassza myserver.database.windows.net felhasználónév: Adja meg a létrehozás során megadott felhasználónevet.
 Jelszó: Adja meg a létrehozás során megadott jelszót.
 Jelszó megjegyzése: Válassza az Igen lehetőséget.
 
 5. Kattintson a **Csatlakozás** gombra.
 6. Tallózás **adatbázisok** a bal oldali menüből.
 7. (Opcionálisan) Saját *adatbázisból* származó adatok létrehozása vagy lekérdezése
 8. Zárja be a távoli asztali kapcsolatot a *myVm-mel.*

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha már nincs rá szükség, az az csoport törlésével eltávolíthatja az erőforráscsoportot és az összes erőforrást: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>További lépések
- További információ az [Azure Private Linkről](private-link-overview.md)
