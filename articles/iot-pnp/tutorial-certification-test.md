---
title: Tanúsítsa a IoT Plug and Play Preview-eszközét | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan adható hozzá a termékinformáció az Azure Certified for IoT-Device cataloghoz, hogyan csatlakoztatható az eszköz az Azure IoT Certificate Service-hez, majd futtassa a IoT Plug and Play minősítési teszteket.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550162"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Oktatóanyag: a IoT Plug and Play előnézeti eszköz tanúsítása

Ha közzé szeretne tenni egy IoT Plug and Play előnézeti eszközt az [Azure Certified for IoT Device Catalog szolgáltatásban](https://aka.ms/iotdevcat), akkor meg kell adnia a minősítési tesztek készletét. Az [Azure Certified for IoT](https://aka.ms/ACFI) portál használatával beküldheti az eszközét minősítésre. Az [Azure IoT Certification Service](https://aka.ms/azure-iot-aics) futtatja a minősítési teszteket.

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Mik a IoT Plug and Play a minősítési követelmények.
> * A termék nevének és információinak hozzáadása a portálhoz.
> * A IoT Plug and Play-felületek kapcsolódása és felderítése.
> * A IoT Plug and Play felületek áttekintése és a minősítési tesztek futtatása.
> * A Certified IoT Plug and Play eszközének közzététele a katalógusban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Microsoft partner Center-fiók.
* A Microsoft Partner Network azonosítója.

További információkért lásd: Hogyan lehet bevezetni [Az Azure Certified for IoT](howto-onboard-portal.md) portált.

## <a name="certification-requirements"></a>Minősítési követelmények

A IoT Plug and Play eszközön való hitelesítéséhez az eszköznek meg kell felelnie az alábbi követelményeknek:

* A IoT Plug and Play eszköz kódját telepíteni kell az eszközön.
* A IoT Plug and Play eszköz kódját az Azure IoT SDK-val együtt kell felépíteni.
* Az eszköz kódjának támogatnia kell az [Azure IoT hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
* Az eszköz kódjának meg kell valósítania az [eszköz információs felületét](concepts-common-interfaces.md).
* A képesség modell és az eszköz kódja IoT Centralekkel működik.

A katalógusban jelenleg található összes eszköz előzetesen hitelesített IoT Plug and Play-eszközöknek tekintendő. Nem garantálja a IoT végső termékének minőségét és megfelelőségét Plug and Play szoftver-összetevők, például az SDK és a digitális kettős definíciós nyelv esetében.

Az összes előzetesen hitelesített IoT Plug and Play eszköznek újra hitelesítenie kell a IoT-Plug and Play általánosan elérhetővé tételével a Microsoft által biztosított minősítési követelmények és a szoftverek összetevőinek végleges verziója alapján.

## <a name="add-product-name"></a>Terméknév hozzáadása

A **termék** az ügyfél által megvásárolható termékmodell felhasználóbarát neve. Termék hozzáadása:

1. Jelentkezzen be az [Azure Certified for IoT](https://aka.ms/ACFI) portálra.
1. Nyissa meg a portál **termékek** lapját a bal oldali menüben, és válassza az **+ új**lehetőséget.
1. Adja meg a felhasználóbarát terméknév nevét, és válassza a **Létrehozás**lehetőséget. Az itt megadott név különbözik az eszköz-katalógusban megjelenő névvel.

## <a name="add-product-information"></a>Termék adatainak hozzáadása

Miután sikeresen létrehozta a terméket a portálon, a termék megjelenik a **termékek** lapon. A termék adatainak hozzáadása:

1. Válassza ki a Product (termék) oszlopban a **termék** lapon található létrehozott termék hivatkozást. Az állapotnak vázlat állapotban kell lennie.
1. Válassza a **termék adatai** fejléc melletti **Szerkesztés** hivatkozást. Adja meg a termék információit a termék adatai lapon, és győződjön meg arról, hogy az összes kötelező mezőt elvégezte.
1. Kattintson a **Mentés** gombra. A **Mentés** gomb csak akkor jelenik meg, ha az összes kötelező mezőt kitöltötte. Ha a mezők hiányosak, a gomb azt mondja, hogy **később a Mentés és a Befejezés**.
1. Tekintse át a megadott tartalmat. Fejezze be az összes kötelező mezőt az eszköz katalógusba való közzétételéhez. Bármikor visszatérhet, ha a Product Information (termék **adatai** ) fejléc melletti **Szerkesztés** hivatkozásra kattint

## <a name="connect-and-discover-interfaces"></a>Kapcsolódási és felderítési felületek

A minősítési tesztek futtatásához csatlakoztasson egy eszközt a portálon elérhető [Azure IoT Certification Service](https://aka.ms/azure-iot-aics) (AICS) szolgáltatáshoz.

Ezek a lépések egy lépésből állnak a minősítési tesztek futtatásához, és nem szükséges a termék-eszköz kódjának módosítása. A kezdéshez kövesse az alábbi lépéseket a AICS való kapcsolódáshoz:

1. Jelentkezzen be a portálra a partner Center-fiók használatával.
1. A minősítési folyamat elindításához kattintson a **Kapcsolódás + tesztelés** elemre.
1. Válassza ki azt a [hitelesítési módszert](../iot-dps/concepts-security.md#attestation-mechanism) , amellyel az eszközt az [Azure IoT hub Device Provisioning Service](../iot-dps/about-iot-dps.md)használatával kiépítheti az AICS.
   * Ha [X. 509 tanúsítványt](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)használ, töltse fel a generált X. 509 tanúsítványt. Érdemes áttekinteni az X. 509 tanúsítványok használatát bemutató mintakód: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Ha [szimmetrikus kulcsot](../iot-dps/concepts-symmetric-key-attestation.md)használ, másolja és illessze be a szimmetrikus kulcsot az eszköz kódjába.
   * A TPM-alapú hitelesítési módszer jelenleg nem támogatott.
1. Másolja és illessze be az alábbi generált azonosítókat az eszköz kódjába.
   * [Regisztrációs azonosító](../iot-dps/use-hsm-with-sdk.md)
   * [DPS-AZONOSÍTÓ](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS-végpont](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Amikor az eszköz kódját felépítette és üzembe helyezte az eszközön, válassza a **Csatlakozás** lehetőséget a IoT Plug and Play felületek felderítéséhez.
1. Ha a AICS-kapcsolat sikeresen befejeződött, a **tovább** gombra kattintva tekintse át a IoT Plug and Play felületeit.

## <a name="run-tests-and-publish-the-device"></a>Tesztek futtatása és az eszköz közzététele

Az Áttekintés lapon áttekintheti a felderített IoT Plug and Play felületeit. Ezen a lapon ellenőrizhető, hogy az illesztőfelületben megvalósított primitívek helyesen jelenjenek-e meg. Megtekintheti az interfész helyét is.

1. Győződjön meg arról, hogy a szükséges mezőkhöz meg van adva a hasznos adatok bevitele. Ezek a mezők a megadott csatolóhoz tartozó egyszerű parancs hasznos adatait tartalmazzák.
1. Ha megadta az összes szükséges információt, válassza a **tovább**lehetőséget.
1. A megvalósított IoT Plug and Play felületek tesztelésének futtatásához válassza a **tesztek futtatása**lehetőséget.
1. Az összes teszt automatikusan fut. Ha bármelyik teszt sikertelen, válassza a **naplók megtekintése** lehetőséget a AICS és az Azure IoT hubba küldött nyers telemetria kapcsolatos hibaüzenetek megtekintéséhez.
1. A minősítési tesztek elvégzéséhez válassza a **Befejezés**lehetőséget.
1. Tegye közzé a Certified IoT Plug and Play eszközt a katalógusban. Ha a minősített eszközt hozzá szeretné adni a katalógushoz, válassza a **Hozzáadás a katalógushoz** lehetőséget az eszköztáron. Ha a **Hozzáadás a katalógushoz** szürkén jelenik meg, az azt jelenti, hogy a termék adatai hiányosak, vagy a tesztek sikertelenek voltak. 
1. Válassza a "CERTIFIED és a KATALÓGUSban" hivatkozást a közzétett eszköz az eszköz katalógusban való megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a IoT Plug and Play eszköz tanúsítását, a következő lépés a képesség-modellek kezelésével kapcsolatos további információ:

> [!div class="nextstepaction"]
> [Modellek kezelése](./howto-manage-models.md)
