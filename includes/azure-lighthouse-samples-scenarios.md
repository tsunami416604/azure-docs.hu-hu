---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a5532b7a4574f3c2cc057255742c72bf032d180b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456720"
---
Ezek a minták több-bérlős felügyeleti forgatókönyvek esetében végrehajtható feladatokat mutatnak be.

| **Sablon** | **Leírás** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Tárfiókokat helyez üzembe két különböző erőforráscsoportban.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Azure-felügyeleti szolgáltatásokat hoz létre, összekapcsolja őket, és további megoldásokat telepít. A teljes körű üzembe helyezéshez használja az **rgWithAzureMgmt.json** sablont. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Engedélyezi és beállítja az Azure Security Centert a célzott Azure-előfizetésen belül. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Üzembe helyezi és engedélyezi az Azure Sentinelt egy meglévő Log Analytics-munkaterületen egy delegált előfizetésben. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Ezekkel a sablonokkal Log Analytics virtuálisgép-bővítményeket helyezhet üzembe windowsos és linuxos virtuális gépein, valamint csatlakoztathatja őket a kijelölt Log Analytics-munkaterülethez |
