---
title: Azure útmutató – Azure API FHIR a postman FHIR-kiszolgáló
description: Ez a cikk ismerteti, hogyan lehet hozzáférni egy FHIR API a postman használatával.
services: healthcare-apis
ms.service: healthcare-apis
ms.topic: tutorial
ms.reviewer: dseven
ms.author: mihansen
author: hansenms
ms.date: 02/07/2019
ms.openlocfilehash: 7009fa175e19ad484c83ec3a988d6c33b67fefcd
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823407"
---
# <a name="tutorial-access-fhir-api-with-postman"></a>Oktatóanyag: A postman használatával FHIR API-hozzáférés

Egy ügyfélalkalmazás egy FHIR API elérésére egy [REST API-val](https://www.hl7.org/fhir/http.html). Érdemes közvetlenül kommunikál a FHIR-kiszolgáló létrehozása alkalmazások, például hibakeresési célra is. Ebben az oktatóanyagban végigvezetjük a használatához szükséges lépések [Postman](https://www.getpostman.com/) FHIR kiszolgálóhoz való hozzáférést. Postman egy olyan eszköz, gyakran használják API-kat használó alkalmazások készítése során a hibakereséshez.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-ban FHIR végpont. Beállíthat, amely a felügyelt Azure API használatával az FHIR és a nyílt forráskódú FHIR-kiszolgálóhoz az Azure-hoz. Állítsa be a felügyelt Azure API FHIR a [az Azure portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md), vagy [Azure CLI-vel](fhir-paas-cli-quickstart.md).
- A postman telepítve. Beszerezheti azt a [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>FHIR-kiszolgáló és a hitelesítési adatokat

Postman használatához, meg kell tudni, hogy a következő adatokat:

- A FHIR kiszolgáló URL-CÍMÉT, például `https://MYFHIRSERVICE.azurewebsites.net` vagy `https://MYACCOUNT.azurehealthcareapis.com`
- Az identitásszolgáltató `Authority` FHIR kiszolgáló például `https://login.microsoftonline.com/{TENANT-ID}`
- A beállított `audience`. Ez az Azure API-hoz tartozó FHIR `https://azurehealthcareapis.com` , és az Azure a nyílt forráskódú FHIR kiszolgáló, akkor a [erőforrás-alkalmazás regisztrálása az Azure AD](register-resource-azure-ad-client-app.md).
- A `client_id` (vagy alkalmazás azonosítója), a [ügyfélalkalmazás](register-confidential-azure-ad-client-app.md) a FHIR szolgáltatás eléréséhez fog használni.
- A `client_secret` (vagy az alkalmazás titkos kulcs) az ügyfélalkalmazás.

Végül ellenőrizze, hogy `https://www.getpostman.com/oauth2/callback` ügyfélalkalmazás egy regisztrált válasz URL-cím.

## <a name="connect-to-fhir-server"></a>Csatlakozás kiszolgálóhoz FHIR

A postmannel, tegye a `GET` kérelmet `https://fhir-server-url/metadata`:

![Postman-metaadatok képesség utasítás](media/tutorial-postman/postman-metadata.png)

Ebben a példában a FHIR URL-címe van `https://fhirdocsmsft.azurewebsites.net` és a funkció utasítást a kiszolgáló mindig elérhető legyen `https://fhirdocsmsft.azurewebsites.net/metadata`. Lehet, hogy hitelesítés nélkül érhető el, hogy a végpont.

Ha megpróbálja elérni a korlátozott erőforrásokat, meg kell majd "Sikertelen hitelesítés" választ:

![A hitelesítés nem sikerült](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Hozzáférési jogkivonat beszerzése

Érvényes hozzáférési jogkivonatot beszereznie, válassza az "Engedélyezés", és válasszon típusa "OAuth 2.0":

![Az OAuth 2.0 beállítása](media/tutorial-postman/postman-select-oauth2.png)

A találatok "új hozzáférési Token letöltése" és a egy párbeszédpanel jelenik meg:

![Új hozzáférési jogkivonat kérése](media/tutorial-postman/postman-request-token.png)

Egyes adatok kell:
| Mező                 | Példaérték                                                                                                   | Megjegyzés                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Jogkivonat neve            | MYTOKEN                                                                                                         | A választott név          |
| Engedélyezési típus            | Engedélyezési kód                                                                                              |                            |
| Visszahívási URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Hitelesítési URL-cím              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` van `https://azurehealthcareapis.com` FHIR az Azure API-hoz és `https://MYFHIRSERVICE.azurewebsites.net` nyílt Forráskódú FHIR-kiszolgáló |
| Hozzáférési jogkivonat URL-címe      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| Ügyfél-azonosító             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | Alkalmazásazonosító             |
| Titkos ügyfélkulcs         | `XXXXXXXX`                                                                                                        | Titkos ügyfélkulcs          |
| Állapot                 | `1234`                                                                                                            |                            |
| Ügyfél-hitelesítés | Ügyfél-hitelesítő adatok törzsben elküldendő                                                                                 |                 

A találatok "kérelem Token" és a varázsló végigvezeti az Azure Active Directory-hitelesítési folyamat, és a egy tokent ad vissza a Postmannel. Ha hibákba ütközne nyissa meg a Postman-konzolon (a "Nézet -> Show Postman konzol" menüpont).

A visszaadott jogkivonat képernyőn görgessen le, és nyomja le a "Token használata":

![Jogkivonat használata](media/tutorial-postman/postman-use-token.png)

Most már a jogkivonatot kell feltöltenie a "Hozzáférési Token" mezőjében, és kiválaszthatja az "Elérhető tokenek" származó jogkivonatokat. Ha "Küld" újra ismételje meg a `Patient` erőforrás-keresési, egy állapot szerezheti `200 OK`:

![200 OK](media/tutorial-postman/postman-200-OK.png)

Ebben az esetben vannak az adatbázisban nincs a páciensek és a keresés üres.

Ha, vizsgálja meg a hozzáférési jogkivonatot a olyan eszköz, például [ https://jwt.ms ](https://jwt.ms), tartalom hasonlóan kell megjelennie:

```json
{
  "aud": "https://azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

A hibaelhárítási helyzeteket, ellenőrzése, hogy rendelkezik-e a megfelelő célközönség (`aud` jogcím-) remek elindításához. A felügyelt Azure API FHIR felhasználásra [identitás objektumazonosítók](find-identity-object-ids.md) a szolgáltatáshoz való hozzáférés korlátozásához. Győződjön meg arról, hogy `oid` jogcím a jogkivonat engedélyezett objektumazonosítók listájából objektum-Azonosítót tartalmaz.

## <a name="inserting-a-patient"></a>A betegek beszúrása

Most, hogy egy érvényes hozzáférési jogkivonatot. Beszúrhat egy új kis türelmet. Váltson arra a "POST" metódus, és adja hozzá a következő JSON-dokumentumot a kérelem törzse:

[!code-json[](samples/sample-patient.json)]

Kattintson a "Küldés", és megjelenik a betegek létrehozása sikerült:

![Kis türelmet, létrehozva](media/tutorial-postman/postman-patient-created.png)

Ha a betegek keresés, meg kell jelennie a betegek rekordot:

![Kis türelmet, létrehozva](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a postman segítségével FHIR API már használta. A Microsoft FHIR server támogatott API-funkciókkal olvassa el az Azure-hoz.
 
>[!div class="nextstepaction"]
>[Támogatott FHIR-funkciók](fhir-features-supported.md)