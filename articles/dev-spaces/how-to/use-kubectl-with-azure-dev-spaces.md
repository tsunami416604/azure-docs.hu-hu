---
title: A kubectl használata az Azure dev Spaces használatával
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Ismerje meg, hogyan használhatja a kubectl-parancsokat egy Azure Kubernetes Service-fürt fejlesztői területén az Azure dev Spaces használatával
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438369"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl használata az Azure fejlesztői területtel

A Kubernetes-fürtöt egy Azure fejlesztői tárhelyen belül érheti el, és használhat meglévő Kubernetes-eszközöket (például `kubectl`).

`az aks use-dev-spaces` parancs futtatásakor a rendszer automatikusan hozzáad egy `kubectl` konfigurációs környezetet, így az kubectl-nek már csatlakoznia kell az Azure dev Spaces Kubernetes-fürthöz. Példák:
- Az aktuális környezet megerősítése: `kubectl config current-context`
- Az összes elérhető kontextus listázása: `kubectl config get-contexts`. 
- Változás kontextusa: `kubectl config use-context <context-name>`
- Tekintse meg a Kubernetes irányítópultját: futtassa `kubectl proxy`, majd nyissa meg a böngészőt arra a címre, amelyet a parancs bocsát ki (az URL-cím `/ui` hozzáfűzésével keresse meg a Kubernetes irányítópultot).
- A futó szolgáltatások listázása az alapértelmezett Azure dev Spaces Space *default (alapértelmezett*) nevű helyen: `kubectl get services --namespace=default`

