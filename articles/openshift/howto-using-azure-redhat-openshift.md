---
title: Hozzon létre egy Azure Red Hat OpenShift 4.3 fürt | Microsoft dokumentumok
description: Fürt létrehozása az Azure Red Hat OpenShift 4.3-as ával
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, piros kalap, cli
ms.openlocfilehash: 9488ef593cf4ec8600dcb42ea4a2cefa4fcb1446
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998800"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Azure Red Hat OpenShift 4.3-as fürt létrehozása, elérése és kezelése

> [!IMPORTANT]
> Kérjük, vegye figyelembe, hogy az Azure Red Hat OpenShift 4.3 jelenleg csak privát előzetes verzióban érhető el az USA keleti részén és az USA keleti részén 2. A privát előnézet elfogadása csak meghívással történik. Kérjük, regisztrálja az előfizetést, mielőtt megpróbálná engedélyezni ezt a funkciót: [Azure Red Hat OpenShift private preview registration](https://aka.ms/aro-preview-register)

> [!NOTE]
> Az előzetes verzió funkciói önkiszolgálóak, és a rendelkezésre álló mértékben érhetők el, és nem tartoznak a szolgáltatásiszint-szerződés (SLA) és a korlátozott jótállás hatálya alól. Ezért a funkciók nem éles használatra szántak.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Red Hat OpenShift 4.3-as fürt létrehozásához a következőkre lesz szüksége:

- Az Azure CLI 2.0.72-es vagy újabb verziója
  
- Az "az aro" kiterjesztés

- Két üres alhálózatot tartalmazó virtuális hálózat, amelyekhez nincs hálózati biztonsági csoport csatlakoztatva.  A fürt ezekben az alhálózatokban lesz telepítve.

- Fürt AAD-alkalmazás (ügyfélazonosító és titkos) és egyszerű szolgáltatás, `az aro create` vagy elegendő AAD-engedélyek egy AAD-alkalmazás és egyszerű szolgáltatás automatikus létrehozásához.

- Az RP szolgáltatásnévés a fürtegyszerűszolgáltatás-tagnak a fürt virtuális hálózatán közreműködői szerepkört kell bevonnia.  Ha a virtuális hálózaton a "Felhasználói hozzáférés `az aro create` rendszergazdája" szerepkör van, automatikusan beállítja a szerepkör-hozzárendeléseket.

### <a name="install-the-az-aro-extension"></a>Telepítse az "az aro" kiterjesztést
A `az aro` bővítmény lehetővé teszi az Azure Red Hat OpenShift fürtök létrehozását, elérését és törlését közvetlenül a parancssorból az Azure CLI használatával.

> [!Note] 
> A `az aro` kiterjesztés aktuális előnézetben. Előfordulhat, hogy egy későbbi kiadásban megváltozik vagy eltávolodik.
> A `az aro` bővítmény előzetes verziójának letiltásához `Microsoft.RedHatOpenShift` regisztrálnia kell az erőforrás-szolgáltatót.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Bejelentkezik az Azure-ba.

   ```console
   az login
   ```

2. A bővítmény telepítéséhez `az aro` futtassa a következő parancsot:

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
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Két üres alhálózatot tartalmazó virtuális hálózat létrehozása

Két üres alhálózatot tartalmazó virtuális hálózat létrehozásához kövesse az alábbi lépéseket.

1. Állítsa be a következő változókat.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   PULL_SECRET="<optional-pull-secret>"
   ```
   >[!NOTE]
   > Az opcionális lekéréses titok lehetővé teszi, hogy a fürt hozzáférjen a Red Hat tárolójegyzékek és további tartalom eléréséhez.
   >
   > A lekéréses titok https://cloud.redhat.com/openshift/install/azure/installer-provisioned elérése a *Lekéréses titok másolása*gombra kattintva.
   >
   > Be kell jelentkeznie Red Hat-fiókjába, vagy létre kell hoznia egy új Red Hat-fiókot az üzleti e-mail címével, és el kell fogadnia a feltételeket.
 

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

4. Két üres alhálózat hozzáadása a virtuális hálózathoz.

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

5. Tiltsa le a privát kapcsolatszolgáltatás hálózati házirendjeit a virtuális hálózaton és az alhálózatokon. Ez a követelmény az ARO szolgáltatás számára a fürt eléréséhez és kezeléséhez.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Fürt létrehozása

Fürt létrehozásához futtassa a következő parancsot.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret "$PULL_SECRET"
```

>[!NOTE]
> A fürt létrehozása általában körülbelül 35 percet vesz igénybe.

## <a name="access-the-cluster-console"></a>A fürtkonzol elérése

A fürtkonzol URL-címét (az `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`űrlapról) az Azure Red Hat OpenShift 4.3 fürterőforrás alatt találja. Az erőforrás megtekintéséhez futtassa a következő parancsot:

```console
az aro list -o table
```

A felhasználó segítségével jelentkezhet be a `kubeadmin` fürtbe.  A felhasználó jelszavának megkereséséhez `kubeadmin` futtassa a következő parancsot:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Fürt törlése

Fürt törléséhez futtassa a következő parancsot.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
