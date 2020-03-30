---
title: Az Azure dev spaces-fürt beállítása a Helm 3 használatára (előzetes verzió)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Ismerje meg, hogyan konfigurálhatja a fejlesztői tereket a Helm 3 használatára
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454295"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Az Azure dev spaces-fürt beállítása a Helm 3 használatára (előzetes verzió)

Az Azure dev spaces a Helm 2 alapértelmezés szerint a felhasználói szolgáltatások telepítése a fejlesztési terek az AKS-fürtön. Engedélyezheti, hogy az Azure Dev Spaces helm 3-at használjon a Helm 2 felhasználói szolgáltatások fejlesztési terekben való telepítése helyett. Függetlenül attól, hogy a Helm Azure Dev Spaces a felhasználói szolgáltatások telepítéséhez használt, továbbra is használhatja a Helm 2 vagy 3 ügyfél kezelheti a saját kiadások ugyanazon a fürtön.

Ha engedélyezi a Helm 3 szolgáltatást, az Azure Dev Spaces másképpen viselkedik, amikor a felhasználói szolgáltatásokat fejlesztői terekbe telepíti a következő módokon:

* A talajművelés immár nincs telepítve a fürtre az *azds* névtérben.
* A Helm a kiadási információkat abban a névtérben tárolja, ahol a szolgáltatás telepítve van az *azds* névtér helyett.
* A Helm 3 kiadási információi abban a névtérben maradnak, ahol a szolgáltatás telepítve van a vezérlő törlése után.
* Közvetlenül kommunikálhat az Azure Dev Spaces által a fürtön a Helm 3 ügyfél használatával kezelt bármely kiadással.

Ebben az útmutatóban megtudhatja, hogyan engedélyezheti a Helm 3 az Azure Dev Spaces felhasználói szolgáltatások telepítését a fejlesztői terekben.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Az Azure CLI telepítve van.][azure-cli]

### <a name="register-the-helm3preview-preview-feature"></a>A Helm3Preview előzetes verzió funkció regisztrálása

Ha engedélyezni szeretné, hogy az Azure Dev Spaces a Helm 3 szolgáltatást használja a felhasználói szolgáltatások fejlesztési terekben való telepítéséhez, először engedélyezze a *Helm3Preview* szolgáltatásjelzőt az előfizetésen az *az szolgáltatásregiszter* parancs használatával:

> [!WARNING]
> A *Helm3Preview* funkciójelzővel engedélyező Bármely AKS-fürt, amelyen engedélyezi az Azure Dev Spaces-t, ezt az előnézeti élményt fogja használni. Ha továbbra is engedélyezni szeretné a teljes mértékben támogatott Azure Dev Spaces-t Az AKS-fürtökön, ne engedélyezze az előzetes verziójú funkciókat az éles előfizetéseken. Használjon külön teszt- vagy fejlesztési Azure-előfizetést az előzetes verziójú funkciók teszteléséhez.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

A regisztráció befejezéséhez néhány percet is igénybe vesz. Ellenőrizze a regisztrációs állapotot az *az szolgáltatásmegjelenítés* paranccsal:

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Ha az *állapot* *regisztrálva*van, frissítse a *Microsoft.DevSpaces* regisztrációját az *provider register*használatával:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek, amíg ez a funkció előzetes verzióban érhető el:

* Ez a szolgáltatás nem használható meglévő munkaterheléssel rendelkező AKS-fürtökön. Új AKS-fürtöt kell létrehoznia.

## <a name="create-your-cluster"></a>A fürt létrehozása

Hozzon létre egy új AKS-fürtöt egy olyan régióban, amely rendelkezik ezzel az előnézeti funkcióval. Az alábbi parancsok létrehoznak egy *SamiresourceGroup* nevű erőforráscsoportot és egy Új AKS-fürtöt, amelynek neve *MyAKS:*

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Azure dev-szóközök engedélyezése

Használja a *használat-dev-spaces* paranccsal engedélyezheti a fejlesztői szóközöket az AKS-fürtön, és kövesse az utasításokat. Az alábbi parancs engedélyezi a Fejlesztői szóközöket a *MyResourceGroup* csoport *MyAKS* fürtjében, és létrehoz egy alapértelmezett fejlesztői területet.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Ellenőrizze, hogy a fejlesztői terek helm 3-at futtatnak-e

Ellenőrizze, hogy a talajművelésnem fut-e az *azds* névtérben lévő központi telepítések listázásával:

```cmd
kubectl get deployment -n azds
```

Ellenőrizze, hogy a *talajművelés-üzembe helyezés* nem fut-e az azds névtérben. Példa:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Csapatfejlesztés az Azure Dev Spaces-ben][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md