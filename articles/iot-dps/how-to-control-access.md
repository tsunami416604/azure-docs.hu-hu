---
title: Az IoT Device Provisioning Service biztonsági végpontok |} A Microsoft Docs
description: Alapelvei – hozzáférés az IoT Device Provisioning Service háttérrendszer alkalmazások vezérlése. Biztonsági jogkivonatok kapcsolatos információkat tartalmaz.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 7ff622ceac9c49eda7ba6bca1a8bb3aaabccb816
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495430"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Hozzáférés vezérlése az Azure IoT Hub Device Provisioning Service

Ez a cikk leírja a biztonságos az IoT Device Provisioning service beállításait. A kiépítési szolgáltatás által használt *engedélyek* minden végpont hozzáférést. Engedélyek a funkcionalitás alapján szolgáltatáspéldány való hozzáférést korlátozza.

Ez a cikk ismerteti:

* A különböző engedélyekkel, hogy a kiépítési szolgáltatáshoz való hozzáféréshez háttér-alkalmazásokhoz biztosíthat.
* A hitelesítési folyamat és a jogkivonatok segítségével ellenőrizze az engedélyeket.

### <a name="when-to-use"></a>A következő esetekben használja

A kiépítési szolgáltatás végpontok bármelyikét eléréséhez szükséges engedélyekkel kell rendelkeznie. Például egy háttérbeli alkalmazás tartalmaznia kell egy jogkivonatot, és minden üzenetet küld a szolgáltatás biztonsági hitelesítő adatokat tartalmazó.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

Biztosítani [engedélyek](#device-provisioning-service-permissions) a következő módon:

* **Megosztott hozzáférés engedélyezési házirendek**. Megosztott elérési házirendeket is biztosítson tetszőleges kombinációját [engedélyek](#device-provisioning-service-permissions). A szabályzatokat definiálhat a [az Azure portal][lnk-management-portal], vagy programozott módon használatával a [Device Provisioning Service REST API-k][lnk-resource-provider-apis]. Újonnan létrehozott eszközkiépítési szolgáltatás az alábbi alapértelmezett házirend van:

* **provisioningserviceowner**: A házirend az összes engedélyt.

> [!NOTE]
> Lásd: [engedélyek](#device-provisioning-service-permissions) részletes információkat.

## <a name="authentication"></a>Authentication

Az Azure IoT Hub Device Provisioning Service-végpontokra irányuló hozzáférést biztosít a megosztott elérési házirendekkel jogkivonat ellenőrzésével. Biztonsági hitelesítő adatok, például a szimmetrikus kulcsokat, a rendszer soha nem elküldve a hálózaton keresztül.

> [!NOTE]
> A Device Provisioning Service erőforrás-szolgáltató az Azure-előfizetéssel, biztonságos, mivel az összes szolgáltatót a [Azure Resource Manager][lnk-azure-resource-manager].

Hozhatnak létre, és a biztonsági jogkivonatokkal kapcsolatos további információkért tekintse meg a következő szakaszt.

HTTP az egyetlen támogatott protokoll és hitelesítési érvényes token felvételével valósítja meg a **engedélyezési** kérés fejlécéhez.

#### <a name="example"></a>Példa
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> A [Azure IoT Device Provisioning Service SDK] [ lnk-sdks] automatikusan a szolgáltatáshoz való csatlakozáskor jogkivonatokat hoz létre.

## <a name="security-tokens"></a>Biztonsági jogkivonatok

A Device Provisioning Service biztonsági jogkivonatokat használ elkerülése érdekében a kulcsok küldése az átviteli szolgáltatások hitelesítéséhez. Emellett biztonsági jogkivonatok érvényesség, és a hatókör korlátozott. [Az Azure IoT Device Provisioning szolgáltatási SDK-k] [ lnk-sdks] automatikusan jogkivonatokat hoz létre, anélkül, hogy semmiféle speciális beállítást. Bizonyos forgatókönyvek megkövetelik hozhat létre és használhat közvetlenül a biztonsági jogkivonatokat. Az ilyen forgatókönyvek között megtalálható a közvetlen használatát a HTTP-felületen.

### <a name="security-token-structure"></a>Biztonsági jogkivonat szerkezete

Biztonsági jogkivonatok használatával hozzáférést időhöz kötött bizonyos funkciók az IoT Device Provisioning Service szolgáltatások számára. A kiépítési szolgáltatáshoz való csatlakozáshoz engedély beszerzéséhez szolgáltatások biztonsági jogkivonatot egy közös hozzáférésű vagy egy szimmetrikus kulcs aláírt kell küldenie.

A jogkivonatot egy közös hozzáférési kulcs engedélyezi a hozzáférést a megosztott elérési szabályzatok jogosultságai társított összes funkciót a aláírva. 

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Az alábbiakban a várt értékek:

| Érték | Leírás |
| --- | --- |
| {aláírás} |Egy HMAC-SHA256 aláírás karakterlánccá: `{URL-encoded-resourceURI} + "\n" + expiry`. **Fontos**: A kulcs a Base64 kódolású anyag dekódolni, és a HMAC-SHA256 végzéséhez kulcsaként használt.|
| {expiry} |UTF8 karakterláncok az alapidőpont 00:00:00 (UTC), a 1970. január 1. óta eltelt másodpercek száma. |
| {URL-encoded-resourceURI} | Lower használatieset-URL-Címének kódolása a kisbetűs erőforrás URI-t. URI-előtag (szegmens) szerint a végpontot, amely az ezzel a tokennel, az IoT Device Provisioning Service (nincs protocol) állomásneve kezdve érhető el. Például: `mydps.azure-devices-provisioning.net`. |
| {policyName} |Ez a token által hivatkozott megosztott elérési házirend neve. |

**Megjegyzés: az előtag**: Az URI-előtag karakteres szegmens és nem számítja ki. Például `/a/b` az előtagja `/a/b/c` esetében nem `/a/bc`.

Az alábbi Node.js látható a hívott függvény **generateSasToken** , amely kiszámítja a jogkivonatot a bemeneti adatok `resourceUri, signingKey, policyName, expiresInMins`. A következő szakaszok bemutatják, hogyan lehet inicializálni a különböző adatbevitelek esetében különböző token használati eseteit.

```javascript
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

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Mivel a token érvényességének idő érvényesítési IoT Device Provisioning Service-gépeken, az eltéréseket az óra, a gép, amely létrehozza a jogkivonat a minimális kell lennie.

### <a name="use-security-tokens-from-service-components"></a>Használja a biztonsági jogkivonatokat a szolgáltatás-összetevők

Szolgáltatás-összetevők csak hozhatja létre a megfelelő engedélyek megadása a korábbiakban leírtak megosztott elérési házirendeket használó biztonsági jogkivonatokat.

Az elérhetővé tett végpontokon funkciói a következők:

| Végpont | Funkció |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |A Device Provisioning Service szolgáltatással eszköz beléptetési műveleteket biztosít. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Eszköz regisztrációs csoportok kezeléséhez műveleteket biztosít. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Lekérdezéséhez és kezeléséhez az eszközregisztrációk állapotának műveleteket biztosít. |


Például, egy szolgáltatást, egy előre létrehozott használatával létrehozott megosztott hozzáférési házirend nevű **enrollmentread** hozna létre egy tokent az alábbi paraméterekkel:

* erőforrás-URI: `{mydps}.azure-devices-provisioning.net`,
* aláíró kulcs: kulcsainak egyikét a `enrollmentread` házirend
* házirend neve: `enrollmentread`,
* bármely lejárati time.backn

![A Device Provisioning service-példány megosztott hozzáférési szabályzat létrehozása a portálon][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, szeretne hozzáférést biztosítani, olvassa el az összes regisztrációs rekordot, amely a következő lesz:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referencia-témaköreit:

A következő referencia-témakörök nyújtanak további információt az IoT Device Provisioning Service-hozzáférés szabályozása.

### <a name="device-provisioning-service-permissions"></a>Device Provisioning Service-engedélyek

A következő táblázat felsorolja az engedélyeket, ki férhet hozzá az IoT Device Provisioning Service használatával.

| Engedély | Megjegyzések |
| --- | --- |
| **ServiceConfig** |Engedélyezi a hozzáférést a szolgáltatáskonfiguráció módosítása. <br/>Ez az engedély háttérbeli felhőszolgáltatásokat használják. |
| **EnrollmentRead** |Ad. olvassa el az eszközök regisztrációját és regisztrációs csoportokkal való hozzáférést. <br/>Ez az engedély háttérbeli felhőszolgáltatásokat használják. |
| **EnrollmentWrite** |Biztosít írási hozzáférés az eszközök regisztrációját és regisztrációs csoportokkal. <br/>Ez az engedély háttérbeli felhőszolgáltatásokat használják. |
| **RegistrationStatusRead** |Az olvasási biztosít hozzáférést az eszköz regisztrációs állapotát. <br/>Ez az engedély háttérbeli felhőszolgáltatásokat használják. |
| **RegistrationStatusWrite**  |Engedélyezi a hozzáférést az eszköz regisztrációs állapota törlése. <br/>Ez az engedély háttérbeli felhőszolgáltatásokat használják. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
