---
title: Biztonsági riasztások környezetekhez az Azure DevTest Labs-ben
description: Ez a cikk bemutatja, hogyan tekintheti meg a devtest labs környezetében egy környezet biztonsági riasztásait, és hogyan kell végrehajtani a megfelelő műveletet.
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
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588705"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Biztonsági riasztások környezetekhez az Azure DevTest Labs-ben
Laborfelhasználóként most már megtekintheti az Azure Security Center riasztásait a laborkörnyezetben. A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek. [További információ az Azure Security Center biztonsági riasztásairól.](../security-center//security-center-alerts-overview.md)  


## <a name="prerequisites"></a>Előfeltételek
Jelenleg csak a platformra vonatkozó biztonsági riasztásokat tekintheti meg a laborban üzembe helyezett szolgáltatás (PaaS) környezetként. A szolgáltatás teszteléséhez vagy használatához [telepítsen egy környezetet a laborba.](devtest-lab-create-environment-from-arm.md) 

## <a name="view-security-alerts-for-an-environment"></a>Környezetbiztonsági riasztások megtekintése

1. A tesztkörnyezet kezdőlapján válassza a bal oldali menü **Biztonsági riasztások elemét.** Meg kell jelennie a biztonsági riasztások (magas, közepes és alacsony) száma. További információ a [riasztások besorolásáról.](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)

    ![Biztonsági riasztások – áttekintés](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Kattintson a jobb gombbal az utolsó oszlophárom pontjára (...), és válassza a **Biztonsági riasztások megtekintése parancsot.** 

    ![Biztonsági riasztások megtekintése](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. További részleteket láthat a riasztásokról és a tanácsadói javaslatokról. További információ a [biztonsági riasztások kezeléséről és az azokra való megválaszolásról az Azure Security Centerben.](../security-center/security-center-managing-and-responding-alerts.md)

    ![Biztonsági riasztások megtekintése](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>További lépések
A környezetekről az alábbi cikkekben olvashat bővebben:

- [Többvm-es környezetek és PaaS-erőforrások létrehozása az Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata](devtest-lab-configure-use-public-environments.md)
