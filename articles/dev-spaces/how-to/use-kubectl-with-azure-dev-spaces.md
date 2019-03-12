---
title: Kubectl használata az Azure fejlesztési tárolóhelyek
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773265"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl használata egy Azure-fejlesztési terület

Elérheti a Kubernetes-fürt az Azure Dev-Címtéren belüli és a meglévő eszközök használata a Kubernetes, például `kubectl`.

Futó `az aks use-dev-spaces` parancs automatikusan hozzáad egy `kubectl` konfigurációs környezetben, így ez a kubectl már csatlakoztatható az Azure fejlesztői, szóközök Kubernetes-fürthöz. Példák:
- Erősítse meg az aktuális környezetet: `kubectl config current-context`
- Az összes rendelkezésre álló környezetek listája: `kubectl config get-contexts`. 
- Környezet módosítása: `kubectl config use-context <context-name>`
- A Kubernetes-irányítópult megtekintése: futtassa `kubectl proxy`, majd nyissa meg a böngészőben a cím, amelyet ez a parancs bocsát ki a (hozzáfűzése `/ui` és a Kubernetes-irányítópult arra az URL-címet).
- A futó szolgáltatások az alapértelmezett Azure fejlesztési tárolóhelyek térben nevű lista *alapértelmezett*: `kubectl get services --namespace=default`

