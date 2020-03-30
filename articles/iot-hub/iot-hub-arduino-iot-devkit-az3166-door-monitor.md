---
title: E-mail küldése az Azure Functions használatával nyitott ajtó esetén
description: Figyelje a mágneses érzékelőt, hogy észlelje, mikor nyit egy ajtót, és az Azure Functions segítségével küldjön egy e-mail értesítést.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 6bebe8ac6b9869466938600d6267fd0062c84477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977296"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Ajtófigyelő – Az Azure Functions és a SendGrid használatával e-mailt küldhet, ha egy ajtó ki nyílik           

Az MXChip IoT DevKit beépített mágneses érzékelőt tartalmaz. Ebben a projektben egy közeli erős mágneses mező jelenlétét vagy hiányát érzékeli- ebben az esetben egy kis, állandó mágnesből.

## <a name="what-you-learn"></a>Ismertetett témák

Ebben a projektben megtanulod:
- Az MXChip IoT DevKit mágneses érzékelőjének használata egy közeli mágnes mozgásának érzékelésére.
- Hogyan kell használni a SendGrid szolgáltatást, hogy értesítést küldjön az e-mail címére.

> [!NOTE]
> A projekt gyakorlati használatához hajtsa végre a következő feladatokat:
> - Szereljen mágnest az ajtó szélére.
> - Szerelje fel a DevKit-et az ajtófélfára a mágnes közelében. Az ajtó nyitása vagy bezárása elindítja az érzékelőt, így e-mailben értesítést kap az eseményről.

## <a name="what-you-need"></a>Mi szükséges

Az [első lépések útmutatójának](iot-hub-arduino-iot-devkit-az3166-get-started.md) befejezése:

* A DevKit csatlakoztatása Wi-Fi-hálózathoz
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha nem rendelkezik ilyen, az alábbi módszerek egyikével regisztrálhat:

* Aktiválja [az ingyenes 30 napos próbaverziós Microsoft Azure-fiókot.](https://azure.microsoft.com/free/)
* Igényelje [Az Azure-kreditet,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ha Ön MSDN- vagy Visual Studio-előfizető.

## <a name="deploy-the-sendgrid-service-in-azure"></a>A SendGrid szolgáltatás üzembe helyezése az Azure-ban

[A SendGrid](https://sendgrid.com/) egy felhőalapú e-mail kézbesítési platform. Ez a szolgáltatás e-mail értesítések küldésére szolgál.

> [!NOTE]
> Ha már telepített egy SendGrid-szolgáltatást, közvetlenül az [IoT Hub üzembe helyezéséhez léphet az Azure-ban.](#deploy-iot-hub-in-azure)

### <a name="sendgrid-deployment"></a>SendGrid központi telepítése

Az Azure-szolgáltatások kiépítéséhez használja a **Deploy to Azure** gombot. Ez a gomb lehetővé teszi a nyílt forráskódú projektek gyors és egyszerű telepítését a Microsoft Azure-ba.

Kattintson az **alábbi Üzembe helyezés az Azure-ba** gombra. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Ha még nem jelentkezett be az Azure-fiókjába, jelentkezzen be most. 

Most megjelenik a SendGrid regisztrációs űrlap.

![SendGrid központi telepítése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Töltse ki a regisztrációs űrlapot:

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás üzemeltetéséhez, vagy használjon egy meglévőt. Lásd: [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.](../azure-resource-manager/management/manage-resource-groups-portal.md)

   * **Név**: A SendGrid szolgáltatás neve. Válasszon egy egyedi nevet, amely eltér a többi szolgáltatástól.

   * **Jelszó**: A szolgáltatáshoz jelszó szükséges, amelyet ebben a projektben semmire sem használnak.

   * **E-mail**: A SendGrid szolgáltatás erre az e-mail címre küldi az ellenőrzést.

Jelölje be a **Pin to dashboard** opciót, hogy az alkalmazás könnyebben megtalálható legyen a jövőben, majd kattintson a **Vásárlás** gombra a bejelentkezési űrlap elküldéséhez.
 
### <a name="sendgrid-api-key-creation"></a>SendGrid API-kulcs létrehozása

A telepítés befejezése után kattintson rá, majd a **Kezelés** gombra. Megjelenik a SendGrid-fiók lapja, ahol igazolnia kell e-mail címét.

![SendGrid-kezelés](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

A SendGrid lapon kattintson a **Beállítások** > **API-kulcsok** > **létrehozása API-kulcs**elemre.

![SendGrid először hozzon létre API-t](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Az **API-kulcs létrehozása** lapon adja meg az **API-kulcs nevét,** és kattintson **& nézet létrehozása gombra.**

![SendGrid második létrehozása API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Az API-kulcs csak egyszer jelenik meg. Ügyeljen arra, hogy másolja és tárolja biztonságosan, mivel használják a következő lépésben.

## <a name="deploy-iot-hub-in-azure"></a>Az IoT Hub üzembe helyezése az Azure-ban

A következő lépések más Azure IoT-szolgáltatások kiépítésére és az Azure Functions központi telepítésére kerülnek a projekthez.

Kattintson az **alábbi Üzembe helyezés az Azure-ba** gombra. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Megjelenik a regisztrációs űrlap.

![IoTHub üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Töltse ki a mezőket a regisztrációs űrlapon.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás üzemeltetéséhez, vagy használjon egy meglévőt. Lásd: [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.](../azure-resource-manager/management/manage-resource-groups-portal.md)

   * **Iot Hub name**: Az IoT hub neve. Válasszon egy egyedi nevet, amely eltér a többi szolgáltatástól.

   * **Iot Hub Sku**: F1 (előfizetésenként egy) ingyenes. További díjszabási információkat az [árképzési oldalon](https://azure.microsoft.com/pricing/details/iot-hub/)láthat.

   * **Feladó e-mail:** Ennek a mezőnek meg kell egyeznie a SendGrid szolgáltatás beállításakor használt e-mail címmel.

Jelölje be a **Pin to dashboard** opciót, hogy az alkalmazás könnyebben megtalálható legyen a jövőben, majd kattintson a **Vásárlás** gombra, ha készen áll a következő lépés folytatására.
 
## <a name="build-and-upload-the-code"></a>A kód összeállítása és feltöltése

Ezután töltse be a mintakódot a VS-kódba, és tegye ki a szükséges Azure-szolgáltatásokat.

### <a name="start-vs-code"></a>Vs-kód indítása

- Győződjön meg arról, hogy a DevKit **nincs** csatlakoztatva a számítógéphez.
- Indítsa el a VS Code-ot.
- Csatlakoztassa a DevKit-et a számítógéphez.

> [!NOTE]
> A VS Code indításakor hibaüzenet jelenhet meg arról, hogy nem találja az Arduino IDE-t vagy a kapcsolódó táblacsomagot. Ha ez a hibaüzenet jelenik meg, zárja be a VS Code-ot, indítsa el újra az Arduino IDE-t, és a VS Code megfelelően keresse meg az Arduino IDE elérési útját.

### <a name="open-arduino-examples-folder"></a>Arduino Examples mappa megnyitása

Bontsa ki a bal oldali **ARDUINO EXAMPLES szakaszt,** keresse meg **az MXCHIP AZ3166 > AzureIoT példái című szakaszt,** és válassza a **DoorMonitor**lehetőséget. Ez a művelet megnyit egy új VS Code ablakot egy projektmappával.

![mini-megoldás-példák](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

A példaalkalmazást a parancspalettáról is megnyithatja. A `Ctrl+Shift+P` parancspaletta megnyitásához használja a (macOS: `Cmd+Shift+P`) parancsot, írja be az **Arduino**parancsot, majd keresse meg és válassza az **Arduino: Examples lehetőséget.**

### <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

A megoldásablakban futtassa a felhőalapú létesítési feladatot:
- Típus `Ctrl+P` (macOS: `Cmd+P`).
- Írja `task cloud-provision` be a megadott szövegmezőbe.

A VS Code terminálon egy interaktív parancssor imitáta a szükséges Azure-szolgáltatások kiépítésén keresztül. Válassza ki az összes ugyanazokat az elemeket a kérdéslistából, amelyet korábban kiépített az [Azure-beli IoT Hub üzembe helyezésében.](#deploy-iot-hub-in-azure)

![Felhőkiépítés](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Ha az oldal lefagy a betöltési állapot, amikor megpróbál bejelentkezni az Azure-ba, olvassa el [az IoT DevKit gyIK "page lefagy bejelentkezéskor" című részét.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) 

### <a name="build-and-upload-the-device-code"></a>Az eszközkód összeállítása és feltöltése

Ezután töltse fel az eszköz kódját.

#### <a name="windows"></a>Windows

1. A `Ctrl+P` futtatásához `task device-upload`használható.

2. A terminál kéri, hogy lépjen be a konfigurációs módba. Ehhez tartsa lenyomva az A gombot, majd nyomja meg és engedje fel a reset gombot. A képernyőn megjelenik a DevKit azonosító száma és a *Konfiguráció*szó.

#### <a name="macos"></a>macOS

1. Helyezze a DevKit-et konfigurációs módba: Tartsa lenyomva az A gombot, majd nyomja meg és engedje fel a reset gombot. A képernyőn a "Configuration" (Konfiguráció) látható.

2. Kattintson `Cmd+P` `task device-upload`ide a futtatásához.

#### <a name="verify-upload-and-run-the-sample-app"></a>A mintaalkalmazás ellenőrzése, feltöltése és futtatása

Az [Azure-szolgáltatások létesítése](#provision-azure-services) lépésből lekért kapcsolati karakterlánc most be van állítva. 

A VS Code ezután megkezdi az Arduino vázlat ellenőrzését és feltöltését a DevKitbe.

![eszköz-feltöltés](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

A DevKit újraindul, és elindítja a kód futtatását.

> [!NOTE]
> Előfordulhat, hogy a "Hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez a hiba akkor fordul elő, ha a táblacsomag indexe nem frissül megfelelően. A hiba megoldásához tekintse meg [az IoT DevKit gyakori kérdések fejlesztési szakaszát.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)

## <a name="test-the-project"></a>A projekt tesztelése

A program először akkor inicializálódik, amikor a DevKit stabil mágneses mező jelenlétében van.

Inicializálás után `Door closed` megjelenik a képernyőn. Ha változás van a mágneses mezőben, `Door opened`az állapot a . Minden alkalommal, amikor az ajtó állapota megváltozik, e-mailben értesítést kap. (Ezek az e-mail üzenetek fogadása akár öt percet is igénybe vehet.)

![Mágnesek az érzékelő közelében: Ajtó zárva](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Mágnesek az érzékelő közelében: Ajtó zárva")

![A mágnes eltávolodott az érzékelőtől: Az ajtó kinyílt](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "A mágnes eltávolodott az érzékelőtől: Az ajtó kinyílt")

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Ha problémákat tapasztal, olvassa el az [IoT DevKit gyakori kérdéseket,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy csatlakozzon a következő csatornákon:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan csatlakoztathat egy DevKit-eszközt az Azure IoT Remote Monitoring megoldásgyorsítóhoz, és a SendGrid szolgáltatás használatával e-mailt küldhet. A következő javasolt lépések a következők:

* [Az Azure IoT remote monitoring megoldásgyorsító – áttekintés](https://docs.microsoft.com/azure/iot-suite/)
* [MXChip IoT DevKit-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](/azure/iot-central/core/howto-connect-devkit)
