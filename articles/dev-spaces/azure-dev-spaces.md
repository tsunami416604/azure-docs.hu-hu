---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: 14b51cc2ad2e8e0f294e5e73e542001e30d21c9d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918211"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Az Azure Dev Spaces gyors, iteratív Kubernetes-fejlesztési élményt biztosít a csapatok számára. A fejlesztői gép minimális szintű beállítása mellett iteratív módon futtathat tárolókat és végezhet azokon hibakeresést közvetlenül az Azure Kubernetes Service-ben (AKS). Fejleszthet Windows, Mac vagy Linux rendszereken is, olyan közismert eszközökkel, mint a Visual Studio, a Visual Studio Code vagy a parancssor.

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hogyan egyszerűsíti le az Azure Dev Spaces a Kubernetes fejlesztést 

Az Azure Dev Spaces a következő módokon segíti hatékonyabbá tenni a fejlesztőcsoportok Kubernetesen végzett munkáját:
- Minimalizálja az egyes csapattagok által a helyi fejlesztői gépen elvégzendő beállítások mértékét, és lehetővé teszi a munkavégzést közvetlenül az AKS-ben, amely egy felügyelt Kubernetes-fürt az Azure-ban.
- Gyorsan elvégzi a kódok iterációját és hibakeresését közvetlenül a Kubernetesen a Visual Studio 2017 vagy a Visual Studio Code használatával.
- A Dockerhez és a Kuberneteshez kóddal való konfigurálási objektumokat hoz létre, amelyek a fejlesztéstől egészen az éles környezetig használhatók. 
- Megoszt egy felügyelt Kubernetes-fürtöt a csapat tagjai között a közös munka értekében. A kód elkülönített fejlesztése, illetve végpontok közötti tesztelés más összetevőkkel a függőségek replikálása vagy utánzása nélkül.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>Lásd még:

[Azure Kubernetes Service](/azure/aks)