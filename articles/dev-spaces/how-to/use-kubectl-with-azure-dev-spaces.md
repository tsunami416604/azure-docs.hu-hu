---
title: Kubectl használata az Azure dev spaces-szel
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Megtudhatja, hogy miként használhatja a kubectl-parancsokat egy fejlesztői területen belül egy Azure Kubernetes-szolgáltatásfürtön, és engedélyezve van az Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438369"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl használata Azure dev-térrel

A Kubernetes-fürt egy Azure Dev Space-en belül érhető el, és használhatja a meglévő Kubernetes-eszközöket, például `kubectl`a.

A `az aks use-dev-spaces` futó parancs `kubectl` automatikusan hozzáad egy konfigurációs környezetet, így a kubectl már csatlakoztatva van az Azure Dev Spaces Kubernetes-fürthöz. Példák:
- Az aktuális környezet megerősítése:`kubectl config current-context`
- Az összes rendelkezésre `kubectl config get-contexts`álló környezet felsorolása: . 
- Környezet módosítása:`kubectl config use-context <context-name>`
- Tekintse meg a Kubernetes irányítópultját: futtassa a `kubectl proxy`futtassa, `/ui` majd nyissa meg a böngészőt arra a címre, amelyet ez a parancs kibocsát (az URL-címhez fűzve a Kubernetes-irányítópultra való navigáláshoz).
- A futó szolgáltatások listázása az alapértelmezett Azure Dev Spaces *területen, alapértelmezettnek*nevezve:`kubectl get services --namespace=default`

