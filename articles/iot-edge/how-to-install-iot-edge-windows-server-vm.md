---
title: Futtassa a Windows Server rendszerű virtuális gépek az Azure IoT Edge |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a Windows Server Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: 9e3f7e3b23cba3fab87ee35aa2a15b6305d9ece4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054180"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Futtassa az Azure IoT Edge a Windows Server virtuális gépeken
Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható.

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk az Azure IoT Edge-futtatókörnyezet futtassa a Windows Server 2019 virtuális gépek használatának lépéseit ismerteti a [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-ajánlat. Kövesse az utasításokat, [telepítse az Azure IoT Edge-futtatókörnyezet](how-to-install-iot-edge-windows.md) a Windows egyéb verziói való használatra.

## <a name="deploy-from-the-azure-marketplace"></a>Az Azure Marketplace-ről üzembe helyezése
1.  Keresse meg a [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-ajánlat vagy a "Windows Server" kereséssel [Azure Marketplace-en](https://azuremarketplace.microsoft.com/)
2.  Válassza ki **első most** 
3.  A **szoftvercsomag**, keresse meg a "Windows Server 2019 Datacenter Server Core--tárolók", majd **Folytatás** a következő párbeszédpanelen.
    * Tárolókkal rendelkező Windows Server egyéb verzióinak is használhatja ezeket az utasításokat
4.  Miután az Azure Portalon válassza **létrehozás** , és kövesse a varázsló a virtuális gép üzembe helyezéséhez. 
    *   Ha most először, próbálja ki egy virtuális Gépet, a legegyszerűbb jelszó használatát, és engedélyezze az RDP és SSH nyilvános bejövő port menüjében. 
    *   Ha egy erőforrás nagy számításigényű számítási feladatok, frissítenie kell a virtuális gép mérete további processzorokat és/vagy memória hozzáadásával.
5.  A virtuális gép üzembe helyezése után konfigurálja, hogy csatlakozik az IoT hubhoz:
    1.  Másolja az eszköz kapcsolati karakterláncát az IoT hub létrehozása az IoT Edge-eszköz (követheti a [egy új, az Azure Portalról az Azure IoT Edge-eszköz regisztrálása](how-to-register-device-portal.md) gyakorlati útmutató, ha nem ismeri a folyamat)
    1.  Válassza ki az újonnan létrehozott virtuális gép típusú erőforrást az Azure portal, és nyissa meg a **futtatása paranccsal** lehetőség
    1.  Válassza ki a **RunPowerShellScript** lehetőség
    1.  Másolja ezt a szkriptet a parancssori ablakba, és az eszköz kapcsolati karakterláncát: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Hajtsa végre a parancsfájlt az Edge-futtatókörnyezet telepítését, és beállítjuk a kapcsolati karakterláncot kiválasztásával **futtatása**
    1.  Ha egy-két percen belül egy üzenet, hogy az Edge-futtatókörnyezet telepítve lett, és sikeresen kiépítve jelenik meg.


## <a name="deploy-from-the-azure-portal"></a>Az Azure Portalról üzembe helyezése
1. Az Azure Portalon "A Windows Server" keresése és kiválasztása **Windows Server 2019 Datacenter** a virtuális gép létrehozása a munkafolyamat elindításához. 
2. A **szoftvercsomag kiválasztása** válassza ki a "Windows Server 2019 Datacenter Server Core--tárolók", majd válassza ki **létrehozása**
3. Hajtsa végre a fenti 5. lépés a "központi telepítése a az Azure Marketplace-en" útmutatást.

## <a name="deploy-from-azure-cli"></a>Az Azure CLI telepítése
1. Azure CLI-vel használ az asztalon, ha először jelentkezik be:

   ```azurecli-interactive
   az login
   ```
    
1. Ha több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést:
   1. Az előfizetések listájának:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Az a használni kívánt előfizetés előfizetés-azonosító mező másolása
   1. A kimásolt Azonosítóját a következő parancs futtatásával:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Hozzon létre egy új erőforráscsoportot (vagy adjon meg egy meglévőt a következő lépésben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Hozzon létre egy új virtuális gépet:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```
   * Ez a parancs kérni fogja a jelszó, de a paramétert a `--admin-password` parancsfájl könnyebben beállítása
   * A Windows Server Core-rendszerképre támogatása csak a távoli asztalról. sor, ezért ha szeretné, hogy a teljes asztali élmény, a parancs `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` a képpel

1. Állítsa be az eszköz kapcsolati karakterláncának (követheti a [egy új Azure IoT Edge-eszköz regisztrálása az Azure CLI-vel](how-to-register-device-cli.md) gyakorlati útmutató, ha még nem ismeri a folyamattal):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha problémái vannak az Edge-futtatókörnyezet megfelelően telepíti, olvassa el a [hibaelhárítási](troubleshoot.md) lapot.

Egy meglévő telepítéshez az IoT Edge a legújabb verzióra frissítéséhez lásd [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).

További információ a Windows virtual machines használatával a [Windows Virtual Machines dokumentációja](https://docs.microsoft.com/azure/virtual-machines/windows/).

Ha szeretne SSH be ezt a virtuális Gépet a telepítés után, kövesse a [telepítési az OpenSSH for Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) útmutató a távoli asztal vagy a távoli powershell használatával.
