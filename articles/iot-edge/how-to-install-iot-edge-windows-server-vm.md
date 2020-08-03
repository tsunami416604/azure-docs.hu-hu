---
title: Azure IoT Edge futtatása a Windows Server Virtual Machineson | Microsoft Docs
description: Azure IoT Edge telepítési utasítások a Windows Server piactéren Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494060"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Azure IoT Edge futtatása Windows Serveren Virtual Machines

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből.

Ha többet szeretne megtudni arról, hogyan működik a IoT Edge futtatókörnyezet és milyen összetevőket tartalmaz, tekintse meg [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md)című témakört.

Ez a cikk azokat a lépéseket sorolja fel, amelyekkel [a Windows Server Azure](https://www.microsoft.com/cloud-platform/windows-server-pricing) piactéren elérhető windows Server 2019 virtuális gépen futtathatja a Azure IoT Edge Runtime szolgáltatást. Kövesse az [Azure IoT Edge futtatókörnyezet telepítése](how-to-install-iot-edge-windows.md) Windows rendszeren más verziókkal való használatra című témakör utasításait.

## <a name="deploy-from-the-azure-marketplace"></a>Üzembe helyezés az Azure piactéren

1. Navigáljon a [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace ajánlathoz, vagy keressen rá a "Windows Server" kifejezésre az [Azure piactéren](https://azuremarketplace.microsoft.com/)
2. Válassza a **Letöltés most** lehetőséget.
3. A **szoftvercsomag**területen keresse meg a "Windows Server 2019 Datacenter Server Core with containers" kifejezést, majd válassza a **tovább** gombot a következő párbeszédpanelen.
    * Ezeket az utasításokat a Windows Server más verzióihoz is használhatja tárolókkal
4. A Azure Portalban kattintson a **Létrehozás** gombra, és kövesse a varázslót a virtuális gép üzembe helyezéséhez.
    * Ha először próbálkozik egy virtuális géppel, a legegyszerűbb, ha jelszót használ, és engedélyezi az RDP-t és az SSH-t a nyilvános bejövő port menüjében.
    * Ha erőforrás-igényes számítási feladattal rendelkezik, a virtuális gép méretét további processzorok és/vagy memória hozzáadásával kell frissítenie.
5. A virtuális gép üzembe helyezését követően konfigurálja úgy, hogy az IoT Hubhoz kapcsolódjon:
    1. Másolja a IoT Edge eszközön a IoT Hub létrehozott eszköz-csatlakoztatási karakterláncot. Tekintse meg az eljárást a [Azure Portal a kapcsolatok karakterláncának beolvasásához](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Válassza ki az újonnan létrehozott virtuálisgép-erőforrást a Azure Portal, és nyissa meg a **parancs futtatása** lehetőséget.
    1. Válassza a **RunPowerShellScript** lehetőséget.
    1. Másolja ezt a szkriptet a parancssorablakba az eszközhöz tartozó kapcsolási karakterlánccal:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Futtassa a parancsfájlt az IoT Edge futtatókörnyezet telepítéséhez, és állítsa be a kapcsolódási karakterláncot a **Futtatás** lehetőség kiválasztásával.
    1. Egy-két perc múlva megjelenik egy üzenet arról, hogy a peremhálózati futtatókörnyezet telepítése és üzembe helyezése sikeres volt.

## <a name="deploy-from-the-azure-portal"></a>Üzembe helyezés a Azure Portal

1. A Azure Portalban keressen rá a "Windows Server" kifejezésre, és válassza a **Windows server 2019 Datacenter** lehetőséget a virtuális gép létrehozási munkafolyamatának megkezdéséhez.
2. Válassza ki **a kívánt szoftvercsomagot** : "Windows Server 2019 Datacenter Server Core with containers", majd válassza a **Létrehozás** lehetőséget.
3. Fejezze be az 5. lépést az "üzembe helyezés az Azure Marketplace-en" című részben ismertetett utasítások alapján.

## <a name="deploy-from-azure-cli"></a>Üzembe helyezés az Azure CLI-ből

1. Ha az Azure CLI-t használja az asztalon, először jelentkezzen be:

   ```azurecli-interactive
   az login
   ```

1. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyet használni szeretne:
   1. Listázza az előfizetéseit:

      ```azurecli-interactive
      az account list --output table
      ```

   1. A használni kívánt előfizetés SubscriptionID mezőjének másolása
   1. Futtassa ezt a parancsot a másolt AZONOSÍTÓval:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Hozzon létre egy új erőforráscsoportot (vagy határozzon meg egy meglévőt a következő lépésekben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Új virtuális gép létrehozása:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Ez a parancs felszólítja a jelszó megadására, de a parancsfájlban megadhatja, hogy könnyebb legyen a beállítás. `--admin-password`
   * A Windows Server Core rendszerkép csak a távoli asztal szolgáltatással támogatja a parancssori támogatást, így ha a teljes asztali élményre van szüksége, akkor `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` a képet kell megadni

1. Adja meg az eszköz csatlakoztatási karakterláncát (ha nem ismeri ezt a folyamatot, kövesse a következőt: a [kapcsolatok karakterláncának lekérése az Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) eljárással):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>További lépések

Most, hogy már telepített egy IoT Edge eszközt a futtatott futtatókörnyezettel, telepítheti [IoT Edge modulokat](how-to-deploy-modules-portal.md).

Ha problémákat tapasztal a peremhálózati futtatókörnyezet megfelelő telepítésekor, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

További információ a Windows rendszerű virtuális gépek használatáról a [Windows Virtual Machines dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/windows/).

Ha a telepítés után SSH-t szeretne telepíteni a virtuális gépre, kövesse az OpenSSH-t a [Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) -útmutatóhoz a távoli asztal vagy a távoli PowerShell használatával.
