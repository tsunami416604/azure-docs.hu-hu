---
title: Azure IoT Edge eszköz hozzáadása az Azure IoT Centralhoz | Microsoft Docs
description: Operátorként vegyen fel egy Azure IoT Edge eszközt az Azure IoT Central alkalmazásba
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f16db7ebff087b164228f2b23d6fa7ec302705bb
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406334"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Oktatóanyag: Azure IoT Edge-eszköz hozzáadása az Azure IoT Central-alkalmazáshoz

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá és konfigurálhat egy Azure IoT Edge-eszközt az Azure IoT Central-alkalmazáshoz. Ebben az oktatóanyagban egy IoT Edge-kompatibilis linuxos virtuális gépet választottunk az Azure Marketplace-ről.

Ez az oktatóanyag két részből áll:

* Először is, mint operátor, megtudhatja, hogyan végezheti el a felhőben való első üzembe helyezést egy IoT Edge eszközön.
* Ezt követően megtudhatja, hogyan végezheti el az eszközök első kiépítési módját IoT Edge eszközön.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új IoT Edge-eszköz hozzáadása
> * A IoT Edge eszköz konfigurálása a közös hozzáférésű aláírási (SAS-) kulcs használatával történő kiépítéshez
> * Az irányítópultok és a modul állapotának megtekintése IoT Central
> * Parancsok küldése a IoT Edge eszközön futó modulnak
> * A IoT Edge eszközön futó modul tulajdonságainak beállítása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure IoT Central-alkalmazásra. Ezt a rövid útmutatót követve [hozzon létre egy Azure IoT Central alkalmazást](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge regisztrációs csoport engedélyezése
Az **Adminisztráció** lapon engedélyezze a sas-kulcsok Azure IoT Edge beléptetési csoport számára lehetőséget.

![Képernyőkép az adminisztráció lapról, az eszköz csatlakoztatása kijelölve](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>"Felhőbeli első" Azure IoT Edge eszköz kiépítése  
Ebben a szakaszban egy új IoT Edge eszközt hoz létre a környezeti érzékelő sablonnal, és kiépít egy eszközt. Válassza az **eszközök** > **környezeti érzékelő sablon**lehetőséget. 

![Képernyőfelvétel az eszközök lapról, a környezeti érzékelő sablonnal kiemelve](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Válassza az **+ új**lehetőséget, majd adja meg a választott eszköz azonosítóját és nevét. Kattintson a **Létrehozás** gombra.

![Képernyőkép az új eszköz létrehozása párbeszédpanelről, az eszköz azonosítójával és a Kiemelt létrehozással](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Az eszköz **regisztrált** módba kerül.

![Képernyőfelvétel a környezeti érzékelő sablon oldaláról, az eszköz állapota kiemelve](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>IoT Edge engedélyezett linuxos virtuális gép üzembe helyezése

> [!NOTE]
> Választhat, hogy bármilyen gépet vagy eszközt használ-e. Az operációs rendszer lehet Linux vagy Windows.

Ebben az oktatóanyagban egy Azure IoT-kompatibilis linuxos virtuális gépet használunk, amelyet az Azure-on hoztak létre. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)-en válassza a **Letöltés most**lehetőséget. 

![Képernyőkép az Azure Marketplace-ről, a GET IT most kiemelve](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Válassza a **Folytatás** elemet.

![Képernyőkép: az alkalmazás létrehozása az Azure-ban párbeszédpanelen, a folytatás kiemelve](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


A rendszer elvégezte a Azure Portal. Kattintson a **Létrehozás** gombra.

![Képernyőkép a Azure Portalról a Kiemelt létrehozással](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Válassza az **előfizetés**lehetőséget, hozzon létre egy új erőforráscsoportot, és válassza az USA **2. nyugati** RÉGIÓja lehetőséget a virtuális gépek rendelkezésre állásához. Ezután adja meg a felhasználói és a jelszó adatait. Ezek a későbbi lépésekhez szükségesek, ezért ne feledje. Válassza az **Áttekintés + létrehozás** lehetőséget.

![Képernyőkép a virtuális gép részleteinek létrehozásáról, különböző lehetőségekkel kiemelve](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Az érvényesítés után válassza a **Létrehozás**lehetőséget.

![Képernyőkép a virtuális gép létrehozása lap létrehozásáról és a kijelöltek létrehozásáról](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Az erőforrások létrehozása néhány percet vesz igénybe. Válassza **az Ugrás erőforráshoz**lehetőséget.

![Képernyőkép az üzembe helyezés befejezéséről lap az erőforrás kiemelése lehetőséggel](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Virtuális gép kiépítése IoT Edge eszközként 

A **támogatás + hibaelhárítás**területen válassza a **Serial Console**lehetőséget.

![Képernyőkép a támogatási és hibaelhárítási lehetőségekről, Serial console kiemelve](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

A következőhöz hasonló képernyő jelenik meg:

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Nyomja le az ENTER billentyűt, adja meg a felhasználónevet és a jelszót, majd nyomja le ismét az ENTER billentyűt. 

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Ha rendszergazdaként szeretné futtatni a parancsot (felhasználó "root"), írja be a következőt: **sudo Su –**

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Keresse meg a IoT Edge futtatókörnyezet verzióját. Az írás időpontjában az aktuális GA-verzió a 1.0.8.

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Telepítse a Vim-szerkesztőt, vagy használja a nanot, ha szeretné. 

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Szerkessze a IoT Edge config. YAML fájlt.

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Görgessen lefelé, és jegyezze fel a YAML fájljának a kapcsolatok sztring részét. 

**Előtt**

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Ezután** (nyomja le az ESC billentyűt, és nyomja le az a kisbetűs gombot a Szerkesztés megkezdéséhez.)

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

A YAML fájl szimmetrikus kulcs részének megjegyzése. 

**Előtt**

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Után**

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Lépjen IoT Central. Szerezze be a IoT Edge eszköz hatókör-AZONOSÍTÓját, eszköz-AZONOSÍTÓját és szimmetrikus kulcsát.
![képernyőkép a IoT Centralről, különböző eszköz-csatlakoztatási lehetőségekkel](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Lépjen a Linux rendszerű számítógépre, és cserélje le a hatókör-azonosítót és a regisztrációs azonosítót az eszköz azonosítójával és a szimmetrikus kulccsal.

Nyomja le az ESC billentyűt, és írja be a következőt **: wq!** . A módosítások mentéséhez nyomja le az ENTER billentyűt.

Indítsa újra IoT Edge a módosítások feldolgozásához, majd nyomja le az ENTER billentyűt.

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Írja be a **iotedge listát**. Néhány perc elteltével az üzembe helyezett három modul látható.

![A konzol képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Device Explorer 

IoT Central az eszköz kiépített állapotba kerül.

![Képernyőkép a IoT Central eszközök lehetőségeiről, az eszköz állapota kiemelve](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

A **modulok** lapon látható az eszköz és a modul állapota IoT Centralon. 

![IoT Central modulok lap képernyőképe](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


A felhő tulajdonságai az előző lépésekben létrehozott sablon alapján jelennek meg az űrlapon. Adja meg az értékeket, majd kattintson a **Mentés**gombra. 

![A Linux Edge-eszköz űrlapjának képernyőképe](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Itt látható egy, az irányítópult csempéje formájában megjelenített nézet.

![Képernyőkép a Linux Edge-eszközök irányítópultjának csempéről](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan dolgozhat és kezelhet IoT Edge-eszközöket a IoT Centralban, a következő lépés a javasolt lépés:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Transzparens átjáró konfigurálása](../../iot-edge/how-to-create-transparent-gateway.md)
