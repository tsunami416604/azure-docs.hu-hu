---
title: Rövid útmutató – Azure Private-végpont létrehozása az Azure CLI használatával
description: További információ az Azure Private-végpontról ebben a rövid útmutatóban
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8f2e21bddf0701ee6ce45af8012c853064e23168
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021755"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Rövid útmutató: privát végpont létrehozása az Azure CLI-vel

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára a magánjellegű kapcsolati erőforrásokkal való kommunikációt. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális gépeket virtuális hálózatokon, SQL Database-kiszolgálóként egy privát végponttal az Azure CLI használatával. Ezután elérheti a virtuális gépet, és biztonságosan elérheti a privát kapcsolati erőforrást (ebben a példában SQL Database található privát kiszolgáló).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verzióját kell használnia. A telepített verziójának megkereséséhez futtassa a parancsot `az --version` . További információ: az [Azure CLI telepítése](/cli/azure/install-azure-cli) a telepítéshez vagy a frissítéshez.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforrások létrehozása előtt létre kell hoznia egy erőforráscsoportot a Virtual Network üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westcentralus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy Virtual Network az [az Network vnet Create](/cli/azure/network/vnet)paranccsal. Ez a példa egy *myVirtualNetwork* nevű alapértelmezett Virtual Network hoz létre egy *mySubnet*nevű alhálózattal:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Alhálózat magánhálózati végponti házirendjeinek letiltása

Az Azure üzembe helyezi az erőforrásokat egy virtuális hálózaton belüli alhálózaton, ezért létre kell hoznia vagy frissítenie kell az alhálózatot a magánhálózati végpontok hálózati házirendjeinek letiltásához. Frissítsen egy *mySubnet* nevű alhálózati konfigurációt az [az Network vnet subnet Update paranccsal](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az az VM Create paranccsal. Ha a rendszer kéri, adja meg a virtuális gép bejelentkezési hitelesítő adataiként használandó jelszót. Ez a példa egy *myVm*nevű virtuális gépet hoz létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

Jegyezze fel a virtuális gép nyilvános IP-címét. Ezt a lakcímet fogja használni a virtuális géphez az internetről a következő lépésben való kapcsolódáshoz.

## <a name="create-a-server-in-sql-database"></a>Kiszolgáló létrehozása SQL Database

Hozzon létre egy kiszolgálót SQL Database az az SQL Server Create paranccsal. Ne feledje, hogy a kiszolgáló nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét zárójelek között a saját egyedi értékkel kell helyettesítenie:

```azurecli-interactive
# Create a server in the resource group
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

A kiszolgáló azonosítója a  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` következő lépésben a kiszolgáló azonosítóját fogja használni.

## <a name="create-the-private-endpoint"></a>A magánhálózati végpont létrehozása

Hozzon létre egy privát végpontot a logikai SQL Server számára a Virtual Networkban:

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>A saját DNS zóna konfigurálása

Hozzon létre egy saját DNS zónát SQL Database tartományhoz, és hozzon létre egy társítási hivatkozást a Virtual Network.

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

Kapcsolódjon a virtuális gép *myVm* az internetről a következőképpen:

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött. rdp fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="access-sql-database-privately-from-the-vm"></a>Hozzáférési SQL Database a virtuális gépről

Ebben a szakaszban a virtuális gépről a privát végpont használatával fog csatlakozni a SQL Databasehoz.

1. A *myVM*távoli asztal nyissa meg a PowerShellt.
2. Az nslookup myserver.database.windows.net megadása

   Ehhez hasonló üzenet jelenik meg:

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. SQL Server Management Studio telepítése
4. A Kapcsolódás a kiszolgálóhoz lapon adja meg vagy válassza ki az alábbi adatokat:

   - Kiszolgáló típusa: válassza az adatbázismotor lehetőséget.
   - Kiszolgáló neve: myserver.database.windows.net kiválasztása
   - Felhasználónév: adjon meg egy, a létrehozás során megadott felhasználónevet.
   - Password (jelszó): adja meg a létrehozás során megadott jelszót.
   - Jelszó megjegyzése: válassza az Igen lehetőséget.

5. Kattintson a **Csatlakozás** gombra.
6. A bal oldali menüben lévő **adatbázisok** tallózása.
7. Opcionálisan Információk létrehozása vagy lekérdezése a *mydatabase*
8. A távoli asztali kapcsolat bezárásával *myVm*.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, az az Group delete paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

További információ az [Azure Private linkről](private-link-overview.md)
