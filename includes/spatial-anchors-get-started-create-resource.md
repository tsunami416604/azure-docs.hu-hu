---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305204"
---
## <a name="create-a-spatial-anchors-resource"></a>Térbeli horgonyok erőforrás létrehozása

Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Az Azure portal bal oldali navigációs panelén válassza **erőforrás létrehozása**.

A keresőmezőt használva keressen **térbeli horgonyok**.

   ![Térbeli horgonyok keresése](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Válassza ki **térbeli horgonyok**. A párbeszédpanelen válassza ki a **létrehozás**.

Az a **térbeli horgonyok fiók** párbeszédpanel:

- Adjon meg egy egyedi erőforráscsoport-nevet.
- Válassza ki az előfizetést, amelyhez csatolja az erőforrást.
- Hozzon létre egy erőforráscsoportot kiválasztásával **új létrehozása**. Nevezze el **myResourceGroup** válassza **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Válassza ki a helyet (régiót) az erőforrás helyét is.
- Válassza ki **új** az erőforrás létrehozásának megkezdéséhez.

   ![Erőforrás létrehozása](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Az erőforrás létrehozása után megtekintheti az erőforrás-tulajdonságokat. Másolja ki az erőforrás **Fiókazonosító** értékét egy szövegszerkesztőbe, mert később szüksége.

   ![Erőforrás-tulajdonságok](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

A **beállítások**válassza **kulcs**. Másolás a **elsődleges kulcs** értékét egy szövegszerkesztőbe. Ez az érték a `Account Key`. Erre később még szüksége lesz.

   ![Fiókkulcs](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
