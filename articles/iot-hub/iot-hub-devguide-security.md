---
title: Az Azure IoT Hub biztonságának megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – hogyan szabályozhatja az IoT Hubhoz való hozzáférést az eszközalkalmazások hoz és a háttéralkalmazásokhoz. A biztonsági jogkivonatokról és az X.509-tanúsítványok támogatásáról tartalmaz információkat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: d37320ad6e8caf3300756466eabe8de29a339c4a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258286"
---
# <a name="control-access-to-iot-hub"></a>IoT Hub-hozzáférés szabályozása

Ez a cikk ismerteti az IoT hub védelmének lehetőségeit. Az IoT Hub *engedélyeket* használ az egyes IoT-hub-végpontokhoz való hozzáférés engedélyezéséhez. Az engedélyek a funkciók alapján korlátozzák az IoT-központhoz való hozzáférést.

Ez a cikk bemutatja:

* A különböző engedélyeket, amelyek et adhat egy eszköz vagy háttéralkalmazás az IoT hub eléréséhez.
* A hitelesítési folyamat és az engedélyek ellenőrzéséhez használt jogkivonatok.
* A hitelesítő adatok hatóköre az adott erőforrásokhoz való hozzáférés korlátozására.
* Az IoT Hub támogatása X.509-es tanúsítványokhoz.
* Egyéni eszközhitelesítési mechanizmusok, amelyek meglévő eszközidentitás-jegyzékeket vagy hitelesítési sémákat használnak.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub bármelyik végpontjának eléréséhez megfelelő engedélyekkel kell rendelkeznie. Például egy eszköznek tartalmaznia kell egy biztonsági hitelesítő adatokat tartalmazó jogkivonatot az IoT Hubnak küldött összes üzenettel együtt.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

Az [engedélyeket](#iot-hub-permissions) az alábbi módokon adhat meg:

* **IoT-központ szintű megosztott hozzáférési szabályzatok.** A megosztott hozzáférési házirendek az engedélyek bármilyen kombinációját [biztosíthatják.](#iot-hub-permissions) Szabályzatokat az [Azure Portalon,](https://portal.azure.com)programozott módon az [IoT Hub Resource REST API-k](/rest/api/iothub/iothubresource)használatával, vagy az [aziot hub házirend](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI használatával. Egy újonnan létrehozott IoT hub a következő alapértelmezett házirendekkel rendelkezik:
  
  | Megosztott hozzáférési házirend | Engedélyek |
  | -------------------- | ----------- |
  | iothubowner | Minden engedély |
  | szolgáltatás | **ServiceConnect** engedélyek |
  | eszköz | **DeviceConnect** engedélyek |
  | registryRead | **Rendszerleíró adatbázisOlvasási** engedélyek |
  | registryReadWrite | **RegistryRead** és **RegistryWrite** engedélyek |

* **Eszközönkénti biztonsági hitelesítő adatok**. Minden IoT Hub tartalmaz egy [identitás-beállításjegyzék](iot-hub-devguide-identity-registry.md) Az identitás jegyzékben lévő minden egyes eszköz, konfigurálhatja a biztonsági hitelesítő adatokat, amelyek a **DeviceConnect** engedélyek hatóköre a megfelelő eszköz végpontok.

Például egy tipikus IoT-megoldásban:

* Az eszközfelügyeleti összetevő a *registryReadWrite* házirendet használja.
* Az eseményfeldolgozó-összetevő a *szolgáltatásházirendet* használja.
* A futásidejű eszköz üzleti logikai összetevője a *szolgáltatásházirendet* használja.
* Az egyes eszközök az IoT hub identitásbeállításában tárolt hitelesítő adatokhasználatával csatlakoznak.

> [!NOTE]
> A részletes információkért tekintse meg az [engedélyeket.](#iot-hub-permissions)

## <a name="authentication"></a>Hitelesítés

Az Azure IoT Hub egy biztonsági jogkivonatnak a megosztott hozzáférési szabályzatok és az identitásjegyzékbeli biztonsági hitelesítő adatok alapján végzett ellenőrzésével ad hozzáférést a végpontokhoz.

A biztonsági hitelesítő adatok, például a szimmetrikus kulcsok, soha nem kerülnek át a hálózaton keresztül.

> [!NOTE]
> Az Azure IoT Hub erőforrás-szolgáltató az Azure-előfizetésen keresztül, csakúgy, mint az [Azure Resource Manager](../azure-resource-manager/management/overview.md)összes szolgáltatója.

A biztonsági jogkivonatok létrehozásáról és használatáról az [IoT Hub biztonsági jogkivonatai](iot-hub-devguide-security.md#security-tokens)című témakörben talál további információt.

### <a name="protocol-specifics"></a>Protokoll-sajátosságok

Minden támogatott protokoll, például az MQTT, az AMQP és a HTTPS különböző módokon szállítja a jogkivonatokat.

Az MQTT használatakor a CONNECT csomag az eszközazonosítót `{iothubhostname}/{deviceId}` Ügyfélazonosítóként, a Felhasználónév mezőben, a Jelszó mezőben pedig egy SAS-jogkivonatot használ. `{iothubhostname}`az IoT hub teljes CName-jének kell lennie (például contoso.azure-devices.net).

Az [AMQP](https://www.amqp.org/)használatakor az IoT Hub támogatja a [SASL PLAIN](https://tools.ietf.org/html/rfc4616) és [az AMQP jogcímalapú biztonságát.](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)

Ha AMQP jogcímalapú biztonsági, a szabvány határozza meg, hogyan kell továbbítani ezeket a jogkivonatokat.

A SASL PLAIN esetében a **felhasználónév** a következő lehet:

* `{policyName}@sas.root.{iothubName}`ha IoT hub-szintű jogkivonatokat használ.
* `{deviceId}@sas.{iothubname}`ha eszközhatójelű jogkivonatokat használ.

A jelszómező mindkét esetben tartalmazza a jogkivonatot, az [IoT Hub biztonsági jogkivonataiban leírtak szerint.](iot-hub-devguide-security.md#security-tokens)

A HTTPS úgy valósítja meg a hitelesítést, hogy egy érvényes jogkivonatot tartalmaz az **engedélyezési** kérelem fejlécében.

#### <a name="example"></a>Példa

Felhasználónév (deviceId a kis- és nagybetűk megkülönböztetése):`iothubname.azure-devices.net/DeviceId`

Jelszó (SAS-jogkivonatot hozhat létre a CLI-bővítmény paranccsal, az [iot hub generate-sas-tokendel](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)vagy az [Azure IoT-eszközök a Visual Studio-kódhoz értékével):](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan generálnak jogkivonatokat, amikor csatlakozik a szolgáltatáshoz. Bizonyos esetekben az Azure IoT SDK-k nem támogatják az összes protokollt vagy az összes hitelesítési módszert.

### <a name="special-considerations-for-sasl-plain"></a>A SASL PLAIN különleges szempontjai

A SASL PLAIN amqp használatával, az IoT hubhoz csatlakozó ügyfél minden Egyes TCP-kapcsolathoz egyetlen jogkivonatot használhat. A jogkivonat lejártakor a TCP-kapcsolat bontja a kapcsolatot a szolgáltatásról, és újrakapcsolatot indít. Ez a viselkedés, bár nem okoz problémát egy háttéralkalmazás számára, a következő okok miatt káros az eszközalkalmazások számára:

* Az átjárók általában sok eszköz nevében csatlakoznak. A SASL PLAIN használatakor külön TCP-kapcsolatot kell létrehozniuk az IoT hubhoz csatlakozó minden egyes eszközhöz. Ez a forgatókönyv jelentősen növeli az áram- és hálózati erőforrások fogyasztását, és növeli az egyes eszközkapcsolatok késését.

* Erőforrás-korlátozott eszközök hátrányosan érinti a megnövekedett erőforrások használata újra minden jogkivonat lejárta után.

## <a name="scope-iot-hub-level-credentials"></a>Hatókör IoT-központ szintű hitelesítő adatok

Az IoT-központ szintű biztonsági házirendek korlátozott erőforrás URI-val létrehozott jogkivonatok létrehozásával. Az eszközről a felhőbe irányuló üzenetek eszközről történő küldésének végpontja például az **/devices/{deviceId}/messages/events.** A **DeviceConnect** engedélyekkel rendelkező IoT-központ megosztott hozzáférési szabályzatot is használhatja egy olyan jogkivonat aláírásához, amelynek resourceURI értéke **/devices/{deviceId}**. Ez a megközelítés létrehoz egy jogkivonatot, amely csak az eszköz **eszközazonosító**nevében küldhet üzeneteket.

Ez a mechanizmus hasonló az [Event Hubs közzétevői házirendhez,](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)és lehetővé teszi az egyéni hitelesítési módszerek megvalósítását.

## <a name="security-tokens"></a>Biztonsági jogkivonatok

Az IoT Hub biztonsági jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a hálózaton. Emellett a biztonsági jogkivonatok időérvényessége és hatóköre korlátozott. [Az Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan generálnak jogkivonatokat anélkül, hogy bármilyen speciális konfigurációt. Egyes forgatókönyvek nem igényel biztonsági jogkivonatok közvetlen létrehozása és használata. Az ilyen forgatókönyvek a következők:

* Az MQTT, AMQP vagy HTTPS felületek közvetlen használata.

* A jogkivonat-szolgáltatás minta megvalósítása, ahogy azt az [egyéni eszköz hitelesítése.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

Az IoT Hub lehetővé teszi az eszközök számára az IoT Hubhasználatával történő [hitelesítést az X.509-es tanúsítványokkal.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Biztonsági jogkivonat-struktúra

A biztonsági jogkivonatok segítségével időalapú hozzáférést biztosíthat az eszközökhöz és szolgáltatásokhoz az IoT Hub adott funkcióihoz. Az IoT Hubhoz való csatlakozás engedélyezéséhez az eszközöknek és szolgáltatásoknak megosztott hozzáféréssel vagy szimmetrikus kulccsal aláírt biztonsági jogkivonatokat kell küldeniük. Ezeket a kulcsokat az identitásjegyzék ben egy eszközidentitás tárolja.

A megosztott hozzáférési kulccsal aláírt jogkivonat hozzáférést biztosít a megosztott hozzáférési szabályzat engedélyeihez társított összes funkcióhoz. Az eszközidentitás szimmetrikus kulccsal aláírt jogkivonat csak a **DeviceConnect** engedélyt ad a társított eszközidentitáshoz.

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

A várt értékek a következők:

| Érték | Leírás |
| --- | --- |
| {aláírás} |A Következő űrlap HMAC-SHA256 `{URL-encoded-resourceURI} + "\n" + expiry`aláírási karakterlánca: . **Fontos:** A kulcs dekódolja base64 és kulcsként használják a HMAC-SHA256 számítás végrehajtásához. |
| {resourceURI} |URI-előtag (szegmensenként) a tokenttartalmazó végpontok, kezdve az IoT hub állomásnevét (nincs protokoll). Például: `myHub.azure-devices.net/devices/device1` |
| {lejárat} |UTF8 karakterláncok másodpercek száma a 00:00:00 UTC 1970. |
| {URL-kódolt-resourceURI} |A kisbetűs erőforrás URI-jának kisbetűs URL-kódolása |
| {policyName} |Annak a megosztott hozzáférési szabályzatnak a neve, amelyre ez a jogkivonat hivatkozik. Hiányzik, ha a jogkivonat az eszköz-beállításjegyzék hitelesítő adataira hivatkozik. |

**Megjegyzés előtag:** Az URI-előtag szegmens, nem pedig karakter szerint számítja ki. Például `/a/b` a előtagja, `/a/b/c` de `/a/bc`nem a.

A következő Node.js kódrészlet egy **generateSasToken** nevű függvényt jelenít `resourceUri, signingKey, policyName, expiresInMins`meg, amely kiszámítja a jogkivonatot a bemenetekből. A következő szakaszok részletesen ismertetik, hogyan inicializálhatja a különböző bemenetek a különböző jogkivonat-használati esetek.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Összehasonlításképpen a biztonsági jogkivonat létrehozásához szükséges egyenértékű Python-kód a következő:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Az alábbiakban az előfeltételek telepítési utasításait találjuk.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


A C# szolgáltatásban a következő funkciók at hoz létre a biztonsági jogkivonat:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Mivel a jogkivonat érvényességi ideje érvényesítve van az IoT Hub-gépeken, a jogkivonatot generáló gép órajelének eltolódása minimálisnak kell lennie.

### <a name="use-sas-tokens-in-a-device-app"></a>SAS-jogkivonatok használata eszközalkalmazásban

Kétféleképpen szerezhet be **DeviceConnect-engedélyeket** az IoT Hubbal biztonsági jogkivonatokkal: [használjon szimmetrikus eszközkulcsot az identitásjegyzékből](#use-a-symmetric-key-in-the-identity-registry), vagy használjon [megosztott hozzáférési kulcsot](#use-a-shared-access-policy).

Ne feledje, hogy az eszközökről elérhető összes funkció `/devices/{deviceId}`az előtaggal rendelkező végpontokon való kialakításszerint elérhető.

> [!IMPORTANT]
> Az egyetlen módja annak, hogy az IoT Hub hitelesítse egy adott eszköz az eszköz identitásszimmetrikus kulcs használatával. Azokban az esetekben, amikor egy megosztott hozzáférési szabályzatot használnak az eszköz működésének eléréséhez, a megoldásnak a biztonsági jogkivonatot kibocsátó összetevőt megbízható alösszetevőként kell figyelembe vennie.

Az eszközfelé néző végpontok (a protokolltól függetlenül):

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Eszközről felhőbe irányuló üzenetek küldése. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Felhőből az eszközre irányuló üzenetek fogadása. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Szimmetrikus kulcs használata az identitásjegyzékben

Ha egy eszközidentitás szimmetrikus kulcsát használja egy jogkivonat létrehozásához, a token policyName (`skn`) eleme kimarad.

Például egy minden eszközfunkció eléréséhez létrehozott jogkivonatnak a következő paraméterekkel kell rendelkeznie:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* aláíró kulcs: bármilyen szimmetrikus `{device id}` kulcs az identitáshoz,
* nincs házirend név,
* lejárati idő.

Az előző Node.js függvényt használó példa a következő lehet:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Az eszköz1 összes funkciójához hozzáférést biztosít az eredmény:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> A CLI-bővítmény paranccsal, az [iot hub generate-sas-tokentel](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)vagy az [Azure IoT-eszközök a Visual Studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)paranccsal sas-jogkivonat ot hozhat létre.

### <a name="use-a-shared-access-policy"></a>Megosztott hozzáférési szabályzat használata

Amikor egy megosztott hozzáférési házirendből hoz `skn` létre jogkivonatot, állítsa a mezőt a házirend nevére. Ennek a házirendnek meg kell adnia a **DeviceConnect** engedélyt.

A közös hozzáférési házirendek eszközfunkciók eléréséhez való használatának két fő forgatókönyve a következő:

* [felhőprotokoll-átjárók](iot-hub-devguide-endpoints.md),
* az egyéni hitelesítési sémák megvalósításához használt [tokenszolgáltatások.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

Mivel a megosztott hozzáférési szabályzat potenciálisan hozzáférést biztosít a csatlakozáshoz, mint bármely eszköz, fontos, hogy a megfelelő erőforrás URI biztonsági jogkivonatok létrehozásakor. Ez a beállítás különösen fontos a jogkivonat-szolgáltatások, amelyek hatóköre a jogkivonatot egy adott eszközaz erőforrás URI használatával. Ez a pont kevésbé releváns a protokollátjárók számára, mivel már közvetítik az összes eszköz forgalmát.

Például egy előre létrehozott megosztott hozzáférési szabályzatot használó jogkivonat-szolgáltatás, **amelyet eszköznek** hívnak, a következő paraméterekkel hoz létre jogkivonatot:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* aláíró kulcs: a `device` házirend egyik kulcsa,
* házirend neve: `device`,
* lejárati idő.

Az előző Node.js függvényt használó példa a következő lehet:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eszköz1 összes funkciójához hozzáférést biztosít az eredmény:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

A protokollátjáró ugyanazt a jogkivonatot használhatja `myhub.azure-devices.net/devices`az összes eszközhöz, amely egyszerűen az erőforrás URI-ját állítja a számára.

### <a name="use-security-tokens-from-service-components"></a>Szolgáltatás-összetevők biztonsági jogkivonatainak használata

A szolgáltatás-összetevők csak olyan megosztott hozzáférési házirendek használatával hozhatnak létre biztonsági jogkivonatokat, amelyek a korábban ismertetett megfelelő engedélyeket biztosítják.

A végpontokon elérhető szolgáltatásfunkciók a következők:

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices` |Eszközidentitások létrehozása, frissítése, beolvasása és törlése. |
| `{iot hub host name}/messages/events` |Eszközről felhőbe irányuló üzenetek fogadása. |
| `{iot hub host name}/servicebound/feedback` |Visszajelzés küldése a felhőből az eszközre irányuló üzenetekhez. |
| `{iot hub host name}/devicebound` |Felhőből eszközre irányuló üzenetek küldése. |

Például egy, az előre létrehozott megosztott hozzáférési házirendet **használó, registryRead** nevű szolgáltatás a következő paraméterekkel hoz létre jogkivonatot:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices`,
* aláíró kulcs: a `registryRead` házirend egyik kulcsa,
* házirend neve: `registryRead`,
* lejárati idő.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, amely hozzáférést biztosít az összes eszközidentitás olvasásához, a következő lenne:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Támogatott X.509 tanúsítványok

Bármely X.509-es tanúsítvány használatával hitelesítheti az eszközt az IoT Hubbal egy tanúsítvány ujjlenyomatának vagy egy hitelesítésszolgáltatónak az Azure IoT Hubra való feltöltésével. A tanúsítvány ujjlenyomatai segítségével történő hitelesítés ellenőrzi, hogy a bemutatott ujjlenyomat megegyezik-e a konfigurált ujjlenyomattal. A hitelesítésszolgáltató használatával történő hitelesítés ellenőrzi a tanúsítványláncot. Akárhogy is, a TLS-kézfogás megköveteli, hogy az eszköz érvényes tanúsítvánnyal és személyes kulccsal rendelkezik. A részleteket lásd a TLS specifikációban, például: [RFC 5246 - The Transport Layer Security (TLS) Protocol Version 1.2](https://tools.ietf.org/html/rfc5246/).

A támogatott tanúsítványok a következők:

* **Egy meglévő X.509 tanúsítvány**. Előfordulhat, hogy egy eszközhöz már van X.509-es tanúsítvány társítva. Az eszköz ezzel a tanúsítvánnyal hitelesítheti magát az IoT Hubbal. Ujjlenyomat- vagy hitelesítésszolgáltató-hitelesítéssel is működik. 

* **CA által aláírt X.509 tanúsítvány**. Az eszközök azonosításához és az IoT Hubon való hitelesítéséhez használhatja a hitelesítésszolgáltató által létrehozott és aláírt X.509-es tanúsítványt. Ujjlenyomat- vagy hitelesítésszolgáltató-hitelesítéssel is működik.

* **Saját készítésű és saját maga által aláírt X-509 tanúsítvány.** Az eszköz gyártója vagy a házon belüli üzembe helyező létrehozhatja ezeket a tanúsítványokat, és tárolhatja a megfelelő személyes kulcsot (és tanúsítványt) az eszközön. Erre a célra olyan eszközöket használhat, mint az [OpenSSL](https://www.openssl.org/) és a [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) segédprogram. Csak ujjlenyomat-hitelesítéssel működik. 

Az eszköz használhat X.509-es tanúsítványt vagy biztonsági jogkivonatot a hitelesítéshez, de mindkettőt nem.

A hitelesítéshitelesítésről a hitelesítésszolgáltató használatával kapcsolatos további információt az [X.509 hitelesítésszolgáltatói tanúsítványokkal történő eszközhitelesítés című témakörben talál.](iot-hub-x509ca-overview.md)

### <a name="register-an-x509-certificate-for-a-device"></a>X.509-es tanúsítvány regisztrálása eszközhöz

Az [Azure IoT Service SDK C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (1.0.8+-os verzió) támogatja az X.509-es tanúsítványt használó eszközök regisztrálását a hitelesítéshez. Más API-k, például az eszközök importálása/exportálása szintén támogatják az X.509 tanúsítványokat.

A CLI extension command [az iot hub eszközidentitás](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) használatával x.509-es tanúsítványokat is konfigurálhat az eszközökhöz.

### <a name="c-support"></a>C\# támogatás

A **RegistryManager** osztály programozott módot biztosít az eszköz regisztrálására. Különösen az **AddDeviceAsync** és **UpdateDeviceAsync** metódusok lehetővé teszik, hogy regisztráljon és frissítsen egy eszközt az IoT Hub identitásbeállítási nyilvántartásában. Ez a két módszer egy **eszközpéldányt** vesz igénybe bemenetként. Az **Eszközosztály** tartalmaz egy **hitelesítési** tulajdonságot, amely lehetővé teszi az elsődleges és másodlagos X.509 tanúsítvány ujjlenyomatok megadását. Az ujjlenyomat az X.509 tanúsítvány SHA256 kivonatát jelöli (bináris DER kódolással tárolva). Lehetősége van elsődleges ujjlenyomat, másodlagos ujjlenyomat vagy mindkettő megadására. Elsődleges és másodlagos ujjlenyomatok támogatottak a tanúsítványok váltási forgatókönyvek kezeléséhez.

Itt van egy\# C-kódrészlet az X.509 tanúsítvány ujjlenyomatával történő regisztrálásához:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>X.509-es tanúsítvány használata futásidejű műveletek során

Az [Azure IoT-eszköz SDK.NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (1.0.11+-os verzió) támogatja az X.509 tanúsítványok használatát.

### <a name="c-support"></a>C\# támogatás

A **DeviceAuthenticationWithX509Certificate** osztály támogatja a **DeviceClient-példányok** létrehozását X.509-es tanúsítvánnyal. Az X.509 tanúsítványnak PFX (más néven PKCS #12) formátumúnak kell lennie, amely tartalmazza a személyes kulcsot.

Itt van egy minta kód részlet:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Egyéni eszköz- és modulhitelesítés

Az IoT Hub [identitásjegyzéke](iot-hub-devguide-identity-registry.md) segítségével konfigurálhatja az eszközönkénti/modulonkénti biztonsági hitelesítő adatokat és a [hozzáférés-vezérlést a jogkivonatokkal.](iot-hub-devguide-security.md#security-tokens) Ha egy IoT-megoldás már rendelkezik egy egyéni identitás-beállításjegyzékés/vagy hitelesítési séma, fontolja meg egy *jogkivonat-szolgáltatás* integrálásához az infrastruktúra Az IoT Hub. Ily módon más IoT-funkciókat is használhat a megoldásban.

A jogkivonat-szolgáltatás egy egyéni felhőszolgáltatás. Az IoT Hub *megosztott hozzáférési szabályzatot* használ **a DeviceConnect** vagy **a ModuleConnect** engedélyekkel *eszközhatókörrel* vagy *modulhatókörrel rendelkező* jogkivonatok létrehozásához. Ezek a jogkivonatok lehetővé teszik, hogy egy eszköz és egy modul csatlakozzon az IoT hubhoz.

![A jogkivonat-szolgáltatás mintájának lépései](./media/iot-hub-devguide-security/tokenservice.png)

A tokenszolgáltatás-minta fő lépései:

1. Hozzon létre egy IoT Hub megosztott hozzáférési szabályzatot **a DeviceConnect** vagy **a ModuleConnect** engedélyekkel az IoT hubhoz. Ezt a szabályzatot az [Azure Portalon](https://portal.azure.com) vagy programozott módon hozhatja létre. A jogkivonat-szolgáltatás ezt a szabályzatot használja az általa létrehozott jogkivonatok aláírásához.

2. Ha egy eszköz/modul kell az IoT hub eléréséhez, egy aláírt jogkivonatot kér a jogkivonat-szolgáltatás. Az eszköz hitelesítheti magát az egyéni identitás-beállításjegyzék/hitelesítési sémával a jogkivonat létrehozásához használt eszköz/modul identitás meghatározásához.

3. A jogkivonat-szolgáltatás egy jogkivonatot ad vissza. A jogkivonat ot `/devices/{deviceId}` `/devices/{deviceId}/module/{moduleId}` a `resourceURI`hitelesítés `deviceId` alatt álló eszköz `moduleId` vagy a modul használatával vagy használatával hozják létre. A jogkivonat-szolgáltatás a megosztott hozzáférési szabályzatot használja a jogkivonat létrehozásához.

4. Az eszköz/modul a jogkivonatot közvetlenül az IoT hub.

> [!NOTE]
> A .NET class [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) vagy a Java class [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) segítségével jogkivonatot hozhat létre a tokenszolgáltatásban.

A jogkivonat-szolgáltatás igény szerint beállíthatja a jogkivonat lejáratát. Amikor a jogkivonat lejár, az IoT hub megszakítja az eszköz/modul kapcsolatot. Ezután az eszköz/modul kell kérnie egy új jogkivonatot a jogkivonat-szolgáltatás. Egy rövid lejárati idő növeli a terhelést mind az eszköz/modul, mind a jogkivonat-szolgáltatás.

Ahhoz, hogy egy eszköz/modul csatlakozzon a hubhoz, továbbra is hozzá kell adnia az IoT Hub identitásjegyzékhez – még akkor is, ha egy jogkivonatot használ, és nem egy kulcsot a csatlakozáshoz. Ezért továbbra is használhatja az eszközönkénti/modulonkénti hozzáférés-vezérlést az eszköz-/modulidentitások engedélyezésével vagy letiltásával az [identitásjegyzékben.](iot-hub-devguide-identity-registry.md) Ez a megközelítés csökkenti a hosszú lejárati idővel rendelkező jogkivonatok használatának kockázatát.

### <a name="comparison-with-a-custom-gateway"></a>Összehasonlítás egyéni átjáróval

A jogkivonat-szolgáltatás minta az ajánlott módja egy egyéni identitás-beállításjegyzék/hitelesítési séma az IoT Hub megvalósításához. Ez a minta ajánlott, mert az IoT Hub továbbra is kezeli a megoldásforgalmának nagy részét. Ha azonban az egyéni hitelesítési séma annyira összefonódik a protokollal, szükség lehet egy *egyéni átjáróra* az összes forgalom feldolgozásához. Ilyen eset például a [Transport Layer Security (TLS) és az előre megosztott kulcsok (PsK)](https://tools.ietf.org/html/rfc4279)használata. További információt a [protokollátjáróról](iot-hub-protocol-gateway.md) szóló cikkben talál.

## <a name="reference-topics"></a>Referenciatémakörök:

Az alábbi referenciatémakörök további információt nyújtanak az IoT hubhoz való hozzáférés szabályozásáról.

## <a name="iot-hub-permissions"></a>Az IoT Hub engedélyei

Az alábbi táblázat felsorolja az IoT hubhoz való hozzáférés szabályozásához használható engedélyeket.

| Engedély | Megjegyzések |
| --- | --- |
| **Rendszerleíró adatbázisOlvasás** |Olvasási hozzáférést biztosít az identitás-beállításjegyzékhez. További információ: [Identity registry](iot-hub-devguide-identity-registry.md). <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |
| **Rendszerleíró adatbázisírás** |Olvasási és írási hozzáférést biztosít az identitásjegyzékhez. További információ: [Identity registry](iot-hub-devguide-identity-registry.md). <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |
| **SzolgáltatásCsatlakozás** |Hozzáférést biztosít a felhőszolgáltatás felé irányuló kommunikációhoz és a végpontok figyeléséhez. <br/>Engedélyt ad az eszközről a felhőbe irányuló üzenetek fogadására, a felhőből az eszközre irányuló üzenetek küldésére és a megfelelő kézbesítési nyugták lekérésére. <br/>Engedélyt ad a fájlfeltöltések kézbesítési nyugtázásának lekérésére. <br/>Engedélyt ad az ikerpárok elérésére a címkék és a kívánt tulajdonságok frissítéséhez, a jelentett tulajdonságok lekéréséhez és lekérdezések futtatásához. <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |
| **DeviceConnect** |Hozzáférést biztosít az eszközfelé néző végpontokhoz. <br/>Engedélyt ad az eszközről a felhőbe irányuló üzenetek küldésére és a felhőből az eszközre irányuló üzenetek fogadására. <br/>Engedélyt ad az eszközről történő fájlfeltöltésre. <br/>Engedélyt ad az ikereszköz kívánt tulajdonságértesítéseinek fogadására és az ikereszköz jelentett tulajdonságainak frissítésére. <br/>Engedélyt ad a fájlfeltöltések végrehajtására. <br/>Ezt az engedélyt az eszközök használják. |

## <a name="additional-reference-material"></a>További referenciaanyagok

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [Az IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különböző végpontok, amelyek az egyes IoT-központok elérhetővé teszi a futásidejű és felügyeleti műveletek.

* [A sávszélesség-szabályozás és](iot-hub-devguide-quotas-throttling.md) a kvóták az IoT Hub-szolgáltatásra vonatkozó kvótákat és szabályozási viselkedéseket ismertetik.

* [Az Azure IoT-eszközök és szolgáltatások SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvi SDK-k segítségével, ha az IoT Hub-szolgáltatást használó eszköz- és szolgáltatásalkalmazásokat is fejleszt.

* [Az IoT Hub lekérdezési nyelve](iot-hub-devguide-query-language.md) leírja a lekérdezési nyelvet, amelysegítségével információkat kérhet le az IoT Hubról az eszköztwins és a feladatok használatával.

* [Az IoT Hub MQTT-támogatása](iot-hub-mqtt-support.md) további információt nyújt az MQTT protokoll IoT Hub-támogatásáról.

* [RFC 5246 – A Transport Layer Security (TLS) Protocol Version 1.2](https://tools.ietf.org/html/rfc5246/) további információt nyújt a TLS-hitelesítésről.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az IoT Hub elérésének szabályozását, az alábbi IoT Hub-fejlesztői útmutatók érdekelhetik:

* [Az állapot és a konfigurációk szinkronizálása eszköztwins használatával](iot-hub-devguide-device-twins.md)
* [Közvetlen metódus meghívása eszközön](iot-hub-devguide-direct-methods.md)
* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

Ha ki szeretné próbálni a cikkben ismertetett fogalmak némelyikét, tekintse meg az Alábbi IoT Hub-oktatóanyagokat:

* [Ismerkedés az Azure IoT Hub szolgáltatással](quickstart-send-telemetry-node.md)
* [Felhőből az eszközre irányuló üzenetek küldése az IoT Hubbal](iot-hub-csharp-csharp-c2d.md)
* [Az IoT Hub eszközről felhőbe irányuló üzeneteinek feldolgozása](tutorial-routing.md)
