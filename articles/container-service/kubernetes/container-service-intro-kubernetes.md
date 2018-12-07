---
title: (ELAVULT) Bevezetés az Azure Container Service for kubernetes használatába
description: Az Azure Container Service for Kubernetes használatával egyszerűen helyezhetőek üzembe és felügyelhetőek a tárolóalapú alkalmazások az Azure-ban.
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: e00ac57cc36b3331cfb847ecedc6c75132cdeb6b
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999177"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>(ELAVULT) Bevezetés az Azure Container Service for kubernetes használatába

> [!TIP]
> Ez a cikk, amely a frissített verziót használja Azure Kubernetes Service-ben, lásd: [Azure Kubernetes Service (AKS)-áttekintés](../../aks/intro-kubernetes.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Az Azure Container Service for Kubernetes elősegíti a virtuális gépek egy olyan fürtjének egyszerűbb létrehozását, konfigurálását és kezelését, amely tárolóalapú alkalmazások futtatására lett konfigurálva. Így lehetővé válik, hogy a meglévő ismereteit használja, vagy egy nagy és egyre növekvő közösségi szakértelmet vegyen igénybe a tárolóalapú alkalmazások Microsoft Azure-on történő üzembe helyezéséhez és kezeléséhez.

Az Azure Container Service használatával igénybe veheti az Azure nagyvállalati szintű funkcióit, miközben továbbra is fenntartja az alkalmazás-hordozhatóságot a Kubernetes és a Docker rendszerképformátuma révén.

## <a name="using-azure-container-service-for-kubernetes"></a>Az Azure Container Service for Kubernetes használata
Célunk az Azure Container Service keretében az, hogy a manapság az ügyfelek körében népszerű nyílt forráskódú eszközök és technológiák használatával nyújtsunk tárolóüzemeltetési környezetet. E célból elérhetővé tesszük a standard Kubernetes API-végpontokat. A standard végpontokkal bármely olyan szoftvert használhat, amely képes a Kubernetes-fürtökkel folytatott kommunikációra. Például választhatja a következőket: [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) vagy [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Kubernetes-fürt létrehozása az Azure Container Service használatával
Az Azure Container Service használatához üzembe kell helyeznie egy Azure Container Service-fürtöt az [Azure CLI](container-service-kubernetes-walkthrough.md) használatával vagy a portálon keresztül (keressen a piactéren az **Azure Container Service** kifejezésre). Amennyiben Ön tapasztalt felhasználó, és átfogóbb vezérlésre van szüksége az Azure Resource Manager-sablonok fölött, a nyílt forráskódú [acs-engine](https://github.com/Azure/acs-engine) projekt segítségével felépíthet egy saját egyéni Kubernetes-fürtöt, és üzembe helyezheti azt az `az` parancssori felület használatával.

### <a name="using-kubernetes"></a>A Kubernetes használata
A Kubernetes automatizálja a tárolóalapú alkalmazások üzembe helyezését, méretezését és felügyeletét. Többek között a következő funkciókat tartalmazza:
* Automatikus bin-csomagolás
* Önjavítás
* Vízszintes méretezés
* Szolgáltatásészlelés és terheléselosztás
* Automatizált kibocsátások és visszaállítások
* Titkos kódok és konfigurációk kezelése
* Tárolóvezénylés
* Kötegelt végrehajtás

Az Azure Container Service szolgáltatással üzembe helyezett Kubernetes architekturális diagramja:

![Kubernetes használatához konfigurált Azure Container Service.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Videók

A Kubernetes támogatása az Azure Container Service szolgáltatásban (Azure Friday, 2017. január):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Eszközök az alkalmazások fejlesztéséhez és üzembe helyezéséhez a Kubernetes szolgáltatásban (Azure OpenDev, 2017. június):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>További lépések

Tekintse át a [Kubernetes rövid útmutatóját](container-service-kubernetes-walkthrough.md), hogy már ma el tudja kezdeni az Azure Container Service használatát.
