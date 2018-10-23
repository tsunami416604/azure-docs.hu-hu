---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: j-martens
ms.service: machine-learning
ms.author: jmartens
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/27/2018
ms.openlocfilehash: 300b4a3e5c6c52f1c09f8e70a72f08406861a5d1
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400303"
---
>[!IMPORTANT]
>A létrehozott erőforrások előfeltételként is használhatók az Azure Machine Learning szolgáltatás más oktatóanyagaiban vagy útmutatóiban. 


Ha nem tervezi használni az itt létrehozott erőforrásokat, törölje őket, nehogy többletköltségekkel kelljen számolnia.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.
 
   ![Törlés az Azure Portalon](./media/aml-delete-resource-group/delete-resources.png)

1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés** elemet.

   Ha egy hibaüzenet jelenik meg, amely arra figyelmezteti, hogy „az erőforrás csak a beágyazott erőforrások törlését követően törölhető”, akkor először a beágyazott erőforrásokat kell törölnie. A beágyazott erőforrások törléséről [ebben a hibaelhárítással foglalkozó szakaszban](../articles/machine-learning/desktop-workbench/known-issues-and-troubleshooting-guide.md#cant-delete-experimentation-account) talál további információt. 
