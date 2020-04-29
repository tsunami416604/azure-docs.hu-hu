---
title: Azure Red Hat OpenShift 4,3-fürt létrehozása | Microsoft Docs
description: Fürt létrehozása az Azure Red Hat OpenShift 4,3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398895"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Azure Red Hat OpenShift 4,3-fürt létrehozása, elérése és kezelése

> [!IMPORTANT]
> Vegye figyelembe, hogy az Azure Red Hat OpenShift 4,3 jelenleg csak privát előzetes verzióban érhető el az USA keleti régiójában és az USA 2. keleti régiójában. A privát előzetes verzió elfogadása csak meghívóval történik. Ügyeljen arra, hogy regisztrálja az előfizetését a funkció engedélyezése előtt: [Azure Red Hat OpenShift Private Preview regisztráció](https://aka.ms/aro-preview-register)

> [!NOTE]
> Az előzetes verziójú funkciók önkiszolgálást biztosítanak, és a rendelkezésre álló és elérhető módon érhetők el, és nem tartoznak a szolgáltatói szerződéssel (SLA) és a korlátozott jótállással. A funkciók ezért nem használhatók éles környezetben.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Red Hat OpenShift 4,3-fürt létrehozásához a következőkre lesz szüksége:

- Az Azure CLI verziója 2.0.72 vagy újabb
  
- Az "az ARO" bővítmény

- Olyan virtuális hálózat, amely két üres alhálózatot tartalmaz, amelyek mindegyike nem rendelkezik csatolt hálózati biztonsági csoporttal.  A fürt ezekbe az alhálózatokra lesz telepítve.

- Egy HRE alkalmazás (ügyfél-azonosító és titkos kulcs) és egyszerű szolgáltatásnév, vagy elegendő HRE engedély `az aro create` a HRE-alkalmazás és egyszerű szolgáltatás automatikus létrehozásához.

- Az RP-szolgáltatásnév és a fürtszolgáltatási rendszerbiztonsági tag mindegyikének közreműködői szerepkörrel kell rendelkeznie a fürt virtuális hálózatán.  Ha a virtuális hálózaton a "felhasználói hozzáférés rendszergazdája" szerepkör található, akkor automatikusan `az aro create` beállítja a szerepkör-hozzárendeléseket.

### <a name="install-the-az-aro-extension"></a>Az "az ARO" bővítmény telepítése
A `az aro` bővítmény lehetővé teszi, hogy közvetlenül a parancssorból az Azure CLI használatával hozza létre, elérje és törölje az Azure Red Hat OpenShift-fürtöket.

> [!Note] 
> A `az aro` bővítmény jelenleg előzetes verzióban érhető el. Előfordulhat, hogy egy későbbi kiadásban módosítják vagy el lettek távolítva.
> Ha a `az aro` bővítmény előzetes verziójára szeretne bejelentkezni, regisztrálnia kell `Microsoft.RedHatOpenShift` az erőforrás-szolgáltatót.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Bejelentkezik az Azure-ba.

   ```console
   az login
   ```

2. A `az aro` bővítmény telepítéséhez futtassa a következő parancsot:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Ellenőrizze, hogy az ARO-bővítmény regisztrálva van-e.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat pull-titok beolvasása (nem kötelező)

A Red Hat pull Secret lehetővé teszi, hogy a fürt hozzáférjen a Red Hat Container-jegyzékekhez és a további tartalmakhoz. A lekéréses titok használata nem kötelező, de ajánlott.

A lekéréses titok beszerzése:

1. Nyissa meg a következőt: https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Jelentkezzen be a Red Hat-fiókjába, vagy hozzon létre egy új Red Hat-fiókot az üzleti e-mail-cím használatával; fogadja el a feltételeket és a kikötéseket.
1. Válassza a **lekéréses titok letöltése**lehetőséget.

Mentse a *pull-Secret. txt* fájlt biztonságos helyre. minden alkalommal, amikor fürtöt hoz létre, a fájlt fogja használni.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Két üres alhálózatot tartalmazó virtuális hálózat létrehozása

Kövesse az alábbi lépéseket egy olyan virtuális hálózat létrehozásához, amely két üres alhálózatot tartalmaz.

1. Állítsa be a következő változókat.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Hozzon létre egy erőforráscsoportot a fürthöz.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Hozza létre a virtuális hálózatot.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Adjon hozzá két üres alhálózatot a virtuális hálózathoz.

   ```console
   for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Tiltsa le a hálózati házirendeket a magánhálózati kapcsolat szolgáltatáshoz a virtuális hálózaton és az alhálózatokon. Ez az a követelmény, hogy az ARO szolgáltatás elérje és felügyelje a fürtöt.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Fürt létrehozása

Futtassa a következő parancsot egy fürt létrehozásához.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> A fürt létrehozása általában körülbelül 35 percet vesz igénybe.

## <a name="access-the-cluster-console"></a>A fürt konzoljának elérése

Az Azure Red Hat OpenShift 4,3 fürterőforrás alatt megtalálhatja `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`a fürt konzoljának URL-címét (az űrlapot). A következő parancs futtatásával tekintheti meg az erőforrást:

```console
az aro list -o table
```

A `kubeadmin` felhasználó segítségével bejelentkezhet a fürtbe.  Futtassa a következő parancsot a `kubeadmin` felhasználó jelszavának megkereséséhez:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Fürt törlése

Futtassa a következő parancsot a fürt törléséhez.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
