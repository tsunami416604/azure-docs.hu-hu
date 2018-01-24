---
title: "Azure IoT Hub biztonsági eljárásainak megértését |} Microsoft Docs"
description: "Fejlesztői útmutató - eszközeinek alkalmazásait és háttér-alkalmazások az IoT-központ való hozzáférés vezérlése érdekében hogyan. Biztonsági jogkivonatok, és támogatja az x.509 szabványú tanúsítványokban kapcsolatos adatokat tartalmaz."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 45631e70-865b-4e06-bb1d-aae1175a52ba
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: 43917e4fe4ce7643ea034d128f303a5397dbcdc2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="control-access-to-iot-hub"></a>IoT Hub-hozzáférés szabályozása

Ez a cikk ismerteti az IoT hub biztonsági beállításaival. Az IoT-központ használ *engedélyek* hozzáférést biztosít minden egyes IoT-központ végpontjának. Az engedélyek korlátozhatják az IoT-központ funkciókon alapulnak.

Ez a cikk ismerteti:

* A különböző engedélyek, hogy egy eszköz vagy a háttér-alkalmazást, hogy az IoT hub hozzáférjen biztosíthat.
* A hitelesítési folyamat és a jogkivonatok segítségével ellenőrizze az engedélyeket.
* Hogyan hatókör hitelesítő adatokat egy adott erőforráshoz való hozzáférés korlátozására.
* IoT Hub-támogatás az x.509 szabványú tanúsítványokban.
* Egyéni hitelesítési mechanizmusok, amelyek használják a meglévő eszköz identitása nyilvántartó vagy hitelesítési sémák.

Az IoT-központok végpontjai bármelyikét eléréséhez szükséges engedélyekkel kell rendelkeznie. Például egy eszköz tartalmaznia kell egy jogkivonatot tartalmazó biztonsági hitelesítő adatokat, és minden üzenetet küld az IoT-központ.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

Meg lehet adni [engedélyek](#iot-hub-permissions) a következőképpen:

* **Az IoT hub-szintű megosztott elérési házirendeket**. Megosztott elérési házirendeket is biztosítani bármilyen kombinációját [engedélyek](#iot-hub-permissions). Megadhatja, hogy a házirendek a [Azure-portálon][lnk-management-portal], vagy programozott módon használatával a [IoT-központ erőforrás-szolgáltató REST API-k][lnk-resource-provider-apis]. Egy újonnan létrehozott IoT-központ a következő alapértelmezett házirend tartozik:

  * **iothubowner**: házirend az összes engedélyt.
  * **szolgáltatás**: a házirend **ServiceConnect** engedéllyel.
  * **eszköz**: a házirend **DeviceConnect** engedéllyel.
  * **registryRead**: a házirend **RegistryRead** engedéllyel.
  * **registryReadWrite**: a házirend **RegistryRead** és RegistryWrite engedélyeket.
  * **Eszköz hitelesítő adatokat**. Minden egyes IoT-központ tartalmaz egy [identitásjegyzékhez][lnk-identity-registry]. A identitásjegyzékhez az eszközökhöz, konfigurálhatja a hitelesítő adatokat, amelyek biztosítani **DeviceConnect** hatókörét a megfelelő eszköz végpontok engedélyeket.

Például a egy tipikus IoT-megoldás:

* Az eszköz felügyeleti összetevő használja a *registryReadWrite* házirend.
* Az esemény feldolgozó összetevő használja a *szolgáltatás* házirend.
* A futásidejű eszköz üzleti logika összetevő használja a *szolgáltatás* házirend.
* Egyes eszközök csatlakoznak az IoT-központ identitásjegyzékhez tárolt hitelesítő adatok használatával.

> [!NOTE]
> Lásd: [engedélyek](#iot-hub-permissions) részletes információkat.

## <a name="authentication"></a>Hitelesítés

Azure IoT Hub végpontok hozzáférést biztosít a megosztott elérési házirendeket és az identitás beállításjegyzék hitelesítő adatokat egy token ellenőrzésével.

Biztonsági hitelesítő adatok, például a szimmetrikus kulcsokat, a rendszer soha nem elküldve a hálózaton keresztül.

> [!NOTE]
> Az Azure IoT Hub erőforrás-szolgáltató védett keresztül az Azure-előfizetéshez, mert lévő összes szolgáltatónál a [Azure Resource Manager][lnk-azure-resource-manager].

Összeállítani, és biztonsági jogkivonatokat használatával kapcsolatos további információkért lásd: [IoT-központ biztonsági jogkivonatokat][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Protokoll rögzítésen

Minden támogatott protokollok, például MQTT AMQP vagy HTTPS-en jogkivonatok szállításokkal különböző módon.

MQTT használatakor a CONNECT csomag megegyezik a deviceId a ClientId, {iothubhostname} / {deviceId} a felhasználónév mezőben, és egy SAS-jogkivonat a jelszó mező. {iothubhostname} kell lennie az IoT hub (például contoso.azure-devices.net) teljes CNAME-adatait.

Használata esetén [AMQP][lnk-amqp], IoT-Központ támogatja [SASL egyszerű] [ lnk-sasl-plain] és [AMQP jogcím-alapú-biztonsági][lnk-cbs].

Ha AMQP jogcím-alapú-biztonsági használja, a normál továbbítja ezeket a jogkivonatokat módját adja meg.

A SASL egyszerű a **felhasználónév** lehet:

* `{policyName}@sas.root.{iothubName}`Ha az IoT hub-szintű jogkivonatok használatával.
* `{deviceId}@sas.{iothubname}`Ha az eszköz hatókörű jogkivonatok használatával.

Mindkét esetben a jelszó mező a jogkivonatot tartalmaz, a [IoT-központ biztonsági jogkivonatokat][lnk-sas-tokens].

HTTPS megvalósítja a hitelesítési érvényes token-ot a **engedélyezési** kérelemfejlécet.

#### <a name="example"></a>Példa

Felhasználónév (DeviceId a kis-és nagybetűket):`iothubname.azure-devices.net/DeviceId`

Jelszó (SAS létrehozása a lexikális elem a [eszköz explorer] [ lnk-device-explorer] eszköz):`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> A [Azure IoT SDK-k] [ lnk-sdks] automatikusan hoz létre a jogkivonatokat, a szolgáltatáshoz való kapcsolódáskor. Bizonyos esetekben az Azure IoT SDK-k nem támogatják a protokollok és a hitelesítési módszereket.

### <a name="special-considerations-for-sasl-plain"></a>Különleges szempontok SASL egyszerű

AMQP SASL egyszerű használ, amikor egy ügyfél csatlakozik az IoT-központ egyetlen jogkivonat egyes TCP-kapcsolatok használhatja. Amikor a jogkivonat lejár, az TCP-kapcsolatot bontja a kapcsolatot a service, és egy újracsatlakozás indítása. Ezt a viselkedést, amíg nem jelent problémát, egy háttér-alkalmazás van káros eszköz alkalmazások a következők lehetnek az okai:

* Átjárók általában sok eszközök nevében csatlakozni. SASL egyszerű használata esetén az egyes eszközök csatlakoztatása az IoT-központ különböző TCP-kapcsolat létrehozásához rendelkeznek. Ebben a forgatókönyvben jelentősen növeli a teljesítmény-és hálózati erőforrások, és minden eszköz kapcsolat a késési növeli.
* Erőforrás-korlátozott eszközök megnövekedett használt erőforrások után minden jogkivonat lejáratáról vannak hátrányosan.

## <a name="scope-iot-hub-level-credentials"></a>Hatókör IoT hub-szintű hitelesítő adatokat

Az IoT hub-szintű biztonsági házirendek hatókörét megadhatja a tokenek egy korlátozott erőforrás URI létrehozásával. A végpont eszköz a felhőbe küldött üzeneteket küldhet egy eszközről van például **/devices/ {deviceId} / üzenetek/események**. Használhatja az IoT hub-szintű megosztott elérési házirend **DeviceConnect** engedélyek egy jogkivonatot, amelynek resourceURI van bejelentkezni **/devices/ {deviceId}**. Ez a megközelítés létrehoz egy jogkivonatot, amely csak eszköz nevében küldéséhez használható **deviceId**.

A mechanizmus hasonlít a [Event Hubs közzétevői házirend][lnk-event-hubs-publisher-policy], és lehetővé teszi az egyéni hitelesítési módszerek végrehajtásához.

## <a name="security-tokens"></a>Biztonsági jogkivonatok kiosztva

Az IoT-központ biztonsági jogkivonatokat használ hitelesítéséhez, eszközöket és szolgáltatásokat lehet, ne küldjön kulcsok a keresztülhaladnak a hálózaton. Emellett biztonsági jogkivonatok érvényesség és hatókör korlátozott. [Azure IoT SDK-k] [ lnk-sdks] automatikusan hoz létre jogkivonatokat anélkül, hogy semmiféle speciális beállítást. Bizonyos esetekben kell létrehozni és biztonsági jogkivonatokat közvetlenül használható. Ilyen forgatókönyvek például a következők:

* A MQTT, AMQP vagy HTTPS felületek közvetlen használatát.
* A jogkivonat-szolgáltatás mintát, ahogy végrehajtásának [egyéni eszközhitelesítés][lnk-custom-auth].

Az IoT-központ is lehetővé teszi, hogy az IoT-központ való hitelesítéshez szükséges eszközök [X.509-tanúsítványokat][lnk-x509].

### <a name="security-token-structure"></a>Biztonsági jogkivonat szerkezete

Biztonsági jogkivonatok segítségével idő határolt eszközökhöz való hozzáférést és az adott funkciót az IoT-központ szolgáltatás. Ahhoz, hogy az IoT-központ csatlakozni engedélyezési, eszközöket és szolgáltatásokat kell küldenie egy közös hozzáférésű vagy a szimmetrikus kulcs aláírva biztonsági jogkivonatokat. Ezek a kulcsok eszköz megadásával az identitásjegyzékhez az tárolják.

A token egy megosztott elérési kulcs engedélyezi a hozzáférést a megosztott hozzáférési házirend engedélyeket társított összes funkcióját a aláírva. Egy eszközidentitás szimmetrikus kulcs csak biztosít aláírt jogkivonat a **DeviceConnect** engedély a társított eszközidentitás.

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Az alábbiakban a várt értékek:

| Érték | Leírás |
| --- | --- |
| {aláírás} |Egy HMAC-SHA256 aláírás karakterlánc a következő formátumban: `{URL-encoded-resourceURI} + "\n" + expiry`. **Fontos**: A kulcs a Base64 kódolású anyag dekódolni, és a HMAC-SHA256 számítási végrehajtásához kulcsként. |
| {resourceURI} |URI-előtag (által szegmens) a végpontok hozzáfér a tokenhez, kezdve IoT-központ (protokoll) állomásnevét. Például: `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8 karakterláncok esetében a epoch 00:00:00 UTC 1970. január 1. a másodpercek száma. |
| {URL-encoded-resourceURI} |Alacsonyabb eset az URL-kódolást a nagybetűs erőforrás URI |
| {Házirendnév} |A megosztott elérési házirendet, amely a token hivatkozik neve. Hiányzik, ha a jogkivonat-eszközbeállításjegyzékben hitelesítő adatok hivatkozik. |

**Megjegyzés: előtag alapján**: az URI-előtag számított szegmens és nem karakter. Például `/a/b` előtagja, az `/a/b/c` nem `/a/bc`.

Az alábbi Node.js kódrészletben láthatja a hívott függvény **generateSasToken** , amely kiszámítja a jogkivonatot a bemenetek `resourceUri, signingKey, policyName, expiresInMins`. A következő részek a különböző bemeneti adatokat a különböző token használatából adódó inicializálásával.

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

Összehasonlítása, mint a biztonsági jogkivonat létrehozásához egyenértékű Python kódot van:

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

A funkció a C# használatával hozza létre egy biztonsági jogkivonatot is:

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

    string stringToSign = WebUtility.UrlEncode(resourceUri).ToLower() + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri).ToLower(), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Az idő a token érvényességének IoT-központ gépeken van hitelesítve, mivel az óra, a gép, amely a token hoz létre az eltéréseket minimális kell lennie.

### <a name="use-sas-tokens-in-a-device-app"></a>Használja a SAS-tokenje eszköz alkalmazásban

Két módon beszerzése **DeviceConnect** IoT-központ engedélyeket a biztonsági jogkivonatokat: használja a [eszköz szimmetrikus kulcsot az identitásjegyzékhez az](#use-a-symmetric-key-in-the-identity-registry), vagy használjon egy [megosztott elérési kulcsával](#use-a-shared-access-policy).

Ne feledje, hogy eszközökhöz elérhető összes funkciót tesz elérhetővé előtaggal rendelkező végpontokon tervezési `/devices/{deviceId}`.

> [!IMPORTANT]
> Az egyetlen lehetőség, hogy az IoT-központ végez hitelesítést egy adott eszköz eszköz identitása szimmetrikus kulcsot használ. Azokban az esetekben, amikor egy megosztott elérési házirendet eszköz funkció eléréséhez használt a megoldás figyelembe kell vennie a megbízható részletes a biztonsági jogkivonat kiállító összetevő.

Az eszköz felé néző végpontok (függetlenül a protocol) a következők:

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Eszköz-felhő üzeneteket küldeni. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Felhő eszközre üzeneteket fogadni. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>A szimmetrikus kulcsot használja az identitásjegyzékhez

Amikor egy eszközidentitás szimmetrikus kulcs használatával hoz létre jogkivonatokat, a házirendnév (`skn`) elem a jogkivonat nincs megadva.

Például egy jogkivonatot létrehozott összes eszköz funkciók eléréséhez a következő paramétereket kell rendelkezniük:

* erőforrás URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* aláírási kulcs: bármely szimmetrikus kulcs a `{device id}` identitása
* Nincs házirend neve
* a lejárati idő.

Egy példa az előző Node.js-függvény használatával a következő lesz:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Az eredmény, az összes funkciót device1 hozzáférést biztosít, amely a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Lehet létrehozni a SAS-jogkivonat használatával a .NET [eszköz explorer] [ lnk-device-explorer] eszköz vagy a platformfüggetlen, Python-alapú [az IoT-bővítményt az Azure CLI 2.0] [ lnk-IoT-extension-CLI-2.0] parancssori segédprogram.

### <a name="use-a-shared-access-policy"></a>Egy megosztott elérési házirendet használja

Amikor egy jogkivonatot hoz létre egy megosztott elérési házirendet, állítsa be a `skn` mezőjét a házirend nevét. Ez a házirend biztosítania kell a **DeviceConnect** engedéllyel.

A megosztott elérési házirendeket eszköz funkció eléréséhez használt kétféle módon történhet a következők:

* [a felhő protokoll átjárók][lnk-endpoints],
* [a token szolgáltatások] [ lnk-custom-auth] valósítja meg az egyéni hitelesítési sémák használatával.

Mivel a megosztott elérési házirend potenciálisan hozzáférést biztosíthat bármely olyan eszközről csatlakozni, fontos biztonsági jogkivonatokat létrehozásakor használt a helyes erőforrás URI azonosítója. Ez a beállítás különösen fontos a token szolgáltatásokat, amelyek a jogkivonatot az erőforrás URI azonosítója egy adott eszközt hatókörének rendelkezik. Ezt a pontot fontos kevesebb protokoll átjárók mivel azokat már van közvetítő forgalmat az összes eszköz.

Tegyük fel, a jogkivonat-szolgáltatás használatával az előre létrehozott megosztott hozzáférési házirend nevű **eszköz** jogkivonat hozna létre a következő paraméterekkel:

* erőforrás URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* aláírási kulcs: egyik kulcsának a `device` házirend,
* házirend neve: `device`,
* a lejárati idő.

Egy példa az előző Node.js-függvény használatával a következő lesz:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, az összes funkciót device1 hozzáférést biztosít, amely a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

A protokoll-átjáró használhatja ugyanazokat az összes eszköz egyszerűen beállítása az erőforrás URI azonosítója a `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Használja a biztonsági jogkivonatokat a szolgáltatás-összetevők

Szolgáltatás-összetevők csak hoz létre a megfelelő engedélyek megadását, amint azt korábban megosztott elérési házirendeket használó biztonsági jogkivonatokat.

Ez a szolgáltatás funkciók elérhetők a végpontok:

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices` |Létrehozása, frissítése, lekérése és törlése eszköz identitások. |
| `{iot hub host name}/messages/events` |Eszköz-felhő üzeneteket fogadni. |
| `{iot hub host name}/servicebound/feedback` |A felhő-eszközre küldött üzenetek visszajelzést kap. |
| `{iot hub host name}/devicebound` |Felhő-eszközre küldött üzenetek küldése. |

Tegyük fel, a megosztott hozzáférési házirend nevű szolgáltatás létrehozásakor használja az előre létrehozott **registryRead** jogkivonat hozna létre a következő paraméterekkel:

* erőforrás URI: `{IoT hub name}.azure-devices.net/devices`,
* aláírási kulcs: egyik kulcsának a `registryRead` házirend,
* házirend neve: `registryRead`,
* a lejárati idő.

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, az eszköz identitások olvasási hozzáférést biztosítanak, amely a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Támogatott X.509-tanúsítványokat

X.509-tanúsítvány használatával hitelesíteni egy eszközt az IoT-központ. A tanúsítványok tartalmaznak:

* **Meglévő X.509 tanúsítvány**. Előfordulhat, hogy egy eszköz már társítva X.509 tanúsítvány. Az eszköz a tanúsítvány használatával hitelesítik magukat az IoT-központ.
* **A önálló jön létre, és önaláírt tanúsítvány X-509**. A gyártó vagy a belső fejlesztésű deployer ezeket a tanúsítványokat létrehozni és és tárolására is a megfelelő titkos kulcsnak (tanúsítvány) az eszközön. Használhatja például a [OpenSSL] [ lnk-openssl] és [Windows SelfSignedCertificate] [ lnk-selfsigned] segédprogram erre a célra.
* **X.509 tanúsítvány hitelesítésszolgáltató által aláírt**. Egy eszköz azonosítására és sikerült magát hitelesítenie, IoT-központ, egy X.509 tanúsítvány jön létre, és aláírt által hitelesítésszolgáltató (CA) is használhat. Az IoT-központ csak ellenőrzi, hogy az ujjlenyomat jelenik meg a megegyezik-e a konfigurált ujjlenyomattal. IOT hubbal nem felel meg a tanúsítványlánc.

Egy eszköz vagy használhat egy X.509 tanúsítvány vagy egy biztonsági jogkivonatot a hitelesítés, de soha sem egyszerre mindkettőre.

### <a name="register-an-x509-certificate-for-a-device"></a>Egy eszköz X.509 tanúsítvány regisztrálása

A [Azure IoT szolgáltatás SDK for C#] [ lnk-service-sdk] (verzió 1.0.8+) támogatja az olyan eszköz, amely a hitelesítéshez használja egy X.509 tanúsítvány regisztrálása. Például az importálási/exportálási az eszközök más API-k X.509-tanúsítványokat is támogatja.

### <a name="c-support"></a>C\# támogatása

A **RegistryManager** osztály programozott módon kell regisztrálni egy eszközt biztosít. Különösen a **AddDeviceAsync** és **UpdateDeviceAsync** módszerek lehetővé teszik a regisztrálja, és frissítse egy eszközt az IoT-központ identitásjegyzékhez. Ez a két módszer igénybe vehet egy **eszköz** példány bemeneti adatként. A **eszköz** osztály tartalmaz egy **hitelesítési** tulajdonság, amely lehetővé teszi az elsődleges és másodlagos X.509 tanúsítvány-ujjlenyomatok megadását. Az ujjlenyomat (kódolással bináris DER tárolt) X.509-tanúsítvány egy SHA-1 kivonatoló jelöli. Lehetősége van egy elsődleges ujjlenyomatot vagy egy másodlagos ujjlenyomatot, vagy mindkettő megadására. Elsődleges és másodlagos ujjlenyomatok tanúsítvány helyettesítő helyzetek kezelésére használhatók.

> [!NOTE]
> Az IoT-központ nem kér és nem tárolja a teljes X.509-tanúsítványt, csak az ujjlenyomat.

Íme egy minta C\# kell regisztrálni egy X.509 tanúsítvánnyal eszközt kódrészletet:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Egy X.509 tanúsítvány közbeni futásidejű műveletek

A [Azure IoT-eszközök SDK for .NET] [ lnk-client-sdk] (verzió 1.0.11+) X.509 tanúsítvány használatát támogatja.

### <a name="c-support"></a>C\# támogatása

Az osztály **DeviceAuthenticationWithX509Certificate** a létrehozását támogatja **DeviceClient** példányok X.509 tanúsítvánnyal. Az X.509 tanúsítvány a titkos kulcsot tartalmazó PFX (más néven PKCS #12) formátumban kell lennie.

Íme egy minta kódrészletet:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Egyéni eszközhitelesítés

Használhatja az IoT Hub [identitásjegyzékhez] [ lnk-identity-registry] eszközönkénti biztonsági hitelesítő adatok beállítása és a hozzáférés vezérlése segítségével [jogkivonatok][lnk-sas-tokens]. Ha egy IoT-megoldás már van egy egyéni identitás beállításjegyzék és/vagy hitelesítési séma, érdemes létrehozni egy *token service* Ez az infrastruktúra integrálása az IoT-központ. Ily módon használhatja más IoT-szolgáltatásokat a megoldásban.

A jogkivonat-szolgáltatás egy olyan egyéni felhőalapú szolgáltatás. Az IoT-központ használ *megosztott hozzáférési házirend* rendelkező **DeviceConnect** létrehozásához szükséges engedélyek *eszköz hatókörű* jogkivonatokat. Ezeket a jogkivonatokat engedélyezheti az IoT hub való kapcsolódáshoz.

![A jogkivonat-szolgáltatás minta lépései][img-tokenservice]

A jogkivonat-szolgáltatás minta fő lépései a következők:

1. Az IoT-központot, megosztott hozzáférési házirend létrehozása **DeviceConnect** az IoT hub engedélyeit. Ezt a házirendet hozhat létre a [Azure-portálon] [ lnk-management-portal] vagy programon keresztül. A jogkivonat-szolgáltatás használja ezt a házirendet hoz létre a jogkivonatok aláírásához.
1. Ha egy eszközt az IoT hub hozzáférésre van szüksége, egy aláírt jogkivonat kér a jogkivonat-szolgáltatás. Az eszköz hitelesíthető az egyéni identitás-beállításjegyzék/hitelesítési séma annak meghatározásához, hogy a jogkivonat-szolgáltatás a jogkivonat létrehozásához használja az eszközidentitást.
1. A jogkivonat-szolgáltatás egy jogkivonatot ad vissza. A jogkivonat segítségével hozhatók létre `/devices/{deviceId}` , `resourceURI`, a `deviceId` hitelesített eszközként. A jogkivonat-szolgáltatás a megosztott elérési házirendet használja a jogkivonat létrehozásához.
1. Az eszköz közvetlenül és az IoT hub használja a jogkivonatot.

> [!NOTE]
> Használhatja a .NET-osztályt [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] vagy a Java-osztály [IotHubServiceSasToken] [ lnk-java-sas] a jogkivonat-szolgáltatás a jogkivonat létrehozásához.

A jogkivonat-szolgáltatás tetszés szerint állíthatja be a jogkivonat lejáratáról. Amikor a jogkivonat lejár, az IoT-központ kiszolgálója az eszköz kapcsolata. Ezt követően az eszköz egy új jogkivonatot kell kérniük a jogkivonat-szolgáltatás. Egy rövid lejárati idejének fokozott terhelést jelent a jogkivonat-szolgáltatás és az eszközhöz.

Egy eszköz szeretne az elosztóhoz csatakoztatni, hozzá kell adnia továbbra is azt az IoT-központ identitásjegyzékhez – annak ellenére, hogy az eszköz használ egy jogkivonatot, és nem eszközkulcs való csatlakozáshoz. Ezért továbbra is engedélyezésével vagy letiltásával eszköz identitásokat az eszköz hozzáférés-vezérlés használata a [identitásjegyzékhez][lnk-identity-registry]. Ez a megközelítés azzal csökkenti az a tokenek használatára hosszú lejárati idő feltüntetésével kockázatát.

### <a name="comparison-with-a-custom-gateway"></a>Egy egyéni átjáró összehasonlítása

A jogkivonat-szolgáltatás minta nem az ajánlott módszer az IoT-központot egy egyéni identitás rendszerleíró adatbázis-hitelesítési séma végrehajtásához. Ez a minta használata ajánlott, mivel az IoT-központ továbbra is a megoldás forgalom nagy részét kezeli. Azonban ha az egyéni hitelesítési séma így egymásba kapcsolódik, a protokollal, szükség lehet egy *egyéni átjáró* feldolgozni a forgalmat. Ilyen eset például használ[Transport Layer Security (TLS) és előmegosztott kulccsal (PSKs)][lnk-tls-psk]. További információkért lásd: a [protokoll-átjáró] [ lnk-protocols] cikk.

## <a name="reference-topics"></a>Referencia-témaköreit:

A következő témaköröket további tudnivalókat az IoT hub hozzáférést biztosítanak.

## <a name="iot-hub-permissions"></a>Az IoT-központ engedélyek

Az alábbi táblázat az engedélyeket, használhatja az IoT hub való hozzáférés vezérlése érdekében.

| Engedély | Megjegyzések |
| --- | --- |
| **RegistryRead** |Olvasási hozzáférést biztosít az identitásjegyzékhez. További információkért lásd: [identitásjegyzékhez][lnk-identity-registry]. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |
| **RegistryReadWrite** |Biztosít az olvasási és írási hozzáférést biztosít az identitásjegyzékhez. További információkért lásd: [identitásjegyzékhez][lnk-identity-registry]. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |
| **ServiceConnect** |Cloud service irányuló kommunikációra és figyelési végpontok biztosít hozzáférést. <br/>Engedélyt ad eszközről a felhőbe üzeneteket fogadni, felhőalapú-eszközre küldött üzenetek küldése és a megfelelő kézbesítési visszaigazolások beolvasása. <br/>Engedélyt beolvasása kézbesítési a nyugtázás a fájl feltöltése. <br/>Engedélyt ad a címkék és a kívánt tulajdonságok frissítése, jelentett tulajdonságainak beolvasása és lekérdezéseket futtathat eszköz twins eléréséhez. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |
| **DeviceConnect** |Engedélyezi a hozzáférést a eszköz felé néző végpontok. <br/>Engedélyt ad felhő eszközre üzeneteket eszköz a felhőbe küldött üzeneteket küldjön és fogadjon. <br/>Engedélyt ad egy eszközről fájlfeltöltés végrehajtásához. <br/>Eszköz szükséges iker tulajdonság értesítéseket, és frissítse az eszköz a két engedélyt jelentett tulajdonságait. <br/>Engedélyt hajtsa végre a fájl feltöltése. <br/>Ez az engedély eszközöket használják. |

## <a name="additional-reference-material"></a>További referenciaanyag

Az IoT Hub fejlesztői útmutató más hivatkozás témaköröket tartalmazza:

* [IoT-központok végpontjai] [ lnk-endpoints] ismerteti a különböző végpontok, amelyek minden egyes IoT-központ elérhetővé teszi a futásidejű és felügyeleti műveletek.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti a kvóták és a szabályozás viselkedéseket, amelyek az IoT-központ szolgáltatás vonatkozik.
* [Az Azure IoT eszköz és a szolgáltatás SDK-k] [ lnk-sdks] felsorolja a különböző nyelvi használhatja az eszköz és a szolgáltatás alkalmazások gondoskodnak az IoT hubbal fejlesztésekor SDK-k.
* [Az IoT-központ lekérdezési nyelv] [ lnk-query] a lekérdezési nyelv segítségével adatok lekérését az IoT-központ az eszköz twins és feladatokat ismerteti.
* [Az IoT Hub MQTT támogatási] [ lnk-devguide-mqtt] IoT-központ támogatásával kapcsolatos további információkat biztosít a MQTT protokoll.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan való hozzáférést az IoT-központ, a következő IoT Hub fejlesztői útmutató témakörei iránt érdeklődik esetleg:

* [Eszköz twins segítségével szinkronizálja az állapotot és konfigurációk][lnk-devguide-device-twins]
* [Az eszközön közvetlen metódus][lnk-devguide-directmethods]
* [Több eszközön feladatok ütemezése][lnk-devguide-jobs]

Ha azt szeretné, hogy próbálja ki azokat a jelen cikkben ismertetett fogalmakat, tekintse meg az IoT-központ anyagra:

* [Ismerkedés az Azure IoT Hub][lnk-getstarted-tutorial]
* [Az IoT hubbal felhő eszközre üzenetek küldése][lnk-c2d-tutorial]
* [Az IoT-központ eszközről a felhőbe üzenetek feldolgozása][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-IoT-extension-CLI-2.0]: https://github.com/Azure/azure-iot-cli-extension

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
