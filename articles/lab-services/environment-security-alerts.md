---
title: Biztonsági riasztások a Azure DevTest Labs környezetekben
description: Ebből a cikkből megtudhatja, hogyan tekintheti meg a környezetek biztonsági riasztásait a DevTest Labs szolgáltatásban, és megteheti a megfelelő lépéseket.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992234"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Biztonsági riasztások a Azure DevTest Labs környezetekben
Ez a cikk bemutatja, hogyan tekintheti meg Azure DevTest Labs környezetek biztonsági riasztásait. 

## <a name="prerequisites"></a>Előfeltételek
A biztonsági riasztásokat jelenleg csak a laborban üzembe helyezett környezetek esetében tekintheti meg. A szolgáltatás teszteléséhez vagy használatához telepítsen egy környezetet a laborba. 

## <a name="view-security-alerts-for-an-environment"></a>Környezet biztonsági értesítéseinek megtekintése

1. A labor kezdőlapján válassza a bal oldali menüben a **biztonsági riasztások** elemet. Ekkor meg kell jelennie a biztonsági riasztások számának (magas, közepes és alacsony).

    ![Biztonsági riasztások – áttekintés](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Kattintson a jobb gombbal az utolsó oszlopban található három pontra (...), majd válassza a **biztonsági riasztások megtekintése**lehetőséget. 

    ![Biztonsági riasztások megtekintése](./media/environment-security-alerts/view-security-alerts-menu.png)
3. A riasztásokkal és az Advisor-javaslatokkal kapcsolatban további részleteket talál. 

    ![Biztonsági riasztások megtekintése](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Következő lépések
A környezetekkel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Több virtuális gépre kiterjedő környezetek és Pásti-erőforrások létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata](devtest-lab-configure-use-public-environments.md)
