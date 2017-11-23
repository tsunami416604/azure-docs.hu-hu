---
title: "Az Azure Kubernetes-fürthöz tartozó egyszerű szolgáltatás | Microsoft Docs"
description: "Kubernetes-fürthöz tartozó Azure Active Directory szolgáltatásnév létrehozása és felügyelete az AKS-ben"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: af27d01108cbfb3bd71023ffbce85f348abb0cfe
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Szolgáltatásnevek és az Azure Container Service (AKS)

Az AKS-fürtöknek szükségük van egy [Azure Active Directory-szolgáltatásnévre](../active-directory/develop/active-directory-application-objects.md) az Azure API-kkal való kommunikációhoz. Az egyszerű szolgáltatással dinamikusan kezelhet olyan erőforrásokat, mint a [felhasználó által meghatározott útvonalak](../virtual-network/virtual-networks-udr-overview.md) és a [4. rétegű Azure Load Balancer](../load-balancer/load-balancer-overview.md).

Ebben a cikkben különböző lehetőségeket talál arra, hogyan állíthat be egy szolgáltatásnevet a Kubernetes-fürtökhöz az AKS-ben.

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezeket még létre kell hoznia olvassa el az [AKS gyors útmutatóját](./kubernetes-walkthrough.md).

Azure AD szolgáltatásnév létrehozásához rendelkeznie kell alkalmazásregisztrációs engedéllyel az Azure AD-bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. Ha nem rendelkezik a szükséges engedélyekkel, lehet, hogy meg kell kérnie Azure AD- vagy előfizetés-rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy előzetesen létre kell hoznia egy szolgáltatásnevet a Kubernetes-fürthöz.

Emellett az Azure CLI 2.0.21-es vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió megkereséséhez futtassa a következő parancsot: az --version. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-sp-with-aks-cluster"></a>Szolgáltatásnév létrehozása AKS-fürttel

Mikor az `az aks create` paranccsal AKS-fürtöt helyez üzembe, lehetősége van automatikusan létrehozni egy szolgáltatásnevet.

A következő példa egy AKS-fürtöt hoz létre, és mivel nincs meglévő szolgáltatásnév megadva, a rendszer létrehoz egyet a fürt számára. A művelet végrehajtásához a fióknak rendelkeznie kell a szükséges jogosultságokkal a szolgáltatásnév létrehozásához.

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Meglévő szolgáltatásnév használata

Használhat egy meglévő Azure AD szolgáltatásnevet, vagy előre létrehozhat egyet az AKS-fürttel való használathoz. Ez hasznos, amikor az Azure Portalról telepít fürtöt, ahol meg kell adnia a szolgáltatásnév adatait.

Meglévő szolgáltatásnév használata esetén annak az alábbi követelményeknek kell megfelelnie:

- Hatókör: a fürt üzembe helyezéséhez használt előfizetés
- Szerepkör: Közreműködő
- Titkos ügyfélkód: egy jelszónak kell lennie

## <a name="pre-create-a-new-sp"></a>Új szolgáltatásnév előzetes létrehozása

A szolgáltatásnév Azure CLI felületen való létrehozásához használja az [az ad sp create-for-rbac]() parancsot.

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

A kimenet a következőkhöz hasonló. Jegyezze fel az `appId` és a `password` értékét. A rendszer ezeket az értékeket használja az AKS-fürtök létrehozása során.

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Meglévő szolgáltatásnév használata

Előre létrehozott szolgáltatásnév használatakor adja be az `appId` és a `password` értékét argumentumértékként az `az aks create` parancsba.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> ----client-secret <password>
```

Ha az Azure Portalról helyez üzembe AKS-fürtöt, ezeket az értékeket az AKS-fürt konfigurációs űrlapján adhatja meg.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

AKS és Azure AD szolgáltatásnevek használata esetén vegye figyelembe a következőket.

* A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.
* Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt egyszerű szolgáltatása társítható bármilyen érvényes Azure AD-alkalmazásnévhez (például: `https://www.contoso.org/example`). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.
* Amikor megadja az egyszerű szolgáltatás **ügyfél-azonosítóját**, használhatja az `appId` értékét (ahogyan az a cikkben látható) vagy a megfelelő egyszerű szolgáltatást `name` (például: `https://www.contoso.org/example`).
* A Kubernetes-fürt mester és csomópont virtuális gépein az egyszerű szolgáltatás hitelesítő adatai az /etc/kubernetes/azure.json fájlban lesznek tárolva.
* Ha az `az aks create` parancsot használja az egyszerű szolgáltatás automatikus létrehozásához, az egyszerű szolgáltatás hitelesítő adatai a ~/.azure/acsServicePrincipal.json fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.
* Ha az `az aks create` parancsot használja az egyszerű szolgáltatás automatikus létrehozásához, az egyszerű szolgáltatás hitelesíthető egy, ugyanabban az előfizetésben létrehozott [Azure Container Registryvel](../container-registry/container-registry-intro.md).

## <a name="next-steps"></a>Következő lépések

Az Azure Active Directory szolgáltatásnevekkel kapcsolatos további információért tekintse meg az Azure AD-alkalmazások dokumentációját.

> [!div class="nextstepaction"]
> [Alkalmazás- és szolgáltatásnév objektumok](../active-directory/develop/active-directory-application-objects.md)
