---
title: IoT Plug and Play előzetes verziójú eszköz hitelesítése | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan adhat hozzá termékadatokat az Azure Certified for IoT eszközkatalógushoz, hogyan csatlakoztathatja az eszközt az Azure IoT-tanúsítványszolgáltatáshoz, majd hogyan futtathatja az IoT Plug and Play minősítési teszteket.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550162"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Oktatóanyag: Az IoT Plug and Play előzetes verziójának hitelesítése

IoT Plug and Play előzetes verziójú eszköz közzététele az [Azure Certified for IoT eszközkatalógusban,](https://aka.ms/iotdevcat)meg kell felelnie egy sor minősítési tesztek. Az [Azure Certified for IoT](https://aka.ms/ACFI) portal használatával küldje el az eszközt minősítésre. Az [Azure IoT-tanúsítványszolgáltatás](https://aka.ms/azure-iot-aics) futtatja a minősítési teszteket.

Ebben az oktatóanyagban a következőket tanulhatja meg:

> [!div class="checklist"]
> * Melyek az IoT Plug and Play minősítési követelményei.
> * A termék nevének és adatainak hozzáadása a portálhoz.
> * Az IoT Plug and Play interfészek csatlakoztatása és felfedezése.
> * Az IoT Plug and Play felületek áttekintése és a hitelesítési tesztek futtatása.
> * A hitelesített IoT Plug and Play eszköz közzététele a katalógusban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Microsoft Partner Center-fiók.
* A Microsoft Partner Network azonosítója.

További információ: [Hogyan lehet az Azure Certified for IoT portalra való beszállni.](howto-onboard-portal.md)

## <a name="certification-requirements"></a>Minősítési követelmények

Az IoT Plug and Play eszköz hitelesítéséhez az eszköznek meg kell felelnie az alábbi követelményeknek:

* Az IoT Plug and Play eszközkódját telepíteni kell az eszközre.
* Az IoT Plug and Play eszközkódja az Azure IoT SDK-val épül.
* Az eszközkódnak támogatnia kell az [Azure IoT Hub-eszközkiépítési szolgáltatást.](../iot-dps/about-iot-dps.md)
* Az eszközkódnak végre kell hajtania az [Eszközinformációs felületet.](concepts-common-interfaces.md)
* A képességmodell és az eszközkód együttműködik az IoT Centrallal.

A katalógusban lévő összes eszköz előre hitelesített IoT Plug and Play eszköznek minősül. Nem garantálja az IoT Plug and Play szoftverösszetevők, például az SDK és a Digital Twin Definition Language végtermékének minőségét és megfelelőségét.

Az összes előre tanúsított IoT Plug and Play eszközt újra kell hitelesíteni az IoT Plug and Play általános elérhetősége alapján, a Microsoft által biztosított minősítési követelmények és szoftverösszetevők végleges verziója alapján.

## <a name="add-product-name"></a>Terméknév hozzáadása

**A termék** egy termékmodell rövid neve, amelyet az ügyfél megvásárolhat. Termék hozzáadása:

1. Jelentkezzen be az [Azure Certified for IoT](https://aka.ms/ACFI) portalra.
1. Lépjen a portál **Termékek** lapjára a bal oldali menüből, és válassza a **+ Új**lehetőséget.
1. Adja meg a rövid termék nevét, és válassza a **Létrehozás lehetőséget.** Az itt megadott név eltér az eszközkatalógusban megjelenő névtől.

## <a name="add-product-information"></a>Termékadatok hozzáadása

Miután sikeresen létrehozta a terméket a portálon, a termék megjelenik a **Termékek** lapon. A termékadatok hozzáadása:

1. Válassza ki a termékoszlop **Termék** lapján található létrehozott termékhivatkozást. Az államnak tervezetállapotban kell lennie.
1. Válassza a **Termékinformáció** fejléc melletti **Szerkesztés** hivatkozást. Adja meg a termékkel kapcsolatos információkat a termékinformációs lapon, és győződjön meg arról, hogy az összes szükséges mezőt kikell töltenie.
1. Kattintson a **Mentés** gombra. A **Mentés** gomb csak akkor jelenik meg, ha az összes szükséges mezőt kitöltse. Ha a mezők hiányosak, a **Mentés és befejezés később**gomb .
1. Tekintse át a beírt tartalmat. Töltse ki az eszköz eszközkatalógusban való közzétételéhez szükséges összes mezőt. A **termékinformációs** fejléc melletti **szerkesztési** hivatkozásra kattintva bármikor visszatérhet a termékadatok termékinformációs lapon történő szerkesztéséhez.

## <a name="connect-and-discover-interfaces"></a>Kapcsolódási és felderítési felületek

A minősítési tesztek futtatásához csatlakoztassa az eszközt az [Azure IoT tanúsítványszolgáltatáshoz](https://aka.ms/azure-iot-aics) (AICS), amely elérhető a portálon.

Ezek a lépések a minősítési tesztek futtatásának egyetlen lépései, és nem szükséges módosítani a termékeszköz kódját. A kezdéshez kövesse az alábbi lépéseket az AICS-hez való csatlakozáshoz:

1. Jelentkezzen be a portálra a Partnerközpont-fiókjával.
1. Kattintson a **Connect + teszt** gombra a tanúsítási folyamat elindításához.
1. Válassza ki a [hitelesítési módszert](../iot-dps/concepts-security.md#attestation-mechanism) az eszköz AICS-ba való kiépítéséhez az [Azure IoT Hub eszközkiépítési szolgáltatás](../iot-dps/about-iot-dps.md)használatával.
   * [Ha X.509-es tanúsítványt](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)használ, töltse fel a létrehozott X.509 tanúsítványt. Érdemes lehet áttekinteni az X.509 tanúsítványok használatát bemutató mintakódot: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Szimmetrikus [kulcs](../iot-dps/concepts-symmetric-key-attestation.md)használata esetén másolja és illessze be a szimmetrikus kulcsot az eszközkódjába.
   * A TPM hitelesítési módszer jelenleg nem támogatott.
1. Másolja és illessze be a következő létrehozott azonosítókat az eszköz kódjába.
   * [Regisztrációs azonosító](../iot-dps/use-hsm-with-sdk.md)
   * [DPS-azonosító](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS-végpont](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Amikor az eszközkód meg van építve és telepítve van az eszközön, válassza a **Csatlakozás** lehetőséget az IoT Plug and Play felületek felderítéséhez.
1. Ha az AICS-kapcsolat sikeres, válassza a **Tovább** lehetőséget az IoT Plug and Play felületek felderített áttekintéséhez.

## <a name="run-tests-and-publish-the-device"></a>Tesztek futtatása és az eszköz közzététele

Az ellenőrzés oldalon megtekintheti a felderített IoT Plug and Play felületeket. Ezen a lapon ellenőrizheti a felület kijelzőjén megvalósított primitíveket. A kapcsolat helyét is ellenőrizheti.

1. Győződjön meg arról, hogy a szükséges mezőkhöz hasznos bemenetek vannak megadva. Ezek a mezők a megadott kapcsolat primitív parancsprimitívének hasznos adatát tartalmazzák.
1. Miután megadta az összes szükséges információt, válassza a **Tovább gombot.**
1. A megvalósított IoT Plug and Play felületek tesztjeinek futtatásához válassza a **Tesztek futtatása**lehetőséget.
1. Minden teszt automatikusan fut. Ha valamelyik teszt sikertelen, válassza a Naplók megtekintése az AICS-ból származó **hibaüzenetek** és az Azure IoT Hubnak küldött nyers telemetriai adatok megtekintéséhez.
1. A tanúsítási tesztek elvégzéséhez válassza a **Befejezés**lehetőséget.
1. Tegye közzé a hitelesített IoT Plug and Play eszközt a katalógusban. Ha hozzá szeretné adni a hitelesített eszközt a katalógushoz, válassza a **Hozzáadás a katalógushoz** lehetőséget az eszköztáron. Ha a **Hozzáadás a katalógushoz** szürkén jelenik meg, az azt jelenti, hogy a termékinformáció nem teljes, vagy a tesztek sikertelenek. 
1. Válassza a "Hitelesített és a katalógusban" hivatkozást a közzétett eszköz megtekintéséhez az eszközkatalógusban.

## <a name="next-steps"></a>További lépések

Most, hogy már megismerkedett az IoT Plug and Play eszköz hitelesítésével, a javasolt következő lépés a képességmodellek kezelésével kapcsolatos további tudnivalók:

> [!div class="nextstepaction"]
> [Modellek kezelése](./howto-manage-models.md)
