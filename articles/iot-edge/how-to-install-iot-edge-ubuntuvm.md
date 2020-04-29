---
title: Azure IoT Edge futtatása Ubuntu-Virtual Machines | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Ubuntu 18,04 LTS Virtual Machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349604"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Azure IoT Edge futtatása Ubuntu-Virtual Machines

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Ha egy eszköz konfigurálva van a IoT Edge futtatókörnyezettel, megkezdheti az üzleti logika telepítését a felhőből.

Ha többet szeretne megtudni arról, hogyan működik a IoT Edge futtatókörnyezet és milyen összetevőket tartalmaz, tekintse meg [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md)című témakört.

Ez a cikk egy Ubuntu 18,04 LTS rendszerű virtuális gép üzembe helyezésének lépéseit sorolja fel a telepített és az előre megadott eszköz-kapcsolódási karakterlánc használatával konfigurált Azure IoT Edge futtatókörnyezettel. Az üzembe helyezés egy [Felhőbeli init](../virtual-machines/linux/using-cloud-init.md
) -alapú Azure Resource Manager- [sablon](../azure-resource-manager/templates/overview.md) használatával valósul meg, amelyet a [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) Project repository tart fenn.

Az első rendszerindítás során az Ubuntu 18,04 LTS virtuális gép [telepíti a Azure IoT Edge Runtime legújabb verzióját a Cloud-init használatával](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). A megadott kapcsolati karakterláncot is beállítja a futtatókörnyezet elindítása előtt, így egyszerűen konfigurálhatja és csatlakoztathatja az IoT Edge eszközt anélkül, hogy SSH-vagy távoli asztali munkamenetet kellene elindítania. 

## <a name="deploy-using-deploy-to-azure-button"></a>Üzembe helyezés az Azure-beli üzembe helyezéssel gomb

Az [üzembe helyezés az Azure-ban gomb](../azure-resource-manager/templates/deploy-to-azure-button.md) lehetővé teszi a githubon karbantartott [Azure Resource Manager-sablonok](../azure-resource-manager/templates/overview.md) egyszerűsített telepítését.  Ez a szakasz bemutatja az üzembe helyezés az Azure-ban gomb használatát a [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) Project adattárban.  


1. A iotedge-VM-Deploy Azure Resource Manager sablonnal üzembe helyezünk egy Azure IoT Edge engedélyezett linuxos virtuális gépet.  A kezdéshez kattintson az alábbi gombra:

    [![Üzembe helyezés az Azure-ban gomb a iotedge-VM-Deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Az újonnan elindított ablakban töltse ki a rendelkezésre álló űrlapmezők mezőket:

    > [!div class="mx-imgBorder"]
    > [![Képernyőfelvétel a iotedge-VM-Deploy sablonról](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Előfizetés**: a virtuális gép üzembe helyezéséhez szükséges aktív Azure-előfizetés.

    **Erőforráscsoport**: egy meglévő vagy újonnan létrehozott erőforráscsoport, amely tartalmazza a virtuális gépet és a hozzá társított erőforrásokat.

    **DNS-címke előtagja**: a választott érték, amelyet a virtuális gép állomásnévének előtagjaként kell használni.

    **Rendszergazdai Felhasználónév**: a Felhasználónév, amely rendszergazdai jogosultságokkal fog szolgálni az üzembe helyezéshez.

    **Eszköz-összekapcsolási karakterlánc**: egy eszközhöz tartozó, a kívánt [IoT Hubban](../iot-hub/about-iot-hub.md)létrehozott eszköz- [kapcsolatok karakterlánca](how-to-register-device.md) .

    **VM-méret**: a telepítendő virtuális gép [mérete](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu operációs rendszer verziója**: az Ubuntu operációs rendszer azon verziója, amelyet az alapszintű virtuális gépre kell telepíteni.

    **Hely**: az a [földrajzi régió](https://azure.microsoft.com/global-infrastructure/locations/) , amelybe a virtuális gépet üzembe helyezi, ez az érték alapértelmezés szerint a kiválasztott erőforráscsoport helyét jelöli.

    **Hitelesítés típusa**: válassza a **sshPublicKey** vagy a **jelszó** lehetőséget a beállítástól függően.

    **Rendszergazdai jelszó vagy kulcs**: az SSH nyilvános kulcs értéke vagy a jelszó értéke a választott hitelesítési típustól függően.

    Ha az összes mezőt kitöltötte, jelölje be az oldal alján található jelölőnégyzetet a feltételek elfogadásához, majd válassza a **vásárlás** lehetőséget a telepítés megkezdéséhez.

1. Ellenőrizze, hogy a központi telepítés sikeresen befejeződött-e.  Telepíteni kell egy virtuálisgép-erőforrást a kiválasztott erőforráscsoporthoz.  Jegyezze fel a gép nevét, amelynek formátuma `vm-0000000000000`a következő:. Jegyezze fel a társított **DNS-nevet**is, amelynek a formátuma `<dnsLabelPrefix>`. `<location>`. cloudapp.Azure.com.

    A **DNS-név** az újonnan telepített virtuális gép **Áttekintés** részéből szerezhető be a Azure Portalon belül.

    > [!div class="mx-imgBorder"]
    > [![A iotedge virtuális gép DNS-nevét bemutató képernyőkép](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Ha a telepítés után SSH-t szeretne használni a virtuális gépen, használja a társított **DNS-nevet** a következő paranccsal:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Üzembe helyezés az Azure CLI-ből

1. Győződjön meg arról, hogy telepítette az Azure CLI IOT bővítményt az alábbiakkal:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Ha az Azure CLI-t használja az asztalon, először jelentkezzen be a következővel:

   ```azurecli-interactive
   az login
   ```

1. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyet használni szeretne:
   1. Listázza az előfizetéseit:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Másolja a használni kívánt előfizetés SubscriptionID mezőjét.

   1. Állítsa be a munkaidő-előfizetést a másolt AZONOSÍTÓval:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Hozzon létre egy új erőforráscsoportot (vagy határozzon meg egy meglévőt a következő lépésekben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Új virtuális gép létrehozása:

    A **authenticationType** `password`használatához tekintse meg az alábbi példát:

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Ha SSH-kulccsal szeretne hitelesítést végezni, ezt megteheti egy **authenticationType** megadásával `sshPublicKey`, majd megadhatja az SSH-kulcs értékét a **adminPasswordOrKey** paraméterben.  Erre mutat példát az alábbi ábra.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Ellenőrizze, hogy a központi telepítés sikeresen befejeződött-e.  Telepíteni kell egy virtuálisgép-erőforrást a kiválasztott erőforráscsoporthoz.  Jegyezze fel a gép nevét, amelynek formátuma `vm-0000000000000`a következő:. Jegyezze fel a társított **DNS-nevet**is, amelynek a formátuma `<dnsLabelPrefix>`. `<location>`. cloudapp.Azure.com.

    A **DNS-név** az előző lépés JSON-formátumú kimenetéről szerezhető be a **kimenetek** szakaszban a **nyilvános SSH** -bejegyzés részeként.  Ennek a bejegyzésnek az értéke az újonnan üzembe helyezett gépre való SSH-ba is használható.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    A **DNS-név** a Azure Portalon belüli újonnan telepített virtuális gép **Áttekintés** szakaszában is beszerezhető.

    > [!div class="mx-imgBorder"]
    > [![A iotedge virtuális gép DNS-nevét bemutató képernyőkép](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Ha a telepítés után SSH-t szeretne használni a virtuális gépen, használja a társított **DNS-nevet** a következő paranccsal:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>További lépések

Most, hogy már telepített egy IoT Edge eszközt a futtatott futtatókörnyezettel, telepítheti [IoT Edge modulokat](how-to-deploy-modules-portal.md).

Ha problémákat tapasztal a IoT Edge futtatókörnyezet megfelelő telepítésével kapcsolatban, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

Ha meg szeretné nyitni a portokat a virtuális gép SSH-n vagy más bejövő kapcsolaton keresztüli eléréséhez, tekintse meg az Azure Virtual Machines dokumentációját a [portok és végpontok Linux rendszerű virtuális gépre való megnyitásához](../virtual-machines/linux/nsg-quickstart.md)
