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
ms.openlocfilehash: dcd2854585e2c776f361aa4718af6b99d8cbb593
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47001999"
---
>[!IMPORTANT]
>A létrehozott erőforrások előfeltételként is használhatók más Azure Machine Learning-oktatóanyagokban vagy -útmutatókban. 


Ha már nem kívánja használni a jelen rövid útmutatóban létrehozott erőforrásokat, akkor törölje őket, nehogy többletköltségekkel kelljen számolnia.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.  
 
   ![Törlés az Azure Portalon](./media/aml-delete-resource-group/delete-resources.png)

1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés** elemet.

   Ha egy hibaüzenet jelenik meg, amely arra figyelmezteti, hogy „az erőforrás csak a beágyazott erőforrások törlését követően törölhető”, akkor először a beágyazott erőforrásokat kell törölnie. További részleteket [ebben a hibaelhárítási szakaszban talál](../articles/machine-learning/desktop-workbench/known-issues-and-troubleshooting-guide.md#cant-delete-experimentation-account). 