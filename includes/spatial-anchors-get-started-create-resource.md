---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83638995"
---
## <a name="create-a-spatial-anchors-resource"></a>Térbeli horgonyok erőforrásának létrehozása

Lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

A Azure Portal bal oldali navigációs paneljén válassza az **erőforrás létrehozása**lehetőséget.

A keresőmező segítségével megkeresheti a **térbeli horgonyokat**.

   ![Térbeli horgonyok keresése](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Válassza a **térbeli horgonyok**lehetőséget. A párbeszédpanelen válassza a **Létrehozás**elemet.

A **térbeli horgonyok fiók** párbeszédpanelen:

- Adjon meg egy egyedi erőforrás-nevet, amely normál alfanumerikus karaktereket használ.
- Válassza ki azt az előfizetést, amelyhez az erőforrást csatolni kívánja.
- Hozzon létre egy erőforráscsoportot az **új létrehozása**lehetőség kiválasztásával. Nevezze el **myResourceGroup** , és kattintson **az OK gombra**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Válassza ki azt a helyet (régiót), ahová az erőforrást helyezni kívánja.
- Válassza az **új** lehetőséget az erőforrás létrehozásának megkezdéséhez.

   ![Erőforrás létrehozása](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Az erőforrás létrehozása után az Azure Portal megjeleníti az üzembe helyezés befejezését. Kattintson az **Erőforrás megnyitása** elemre.

![Üzembe helyezés kész](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Ezt követően megtekintheti az erőforrás tulajdonságait. Másolja az erőforrás **Account ID** értékét egy szövegszerkesztőbe, mert később szüksége lesz rá.

   ![Erőforrás tulajdonságai](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Másolja az erőforrás **fiók tartományának** értékét egy szövegszerkesztőbe, mert később szüksége lesz rá.

   ![Fiók tartománya](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

A **Beállítások**területen válassza a **kulcs**elemet. Másolja az **elsődleges kulcs** értékét egy szövegszerkesztőbe. Ez az érték a `Account Key` . Erre később még szüksége lesz.

   ![Fiókkulcs](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
