---
title: Biztonsági végpontok az IoT-eszközkiépítési szolgáltatásban | Microsoft dokumentumok
description: Fogalmak – hogyan szabályozhatja az IoT-eszközlétesítési szolgáltatáshoz (DPS) való hozzáférést a háttér-alkalmazásokhoz. A biztonsági jogkivonatokra vonatkozó információkat tartalmazza.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 2a7e0932d226b1533c039b8529c2c11de06cf525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285148"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Hozzáférés szabályozása az Azure IoT Hub eszközkiépítési szolgáltatásához

Ez a cikk ismerteti az IoT-eszközkiépítési szolgáltatás védelmének lehetőségeit. A létesítési szolgáltatás *engedélyeket* használ az egyes végpontokhoz való hozzáférés engedélyezéséhez. Az engedélyek a funkciók alapján korlátozzák a szolgáltatáspéldányhoz való hozzáférést.

Ez a cikk a következőket ismerteti:

* A különböző engedélyeket, amelyek et adhat egy háttéralkalmazás nak a létesítési szolgáltatás eléréséhez.
* A hitelesítési folyamat és az engedélyek ellenőrzéséhez használt jogkivonatok.

### <a name="when-to-use"></a>A következő esetekben használja

A kiépítési szolgáltatás bármely végpontjának eléréséhez megfelelő engedélyekkel kell rendelkeznie. Például egy háttéralkalmazásnak tartalmaznia kell egy biztonsági hitelesítő adatokat tartalmazó jogkivonatot a szolgáltatásnak küldött összes üzenettel együtt.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

Az [engedélyeket](#device-provisioning-service-permissions) az alábbi módokon adhat meg:

* **Megosztott hozzáférés-engedélyezési házirendek**. A megosztott hozzáférési házirendek az engedélyek bármilyen kombinációját [biztosíthatják.](#device-provisioning-service-permissions) Szabályzatokat definiálhat az [Azure Portalon,][lnk-management-portal]vagy programozott módon az [Eszközkiépítési szolgáltatás REST API-k][lnk-resource-provider-apis]használatával. Egy újonnan létrehozott létesítési szolgáltatás a következő alapértelmezett házirenddel rendelkezik:

* **provisioningserviceowner**: Szabályzat minden engedéllyel.

> [!NOTE]
> A részletes információkért tekintse meg az [engedélyeket.](#device-provisioning-service-permissions)

## <a name="authentication"></a>Hitelesítés

Az Azure IoT Hub-eszközkiépítési szolgáltatás hozzáférést biztosít a végpontokhoz egy jogkivonat a megosztott hozzáférési szabályzatok ellenőrzése. A biztonsági hitelesítő adatok, például a szimmetrikus kulcsok, soha nem kerülnek át a hálózaton keresztül.

> [!NOTE]
> Az Eszközkiépítési szolgáltatás erőforrás-szolgáltató az Azure-előfizetésen keresztül védett, csakúgy, mint az Azure Resource Manager összes [szolgáltatója.][lnk-azure-resource-manager]

A biztonsági jogkivonatok létrehozásáról és használatáról a következő szakaszban talál további információt.

A HTTP az egyetlen támogatott protokoll, és úgy valósítja meg a hitelesítést, hogy egy érvényes jogkivonatot tartalmaz az **engedélyezési** kérelem fejlécében.

#### <a name="example"></a>Példa
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Az [Azure IoT-eszközkiépítési szolgáltatás SDK-k][lnk-sdks] automatikusan generáljogú, amikor csatlakozik a szolgáltatáshoz.

## <a name="security-tokens"></a>Biztonsági jogkivonatok

Az eszközkiépítési szolgáltatás biztonsági jogkivonatokat használ a szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a vezetéken. Emellett a biztonsági jogkivonatok időérvényessége és hatóköre korlátozott. [Az Azure IoT-eszközkiépítési szolgáltatás SDK-k][lnk-sdks] automatikusan generálja a jogkivonatok at anélkül, hogy bármilyen speciális konfigurációt. Egyes forgatókönyvek nem igényel biztonsági jogkivonatok közvetlen létrehozása és használata. Az ilyen forgatókönyvek közé tartozik a HTTP felület közvetlen használata.

### <a name="security-token-structure"></a>Biztonsági jogkivonat-struktúra

A biztonsági jogkivonatok segítségével időhöz kötött hozzáférést biztosíthat a szolgáltatásokhoz az IoT-eszközkiépítési szolgáltatás adott funkcióihoz. Ahhoz, hogy engedélyt kapjon a létesítési szolgáltatáshoz való csatlakozáshoz, a szolgáltatásoknak megosztott hozzáféréssel vagy szimmetrikus kulccsal aláírt biztonsági jogkivonatokat kell küldeniük.

A megosztott hozzáférési kulccsal aláírt jogkivonat hozzáférést biztosít a megosztott hozzáférési szabályzat engedélyeihez társított összes funkcióhoz. 

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

A várt értékek a következők:

| Érték | Leírás |
| --- | --- |
| {aláírás} |A Következő űrlap HMAC-SHA256 `{URL-encoded-resourceURI} + "\n" + expiry`aláírási karakterlánca: . **Fontos:** A kulcs dekódolja base64 és kulcsként használják a HMAC-SHA256 számítás végrehajtásához.|
| {lejárat} |UTF8 karakterláncok másodpercek száma a 00:00:00 UTC 1970. |
| {URL-kódolt-resourceURI} | A kisbetűs erőforrás URI-jának kisbetűs URL-kódolása. URI-előtag (szegmensenként) a tokenttartalmazó végpontok, kezdve az IoT-eszköz létesítési szolgáltatás állomásneve (nincs protokoll). Például: `mydps.azure-devices-provisioning.net`. |
| {policyName} |Annak a megosztott hozzáférési szabályzatnak a neve, amelyre ez a jogkivonat hivatkozik. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Összehasonlításképpen a biztonsági jogkivonat létrehozásához szükséges egyenértékű Python-kód a következő:

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
> Mivel a jogkivonat érvényességi ideje érvényesítve van az IoT-eszköz létesítési szolgáltatás gépeken, a jogkivonatot generáló gép órajelének eltolódása minimálisnak kell lennie.

### <a name="use-security-tokens-from-service-components"></a>Szolgáltatás-összetevők biztonsági jogkivonatainak használata

A szolgáltatás-összetevők csak olyan megosztott hozzáférési házirendek használatával hozhatnak létre biztonsági jogkivonatokat, amelyek a korábban ismertetett megfelelő engedélyeket biztosítják.

A végpontokon elérhető szolgáltatásfunkciók a következők:

| Végpont | Funkció |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Eszközregisztrációs műveleteket biztosít az eszközkiépítési szolgáltatással. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Az eszközregisztrációs csoportok kezeléséhez szolgáló műveleteket biztosít. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Az eszközregisztrációk állapotának lekéréséhez és kezeléséhez szolgáló műveleteket biztosít. |


Például egy előre létrehozott megosztott hozzáférési szabályzattal **létrehozott, enrollmentread** nevű szolgáltatás a következő paraméterekkel hoz létre jogkivonatot:

* erőforrás URI-ja: `{mydps}.azure-devices-provisioning.net`,
* aláíró kulcs: a `enrollmentread` házirend egyik kulcsa,
* házirend neve: `enrollmentread`,
* bármely lejárati idő.backn

![Megosztott hozzáférési szabályzat létrehozása az eszközkiépítési szolgáltatáspéldányhoz a portálon][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, amely hozzáférést biztosít az összes regisztrációs rekord olvasásához, a következő lenne:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referenciatémakörök:

Az alábbi referenciatémakörök további információkat nyújtanak az IoT-eszközkiépítési szolgáltatáshoz való hozzáférés szabályozásáról.

### <a name="device-provisioning-service-permissions"></a>Eszközkiépítési szolgáltatás engedélyei

Az alábbi táblázat felsorolja az IoT-eszközkiépítési szolgáltatáshoz való hozzáférés szabályozásához használható engedélyeket.

| Engedély | Megjegyzések |
| --- | --- |
| **ServiceConfig szolgáltatás** |Hozzáférést biztosít a szolgáltatáskonfigurációk módosításához. <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |
| **BeiratkozásOlvassa el** |Olvasási hozzáférést biztosít az eszközregisztrációkhoz és a regisztrációs csoportokhoz. <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |
| **EnrollmentWrite írása** |Írási hozzáférést biztosít az eszközregisztrációkhoz és a regisztrációs csoportokhoz. <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |
| **RegistrationStatusRead** |Olvasási hozzáférést biztosít az eszköz regisztrációs állapotához. <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |
| **RegistrationStatusWrite**  |Biztosít törlési hozzáférést az eszköz regisztrációs állapotához. <br/>Ezt az engedélyt a háttér-felhőszolgáltatások használják. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
