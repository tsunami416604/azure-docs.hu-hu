---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681263"
---
## <a name="create-a-spatial-anchors-resource"></a>Térbeli horgonyok erőforrás létrehozása

Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Az Azure portal bal oldali navigációs panelén válassza **erőforrás létrehozása**.

A keresőmezőt használva keressen **térbeli horgonyok**.

   ![Térbeli horgonyok keresése](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Válassza ki **térbeli horgonyok**. A párbeszédpanelen válassza ki a **létrehozás**.

Az a **térbeli horgonyok fiók** párbeszédpanel:

- Adja meg egy egyedi erőforráscsoport nevét, rendszeres alfanumerikus karakterek használatával.
- Válassza ki az előfizetést, amelyhez csatolja az erőforrást.
- Hozzon létre egy erőforráscsoportot kiválasztásával **új létrehozása**. Nevezze el **myResourceGroup** válassza **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Válassza ki a helyet (régiót) az erőforrás helyét is.
- Válassza ki **új** az erőforrás létrehozásának megkezdéséhez.

   ![Erőforrás létrehozása](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Az erőforrás létrehozása után az Azure portálon az jelenik meg, hogy a telepítés befejeződött. Kattintson az **Erőforrás megnyitása** elemre.

![Üzembe helyezés kész](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Ezt követően megtekintheti az erőforrás-tulajdonságok is. Másolja ki az erőforrás **Fiókazonosító** értékét egy szövegszerkesztőbe, mert később szüksége.

   ![Erőforrás-tulajdonságok](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

A **beállítások**válassza **kulcs**. Másolás a **elsődleges kulcs** értékét egy szövegszerkesztőbe. Ez az érték a `Account Key`. Erre később még szüksége lesz.

   ![Fiókkulcs](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
