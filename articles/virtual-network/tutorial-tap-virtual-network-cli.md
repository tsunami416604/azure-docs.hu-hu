---
title: Virtuális hálózat TAP létrehozása, módosítása vagy törlése – Azure CLI
description: Ismerje meg, hogyan hozhat létre, módosíthat vagy törölhet virtuális hálózati TAP-ot az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 56288a65dc9e5b12a12393965b9670e394146181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234978"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Virtuális hálózati TAP használata az Azure CLI használatával

Az Azure virtuális hálózati TAP (Terminal Access Point) lehetővé teszi a virtuális gép hálózati forgalmának folyamatos streamelését egy hálózati csomaggyűjtő vagy -elemző eszköz felé. A gyűjtő vagy elemző eszközt egy [hálózati virtuális berendezés](https://azure.microsoft.com/solutions/network-appliances/) partner biztosítja. A virtuális hálózati TAP-val való együttműködésre ellenőrzött partnermegoldások listáját a [partnermegoldások](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)ban találja. 

## <a name="create-a-virtual-network-tap-resource"></a>Virtuális hálózati TAP-erőforrás létrehozása

Virtuális hálózati TAP-erőforrás létrehozása előtt olvassa el [az előfeltételeket.](virtual-network-tap-overview.md#prerequisites) Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/bash)követett parancsokat, vagy az Azure parancssori felületének (CLI) futtatásával a számítógépről. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely nem igényel az Azure CLI telepítését a számítógépre. A megfelelő [engedélyekkel](virtual-network-tap-overview.md#permissions)rendelkező fiókkal kell bejelentkeznie az Azure-ba. Ez a cikk az Azure CLI 2.0.46-os vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). A virtuális hálózati TAP jelenleg bővítményként érhető el. A bővítmény telepítéséhez futtatnia `az extension add -n virtual-network-tap`kell. Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatnia, hogy hozzon létre egy kapcsolatot az Azure-ral.

1. Az előfizetés azonosítójának lekérése egy későbbi lépésben használt változóba:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Állítsa be azt az előfizetés-azonosítót, amelyet egy virtuális hálózati TAP-erőforrás létrehozásához fog használni.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Regisztrálja újra a virtuális hálózati TAP-erőforrás létrehozásához használt előfizetés-azonosítót. Ha a TAP-erőforrás létrehozásakor regisztrációs hiba lép fel, futtassa a következő parancsot:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Ha a virtuális hálózat TAP célja a hálózati virtuális készülék hálózati interfésze gyűjtő- vagy elemzőeszköz számára -

   - A hálózati virtuális berendezés hálózati illesztőjének IP-konfigurációjának beolvasása egy későbbi lépésben használt változóba. Az azonosító az a végpont, amely összesíti a TAP-forgalmat. A következő példa a *myNetworkInterface*nevű hálózati illesztő *ipconfig1* IP-konfigurációjának azonosítóját olvassa be egy *myResourceGroup*nevű erőforráscsoportban:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Hozza létre a virtuális hálózat TAP westcentralus azure régióban az IP-konfiguráció azonosítóját, mint a cél és egy opcionális port tulajdonság. A port megadja a hálózati csatoló IP-konfigurációjának azon célportját, amelyen a TAP-forgalom érkezik:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Ha a virtuális hálózat célja a TAP egy azure belső terheléselosztó:
  
   - Az Azure belső terheléselosztó előtér-IP-konfigurációjának lekérése egy későbbi lépésben használt változóba. Az azonosító az a végpont, amely összesíti a TAP-forgalmat. A következő példa a *frontendipconfig1* előtér-IP-konfigurációjának azonosítóját olvassa be egy *myInternalLoadBalancer*nevű terheléselosztóhoz egy *myResourceGroup*nevű erőforráscsoportban:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Hozza létre a virtuális hálózati TAP-t az előtér IP-konfigurációjának azonosítója alapján célként és választható porttulajdonságként. A port megadja azt a célportot az előtér-IP-konfiguráción, ahol a TAP-forgalom érkezik:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. A virtuális hálózat létrehozásának megerősítése TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>TAP-konfiguráció hozzáadása hálózati adapterhez

1. Meglévő virtuális hálózati TAP-erőforrás azonosítójának beolvasása. A következő példa egy *myTap* nevű virtuális hálózatot kér be egy *myResourceGroup*nevű erőforráscsoportban:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Hozzon létre egy TAP-konfigurációt a figyelt virtuális gép hálózati felületén. A következő példa egy TAP-konfigurációt hoz létre a myNetworkInterface nevű hálózati *adapterhez:*

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. A TAP-konfiguráció létrehozásának megerősítése:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>A TAP-konfiguráció törlése hálózati adapteren

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Virtuális hálózati TA-k listázása előfizetésben

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Virtuális hálózati TAP törlése erőforráscsoportban

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
