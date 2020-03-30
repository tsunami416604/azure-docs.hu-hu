---
title: Az Azure IoT Edge futtatása Ubuntu virtuális gépeken | Microsoft dokumentumok
description: Az Azure IoT Edge beállítási utasításai ubuntu 18.04 LTS virtuális gépekhez
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349604"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Az Azure IoT Edge futtatása ubuntus virtuális gépeken

Az Azure IoT Edge-futásidejű, mi bekapcsolja az eszközt egy IoT Edge-eszköz. A futásidejű olyan kicsi eszközökre is telepíthető, mint a Raspberry Pi vagy az ipari kiszolgáló. Miután egy eszköz konfigurálva van az IoT Edge-futásidejű, megkezdheti az üzleti logika üzembe helyezését a felhőből.

Ha többet szeretne megtudni az IoT Edge futásidejű működéséről és a benne lévő összetevőkről, [olvassa el az Azure IoT Edge futásidejű és architektúrájának megismerése című témakört.](iot-edge-runtime.md)

Ez a cikk az Ubuntu 18.04 LTS virtuális gép üzembe helyezésének lépéseit sorolja fel az Azure IoT Edge futásidejű telepített és konfigurált egy előre megadott eszközkapcsolati karakterlánc használatával. A központi telepítés egy [felhőalapú](../virtual-machines/linux/using-cloud-init.md
) [Azure Resource Manager-sablon](../azure-resource-manager/templates/overview.md) használatával történik, amely et az [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) projekttárházban tart fenn.

Az első rendszerindításkor az Ubuntu 18.04 LTS virtuális gép [az Azure IoT Edge futásidejű verziójának legújabb verzióját telepíti a cloud-init segítségével.](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt) Emellett beállítja a megadott kapcsolati karakterláncot a futásidő indítása előtt, így egyszerűen konfigurálhatja és csatlakoztathatja az IoT Edge-eszközt anélkül, hogy SSH- vagy távoli asztali munkamenetet kellene elindítania. 

## <a name="deploy-using-deploy-to-azure-button"></a>Üzembe helyezés az Azure-ba való üzembe helyezés gombbal

Az [Azure-ra való üzembe helyezés gomb](../azure-resource-manager/templates/deploy-to-azure-button.md) lehetővé teszi a GitHubon karbantartott Azure Resource [Manager-sablonok](../azure-resource-manager/templates/overview.md) egyszerűsített üzembe helyezését.  Ez a szakasz bemutatja az [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) projekttártárban található Üzembe helyezés az Azure-ra gomb használatát.  


1. Egy Azure IoT Edge-kompatibilis Linux virtuális gép üzembe helyezése az iotedge-vm-deploy Azure Resource Manager sablon használatával.  Először kattintson az alábbi gombra:

    [![Üzembe helyezés az Azure Button szolgáltatásba az iotedge-vm üzembe helyezéséhez](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Az újonnan indított ablakban töltse ki a rendelkezésre álló űrlapmezőket:

    > [!div class="mx-imgBorder"]
    > [![Képernyőkép az iotedge-vm üzembe helyezési sablonról](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Előfizetés**: Az aktív Azure-előfizetés a virtuális gép üzembe helyezéséhez.

    **Erőforráscsoport**: Meglévő vagy újonnan létrehozott erőforráscsoport, amely tartalmazza a virtuális gépet és a társított erőforrásokat.

    **DNS-címkeelőtag:** A virtuális gép állomásnevének előtaghoz használt szükséges érték.

    **Felügyeleti felhasználónév**: Egy felhasználónév, amely a telepítés során root jogosultságokat kap.

    **Device Connection String**: [Eszközkapcsolat-karakterlánc](how-to-register-device.md) a tervezett [IoT Hubon](../iot-hub/about-iot-hub.md)belül létrehozott eszközhöz.

    **Virtuális gép mérete:** Az üzembe helyezendő virtuális gép [mérete](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu operációs rendszer verziója**: Az Ubuntu operációs rendszer verziója az alap virtuális gépre telepítendő.

    **Hely**: Az a [földrajzi régió,](https://azure.microsoft.com/global-infrastructure/locations/) amelybe a virtuális gépet telepíteni szeretné, ez az érték alapértelmezés szerint a kijelölt erőforráscsoport helyére vonatkozik.

    **Hitelesítés típusa**: Válassza ki **az sshPublicKey-t** vagy **a jelszót** a preferenciától függően.

    **Rendszergazdai jelszó vagy kulcs:** Az SSH nyilvános kulcs értéke vagy a jelszó értéke a hitelesítés típusának választásától függően.

    Miután az összes mező ki lett töltve, jelölje be a lap alján található jelölőnégyzetet a feltételek elfogadásához, és a Telepítés gombra a telepítés megkezdéséhez válassza a **Vásárlás** lehetőséget.

1. Ellenőrizze, hogy a telepítés sikeresen befejeződött-e.  Egy virtuálisgép-erőforrást a kijelölt erőforráscsoportba kellett volna telepíteni.  Vegye figyelembe a gép nevét, ennek `vm-0000000000000`a formátumban kell lennie. Vegye figyelembe a kapcsolódó **DNS-nevet**is, amelynek formátumban `<dnsLabelPrefix>`kell lennie. `<location>`cloudapp.azure.com.

    A **DNS-név** az Azure Portalon belül az újonnan üzembe helyezett virtuális gép **áttekintése** szakaszból szerezhető be.

    > [!div class="mx-imgBorder"]
    > [![Képernyőkép az iotedge vm dns-nevéről](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Ha a telepítés után ssh-t szeretne bevonni ebbe a virtuális gépbe, használja a társított **DNS-nevet** a következő paranccsal:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Üzembe helyezés az Azure CLI-ből

1. Győződjön meg arról, hogy telepítette az Azure CLI iot bővítményt a következőkkel:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Ezután, ha az Azure CLI-t használja az asztalon, először jelentkezzen be:

   ```azurecli-interactive
   az login
   ```

1. Ha több előfizetése van, válassza ki a használni kívánt előfizetést:
   1. Listázza az előfizetéseit:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Másolja a használni kívánt előfizetés Előfizetési azonosító mezőjét.

   1. Állítsa be a munkaelőfizetést a másolt azonosítóval:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Hozzon létre egy új erőforráscsoportot (vagy adjon meg egy meglévőt a következő lépésekben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Új virtuális gép létrehozása:

    A **hitelesítéstípusa** `password`használatához lásd az alábbi példát:

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

    Az SSH-kulccsal történő hitelesítéshez ezt megteheti **a** `sshPublicKey`, majd adja meg az SSH-kulcs értékét az **adminPasswordOrKey** paraméterben.  Erre mutat példát az alábbi ábra.

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

1. Ellenőrizze, hogy a telepítés sikeresen befejeződött-e.  Egy virtuálisgép-erőforrást a kijelölt erőforráscsoportba kellett volna telepíteni.  Vegye figyelembe a gép nevét, ennek `vm-0000000000000`a formátumban kell lennie. Vegye figyelembe a kapcsolódó **DNS-nevet**is, amelynek formátumban `<dnsLabelPrefix>`kell lennie. `<location>`cloudapp.azure.com.

    A **DNS-név** az előző lépés JSON-formátumú kimenetéből, a **nyilvános SSH-bejegyzés** részeként a **kimenetek** szakaszban érhető el.  Ennek a bejegyzésnek az értéke használható az SSH-hoz az újonnan üzembe helyezett gépbe.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    A **DNS-név** is beszerezhető az Azure Portalon belül újonnan üzembe helyezett virtuális gép **áttekintése** szakaszból.

    > [!div class="mx-imgBorder"]
    > [![Képernyőkép az iotedge vm dns-nevéről](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Ha a telepítés után ssh-t szeretne bevonni ebbe a virtuális gépbe, használja a társított **DNS-nevet** a következő paranccsal:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>További lépések

Most, hogy egy IoT Edge-eszközt létesített a futásidejű telepítve, telepítheti az [IoT Edge-modulokat.](how-to-deploy-modules-portal.md)

Ha problémái vannak az IoT Edge futásidejű megfelelő telepítésével, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Ha egy meglévő telepítést az IoT Edge legújabb verziójára szeretne frissíteni, olvassa [el az IoT Edge biztonsági démonának és futásidejű](how-to-update-iot-edge.md)verziójának frissítése című témakört.

Ha portokat szeretne megnyitni a virtuális gép SSH-n vagy más bejövő kapcsolatokon keresztültörténő eléréséhez, olvassa el az Azure virtuális gépek dokumentációját a [portok és végpontok Linux virtuális gép hez való megnyitásáról](../virtual-machines/linux/nsg-quickstart.md)
