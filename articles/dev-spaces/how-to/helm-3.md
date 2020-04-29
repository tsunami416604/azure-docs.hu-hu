---
title: Azure Dev Spaces-fürt konfigurálása a Helm 3 használatára (előzetes verzió)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Ismerje meg, hogyan konfigurálhatja a dev Spaces-fürtöt a Helm 3 használatára
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79454295"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Azure Dev Spaces-fürt konfigurálása a Helm 3 használatára (előzetes verzió)

Az Azure dev-helyek alapértelmezés szerint a Helm 2-t használják a felhasználói szolgáltatások telepítéséhez az AK-fürtön található fejlesztői tárhelyeken. Engedélyezheti az Azure dev Spaces használatát a Helm 3 helyett, ha a felhasználói szolgáltatásokat a fejlesztői tárhelyeken telepíti. Függetlenül attól, hogy a Helm Azure dev Spaces melyik verzióját használja a felhasználói szolgáltatások telepítésére, továbbra is használhatja a 2. vagy a 3. ügyfelet a saját kiadásainak ugyanazon a fürtön való kezelésére.

A Helm 3 engedélyezésekor az Azure dev Spaces különböző módon viselkedik, amikor a felhasználói szolgáltatásokat a következő módokon telepíti a fejlesztői tárhelyekre:

* A kormányrúd már nincs telepítve a fürtön a *azds* névtérben.
* A Helm tárolja a kiadási információkat abban a névtérben, ahol a szolgáltatás telepítve van a *azds* névtér helyett.
* A Helm 3 kiadási adatai abban a névtérben maradnak, ahol a szolgáltatás a vezérlő törlése után települ.
* Az Azure dev Spaces által kezelt bármely kiadást közvetlenül használhatja a-fürtön a Helm 3 ügyféllel.

Ebből az útmutatóból megtudhatja, hogyan engedélyezheti a Helm 3 for Azure dev Spaces szolgáltatást a felhasználói szolgáltatások fejlesztői tárhelyeken való telepítéséhez.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* Az [Azure CLI telepítve van][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Regisztrálja a Helm3Preview előzetes verziójának funkcióját

Ha engedélyezni szeretné az Azure dev Spaces számára a 3. Helm használatát a felhasználói szolgáltatások fejlesztői tárhelyeken való telepítéséhez, először engedélyezze az előfizetéshez tartozó *Helm3Preview* funkció jelzőjét az az *Feature Register* paranccsal:

> [!WARNING]
> A *Helm3Preview* funkció jelzőjén az Azure dev Spaces szolgáltatást ENGEDÉLYEZő AK-fürtök ezt az előnézeti élményt fogják használni. Ha továbbra is engedélyezni szeretné a teljes mértékben támogatott Azure dev-helyeket az AK-fürtökön, ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Az előzetes verzió funkcióinak teszteléséhez használjon külön tesztelési vagy fejlesztési Azure-előfizetést.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

A regisztráció elvégzése néhány percet vesz igénybe. A regisztrációs állapotot az az *Feature show* paranccsal tekintheti meg:

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Ha az *állapot* *regisztrálva*van, frissítse a *Microsoft. DevSpaces* regisztrációját az *az Provider Register*paranccsal:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Korlátozások

A szolgáltatás előzetes verziójában a következő korlátozások érvényesek:

* A szolgáltatás nem használható a meglévő számítási feladatokkal rendelkező AK-fürtökön. Létre kell hoznia egy új AK-fürtöt.

## <a name="create-your-cluster"></a>A fürt létrehozása

Hozzon létre egy új AK-fürtöt egy olyan régióban, amely rendelkezik ezzel az előzetes verziójú funkcióval. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MYAKS*nevű új AK-fürtöt:

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Az Azure dev Spaces használatának engedélyezése

A *use-dev-Spaces* paranccsal engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. Az alábbi parancs lehetővé teszi a dev Spaces használatát a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy alapértelmezett fejlesztői helyet.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Ellenőrizze, hogy a dev Spaces fut-e a Helm 3

Győződjön meg arról, hogy a kormányrúd nem fut a *azds* névtérben lévő központi telepítések listázásával:

```cmd
kubectl get deployment -n azds
```

Győződjön meg róla, hogy az azds *-* névtérben nem fut a rendszer. Például:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md