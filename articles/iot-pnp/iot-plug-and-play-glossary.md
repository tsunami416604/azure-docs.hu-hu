---
title: Szószedet – IoT Plug and Play előzetes verzió | Microsoft Docs
description: Fogalmak – a IoT Plug and Play előzetes verziójának általános feltételeinek glosszáriuma.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 10/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 22b2cb8caa815b2ce8343a060a5f12133b763b83
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890346"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>A IoT használati feltételeinek szószedete Plug and Play előzetes verzió

A IoT Plug and Play cikkeiben használt általános feltételek definíciói.

## <a name="azure-certified-for-iot-portal"></a>Az Azure Certified for IoT portál

Az [Azure Certified for IoT portál](https://aka.ms/ACFI) webhelyét a következő módon használhatja:

- Fejezze be a [IoT Plug and Play eszközének](#iot-plug-and-play-device) [minősítési folyamatát](#device-certification) .
- [Eszköz-képesség modellek](#device-capability-model)keresése.
- Tegye közzé az eszköz képességeinek modelljét a [nyilvános modell adattárában](#public-model-repository).

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI egy platformfüggetlen, parancssori eszköz az Azure-erőforrások kezeléséhez. Az Azure CLI-hez készült Azure IoT-bővítmény parancssori eszköz a [IoT Plug and Play-eszközökhöz](#iot-plug-and-play-device)való interakcióhoz és teszteléshez. A bővítmény a következőre használható:

- Kapcsolódjon egy IoT Plug and Play eszközhöz.
- Az eszköz által küldött [telemetria](#telemetry) megtekintése.
- Az eszköz [tulajdonságainak](#properties)használata.
- Hívja meg az eszköz [parancsait](#commands).
- [Modell-adattárak](#model-repository), [felületek](#interface)és [eszköz-képesség modellek](#device-capability-model)kezelése.

## <a name="azure-iot-central"></a>Azure IoT Central

Az Azure IoT Central egy teljes körűen felügyelt, szoftveres megoldás, amely megkönnyíti a [IoT Plug and Play-eszközök](#iot-plug-and-play-device)csatlakoztatását, figyelését és felügyeletét. Az [eszköz funkciós modelljeivel](#device-capability-model) automatikusan konfigurálhat egy IoT Central alkalmazást az eszközök figyeléséhez és kezeléséhez.

## <a name="azure-iot-certification-service"></a>Azure IoT minősítési szolgáltatás

Az Azure IoT minősítési szolgáltatás minősítési teszteket futtat, amikor [IoT Plug and Play eszközt](#iot-plug-and-play-device) küld a minősítéshez az [Azure Certified for IoT portálon](#azure-certified-for-iot-portal)keresztül. Ahhoz, hogy hozzá lehessen adni egy eszközt a [Certified for IoT-Device cataloghoz](#certified-for-iot-device-catalog), az eszköznek hitelesítettnek kell lennie.

## <a name="azure-iot-tools-extension"></a>Azure IoT-eszközök bővítmény

Az Azure IoT Tools egy olyan bővítmények gyűjteménye a [Visual Studio Code](#visual-studio-code) -ban, amely segítséget nyújt a IoT hub és a IoT-eszközök fejlesztésében. A IoT Plug and Play-eszközök fejlesztéséhez a következőket nyújtja:

- [Eszköz-képesség modellek](#device-capability-model) és [felületek](#interface)készítése.
- Közzététel a [modell adattárakban](#model-repository).
- Hozzon létre csontváz-kódot az eszköz alkalmazásának megvalósításához.

## <a name="azure-iot-explorer-tool"></a>Az Azure IoT Explorer eszköze

Az Azure IoT Explorer egy grafikus eszköz, amellyel kezelheti és tesztelheti a [IoT Plug and Play eszközöket](#iot-plug-and-play-device). Miután telepítette az eszközt a helyi gépre, a következőt használhatja:

- Tekintse meg az [IoT hub](#azure-iot-hub)-hoz csatlakoztatott eszközöket.
- Kapcsolódjon egy IoT Plug and Play eszközhöz.
- Az eszköz által küldött [telemetria](#telemetry) megtekintése.
- Az eszköz [tulajdonságainak](#properties)használata.
- Hívja meg az eszköz [parancsait](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

Az IoT Hub egy olyan felügyelt szolgáltatás, amely a felhőn fut, és amely az IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikáció üzenetközpontjaként működik. A [IoT Plug and Play-eszközök](#iot-plug-and-play-device) csatlakozni tudnak egy IoT hubhoz. Az IoT-megoldás egy IoT hubot használ a következő engedélyezéséhez:

- Eszközök, amelyekkel telemetria küldhetnek egy felhőalapú megoldásba.
- Felhőalapú megoldás a csatlakoztatott eszközök kezelésére.

## <a name="azure-iot-device-sdk"></a>Azure IoT-eszköz SDK

Több nyelvhez is vannak eszközök SDK-k, amelyek segítségével IoT Plug and Play eszköz ügyfélalkalmazások hozhatók létre. Az [eszközök minősítésének](#device-certification) egyik követelménye az, hogy az eszköz ügyfél-kódja az Azure IoT Device SDK-k egyikét használja.

## <a name="certified-for-iot-device-catalog"></a>IoT-eszköz katalógusának minősítése

A [tanúsított IoT-eszközök katalógusa](https://catalog.azureiotsolutions.com/) felsorolja azokat a [IoT Plug and Play eszközöket](#iot-plug-and-play-device) , amelyek átadták az [eszköz minősítési](#device-certification) teszteit. A IoT Plug and Play eszközök a katalógusban és a nyilvános modell adattárában közzétett [eszköz-képesség modelljei](#device-capability-model) .

## <a name="commands"></a>Parancsok

Az [illesztőfelületekben](#interface) definiált parancsok olyan metódusokat jelölnek, amelyek a [digitális iker](#digital-twin)-on hajthatók végre. Például egy eszköz újraindítására szolgáló parancs.

## <a name="common-interface"></a>Általános interfész

Minden [IoT-Plug and Play eszköznek](#iot-plug-and-play-device) várhatóan végre kell hajtania néhány gyakori [felületet](#interface). Az eszköz adatai felület például meghatározza az eszköz hardver-és operációsrendszer-információit. Az [eszköz minősítése](#device-certification) megköveteli, hogy az eszköz több közös felületet implementáljon. A Common Interface-definíciókat a nyilvános modell tárházból kérheti le.

## <a name="company-model-repository"></a>Vállalati modell tárháza

A vállalatok a vállalati [modell tárházát](#model-repository) saját tárolóként használhatják az [eszköz képességeinek modelljeihez](#device-capability-model) és [felületéhez](#interface).

## <a name="connection-string"></a>Kapcsolati sztring

A kapcsolati karakterláncok a végponthoz való kapcsolódáshoz szükséges adatokat ágyazzák össze. A kapcsolatok karakterlánca általában tartalmazza a végpontok és a biztonsági információk címeit, de a kapcsolatok karakterlánc-formátumai eltérőek a szolgáltatások között. A IoT Hub szolgáltatáshoz két típusú kapcsolattípus van társítva:

- Az eszköz kapcsolati karakterláncai lehetővé teszik, hogy a [IoT Plug and Play eszközök](#iot-plug-and-play-device) csatlakozzanak az IoT hub eszközre irányuló végpontokhoz. Az eszközön a kapcsolati sztring használatával biztonságos kapcsolat hozható létre egy IoT hubhoz.
- IoT Hub kapcsolati karakterláncok lehetővé teszik a háttér-megoldások és-eszközök számára, hogy biztonságosan kapcsolódjanak a IoT hub szolgáltatáshoz csatlakozó végpontokhoz. Ezek a megoldások és eszközök kezelik az IoT hubot és az ahhoz csatlakoztatott eszközöket.
- A vállalati modell tárházának kapcsolati karakterláncai lehetővé teszik a háttérbeli megoldások és eszközök biztonságos csatlakoztatását a [vállalati modell tárházához](#company-model-repository). Ezek a megoldások és eszközök használják fel vagy kezelhetik az [eszköz képességeinek modelljeit](#device-capability-model) és [felületeit](#interface) az adattárban.

## <a name="device-capability-model"></a>Eszköz képességeinek modellje

Az eszköz képességeinek modellje leírja a [IoT Plug and Play eszközt](#iot-plug-and-play-device) , és meghatározza az eszköz által megvalósított [felületek](#interface) készletét. Az eszköz képességi modellje általában egy fizikai eszköz, termék vagy SKU-nak felel meg. Az eszköz képességi modelljét a [digitális kettős definíciós nyelv](#digital-twin-definition-language) használatával határozhatja meg.

## <a name="device-certification"></a>Eszköz minősítése

Az eszköz minősítése egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) hitelesítésének folyamata annak érdekében, hogy hozzá lehessen adni a [Certified for IoT Device cataloghoz](#certified-for-iot-device-catalog) , valamint az [eszköz képességeinek modelljét](#device-capability-model) és a nyilvános modellhez hozzáadott [felületeit](#interface) [ tárház](#public-model-repository).

## <a name="device-developer"></a>Eszközfejlesztő

Az eszközök fejlesztői egy [eszköz-képességi modellt](#device-capability-model), [felületet](#interface)és egy [Azure IoT eszközoldali SDK](#azure-iot-device-sdk) -t használnak a [IoT Plug and Play eszközön](#iot-plug-and-play-device)való futtatáshoz.

## <a name="device-modeling"></a>Eszköz modellezése

Az [eszközök fejlesztői](#device-developer) a [digitális kettős definíciós nyelvet](#digital-twin-definition-language) használják a [IoT Plug and Play eszköz](#iot-plug-and-play-device)képességeinek modellezéséhez. A modell a modell adattárával osztható meg. Az eszközök fejlesztői a modellből létrehozhatnak csontváz-kódot. A [megoldás fejlesztője](#solution-developer) IoT-megoldást konfigurálhat a modellből.

## <a name="device-provisioning-service"></a>Eszköz kiépítési szolgáltatása

Az [Azure IoT Central](#azure-iot-central) az eszköz kiépítési szolgáltatásával kezeli az összes eszköz regisztrációját és kapcsolódását. További információ: [Device connectivity in Azure IoT Central](../iot-central/preview/overview-iot-central-get-connected.md). A Device kiépítési szolgáltatással felügyelheti az eszközök regisztrációját és a IoT Hub-alapú IoT-megoldáshoz való kapcsolódást is. További információ: [eszközök kiépítés az Azure IoT hub Device Provisioning Service](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Eszközregisztráció

Ahhoz, hogy egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozni tudjanak egy IoT-megoldáshoz, regisztrálni kell a megoldásban. Az [Azure IoT Central](#azure-iot-central) az eszközök regisztrálását az [eszköz kiépítési szolgáltatásával](#device-provisioning-service) kezeli. Az egyéni IoT-megoldásokban az eszközök regisztrálása az IoT hub-ban Azure Portal vagy programozott módon végezhető el.

## <a name="device-first"></a>Eszköz – első

Az [Azure IoT Central](#azure-iot-central) támogatja az eszközök első regisztrálását és a csatlakoztatási forgatókönyvet. Ebben a forgatókönyvben egy [IoT Plug and Play-eszköz](#iot-plug-and-play-device) csatlakozhat egy IoT Central-alkalmazáshoz anélkül, hogy előzetesen regisztrálva lenne. A regisztráció automatikusan megtörténik, amikor az eszköz először csatlakozik az alkalmazáshoz.

## <a name="digital-twin"></a>Digitális Twin

A digitális Twin egy [IoT Plug and Play eszköz](#iot-plug-and-play-device)modellje. A digitális Twin modell a [digitális kettős definíciós nyelv](#digital-twin-definition-language)használatával van modellezve. Az [Azure IoT-eszköz SDK](#azure-iot-device-sdk) -k használatával a digitális ikrek a futási időben is kommunikálhatnak. Beállíthat például egy tulajdonságot egy digitális Twin-eszközön, és az SDK közli ezt a változást a felhőben lévő IoT-megoldással.

## <a name="digital-twin-change-events"></a>Digitális kettős változási események

Ha egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozik egy [IoT hubhoz](#azure-iot-hub), a hub útválasztási képességével digitális kettős változtatásokról is küldhet értesítéseket. Például, ha egy [tulajdonság](#properties) értéke megváltozik egy eszközön, IoT hub küldhet értesítést egy végpontnak, például egy Service Bus üzenetsor számára.

## <a name="digital-twin-definition-language"></a>Digitális kettős definíciós nyelv

A modellek és felületek [IoT Plug and Play eszközökhöz](#iot-plug-and-play-device)való leírásának nyelve. A Digital [Twin Definition Language](https://aka.ms/DTDL) segítségével leírhatja a [digitális Twin](#digital-twin) funkcióit, és engedélyezheti a IoT platform-és IoT-megoldásokat az entitás szemantikai felhasználása érdekében.

## <a name="digital-twin-route"></a>Digitális kettős útvonal

Egy [IoT-hubhoz](#azure-iot-hub) beállított útvonal, amely a [digitális kettős változási eseményeket](#digital-twin-change-events) és végpontokat (például Service Bus üzenetsor) továbbítja.

## <a name="interface"></a>Felület

Az illesztőfelületek a [IoT Plug and Play eszköz](#iot-plug-and-play-device) vagy a [digitális Twin](#digital-twin)szolgáltatás által megvalósított kapcsolódó képességeket ismertetik. A csatolókat a különböző [eszköz-képesség modellek](#device-capability-model)között is felhasználhatja.

## <a name="iot-hub-query-language"></a>IoT Hub lekérdezés nyelve

A IoT Hub lekérdezési nyelv több célra szolgál. Használhatja például a nyelvet az IoT hub-ban [regisztrált eszközök](#device-registration) keresésére, vagy a [digitális kettős útválasztási](#digital-twin-route) viselkedés pontosítására.

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play eszköz

A IoT Plug and Play-eszközök általában egy kis méretű, önálló számítástechnikai eszköz, amely adatokat gyűjt, vagy más eszközöket irányít, valamint olyan szoftvert vagy belső vezérlőprogramot futtat, amely [eszköz-képességi modellt](#device-capability-model)valósít meg.  Például egy IoT Plug and Play eszköz lehet környezeti figyelő eszköz vagy egy intelligens mezőgazdasági öntözési rendszer vezérlője. Felhőalapú IoT-megoldást is írhat a IoT Plug and Play eszközökről származó adatok parancsára, vezérlésére és fogadására. Az [Azure Certified for IoT-eszközök katalógusa](#certified-for-iot-device-catalog) felsorolja az elérhető IoT Plug and Play eszközöket. A rendszer ellenőrizte az összes IoT Plug and Play eszközt a katalógusban, és rendelkezik egy [eszköz képesség modellel](#device-capability-model).

## <a name="microsoft-partner-center"></a>Microsoft partner Center

A [Microsoft partner Center](https://docs.microsoft.com/partner-center/) a szervezet teljes körű kapcsolatát a Microsofttal kezeli. Ahhoz, hogy hitelesíteni tudja a [IoT Plug and Play eszközét](#iot-plug-and-play-device) az [Azure Certified for IoT portálon](#azure-certified-for-iot-portal), szüksége van egy Microsoft partner Center-fiókra.

## <a name="model-discovery"></a>Modellfelderítés

Ha egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozik egy IoT-megoldáshoz, a megoldás az eszköz képességeinek megkeresésével képes észlelni az eszköz [képességeit.](#device-capability-model) Egy eszköz képes a megoldásba küldeni a képesség modelljét, vagy a megoldás egy eszköz képesség modelljét is megtalálja a [modell tárházában](#model-repository).

## <a name="model-repository"></a>Modell tárháza

A modell tárháza az [eszköz képességeinek modelljeit](#device-capability-model) és [felületeit](#interface)tárolja. Egyetlen [nyilvános modell tárháza](#public-model-repository)van. A szervezetek létrehozhatják saját szervezeti modell-tárházait.

## <a name="model-repository-rest-api"></a>Modell tárháza REST API

API a modell-Tárházak kezeléséhez és interakcióhoz. Használhatja például az API-t az [eszköz képességi modelljeinek](#device-capability-model) hozzáadásához és a képességi modellek kereséséhez.

## <a name="properties"></a>Tulajdonságok

A tulajdonságok olyan adatmezők, amelyek egy olyan [felületen](#interface) vannak meghatározva, amely egy digitális iker bizonyos állapotát jelöli. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja. A csak olvasható tulajdonságokat, például a sorozatszámot a [IoT Plug and Play eszközön](#iot-plug-and-play-device) futó kód állítja be.  Az írható tulajdonságok, például a riasztási küszöbértékek általában a felhőalapú IoT-megoldás alapján vannak beállítva.

## <a name="public-model-repository"></a>Nyilvános modell tárháza

Létezik egy nyilvános modell tárháza, amely az [eszköz képességeinek modelljeit](#device-capability-model) és [felületeit](#interface) tárolja a [hitelesített eszközökhöz](#device-certification). A nyilvános modell tárháza a [közös illesztőfelület](#common-interface) -definíciókat is tárolja.

## <a name="registration-id"></a>Regisztrációs azonosító

A regisztrációs azonosító egyedileg azonosít egy eszközt az [eszköz kiépítési szolgáltatásában](#device-provisioning-service). Ez az azonosító nem egyezik meg az eszköz AZONOSÍTÓjának [IoT hub](#azure-iot-hub)-beli egyedi azonosítóval.

## <a name="scope-id"></a>Hatókör-azonosító

A hatókör-azonosító hatóköre egyedileg azonosítja az [eszköz kiépítési szolgáltatásának](#device-provisioning-service) példányát.

## <a name="shared-access-signature"></a>Közös hozzáférési aláírás

A közös hozzáférésű aláírások az SHA-256 biztonságos kivonatokon vagy URI-kon alapuló hitelesítési mechanizmusok. A közös hozzáférésű aláírás hitelesítésének két összetevője van: egy közös hozzáférési házirend és egy közös hozzáférési aláírás (más néven token). A [IoT Plug and Play-eszközök](#iot-plug-and-play-device) közös hozzáférési aláírással hitelesítik magukat az [IoT hub](#azure-iot-hub)használatával.

## <a name="solution-developer"></a>Megoldás fejlesztője

A megoldás-fejlesztő létrehozza a megoldás hátterét. A megoldás-fejlesztő általában az Azure-erőforrásokkal, például a [IoT hub](#azure-iot-hub) és a [modell-adattárakkal](#model-repository), vagy a [IoT Centralokkal](#azure-iot-central)működik.

## <a name="telemetry"></a>Telemetria

Az [illesztőfelületben](#interface) definiált telemetria mezők a méréseket jelölik. Ezek a mérések jellemzően olyan értékek, mint az érzékelő által az [IoT Plug and Play eszköz](#iot-plug-and-play-device) által küldött adatstreamek.

## <a name="visual-studio-code"></a>Visual Studio Code

A Visual Studio Code egy modern Kódszerkesztő, amely több platformon is elérhető. A bővítmények, például az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Pack, lehetővé teszik a szerkesztő testreszabását, hogy a számos fejlesztési forgatókönyvet támogassanak.
