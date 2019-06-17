---
title: Telepítse és konfigurálja az Azure CLI használatával Azure-tűzfal
description: Ebből a cikkből megismerheti, hogyan telepítheti és konfigurálhatja az Azure-tűzfal az Azure CLI használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 06/11/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: b40ac789fbc331e779e85462724e5c8a8e9bce47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083352"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Telepítse és konfigurálja az Azure CLI használatával Azure-tűzfal

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Például előfordulhat, hogy szeretné korlátozni a webhelyekhez való hozzáférést. Vagy előfordulhat, hogy szeretné korlátozni a kimenő IP-címek és portok elérhető.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben a cikkben létrehoz egy egyszerűsített egyetlen virtuális hálózaton az egyszerű telepítés három alhálózatot. Éles környezetekben üzemelő példányok egy [küllős modell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) ajánlott. A tűzfal az a saját virtuális hálózatában. A számítási feladatok kiszolgálók társviszonyban lévő virtuális hálózatok egy vagy több alhálózattal rendelkező ugyanabban a régióban találhatók.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Alkalmazás www.google.com való hozzáférés engedélyezése a szabály konfigurálása
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * A tűzfal tesztelése

Ha szeretné, ez az eljárás használatával elvégezhető a [az Azure portal](tutorial-firewall-deploy-portal.md) vagy [Azure PowerShell-lel](deploy-ps.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-cli"></a>Azure CLI

Ha helyi telepítése és használata a parancssori Felületet, futtassa az Azure CLI 2.0.4-es vagy újabb. A verzió megkereséséhez futtassa **az--verzió**. Telepítésekor vagy verziófrissítésekor kapcsolatos információkért lásd: [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport tartalmazza a központi telepítés összes erőforrást.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Ez a virtuális hálózat rendelkezik három alhálózatot.

> [!NOTE]
> A AzureFirewallSubnet alhálózat minimális mérete/26-os.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/24
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

Az Srv-Jump virtuális gép létrehozása.

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



Hozzon létre egy NIC-kiszolgáló IP-címek meghatározott DNS Srv-dolgozhat, és a nyilvános IP-cím tesztelése.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Most hozzon létre a munkaterhelési virtuális gépet.
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

Már üzembe helyezheti a tűzfal a virtuális hálózatban.

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

Hozzon létre egy tábla BGP-útvonalpropagálás le van tiltva

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Az útvonal létrehozása.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Az útválasztási táblázatot az alhálózathoz való társítása

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Az alkalmazás a szabály lehetővé teszi, hogy a kimenő hozzáférést www.google.com.

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

A hálózati szabály lehetővé teszi, hogy a két IP-cím (DNS) 53-as porton a kimenő hozzáférést.

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

Most tesztelje le, a tűzfal, ellenőrizze, hogy az elvárt módon működik.

1. Vegye figyelembe a magánhálózati IP-címét a **Srv-munkahelyi** virtuális gépet:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Csatlakozzon a távoli asztali **Srv-Jump** virtuális gépet, és jelentkezzen be. Itt, nyissa meg a távoli asztali kapcsolatot a **Srv-munkahelyi** magánhálózati IP-címet, és jelentkezzen be.

3. A **SRV-munkahelyi**, nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsokat:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Mindkét parancsot kell visszaadnia a válaszokat, megjelenítése, hogy a DNS-lekérdezések első vannak-e a tűzfalon keresztül.

1. Futtassa az alábbi parancsot:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   A www.google.com kérelmek sikeres legyen, és a www.microsoft.com kérelmek sikertelenek. Ez azt mutatja be, hogy a tűzfalszabályok vannak a várt módon működik.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.
* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfal erőforrások megtarthatja a következő oktatóanyagra, vagy ha már nincs rá szükség, törölje a **Test-Keretrendszer-RG** tűzfal kapcsolódó összes erőforrást törölheti az erőforráscsoportot:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
