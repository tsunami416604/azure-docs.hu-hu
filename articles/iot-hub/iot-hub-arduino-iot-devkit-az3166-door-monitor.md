---
title: E-mailt küld ajtó meg van nyitva, SendGrid szolgáltatás és az Azure Functions használatával |} Microsoft Docs
description: A mágneses érzékelő észlelés, ha az ajtó már meg van nyitva, és e-mailben értesítést küldeni az Azure Functions használatával figyelheti.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e0882a6c87454498d0d1370ee244bfffc137aafb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="door-monitor"></a>Ajtó figyelője          

A MXChip IoT DevKit beépített mágneses érzékelő tartalmazza. Ebben a projektben észlelni megléte vagy hiánya, közeli erős mágneses mező – ebben az esetben egy kis származik. állandó mágneses.

## <a name="what-you-learn"></a>Ismertetett témák

Ez a projekt elsajátíthatja:
- Hogyan használható a MXChip IoT DevKit mágneses érzékelő észleli a közeli mágneses mozgása.
- Hogyan használható a SendGrid szolgáltatás értesítést küldeni az e-mail címét.

> [!NOTE]
> A projekt gyakorlati használható:
> - A mágneses ajtó széle csatlakoztatni.
> - A mágneses megközelíti a ajtó jamb a DevKit csatlakoztatni. Megnyitása vagy záró vált az érzékelő, ami azt eredményezi, hogy az e-mailben értesítést az esemény fogadását.

## <a name="what-you-need"></a>Mi szükséges

Befejezés a [– első lépések útmutató]({{"/docs/get-started/" | absolute_url }}) számára:

* A Wi-Fi csatlakozik DevKit rendelkezik
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha még nem rendelkezik ilyennel, regisztrálni keresztül ezen módszerek egyikét:

* Aktiválja a [ingyenes 30 napos próba Microsoft Azure-fiókot](https://azureinfo.microsoft.com/us-freetrial.html).
* Jogcím a [Azure-kreditjeinek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) az MSDN webhelyen vagy a Visual Studio előfizetői esetén.

## <a name="deploy-sendgrid-service-in-azure"></a>Az Azure-ban a SendGrid szolgáltatás üzembe helyezése

[SendGrid](https://sendgrid.com/) egy felhőalapú e-mail kézbesítési platform. Ez a szolgáltatás számára e-mail értesítések küldéséhez használható.

> [!NOTE]
> Ha már telepítette a SendGrid szolgáltatás, előfordulhat, hogy folytatja közvetlenül [IoT-központ telepítése az Azure-ban](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>SendGrid központi telepítés

Azure-szolgáltatások telepítéséhez, használja a **az Azure telepítéséhez** gombra. Ez a gomb lehetővé teszi, hogy gyorsan és egyszerűen telepítése a Microsoft Azure nyílt forráskódú projekteket.

Kattintson a **az Azure telepítéséhez** gombra kattint, az alábbiakban. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Ekkor a következő oldalon megjelenik.

> [!NOTE]
> Ha nem látja a következő oldalt, esetleg először jelentkezzen be az Azure-fiókjával.

Fejezze be a bejelentkezési űrlap kitöltése:

  * **Erőforráscsoport**: hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás, vagy használjon egy meglévőt. Lásd: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

  * **Név**: A a SendGrid szolgáltatás nevét. Adjon egyedi nevet, előfordulhat, hogy más szolgáltatások eltérő.

  * **Jelszó**: A szolgáltatás megköveteli a jelszót, amelyek nem lesznek minden ebben a projektben.

  * **E-mailek**: A SendGrid szolgáltatás által küldött ellenőrzési ezt az e-mail címet.

  > [!NOTE]
  > Ellenőrizze a **rögzítés az irányítópulton** beállítás, az alkalmazás könnyebben megtalálható a jövőben.
 
![SendGrid központi telepítés](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>SendGrid API-kulcs létrehozása

Miután a telepítés sikeres, kattintson rá, és kattintson a **kezelése** gombra. A SendGrid oldalra, és ellenőrizze az e-mail címét.

![SendGrid kezelése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

A SendGrid lapján kattintson a **beállítások** > **API-kulcsokat** > **API-kulcs létrehozása**. Bemenet a **API-kulcsnév** kattintson **Létrehozás & nézet**.

![SendGrid először API létrehozása](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![SendGrid második API-t létrehozni](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Az API-kulcs csak egyszer jelenik meg. Győződjön meg arról, másolása és tárolja biztonságos helyen, mivel használatban van a következő lépéssel.

## <a name="deploy-iot-hub-in-azure"></a>Az Azure IoT-központ telepítése

Az alábbi lépéseket kiépíti az egyéb Azure IoT kapcsolódó szolgáltatások, és központi telepítése az Azure Functions ebben a projektben.

Kattintson a **az Azure telepítéséhez** gombra kattint, az alábbiakban. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Ekkor a következő oldalon megjelenik.

> [!NOTE]
> A következő lap nem jelenik meg, ha szeretne először jelentkezzen be az Azure-fiókjával.

Fejezze be a bejelentkezési űrlap kitöltése:

  * **Erőforráscsoport**: hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás, vagy használjon egy meglévőt. Lásd: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

  * **IOT-Központnév**: az IoT hub nevét. Adjon egyedi nevet, előfordulhat, hogy más szolgáltatások eltérő.

  * **Az IOT Hub Sku**: F1 ingyenes (előfizetésenként csak egy). Láthatja, hogy további információkat a [tarifacsomagot és méretet](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Az e-mailben**: ugyanazt az e-mail címet a SendGrid szolgáltatás használható legyen.

  > [!NOTE]
  > Ellenőrizze a **rögzítés az irányítópulton** beállítás, az alkalmazás könnyebben megtalálható a jövőben.
 
![Telepítési IOT hubbal](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Build, és töltse fel a kódot

### <a name="start-vs-code"></a>Indítsa el a Visual STUDIO Code

- Győződjön meg arról, hogy a DevKit **nem** csatlakoztatva a számítógéphez.
- Indítsa el a Visual STUDIO Code.
- A DevKit kapcsolódni a számítógéphez.

Visual STUDIO Code automatikusan észleli a DevKit, és egy bevezető lap megnyitása:

![VSCode](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-start.png)

> [!NOTE]
> Visual STUDIO Code elindításához jelenhet egy hibaüzenet, hogy nem található a Arduino IDE vagy a csomag kapcsolódó tábla. Ha ez a hibaüzenet jelenik meg, zárja be és a kódot, indítsa el újra az Arduino IDE és VS kódot kell megkeresheti a Arduino IDE elérési utat helyesen.

### <a name="open-arduino-examples-folder"></a>Arduino példák mappa megnyitása

Bontsa ki a bal oldali **ARDUINO példák** szakaszban, keresse meg a **MXCHIP AZ3166 példák > AzureIoT**, és válassza ki **DoorMonitor**. Ez a művelet a projektmappa azt egy új Visual STUDIO Code ablak nyílik meg.

![Mini solution példák](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Ha véletlenül bezárja a panelen, akkor nyissa meg újra. Használjon `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) nyissa meg a parancs palettát, írja be a következőt **Arduino**, keresése és válassza a **Arduino: Példák**.

### <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

A megoldás-ablakban futtassa a kiépítés feladat felhő:
- Típus `Ctrl+P` (macOS: `Cmd+P`).
- Adja meg `task cloud-provision` a megadott szövegmezőben.

A Visual STUDIO Code terminálban egy interaktív parancssori végigvezeti Önt a szükséges Azure-szolgáltatások kiépítése. Válassza ki az összes cikkeket a kért listából a korábban kiépített [IoT-központ telepítése az Azure-ban](#deploy-iot-hub-in-azure).

![Felhő kiépítése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Ha a lap lefagy a betöltés állapota, amikor megpróbál bejelentkezni az Azure-ba, tekintse meg a [gyakran ismételt kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) a probléma megoldásához. 

### <a name="build-and-upload-the-device-code"></a>Build, és töltse fel a kód

#### <a name="windows"></a>Windows

1. Használjon `Ctrl+P` futtatásához `task device-upload`.
2. A Terminálszolgáltatások konfigurációs módja megadását kéri. Ehhez az szükséges, tartsa lenyomva a gombot A, majd leküldéses és kiadási a Visszaállítás gombra. A képernyőn megjelenik a DevKit azonosítószámának és a word *konfigurációs*.

Ez az eljárás beállítja a kapcsolati karakterláncot, amely kéri le a rendszer a [rendelkezés Azure-szolgáltatások](#provision-azure-services) lépés.

VS-kódot, majd elindul, ellenőrzi, majd ismét feltölteni a Arduino vázlat a DevKit számára:

![eszköz-feltöltése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

A DevKit újraindul, és elindul a kódot.

> [!NOTE]
> Alkalmanként jelenhet meg egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez akkor fordul elő, ha a tábla csomag index nem frissül megfelelően. Ez a hiba megoldása érdekében tekintse meg a [gyakran ismételt kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. A DevKit konfigurációs üzemmódba: tartsa lenyomva a gombot A, majd leküldéses és kiadás a Visszaállítás gombra. A képernyőn megjelenik a "Konfiguráció".
2. Használjon `Cmd+P` futtatásához `task device-upload`.

Ez az eljárás beállítja a kapcsolati karakterláncot, amely kéri le a rendszer a [rendelkezés Azure-szolgáltatások](#provision-azure-services) lépés.

VS-kódot, majd elindul, ellenőrzi, majd ismét feltölteni a Arduino vázlat a DevKit számára:

![eszköz-feltöltése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

A DevKit újraindul, és elindul a kódot.

> [!NOTE]
> Alkalmanként jelenhet meg egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez akkor fordul elő, ha a tábla csomag index nem frissül megfelelően. Ez a hiba megoldása érdekében tekintse meg a [gyakran ismételt kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

A program először inicializálja a stabil mágneses mező mellett a DevKit esetén.

Az inicializálás után `Door closed` a képernyőn jelenik meg. A mágneses mező módosítása esetén, az állapot változik `Door opened`. Minden alkalommal, amikor az ajtó állapotváltozások, e-mailben értesítést kapni. (Ezek az e-mail üzenetek csak akkor kapja akár öt percet vehet.)

![Közel az érzékelő mágnesek: ajtaja zárva](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "mágnesek megközelíti a érzékelő: ajtaja zárva")

![Mágneses mozdítani az érzékelő: ajtaja nyitva](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "mágneses mozdítani az érzékelő: ajtó megnyitása")

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémába ütközik, tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) , illetve a következő csatornákon keresztül csatlakozik:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Megtanulhatta, egy DevKit eszköz csatlakoztatása az Azure IoT Suite, és a SendGrid szolgáltatás használata az e-mailt küldeni. A javasolt következő lépések a következők:

* [Az Azure IoT Suite – áttekintés](https://docs.microsoft.com/azure/iot-suite/)
* [Csatlakoztasson egy MXChip IoT DevKit eszközt a Microsoft IoT Central alkalmazás](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
