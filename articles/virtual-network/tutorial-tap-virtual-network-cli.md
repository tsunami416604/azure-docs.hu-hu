---
title: Létrehozása, módosítása vagy törlése a virtuális hálózati TAP - Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozása, módosítása vagy törlése a virtuális hálózati TAP az Azure CLI használatával.
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
ms.date: 09/17/2018
ms.author: kaanan
ms.openlocfilehash: 0662b66d44533a888d491810ba45efac9a397aa1
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700842"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Virtuális hálózati TAP használatával az Azure parancssori felület használata

Az Azure virtuális hálózati TAP (Terminálszolgáltatások hozzáférési pont) lehetővé teszi folyamatosan stream a virtuális gép hálózati forgalmat egy hálózati csomag adatgyűjtőnek vagy az analytics eszközt. A gyűjtő vagy analytics eszközt által biztosított egy [hálózati virtuális berendezésen](https://azure.microsoft.com/solutions/network-appliances/) partner. Dolgozunk a virtuális hálózati TAP érvényesített partneri megoldások listáját lásd: [partneri megoldások](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Hozzon létre egy virtuális hálózati TAP-erőforrás

Olvasási [Előfeltételek](virtual-network-tap-overview.md#prerequisites) egy virtuális hálózati TAP-erőforrás létrehozása előtt. A következő parancsokat futtathat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy az Azure parancssori felület (CLI) futtatja a számítógépről. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület, nem igényel, az Azure CLI telepítéséhez a számítógépen. Jelentkezzen be az Azure-bA egy olyan fiókkal, amely rendelkezik a megfelelő [engedélyek](virtual-network-tap-overview.md#permissions). Ehhez a cikkhez az Azure CLI 2.0.46 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

1. Kérje le egy változóba egy későbbi lépésben használt előfizetés azonosítója:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Állítsa be a virtuális hálózati TAP-erőforrás létrehozásához használt előfizetés-azonosítója.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Regisztrálja újra a virtuális hálózati TAP-erőforrás létrehozásához használni kívánt előfizetés-azonosítója. Ha egy regisztrációs hiba KOPPINTSON erőforrás létrehozásakor, futtassa a következő parancsot:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Ha a cél a virtuális hálózati TAP esetében a hálózati adaptert a gyűjtő vagy elemzőeszköz - hálózati virtuális berendezésen

   - IP-konfigurációja, a hálózati virtuális berendezés hálózati adaptert egy változóba egy későbbi lépésben használt lekéréséhez. Az azonosító a végpontot, amely szerint a KOPPINTSON forgalom összesítő. Az alábbi példa lekéri az azonosítója a *ipconfig1* nevű hálózati adapter IP-konfigurációt *myNetworkInterface*, egy erőforráscsoport nevű *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Hozzon létre a virtuális hálózati TAP használatával az IP-konfiguráció Azonosítóját a cél és a egy opcionális port tulajdonság westcentralus azure régióban. A port a hálózati adapter IP-konfiguráció a KOPPINTSON forgalmat fogadja, adja meg a célport:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Ha a cél a virtuális hálózati TAP esetében az azure belső terheléselosztó:
  
   - Az előtérbeli IP-konfigurációja, az Azure belső terheléselosztó, amely egy későbbi lépésben szolgál egy változóba lekéréséhez. Az azonosító a végpontot, amely szerint a KOPPINTSON forgalom összesítő. Az alábbi példa lekéri az azonosítója a *frontendipconfig1* nevű terheléselosztót az előtérbeli IP-konfigurációt *myInternalLoadBalancer*, az erőforráscsoport neve  *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - Hozzon létre a virtuális hálózati TAP használatával az előtérbeli IP-konfiguráció Azonosítóját a cél és a egy opcionális port tulajdonság. A port célport megadja a ahol KOPPINTSON forgalmat fogadja az előtérbeli IP-konfiguráció:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Erősítse meg a virtuális hálózati TAP létrehozása:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Hálózati adapter egy KOPPINTÁSSAL konfiguráció hozzáadása

1. Beolvasni a meglévő virtuális hálózat KOPPINTSON erőforrás azonosítója. Az alábbi példa lekéri a virtuális hálózati TAP nevű *myTap* nevű erőforráscsoportból *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network tap show show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Hozzon létre egy KOPPINTÁSSAL konfigurációt a figyelt virtuális gép hálózati adapterén. A következő példában létrehozunk egy KOPPINTÁSSAL konfigurációs nevű hálózati adapter *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Erősítse meg a TAP-konfiguráció létrehozása:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>A hálózati adapteren KOPPINTSON konfigurációjának törlése

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --tap-configuration-name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Virtuális hálózat lista koppint az előfizetéshez

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>KOPPINTSON egy erőforráscsoportot a virtuális hálózat törlése

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
