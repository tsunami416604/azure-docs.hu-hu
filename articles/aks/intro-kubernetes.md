---
title: "Bevezetés az Azure Container Service for Kubernetes használatába | Microsoft Docs"
description: "Az Azure Container Service for Kubernetes használatával egyszerűen helyezhetőek üzembe és felügyelhetőek a tárolóalapú alkalmazások az Azure-ban."
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, tárolók, mikroszolgáltatások, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 9fba9fdda3503ec80fede845466858825e3677a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Bevezetés az Azure Tárolószolgáltatás (AKS)

Az Azure tároló szolgáltatás (AKS) leegyszerűsíti létrehozásához, konfigurálásához és egy fürt indexelése alkalmazások futtatásához az előre konfigurált virtuális gépek kezeléséhez. Így lehetővé válik, hogy a meglévő ismereteit használja, vagy egy nagy és egyre növekvő közösségi szakértelmet vegyen igénybe a tárolóalapú alkalmazások Microsoft Azure-on történő üzembe helyezéséhez és kezeléséhez.

AKS használatával kihasználhatja az Azure, a vállalati szintű funkciók alkalmazás hordozhatóság Kubernetes és a Docker képformátum keresztül továbbra is megőrzésével.

## <a name="managed-kubernetes-in-azure"></a>Felügyelt Kubernetes az Azure-ban

AKS csökkenti az összetettséget és a működési munkaterhelés a Kubernetes fürt kezelése kiszervezésével nagy része, amely felelős az Azure-bA. Egy felhőszolgáltatás Kubernetes, Azure leírók kritikus feladatok állapotának figyelését és karbantartását, például. Ezenkívül kell fizetnie csak a az ügynök csomópontok belül a fürtökhöz, nem a főkiszolgálók. A felügyelt szolgáltatásként Kubernetes AKS biztosítja:

> [!div class="checklist"]
> * Automatikus Kubernetes verziófrissítések és javítás
> * Egyszerű fürtméretezés
> * Öngyógyító üzemeltetett vezérlő vezérlősík (főkiszolgálók)
> * Költségmegtakarításhoz – csak azért fizessen-ügynököt futtató alkalmazáskészlet csomópontok

Az Azure-ral a fürtben található csomópontok a AKS felügyeleti kezelése már nincs szüksége sok feladatok elvégzéséhez manuálisan, például a fürt frissítések. Azure kezeli a kritikus fontosságú karbantartási műveletek meg, mert AKS nem közvetlen hozzáférést biztosít (például az SSH) a fürthöz.

## <a name="using-azure-container-service-aks"></a>Azure Tárolószolgáltatás (AKS) használatával
AKS célja egy tároló üzemeltetési környezet nyílt forráskódú eszközök és az ügyfelek között népszerű manapság technológiák használatával adja meg. E célból elérhetővé tesszük a standard Kubernetes API-végpontokat. A standard végpontokkal bármely olyan szoftvert használhat, amely képes a Kubernetes-fürtökkel folytatott kommunikációra. Például választhatja a következőket: [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) vagy [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Azure tároló szolgáltatás (AKS) használatával Kubernetes fürt létrehozása
A AKS használatának megkezdéséhez AKS-fürt üzembe helyezése a a [Azure CLI](./kubernetes-walkthrough.md) vagy a portálon (keresése a piactéren **Azure Tárolószolgáltatás**). Amennyiben Ön tapasztalt felhasználó, és átfogóbb vezérlésre van szüksége az Azure Resource Manager-sablonok fölött, a nyílt forráskódú [acs-engine](https://github.com/Azure/acs-engine) projekt segítségével felépíthet egy saját egyéni Kubernetes-fürtöt, és üzembe helyezheti azt az `az` parancssori felület használatával.

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

## <a name="videos"></a>Videók

Azure Tárolószolgáltatás (AKS) – péntek, Azure október 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Eszközök fejlesztéséhez és Kubernetes - Azure OpenDev. június 2017 alkalmazások telepítéséhez:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

További információ a központi telepítésére és felügyeletére AKS rendelkező a AKS gyors üzembe helyezés.

> [!div class="nextstepaction"]
> [AKS oktatóanyag](./kubernetes-walkthrough.md)