---
title: Azure Red Hat OpenShift 4 privát fürt létrehozása
description: Ismerje meg, hogyan hozhat létre a OpenShift-t futtató Azure Red Hat OpenShift Private-fürtöt
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 581587382c3bfd03ed329672e5c6ca065554d1c7
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727640"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Azure Red Hat OpenShift 4 privát fürt létrehozása

Ebben a cikkben előkészíti a környezetet a OpenShift 4 rendszerű Azure Red Hat OpenShift-fürtök létrehozására. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges virtuális hálózatok és alhálózatok létrehozása
> * Fürt üzembe helyezése privát API-kiszolgálói végponttal és privát bejövő adatkezelővel

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.75 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Előkészületek

### <a name="install-the-az-aro-extension"></a>Az "az ARO" bővítmény telepítése
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

1. **[Nyissa meg a Red Hat OpenShift cluster Manager portált](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) , és jelentkezzen be.**

   Be kell jelentkeznie a Red Hat-fiókjába, vagy létre kell hoznia egy új Red Hat-fiókot az üzleti e-mail-címével, és el kell fogadnia a használati feltételeket.

2. **Kattintson a lekérési titok letöltése elemre.**

Tartsa meg a mentett `pull-secret.txt` fájlt biztonságos helyen – a rendszer minden egyes fürt létrehozásakor használni fogja.

A parancs futtatásakor a (z `az aro create` ) paraméter használatával hivatkozhat a lekéréses titkos kulcsra `--pull-secret @pull-secret.txt` . Futtassa `az aro create` azt a könyvtárat, ahová a fájlt mentette `pull-secret.txt` . Ellenkező esetben cserélje le `@pull-secret.txt` a-t a kifejezésre `@<path-to-my-pull-secret-file` .

Ha átmásolja a lekéréses titkot, vagy más parancsfájlokban hivatkozik rá, a lekéréses titkot érvényes JSON-karakterláncként kell formázni.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Két üres alhálózatot tartalmazó virtuális hálózat létrehozása

Ezután létre fog hozni egy virtuális hálózatot, amely két üres alhálózatot tartalmaz.

1. **Állítsa be a következő változókat.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
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
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

A parancs végrehajtása után az `az aro create` általában körülbelül 35 percet vesz igénybe a fürt létrehozásakor.

>[!IMPORTANT]
> Ha úgy dönt, hogy egyéni tartományt ad meg, például **foo.example.com**, a OpenShift-konzol a beépített tartomány helyett egy URL-címen lesz elérhető `https://console-openshift-console.apps.foo.example.com` `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .
>
> Alapértelmezés szerint a OpenShift önaláírt tanúsítványokat használ a által létrehozott összes útvonalhoz `*.apps.<random>.<location>.aroapp.io` .  Ha az egyéni DNS lehetőséget választja, akkor a fürthöz való csatlakozás után a OpenShift dokumentációjában [be kell állítania egy egyéni hitelesítésszolgáltatót](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) az API-kiszolgáló bejövő vezérlője és [Egyéni hitelesítésszolgáltatója](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)számára.

## <a name="connect-to-the-private-cluster"></a>Kapcsolódás a privát fürthöz

A felhasználó segítségével bejelentkezhet a fürtbe `kubeadmin` .  Futtassa a következő parancsot a felhasználó jelszavának megkereséséhez `kubeadmin` .

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Az alábbi példa kimenetében látható, hogy a jelszó a következő lesz: `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

A fürt konzoljának URL-címét a következő parancs futtatásával érheti el, amely a következőképpen fog kinézni:`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Egy privát Azure Red Hat OpenShift-fürthöz való kapcsolódáshoz a következő lépést kell elvégeznie egy olyan gazdagépen, amely vagy a létrehozott Virtual Network vagy egy olyan Virtual Network, amely a fürtnek a Virtual Network való üzembe helyezéséhez [van társítva](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

Indítsa el a konzol URL-címét egy böngészőben, és jelentkezzen be a `kubeadmin` hitelesítő adatok használatával.

![Azure Red Hat OpenShift bejelentkezési képernyő](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>A OpenShift parancssori felületének telepítése

Ha bejelentkezett a OpenShift webkonzolba, kattintson a következőre **?** a jobb felső sarokban, majd a **parancssori eszközök menüpontban**. Töltse le a számítógépének megfelelő kiadást.

![Azure Red Hat OpenShift bejelentkezési képernyő](media/aro4-download-cli.png)

A parancssori felület legújabb kiadását is letöltheti a gépre <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

## <a name="connect-using-the-openshift-cli"></a>Kapcsolat a OpenShift CLI használatával

Kérje le az API-kiszolgáló címeit.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Egy privát Azure Red Hat OpenShift-fürthöz való kapcsolódáshoz a következő lépést kell elvégeznie egy olyan gazdagépen, amely vagy a létrehozott Virtual Network vagy egy olyan Virtual Network, amely a fürtnek a Virtual Network való üzembe helyezéséhez [van társítva](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

Jelentkezzen be a OpenShift-fürt API-kiszolgálójára a következő parancs használatával. Cserélje le a ** \< kubeadmin Password>** az imént beolvasott jelszóra.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy, a OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürtöt telepítettünk. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges virtuális hálózatok és alhálózatok létrehozása
> * Fürt üzembe helyezése
> * Kapcsolódás a fürthöz a `kubeadmin` felhasználó használatával

A következő cikkből megtudhatja, hogyan konfigurálhatja a fürtöt hitelesítésre Azure Active Directory használatával.


* [A Azure Active Directory hitelesítés konfigurálása a parancssor használatával](configure-azure-ad-cli.md)


* [A Azure Active Directory hitelesítés konfigurálása a Azure Portal és a OpenShift webkonzol használatával](configure-azure-ad-cli.md)
