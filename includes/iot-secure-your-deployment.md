---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748752"
---
Ez a cikk az Azure IoT-alapú eszközök internetes hálózatának (IoT) infrastruktúrájának védelméhez a következő részletességi szintet tartalmazza. Az egyes összetevők konfigurálásához és üzembe helyezéséhez a megvalósítási szint részleteire hivatkozik. Összehasonlításokat és választási lehetőségeket is biztosít a különböző versengő módszerek között.

Az Azure IoT üzembe helyezésének biztonságossá tétele a következő három biztonsági területre osztható:

* **Eszközbiztonság:** Az IoT-eszköz biztonságossá tétele a vadonban üzembe helyezett állapotban.

* **Kapcsolatbiztonság:** Az IoT-eszköz és az IoT Hub között továbbított összes adat bizalmas és hamisíthatatlan.

* **Felhőbiztonság:** Az adatok védelmének eszköze, miközben azok áthaladnak, és a felhőben tárolják.

![Három biztonsági terület](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Biztonságos eszközkiépítés és hitelesítés

Az IoT-megoldásgyorsítók az alábbi két módszerrel biztosítják az IoT-eszközöket:

* Azáltal, hogy egy egyedi identitáskulcs (biztonsági jogkivonatok) minden eszköz, amely az eszköz által az IoT Hub kommunikációra használható.

* Az eszközön lévő [X.509-es tanúsítvány](https://www.itu.int/rec/T-REC-X.509-201210-S) és a személyes kulcs használatával az eszköz hitelesítéséhez az IT Hub. Ez a hitelesítési módszer biztosítja, hogy az eszközön lévő személyes kulcs az eszközön kívül bármikor ne legyen ismert, magasabb szintű biztonságot nyújtva.

A biztonsági jogkivonat-módszer hitelesítést biztosít az eszköz által az IoT Hubhoz intézett minden egyes híváshoz azáltal, hogy a szimmetrikus kulcsot társítja az egyes hívásaihoz. Az X.509-alapú hitelesítés lehetővé teszi az IoT-eszközök hitelesítését a fizikai rétegen a TLS-kapcsolat létrehozása részeként. A biztonsági token-alapú módszer az X.509-es hitelesítés nélkül is használható, ami kevésbé biztonságos minta. A két módszer közötti választást elsősorban az határozza meg, hogy mennyire kell biztonságosnak lennie az eszköz hitelesítésének, és a biztonságos tárolás rendelkezésre állása az eszközön (a személyes kulcs biztonságos tárolásához).

## <a name="iot-hub-security-tokens"></a>Az IoT Hub biztonsági jogkivonatai

Az IoT Hub biztonsági jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a hálózaton. Emellett a biztonsági jogkivonatok időérvényessége és hatóköre korlátozott. Az Azure IoT SDK-k automatikusan generálnak jogkivonatokat anélkül, hogy bármilyen speciális konfigurációt. Egyes forgatókönyvek azonban megkövetelik a felhasználótól, hogy közvetlenül hozzon létre és használjon biztonsági jogkivonatokat. Ezek a forgatókönyvek közé tartozik az MQTT, AMQP vagy HTTP felületek közvetlen használata, vagy a jogkivonat-szolgáltatás minta megvalósítása.

A biztonsági jogkivonat szerkezetéről és használatáról további részletek a következő cikkekben találhatók:

* [Biztonsági jogkivonat-struktúra](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [SAS-tokenek használata eszközként](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Minden IoT Hub rendelkezik egy [identitás-beállításjegyzék,](../articles/iot-hub/iot-hub-devguide-identity-registry.md) amely segítségével hozzon létre egy eszközön ként i erőforrást a szolgáltatásban, például egy várólistát, amely tartalmazza a repülés közbeni felhőből az eszközre üzeneteket, és az eszköz felé néző végpontok elérésének engedélyezéséhez. Az IoT Hub identitásjegyzék biztonságos tárolását biztosítja az eszközidentitások és a biztonsági kulcsok a megoldás. Az egyes vagy eszközidentitások csoportjai hozzáadhatók egy engedélyezési listához vagy egy tiltólistához, amely lehetővé teszi az eszközhozzáférés teljes körű szabályozását. Az alábbi cikkek további részleteket tartalmaznak az identitásjegyzék és a támogatott műveletek szerkezetéről.

[Az IoT Hub támogatja az olyan protokollokat, mint az MQTT, az AMQP és a HTTP.](../articles//iot-hub/iot-hub-devguide-security.md) Ezek a protokollok mindegyike az IoT-eszköz biztonsági jogkivonatait használja az IoT Hubhoz másképpen:

* AMQP: SASL PLAIN és AMQP`{policyName}@sas.root.{iothubName}` jogcímeken alapuló biztonság ( IoT hub szintű jogkivonatok; `{deviceId}` az eszközható jellegű tokenekkel).

* MQTT: A `{deviceId}` CONNECT `{ClientId}`csomag `{IoThubhostname}/{deviceId}` a , a **Felhasználónév** mezőben és egy SAS-jogkivonatként használatos a **Jelszó** mezőben.

* HTTP: Az érvényes jogkivonat az engedélyezési kérelem fejlécében található.

Az IoT Hub identitásjegyzéke eszközenkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés konfigurálására használható. Ha azonban egy IoT-megoldás már jelentős befektetéssel rendelkezik egy [egyéni eszközidentitás-beállításjegyzékbe és/vagy hitelesítési sémába,](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)integrálható egy meglévő infrastruktúrába az IoT Hub-szolgáltatással egy jogkivonat-szolgáltatás létrehozásával.

### <a name="x509-certificate-based-device-authentication"></a>X.509 tanúsítványalapú eszközhitelesítés

Az [eszközalapú X.509-es tanúsítvány](../articles/iot-hub/iot-hub-devguide-security.md) és a hozzá tartozó személyes és nyilvános kulcspár használata további hitelesítést tesz lehetővé a fizikai rétegen. A személyes kulcs biztonságosan tárolódik az eszközön, és nem észlelhető az eszközön kívül. Az X.509 tanúsítvány információkat tartalmaz az eszközről, például az eszközazonosítót és egyéb szervezeti részleteket. A tanúsítvány aláírása a személyes kulcs használatával jön létre.

Magas szintű eszközkiépítési folyamat:

* Azonosító társítása fizikai eszközhöz – eszközidentitáshoz és/vagy Az eszközgyártás vagy üzembe helyezés során az eszközhöz társított X.509 tanúsítványhoz.

* Hozzon létre egy megfelelő identitásbejegyzést az IoT Hubban – eszközidentitás és a kapcsolódó eszközadatok az IoT Hub identitásjegyzékben.

* Biztonságosan tárolja az X.509 tanúsítvány ujjlenyomatát az IoT Hub identitásjegyzékében.

### <a name="root-certificate-on-device"></a>Főtanúsítvány az eszközön

Biztonságos TLS-kapcsolat létrehozása közben az IoT Hub, az IoT-eszköz hitelesíti az IoT Hub egy főtanúsítvány, amely része az eszköz SDK használatával. A C ügyfél SDK esetében a tanúsítvány\\a\\"c certs" mappában található a tárház gyökere alatt. Bár ezek a főtanúsítványok hosszú élettartamúak, még mindig lejárhatnak vagy visszavonhatók. Ha nincs mód a tanúsítvány frissítésére az eszközön, előfordulhat, hogy az eszköz később nem tud csatlakozni az IoT Hubhoz (vagy bármely más felhőszolgáltatáshoz). Az IoT-eszköz üzembe helyezése után a főtanúsítvány frissítésére szolgáló eszköz hatékonyan csökkenti ezt a kockázatot.

## <a name="securing-the-connection"></a>A kapcsolat biztonságossá tétele

Az IoT-eszköz és az IoT-központ közötti internetkapcsolat a Transport Layer Security (TLS) szabvány használatával védett. Az Azure IoT ebben a sorrendben támogatja a [TLS 1.2,](https://tools.ietf.org/html/rfc5246)TLS 1.1 és TLS 1.0.Azure IoT. A TLS 1.0 támogatása csak a visszamenőleges kompatibilitást biztosítja. Ellenőrizze [a TLS-támogatás az IoT Hub,](../articles/iot-hub/iot-hub-tls-support.md) hogyan konfigurálhatja a hub tls 1.2, mivel ez biztosítja a legnagyobb biztonságot.

## <a name="securing-the-cloud"></a>A felhő védelme

Az Azure IoT Hub lehetővé teszi a [hozzáférés-vezérlési szabályzatok](../articles/iot-hub/iot-hub-devguide-security.md) meghatározását minden egyes biztonsági kulcshoz. A következő engedélykészletet használja az IoT Hub egyes végpontjaihoz való hozzáférés engedélyezéséhez. Az engedélyek a funkciók alapján korlátozzák az IoT Hubhoz való hozzáférést.

* **RegistryRead**. Olvasási hozzáférést biztosít az identitás-beállításjegyzékhez. További információt az [identitásjegyzék ben](../articles/iot-hub/iot-hub-devguide-identity-registry.md)talál.

* **RegistryReadWrite**. Olvasási és írási hozzáférést biztosít az identitásjegyzékhez. További információt az [identitásjegyzék ben](../articles/iot-hub/iot-hub-devguide-identity-registry.md)talál.

* **ServiceConnect**. Hozzáférést biztosít a felhőszolgáltatás felé irányuló kommunikációhoz és a végpontok figyeléséhez. Például engedélyt ad a háttér-felhőszolgáltatásoknak az eszközről a felhőbe irányuló üzenetek fogadására, a felhőből az eszközre irányuló üzenetek küldésére és a megfelelő kézbesítési nyugták lekérésére.

* **DeviceConnect**lehetőséget. Hozzáférést biztosít az eszközfelé néző végpontokhoz. Például engedélyt ad az eszközről a felhőbe irányuló üzenetek küldésére és a felhőből az eszközre irányuló üzenetek fogadására. Ezt az engedélyt az eszközök használják.

Kétféleképpen szerezhet be **DeviceConnect-engedélyeket** az IoT Hubbal [biztonsági jogkivonatokkal:](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)eszközidentitás-kulcs vagy megosztott hozzáférési kulcs használatával. Fontos megjegyezni továbbá, hogy az eszközökről elérhető összes funkció az előtaggal `/devices/{deviceId}`rendelkező végpontokon való tervezés sel rendelkezik.

A szolgáltatás-összetevők csak a megfelelő engedélyeket biztosító megosztott hozzáférési házirendek használatával [hozhatnak létre biztonsági jogkivonatokat.](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components)

Az Azure IoT Hub és más szolgáltatások, amelyek a megoldás részét képezik, lehetővé teszik a felhasználók kezelését az Azure Active Directory használatával.

Az Azure IoT Hub által bevitt adatokat számos szolgáltatás, például az Azure Stream Analytics és az Azure blob storage is felhasználhatja. Ezek a szolgáltatások lehetővé teszik a felügyeleti hozzáférést. További információ ezekről a szolgáltatásokról és a rendelkezésre álló lehetőségekről:

* [Azure Cosmos DB:](https://azure.microsoft.com/services/cosmos-db/)A méretezhető, teljes indexelt adatbázis-szolgáltatás félig strukturált adatok, amely kezeli a metaadatokat a kihelyezett eszközök, például attribútumok, konfigurációs és biztonsági tulajdonságok. Az Azure Cosmos DB nagy teljesítményű és nagy átviteli sebességű feldolgozást, séma-független adatindexelést és gazdag SQL-lekérdezési felületet kínál.

* [Azure Stream Analytics:](https://azure.microsoft.com/services/stream-analytics/)Valós idejű adatfolyam-feldolgozás a felhőben, amely lehetővé teszi, hogy gyorsan fejlesszen ki és telepítsen egy alacsony költségű elemzési megoldást az eszközök, érzékelők, infrastruktúra és alkalmazások valós idejű elemzéseinek feltárására. A teljes körűen felügyelt szolgáltatásból származó adatok bármely kötetre skálázhatók, miközben továbbra is nagy átviteli, alacsony késésű és rugalmasság érhető el.

* [Azure App Services:](https://azure.microsoft.com/services/app-service/)Felhőalapú platform, amely hatékony webes és mobilalkalmazásokat hozhat létre, amelyek bárhol csatlakozhatnak az adatokhoz; a felhőben vagy a helyszínen. Vonzó mobilalkalmazásokat készíthet iOS, Android és Windows rendszerre. Integrálja szoftverét szolgáltatásként (SaaS) és vállalati alkalmazásokkal, és minden hatóel őket képes kapcsolattal több tucat felhőalapú szolgáltatással és vállalati alkalmazással. Kód a kedvenc nyelv és IDE (.NET, Node.js, PHP, Python, vagy Java), hogy építsenek webes alkalmazások és API-k gyorsabb, mint valaha.

* [Logic Apps:](https://azure.microsoft.com/services/app-service/logic/)Az Azure App Service Logikai alkalmazások funkciója segít integrálni az IoT-megoldást a meglévő üzletági rendszerekbe, és automatizálni a munkafolyamatokat. A Logic Apps lehetővé teszi a fejlesztők számára, hogy olyan munkafolyamatokat tervezzenek, amelyek egy eseményindítóból indulnak ki, majd egy sor lépést hajtanak végre – olyan szabályokat és műveleteket, amelyek hatékony összekötőket használnak az üzleti folyamatokba való integráláshoz. A Logic Apps beépített kapcsolatot biztosít az SaaS, a felhőalapú és a helyszíni alkalmazások hatalmas ökoszisztémájához.

* [Azure Blob storage:](https://azure.microsoft.com/services/storage/)Megbízható, gazdaságos felhőalapú tárhely az eszközök által a felhőbe küldött adatok számára.

## <a name="conclusion"></a>Összegzés

Ez a cikk áttekintést nyújt az IoT-infrastruktúra Azure IoT-n keresztüli tervezéséhez és üzembe helyezéséhez szükséges megvalósítási szint részleteiről. Az egyes összetevők biztonságosbeállítása kulcsfontosságú a teljes IoT-infrastruktúra biztosításához. Az Azure IoT-ben elérhető tervezési lehetőségek bizonyos szintű rugalmasságot és választási lehetőséget biztosítanak; azonban minden választásnak biztonsági következményei lehetnek. Javasoljuk, hogy ezeket a döntéseket kockázat-/költségértékeléssel értékeljék.
