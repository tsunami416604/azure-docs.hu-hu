---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198758"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Az Azure Dev Spaces segíti felgyorsítani a Kubernetesen végzett fejlesztést. Az Azure Dev Spaces használatával teljes fejlesztési képességeket, például hibakeresést is hozzáad az Azure Kubernetes tárolókhoz, és iteratívan fejleszthet tárolókat a felhőben a már ismerős eszközök, például a VS Code, a Visual Studio vagy a parancssor használatával. Az Azure Dev Spaces különösen hasznos a csapatban való fejlesztéshez, ahol az egyéni kódágak elkülönítése a saját terükön kritikus részét képezi a fejlesztés életciklusának.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hogyan egyszerűsíti le az Azure Dev Spaces a Kubernetes fejlesztést 

Ez a megközelítés számos előnnyel jár:

* Az éles környezeteknek megfelelő, infrastruktúrát nem tartalmazó fejlesztési környezet, amely teljes hozzáférést biztosít a felhőalapú erőforrásokhoz.
* A Node.js és a .NET Core tárolók közvetlen hibaelhárítása a Kubernetesben a VS Code vagy a Visual Studio használatával. Minden más nyelv fejleszthető a parancssori felületen.
* Kubernetes-példány megosztása a fejlesztői csapatban a költségek csökkentése és az új csapattagok helyi gépi beállításainak minimalizálása érdekében.
* A kód elkülönített fejlesztése, illetve végpontok közötti tesztelés más összetevőkkel a függőségek replikálása vagy utánzása nélkül.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)