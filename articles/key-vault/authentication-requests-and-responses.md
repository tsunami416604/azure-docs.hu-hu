---
title: Hitelesítés, kérelmek és válaszok
description: Hitelesítés az AD-hez a Key Vaulttal
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ca486768cf56059328801b1b4b1036bb8aeece8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081780"
---
# <a name="authentication-requests-and-responses"></a>Hitelesítés, kérelmek és válaszok

Az Azure Key Vault támogatja a JSON formátumú kérelmeket és válaszokat. Az Azure Key Vault kérelmeket a rendszer átirányítja egy érvényes Azure Key Vault URL-cím használatával HTTPS néhány URL-paraméter- és JSON-kódolású kérések és válaszok szervek.

Ez a témakör ismerteti az Azure Key Vault szolgáltatás-adatait. Azure REST-felületeihez, beleértve a hitelesítés/engedélyezés és a egy hozzáférési jogkivonat beszerzése az általános információkhoz lásd: [Azure REST API-referencia](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>Kérés URL-címe  
 Kulcskezelési műveletek használata a HTTP DELETE, GET, PATCH, PUT és HTTP POST és a titkosítási műveletek ellen a meglévő objektumok használata a HTTP POST. Ügyfelek, amelyek nem támogatják a HTTP-műveleteket is használhatnak HTTP POST használatával adja meg a kívánt műveletet; az X-HTTP-kérelem fejléce a szervezet normális esetben nem igénylő kérelmek tartalmaznia kell egy üres törzs HTTP POST, például amikor a POST használatát törlése helyett használatakor.  

 Dolgozunk az Azure Key vaultban objektumok, a következők példa URL-címek:  

- A Key Vault használja – TESTKEY nevű kulcs létrehozásához `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Be a Key Vault használata – IMPORTEDKEY nevű kulcs importálása `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- A Key Vault használja – MYSECRET nevű titkos kulcs beolvasásához `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Egy kivonatoló aláírásához egy kulccsal nevű TESTKEY a Key Vault használja – `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Az alábbiak szerint, mindig van egy Key Vaultot a kérés a szolgáltató  `https://{keyvault-name}.vault.azure.net/`  

  /Keys elérési mindig tárolt kulcsok, titkos kulcsok mindig /secrets útvonalon tárolja.  

## <a name="api-version"></a>API-verzió  
 Az Azure Key Vault szolgáltatás támogatja a protokoll versioning kompatibilitást biztosítanak a alsó szintű ügyfelek számára, bár nem minden képességek azon ügyfelek számára érhető el. Az ügyfeleknek kell használniuk a `api-version` lekérdezési karakterlánc paraméter a protokoll, amely nincs alapértelmezett érték nem támogatják a verzió megadása.  

 Az Azure Key Vault-protokollverziók hajtsa végre a sémát használ, {YYYY} számozása dátumot. {MM}. {DD} formátum.  

## <a name="request-body"></a>A kérelem törzse  
 A HTTP-specifikációnak megfelelően a GET műveletek egy kérelem törzse nem lehet, és a POST és PUT műveleteket kell rendelkeznie a kérelem törzsében. A törlési műveletek a szervezet nem kötelező HTTP.  

 Ha nincs másként jelezve a művelet leírását, a kérelem törzsében tartalomtípus kell application/json, és tartalmaznia kell egy szerializált JSON objektum megfelelő tartalomtípushoz.  

 Ha nincs másként jelezve a művelet leírását, az Accept-kérelem fejléce tartalmaznia kell application/json adathordozó-típusa.  

## <a name="response-body"></a>Választörzs  
 Ha másként nincs jelezve a művelet leírását, a válasz törzse tartalomtípus sikeres és sikertelen műveletek kell application/json, és a hibával kapcsolatos részletes információkat tartalmaz.  

## <a name="using-http-post"></a>HTTP POST használatával  
 Egyes ügyfelek nem lehet bizonyos HTTP-műveletek, például a PATCH és DELETE használhatják. Az Azure Key Vault támogatja a HTTP POST alternatívájaként ezek az ügyfelek feltéve, hogy az ügyfél a specifikus az eredeti HTTP-művelet az "X-HTTP-METÓDUS" fejlécet is tartalmazza. HTTP POST támogatása az egyes az ebben a dokumentumban meghatározott API-t kell jegyezni.  

## <a name="error-responses"></a>Hibaválaszok  
 Hibakezelés HTTP-állapotkódok fogja használni. Tipikus eredmények a következők:  

- 2xx – sikeres: Használja a normál működést. A válasz törzse fogja tartalmazni a várt eredmény  

- 3xx – átirányítási: A 304 "Nem módosította" Előfordulhat, hogy vissza kell adni egy feltételes GET teljesítéséhez. Egyéb 3xx kódok jelezheti a DNS-és elérési útja a jövőben használhatók.  

- 4xx – ügyfél-hiba: Hibás kérésekből, a hiányzó kulcsok, a szintaktikai hibákat, a érvénytelen paramétereket, a hitelesítési hibák használják, stb. A válasz törzse fogja tartalmazni a hibával kapcsolatos részletes magyarázatát.  

- 5XX – kiszolgálóhiba: Használja a belső hibákat. A válasz törzse összesített hibaadatokat fog tartalmazni.  

  A rendszer tervezték proxy vagy tűzfal mögé. Ezért egy ügyfél más hibakódok kaphat.  

  Az Azure Key Vault hibaadatok is a válasz törzsében adja vissza, ha probléma merül fel. A válasz törzse JSON formátumban, és nyilvánul meg:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Authentication  
 Minden kérést az Azure Key Vault kell hitelesíteni. Az Azure Key Vault támogatja az Azure Active Directory hozzáférési jogkivonatok, amelyek be lehet szerezni az OAuth2 használatával [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Az alkalmazás regisztrálása és használata az Azure Key Vault hitelesítésével további információkért lásd: [ügyfélalkalmazás regisztrálása az Azure AD-](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Hozzáférési jogkivonatok el kell küldeni a szolgáltatást a HTTP-engedélyezési fejléc használatával:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Ha a hozzáférési jogkivonat nem tartalmazza, vagy egy token nem fogadja el a szolgáltatást, HTTP 401-es hiba visszatér az ügyfélhez, és magukban foglalják a WWW-Authenticate fejléc, például:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 A WWW-Authenticate fejléc a paraméterek a következők:  

-   Hitelesítés: A cím az OAuth2 engedélyezési szolgáltatás, amely a kérelem hozzáférési jogkivonat beszerzésére is használható.  

-   erőforrás: Az engedélyezési kérésben használata az erőforrás neve.  

## <a name="see-also"></a>Lásd még:  
 [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
