---
title: Oktatóanyag – Azure Red Hat 4. OpenShift-fürt létrehozása
description: Megtudhatja, hogyan hozhat létre Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 96954d0ebf56251a66d4b9c8bdcce07153f64068
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469966"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift 4 fürt létrehozása

Ebben az oktatóanyagban, amely három részből áll, előkészíti a környezetet egy OpenShift 4 rendszerű Azure Red Hat OpenShift-fürt létrehozásához, és létrehoz egy fürtöt. Az alábbiakat fogja elsajátítani:
> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges virtuális hálózatok és alhálózatok létrehozása
> * Fürt üzembe helyezése

## <a name="before-you-begin"></a>Előkészületek

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest).

Az Azure Red Hat OpenShift legalább 40 mag szükséges a OpenShift-fürt létrehozásához és futtatásához. Egy új Azure-előfizetéshez tartozó alapértelmezett Azure-erőforrás-kvóta nem felel meg ennek a követelménynek. Az erőforrás-korlát növeléséhez tekintse meg a [standard kvóta: a határértékek](../azure-portal/supportability/per-vm-quota-requests.md)csökkentése virtuálisgép-sorozat szerint című témakört.

### <a name="verify-your-permissions"></a>Engedélyek ellenőrzése

Azure Red Hat OpenShift-fürt létrehozásához ellenőrizze az alábbi engedélyeket az Azure-előfizetéshez, Azure Active Directory felhasználóhoz vagy egyszerű szolgáltatáshoz:

|Engedélyek|Az VNet tartalmazó erőforráscsoport|Felhasználó által futtatott `az aro create`|Az egyszerű szolgáltatás a következőképpen lett átadva `–client-id`|
|----|:----:|:----:|:----:|
|**Felhasználói hozzáférés adminisztrátora**|X|X| |
|**Közreműködő**|X|X|X|

### <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

Ezután regisztrálnia kell az `Microsoft.RedHatOpenShift` erőforrás-szolgáltatót az előfizetésében.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat pull-titok beolvasása (nem kötelező)

A Red Hat pull Secret lehetővé teszi, hogy a fürt hozzáférjen a Red Hat Container-jegyzékekhez a további tartalommal együtt. Ez a lépés nem kötelező, de ajánlott.

1. **[Navigáljon a Red Hat OpenShift a cluster Manager-portálra](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) , és jelentkezzen be.**

   Be kell jelentkeznie a Red Hat-fiókjába, vagy létre kell hoznia egy új Red Hat-fiókot az üzleti e-mail-címével, és el kell fogadnia a használati feltételeket.

2. Ha első alkalommal hoz létre fürtöt, nyissa meg a [**OpenShift termék lapját**](https://developers.redhat.com/products/codeready-containers) . A regisztráció után lépjen a [**Red Hat OpenShift a Fürtfelügyelő lapra**](https://cloud.redhat.com/openshift/), ahol a **pull Secret letöltése** elemre kattinthat, és letöltheti az ARO-fürthöz használni kívánt lekéréses titkot.

Tartsa biztonságos helyen a mentett `pull-secret.txt` fájlt. A fájl minden egyes fürt létrehozásakor használatos, ha olyan fürtöt kell létrehoznia, amely a Red hat vagy a Certified partnereknek szóló mintákat vagy operátorokat tartalmaz.

A parancs futtatásakor a (z `az aro create` ) paraméter használatával hivatkozhat a lekéréses titkos kulcsra `--pull-secret @pull-secret.txt` . Futtassa `az aro create` azt a könyvtárat, ahová a fájlt mentette `pull-secret.txt` . Ellenkező esetben cserélje le `@pull-secret.txt` a-t a kifejezésre `@<path-to-my-pull-secret-file>` .

Ha átmásolja a lekéréses titkot, vagy más parancsfájlokban hivatkozik rá, a lekéréses titkot érvényes JSON-karakterláncként kell formázni.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Egyéni tartomány előkészítése a fürt számára (nem kötelező)

A parancs futtatásakor `az aro create` megadhat egy egyéni tartományt a fürthöz a `--domain foo.example.com` paraméter használatával.

Ha a fürthöz egyéni tartományt ad meg, vegye figyelembe a következő szempontokat:

* A fürt létrehozása után 2 DNS-rekordot kell létrehoznia a DNS-kiszolgálón a megadott értékhez `--domain` :
    * **API** – az API-kiszolgálóra mutat
    * ** \* . apps** – a bejövő forgalomra mutat
    * A következő parancs végrehajtásával kérheti le ezeket az értékeket: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* A OpenShift-konzol a beépített tartomány helyett egy URL-címen lesz elérhető `https://console-openshift-console.apps.foo.example.com` `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Alapértelmezés szerint a OpenShift önaláírt tanúsítványokat használ a által létrehozott összes útvonalhoz `*.apps.<random>.<location>.aroapp.io` .  Ha úgy dönt, hogy a fürthöz való csatlakozás után egyéni DNS-t használ, akkor a OpenShift dokumentációjában [be kell állítania egy egyéni hitelesítésszolgáltatót a bejövő vezérlőhöz](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) , valamint az [API-kiszolgáló egyéni hitelesítésszolgáltatóját](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Két üres alhálózatot tartalmazó virtuális hálózat létrehozása

Ezután létre fog hozni egy virtuális hálózatot, amely két üres alhálózatot tartalmaz.

1. **Állítsa be a következő változókat abban a rendszerhéj-környezetben, amelyben végre fogja hajtani a `az` parancsokat.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Hozzon létre egy erőforráscsoportot.**

    Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal.
    
> [!NOTE]
> Az Azure Red Hat OpenShift nem érhető el minden olyan régióban, ahol létre lehet hozni egy Azure-erőforráscsoportot. Tekintse meg az [elérhető régiók](https://docs.openshift.com/aro/4/welcome/index.html#available-regions) című témakört az Azure Red Hat OpenShift támogatásával kapcsolatban.

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    The following example output shows the resource group created successfully:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Hozzon létre egy virtuális hálózatot.**

    A OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürtökhöz két üres alhálózattal rendelkező virtuális hálózat szükséges a fő-és munkavégző csomópontokhoz.

    Hozzon létre egy új virtuális hálózatot ugyanabban az erőforráscsoportban, amelyet korábban hozott létre:

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    A következő példa kimenete a virtuális hálózat sikeres létrehozását mutatja be:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Adjon hozzá üres alhálózatot a főcsomópontokhoz.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Adjon hozzá üres alhálózatot a munkavégző csomópontokhoz.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Tiltsa le az alhálózat magánhálózati végpontjának házirendjeit](../private-link/disable-private-link-service-network-policy.md) a fő alhálózaton.** Ez szükséges ahhoz, hogy csatlakozni tudjon és kezelhesse a fürtöt.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>A fürt létrehozása

Futtassa a következő parancsot egy fürt létrehozásához. Ha a következő lehetőségek egyikét választja, módosítsa a parancsot ennek megfelelően:
* [A Red Hat pull Secret](#get-a-red-hat-pull-secret-optional) is átadható, amely lehetővé teszi, hogy a fürt hozzáférjen a Red Hat Container-jegyzékekhez a további tartalommal együtt. Adja hozzá az `--pull-secret @pull-secret.txt` argumentumot a parancshoz.
* Igény szerint [Egyéni tartományt is használhat](#prepare-a-custom-domain-for-your-cluster-optional). Adja hozzá az `--domain foo.example.com` argumentumot a parancshoz, és cserélje le a kifejezést `foo.example.com` a saját egyéni tartományára.

> [!NOTE]
> Ha nem kötelező argumentumokat ad hozzá a parancshoz, akkor ügyeljen arra, hogy a parancs előző sorában található argumentumot záró fordított perjeltel adja meg.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

A parancs végrehajtása után az `az aro create` általában körülbelül 35 percet vesz igénybe a fürt létrehozásakor.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges virtuális hálózatok és alhálózatok létrehozása
> * Fürt üzembe helyezése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Kapcsolódás Azure Red Hat OpenShift-fürthöz](tutorial-connect-cluster.md)