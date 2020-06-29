---
title: ELAVULT Egyszerű szolgáltatásnév az Azure Kubernetes-fürthöz
description: Kubernetes-fürthöz tartozó Azure Active Directory egyszerű szolgáltatás létrehozása és felügyelete az Azure Container Service-ben
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1653fbcaf81981ca3d32d41c2e2892093922011b
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85504739"
---
# <a name="deprecated-set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>ELAVULT Azure AD egyszerű szolgáltatás beállítása Kubernetes-fürthöz a Container Service-ben

> [!TIP]
> Az Azure Kubernetes Service-t használó cikk frissített verziója: [egyszerű szolgáltatások az Azure Kubernetes szolgáltatással (ak)](../../aks/kubernetes-service-principal.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Az Azure Container Service-ben a Kubernetes számára szükséges egy [Azure Active Directory egyszerű szolgáltatás](../../active-directory/develop/app-objects-and-service-principals.md) az Azure API-kkal való kommunikációhoz. Az egyszerű szolgáltatásnév az erőforrások (például a [felhasználó által megadott útvonalak](../../virtual-network/virtual-networks-udr-overview.md) és a [4. réteg Azure Load Balancer](../../load-balancer/load-balancer-overview.md)) dinamikus kezeléséhez szükséges.


Ebben a cikkben különböző lehetőségeket talál arra, hogyan állíthat be egy egyszerű szolgáltatást a Kubernetes-fürthöz. Ha például telepítette és beállította az [Azure CLI-t](/cli/azure/install-az-cli2)-t, akkor futtathatja a [`az acs create`](/cli/azure/acs) parancsot a Kubernetes-fürt és az egyszerű szolgáltatás egyidejű létrehozásához.


## <a name="requirements-for-the-service-principal"></a>Az egyszerű szolgáltatás követelményei

Meglévő Azure AD egyszerű szolgáltatást is használhat, amely megfelel az alábbi feltételeknek, vagy újat is létrehozhat.

* **Hatókör**: Erőforráscsoport

* **Szerepkör**: közreműködő

* **Ügyfél titkos kulcsa**: jelszónak kell lennie. Jelenleg nem használhat egyszerű szolgáltatás beállítást tanúsítvány hitelesítéshez.

> [!IMPORTANT]
> Egyszerű szolgáltatás létrehozásához rendelkeznie kell engedélyekkel alkalmazások regisztrációjához az Azure AD bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. [Ellenőrizze a portálon](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app), hogy rendelkezik-e a szükséges engedélyekkel.
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>1. lehetőség: Egyszerű szolgáltatás létrehozása az Azure AD-ban

Az Azure-ban többféle módszerrel is létrehozható Azure AD egyszerű szolgáltatás a Kubernetes-fürt üzembe helyezése előtt.

A következő példaparancsok megmutatják, hogyan teheti ezt meg az [Azure CLI](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) segítségével. Másik megoldásként az [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md), a [portál](../../active-directory/develop/howto-create-service-principal-portal.md) és egyéb módszerek használatával is létrehozhat egyszerű szolgáltatást.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

A kimenet a következőkhöz hasonló (itt kivonatosan látható):

![Egyszerű szolgáltatás létrehozása](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Kiemelve az **ügyfél-azonosító** ( `appId` ) és az **ügyfél titkos kulcsa** ( `password` ), amelyet az egyszerű szolgáltatásnév paraméterként használ a fürt üzembe helyezéséhez.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Egyszerű szolgáltatás megadása a Kubernetes-fürt létrehozásakor

A Kubernetes-fürt létrehozásakor adja meg paraméterekként egy létező egyszerű szolgáltatás **ügyfél-azonosítóját** (`appId` néven is szerepel az alkalmazás-azonosító esetében) és a **titkos ügyfélkulcsot** (`password`). Ellenőrizze, hogy az egyszerű szolgáltatás megfelel-e a cikk elején szereplő feltételeknek.

Ezeket a paramétereket a Kubernetes-fürt üzembe helyezésekor adhatja meg az [Azure Command-Line Interface (CLI)](container-service-kubernetes-walkthrough.md) vagy az [Azure Portal](../dcos-swarm/container-service-deployment.md) segítségével, illetve más módszerekkel.

>[!TIP]
>Az **ügyfél-azonosító** megadásakor győződjön meg arról, hogy az egyszerű szolgáltatás `appId`-ját és nem az `ObjectId`-ját használja.
>

Az alábbi példában az Azure CLI-vel adjuk át a paramétereket. Ez a példa a [Kubernetes gyorsindítási sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes) használja.

1. A paraméterfájl-sablont `azuredeploy.parameters.json` a GitHubról [töltheti le](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json).

2. Az egyszerű szolgáltatás megadásához adja meg a `servicePrincipalClientId` és a `servicePrincipalClientSecret` értékét a fájlban. (A saját értékeit is meg kell adnia a `dnsNamePrefix` és az `sshRSAPublicKey` esetében. Az utóbbi az SSH nyilvános kulcsa a fürt eléréséhez.) Mentse a fájlt.

    ![Az egyszerű szolgáltatás paramétereinek továbbítása](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. A `--parameters` használatával futtassa a következő parancsot, amely beállítja az útvonalat az azuredeploy.parameters.json fájlra. Ez a parancs egy, az USA nyugati régiójában létrehozott, `myResourceGroup` nevű erőforráscsoportban helyezi üzembe a fürtöt.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>2. lehetőség: Egyszerű szolgáltatás létrehozása a fürt az `az acs create` paranccsal való létrehozásakor

Ha a parancsot a [`az acs create`](/cli/azure/acs#az-acs-create) Kubernetes-fürt létrehozásához futtatja, akkor lehetősége van automatikusan létrehozni egy egyszerű szolgáltatásnevet.

Hasonlóan mint más Kubernetes-fürt létrehozási lehetőségek esetében, az `az acs create` futtatásakor megadhatja egy meglévő egyszerű szolgáltatás paramétereit. Ha azonban kihagyja ezeket a paramétereket, az Azure CLI létrehoz egyet automatikusan a Container Service szolgáltatással való használatra. Ez az üzembe helyezés során transzparens módon történik.

A következő parancs létrehoz egy Kubernetes-fürtöt, valamint előállítja az SSH-kulcsokat és az egyszerű szolgáltatás hitelesítő adatait:

```azurecli
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Ha a fiók nem rendelkezik az egyszerű szolgáltatás létrehozásához szükséges Azure AD- és előfizetési engedélyekkel, a parancs a következőhöz hasonló hibaüzenetet hoz létre: `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

* Ha az előfizetésben nincs engedélye egyszerű szolgáltatás létrehozására, lehet, hogy meg kell kérnie Azure AD- vagy előfizetés-rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy kérnie kell tőlük egy, az Azure Container Service-szel használható egyszerű szolgáltatást.

* A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.

* Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt egyszerű szolgáltatása társítható bármilyen érvényes Azure AD-alkalmazásnévhez (például: `https://www.contoso.org/example`). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.

* Amikor megadja az egyszerű szolgáltatás **ügyfél-azonosítóját**, használhatja az `appId` értékét (ahogyan az a cikkben látható) vagy a megfelelő egyszerű szolgáltatást `name` (például: `https://www.contoso.org/example`).

* A Kubernetes-fürt fő és ügynök virtuális gépein az egyszerű szolgáltatás hitelesítő adatai az `/etc/kubernetes/azure.json` fájlban találhatók.

* Ha az `az acs create` parancsot használja az egyszerű szolgáltatás automatikus létrehozásához, az egyszerű szolgáltatás hitelesítő adatai a `~/.azure/acsServicePrincipal.json` fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.

* Ha az `az acs create` parancsot használja az egyszerű szolgáltatás automatikus létrehozásához, az egyszerű szolgáltatás hitelesíthető egy, ugyanabban az előfizetésben létrehozott [Azure Container Registryvel](../../container-registry/container-registry-intro.md).

* Az egyszerű szolgáltatásban használt hitelesítő adatok érvényessége lejárhat. Ebben az esetben a fürtcsomópontok **NotReady** állapotba kerülhetnek. A kezelési információkat a [Hitelesítő adatok lejárata](#credential-expiration) szakaszban találja.

## <a name="credential-expiration"></a>Hitelesítő adatok lejárata

Az egyszerű szolgáltatásokban használt hitelesítő adatok a létrehozásuktól számítva 1 évig érvényesek, kivéve, ha megad egy egyéni érvényességi időkeretet a `--years` paraméter segítségével. Ha a hitelesítő adatok érvényessége lejár, előfordulhat hogy a fürtcsomópontok **NotReady** állapotba lépnek.

Az egyszerű szolgáltatás lejárati idejének ellenőrzéséhez hajtsa végre az [az ad app show](/cli/azure/ad/app#az-ad-app-show) parancsot a `--debug` paraméterrel, és keresse a `passwordCredentials``endDate` értékét a kimenet aljához közel:

```azurecli
az ad app show --id <appId> --debug
```

Kimenet (itt csonkolva látható):

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

Ha az egyszerű szolgáltatásban használt hitelesítő adatok érvényessége lejárt, az [az ad sp reset-credentials](/cli/azure/ad/sp) parancs segítségével frissítse a hitelesítő adatokat:

```azurecli
az ad sp reset-credentials --name <appId>
```

Kimenet:

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

Ezután frissítse az `/etc/kubernetes/azure.json` fájlt az új hitelesítő adatokkal minden fürtcsomóponton, és indítsa újra a csomópontokat.

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Kubernetes-szel](container-service-kubernetes-walkthrough.md) a tárolószolgáltatás-fürtben.

* A Kubernetes egyszerű szolgáltatásainak hibaelhárításához tekintse meg az [ACS Engine dokumentációját](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).
