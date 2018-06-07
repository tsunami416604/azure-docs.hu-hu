---
title: Biztonsági végpontok IoT eszköz kiépítése szolgáltatáshoz |} Microsoft Docs
description: Alapfogalmak - háttér-alkalmazások IoT eszköz kiépítése szolgáltatáshoz való hozzáférés vezérlése érdekében hogyan. Biztonsági jogkivonatok kapcsolatos adatokat tartalmaz.
author: dsk-2015
manager: timlt
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: dkshir
ms.openlocfilehash: e33f6b61f757c9d7f6a773141ad0deea363be2b7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629388"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub eszköz kiépítése szolgáltatáshoz hozzáférést vezérlő

Ez a cikk ismerteti az IoT-eszközök üzembe helyezési szolgáltatása biztonsági beállításaival. A létesítési szolgáltatás által használt *engedélyek* hozzáférést biztosít egyes végpontok. Engedélyek korlátozza a hozzáférést egy szolgáltatáspéldány funkciókon alapulnak.

Ez a cikk ismerteti:

* A különböző engedélyek, hogy a létesítési szolgáltatás eléréséhez háttér-alkalmazásokhoz biztosíthat.
* A hitelesítési folyamat és a jogkivonatok segítségével ellenőrizze az engedélyeket.

### <a name="when-to-use"></a>A következő esetekben használja

A létesítési szolgáltatás végpontok bármelyikét eléréséhez szükséges engedélyekkel kell rendelkeznie. Például egy háttér-alkalmazás tartalmaznia kell egy jogkivonatot tartalmazó biztonsági hitelesítő adatokat, és minden üzenetet küld a szolgáltatás.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

Meg lehet adni [engedélyek](#device-provisioning-service-permissions) a következőképpen:

* **Megosztott hozzáférés-engedélyezési házirendek**. Megosztott elérési házirendeket is biztosítani bármilyen kombinációját [engedélyek](#device-provisioning-service-permissions). Megadhatja, hogy a házirendek a [Azure-portálon][lnk-management-portal], vagy programozott módon használatával a [eszköz kiépítése szolgáltatás REST API-k][lnk-resource-provider-apis]. Egy újonnan létrehozott létesítési szolgáltatás a következő alapértelmezett szabályzattal rendelkezik:

  * **provisioningserviceowner**: házirend az összes engedélyt.

> [!NOTE]
> Lásd: [engedélyek](#device-provisioning-service-permissions) részletes információkat.

## <a name="authentication"></a>Hitelesítés

Azure IoT Hub eszköz kiépítése szolgáltatáshoz végpontok hozzáférést biztosít a megosztott elérési házirendek szerinti jogkivonat ellenőrzésével. Biztonsági hitelesítő adatok, például a szimmetrikus kulcsokat, a rendszer soha nem elküldve a hálózaton keresztül.

> [!NOTE]
> Az eszköz kiépítése szolgáltatáshoz erőforrás-szolgáltató védett keresztül az Azure-előfizetéshez, mert lévő összes szolgáltatónál a [Azure Resource Manager][lnk-azure-resource-manager].

Összeállítani, és biztonsági jogkivonatokat használatával kapcsolatos további információkért lásd: a következő szakaszban.

HTTP az egyetlen támogatott protokoll, és megvalósítja az hitelesítési érvényes token-ot a **engedélyezési** kérelemfejlécet.

#### <a name="example"></a>Példa
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> A [Azure IoT eszközoldali létesítési szolgáltatás SDK-k] [ lnk-sdks] automatikusan hoz létre a jogkivonatokat, a szolgáltatáshoz való kapcsolódáskor.

## <a name="security-tokens"></a>Biztonsági jogkivonatok kiosztva
Az eszköz kiépítése szolgáltatáshoz biztonsági jogkivonatokat használ hitelesítésére szolgáltatásokat ne küldjön kulcsok a keresztülhaladnak a hálózaton. Emellett biztonsági jogkivonatok érvényesség és hatókör korlátozott. [Az Azure IoT eszközoldali létesítési szolgáltatás SDK-k] [ lnk-sdks] automatikusan hoz létre jogkivonatokat anélkül, hogy semmiféle speciális beállítást. Bizonyos esetekben kell létrehozni és biztonsági jogkivonatokat közvetlenül használható. Ilyen forgatókönyvek például a HTTP-felület közvetlen használatát.

### <a name="security-token-structure"></a>Biztonsági jogkivonat szerkezete

Biztonsági jogkivonatok segítségével hozzáférést biztosíthat idő, amelyet az IoT eszköz kiépítése szolgáltatáshoz az adott funkciót szolgáltatások. Ahhoz, hogy a létesítési szolgáltatáshoz való kapcsolódáshoz engedélyezési, a szolgáltatások aláírva, vagy egy közös hozzáférésű, vagy a szimmetrikus kulcs biztonsági jogkivonatokat kell küldenie.

A token egy megosztott elérési kulcs engedélyezi a hozzáférést a megosztott hozzáférési házirend engedélyeket társított összes funkcióját a aláírva. 

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Az alábbiakban a várt értékek:

| Érték | Leírás |
| --- | --- |
| {aláírás} |Egy HMAC-SHA256 aláírás karakterlánc a következő formátumban: `{URL-encoded-resourceURI} + "\n" + expiry`. **Fontos**: A kulcs a Base64 kódolású anyag dekódolni, és a HMAC-SHA256 számítási végrehajtásához kulcsként.|
| {expiry} |UTF8 karakterláncok esetében a epoch 00:00:00 UTC 1970. január 1. a másodpercek száma. |
| {URL-encoded-resourceURI} | Alacsonyabb eset, a nagybetűs erőforrás URI URL-kódolást. URI-előtag (által szegmens) a végpontok a jogkivonatok az IoT-kiépítés szolgáltatást (protokoll) állomásneve kezdődően elérhető. Például: `mydps.azure-devices-provisioning.net`. |
| {Házirendnév} |A megosztott elérési házirendet, amely a token hivatkozik neve. |

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

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Az idő a token érvényességének IoT eszköz kiépítése szolgáltatáshoz gépeken van hitelesítve, mivel az óra, a gép, amely a token hoz létre az eltéréseket minimális kell lennie.


### <a name="use-security-tokens-from-service-components"></a>Használja a biztonsági jogkivonatokat a szolgáltatás-összetevők

Szolgáltatás-összetevők csak hoz létre a megfelelő engedélyek megadását, amint azt korábban megosztott elérési házirendeket használó biztonsági jogkivonatokat.

Az alábbiakban a kikerültek a végpontok a funkciói:

| Végpont | Funkció |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Eszköz tanúsítványigénylési műveletekhez az eszköz kiépítése szolgáltatáshoz biztosít. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Feldolgozza a műveleteket a eszköz beléptetési csoportok kezelése. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Itt a beolvasása és a regisztrációk eszköz állapotának kezelése. |


Tegyük fel, egy szolgáltatás generálta egy előre létrehozott megosztott hozzáférési házirend nevű **enrollmentread** jogkivonat hozna létre a következő paraméterekkel:

* erőforrás URI: `{mydps}.azure-devices-provisioning.net`,
* aláírási kulcs: egyik kulcsának a `enrollmentread` házirend,
* házirend neve: `enrollmentread`,
* a lejárati idő.

![A terjesztési pontok példány megosztott hozzáférési házirend létrehozása a portálon][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény az összes beléptetési rekord olvasási hozzáférést biztosítanak, a következő lesz:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referencia-témaköreit:

A következő témaköröket nyújtanak további információt a IoT eszköz kiépítése szolgáltatáshoz való hozzáférés szabályozása.

## <a name="device-provisioning-service-permissions"></a>Eszköz kiépítése szolgáltatáshoz engedélyek

Az alábbi táblázat az engedélyeket, használhatja a IoT eszköz kiépítése szolgáltatáshoz való hozzáférés vezérlése érdekében.

| Engedély | Megjegyzések |
| --- | --- |
| **ServiceConfig** |A szolgáltatáskonfiguráció módosítása biztosít hozzáférést. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |
| **EnrollmentRead** |Olvasási hozzáférés az eszközök regisztrációját és a beléptetési csoportok biztosít. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |
| **EnrollmentWrite** |Biztosít az eszközök regisztrációját és a beléptetési csoportok írási hozzáféréssel. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |
| **RegistrationStatusRead** |Olvasási hozzáférést az eszköz regisztrációs állapotát. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |
| **RegistrationStatusWrite**  |Biztosít hozzáférést az eszköz regisztrációs állapotát törlése. <br/>Háttér-felhőszolgáltatások használja ezt az engedélyt. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
