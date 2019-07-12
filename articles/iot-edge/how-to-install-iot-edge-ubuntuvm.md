---
title: Az Azure IoT Edge Ubuntu virtuális gépeken fussanak |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat az Ubuntu 16.04 Azure Marketplace-en Virtual machines szolgáltatásban
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 8275bceca1a18f49eb7eeece66a3866d77c47635
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796164"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Run Azure IoT Edge on Ubuntu Virtual Machines

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható.

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk az Azure IoT Edge-futtatókörnyezet Futtatás Ubuntu 16.04 virtuális gépen az előre konfigurált használatával lépéseit ismerteti [Ubuntu Azure Marketplace-ajánlat az Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm). 

Az első rendszerindításkor az Azure IoT Edge Ubuntu virtuális gép előtelepíti az Azure IoT Edge-futtatókörnyezet legfrissebb verzióját. Azt is tartalmaz egy parancsfájlt, beállítjuk a kapcsolati karakterláncot, és indítsa újra a modult, amely távolról indíthatók az Azure virtuális gép portálon vagy a, így könnyedén konfigurálhat és az IoT Edge-eszköz csatlakoztatása egy SSH- vagy távoli elindítása nélkül, az Azure parancssori felületével asztali munkamenetet. Ez a szkript beállítjuk a kapcsolati karakterláncot, amíg az IoT Edge-ügyfél teljes nincs telepítve, így nem kell olyan hozhatnak létre, amelyek az automation várakozik.

## <a name="deploy-from-the-azure-marketplace"></a>Az Azure Marketplace-ről üzembe helyezése
1.  Keresse meg a [Ubuntu rendszeren az Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm) Piactéri ajánlat vagy az "Azure IoT Edge az Ubuntu" kereséssel [az Azure Marketplace-en](https://azuremarketplace.microsoft.com/)
2.  Válassza ki **most már az első informatikai** , majd **Folytatás** a következő párbeszédpanelen.
3.  Miután az Azure Portalon válassza **létrehozás** , és kövesse a varázsló a virtuális gép üzembe helyezéséhez. 
    *   Ha most először, próbálja ki egy virtuális Gépet, a legegyszerűbb jelszóra és a nyilvános bejövő port menü az SSH engedélyezéséhez. 
    *   Ha egy erőforrás nagy számításigényű számítási feladatok, frissítenie kell a virtuális gép mérete további processzorokat és/vagy memória hozzáadásával.
4.  A virtuális gép üzembe helyezése után konfigurálja, hogy csatlakozik az IoT hubhoz:
    1.  Másolja az eszköz kapcsolati karakterláncát az IoT hub létrehozása az IoT Edge-eszköz (követheti a [egy új, az Azure Portalról az Azure IoT Edge-eszköz regisztrálása](how-to-register-device-portal.md) gyakorlati útmutató, ha nem ismeri a folyamat)
    1.  Válassza ki az újonnan létrehozott virtuális gép típusú erőforrást az Azure portal, és nyissa meg a **futtatása paranccsal** lehetőség
    1.  Válassza ki a **RunShellScript** lehetőség
    1.  Hajtsa végre az alábbi parancsfájlt keresztül a parancssori ablakba, és az eszköz kapcsolati karakterláncát: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Válassza ki **futtatása**
    1.  Várjon néhány pillanatot, és a képernyő kell adja meg a kapcsolati karakterlánc jelzi a sikert jelző üzenet beállítása sikerült.


## <a name="deploy-from-the-azure-portal"></a>Az Azure Portalról üzembe helyezése
Az Azure Portalról, "Azure IoT Edge" keresése és kiválasztása **Ubuntu Server 16.04 LTS + az Azure IoT Edge-futtatókörnyezet** a virtuális gép létrehozása a munkafolyamat elindításához. Itt lépéseket a 3. és 4 a fenti "Üzembe helyezése a az Azure piactér" utasításban.

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
    
   1. Másolja ki a használni kívánt előfizetés előfizetés-azonosító mezőjére.

   1. Állítsa be az imént kimásolt Azonosítóját működő előfizetésbe:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Hozzon létre egy új erőforráscsoportot (vagy adjon meg egy meglévőt a következő lépésben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Fogadja el a használati feltételeket a virtuális gép. Ha azt szeretné, hogy először olvassa el a feltételeket, kövesse a lépéseket a [üzembe helyezése az Azure Marketplace-ről](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Hozzon létre egy új virtuális gépet:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Állítsa be az eszköz kapcsolati karakterláncának (követheti a [egy új Azure IoT Edge-eszköz regisztrálása az Azure CLI-vel](how-to-register-device-cli.md) gyakorlati útmutató, ha még nem ismeri a folyamattal):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Szeretne SSH be ezt a virtuális Gépet a telepítés után, ha használható a nyilvános IP-címre a parancsot: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha az IoT Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Egy meglévő telepítéshez az IoT Edge a legújabb verzióra frissítéséhez lásd [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).
