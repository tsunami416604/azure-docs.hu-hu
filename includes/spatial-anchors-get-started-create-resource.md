---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752286"
---
## <a name="create-a-spatial-anchors-resource"></a>Térbeli horgonyok erőforrás létrehozása

1. Lépjen az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

2. Az Azure Portal bal oldali menüből válassza ki a **erőforrás létrehozása**.

3. A keresősávban keressen a "Térbeli horgonyok".

   ![Térbeli horgonyok keresése](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Válassza ki **térbeli horgonyok (előzetes verzió)** egy párbeszédpanel megnyitásához, és válassza ki **létrehozás**.

5. Az a **térbeli horgonyok fiók** űrlapot:

   1. Adjon meg egy egyedi erőforráscsoport-nevet.
   2. Válassza ki az előfizetést, az erőforrás csatolása.
   3. Hozzon létre egy erőforráscsoportot kiválasztásával **új létrehozása** nevet az erőforráscsoportnak és **myResourceGroup** , és válassza ki **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Válassza ki a helyet (régiót), ahol az erőforrás kerülnek.
   5. Válassza ki **új** az erőforrás létrehozásának megkezdéséhez.

   ![Erőforrás létrehozása](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Miután az erőforrás-létrehozás sikeresen befejeződött, az erőforrás-tulajdonságokat is megtekinthetők. Jegyezze fel az erőforrás **Fiókazonosító** értékeként adja, szükség lesz később.

   ![Erőforrás-tulajdonságok megtekintése](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. Alatt **beállítások**, jelölje be **kulcs** , és jegyezze fel a **elsődleges kulcs** értéket. Ez az érték a `Account Key` , és később fogja használni.

   ![Fiókkulcs megtekintése](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
