---
title: A kubectl használata az Azure dev Spaces használatával
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Ismerje meg, hogyan használhatja a kubectl-parancsokat egy Azure Kubernetes Service-fürt fejlesztői területén az Azure dev Spaces használatával
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438369"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl használata az Azure fejlesztői területtel

A Kubernetes-fürtöt egy Azure fejlesztői tárhelyen belül érheti el, és használhatja a meglévő `kubectl`Kubernetes-eszközöket, például a következőt:.

A `az aks use-dev-spaces` futó parancs automatikusan hozzáadja a `kubectl` konfigurációs környezetet, így a kubectl már csatlakoznia kell az Azure dev Spaces Kubernetes-fürthöz. Példák:
- Az aktuális környezet megerősítése:`kubectl config current-context`
- Az összes elérhető kontextus listázása: `kubectl config get-contexts`. 
- Környezet módosítása:`kubectl config use-context <context-name>`
- Tekintse meg a Kubernetes irányítópultját: futtassa `kubectl proxy`a parancsot, majd nyissa meg a böngészőt a `/ui` parancs által kibocsátott címre (fűzze hozzá az URL-címhez, és keresse meg a Kubernetes irányítópultját).
- A futó szolgáltatások listázása az alapértelmezett Azure dev Spaces Space *default (alapértelmezett*) nevű helyen:`kubectl get services --namespace=default`

