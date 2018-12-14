---
title: Azure IoT Hub biztonsági eljárásainak megértését |} A Microsoft Docs
description: Fejlesztői útmutató – eszköz alkalmazások és a háttér-alkalmazások az IoT Hub elérésének szabályozása. Biztonsági jogkivonatok és támogatás az X.509-tanúsítványokat kapcsolatos információkat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 87df2731d45ffa51bc2fd298aa1b678b10e38515
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344329"
---
# <a name="control-access-to-iot-hub"></a>IoT Hub-hozzáférés szabályozása

Ez a cikk ismerteti az IoT hub biztonsági beállításaival. Használja az IoT Hub *engedélyek* minden IoT hub-végponton való hozzáférést. Engedélyek férjenek hozzá az IoT hub funkciók alapján.

Ez a cikk mutatja be:

* A különböző engedélyekkel, hogy egy eszköz vagy a háttér-alkalmazás elérésére az IoT hub biztosíthat.
* A hitelesítési folyamat és a jogkivonatok segítségével ellenőrizze az engedélyeket.
* Hogyan hatókör hitelesítő adatokat adott erőforrásokhoz való hozzáférés korlátozásához.
* Az IoT Hub-támogatás az X.509-tanúsítványokat.
* Egyéni hitelesítési mechanizmusok meglévő identitás eszköznyilvántartások vagy hitelesítési sémákat.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub-végpontok bármelyikét eléréséhez szükséges engedélyekkel kell rendelkeznie. Például egy eszköz tartalmaznia kell egy jogkivonatot, és minden üzenetet küld az IoT Hub biztonsági hitelesítő adatokat tartalmazó.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

Biztosítani [engedélyek](#iot-hub-permissions) a következő módon:

* **IoT hub-szintű megosztott hozzáférési házirendek**. Megosztott elérési házirendeket is biztosítson tetszőleges kombinációját [engedélyek](#iot-hub-permissions). Szabályzatokat definiálhat a [az Azure portal](https://portal.azure.com), programozott módon használatával a [IoT Hub erőforrás REST API-k](/rest/api/iothub/iothubresource), vagy a [az iot hub házirend](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI. Egy újonnan létrehozott IoT hub a következő alapértelmezett szabályzatokkal rendelkezik:
  
  | Megosztott elérési házirend | Engedélyek |
  | -------------------- | ----------- |
  | iothubowner | Minden engedély |
  | szolgáltatás | **ServiceConnect** engedélyek |
  | eszköz | **DeviceConnect** engedélyek |
  | registryRead | **RegistryRead** engedélyek |
  | registryReadWrite | **RegistryRead** és **RegistryWrite** engedélyek |

* **Az eszközönkénti biztonsági hitelesítő adatok**. Minden IoT Hub tartalmaz egy [eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md) az eszközidentitás-jegyzékben lévő minden egyes eszközhöz, konfigurálhatja a biztonsági hitelesítő adatokat, amelyek biztosítanak **DeviceConnect** engedélyek hatókörét arra a megfelelő eszköz végpontok.

Ha például a tipikus IoT-megoldások:

* Az eszköz felügyeleti összetevőt használja a *registryReadWrite* házirend.
* Az event processor összetevő használja a *szolgáltatás* házirend.
* A futásidejű eszköz üzleti logika összetevőt használja a *szolgáltatás* házirend.
* Egyes eszközök csatlakoztatása az IoT hub eszközidentitás-jegyzékben lévő tárolt hitelesítő adatok használatával.

> [!NOTE]
> Lásd: [engedélyek](#iot-hub-permissions) részletes információkat.

## <a name="authentication"></a>Hitelesítés

Az Azure IoT Hub végpontok hozzáférést biztosít egy jogkivonatot a megosztott elérési házirendeket és az identitás beállításjegyzék biztonsági hitelesítő adatok szemben ellenőrzésével.

Biztonsági hitelesítő adatok, például a szimmetrikus kulcsokat, a rendszer soha nem elküldve a hálózaton keresztül.

> [!NOTE]
> Az Azure IoT Hub erőforrás-szolgáltató az Azure-előfizetéssel, biztonságos, mivel az összes szolgáltatót a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

Hozza létre, és a biztonsági jogkivonatokkal kapcsolatos további információkért lásd: [IoT Hub biztonsági tokenek](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Protokoll tulajdonságairól

Minden támogatott protokollt, például MQTT, AMQP és a HTTPS, szállítja jogkivonatok különböző módon.

MQTT használatakor a CONNECT csomag rendelkezik-e az eszköz azonosítójával, mint a ClientId, `{iothubhostname}/{deviceId}` a felhasználónév mezőbe, és a egy SAS-token, a jelszó mezőbe. `{iothubhostname}` a teljes CName az IoT hub (például contoso.azure-devices.net) kell lennie.

Használata esetén [AMQP](https://www.amqp.org/), IoT Hub által támogatott [SASL egyszerű](http://tools.ietf.org/html/rfc4616) és [AMQP jogcím-alapú-biztonsági](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

AMQP jogcím-alapú-biztonság használatakor a normál ezek a jogkivonatok továbbítására módját adja meg.

A SASL egyszerű a **felhasználónév** lehet:

* `{policyName}@sas.root.{iothubName}` Ha az IoT hub-szintű jogkivonatok használatával.
* `{deviceId}@sas.{iothubname}` Ha az eszköz hatókörébe tartozó jogkivonatok használatával.

Mindkét esetben a jelszó mező tartalmaz a jogkivonat leírtak szerint [IoT Hub biztonsági tokenek](iot-hub-devguide-security.md#security-tokens).

HTTPS érvényes token felvételével hitelesítést valósít meg a **engedélyezési** kérés fejlécéhez.

#### <a name="example"></a>Példa

Felhasználónév (az Eszközanosító megkülönbözteti a kis-és nagybetűket): `iothubname.azure-devices.net/DeviceId`

Jelszó (is létrehozhat egy SAS-token az a [device explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) eszközzel, a CLI-bővítmény parancs [az iot hub létrehozása sas-jogkivonat](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), vagy a [Visual Studióhoz készült Azure IoT Hub-eszközkészlet bővítmény Kód](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (korábbi nevén Azure IoT-eszközkészlet bővítmény)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> A [Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan a szolgáltatáshoz való csatlakozáskor jogkivonatokat hoz létre. Bizonyos esetekben az Azure IoT SDK-k nem támogatják, minden protokoll, vagy a hitelesítési módszereket.

### <a name="special-considerations-for-sasl-plain"></a>SASL egyszerű vonatkozó különleges szempontok

Az AMQP használatával SASL egyszerű használatakor egy ügyfél csatlakoztatása az IoT hub egyes TCP-kapcsolatok az egyes használhatja. Amikor a jogkivonat lejár, a TCP-kapcsolat bontja a kapcsolatot a szolgáltatás, és elindít egy újracsatlakozás. Ezt a viselkedést, amíg nem problémás háttér-alkalmazások esetén van káros eszköz alkalmazás a következő okok miatt:

* Átjárók általában connect számos eszközök nevében. SASL egyszerű használatakor az egyedi TCP-kapcsolatot minden egyes eszközhöz csatlakozik az IoT hub létrehozása rendelkeznek. Ez a forgatókönyv jelentősen növeli az energiagazdálkodással és hálózati erőforrások felhasználását, és növeli a késést, minden egyes eszköz kapcsolat.

* Erőforrás-korlátozott eszközök megnövekedett használt erőforrások után minden jogkivonat lejárati hátrányosan érintik.

## <a name="scope-iot-hub-level-credentials"></a>Hatókör IoT hub-szintű hitelesítő adatokkal

IoT hub-szintű biztonsági házirendek egy korlátozott erőforrás URI-jogkivonatok létrehozásával korlátozhatja körét. Ha például a végpontot, hogy az eszköz – felhő üzeneteket küldhet egy eszközről van-e **/devices/ {deviceId} / üzenetek/események**. Használhatja az IoT hub-szintű megosztott elérési házirendet a **DeviceConnect** engedélyek egy jogkivonatot, amelynek a resourceURI van a bejelentkezni **/devices/ {deviceId}**. Ezzel a módszerrel hoz létre, amely csak eszköz nevében üzenetek küldéséhez használható jogkivonatot **deviceId**.

Ez a mechanizmus hasonlít a [az Event Hubs közzétevői házirend](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab), és egyéni hitelesítési módszerek megvalósítását teszi.

## <a name="security-tokens"></a>Biztonsági jogkivonatok

Az IoT Hub biztonsági jogkivonatokat használ eszközökön és szolgáltatásokon elkerülése érdekében a kulcsok küld hálózati hitelesítéséhez. Emellett biztonsági jogkivonatok érvényesség, és a hatókör korlátozott. [Az Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan jogkivonatokat hoz létre, anélkül, hogy semmiféle speciális beállítást. Bizonyos forgatókönyvek megkövetelik hozhat létre és használhat közvetlenül a biztonsági jogkivonatokat. Ilyen forgatókönyvek például a következők:

* Az MQTT, AMQP vagy HTTPS Surface-eszközök közvetlen használatát.

* A jogkivonat-szolgáltatás minta, amint azt megvalósítását [egyéni eszközhitelesítés](iot-hub-devguide-security.md#custom-device-and-module-authentication).

Az IoT Hub is lehetővé teszi, hogy hitelesíteni az IoT Hub-eszközök használatával [X.509 tanúsítványok](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Biztonsági jogkivonat szerkezete

Időhöz kötött access-eszközökre, és bizonyos funkciók az IoT Hub-szolgáltatások biztosítása a biztonsági jogkivonatok használatával. Get-jához való csatlakoztatása az IoT hubhoz, eszközöket és szolgáltatásokat el kell küldenie egy közös hozzáférésű vagy egy szimmetrikus kulcs aláírt biztonsági jogkivonatokat. Ezek a kulcsok és a egy eszközidentitást az eszközidentitás-jegyzékben lévő tárolódnak.

A jogkivonatot egy közös hozzáférési kulcs engedélyezi a hozzáférést a megosztott elérési szabályzatok jogosultságai társított összes funkciót a aláírva. Egy új eszközidentitást szimmetrikus kulcs csak biztosít aláírt jogkivonat a **DeviceConnect** engedély a társított eszközidentitás.

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Az alábbiakban a várt értékek:

| Érték | Leírás |
| --- | --- |
| {aláírás} |Egy HMAC-SHA256 aláírás karakterlánccá: `{URL-encoded-resourceURI} + "\n" + expiry`. **Fontos**: A kulcs a Base64 kódolású anyag dekódolni, és a HMAC-SHA256 végzéséhez kulcsaként használt. |
| {resourceURI} |URI-előtag (szegmens) szerint a végpontot, amely az ezzel a tokennel, az IoT hub (nincs protocol) állomásneve kezdve érhető el. Például: `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8 karakterláncok az alapidőpont 00:00:00 (UTC), a 1970. január 1. óta eltelt másodpercek száma. |
| {URL-encoded-resourceURI} |Lower használatieset-URL-Címének kódolása a kisbetűs erőforrás URI azonosítója |
| {policyName} |Ez a token által hivatkozott megosztott elérési házirend neve. Hiányzik, ha a jogkivonat-eszközt a tárolójegyzék hitelesítő adatainak hivatkozik. |

**Megjegyzés: az előtag**: Az URI-előtag karakteres szegmens és nem számítja ki. Például `/a/b` az előtagja `/a/b/c` esetében nem `/a/bc`.

Az alábbi Node.js látható a hívott függvény **generateSasToken** , amely kiszámítja a jogkivonatot a bemeneti adatok `resourceUri, signingKey, policyName, expiresInMins`. A következő szakaszok bemutatják, hogyan lehet inicializálni a különböző adatbevitelek esetében különböző token használati eseteit.

```nodejs
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct autorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Összehasonlítása, mint a megfelelő Python-kód biztonsági jogkivonat létrehozásához a következő:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

A C# használatával hozzon létre egy biztonsági tokent funkciója:

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
> Mivel a token érvényességének idő van érvényesíti az IoT Hub-gépeken, az eltéréseket az óra, a gép, amely létrehozza a jogkivonat a minimális kell lennie.

### <a name="use-sas-tokens-in-a-device-app"></a>Egy eszköz alkalmazásban használja a SAS-tokenek

Kétféleképpen juthat **DeviceConnect** biztonsági jogkivonatokat az IoT Hub engedélyek: használja egy [szimmetrikus eszköz kulcsát az identitásjegyzékben lévő](#use-a-symmetric-key-in-the-identity-registry), vagy használjon egy [megosztott elérési kulcsával](#use-a-shared-access-policy).

Ne feledje, hogy minden funkciója elérhető eszközöket tesz elérhetővé Tervező előtag rendelkező végpontokon `/devices/{deviceId}`.

> [!IMPORTANT]
> Az egyetlen módszer, hogy az IoT Hub végzi a hitelesítést egy adott eszköz az eszköz identitása szimmetrikus kulcsot használ. Azokban az esetekben, amikor egy megosztott elérési házirendet használja el az eszköz funkcióit, a megoldás figyelembe kell venni a megbízható részletes a biztonsági jogkivonat kiállítása összetevő.

Az eszköz felé néző végpontok a következők (függetlenül a protokoll):

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Eszköz – felhő üzeneteket küldhet. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Felhőből az eszközre irányuló üzenetek fogadása. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Az eszközidentitás-jegyzékben lévő szimmetrikus kulcs használata

Ha egy jogkivonatot a policyname paraméter a szimmetrikus kulcs egy device identity használva (`skn`) elem a jogkivonat hiányzik.

Például egy jogkivonatot létrehozott összes eszköz funkcióinak eléréséhez az alábbi paramétereket kell rendelkeznie:

* erőforrás-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* aláíró kulcs: minden olyan szimmetrikus kulcsot a `{device id}` identitás
* Nincs házirendnév
* minden olyan lejárati időt.

Egy példa az előző Node.js-függvény használatával a következő lesz:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Az eredmény, amely hozzáférést biztosít minden funkció esetében device1, a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Lehetséges, az SAS-token létrehozásához a [device explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) eszközzel, a CLI-bővítmény parancs [az iot hub létrehozása sas-jogkivonat](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), vagy a [Visual Studióhoz készült Azure IoT Hub-eszközkészlet bővítmény Kód](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

### <a name="use-a-shared-access-policy"></a>Megosztott elérési házirendet használja

Amikor egy megosztott elérési házirendet hoz létre egy jogkivonatot, állítsa be a `skn` mezőt a házirend nevét. Ez a szabályzat kell biztosítania a **DeviceConnect** engedéllyel.

A megosztott elérési házirendeket eszköz funkcióinak eléréséhez használt két fő forgatókönyvek a következők:

* [a felhő protokollátjárók](iot-hub-devguide-endpoints.md),
* [szolgáltatásokat jogkivonat](iot-hub-devguide-security.md#custom-device-and-module-authentication) egyéni hitelesítési sémákat megvalósításához használt.

Mivel a megosztott elérési házirend potenciálisan adhat hozzáférést, bármilyen eszközről csatlakozni, fontos biztonsági jogkivonatokat létrehozásakor használja a megfelelő erőforrás-URI. Ez a beállítás különösen fontos a jegykiadó szolgáltatások, amelynek a hatóköre a jogkivonatot egy adott eszközhöz, az erőforrás-URI használatával. Ez a pont fontos kevesebb protokollátjárók, azok már vannak közvetítő forgalmat az összes eszközt.

Például, egy jogkivonat-szolgáltatás használatával az előre létrehozott megosztott hozzáférési házirend nevű **eszköz** hozna létre egy tokent az alábbi paraméterekkel:

* erőforrás-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* aláíró kulcs: kulcsainak egyikét a `device` házirend
* házirend neve: `device`,
* minden olyan lejárati időt.

Egy példa az előző Node.js-függvény használatával a következő lesz:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, amely hozzáférést biztosít minden funkció esetében device1, a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

A protokoll-átjáró használhatja ugyanezt a tokent az összes olyan eszközt, az erőforrás URI azonosítója csak állítsa a `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Használja a biztonsági jogkivonatokat a szolgáltatás-összetevők

Szolgáltatás-összetevők csak hozhatja létre a megfelelő engedélyek megadása a korábbiakban leírtak megosztott elérési házirendeket használó biztonsági jogkivonatokat.

Az elérhetővé tett végpontokon funkciói a következők:

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices` |Létrehozása, frissítése, lekérése és törlése az eszközidentitások. |
| `{iot hub host name}/messages/events` |Eszköz – felhő üzeneteket fogadni. |
| `{iot hub host name}/servicebound/feedback` |Visszajelzés a felhőből az eszközre irányuló üzenetek fogadására. |
| `{iot hub host name}/devicebound` |Üzenetküldés a felhőből az eszközre. |

Tegyük fel, a megosztott hozzáférési házirend nevű szolgáltatás létrehozásához használja az előre létrehozott **registryRead** hozna létre egy tokent az alábbi paraméterekkel:

* erőforrás-URI: `{IoT hub name}.azure-devices.net/devices`,
* aláíró kulcs: kulcsainak egyikét a `registryRead` házirend
* házirend neve: `registryRead`,
* minden olyan lejárati időt.

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, az összes eszközidentitások olvasási hozzáférést biztosítanak, amely a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Támogatott X.509-tanúsítványok

Bármely X.509-tanúsítvány használatával hitelesíteni a egy eszközt az IoT Hub fel kell töltenie a tanúsítvány-ujjlenyomat vagy a hitelesítésszolgáltató (CA) az Azure IoT hubra. Hitelesítési tanúsítvány-ujjlenyomatok csak használatával ellenőrzi, hogy az itt bemutatott ujjlenyomata megegyezik-e a konfigurált ujjlenyomattal. Hitelesítésszolgáltató használata hitelesítési a tanúsítványlánc ellenőrzése. 

Támogatott a tanúsítványok tartalmaznak:

* **Egy meglévő X.509-tanúsítvány**. Előfordulhat, hogy egy eszköz már egy X.509 tanúsítvány társítva. Az eszköz használhatja ezt a tanúsítványt az IoT Hub-hitelesítést. Ujjlenyomattal vagy a CA hitelesítési együttműködik. 

* **Hitelesítésszolgáltató által aláírt X.509-tanúsítvány**. Egy eszköz azonosítására és hitelesítéséhez az IoT hubbal, egy X.509 tanúsítvány jön létre, és a hitelesítésszolgáltató (CA) aláírt használhatja. Ujjlenyomattal vagy a CA hitelesítési együttműködik.

* **Önállóan létrehozott és önaláírt tanúsítvány X-509**. Egy eszköz gyártója, vagy a belső fejlesztésű deployer ezeket a tanúsítványokat létrehozni és tárolására is használható a megfelelő titkos kulcs (és a tanúsítvány) az eszközön. Eszközöket használhatja például [OpenSSL](https://www.openssl.org/) és [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) segédprogram erre a célra. Csak akkor működik ujjlenyomat-hitelesítéssel. 

Egy eszköz előfordulhat, hogy használja egy X.509 tanúsítvány, vagy egy biztonsági jogkivonatot a hitelesítés, de nem mindkettőt.

Hitelesítés hitelesítésszolgáltató használatával kapcsolatos további információkért lásd: [X.509 Hitelesítésszolgáltatói tanúsítványok használatával Eszközhitelesítés](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Egy X.509 tanúsítvány, az eszköz regisztrálása

A [Azure IoT Service SDK használata a C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service) (verzió 1.0.8+) támogatja egy X.509-tanúsítványt használ a hitelesítéshez eszköz regisztrálása. Más API-k, például az eszközök importálása és exportálása az X.509-tanúsítványokat is támogatják.

A CLI-bővítmény parancs is használható [az iot hub-eszközidentitást](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) eszközök X.509-tanúsítványok konfigurálása.

### <a name="c-support"></a>C\# támogatása

A **RegistryManager** osztály lehetővé teszi a programozott eszköz regisztrálása. Különösen a **AddDeviceAsync** és **UpdateDeviceAsync** módszer lehetővé teszi regisztrálása és a egy eszközét az IoT Hub identitásjegyzékében. Két módszer közül igénybe vehet egy **eszköz** bevitel példányára. A **eszköz** osztály tartalmaz egy **hitelesítési** tulajdonságot, amely lehetővé teszi, hogy az elsődleges és másodlagos X.509-tanúsítvány ujjlenyomataival adja meg. Az ujjlenyomatot (a bináris kódolás DER használatával tárolt) X.509-tanúsítvány SHA256-kivonata egy jelöli. Lehetősége van egy elsődleges ujjlenyomatot, vagy egy másodlagos ujjlenyomatot vagy mindkettő megadására. Az elsődleges és másodlagos ujjlenyomat kezeléséhez tanúsítvány helyettesítő forgatókönyvek támogatottak.

Íme egy példa C\# kódrészlet használatával egy X.509 tanúsítvány ujjlenyomata eszköz regisztrálása:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Futásidejű műveletek során egy X.509 tanúsítvány használata

A [.NET-hez készült Azure IoT eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device) (verzió 1.0.11+) X.509-tanúsítványok használatát támogatja.

### <a name="c-support"></a>C\# támogatása

Az osztály **DeviceAuthenticationWithX509Certificate** támogatja a **DeviceClient** példányok, X.509 tanúsítvánnyal. X.509-tanúsítvány, amely tartalmazza a titkos kulcs (más néven a PKCS #12) PFX formátumban kell lennie.

Íme egy példa a kódrészlet:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Egyéni eszköz- és modul-hitelesítés

Használhatja az IoT Hub [eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md) kiszolgálónként – eszköz nebo modul biztonsági hitelesítő adatok konfigurálása és hozzáférés vezérlése segítségével [jogkivonatok](iot-hub-devguide-security.md#security-tokens). Ha egy IoT-megoldás már van egy egyéni identitás beállításjegyzék és/vagy a hitelesítési sémát, érdemes lehet létrehozni egy *jogkivonat-szolgáltatás* erre az infrastruktúrára integrálni az IoT hubbal. Ezzel a módszerrel a megoldás többi IoT funkciók is használhatók.

Egy jogkivonat-szolgáltatás egy olyan egyéni felhőalapú szolgáltatás. Használja az IoT Hub *megosztott hozzáférési szabályzat* a **DeviceConnect** vagy **ModuleConnect** létrehozásához szükséges engedélyek *eszköz hatókörű* vagy *modul hatókörű* jogkivonatokat. Ezek a jogkivonatok egy eszköz és a modul csatlakozni az IoT hub engedélyezése.

![A jogkivonat-szolgáltatás minta lépések](./media/iot-hub-devguide-security/tokenservice.png)

A jogkivonat-szolgáltatás minta fő lépései a következők:

1. Az IoT hubot, megosztott hozzáférési szabályzat létrehozása **DeviceConnect** vagy **ModuleConnect** az IoT hub engedélyekkel. Ezt a házirendet hozhat létre a [az Azure portal](https://portal.azure.com) vagy programozott módon. A jogkivonat-szolgáltatás ezt a házirendet hoz létre a jogkivonatok aláírásához használja.

2. Ha egy eszköz vagy a modul az IoT hub hozzáférésre van szüksége, egy aláírt jogkivonatot kér a jogkivonat-szolgáltatás. Az eszköz hitelesíteni tudja megállapítani az eszköz/modul identitás, amely a token létrehozásához használja a jogkivonat-szolgáltatás az egyéni identitás rendszerleíró adatbázis-hitelesítési sémával.

3. A jogkivonat-szolgáltatás egy tokent ad vissza. A jogkivonat használatával hozták létre `/devices/{deviceId}` vagy `/devices/{deviceId}/module/{moduleId}` , `resourceURI`, a `deviceId` legyen az eszköz a hitelesített vagy `moduleId` a hitelesített modulként. A jogkivonat-szolgáltatás a megosztott elérési házirendet használja a token létrehozásához.

4. Az eszköz/modul közvetlenül és az IoT hub használja a jogkivonatot.

> [!NOTE]
> Használhatja a .NET-osztály [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) vagy a Java-osztály [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token) egy jogkivonatot a jogkivonat-szolgáltatás létrehozásához.

A jogkivonat-szolgáltatás igény szerint állíthatja be a token elévülési idejénél. Ha a jogkivonat lejár, az IoT hub adatbázisai az eszköz/modul kapcsolat. Ezt követően az eszköz/modul egy új jogkivonatot kell kérhet a jogkivonat-szolgáltatás. Egy rövid lejárati idő az eszköz/modul és a jogkivonat-szolgáltatás terhelését növeli.

Egy eszköz/modul a hubhoz való csatlakozáshoz, hozzá kell adnia továbbra is azt az IoT Hub-identitásjegyzék – annak ellenére, hogy használ egy jogkivonatot, és nem kulcs való csatlakozáshoz. Így továbbra is engedélyezi vagy letiltja az eszköz/modul identitások /-eszköz / /-module hozzáférés-vezérlés használata a [eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md). Ez a megközelítés csökkenti a jogkivonatok használata hosszú lejárati idő kockázatát.

### <a name="comparison-with-a-custom-gateway"></a>Egyéni átjáróval összehasonlítása

A jogkivonat-szolgáltatás az ajánlott módszer a valósíthatók meg egy egyéni identitás rendszerleíró adatbázis-hitelesítési sémát az IoT Hub egyik. Ez a minta használata javasolt, mert az IoT Hub továbbra is a legtöbb megoldás forgalmat kezeli. Azonban ha az egyéni hitelesítési séma van így egymásba kapcsolódik a protokoll, szükség lehet egy *egyéni átjáró* összes forgalom feldolgozásához. Ilyen esetben egy példát használja [Transport Layer Security (TLS) és az előmegosztott kulcsok (PSKs)](https://tools.ietf.org/html/rfc4279). További információkért lásd: a [protokoll-átjáró](iot-hub-protocol-gateway.md) cikk.

## <a name="reference-topics"></a>Referencia-témaköreit:

A következő referencia-témakörök nyújtanak további információt az IoT hub-hozzáférés szabályozása.

## <a name="iot-hub-permissions"></a>Az IoT Hub engedélyekkel

Az alábbi táblázat felsorolja az engedélyeket, ki férhet hozzá az IoT hub segítségével.

| Engedély | Megjegyzések |
| --- | --- |
| **RegistryRead** |Olvasási hozzáférést biztosít az identitásjegyzékhez biztosít. További információkért lásd: [Eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md). <br/>Ezt az engedélyt a háttér felhőszolgáltatások használják. |
| **RegistryReadWrite** |Olvasási és írási hozzáférést biztosít az identitásjegyzékhez. További információkért lásd: [Eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md). <br/>Ezt az engedélyt a háttér felhőszolgáltatások használják. |
| **ServiceConnect** |Engedélyezi a hozzáférést a felhőalapú szolgáltatás által használt kommunikációs és végpontok monitorozása. <br/>Engedélyt ad a eszköz – felhő üzeneteket fogadni, üzenetküldés a felhőből az eszközre és a megfelelő kézbesítési visszaigazolások lekéréséhez. <br/>Engedélyt beolvasni a kézbesítési nyugtázás a fájl feltöltése. <br/>Engedélyt hozzáférés twins frissítése a címkék és kívánt tulajdonságok, jelentett tulajdonságok beolvasása és lekérdezéseket futtathat. <br/>Ezt az engedélyt a háttér felhőszolgáltatások használják. |
| **DeviceConnect** |Engedélyezi a hozzáférést a eszköz felé néző végpontok. <br/>Engedélyt ad a eszköz a felhőbe irányuló üzeneteket küldjön és fogadjon a felhőből az eszközre irányuló üzenetek. <br/>Engedélyt ad a fájlfeltöltés eszközről végrehajtásához. <br/>Engedélyt eszköz ikereszköz kívánt tulajdonság értesítéseket kapni, és frissítse az ikereszköz jelentett tulajdonságait. <br/>Engedélyt hajtsa végre a fájlt tölt fel. <br/>Ez az engedély eszközök használják. |

## <a name="additional-reference-material"></a>További – referenciaanyag

Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különféle végpontok, amely minden IoT-központ közzéteszi a futásidejű és felügyeleti műveletekhez.

* [Sávszélesség-szabályozási és kvóták](iot-hub-devguide-quotas-throttling.md) ismerteti a kvóták és szabályozás viselkedéseket, amelyek érvényesek az IoT Hub szolgáltatást.

* [Az Azure IoT eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.

* [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) ismerteti a lekérdezési nyelv az ikereszközökhöz és feladatokhoz kapcsolatos adatok lekérését az IoT Hub segítségével.

* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md) további információ az IoT Hub-támogatásról nyújt az MQTT protokoll.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az IoT Hub elérésének szabályozására, érdekelheti, az alábbi az IoT Hub fejlesztői útmutató témakörök:

* [Ikereszközök használata az állapot és a konfiguráció szinkronizálása](iot-hub-devguide-device-twins.md)
* [Az eszközön közvetlen metódus meghívása](iot-hub-devguide-direct-methods.md)
* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

Ha szeretné, és próbálja ki azokat a jelen cikkben ismertetett fogalmakat, a következő IoT Hub oktatóanyagokban talál:

* [Ismerkedés az Azure IoT Hub szolgáltatással](quickstart-send-telemetry-node.md)
* [Az IoT Hub felhőből az eszközre irányuló üzenetek küldésének módja](iot-hub-csharp-csharp-c2d.md)
* [Az IoT Hub eszköz a felhőbe irányuló üzenetek feldolgozása](tutorial-routing.md)
