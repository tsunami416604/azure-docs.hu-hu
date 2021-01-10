---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060590"
---
Az oktatóanyaghoz a következő Azure-erőforrások szükségesek:

* IoT Hub
* Tárfiók
* Azure Media Services fiók
* Linux virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](../../../../../iot-edge/how-to-install-iot-edge.md) telepítve

Ebben a rövid útmutatóban azt javasoljuk, hogy az Azure-előfizetésében lévő szükséges erőforrások üzembe helyezéséhez használja az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Cloud Shellt](https://shell.azure.com).
1. Ha első alkalommal használja a Cloud Shell, a rendszer kérni fogja, hogy válasszon egy előfizetést egy Storage-fiók és egy Microsoft Azure-fájlmegosztás létrehozásához. Válassza a **tároló létrehozása** lehetőséget a Cloud Shell munkamenet-információhoz tartozó Storage-fiók létrehozásához. Ez a Storage-fiók elkülönül a parancsfájl által a Azure Media Services-fiókkal való használatra létrehozott fióktól.
1. A Cloud Shell ablak bal oldalán lévő legördülő menüben válassza a **bash** lehetőséget a környezetében.

    ![Környezeti választó](../../../media/quickstarts/env-selector.png)
1. Futtassa az alábbi parancsot.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    A szkript sikeres befejeződése után az előfizetésben szereplő összes szükséges erőforrást látnia kell.
1. A szkript befejeződése után válassza ki a kapcsos zárójeleket, hogy elérhetővé tegye a mappastruktúrát. A *~/clouddrive/LVA-Sample* könyvtárban néhány fájl jelenik meg. Ebben a rövid útmutatóban a következők szerepelnek:

     * ***~/clouddrive/LVA-Sample/Edge-Deployment/.env** _ – ez a fájl olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez.
     _ ***~/clouddrive/lva-sample/appsetting.json** _ – a Visual Studio Code ezt a fájlt használja a mintakód futtatásához.
     
    Ezekre a fájlokra szüksége lesz, amikor a következő szakaszban a Visual Studio Code-ban beállítja a fejlesztési környezetet. Előfordulhat, hogy most egy helyi fájlba szeretné másolni őket.
    
    ![Alkalmazásbeállítások](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Ha a létrehozott Azure-erőforrásokkal kapcsolatos problémákba ütközik, tekintse meg az _ *[hibaelhárítási útmutatót](../../../troubleshoot-how-to.md#common-error-resolutions)** a gyakran előforduló problémák megoldásához.