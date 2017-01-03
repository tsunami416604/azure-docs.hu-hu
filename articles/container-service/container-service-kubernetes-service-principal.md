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
ms.date: 12/20/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 24e12e4606a5ec4fabf7046fe9847123033bb70a
ms.openlocfilehash: 073a9e66ac68643b27ecdd44a4ecac3ad79ec218


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Tudnivalók a Kubernetes-fürthöz tartozó Azure Active Directory egyszerű szolgáltatásról az Azure Container Service-ben



Az Azure Container Service-ben a Kubernetes-nek szüksége van egy [Azure Active Directory egyszerű szolgáltatásra](../active-directory/active-directory-application-objects.md), amely szolgáltatásfiókként kommunikál az Azure API-kkal. Az egyszerű szolgáltatással dinamikusan kezelhet olyan erőforrásokat, mint a [felhasználó által meghatározott útvonalak](../virtual-network/virtual-networks-udr-overview.md) és a 4. rétegű [Azure Load Balancer](../load-balancer/load-balancer-overview.md).

Ebben a cikkben különböző lehetőségeket talál arra, hogy hogyan adhat meg egy egyszerű szolgáltatást a Kubernetes-fürthöz. Például ha telepítette és beállította az [Azure CLI 2.0-t (Előzetes verzió)](https://docs.microsoft.com/cli/azure/install-az-cli2), akkor futtathatja a [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) parancsot a Kubernetes-fürt és az egyszerű szolgáltatás egyidejű létrehozásához.

> [!NOTE]
> A Kubernetes támogatása az Azure Container Service-ben jelenleg előzetes verzióban van.


## <a name="requirements-for-the-service-principal"></a>Az egyszerű szolgáltatás követelményei

A következő követelményekkel rendelkezik az Azure Active Directory egyszerű szolgáltatás a Kubernetes-fürtben az Azure Container Service-ben. 

* **Hatókör** – az Azure-előfizetés, amelyben a fürt üzembe lesz helyezve

* **Szerepkör** - **Közreműködő**

* **Titkos ügyfélkulcs** – egy jelszónak kell lennie. Jelenleg nem használhat egyszerű szolgáltatás beállítást tanúsítvány hitelesítéshez.

> [!NOTE]
> Minden egyszerű szolgáltatás társítva van egy Azure Active Directory-alkalmazással. A Kubernetes-fürthöz tartozó egyszerű szolgáltatás társítható bármilyen érvényes Azure Active Directory-alkalmazásnévvel.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>A Kubernetes-fürthöz tartozó egyszerű szolgáltatás lehetőségei

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>1. lehetőség: Az egyszerű szolgáltatás azonosítója és a titkos ügyfélkulcs továbbítása

A Kubernetes-fürt létrehozásakor adja meg paraméterekként egy létező egyszerű szolgáltatás **ügyfél-azonosítóját** (gyakran szerepel `appId` néven az alkalmazás-azonosító esetében) és a **titkos ügyfélkulcsot** (`password`). Ha egy meglévő egyszerű szolgáltatást használ, győződjön meg arról, hogy megfelel az előző szakaszban ismertetett követelményeknek. Ha létre kell hozni az egyszerű szolgáltatást, tekintse meg a cikkben lentebb található [Egyszerű szolgáltatás létrehozása](#create-a-service-principal-in-azure-active-directory) szakaszt.

A [Kubernetes-fürt üzembe helyezésekor](./container-service-deployment.md) megadhatja ezeket a paramétereket a portál, az Azure Command-Line Interface (CLI) vagy az Azure PowerShell használatával.

>[!TIP] 
>Az **ügyfél-azonosító** megadásakor győződjön meg arról, hogy az egyszerű szolgáltatás `appId`-ját és nem az `ObjectId`-ját használja.
>

A következő példa megmutatja a paraméterek továbbításának egyik módját az [Azure CLI](../xplat-cli-install.md) használatával [Resource Manager módban](../xplat-cli-connect.md). Ez a példa a [Kubernetes gyorsindítási sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes) használja.

1. [Töltse le](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) az azuredeploy.parameters.json sablonparaméter-fájlt a Githubról.

2. Az egyszerű szolgáltatás megadásához adja meg a `servicePrincipalClientId` és a `servicePrincipalClientSecret` értékét a fájlban. (A saját értékeit is meg kell adnia a `dnsNamePrefix` és az `sshRSAPublicKey` esetében. Az utóbbi a nyilvános SSH-kulcs, amely hozzáfér a fürthöz.) Mentse a fájlt.

    ![Az egyszerű szolgáltatás paramétereinek továbbítása](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Futtassa a következő parancsot a `-e` paraméter használatával, a azuredeploy.parameters.json fájlhoz való elérési út beállításához. Ez a parancs üzembe helyezi a fürtöt egy meglévő `myResourceGroup` néven szereplő erőforráscsoportban.

    ```CLI
    azure group deployment create -n myClusterName -g myResourceGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" -e azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>2. lehetőség: Az egyszerű szolgáltatás létrehozása a fürt létrehozáskor az Azure CLI 2.0 Előzetes verzió használatával

Ha telepítette és beállította az [Azure CLI 2.0-t (Előzetes verzió)](https://docs.microsoft.com/cli/azure/install-az-cli2), akkor futtathatja a [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) parancsot a [fürt létrehozásához](./container-service-create-acs-cluster-cli.md).

Hasonlóan mint más Kubernetes-fürt létrehozási lehetőségek esetében, az `az acs create` futtatásakor megadhatja egy meglévő egyszerű szolgáltatás paramétereit. Azonban ha kihagyja ezeket a paramétereket, az Azure Container Service automatikusan létrehoz egy egyszerű szolgáltatást. Ez az üzembe helyezés során transzparens módon történik. 

A következő parancs létrehoz egy Kubernetes-fürtöt, valamint előállítja az SSH-kulcsokat és az egyszerű szolgáltatás hitelesítő adatait:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban

Az Azure többféle módszert is biztosít, ha létre szeretne hozni egy egyszerű szolgáltatást az Azure Active Directory-ban a Kubernetes-fürtben való használatra. 

A következő példaparancsok megmutatják, hogyan teheti ezt meg az [Azure CLI 2.0 (Előzetes verzió)](https://docs.microsoft.com/cli/azure/install-az-cli2) segítségével. Az egyszerű szolgáltatást létrehozhatja az [Azure Command-Line Interface](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md), az [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md), vagy a [klasszikus portál](../azure-resource-manager/resource-group-create-service-principal-portal.md) segítségével is.

> [!IMPORTANT]
> Ne felejtse el áttekinteni az egyszerű szolgáltatás követelményeit, amelyet a cikkben korábban ismertettünk.
>

```console
az login

az account set --subscription="mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Ez a következőhöz hasonló kimenetet ad vissza (itt kivonatosan látható):

![Egyszerű szolgáltatás létrehozása](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

A fürt üzembe helyezéséhez használt egyszerű szolgáltatás paraméterek, az **ügyfél-azonosító** (`appId`) és a **titkos ügyfélkulcs** (`password`), ki vannak emelve.


Erősítse meg az egyszerű szolgáltatást a következő parancs futtatásával egy új felületen, helyettesítve a `appId`, a `password`, és a `tenant`:

```console 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Néhány fontos megjegyzés


* Amikor megadja az egyszerű szolgáltatás **ügyfél-azonosítóját**, használhatja a `appId` értékét (a cikkben látottaknak megfelelően) vagy a megfelelő egyszerű szolgáltatást `name` (például        `https://www.contoso.org/example`).

* Ha az `az acs create` parancsot használja az egyszerű szolgáltatás automatikus létrehozására, az egyszerű szolgáltatás hitelesítő adatai a ~/.azure/acsServicePrincipal.json fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.

* A Kubernetes-fürt mester és csomópont virtuális gépein az egyszerű szolgáltatás hitelesítő adatai az /etc/kubernetes/azure.json fájlban lesznek tárolva.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Kubernetes-szel](container-service-kubernetes-walkthrough.md) a tárolószolgáltatás-fürtben.



<!--HONumber=Jan17_HO1-->


