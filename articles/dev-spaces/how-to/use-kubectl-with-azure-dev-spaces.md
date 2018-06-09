---
title: Kubectl használata Azure fejlesztői szóközöket |} Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: 3dc0dd4b571f716bcabb67c4cbef1ea6d762eb94
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248658"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Az Azure fejlesztői címtér kubectl használata

A Kubernetes fürtön belül az Azure fejlesztői címtér végezheti el, és a meglévő Kubernetes eszközök, például `kubectl`.

Futtató `az aks use-dev-spaces` parancs automatikusan felveszi a `kubectl` konfigurációs környezet meg, így kubectl Azure fejlesztői szóközöket Kubernetes fürtjét már kell csatlakoztatni. Példák:
- Erősítse meg az aktuális környezetben: `kubectl config current-context`
- Az összes rendelkezésre álló környezetek felsorolása: `kubectl config get-contexts`. 
- Módosítsa a környezetben: `kubectl config use-context <context-name>`
- A Kubernetes irányítópult megtekintése: futtassa `kubectl proxy`, majd nyissa meg a böngészőt, hogy a cím, a parancs bocsát ki (hozzáfűzése `/ui` navigáljon a Kubernetes irányítópult URL-címet).
- A futó szolgáltatások az alapértelmezett Azure fejlesztői szóközöket területen nevű lista *alapértelmezett*: `kubectl get services --namespace=default`

