---
title: Az Azure Firewall telepítése és konfigurálása az Azure CLI használatával
description: Ebben a cikkben megtudhatja, hogyan telepítheti és konfigurálhatja az Azure Firewall az Azure CLI használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: e97783d1a32916cad151f1d0858a8190d0005fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73831969"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Az Azure Firewall telepítése és konfigurálása az Azure CLI használatával

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Előfordulhat például, hogy korlátozni szeretné a webhelyekhez való hozzáférést. Vagy korlátozhatja a kimenő IP-címeket és portokat, amelyek elérhetők.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg. A teljes tartományfájl [tartalmazhat SQL-példányokat](sql-fqdn-filtering.md)is.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben a cikkben hozzon létre egy egyszerűsített egyetlen virtuális hálózat három alhálózattal a könnyű telepítés érdekében. Éles környezetben éles környezetben, a [hub és a küllőmodell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) ajánlott. A tűzfal saját virtuális hálózatában található. A számítási feladatok kiszolgálók egyenrangú virtuális hálózatok ugyanabban a régióban egy vagy több alhálózattal.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Alkalmazásszabály konfigurálása a www.google.com való hozzáférés engedélyezéséhez
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * A tűzfal tesztelése

Ha szeretné, ezt az eljárást az [Azure Portalon](tutorial-firewall-deploy-portal.md) vagy az [Azure PowerShellen](deploy-ps.md)keresztül végezheti el.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-cli"></a>Azure CLI

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, futtassa az Azure CLI 2.0.4-es vagy újabb verzióját. A verzió megkereséséhez futtassa az **az --version .** A telepítésről és a frissítésről az [Azure CLI telepítése című]( /cli/azure/install-azure-cli)témakörben talál további információt.

Az Azure Firewall bővítmény telepítése:

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport tartalmazza a központi telepítéshez szükséges összes erőforrást.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Ez a virtuális hálózat három alhálózattal rendelkezik.

> [!NOTE]
> Az AzureFirewallSubnet alhálózat mérete /26. Az alhálózat méretéről az [Azure Firewall gyIK](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)című témakörben talál további információt.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a helyettesítő és a számítási feladatokat futtató virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.
Amikor a rendszer kéri, írja be a virtuális gép jelszavát.

Hozza létre a Srv-Jump virtuális gépet.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Hozzon létre egy hálózati adaptert az Srv-Work számára adott DNS-kiszolgáló IP-címekkel, és ne hozzon létre nyilvános IP-címet.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Most hozza létre a számítási feladatok virtuális gép.
Amikor a rendszer kéri, írja be a virtuális gép jelszavát.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Most telepítse a tűzfalat a virtuális hálózatba.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Tábla létrehozása a BGP-útvonal propagálásának letiltásával

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Hozza létre az útvonalat.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Az útvonaltábla társítása az alhálózathoz

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Az alkalmazásszabály lehetővé teszi a kimenő hozzáférést a www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

A hálózati szabály lehetővé teszi a kimenő hozzáférést két IP-címhez az 53-as porton (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje a tűzfalat, hogy ellenőrizze, hogy a várt módon működik-e.

1. Vegye figyelembe az **Srv-Work** virtuális gép privát IP-címét:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Csatlakoztasson egy távoli asztalt **a Srv-Jump** virtuális géphez, és jelentkezzen be. Innen nyisson meg egy távoli asztali kapcsolatot a **Srv-Work** privát IP-címhez, és jelentkezzen be.

3. Az **SRV-Work-en**nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsokat:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Mindkét parancsnak válaszokat kell adnia, ami azt mutatja, hogy a DNS-lekérdezések átjutnak a tűzfalon.

1. Futtassa az alábbi parancsot:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   A `www.google.com` kérelmeknek sikeresnek `www.microsoft.com` kell lenniük, és a kérelmeknek sikertelennek kell lenniük. Ez azt mutatja, hogy a tűzfalszabályok a várt módon működnek.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.
* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Megtarthatja a tűzfal erőforrásait a következő oktatóanyaghoz, vagy ha már nincs rá szükség, törölje a **Test-FW-RG** erőforráscsoportot az összes tűzfallal kapcsolatos erőforrás törléséhez:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
