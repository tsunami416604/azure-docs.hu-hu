---
title: Kifejezések szószedete - IoT Plug and Play Preview | Microsoft dokumentumok
description: Fogalmak – az IoT Plug and Play előzetesverziójával kapcsolatos közös kifejezések szószedete.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767073"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Az IoT Plug and Play előzetes feltételeinek szószedete

Az IoT Plug and Play cikkekben használt általános kifejezések definíciói.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified for IoT portál

Az Azure [Certified for IoT portal](https://aka.ms/ACFI) webhelyhasználatával a következőkre használhatja:

- Fejezze be az [IoT Plug and Play eszköz](#iot-plug-and-play-device) [minősítési folyamatát.](#device-certification)
- Keresse meg [az eszközképességi modelleket.](#device-capability-model)
- Eszközképességi modell közzététele a [nyilvános modelltárházban.](#public-model-repository)

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI egy többplatformos, parancssori eszköz az Azure-erőforrások kezeléséhez. Az Azure CLI Azure IoT-bővítménye parancssori eszköz az [IoT Plug and Play eszközökkel](#iot-plug-and-play-device)való interakcióhoz és teszteléshez. A bővítmény ta-

- Csatlakozzon egy IoT Plug and Play eszközhöz.
- Tekintse meg az eszköz által küldött [telemetriai adatokat.](#telemetry)
- Az eszköz [tulajdonságainak](#properties)használata .
- Az [eszközparancsok hívása](#commands).
- [Modelladattárak](#model-repository), [illesztőfelületek](#interface)és [eszközképességi modellek](#device-capability-model)kezelése .

## <a name="azure-iot-central"></a>Azure IoT Central

Az Azure IoT Central egy teljes körűen felügyelt szoftverszolgáltatás-megoldás, amely megkönnyíti az [IoT Plug](#iot-plug-and-play-device)and Play eszközök csatlakoztatását, figyelését és kezelését. Az [eszközképesség-modellek](#device-capability-model) segítségével automatikusan konfigurálhat egy IoT Central alkalmazást az eszközök figyelésére és kezelésére.

## <a name="azure-iot-certification-service"></a>Azure IoT-tanúsítványszolgáltatás

Az Azure IoT-tanúsítványszolgáltatás egy sor minősítési tesztet futtat, amikor [ioT Plug and Play eszközt](#iot-plug-and-play-device) küld be minősítésre az Azure [Certified for IoT portálon](#azure-certified-for-iot-portal)keresztül. Mielőtt hozzáadhatna egy eszközt az [IoT-tanúsítvány eszközkatalógushoz,](#certified-for-iot-device-catalog)az eszköznek hitelesnek kell lennie.

## <a name="azure-iot-tools-extension"></a>Az Azure IoT Tools bővítmény

Az Azure IoT Tools bővítmények gyűjteménye a [Visual Studio-kódban,](#visual-studio-code) amely segít az IoT Hub használatával való együttműködésben és az IoT-eszközök fejlesztésében. Az IoT Plug and Play eszközök fejlesztéséhez a következőket segíti a következőkben:

- Az [eszközképességi modellek](#device-capability-model) és [interfészek készítése](#interface).
- Közzététel [a modelltárolókban](#model-repository).
- Hozzon létre csontváz kódot az eszközalkalmazás megvalósításához.

## <a name="azure-iot-explorer-tool"></a>Az Azure IoT-kezelő eszköz

Az Azure IoT Explorer egy grafikus eszköz, amellyel kezelheti és tesztelheti [az IoT Plug and Play eszközöket.](#iot-plug-and-play-device) Miután telepítette az eszközt a helyi számítógépre, a következő eszközökre használhatja:

- Tekintse meg az [IoT hubhoz](#azure-iot-hub)csatlakoztatott eszközöket.
- Csatlakozzon egy IoT Plug and Play eszközhöz.
- Tekintse meg az eszköz által küldött [telemetriai adatokat.](#telemetry)
- Az eszköz [tulajdonságainak](#properties)használata .
- Az [eszközparancsok hívása](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

Az IoT Hub egy olyan felügyelt szolgáltatás, amely a felhőn fut, és amely az IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikáció üzenetközpontjaként működik. [Az IoT Plug and Play-eszközök](#iot-plug-and-play-device) csatlakozhatnak egy IoT hubhoz. Az IoT-megoldások egy IoT-hubot használ az engedélyezéshez:

- Telemetriai adatokat küldegy felhőalapú megoldás.
- Felhőalapú megoldás a csatlakoztatott eszközök kezelésére.

## <a name="azure-iot-device-sdk"></a>Azure IoT-eszköz SDK

Több nyelvhez több eszköz SDK-k is léteznek, amelyek segítségével IoT Plug and Play eszközügyfél-alkalmazásokat hozhat létre. Az [eszközminősítés](#device-certification) egyik követelménye, hogy az eszközügyfél-kód az Azure IoT-eszköz SDK-k egyikét használja.

## <a name="certified-for-iot-device-catalog"></a>IT-eszközkatalógusminősítéssel

A [Certified for IoT eszközkatalógus](https://catalog.azureiotsolutions.com/) felsorolja azokat az [IoT Plug and Play eszközöket,](#iot-plug-and-play-device) amelyek megfeleltek az [eszközminősítési](#device-certification) teszteken. Az IOt Plug and Play eszközök [eszközképességi modelljei](#device-capability-model) a katalógusban, és a nyilvános modelltárházban közzétéve.

## <a name="commands"></a>Parancsok

Az [összeköttetésben](#interface) definiált parancsok olyan módszereket jelölnek, amelyek a [digitális ikerkapcsolaton végrehajthatók.](#digital-twin) Például egy eszköz újraindítására vonatkozó parancs.

## <a name="common-interface"></a>Közös felület

Az [IoT Plug and Play eszközökvárhatóan](#iot-plug-and-play-device) megvalósítanak néhány gyakori [felületet.](#interface) Az eszközinformációs felület például hardver- és operációsrendszer-információkat határoz meg az eszközről. [Az eszközhitelesítéshez](#device-certification) az eszköznek több közös felületet kell megvalósítania. A nyilvános modelltárházból lekérheti a közös felületdefiníciókat.

## <a name="company-model-repository"></a>Vállalati modelltárház

A szervezetek a vállalati [modelltárházat](#model-repository) magántárolóként használhatják [eszközképességi modellekhez](#device-capability-model) és [felületekhez.](#interface)

## <a name="connection-string"></a>Kapcsolati sztring

A kapcsolati karakterlánc magában foglalja a végponthoz való csatlakozáshoz szükséges adatokat. A kapcsolati karakterlánc általában tartalmazza a végpont címét és a biztonsági információkat, de a kapcsolati karakterlánc-formátumok szolgáltatásokonként eltérőek lehetnek. Az IoT Hub szolgáltatáshoz kétféle kapcsolati karakterlánc van társítva:

- Az eszközkapcsolati karakterláncok lehetővé teszik, hogy az [IoT Plug and Play-eszközök](#iot-plug-and-play-device) csatlakozzanak az IOt-elosztó eszközfelé néző végpontjaihoz. Az eszközön lévő ügyfélkód a kapcsolati karakterlánc ot használja az IoT-központtal való biztonságos kapcsolat létrehozásához.
- Az IoT Hub kapcsolati karakterláncai lehetővé teszik, hogy a háttérmegoldások és -eszközök biztonságosan kapcsolódjanak az IoT hub szolgáltatásnéző végpontokhoz. Ezek a megoldások és eszközök kezelik az IoT hubot és a hozzá csatlakoztatott eszközöket.
- A vállalati modelltár adattárhálózati kapcsolati karakterláncai lehetővé teszik, hogy a háttérmegoldások és -eszközök biztonságosan kapcsolódjanak a [vállalati modelltárházhoz.](#company-model-repository) Ezek a megoldások és eszközök fogyasztják vagy kezelik az [eszközképességi modelleket](#device-capability-model) és [felületeket](#interface) a tárházban.

## <a name="device-capability-model"></a>Eszközképességi modell

Az eszközképességi modell leírja az [IoT Plug and Play eszközt,](#iot-plug-and-play-device) és meghatározza az eszköz által [megvalósított felületek](#interface) készletét. Az eszközképességi modell általában egy fizikai eszköznek, terméknek vagy termékváltozatnak felel meg. A [Digitális ikerdefiníciós nyelv](#digital-twin-definition-language) segítségével definiálhatja az eszközképességi modellt.

## <a name="device-certification"></a>Eszköz minősítése

Az eszközminősítés az [IoT Plug and Play eszköz](#iot-plug-and-play-device) hitelesítésének folyamata, amely lehetővé teszi, hogy hozzá lehessen adni az [IoT-eszközkatalógushoz,](#certified-for-iot-device-catalog) valamint annak [eszközképességi modelljéhez](#device-capability-model) és [interfészeihez](#interface) a [nyilvános modelltárházba.](#public-model-repository)

## <a name="device-developer"></a>Eszközfejlesztő

Az eszközfejlesztő egy [eszközképességi modellt](#device-capability-model), [illesztőfelületeket](#interface)és egy [Azure IoT-eszköz SDK-t](#azure-iot-device-sdk) használ az [IoT Plug and Play eszközön](#iot-plug-and-play-device)futtatandó kód megvalósításához.

## <a name="device-modeling"></a>Eszközmodellezés

Az [eszközfejlesztő](#device-developer) a [Digital Twin Definition Language segítségével](#digital-twin-definition-language) modellezi az [IoT Plug and Play eszközök](#iot-plug-and-play-device)képességeit. A modell egy modelltárház használatával osztható meg. Az eszközfejlesztő csontváz-eszközkódot hozhat létre a modellből. A [megoldás fejlesztői](#solution-developer) konfigurálhatja az IoT-megoldás a modellből.

## <a name="device-provisioning-service"></a>Eszközkiépítési szolgáltatás

[Az Azure IoT Central](#azure-iot-central) az eszközkiépítési szolgáltatást használja az összes eszközregisztráció és -kapcsolat kezeléséhez. További információ: [Device connectivity in Azure IoT Central.](../iot-central/core/concepts-get-connected.md) Az eszközkiépítési szolgáltatás segítségével is kezelheti az eszköz regisztrációját és az IoT Hub-alapú IoT-megoldáshoz való csatlakozást. További információ: [Eszközök kiépítése az Azure IoT Hub-eszközkiépítési szolgáltatással.](../iot-dps/about-iot-dps.md)

## <a name="device-registration"></a>Eszközregisztráció

Ahhoz, hogy egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozik egy IoT-megoldáshoz, regisztrálnia kell a megoldással. [Az Azure IoT Central](#azure-iot-central) az [eszközkiépítési szolgáltatást](#device-provisioning-service) használja az eszközregisztráció kezeléséhez. Egy egyéni IoT-megoldásban regisztrálhatja az eszközöket az IoT-központtal az Azure Portalon vagy programozott módon.

## <a name="device-first"></a>Eszköz-első

[Az Azure IoT Central](#azure-iot-central) támogatja az eszköz-első regisztrációs és kapcsolati forgatókönyv. Ebben a forgatókönyvben egy [IoT Plug and Play-eszköz](#iot-plug-and-play-device) előzetes regisztráció nélkül is csatlakozhat egy IoT Central-alkalmazáshoz. A regisztráció automatikusan megtörténik, amikor egy eszköz először csatlakozik az alkalmazáshoz.

## <a name="digital-twin"></a>Digitális iker

A digitális ikeregy [IoT Plug and Play eszköz](#iot-plug-and-play-device)modellje. A digitális ikerpár modellezése a [Digital Twin Definition Language segítségével történik.](#digital-twin-definition-language) Az Azure [IoT-eszköz SDK-k](#azure-iot-device-sdk) segítségével kommunikálhat a digitális ikrek futásidőben. Beállíthatja például egy tulajdonság értékét egy digitális ikerkészüléken, és az SDK közli ezt a módosítást az IoT-megoldással a felhőben.

## <a name="digital-twin-change-events"></a>Digitális ikerváltási események

Ha egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozik egy [IoT hubhoz,](#azure-iot-hub)a hub használhatja az útválasztási képességét a digitális ikermódosításokról szóló értesítések küldésére. Például ha egy [tulajdonság](#properties) értéke megváltozik egy eszközön, az IoT Hub értesítést küldhet egy végpontnak, például egy Service Bus-várólistának.

## <a name="digital-twin-definition-language"></a>Digitális ikerdefiníciós nyelv

Az [IoT Plug](#iot-plug-and-play-device)and Play eszközök modelljeit és felületeit leíró nyelv. A [Digital Twin Definition Language](https://aka.ms/DTDL) használatával leírhatod a [digitális ikerhálózat képességeit,](#digital-twin) és engedélyezheti az IoT platform és az IoT-megoldások használatát az entitás szemantikájának kihasználásához.

## <a name="digital-twin-route"></a>Digitális ikerútvonal

Egy [IoT-hubban](#azure-iot-hub) beállított útvonal, amely [digitális ikermódosítási eseményeket](#digital-twin-change-events) és végpontokat, például egy Service Bus-várólistát biztosít.

## <a name="interface"></a>Felület

Az interfész az [IoT Plug and Play eszköz](#iot-plug-and-play-device) vagy [a digitális ikereszköz](#digital-twin)által megvalósított kapcsolódó képességeket írja le. A felületeket újra felhasználhatja a különböző [eszközképességi modellek között.](#device-capability-model)

## <a name="iot-hub-query-language"></a>Az IoT Hub lekérdezési nyelve

Az IoT Hub lekérdezési nyelv több célra használható. A nyelv segítségével például az IoT-központban [regisztrált eszközök](#device-registration) kereséséhez vagy a digitális ikerútválasztási viselkedés finomításához [használhatja.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play eszköz

Az IoT Plug and Play eszköz általában egy kis méretű, önálló számítástechnikai eszköz, amely adatokat gyűjt vagy más eszközöket vezérel, és olyan szoftvert vagy belső vezérlőprogramot futtat, amely [eszközképességi modellt](#device-capability-model)valósít meg.  Egy IoT Plug and Play eszköz lehet például egy környezeti figyelő eszköz, vagy egy intelligens mezőgazdasági öntözőrendszer vezérlője. Írhat egy felhőben üzemeltetett IoT-megoldást az IoT Plug and Play eszközökről történő vezérléshez, vezérléshez és fogadáshoz. Az [Azure Certified for IoT eszközkatalógus](#certified-for-iot-device-catalog) felsorolja az elérhető IoT Plug and Play eszközöket. A katalógusban lévő minden IoT Plug and Play eszköz érvényesítve lett, és rendelkezik egy [eszközképességi modellel.](#device-capability-model)

## <a name="microsoft-partner-center"></a>Microsoft Partnerközpont

[A Microsoft Partner Center](https://docs.microsoft.com/partner-center/) az a hely, ahol a szervezet kezeli a Microsofttal való végpontok közötti kapcsolatát. Az [IoT Plug and Play-eszköz](#iot-plug-and-play-device) hitelesítéséhez microsoftpartnercenter-fiókra van szüksége az [Azure Certified for IoT portálon.](#azure-certified-for-iot-portal)

## <a name="model-discovery"></a>Modellfelderítés

Amikor egy [IoT Plug and Play eszköz](#iot-plug-and-play-device) csatlakozik egy IoT-megoldáshoz, a megoldás az eszköz [képességmodelljének](#device-capability-model)megkeresésével felderítheti az eszköz képességeit. Az eszköz elküldheti képességmodelljét a megoldásnak, vagy a megoldás megtalálja az eszközképességi modellt egy [modelltárházban.](#model-repository)

## <a name="model-repository"></a>Modelltárház

A modelltáraz [eszközképességi modelleket](#device-capability-model) és [felületeket](#interface)tárolja. Van egy [nyilvános modell tárház](#public-model-repository). A szervezetek létrehozhatják saját szervezeti modell-adattárak.

## <a name="model-repository-rest-api"></a>Modelltár REST API

API a modelladattárak kezeléséhez és kezeléséhez. Az API használatával például [eszközképességi modelleket](#device-capability-model) adhat hozzá, és képességmodelleket kereshet.

## <a name="properties"></a>Tulajdonságok

A tulajdonságok olyan adatmezők, amelyeket egy [kapcsolatban](#interface) definiálnak, és amelyek egy digitális ikerpár bizonyos állapotát képviselik. A tulajdonságokat írásvédettként vagy írhatóként deklarálhatja. Az írásvédett tulajdonságokat, például a sorozatszámot, magát az [IoT Plug and Play eszközön](#iot-plug-and-play-device) futó kód állítja be.  Írható tulajdonságok, például egy riasztási küszöbértéket, általában a felhőalapú IoT-megoldásból vannak beállítva.

## <a name="public-model-repository"></a>Nyilvános modelltárház

Egyetlen nyilvános modelltárház található, amely a tanúsított [eszközök](#device-certification) [eszközképességi modelljeit](#device-capability-model) és [felületeit](#interface) tárolja. A nyilvános modelltárház [közös felületdefiníciókat](#common-interface) is tárol.

## <a name="registration-id"></a>Regisztrációs azonosító

A regisztrációs azonosító egyedileg azonosítja az eszközt az [eszközkiépítési szolgáltatásban.](#device-provisioning-service) Ez az azonosító nem egyezik meg az [it-elosztóban](#azure-iot-hub)lévő eszközegyedi azonosítójával.

## <a name="scope-id"></a>Hatókör-azonosító

A hatókörazonosító hatókör e-kiszolgáló egyedileg azonosítja [az eszközkiépítési szolgáltatás](#device-provisioning-service) példánya.

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód

A megosztott hozzáférésű aláírások az SHA-256 biztonságos kivonatain vagy URI-in alapuló hitelesítési mechanizmus. A megosztott hozzáférésű aláírás hitelesítésének két összetevője van: egy megosztott hozzáférési házirend és egy közös hozzáférési aláírás (más néven jogkivonat). Az [IoT Plug and Play-eszköz](#iot-plug-and-play-device) megosztott hozzáférésű aláírást használ az [IoT-központtal](#azure-iot-hub)való hitelesítéshez.

## <a name="solution-developer"></a>Megoldás fejlesztője

A megoldás fejlesztője hozza létre a megoldás háttér. A megoldásfejlesztő általában azure-erőforrásokkal, például [az IoT Hub-](#azure-iot-hub) és [modelladattárakkal,](#model-repository)illetve az [IoT Central](#azure-iot-central)szolgáltatással működik.

## <a name="telemetry"></a>Telemetria

Az [illesztőfelületen](#interface) definiált telemetriai mezők méréseket jelölnek. Ezek a mérések általában olyan értékek, mint például az [IoT Plug and Play eszköz](#iot-plug-and-play-device) által adatfolyamként küldött érzékelőértékek.

## <a name="visual-studio-code"></a>Visual Studio-kód

A Visual Studio kódegy modern kódszerkesztő, amely több platformon is elérhető. A bővítmények, például az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) csomag, lehetővé teszik, hogy testre szabja a szerkesztőt a fejlesztési forgatókönyvek széles körének támogatásához.
