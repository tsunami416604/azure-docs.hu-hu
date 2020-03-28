---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67135039"
---
## <a name="create-a-spatial-anchors-resource"></a>Térbeli horgonyok erőforrás létrehozása

Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portalt.</a>

Az Azure Portal bal oldali navigációs ablakában válassza az **Erőforrás létrehozása lehetőséget.**

A keresőmező segítségével keresse meg a **Térbeli horgonyokat.**

   ![Térbeli horgonyok keresése](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Válassza **a Térbeli horgonyok**lehetőséget. A párbeszédpanelen válassza a **Létrehozás gombot.**

A **Térbeli horgonyok fiók** párbeszédpanelén:

- Adjon meg egy egyedi erőforrásnevet, normál alfanumerikus karakterek használatával.
- Válassza ki azt az előfizetést, amelyhez csatolni szeretné az erőforrást.
- Erőforráscsoport létrehozása az **Új létrehozása**lehetőség kiválasztásával. Nevezze el **myResourceGroup-nak,** és válassza **az OK gombot.**
      [!INCLUDE [resource group intro text](resource-group.md)]
- Válassza ki azt a helyet (régiót), ahová az erőforrást helyezni szeretné.
- Az erőforrás létrehozásának megkezdéséhez válassza az **Új** lehetőséget.

   ![Erőforrás létrehozása](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Az erőforrás létrehozása után az Azure Portal megmutatja, hogy a központi telepítés befejeződött. Kattintson az **Erőforrás megnyitása** elemre.

![Üzembe helyezés kész](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Ezután megtekintheti az erőforrás tulajdonságait. Másolja az erőforrás **fiókazonosító-értékét** egy szövegszerkesztőbe, mert később szüksége lesz rá.

   ![Erőforrás tulajdonságai](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

A **Beállítások csoportban**válassza a **Kulcs**lehetőséget. Másolja az **Elsődleges kulcs** értékét egy szövegszerkesztőbe. Ez az `Account Key`érték a . Erre később még szüksége lesz.

   ![Fiókkulcs](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
