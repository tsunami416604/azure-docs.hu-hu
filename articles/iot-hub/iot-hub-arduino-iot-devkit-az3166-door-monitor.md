---
title: E-mailt küld ajtó megnyílik a SendGrid szolgáltatás és az Azure Functions használatával |} A Microsoft Docs
description: Észlelés, ha az ajtó megnyílik, és e-mail-értesítés küldése az Azure Functions használatával a mágneses érzékelő monitorozása.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 25cb3ba53c663a642f0871becbfbcab39d521c67
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437715"
---
# <a name="door-monitor"></a>Ajtó figyelője          

Az MXChip IoT DevKit tartalmaz egy beépített mágneses érzékelő. Ebben a projektben észlelni a jelenléte vagy hiánya egy közeli erős mágneses mezőt – ebben az esetben egy kisméretű származik. állandó mágneses.

## <a name="what-you-learn"></a>Ismertetett témák

Ez a projekt bemutatja:
- Hogyan észleli a közeli mágneses áthelyezését az MXChip IoT DevKit mágneses érzékelő használatával.
- Hogyan lehet a SendGrid szolgáltatás használatával értesítést küldeni az e-mail-címét.

> [!NOTE]
> Ez a projekt gyakorlati használatáért:
> - Csatlakoztassa a széle és a egy ajtót egy mágneses.
> - A fejlesztői készlet csatlakoztatása az ajtó jamb a mágneses közelében. Megnyitása vagy bezárása az ajtó aktiválják az érzékelő, a fogadó e-mail-értesítést, az esemény eredményez.

## <a name="what-you-need"></a>Mi szükséges

Fejezze be a [– első lépések útmutató](iot-hub-arduino-iot-devkit-az3166-get-started.md) való:

* A fejlesztői készlet Wi-Fi csatlakozik
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha nem rendelkezik egy, az alábbi módszerek egyikét keresztül regisztrálhatja:

* Aktiválja a [ingyenes 30 napos próba Microsoft Azure-fiók](https://azure.microsoft.com/free/).
* Jogcím a [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) MSDN vagy a Visual Studio-előfizetők.

## <a name="deploy-sendgrid-service-in-azure"></a>Az Azure-ban a SendGrid szolgáltatás üzembe helyezése

[A SendGrid](https://sendgrid.com/) egy felhőalapú e-mail kézbesítési platform. Ez a szolgáltatás használható e-mail értesítések küldéséhez.

> [!NOTE]
> Ha már üzembe helyezte a SendGrid-szolgáltatás, előfordulhat, hogy folytatja a műveletet közvetlenül [IoT Hub üzembe helyezése az Azure-ban](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>A SendGrid-telepítés

Üzembe helyezi az Azure-szolgáltatások, használja a **üzembe helyezés az Azure** gombra. Ez a gomb lehetővé teszi, hogy gyors és egyszerű üzembe helyezés a Microsoft Azure nyílt forráskódú projektek.

Kattintson a **üzembe helyezés az Azure** gomb, az alábbiakban. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Ezután a következő oldal jelenik meg.

> [!NOTE]
> Ha nem látja, akkor a következő lap, szükség lehet először jelentkezzen be az Azure-fiókjával.

Töltse ki a regisztrációs űrlapot:

  * **Erőforráscsoport**: hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás üzemeltetéséhez, vagy használjon egy meglévőt. Lásd: [erőforráscsoportok használata az Azure-erőforrások kezelése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Név**: A SendGrid-szolgáltatás nevét. Válasszon egy egyedi nevet, más szolgáltatások lehet eltérő.

  * **Jelszó**: A szolgáltatás megköveteli egy jelszót, és nem lesz az összes adat ebben a projektben.

  * **E-mailek**: A SendGrid szolgáltatás ellenőrzési küld erre a címre.

  > [!NOTE]
  > Ellenőrizze a **rögzítés az irányítópulton** lehetőség, hogy könnyebben megtalálja a későbbiekben ezt az alkalmazást.
 
![A SendGrid-telepítés](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>A SendGrid API-kulcs létrehozása

Miután az üzembe helyezés sikeres, kattintson rá, és kattintson a **kezelés** gombra. A SendGrid lapra kerül, és győződjön meg az e-mail-címét.

![A SendGrid kezelése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

A SendGrid lapon kattintson a **beállítások** > **API-kulcsok** > **API-kulcs létrehozása**. Bemenet a **API-kulcs neve** kattintson **Létrehozás & nézet**.

![A SendGrid API először létrehozása](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![A SendGrid API második létrehozása](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Az API-kulcs csak egyszer jelenik meg. Győződjön meg arról, másolása és tárolja biztonságos helyen, mert azt már használja a következő lépésben.

## <a name="deploy-iot-hub-in-azure"></a>Az Azure IoT Hub üzembe helyezése

Az alábbi lépéseket is üzembe helyezi a többi Azure IoT kapcsolódó szolgáltatások és az Azure Functions üzembe helyezése a projekthez.

Kattintson a **üzembe helyezés az Azure** gomb, az alábbiakban. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Ezután a következő oldal jelenik meg.

> [!NOTE]
> Ha nem látja a következő lap, szükség lehet először jelentkezzen be az Azure-fiókjával.

Töltse ki a regisztrációs űrlapot:

  * **Erőforráscsoport**: hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás üzemeltetéséhez, vagy használjon egy meglévőt. Lásd: [erőforráscsoportok használata az Azure-erőforrások kezelése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **IOT Hub nevére**: az IoT hub nevét. Válasszon egy egyedi nevet, más szolgáltatások lehet eltérő.

  * **IOT Hub-termékváltozat**: F1 (előfizetésenként csak egy) díjmentes. Megtekintheti, további díjszabási információk [tarifacsomagot és méretet](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **E-mailből**: Ez legyen a SendGrid szolgáltatás beállítása során használt azonos e-mail-címre.

  > [!NOTE]
  > Ellenőrizze a **rögzítés az irányítópulton** lehetőség, hogy könnyebben megtalálja a későbbiekben ezt az alkalmazást.
 
![IoTHub-telepítés](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Hozhat létre és töltse fel a kódot

### <a name="start-vs-code"></a>Indítsa el a VS Code

- Győződjön meg arról, hogy a fejlesztői készlet **nem** csatlakoztatva a számítógéphez.
- Indítsa el a VS Code.
- A fejlesztői készlet csatlakoztatása a számítógéphez.

> [!NOTE]
> Ha elindítja a VS Code, egy hibaüzenet, hogy nem található az Arduino IDE vagy kapcsolódó tábla csomag kaphat. Ha ezt a hibaüzenetet kapja, zárja be a VS Code, indítsa el újra az Arduino IDE és a VS Code kell keresse meg az Arduino IDE elérési utat helyesen.

### <a name="open-arduino-examples-folder"></a>Arduino példák mappa megnyitása

Bontsa ki a bal oldali **ARDUINO példák** területén keresse meg a **példák az MXCHIP AZ3166 > AzureIoT**, és válassza ki **DoorMonitor**. Ez a művelet egy új VS Code-ablak megnyílik, a projektmappa.

![Mini-solution-példák](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> A példában a parancskatalógus is megnyithatja. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

### <a name="provision-azure-services"></a>Azure-szolgáltatások üzembe helyezése

A megoldás ablakban futtassa a felhő kiépítési tevékenység:
- Típus `Ctrl+P` (macOS: `Cmd+P`).
- Adja meg `task cloud-provision` a megadott szövegmezőben.

A VS Code terminálban egy interaktív parancssori végigvezeti a szükséges Azure-szolgáltatások kiépítése. Válassza ki az összes cikkeket a kért listából a korábban kiépített [IoT Hub üzembe helyezése az Azure-ban](#deploy-iot-hub-in-azure).

![Felhő üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Ha az oldal lefagy a betöltés állapota, amikor megpróbál bejelentkezni Azure-ba, olvassa el [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) a probléma megoldásához. 

### <a name="build-and-upload-the-device-code"></a>Hozhat létre és töltse fel az eszköz kódot

#### <a name="windows"></a>Windows

1. Használat `Ctrl+P` futtatásához `task device-upload`.
2. A terminálban kéri, hogy adja meg a konfigurációs mód. Ehhez A gombot lenyomva, majd küldje le, és engedje a Visszaállítás gombra. A képernyőn megjelenik a DevKit azonosító száma és a word *konfigurációs*.

Ez az eljárás beállítja a kapcsolati karakterláncot, amelyet a rendszer beolvassa a [üzembe helyezése Azure-szolgáltatások](#provision-azure-services) . lépés.

A VS Code, majd elindul, ellenőrzi, és töltse fel az Arduino vázlat, a fejlesztői készlet:

![eszköz-feltöltést](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

> [!NOTE]
> Bizonyos esetekben előfordulhat, hogy kap egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez akkor fordul elő, ha a tábla csomagindexet nem megfelelően frissül. Ez a hiba elhárításához tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. A fejlesztői készlet konfigurációs módba: tartsa lenyomva A gombra, majd leküldéses és -kiadási a Visszaállítás gombra. A képernyőn megjelenik a "Konfiguráció".
2. Használat `Cmd+P` futtatásához `task device-upload`.

Ez az eljárás beállítja a kapcsolati karakterláncot, amelyet a rendszer beolvassa a [üzembe helyezése Azure-szolgáltatások](#provision-azure-services) . lépés.

A VS Code, majd elindul, ellenőrzi, és töltse fel az Arduino vázlat, a fejlesztői készlet:

![eszköz-feltöltést](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

> [!NOTE]
> Bizonyos esetekben előfordulhat, hogy kap egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez akkor fordul elő, ha a tábla csomagindexet nem megfelelően frissül. Ez a hiba elhárításához tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

A program először inicializálja a DevKit zajok mellett egy stabil mágneses mező esetén.

Az inicializálás után `Door closed` a képernyő jelenik meg. A mágneses mezőben változás történik, ha az állapot változik `Door opened`. Minden alkalommal, amikor az ajtó állapotváltozásait, e-mailben értesítést kap. (A ezen e-mailek fogadását akár öt percig is eltarthat.)

![Az érzékelő közeli mágnesek: ajtaja zárva](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "közel az érzékelő mágnesek: ajtaja zárva")

![Mágneses érzékelő mozdítani: ajtó megnyitott](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "mágneses érzékelő mozdítani: ajtó megnyitása")

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy létesítsen a következő csatornákon:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Megtanulhatta, hogyan egy DevKit eszköz csatlakoztatása az Azure IoT távoli figyelési megoldásgyorsító és e-mail küldése a SendGrid szolgáltatás használatával. Íme a javasolt következő lépések:

* [Az Azure IoT távoli figyelési megoldásgyorsító áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
