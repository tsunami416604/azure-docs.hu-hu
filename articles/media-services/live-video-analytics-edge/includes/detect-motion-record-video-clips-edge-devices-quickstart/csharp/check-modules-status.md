---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682119"
---
A [IoT Edge üzembe helyezési jegyzékfájljának előállítása és telepítése](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) lépésben, a Visual Studio Code-ban bontsa ki a **LVA-Sample-Device** CSOMÓPONTOT az **Azure IoT hub** alatt (a bal alsó szakaszban). A következő modulokat kell látnia:

* A (z) nevű Live Video Analytics-modul `lvaEdge`
* A `rtspsim` modul, amely egy élő videó-hírcsatorna forrásaként szolgáló RTSP-kiszolgálót szimulál

  ![Modulok](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Ha saját peremhálózati eszközt használ a telepítési parancsfájlból kiépített helyett, nyissa meg a peremhálózati eszközét, és futtassa az alábbi parancsokat **rendszergazdai jogosultságokkal**az ehhez a rövid útmutatóhoz használt minta videofájl lekéréséhez és tárolásához:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
