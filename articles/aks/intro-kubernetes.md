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
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: a8ac18464d0efcc0db96e1667f18f2f853208573
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Bevezetés az Azure Tárolószolgáltatás (AKS)

Az Azure tároló szolgáltatás (AKS) leegyszerűsíti létrehozásához, konfigurálásához és egy fürt indexelése alkalmazások futtatásához az előre konfigurált virtuális gépek kezeléséhez. Így lehetővé válik, hogy a meglévő ismereteit használja, vagy egy nagy és egyre növekvő közösségi szakértelmet vegyen igénybe a tárolóalapú alkalmazások Microsoft Azure-on történő üzembe helyezéséhez és kezeléséhez.

AKS használatával kihasználhatja az Azure, a vállalati szintű funkciók alkalmazás hordozhatóság Kubernetes és a Docker képformátum keresztül továbbra is megőrzésével.

## <a name="using-azure-container-service-aks"></a>Azure Tárolószolgáltatás (AKS) használatával
A AKS célunk adjon meg egy tároló üzemeltetési környezet nyílt forrású eszközök és technológiák manapság népszerű, az ügyfelek között. E célból elérhetővé tesszük a standard Kubernetes API-végpontokat. A standard végpontokkal bármely olyan szoftvert használhat, amely képes a Kubernetes-fürtökkel folytatott kommunikációra. Például választhatja a következőket: [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) vagy [draft](https://github.com/Azure/draft).

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