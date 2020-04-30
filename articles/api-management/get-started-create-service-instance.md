---
title: Azure API Management-példány létrehozása | Microsoft Docs
description: Új Azure API Management-példány létrehozásához kövesse ennek az oktatóanyagnak a lépéseit.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: acfa10ea506e063bf6b230386ca0722f1c959d18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75442564"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Új Azure API Management-szolgáltatáspéldány létrehozása

Az Azure API Management (APIM) segít közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják használni az adataikban és szolgáltatásaikban rejlő lehetőségeket. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. Az APIM segítségével modern API-átjárókat hozhat létre meglévő háttérrendszerekhez, és az üzemeltetés helyétől függetlenül kezelheti azokat. További információt az [Áttekintés](api-management-key-concepts.md) témakörben talál.

Ez a gyors útmutató az új API Management-példányok Azure Portalon történő létrehozásának lépéseit mutatja be.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![API Management-példány](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-new-service"></a>Új szolgáltatás létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Kiválaszthatja az **erőforrás létrehozása** lehetőséget is az Azure **kezdőlapján** . 
   
   ![Válassza az erőforrás létrehozása lehetőséget.](./media/get-started-create-service-instance/00-CreateResource-01.png)
   
1. Az **új** képernyőn válassza az **integráció** lehetőséget, majd válassza a **API Management**lehetőséget.
   
   ![Új Azure API Management-példány](./media/get-started-create-service-instance/00-CreateResource-02.png)
   
1. A **API Management szolgáltatás** képernyőn adja meg a beállításokat.
   
   ![új példány](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)
   
   | Beállítás                 | Ajánlott érték                               | Leírás                                                                                                                                                                                                                                                                                                                         |
|-------------------------|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Név**                | Az API Management szolgáltatás egyedi neve | A név később nem módosítható. A szolgáltatásnév alapján jön létre a *{név}.azure-api.net* formátumú alapértelmezett tartománynév. Ha egyéni tartománynevet kíván használni, tekintse meg az [Egyéni tartománynév konfigurálása](configure-custom-domain.md) témakört. <br/> A szolgáltatásnév a szolgáltatásra és a megfelelő Azure erőforrásra utal. |
| **Előfizetés**        | Az Ön előfizetése                             | Az új szolgáltatáspéldány létrehozásához használt előfizetés. Az Ön számára elérhető Azure-előfizetések közül választhat.                                                                                                                                                            |
| **Erőforráscsoport**      | *apimResourceGroup*                           | Válasszon ki egy új vagy egy létező erőforrást. Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információ [itt](../azure-resource-manager/management/overview.md#resource-groups).                                                                                                  |
| **Hely**            | *USA nyugati régiója*                                    | Válassza ki az Önhöz legközelebbi földrajzi régiót. A legördülő listában csak az elérhető API Management szolgáltatási régiók jelennek meg.                                                                                                                                                                                                          |
| **Szervezet neve**   | Az intézmény neve                 | Ezt a nevet sok helyen használja a rendszer, többek között a fejlesztői portál címében és az értesítő e-mailek küldőjeként.                                                                                                                                                                                                             |
| **Rendszergazdai e-mail** | *rendszergazdai\@org.com*                               | Az itt megadott e-mail-címre érkezik majd az **API Management** által küldött összes értesítés.                                                                                                                                                                                                                                              |
| **Díjszabási csomag**        | *Fejlesztő*                                   | Állítsa be a **Fejlesztői** szintet a szolgáltatás értékeléséhez. Ez a szint nem használható éles környezetben. További információt az API Management szintjeinek skálázásáról a [frissítés és skálázás](upgrade-and-scale.md) oldalon talál.                                                                                                                                    |

3. Válassza a **Létrehozás** elemet.

    > [!TIP]
    > Egy új API Management szolgáltatás létrehozása általában 20-30 percig tart. A **Rögzítés az irányítópulton** lehetőség használatával könnyebben megtalálhatja az újonnan létrehozott szolgáltatást.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást az alábbi lépések végrehajtásával:

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok**elemet. A **kezdőlapon** kiválaszthatja az **erőforráscsoportok** lehetőséget is. 

   ![Erőforráscsoportok navigációja](./media/get-started-create-service-instance/00-DeleteResource-01.png)

1. Az **erőforráscsoportok** lapon válassza ki az erőforráscsoportot.

   ![Erőforráscsoportok navigációja](./media/get-started-create-service-instance/00-DeleteResource-02.png)

1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése**elemet. 
   
1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

   ![Erőforráscsoport törlése](./media/get-started-create-service-instance/00-DeleteResource-03.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az első API importálása és közzététele](import-and-publish.md)
