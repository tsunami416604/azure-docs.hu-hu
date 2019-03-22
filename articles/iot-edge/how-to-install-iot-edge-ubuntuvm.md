---
title: Az Azure IoT Edge Ubuntu virtuális gépeken fussanak |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat az Ubuntu 16.04 Azure Marketplace-en Virtual machines szolgáltatásban
author: gregman
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: gregman
ms.openlocfilehash: 5b3dcb3abad071cb5d079d6c740cc09e2577a363
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116418"
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
1. Ha most először a parancssori felület a virtuális gép üzembe helyezése, szüksége lesz programozott üzembe helyezés az Azure-előfizetés engedélyezése:
   1. Nyissa meg a [Ubuntu rendszeren az Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace-ajánlat
   1. Válassza ki **most már az első informatikai** és **Folytatás** későbbi párbeszédpanelen
   1. Válassza ki **történő programozott telepítését? Első lépések** a portálon a párbeszédpanel alján
   1. Kattintson a **engedélyezése** gombra a **programozott telepítés konfigurálása** lapon, majd kattintson a **mentése**
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
   1. Az imént kimásolt Azonosítóját a következő parancs futtatásával:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Hozzon létre egy új erőforráscsoportot (vagy adjon meg egy meglévőt a következő lépésben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Hozzon létre egy új virtuális gépet:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM –-image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

1. Állítsa be az eszköz kapcsolati karakterláncának (követheti a [egy új Azure IoT Edge-eszköz regisztrálása az Azure CLI-vel](how-to-register-device-cli.md) gyakorlati útmutató, ha még nem ismeri a folyamattal):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

Szeretne SSH be ezt a virtuális Gépet a telepítés után, ha használható a nyilvános IP-címre a parancsot: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha az Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Egy meglévő telepítéshez az IoT Edge a legújabb verzióra frissítéséhez lásd [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).
