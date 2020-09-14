---
title: Szerepköralapú hozzáférés-vezérlés – Custom Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan konfigurálhat szerepköralapú hozzáférés-vezérlést a Custom Vision projektjeihez.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fc536b3b0c7af113919f274fc4bdd5fad63cbd06
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057186"
---
# <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Custom Vision támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), az Azure-erőforrásokhoz való egyéni hozzáférés kezelésére szolgáló engedélyezési rendszert. A RBAC használatával különböző csapattagokat rendelhet hozzá a Custom Vision projektjeihez. A RBAC kapcsolatos további információkért tekintse meg az [Azure RBAC dokumentációját](https://docs.microsoft.com/azure/role-based-access-control/).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Szerepkör-hozzárendelés hozzáadása Custom Vision erőforráshoz

Az Azure-RBAC hozzárendelhető egy Custom Vision erőforráshoz. Az Azure-erőforrásokhoz való hozzáférés biztosításához hozzá kell adnia egy szerepkör-hozzárendelést.
1. Az [Azure Portalon](https://ms.portal.azure.com/) válassza a **Minden szolgáltatás** elemet. 
1. Ezután válassza ki a **Cognitive Services**, és navigáljon az adott Custom Vision képzési erőforráshoz.
   > [!NOTE]
   > A teljes erőforráscsoportok, előfizetések és felügyeleti csoportok RBAC is beállíthatja. Ehhez válassza ki a kívánt hatóköri szintet, majd navigáljon a kívánt elemhez (például válassza az **erőforráscsoportok** lehetőséget, majd kattintson a át a kívánt erőforrás-csoportra).
1. A bal oldali navigációs ablaktáblán válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Válassza ki a **szerepkör-hozzárendelések** lapot a hatókörhöz tartozó szerepkör-hozzárendelések megtekintéséhez.
1. Válassza a **Hozzáadás**  ->  **szerepkör-hozzárendelés hozzáadása**elemet.
1. A **szerepkör** legördülő listában válassza ki a hozzáadni kívánt szerepkört.
1. A **Select (kiválasztás** ) listán válasszon ki egy felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást. Ha nem látja a rendszerbiztonsági tag elemet a listában, beírhatja a kiválasztási mezőt a megjelenítendő nevek, e-mail-címek és objektumazonosítók kereséséhez.
1. A szerepkör hozzárendeléséhez válassza a **Mentés** lehetőséget.

Néhány percen belül a cél hozzá lesz rendelve a kiválasztott szerepkörhöz a kiválasztott hatókörben.

## <a name="custom-vision-role-types"></a>Custom Vision szerepkör típusai

A következő táblázat segítségével meghatározhatja a Custom Vision erőforrásainak hozzáférési igényeit.

|Szerepkör  |Engedélyek  |
|---------|---------|
|`Cognitive Service Custom Vision Contributor`     | Teljes hozzáférés a projektekhez, beleértve a projektek létrehozását, szerkesztését és törlését.        |
|`Cognitive Service Custom Vision Trainer`     | Teljes hozzáférés, kivéve a projekt létrehozását vagy törlését. Az oktatók megtekinthetik és szerkeszthetik a projekteket és betanítják, közzétehetik, közzétehetik vagy exportálhatjuk a modelleket.        |
|`Cognitive Service Custom Vision Labeler`     | A betanítási lemezképek feltöltésére, szerkesztésére és törlésére, valamint címkék létrehozására, hozzáadására, eltávolítására és törlésére képes. A címkéző megtekinthetik a projekteket, de nem frissíthetik a betanítási képeket és címkéket.         |
|`Cognitive Service Custom Vision Deployment`     | A modellek közzétételének, közzétételének visszavonásának vagy exportálásának lehetősége. Az üzembe helyezések megtekinthetik a projekteket, de nem frissíthetik a projektet, a betanítási képeket vagy a címkéket.        |
|`Cognitive Service Custom Vision Reader`     | A projektek megtekintésének lehetősége. Az olvasók nem végezhetnek változást.        |
