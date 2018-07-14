---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7d7cd8a197a89781a75f47bb4b4e2ec8fe7c3cb4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38945619"
---
# <a name="secure-your-iot-deployment"></a>Az IoT üzemelő példányának védelme

Ez a cikk a következő részletességi szintje az Azure IoT-alapú eszközök internetes hálózata (IoT) infrastruktúra biztonságossá tételéhez. Megvalósítási szint konfigurálása és telepítése az egyes összetevők részleteit hivatkozik. Összehasonlítások és különböző, egymással versengő módszerek között is biztosít.

Az Azure IoT-környezet biztonságossá tétele a következő három biztonsági területre osztható:

* **Eszközbiztonság**: az IoT eszköz védelme közben van üzembe helyezve a helyettesítő karakterek.
* **Kapcsolatbiztonság**: bizalmas és hamisíthatatlan biztosítása az IoT eszköz és az IoT Hub között továbbított összes adat.
* **Cloud Security**: keresztül halad át, és a felhőben tárolt adatok védelmét biztosítani.

![A három biztonsági terület][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Device Provisioning Service és a hitelesítés biztonságossá tétele

Az IoT-megoldásgyorsítók biztonságos IoT-eszközök által az alábbi két módszer:

* Azáltal, hogy egy egyedi azonosító kulcsot (biztonsági jogkivonatokat) minden egyes eszközhöz, amely az eszköz által az IoT hubbal való kommunikációhoz használható.
* Az eszközön futó használatával [X.509-tanúsítvány] [ lnk-x509] és hitelesíteni az eszközt az IoT hubhoz eszközként titkos kulcs. Ez a hitelesítési módszer biztosítja, hogy a titkos kulcs az eszközön nem ismert kívül az eszköz bármikor magasabb szintű biztonságot biztosít.

A biztonsági jogkivonat módszert biztosít a hitelesítést minden egyes híváshoz az eszköz által az IoT hubhoz a szimmetrikus kulcs az egyes műveletmeghívásokhoz társításával. X.509-alapú hitelesítés lehetővé teszi, hogy az IoT-eszközök hitelesítési TLS kapcsolat létrehozásának részeként a fizikai rétegben. A biztonsági jogkivonat-alapú módszer az X.509-hitelesítéssel, amelyek kevésbé biztonságos minta anélkül is használható. A választás a két módszer elsődlegesen szabja meg hogyan biztonságos eszköz hitelesítési kell lennie, és biztonságos tárolási (így biztonságosan tárolhatja a titkos kulcsot) az eszközön rendelkezésre állását.

## <a name="iot-hub-security-tokens"></a>IoT Hub biztonsági tokenek

Az IoT Hub biztonsági jogkivonatokat használ hitelesítéséhez, eszközöket és szolgáltatásokat elkerülése érdekében a kulcsok küldése a hálózaton. Emellett biztonsági jogkivonatok érvényesség, és a hatókör korlátozott. Az Azure IoT SDK-k anélkül, hogy semmiféle speciális beállítást automatikusan jogkivonatokat hoz létre. Bizonyos esetekben azonban szükség van a felhasználó létrehozásához, és biztonsági jogkivonatokat közvetlenül használni. Ilyen például, amikor az MQTT, AMQP vagy HTTP-felületek közvetlen használatát, vagy jogkivonat-szolgáltatás minta megvalósítását.

További részleteket a biztonsági jogkivonat és annak használatát struktúráját a következő cikkekben található:

* [Biztonsági jogkivonat szerkezete][lnk-security-tokens]
* [SAS-tokeneket használó eszközként][lnk-sas-tokens]

Minden IoT-központ rendelkezik egy [eszközidentitás-jegyzék] [ lnk-identity-registry] , amely használható, eszköz erőforrások létrehozása a szolgáltatásban, például egy üzenetsoron, amely tartalmazza a felhőből az eszközre irányuló üzenetek átvitel közben, és hozzáférést a eszköz felé néző végpontok. Az IoT Hub-identitásjegyzék eszközidentitások és a egy megoldás esetében a biztonsági kulcsok biztonságos tárolást biztosít. Személy vagy csoport eszközidentitások lehet hozzáadni az engedélyezési listán, vagy egy tiltólista engedélyezése az eszközhozzáférés teljes vezérlését. A következő cikkekben talál további részletekkel szolgálnak az eszközidentitás-jegyzék és a támogatott műveletek struktúráját.

[IoT Hub által támogatott protokollok, például MQTT, AMQP és a HTTP][lnk-protocols]. Eltérően használja ezeket a protokollokat mindegyike biztonsági jogkivonatokat az IoT-eszközről az IoT hubhoz:

* Az AMQP: SASL egyszerű, és AMQP jogcímalapú biztonsági (`{policyName}@sas.root.{iothubName}` IoT hub-szintű jogkivonatokkal; `{deviceId}` eszköz hatókörű jogkivonatokkal).
* MQTT: Csatlakozás csomagot használ `{deviceId}` , a `{ClientId}`, `{IoThubhostname}/{deviceId}` a a **felhasználónév** mezőt és a egy SAS-tokent a a **jelszó** mező.
* HTTP: Érvényes token az engedélyezési kérés fejlécében.

Az IoT Hub eszközidentitás-jegyzék segítségével az eszközönkénti biztonsági hitelesítő adatok konfigurálása és a hozzáférés-vezérlés. Azonban ha egy IoT-megoldás már jelentős befektetéseket szerepel egy [egyéni identitás beállításjegyzék és/vagy hitelesítési séma][lnk-custom-auth], egy meglévő infrastruktúra az IoT Hub integrálható Hozzon létre egy jogkivonat-szolgáltatás.

### <a name="x509-certificate-based-device-authentication"></a>X.509 tanúsítvány alapú eszközhitelesítés

Használatát egy [eszközalapú X.509-tanúsítvány] [ lnk-use-x509] és a kapcsolódó privát és nyilvános kulcsból álló párként lehetővé teszi, hogy a további hitelesítési a fizikai rétegben. A titkos kulcs lesz biztonságosan tárolva az eszközön, és nem észlelhető az eszközön kívül. X.509-tanúsítvány információ az eszköz, például az eszköz azonosítója, és egyéb szervezeti adatait tartalmazza. A tanúsítvány-aláírás a titkos kulcs használatával jön létre.

Magas szintű eszközt a kiépítési folyamat:

* Egy fizikai eszközre – eszközidentitást és/vagy eszköz gyártási vagy az üzembe helyezés során az eszközhöz társított X.509-tanúsítvány azonosítót rendel.
* Hozzon létre egy megfelelő identitás az IoT Hub-eszközidentitást, valamint a kapcsolódó eszközadatokat az IoT Hub identitásjegyzékében.
* Tárolja biztonságosan X.509-tanúsítvány ujjlenyomata az IoT Hub identitásjegyzékében.

### <a name="root-certificate-on-device"></a>Legfelső szintű tanúsítványt az eszköz

Az IoT Hub biztonságos TLS kapcsolódás közben, az IoT eszköz hitelesíti az IoT Hub egy legfelső szintű tanúsítványt használ, amely az eszköz SDK része. A C ügyfél SDK-t, a tanúsítványt a mappában található "\\c\\tanúsítványok" a tárház gyökerében. Bár ezek a legfelső szintű tanúsítványok hosszú élettartamú, továbbra is lehetséges, hogy lejáratukig vagy vonható vissza. Ha nem tudja az eszközön a tanúsítvány frissítése, az eszköz nem lehet képes kapcsolódni. ezt követően az IoT Hub (vagy bármely más felhőalapú szolgáltatás). A legfelső szintű tanúsítvány frissítése az IoT eszköz ténylegesen üzembe helyezése után azt kellene csökkenti a kockázat.

## <a name="securing-the-connection"></a>A kapcsolat biztonságossá tétele

Az IoT-eszköz és az IoT Hub közötti internetes kapcsolat a Transport Layer Security (TLS) szabvány használatával lett biztonságossá téve. Az Azure IoT támogatja [a TLS 1.2][lnk-tls12], TLS 1.1 és TLS 1.0, az itt látható sorrendben. A TLS 1.0 támogatása csak visszamenőleges kompatibilitásra van megadva. Ha lehetséges használja a TLS 1.2-es, a lehető legnagyobb biztonságot biztosít.

## <a name="securing-the-cloud"></a>A felhő biztonságossá tétele

Az Azure IoT Hub lehetővé teszi, hogy a definíciójának [hozzáférés-vezérlési házirendeket] [ lnk-protocols] minden egyes biztonsági kulcs. Az egyes IoT Hub-végpontok hozzáférést használja a következő engedélyekkel vannak beállítva. Engedélyek férjenek hozzá az IoT Hub funkciók alapján.

* **RegistryRead**. Olvasási hozzáférést biztosít az identitásjegyzékhez biztosít. További információkért lásd: [eszközidentitás-jegyzék][lnk-identity-registry].
* **RegistryReadWrite**. Olvasási és írási hozzáférést biztosít az identitásjegyzékhez. További információkért lásd: [eszközidentitás-jegyzék][lnk-identity-registry].
* **ServiceConnect**. Engedélyezi a hozzáférést a felhőalapú szolgáltatás által használt kommunikációs és végpontok monitorozása. Például azt engedélyt háttérbeli felhőszolgáltatásokat eszköz – felhő üzeneteket fogadni, üzenetküldés a felhőből az eszközre és a megfelelő kézbesítési visszaigazolások lekéréséhez.
* **DeviceConnect**. Engedélyezi a hozzáférést a eszköz felé néző végpontok. Például hogy engedélyt ad a eszköz a felhőbe irányuló üzeneteket küldjön és fogadjon a felhőből az eszközre irányuló üzenetek. Ez az engedély eszközök használják.

Kétféleképpen juthat **DeviceConnect** az IoT Hub és az engedélyek [biztonsági jogkivonatokat][lnk-sas-tokens]: egy device identity kulcs vagy egy közös hozzáférési kulcs használatával. Fontos továbbá, vegye figyelembe, hogy minden funkciója elérhető eszközöket tesz elérhetővé Tervező előtag rendelkező végpontokon `/devices/{deviceId}`.

[Szolgáltatás-összetevők csak hozhat létre a biztonsági jogkivonatokat] [ lnk-service-tokens] megosztott hozzáférési házirendek a megfelelő engedélyek használata.

Az Azure IoT Hub és egyéb szolgáltatások, amelyek a megoldás részét képezhetik lehetővé teszik a felhasználók az Azure Active Directory használatával felügyeleti.

Azure IoT Hub által betöltött adatok különböző szolgáltatások, például az Azure Stream Analytics és az Azure blob storage képes használni. Ezek a szolgáltatások felügyeleti hozzáférés engedélyezése. További információ ezen szolgáltatásokkal és az elérhető lehetőségek közül:

* [Az Azure Cosmos DB][lnk-cosmosdb]: méretezhető, teljes körűen indexelt adatbázis-szolgáltatás, amely felügyeli az eszközök metaadatait, részben strukturált adatok számára hozza létre, például az attribútumokat, konfigurációs és biztonsági tulajdonságait. Az Azure Cosmos DB kínál a nagy teljesítményű és nagy átviteli sebességű feldolgozására, sémafüggetlen indexelését, adatok és a egy részletes SQL-lekérdezési felületet.
* [Az Azure Stream Analytics][lnk-asa]: valós idejű streamfeldolgozás a felhőben, amely lehetővé teszi, hogy gyors fejlesztése, és a valós idejű adatok elemzése az eszközök, érzékelők, infrastruktúra, alacsony költségű analytics megoldás üzembe helyezése és alkalmazásokat. E teljes körűen felügyelt szolgáltatás az adatokat tetszőleges adatmennyiséghez a nagy átviteli sebességet, közel valós idejű és rugalmasság méretezheti.
* [Az Azure App Services][lnk-appservices]: egy felhőalapú platform, amely hatékony webes és mobilalkalmazások bárhol adatokat; a felhőben vagy a helyszínen. Vonzó alkalmazások készítése iOS, Android és Windows rendszerre. Integrálható a szoftvert, mint a szoftverszolgáltatások (SaaS) és a vállalati alkalmazásait-az-a – azonnali kapcsolódás több tucat felhőalapú szolgáltatáshoz és vállalati alkalmazáshoz. Kedvenc nyelvének és integrált Fejlesztőkörnyezetének (.NET, Node.js, PHP, Python vagy Java) hozhat létre webalkalmazásokat és API-k minden eddiginél gyorsabban, a kód.
* [A Logic Apps][lnk-logicapps]: az Azure App Service Logic Apps funkciója segít integrálni az IoT-megoldás a meglévő üzleti rendszerekhez és a munkafolyamatok automatizálása. A Logic Apps segítségével a fejlesztők olyan egy eseményindítóval kezdődnek, majd végrehajtanak bizonyos lépéseket munkafolyamatokat – szabályok és műveletek, amely integrálható az üzleti folyamatok hatékony összekötők használatával. A Logic Apps-az-– azonnali kapcsolatok beépítésével az SaaS, felhőalapú, és kínál a helyszíni alkalmazásokat.
* [Az Azure blob storage-bA][lnk-blob]: megbízható, gazdaságos felhőalapú tárolás az adatok, amelyek az eszközök küldenek a felhőbe.

## <a name="conclusion"></a>Összegzés

Ez a cikk áttekintést nyújt az implementáció szintű adatok tervezéséhez és üzembe helyezése IoT-infrastruktúrát az Azure IoT használatával. Biztonságos minden egyes összetevő konfigurálása, mint az általános IoT-infrastruktúra biztonságossá tétele a kulcs. Elérhető az Azure IoT a tervezési döntések bizonyos fokú rugalmasság és választási lehetőség; adja meg. Minden választási lehetőséget azonban biztonsági hatásai lehetnek. Javasoljuk, hogy ezek a lehetőségek mindegyike lehet értékelni egy kockázata és költsége felmérést.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components
[lnk-cosmosdb]: https://azure.microsoft.com/services/cosmos-db/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
