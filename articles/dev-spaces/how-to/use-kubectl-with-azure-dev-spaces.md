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
description: Gyors Kubernetes fejlesztése a tárolók és mikroszolgáltatások létrehozására az Azure-on
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
manager: douge
ms.openlocfilehash: 38a433a14ab977fb56a8331a057d27241f1d9783
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Az Azure fejlesztői címtér kubectl használata

A Kubernetes fürtön belül az Azure fejlesztői címtér végezheti el, és a meglévő Kubernetes eszközök, például `kubectl`.

Futó `azds resource create` vagy `azds resource select` automatikusan felveszi a `kubectl` konfigurációs környezet meg, így kubectl Azure fejlesztői szóközöket Kubernetes fürtjét már kell csatlakoztatni. Példák:
- Erősítse meg az aktuális környezetben: `kubectl config current-context`
- Az összes rendelkezésre álló környezetek felsorolása: `kubectl config get-contexts`. 
- Módosítsa a környezetben: `kubectl config use-context <context-name>`
- A Kubernetes irányítópult megtekintése: futtassa `kubectl proxy`, majd nyissa meg a böngészőt, hogy a cím, a parancs bocsát ki (hozzáfűzése `/ui` navigáljon a Kubernetes irányítópult URL-címet).
- A futó szolgáltatások az alapértelmezett Azure fejlesztői szóközöket területen nevű lista *alapértelmezett*: `kubectl get services --namespace=default`

