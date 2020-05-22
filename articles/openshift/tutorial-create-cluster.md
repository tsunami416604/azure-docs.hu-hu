---
title: Oktatóanyag – Azure Red Hat 4. OpenShift-fürt létrehozása
description: Megtudhatja, hogyan hozhat létre Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 86e1bc088c3e4327fbd0b9ad4a05e7c42c3fb776
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773491"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift 4 fürt létrehozása

Ebben az oktatóanyagban, amely három részből áll, előkészíti a környezetet egy OpenShift 4 rendszerű Azure Red Hat OpenShift-fürt létrehozásához, és létrehoz egy fürtöt. Az alábbiakat fogja elsajátítani:
> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges virtuális hálózatok és alhálózatok létrehozása
> * Fürt üzembe helyezése

## <a name="before-you-begin"></a>Előkészületek

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.75 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Engedélyek ellenőrzése

Azure Red Hat OpenShift-fürt létrehozásához ellenőrizze az alábbi engedélyeket az Azure-fiók és-felhasználó számára:

[!div class="mx-tdBreakAll"]
|Engedélyek|Az VNet tartalmazó erőforráscsoport|Felhasználó által futtatott`az aro create`|Az egyszerű szolgáltatás a következőképpen lett átadva`–client-id`|
|----|:----:|:----:|:----:|
|**Felhasználói hozzáférés adminisztrátora**|X|X| |
|**Közreműködő**|X|X|X|

### <a name="install-the-az-aro-extension"></a>A `az aro` bővítmény telepítése
A `az aro` bővítmény lehetővé teszi, hogy közvetlenül a parancssorból az Azure CLI használatával hozza létre, elérje és törölje az Azure Red Hat OpenShift-fürtöket.

A bővítmény telepítéséhez futtassa a következő parancsot `az aro` .

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Ha már telepítette a bővítményt, a következő parancs futtatásával frissítheti azt.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

Ezután regisztrálnia kell az `Microsoft.RedHatOpenShift` erőforrás-szolgáltatót az előfizetésében.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Ellenőrizze, hogy a bővítmény regisztrálva van-e.

```azurecli-interactive
az -v
```

  Az alábbihoz hasonló kimenetet kell kapnia.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat pull-titok beolvasása (nem kötelező)

A Red Hat pull Secret lehetővé teszi, hogy a fürt hozzáférjen a Red Hat Container-jegyzékekhez a további tartalommal együtt. Ez a lépés nem kötelező, de ajánlott.

1. **[Navigáljon a Red Hat OpenShift a cluster Manager-portálra](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) , és jelentkezzen be.**

   Be kell jelentkeznie a Red Hat-fiókjába, vagy létre kell hoznia egy új Red Hat-fiókot az üzleti e-mail-címével, és el kell fogadnia a használati feltételeket.

2. **Kattintson a lekérési titok letöltése elemre.**

Tartsa meg a mentett `pull-secret.txt` fájlt biztonságos helyen – a rendszer minden egyes fürt létrehozásakor használni fogja.

A parancs futtatásakor a (z `az aro create` ) paraméter használatával hivatkozhat a lekéréses titkos kulcsra `--pull-secret @pull-secret.txt` . Futtassa `az aro create` azt a könyvtárat, ahová a fájlt mentette `pull-secret.txt` . Ellenkező esetben cserélje le `@pull-secret.txt` a-t a kifejezésre `@<path-to-my-pull-secret-file>` .

Ha átmásolja a lekéréses titkot, vagy más parancsfájlokban hivatkozik rá, a lekéréses titkot érvényes JSON-karakterláncként kell formázni.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Két üres alhálózatot tartalmazó virtuális hálózat létrehozása

Ezután létre fog hozni egy virtuális hálózatot, amely két üres alhálózatot tartalmaz.

1. **Állítsa be a következő változókat.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Erőforráscsoport létrehozása**

    Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot az [az Group Create] [az-Group-Create] parancs használatával.

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    A következő példa kimenete azt mutatja, hogy az erőforráscsoport sikeresen létrejött:

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

    Hozzon létre egy új virtuális hálózatot ugyanabban az erőforráscsoporthoz, amelyet korábban hozott létre.

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

5. **[Tiltsa le az alhálózat magánhálózati végpontjának házirendjeit](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) a fő alhálózaton.** Ez szükséges ahhoz, hogy csatlakozni tudjon és kezelhesse a fürtöt.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>A fürt létrehozása

Futtassa a következő parancsot egy fürt létrehozásához. [A Red Hat pull Secret](#get-a-red-hat-pull-secret-optional) is átadható, amely lehetővé teszi, hogy a fürt hozzáférjen a Red Hat Container-jegyzékekhez a további tartalommal együtt.

>[!NOTE]
> Ha másolási és beillesztési parancsokat használ, és a választható paraméterek egyikét használja, ügyeljen arra, hogy törölje a kezdeti hashtageket és a záró megjegyzés szövegét. Emellett a parancs előző sorában található argumentumot záró fordított perjeltel is lezárhatja.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

A parancs végrehajtása után az `az aro create` általában körülbelül 35 percet vesz igénybe a fürt létrehozásakor.

>[!IMPORTANT]
> Ha úgy dönt, hogy egyéni tartományt ad meg, például **foo.example.com**, a OpenShift-konzol a beépített tartomány helyett egy URL-címen lesz elérhető `https://console-openshift-console.apps.foo.example.com` `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .
>
> Alapértelmezés szerint a OpenShift önaláírt tanúsítványokat használ a által létrehozott összes útvonalhoz `*.apps.<random>.<location>.aroapp.io` .  Ha úgy dönt, hogy a fürthöz való csatlakozás után egyéni DNS-t használ, akkor a OpenShift dokumentációjában [be kell állítania egy egyéni hitelesítésszolgáltatót a bejövő vezérlőhöz](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) , valamint az [API-kiszolgáló egyéni hitelesítésszolgáltatóját](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges virtuális hálózatok és alhálózatok létrehozása
> * Fürt üzembe helyezése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Kapcsolódás Azure Red Hat OpenShift-fürthöz](tutorial-connect-cluster.md)
