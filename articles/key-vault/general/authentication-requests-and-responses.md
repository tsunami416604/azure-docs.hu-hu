---
title: Hitelesítés, kérelmek és válaszok
description: Hitelesítés a AD-ben a Key Vault használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 33e3bc13e67e268b82bf517033b4b1c7c51c361f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430889"
---
# <a name="authentication-requests-and-responses"></a>Hitelesítés, kérelmek és válaszok

Az Azure Key Vault támogatja a JSON formátumú kérelmeket és válaszokat. Az Azure Key Vault-ba irányuló kérelmek egy érvényes Azure Key Vault URL-címre irányulnak, https használatával, néhány URL-paraméterrel és JSON-kódolású kérés- és válaszszervekkel.

Ez a témakör az Azure Key Vault szolgáltatás sajátosságait ismerteti. Az Azure REST-felületek használatával kapcsolatos általános tudnivalókat, beleértve a hitelesítést/engedélyezést és a hozzáférési jogkivonat beszerzésének módját, az [Azure REST API-referencia című témakörben talál.](https://docs.microsoft.com/rest/api/azure)

## <a name="request-url"></a>Kérés URL-címe  
 A kulcskezelési műveletek http delete, GET, PATCH, PUT és HTTP POST és kriptográfiai műveleteket használnak a meglévő kulcsobjektumokon, amelyek a HTTP POST protokollt használják. Azok az ügyfelek, amelyek nem támogatnak bizonyos HTTP-műveleteket, az X-HTTP-REQUEST fejléc használatával is használhatják a HTTP POST-ot a tervezett művelet megadásához; azoknak a kéréseknek, amelyek általában nem igényelnek törzset, üres törzset kell tartalmazniuk a HTTP POST használatakor, például a POST használatakor a DELETE helyett.  

 Az Azure Key Vault objektumaival való munkához a következő példa URL-címek találhatók:  

- TESTKEY nevű kulcs létrehozása key vault-használatban -`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- IMPORTÁLTKEY nevű kulcs importálása key vault-használatba -`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Ahhoz, hogy egy titkos nevű MYSECRET egy Key Vault használata -`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Kivonatírás írása egy TESTKEY nevű kulcs használatával key vault-használatesetén -`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  A Key Vaultba irányuló kérelem reklamációja mindig a következő:`https://{keyvault-name}.vault.azure.net/`  

  A kulcsok mindig a /keys elérési út alatt tárolódnak, a titkos kulcsok mindig a /secrets elérési út alatt tárolódnak.  

## <a name="api-version"></a>API-verzió  
 Az Azure Key Vault Service támogatja a protokoll verziószámozás, hogy kompatibilitást biztosítson az eltérő szintű ügyfelekkel, bár nem minden képesség lesz elérhető az ügyfelek számára. Az ügyfeleknek `api-version` a lekérdezési karakterlánc paramétert kell használniuk az általuk támogatott protokoll verziójának megadásához, mivel nincs alapértelmezett.  

 Az Azure Key Vault protokollverziói egy {YYYY} használatával dátumszámozási sémát követnek. {MM}. {DD} formátumban.  

## <a name="request-body"></a>A kérelem törzse  
 A HTTP-specifikáció szerint a GET-műveleteknek NEM lehet kéréstörzsük, a POST és a PUT műveleteknek pedig kéréstörzsnek kell rendelkezniük. A DELETE műveletek törzse nem kötelező http-ben.  

 Ha a művelet leírása másként nem rendelkezik, a kérelemtörzs-tartalomtípusnak alkalmazás/json nak kell lennie, és tartalmaznia kell egy tartalomtípusnak megfelelő szerializált JSON-objektumot.  

 Ha a művelet leírása másként nem rendelkezik, az Elfogadáskérelem fejlécének tartalmaznia kell az alkalmazás/json adathordozó típusát.  

## <a name="response-body"></a>Válasz törzse  
 Ha csak a művelet leírása másként nem rendelkezik, a sikeres és a sikertelen műveletek választörzs-tartalomtípusa alkalmazás/json lesz, és részletes hibainformációkat tartalmaz.  

## <a name="using-http-post"></a>A HTTP POST használata  
 Előfordulhat, hogy egyes ügyfelek nem tudnak bizonyos HTTP-műveleteket használni, például a PATCH vagy a DELETE. Az Azure Key Vault támogatja a HTTP POST-ot alternatívaként ezen ügyfelek számára, feltéve, hogy az ügyfél tartalmazza az "X-HTTP-METHOD" fejlécet az eredeti HTTP-művelethez. A HTTP POST támogatása a dokumentumban definiált api-k mindegyikéhez ismert.  

## <a name="error-responses"></a>Hibaválaszok  
 A hibakezelés HTTP-állapotkódokat fog használni. A tipikus eredmények a következők:  

- 2xx – Siker: Normál működéshez használható. A válaszszerv a várt eredményt fogja tartalmazni  

- 3xx - Átirányítás: A 304 "Nem módosított" lehet vissza teljesíteni a feltételes GET. A jövőben más 3xx kódok is használhatók a DNS és az elérési út változásainak jelzésére.  

- 4xx - Ügyfélhiba: Hibás kérésekhez, hiányzó kulcsokhoz, szintaktikai hibákhoz, érvénytelen paraméterekhez, hitelesítési hibákhoz stb. A választörzs részletes hibamagyarázatot fog tartalmazni.  

- 5xx – Kiszolgálóhiba: Belső kiszolgálóhibákhoz használható. A választörzs összegzett hibainformációkat fog tartalmazni.  

  A rendszer úgy van kialakítva, hogy proxy vagy tűzfal mögött működjön. Ezért előfordulhat, hogy az ügyfél más hibakódokat is kap.  

  Az Azure Key Vault is a választörzsben hibaüzenetet ad vissza, ha probléma merül fel. A választörzs JSON formátumú, és a következő formában érhető el:  

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

## <a name="authentication"></a>Hitelesítés  
 Az Azure Key Vault minden kérelmet hitelesíteni kell. Az Azure Key Vault támogatja az Azure Active Directory hozzáférési jogkivonatokat, amelyek az OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)] használatával szerezhetők be. 
 
 Az alkalmazás regisztrálásáról és az Azure Key Vault használatának hitelesítéséről az [Ügyfélalkalmazás regisztrálása az Azure AD-vel című](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad)témakörben talál további információt.
 
 A hozzáférési jogkivonatokat a HTTP-engedélyezési fejléc használatával kell elküldeni a szolgáltatásnak:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Ha nem ad meg hozzáférési jogkivonatot, vagy ha a szolgáltatás nem fogad el jogkivonatot, a rendszer http 401-es hibát ad vissza az ügyfélnek, és tartalmazza a WWW-Authenticate fejlécet, például:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 A WWW-Authenticate fejléc paraméterei a következők:  

-   engedélyezés: Az OAuth2 engedélyezési szolgáltatás címe, amely a kérelem hez szükséges hozzáférési jogkivonat megszerzéséhez használható.  

-   erőforrás: Az engedélyezési kérelemben használandó erőforrás (`https://vault.azure.net`) neve.  

