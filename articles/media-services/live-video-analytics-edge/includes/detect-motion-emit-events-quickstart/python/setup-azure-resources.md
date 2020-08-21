---
ms.openlocfilehash: 67d90836c382728f989ab2cb4fde4d81bac9eb25
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691024"
---
Az oktatóanyaghoz a következő Azure-erőforrások szükségesek:

* IoT Hub
* Tárfiók
* Azure Media Services fiók
* Linux virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](../../../../../iot-edge/how-to-install-iot-edge-linux.md) telepítve

Ebben a rövid útmutatóban azt javasoljuk, hogy az Azure-előfizetésében lévő szükséges erőforrások üzembe helyezéséhez használja az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Cloud Shellt](https://shell.azure.com).
1. Ha első alkalommal használja a Cloud Shell, a rendszer kérni fogja, hogy válasszon egy előfizetést egy Storage-fiók és egy Microsoft Azure-fájlmegosztás létrehozásához. Válassza a **tároló létrehozása** lehetőséget a Cloud Shell munkamenet-információhoz tartozó Storage-fiók létrehozásához. Ez a Storage-fiók elkülönül a parancsfájl által a Azure Media Services-fiókkal való használatra létrehozott fióktól.
1. A Cloud Shell ablak bal oldalán lévő legördülő menüben válassza a **bash** lehetőséget a környezetében.

    ![Környezeti választó](../../../media/quickstarts/env-selector.png)
1. Futtassa az alábbi parancsot:

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Ha a parancsfájl sikeresen befejeződik, az előfizetésben szereplő összes szükséges erőforrást látnia kell.
1. A szkript befejeződése után válassza ki a kapcsos zárójeleket, hogy elérhetővé tegye a mappastruktúrát. A *~/clouddrive/LVA-Sample* könyvtárban néhány fájl jelenik meg. Ebben a rövid útmutatóban a következők szerepelnek:

     * ***~/clouddrive/LVA-Sample/Edge-Deployment/.env*** – ez a fájl olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code ezt a fájlt használja a mintakód futtatásához.
     
    Ezekre a fájlokra szüksége lesz, amikor a következő szakaszban a Visual Studio Code-ban beállítja a fejlesztési környezetet. Előfordulhat, hogy most egy helyi fájlba szeretné másolni őket.
    
    ![Alkalmazásbeállítások](../../../media/quickstarts/clouddrive.png)
    