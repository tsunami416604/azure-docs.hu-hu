---
title: "Az Azure Kubernetes-fürthöz tartozó egyszerű szolgáltatás | Microsoft Docs"
description: "Azure Active Directory egyszerű szolgáltatás létrehozása és kezelése az Azure Container Service-fürtben Kubernetes-szel"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 71c6c5ffacf49b907e3e9f488789f31928b25823
ms.openlocfilehash: e01a9ef7d223e7a5a06475cf419b73959baa803f


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Tudnivalók a Kubernetes-fürthöz tartozó Azure Active Directory egyszerű szolgáltatásról az Azure Container Service-ben



Az Azure Container Service-ben a Kubernetes-nek szüksége van egy [Azure Active Directory egyszerű szolgáltatásra](../active-directory/active-directory-application-objects.md), amely szolgáltatásfiókként kommunikál az Azure API-kkal. Az egyszerű szolgáltatással dinamikusan kezelhet olyan erőforrásokat, mint a felhasználó által meghatározott útvonalak és a 4. rétegű Azure Load Balancer.

Ebben a cikkben különböző lehetőségeket talál arra, hogy hogyan adhat meg egy egyszerű szolgáltatást a Kubernetes-fürthöz. Például ha telepítette és beállította az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)-t, akkor futtathatja a [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) parancsot a Kubernetes-fürt és az egyszerű szolgáltatás egyidejű létrehozásához.



## <a name="requirements-for-the-service-principal"></a>Az egyszerű szolgáltatás követelményei

A következő követelményekkel rendelkezik az Azure Active Directory egyszerű szolgáltatás a Kubernetes-fürtben az Azure Container Service-ben. 

* **Hatókör**: az erőforráscsoport, amelyben a fürt üzembe lesz helyezve

* **Szerepkör**: **Közreműködő**

* **Titkos ügyfélkulcs**: egy jelszónak kell lennie. Jelenleg nem használhat egyszerű szolgáltatás beállítást tanúsítvány hitelesítéshez.

> [!NOTE]
> Minden egyszerű szolgáltatás társítva van egy Azure Active Directory-alkalmazással. A Kubernetes-fürthöz tartozó egyszerű szolgáltatás társítható bármilyen érvényes Azure Active Directory-alkalmazásnévvel.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>A Kubernetes-fürthöz tartozó egyszerű szolgáltatás lehetőségei

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>1. lehetőség: Az egyszerű szolgáltatás azonosítója és a titkos ügyfélkulcs továbbítása

A Kubernetes-fürt létrehozásakor adja meg paraméterekként egy létező egyszerű szolgáltatás **ügyfél-azonosítóját** (`appId` néven is szerepel az alkalmazás-azonosító esetében) és a **titkos ügyfélkulcsot** (`password`). Ha egy meglévő egyszerű szolgáltatást használ, győződjön meg arról, hogy megfelel az előző szakaszban ismertetett követelményeknek. Ha létre kell hozni az egyszerű szolgáltatást, tekintse meg a cikkben lentebb található [Egyszerű szolgáltatás létrehozása](#create-a-service-principal-in-azure-active-directory) szakaszt.

Ezeket a paramétereket a [Kubernetes-fürt üzembe helyezésekor](./container-service-deployment.md) adhatja meg a portálon, az Azure parancssori felületének (CLI) 2.0-s verziójában, az Azure PowerShellben vagy más módszerekkel.

>[!TIP] 
>Az **ügyfél-azonosító** megadásakor győződjön meg arról, hogy az egyszerű szolgáltatás `appId`-ját és nem az `ObjectId`-ját használja.
>

Az alábbi példában az Azure CLI 2.0-s verziójával adjuk át a paramétereket (lásd a [telepítési és beállítási utasításokat](/cli/azure/install-az-cli2)). Ez a példa a [Kubernetes gyorsindítási sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes) használja.

1. A paraméterfájl-sablont `azuredeploy.parameters.json` a GitHubról [töltheti le](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json).

2. Az egyszerű szolgáltatás megadásához adja meg a `servicePrincipalClientId` és a `servicePrincipalClientSecret` értékét a fájlban. (A saját értékeit is meg kell adnia a `dnsNamePrefix` és az `sshRSAPublicKey` esetében. Az utóbbi a nyilvános SSH-kulcs, amely hozzáfér a fürthöz.) Mentse a fájlt.

    ![Az egyszerű szolgáltatás paramétereinek továbbítása](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. A `--parameters` használatával futtassa a következő parancsot, amely beállítja az útvonalat az azuredeploy.parameters.json fájlra. Ez a parancs egy, az USA nyugati régiójában létrehozott, `myResourceGroup` nevű erőforráscsoportban helyezi üzembe a fürtöt.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus" 
    
    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20"></a>2. lehetőség: Az egyszerű szolgáltatás létrehozása a fürt létrehozáskor az Azure CLI 2.0 használatával

Ha telepítette és beállította az [Azure CLI 2.0-s verziót](https://docs.microsoft.com/cli/azure/install-az-cli2), akkor futtathatja az [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) parancsot a [fürt létrehozásához](./container-service-create-acs-cluster-cli.md).

Hasonlóan mint más Kubernetes-fürt létrehozási lehetőségek esetében, az `az acs create` futtatásakor megadhatja egy meglévő egyszerű szolgáltatás paramétereit. Azonban ha kihagyja ezeket a paramétereket, az Azure Container Service automatikusan létrehoz egy egyszerű szolgáltatást. Ez az üzembe helyezés során transzparens módon történik. 

A következő parancs létrehoz egy Kubernetes-fürtöt, valamint előállítja az SSH-kulcsokat és az egyszerű szolgáltatás hitelesítő adatait:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban

Az Azure többféle módszert is biztosít, ha létre szeretne hozni egy egyszerű szolgáltatást az Azure Active Directory-ban a Kubernetes-fürtben való használatra. 

A következő példaparancsok megmutatják, hogyan teheti ezt meg az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) segítségével. Az [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md), a [klasszikus portál](../azure-resource-manager/resource-group-create-service-principal-portal.md) és egyéb módszerek használatával is létrehozhat egyszerű szolgáltatásokat.

> [!IMPORTANT]
> Ne felejtse el áttekinteni az egyszerű szolgáltatás követelményeit, amelyet a cikkben korábban ismertettünk.
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Ez a következőhöz hasonló kimenetet ad vissza (itt kivonatosan látható):

![Egyszerű szolgáltatás létrehozása](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

A fürt üzembe helyezéséhez használt egyszerű szolgáltatás paraméterek, az **ügyfél-azonosító** (`appId`) és a **titkos ügyfélkulcs** (`password`), ki vannak emelve.


Erősítse meg az egyszerű szolgáltatást a következő parancs futtatásával egy új felületen, helyettesítve a `appId`, a `password`, és a `tenant`:

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Néhány fontos megjegyzés


* Amikor megadja az egyszerű szolgáltatás **ügyfél-azonosítóját**, használhatja a `appId` értékét (a cikkben látottaknak megfelelően) vagy a megfelelő egyszerű szolgáltatást `name` (például        `https://www.contoso.org/example`).

* Ha az `az acs create` parancsot használja az egyszerű szolgáltatás automatikus létrehozására, az egyszerű szolgáltatás hitelesítő adatai a ~/.azure/acsServicePrincipal.json fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.

* A Kubernetes-fürt mester és csomópont virtuális gépein az egyszerű szolgáltatás hitelesítő adatai az /etc/kubernetes/azure.json fájlban lesznek tárolva.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Kubernetes-szel](container-service-kubernetes-walkthrough.md) a tárolószolgáltatás-fürtben.



<!--HONumber=Feb17_HO4-->


