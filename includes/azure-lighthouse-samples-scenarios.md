---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204571"
---
Ezek a minták több-bérlős felügyeleti forgatókönyvek esetében végrehajtható feladatokat mutatnak be.

| **Sablon** | **Leírás** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Kulcstárolót létesít az ügyfél bérlőjén, és hozzáférési szabályzatokat hoz létre.
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Tárfiókokat helyez üzembe két különböző erőforráscsoportban.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Azure-felügyeleti szolgáltatásokat hoz létre, összekapcsolja őket, és további megoldásokat telepít. A teljes körű üzembe helyezéshez használja az **rgWithAzureMgmt.json** sablont. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Engedélyezi és beállítja az Azure Security Centert a célzott Azure-előfizetésen belül. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Üzembe helyezi és engedélyezi az Azure Sentinelt egy meglévő Log Analytics-munkaterületen egy delegált előfizetésben. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Ezekkel a sablonokkal Log Analytics virtuálisgép-bővítményeket helyezhet üzembe windowsos és linuxos virtuális gépein, valamint csatlakoztathatja őket a kijelölt Log Analytics-munkaterülethez |
