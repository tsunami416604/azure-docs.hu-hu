---
title: Kubectl használata az Azure fejlesztési tárolóhelyek |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: b522ab9177d963f65ac6ea75538ddc46331875da
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706789"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl használata egy Azure-fejlesztési terület

Elérheti a Kubernetes-fürt az Azure Dev-Címtéren belüli és a meglévő eszközök használata a Kubernetes, például `kubectl`.

Futó `az aks use-dev-spaces` parancs automatikusan hozzáad egy `kubectl` konfigurációs környezetben, így ez a kubectl már csatlakoztatható az Azure fejlesztői, szóközök Kubernetes-fürthöz. Példák:
- Erősítse meg az aktuális környezetet: `kubectl config current-context`
- Az összes rendelkezésre álló környezetek listája: `kubectl config get-contexts`. 
- Környezet módosítása: `kubectl config use-context <context-name>`
- A Kubernetes-irányítópult megtekintése: futtassa `kubectl proxy`, majd nyissa meg a böngészőben a cím, amelyet ez a parancs bocsát ki a (hozzáfűzése `/ui` és a Kubernetes-irányítópult arra az URL-címet).
- A futó szolgáltatások az alapértelmezett Azure fejlesztési tárolóhelyek térben nevű lista *alapértelmezett*: `kubectl get services --namespace=default`

