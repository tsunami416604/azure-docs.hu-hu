---
title: Azure IoT Edge eszköz hozzáadása az Azure IoT Centralhoz | Microsoft Docs
description: Operátorként vegyen fel egy Azure IoT Edge eszközt az Azure-IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893479"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Oktatóanyag: Azure IoT Edge-eszköz hozzáadása az Azure IoT Central-alkalmazáshoz (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és konfigurálhat egy *Azure IOI Edge-eszközt* a Microsoft Azure IoT Central alkalmazáshoz. Ebben az oktatóanyagban egy Azure IoT Edge engedélyezett linuxos virtuális gépet választottunk az Azure Marketplace-ről.

Ez az oktatóanyag két részből áll:

* Először is, mint operátor, megtudhatja, hogyan végezheti el a felhőben való első üzembe helyezést egy Azure IoT Edge eszközön.
* Ezt követően megtudhatja, hogyan végezheti el az eszközök első üzembe helyezését egy Azure IoT Edge eszközön.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új Azure IoT Edge-eszköz hozzáadása
> * A Azure IoT Edge eszköz konfigurálása az SAS-kulcs használatának elősegítése érdekében
> * Irányítópultok, modul állapotának megtekintése IoT Central
> * Parancsok küldése a Azure IoT Edge eszközön futó modulnak
> * A Azure IoT Edge eszközön futó modul tulajdonságainak beállítása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure IoT Central-alkalmazásra. Ezt a rövid útmutatót követve [hozzon létre egy Azure IoT Central alkalmazást](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge regisztrációs csoport engedélyezése
Engedélyezze a SAS-kulcsokat Azure IoT Edge beléptetési csoport számára az adminisztráció lapon.

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>A felhő első Azure IoT Edge eszköz kiépítés   
Ebben a szakaszban egy új Azure IoT Edge eszközt fog létrehozni a **környezeti érzékelő sablonnal** , és kiépít egy eszközt. Kattintson a bal oldali navigációs sávon a Devices (eszközök) elemre, majd a környezeti érzékelő sablon elemre. 

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Kattintson az **+ új** elemre, és adja meg az eszköz azonosítóját és nevét, amely megfelel Önnek. 

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Az eszköz **regisztrált** módba kerül.

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Azure IoT Edge engedélyezett linuxos virtuális gép üzembe helyezése

>Megjegyzés: bármelyik gépet vagy eszközt használhat. Operációs rendszer: Linux vagy Windows)

Ebben az oktatóanyagban egy Azure IoT-kompatibilis linuxos virtuális gépet választottunk, amely az Azure-on hozható létre. Ekkor megnyílik az [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) -en, és kattintson a **Letöltés** gombra. 

![Azure Piactér](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Kattintson a **Folytatás** gombra.

![Azure Piactér](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


A rendszer elvégzi a Azure Portal. Kattintson a **Létrehozás** gombra

![Azure Piactér](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Válassza az előfizetés lehetőséget, hozzon létre egy új erőforráscsoportot, és válassza ki az USA 2. nyugati régióját a virtuális gépek rendelkezésre állásához, majd adja meg a felhasználó Jegyezze fel a felhasználót, és a későbbi lépésekhez jelszóra lesz szükség. Kattintson a **felülvizsgálat + létrehozás** gombra.

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Az ellenőrzés után kattintson a **Létrehozás** gombra.

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Az erőforrások létrehozása néhány percet vesz igénybe. Kattintson a Ugrás az **erőforráshoz** lehetőségre

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Virtuális gép kiépítése Azure IoT Edge eszközként 

A bal oldali navigációs sávon a támogatás + hibaelhárítás alatt kattintson Serial console

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Az alábbihoz hasonló képernyő jelenik meg

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Nyomja le az ENTER billentyűt, és adja meg a felhasználónevet és a jelszót. 

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Parancs futtatása rendszergazdaként vagy gyökérként futtassa a következő parancsot: **sudo Su –**

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Azure IoT Edge futtatókörnyezet verziójának keresése. A GA aktuális verziója 1.0.8

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Telepítse a Vim-szerkesztőt, vagy használja a nanot, ha az Ön igényei. 

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Azure IoT Edge config. YAML fájl szerkesztése

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Görgessen le, és jegyezze fel a YAML fájljának kapcsolatok sztring részét. 

**Előtt**

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Ezután** (nyomja le az ESC billentyűt, és nyomja le az a kisbetű gombot a Szerkesztés megkezdéséhez)

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

A YAML fájl nem Megjegyzés szimmetrikus kulcsának része. 

**Előtt**

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Után**

![Ubuntu rendszerű virtuális gép](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Nyissa meg a IoT Central és a hatókör-azonosító, az eszköz azonosítója és a szimmetrikus kulcs beszerzése a Azure IoT Edge eszköz ![eszköz csatlakoztatása](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Lépjen a Linux mezőbe, és cserélje le a hatókör-azonosítót, a regisztrációs azonosítót az eszköz azonosítójával és a szimmetrikus kulccsal

Nyomja le az **ESC** billentyűt és írja be a következőt **:** majd nyomja le az **ENTER** billentyűt a módosítások mentéséhez

Indítsa újra Azure IoT Edge a módosítások feldolgozásához, majd nyomja le az **ENTER** billentyűt

![Eszköz csatlakoztatása](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Type: **iotedge lista**, néhány percet vesz igénybe, három modult fog látni

![Eszköz csatlakoztatása](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Device Explorer 

IoT Central az eszköz üzembe helyezése kiépített állapotba kerül

![Eszköz csatlakoztatása](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

A modulok lap megjeleníti az eszköz és a modul állapotát IoT Central 

![Eszköz csatlakoztatása](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


A felhő tulajdonságai egy űrlapon jelennek meg (az előző lépésekben létrehozott sablonból). Adja meg az értékeket, majd kattintson a **Mentés**gombra. 

![Eszköz csatlakoztatása](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Irányítópult csempéje

![Eszköz csatlakoztatása](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Új Azure IoT Edge-eszköz hozzáadása
* A Azure IoT Edge eszköz konfigurálása az SAS-kulcs használatának elősegítése érdekében
* Irányítópultok, modul állapotának megtekintése IoT Central
* Parancsok küldése a Azure IoT Edge eszközön futó modulnak
* A Azure IoT Edge eszközön futó modul tulajdonságainak beállítása

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan dolgozhat a Azure IoT Edge-eszközök felügyeletével IoT Centralban, itt látható a következő lépés:

<!-- Next how-tos in the sequence -->

Transzparens átjáró konfigurálása, az oktatóanyag követése

> [!div class="nextstepaction"]
> [Transzparens átjáró konfigurálása](../../iot-edge/how-to-create-transparent-gateway.md)
