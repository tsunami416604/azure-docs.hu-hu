---
title: REST API-hibakódok – Azure Key Vault
description: Ezeket a hibakódokat egy Azure Key Vault-webszolgáltatáson végzett művelet visszaadhatja.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 01fb5393217834bc0196da25c4a56314ca7eae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294541"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Az Azure Key Vault REST API-hibakódjai
 
A következő hibakódokat egy Azure Key Vault webszolgáltatáson végzett művelet visszaadhatja.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Nem hitelesített kérelem

A 401 azt jelenti, hogy a kérelem nincs hitelesítve a Key Vault számára. 

A kérelem hitelesítése akkor történik meg, ha:

- A kulcstartó ismeri a hívó identitását; És
- A hívó megkísérelhet hozzáférni a Key Vault erőforrásaihoz. 

Számos különböző oka lehet annak, hogy a kérelem visszaadhat 401-et.

### <a name="no-authentication-token-attached-to-the-request"></a>Nincs hitelesítési jogkivonat csatolva a kérelemhez. 

Íme egy példa put kérés, amely az érték egy titkos:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Az "Engedélyezés" fejléc a hozzáférési jogkivonat, amely szükséges minden hívás a Key Vault adatsík műveletek. Ha a fejléc hiányzik, akkor a válasznak 401-nek kell lennie.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>A jogkivonat nem rendelkezik a hozzá társított megfelelő erőforrással. 

Amikor egy hozzáférési jogkivonatot kér az Azure OAUTH-végpontról, az "erőforrás" nevű paraméter kötelező. Az érték fontos a jogkivonat-szolgáltató számára, mert a jogkivonatot a tervezett használatra hatóköre ként méretezi. A Key Vault eléréséhez **szükséges összes** jogkivonat erőforrása *https:\//vault.keyvault.net* (záró perjel nélkül).

### <a name="the-token-is-expired"></a>A token lejárt

A tokenek base64 kódolásúak, és az értékek [http://jwt.calebb.net](http://jwt.calebb.net)dekódolhatók olyan webhelyeken, mint a. Itt van a fenti token dekódolt:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Számos fontos részt láthatunk ebben a tokenben:

- aud (közönség): A token forrása. Figyelje meg, hogy ez <https://vault.azure.net>. Ez a jogkivonat nem fog működni minden olyan erőforrás, amely nem kifejezetten egyezik meg ezt az értéket, például a grafikon.
- iat (kiadva): A kullancsok száma a token kiállításának kora óta.
- nbf (nem korábban): A kullancsok száma a korszak kezdete óta, amikor ez a token érvényessé válik.
- exp (lejárat): A tickek száma a korszak kezdete óta, amikor ez a token lejár.
- appid (alkalmazásazonosító): A kérelmet küldő alkalmazásazonosító GUID azonosítója.
- tid (bérlőazonosító): A kérést küldő megbízó bérlőazonosítójának GUID azonosítója

Fontos, hogy a kérelem működéséhez az összes érték megfelelően azonosítható legyen a jogkivonatban. Ha minden rendben van, akkor a kérelem nem eredményez 401-et.

### <a name="troubleshooting-401"></a>Hibaelhárítás 401

401s kell vizsgálni a jogkivonat-generálási ponttól, mielőtt a kérelem a key vault. Általában kód van használatban a token kéréséhez. A jogkivonat beérkezése után a key vault-kérelem. Ha a kód helyileg fut, a Fiddler segítségével rögzítheti a kérést/választ. `https://login.microsoftonline.com` A kérelem így néz ki:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

A felhasználó által megadott alábbi információk helyesek:

- A kulcstartó bérlői azonosítója
- Az erőforrás-érték https%3A%2F%2Fvault.azure.net (URL-kódolású)
- Ügyfél-azonosító
- Titkos ügyfélkulcs

Győződjön meg arról, hogy a kérelem többi része közel azonos.

Ha csak a válasz-hozzáférési jogkivonatot tudja beszerezni, dekódolhatja azt (ahogy fent látható), biztosítva a bérlői azonosítót, az ügyfélazonosítót (alkalmazásazonosítót) és az erőforrást.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Elégtelen engedélyek

A HTTP 403 azt jelenti, hogy a kérelem hitelesített (ismeri a kérelmező identitást), de az identitás nem rendelkezik engedéllyel a kért erőforrás eléréséhez. Ennek két oka van:

- Nincs hozzáférési házirend az identitáshoz.
- A kérelmező erőforrás IP-címe nem szerepel a key vault tűzfalbeállításaiban.

A HTTP 403 gyakran akkor fordul elő, ha az ügyfél alkalmazása nem az ügyfél által elmedett ügyfélazonosítót használja. Ez általában azt jelenti, hogy a hozzáférési szabályzatok nincsenek megfelelően beállítva a tényleges hívó identitáshoz.

### <a name="troubleshooting-403"></a>Hibaelhárítás 403

Először kapcsolja be a naplózást. Ennek módjáról az Azure [Key Vault naplózása című](key-vault-logging.md)témakörben talál.

A naplózás bekapcsolása után megállapíthatja, hogy a 403-as hozzáférési szabályzat vagy tűzfalházirend miatt történt-e.

#### <a name="error-due-to-firewall-policy"></a>Tűzfalházirend miatti hiba

"Az ügyfélcím (00.00.00.00) nincs engedélyezve, és a hívó nem megbízható szolgáltatás"

Az "Azure megbízható szolgáltatások" korlátozott listája található. Az Azure-webhelyek **nem** megbízható Azure-szolgáltatás. További információt az Azure [App Services Key Vault firewall hozzáférése](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)című blogbejegyzésben talál.

Ahhoz, hogy működjön, hozzá kell adnia az Azure-webhely IP-címét a Key Vaulthoz.

Ha hozzáférési házirend miatt: keresse meg a kérelem objektumazonosítóját, és győződjön meg arról, hogy az objektumazonosító megegyezik azzal az objektummal, amelyhez a felhasználó a hozzáférési szabályzatot próbálja hozzárendelni. Gyakran több objektum lesz az AAD-ben, amelyek azonos nevűek, ezért nagyon fontos a megfelelő kiválasztása. A hozzáférési házirend törlésével és újbóli hozzáadásával ellenőrizheti, hogy több objektum létezik-e ugyanazzal a névvel.

Emellett a legtöbb hozzáférési szabályzat nem igényli az "Engedélyezett alkalmazás" használatát a portálon látható módon. Az engedélyezett alkalmazások ritka "a nevében" hitelesítési forgatókönyvekhez használatosak. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Túl sok kérés

Szabályozás akkor történik, ha a kérelmek száma meghaladja a megadott maximális az időkeretben. Szabályozás esetén a Key Vault válasza HTTP 429 lesz. A kérelmek típusaira vonatkozóan a megadott maximumok szerepelnek. Például: egy HSM 2048 bites kulcs létrehozása 10 másodpercenként 5 kérelem, de minden más HSM-tranzakció 1000 kérelem/10 másodperces korláttal rendelkezik. Ezért fontos megérteni, hogy milyen típusú hívások történnek a szabályozás okának meghatározásakor.
A Key Vaultba irányuló kérelmek általában legfeljebb 2000 kérelmek/10 másodperc. Kivételek a kulcsműveletek, a [Key Vault szolgáltatáskorlátaiban](key-vault-service-limits.md) dokumentált módon

### <a name="troubleshooting-429"></a>Hibaelhárítás 429
A szabályozás taszaaaaaaatot a következő technikákkal:

- Csökkentse a Key Vaultba irányuló kérelmek számát annak meghatározásával, hogy vannak-e minták a kért erőforráshoz, és megpróbálja gyorsítótárazni őket a hívó alkalmazásban. 

- Key Vault szabályozása esetén, igazítsa a kérelmező kódot, hogy egy exponenciális backoff az újrapróbálkozáshoz. Az algoritmus itt ismertetésre: [Hogyan kell szabályozni az alkalmazást](key-vault-ovw-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Ha a kérelmek száma nem csökkenthető a gyorsítótárazás és az időzített kihátrálás nem működik, majd fontolja meg a kulcsok felosztása több key vaults. Egyetlen előfizetés szolgáltatáskorlátja az egyes Key Vault-korlát 5-szerese. Ha 5-nél több key vaultot használ, érdemes több előfizetést használni. 

Részletes útmutatást, beleértve a korlátozások növelésére irányuló kérést, itt található: [Key Vault szabályozási útmutató](key-vault-ovw-throttling.md)


