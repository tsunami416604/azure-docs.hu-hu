---
title: Az Azure IoT Edge futtatása Windows Server virtuális gépeken | Microsoft dokumentumok
description: Az Azure IoT Edge beállítási utasításai a Windows Server Piactér virtuális gépeken
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045900"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Az Azure IoT Edge futtatása Windows Server virtuális gépeken

Az Azure IoT Edge-futásidejű, mi bekapcsolja az eszközt egy IoT Edge-eszköz. A futásidejű olyan kicsi eszközökre is telepíthető, mint a Raspberry Pi vagy az ipari kiszolgáló. Miután egy eszköz konfigurálva van az IoT Edge-futásidejű, megkezdheti az üzleti logika üzembe helyezését a felhőből.

Ha többet szeretne megtudni az IoT Edge futásidejű működéséről és a benne lévő összetevőkről, [olvassa el az Azure IoT Edge futásidejű és architektúrájának megismerése című témakört.](iot-edge-runtime.md)

Ez a cikk az Azure IoT Edge futásidejű windows Server 2019 virtuális gépen a [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-ajánlat használatával történő futtatásának lépéseit sorolja fel. Kövesse az [Azure IoT Edge futásidejű Windows-alapú telepítése](how-to-install-iot-edge-windows.md) című témakörutasításait más verziókkal való használatra.

## <a name="deploy-from-the-azure-marketplace"></a>Üzembe helyezés az Azure Piactérről

1. Keresse meg a [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Piactér ajánlatát, vagy keressen a "Windows Server" kifejezésre az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/)
2. Válassza **a BESZEREZNI MOST lehetőséget**
3. A **Szoftverterv**ben keresse meg a "Windows Server 2019 Datacenter Server Core tárolókkal" lehetőséget, majd válassza a **Folytatás** a következő párbeszédpanelen lehetőséget.
    * Ezeket az utasításokat a Windows Server tárolóval rendelkező más verzióihoz is használhatja
4. Miután az Azure Portalon, válassza **a Létrehozása** és kövesse a varázslót a virtuális gép üzembe helyezéséhez.
    * Ha ez az első alkalom, hogy kipróbál egy virtuális gép, a legegyszerűbb, hogy egy jelszót, és engedélyezni rdp és SSH a nyilvános bejövő port menüben.
    * Ha erőforrás-igényes számítási feladattal rendelkezik, további processzorok és/vagy memória hozzáadásával frissítenie kell a virtuális gép méretét.
5. A virtuális gép üzembe helyezése után konfigurálja úgy, hogy csatlakozzon az IoT Hubhoz:
    1. Másolja az eszköz kapcsolati karakterláncát az IoT Hubban létrehozott IoT Edge-eszközről. Lásd az eljárást [A kapcsolati karakterlánc lekérése az Azure Portalon.](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)
    1. Válassza ki az újonnan létrehozott virtuálisgép-erőforrást az Azure Portalon, és nyissa meg a **futtatási parancs** lehetőséget
    1. Válassza a **RunPowerShellScript** beállítást
    1. Másolja ezt a parancsfájlt a parancsablakba az eszköz kapcsolati karakterláncával:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. A parancsfájl végrehajtása az IoT Edge futásidejének telepítéséhez és a kapcsolati karakterlánc beállításához a **Futtatás**
    1. Egy-két perc múlva meg kell jelennie egy üzenetet, hogy az Edge futásidejű telepítése és kiépítése sikeresen.

## <a name="deploy-from-the-azure-portal"></a>Üzembe helyezés az Azure Portalról

1. Az Azure Portalon keressen rá a "Windows Server" kifejezésre, és válassza a **Windows Server 2019 Datacenter lehetőséget** a virtuális gép létrehozási munkafolyamatának megkezdéséhez.
2. Válasszon **válasszon egy szoftvercsomagot** a "Windows Server 2019 Datacenter Server Core with Containers" (Windows Server Server Core tárolókkal) lehetőség közül, majd válassza a **Létrehozás lehetőséget.**
3. Az 5.

## <a name="deploy-from-azure-cli"></a>Üzembe helyezés az Azure CLI-ből

1. Ha az Azure CLI-t használja az asztalon, először jelentkezzen be:

   ```azurecli-interactive
   az login
   ```

1. Ha több előfizetése van, válassza ki a használni kívánt előfizetést:
   1. Listázza az előfizetéseit:

      ```azurecli-interactive
      az account list --output table
      ```

   1. A használni kívánt előfizetési azonosító mező másolása
   1. Futtassa ezt a parancsot a másolt azonosítóval:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Hozzon létre egy új erőforráscsoportot (vagy adjon meg egy meglévőt a következő lépésekben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Új virtuális gép létrehozása:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Ez a parancs jelszót kér, de hozzáadhatja `--admin-password` a lehetőséget, hogy könnyebben beállítsa egy parancsfájlban
   * A Windows Server Core lemezkép parancssori támogatása csak a távoli asztallal `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` rendelkezik, így ha a teljes asztali élményt szeretné, adja meg a lemezképként

1. Állítsa be az eszköz kapcsolati karakterláncát (A [kapcsolatlekérés az Azure CLI-eljárással,](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) ha nem ismeri ezt a folyamatot):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>További lépések

Most, hogy egy IoT Edge-eszközt létesített a futásidejű telepítve, telepítheti az [IoT Edge-modulokat.](how-to-deploy-modules-portal.md)

Ha problémái vannak az Edge futásidejű megfelelő telepítésével, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Ha egy meglévő telepítést az IoT Edge legújabb verziójára szeretne frissíteni, olvassa [el az IoT Edge biztonsági démonának és futásidejű](how-to-update-iot-edge.md)verziójának frissítése című témakört.

A Windows virtuális gépek használatáról a [Windows virtuális gépek dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/windows/)talál további információval.

Ha azt szeretné, hogy SSH ebbe a virtuális gép a telepítés után, kövesse a [telepítés OpenSSH for Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) útmutató segítségével távoli asztali vagy távoli powershell.
