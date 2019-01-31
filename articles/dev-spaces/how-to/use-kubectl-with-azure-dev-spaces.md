---
title: Kubectl használata az Azure fejlesztési tárolóhelyek |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: b0ceccc4f8b16c21e8a66a5f1b8cf0e7b6f47a4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469311"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl használata egy Azure-fejlesztési terület

Elérheti a Kubernetes-fürt az Azure Dev-Címtéren belüli és a meglévő eszközök használata a Kubernetes, például `kubectl`.

Futó `az aks use-dev-spaces` parancs automatikusan hozzáad egy `kubectl` konfigurációs környezetben, így ez a kubectl már csatlakoztatható az Azure fejlesztői, szóközök Kubernetes-fürthöz. Példák:
- Erősítse meg az aktuális környezetet: `kubectl config current-context`
- Az összes rendelkezésre álló környezetek listája: `kubectl config get-contexts`. 
- Környezet módosítása: `kubectl config use-context <context-name>`
- A Kubernetes-irányítópult megtekintése: futtassa `kubectl proxy`, majd nyissa meg a böngészőben a cím, amelyet ez a parancs bocsát ki a (hozzáfűzése `/ui` és a Kubernetes-irányítópult arra az URL-címet).
- A futó szolgáltatások az alapértelmezett Azure fejlesztési tárolóhelyek térben nevű lista *alapértelmezett*: `kubectl get services --namespace=default`

