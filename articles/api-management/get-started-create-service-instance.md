---
title: "Hozzon létre egy Azure API Management példányt |} Microsoft Docs"
description: "Ez az oktatóanyag hozzon létre egy új Azure API Management-példányt kövesse."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Hozzon létre egy új Azure API Management szolgáltatáspéldány

Ez az oktatóanyag leírja a lépéseket egy új API felügyeleti példány történő létrehozásához a [Azure-portálon](https://portal.azure.com/).

## <a name="prerequisites"></a>Előfeltételek

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>Új szolgáltatás létrehozása

1. Az a [Azure-portálon](https://portal.azure.com/), jelölje be **új** > **vállalati integrációs** > **API management**.

    Választhatja a **új**, típus `API management` a keresőmezőbe, és nyomja le az ENTER billentyűt. Kattintson a **Create** (Létrehozás) gombra.

2. Az a **API-kezelés szolgáltatás** ablakban adja meg egy egyedi **neve** az API Management szolgáltatás. Ez a név később már nem módosítható.

    > [!TIP]
    > Szolgáltatásnév létrehozásához egy alapértelmezett tartomány nevét formájában használatos *{name} .azure-api.net.* Ha azt szeretné, egy egyéni tartománynév használatára, lásd: [az egyéni tartománynév konfigurálása](configure-custom-domain.md). <br/>
    > Szolgáltatásnév segítségével tekintse meg a szolgáltatás és a megfelelő Azure-erőforrás.

5. Válassza ki a **előfizetés** között az Azure-előfizetések rendelkezik hozzáféréssel.
6. A **Resource Group** (Erőforráscsoport) résznél válasszon egy új vagy meglévő erőforrást.  Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információkat [itt](../azure-resource-manager/resource-group-overview.md#resource-groups) talál.
7. A **hely**, válassza ki a földrajzi régiót, ahol az API Management szolgáltatás létrehozása. Csak az elérhető API-kezelés szolgáltatás régiók jelennek meg a legördülő listában. 
9. Adjon meg egy **szervezetnév**. Ez a név szerepel a helyek számát. Például a címe a fejlesztői portálján és a küldőt az értesítési e-mailek.
10. A **rendszergazdai e-mail**, állítsa be e-mail címét, mely minden az értesítések a **API Management** küldi.
11. A **tarifacsomag**, beállíthatja **fejlesztői** réteg értékelni a szolgáltatást. A réteg van nem éles környezetben való használathoz. Az API Management szinteket méretezésével kapcsolatos további információkért lásd: [frissítése és méretezése](upgrade-and-scale.md).
12. Válassza a **Létrehozás** elemet.

    > [!TIP]
    > Általában tart, az API Management szolgáltatás létrehozása a 20-30 perc között. Kiválasztása **rögzítés az irányítópulton** keresése egy újonnan létrehozott service egyszerűbbé teszi.

## <a name="next-steps"></a>Következő lépések

[Az API-t az Azure API Management közzététele](#api-management-getstarted-publish-api.md)