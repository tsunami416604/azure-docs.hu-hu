---
title: Hitelesítés, a kérések és válaszok
description: Az AD-hez a Key vault hitelesítéséhez
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 94080fb124478a4b8e196e341c335ca32321ecdf
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="authentication-requests-and-responses"></a>Hitelesítés, a kérések és válaszok

Az Azure Key Vault támogatja a JSON formátumú kérelmeit és válaszait. Az Azure Key Vault kérelmek egy érvényes Azure Key Vault URL-cím használatára van irányítva, néhány URL-cím paraméterekkel rendelkező HTTPS és a JSON kódolású kérelem és válasz.

Ez a témakör ismerteti az Azure Key Vault szolgáltatás részletekről. Az Azure REST csatolókat, beleértve a hitelesítési/engedélyezési és a hozzáférési tokent, beszerzésére használatával általános információkhoz lásd: [Azure REST API-referencia](https://docs.microsoft.com/rest/api/).

## <a name="request-url"></a>Lekérdezés URL-címe  
 Kulcskezelés műveletei pedig HTTP DELETE, GET, javítás, PUT és a HTTP POST és titkosítási műveleteket a meglévő kulcs objektumokon használja a HTTP POST. Ügyfelek, amelyek nem támogatják a HTTP-műveleteket is használhatnak HTTP POST az X-HTTP-kérelem fejléce használatával adja meg a kívánt művelet; általában nem kell a törzs kérelmek tartalmaznia kell egy üres szövegtörzzsel, például amikor a FELADÁS egy vagy több törlése helyett a HTTP POST használatakor.  

 Az Azure Key Vault objektumok használatához a következők példa URL-címek:  

-   A Key Vault-use - TESTKEY nevű kulcs létrehozásához `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

-   A Key Vault használatba - IMPORTEDKEY nevű kulcs importálása `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

-   A Key Vault-use - MYSECRET nevű titkos kulcs segítségével `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

-   Egy kivonatoló aláírásához kulccsal nevű TESTKEY a Key Vault-use- `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

 A szolgáltató a kulcstároló a kérelmek az alábbiak szerint, mindig van  `https://{keyvault-name}.vault.azure.net/`  

 Kulcsokat mindig tárolja a /keys elérési úton található, a titkos kulcsok mindig tárolja a /secrets elérési úton található.  

## <a name="api-version"></a>API-verzió  
 Az Azure Key Vault szolgáltatás protokoll versioning arra, hogy alacsony szintű ügyfelek is kompatibilisek támogatja, de nem minden képességek ezek az ügyfelek számára elérhető lesz. Az ügyfeleknek kell használniuk a `api-version` lekérdezési karakterlánc adhatja meg a protokoll használatát támogatja, nincs alapértelmezett verzióját.  

 Az Azure Key Vault protokollverziója számozási {éééé} használatával dátum kövesse. {MM}. {NN} formátumban.  

## <a name="request-body"></a>A kérelem törzse  
 A HTTP-specifikáció szerint GET műveleteknek nem lehet egy kérelemtörzset, és a POST és a PUT műveletek rendelkeznie kell egy kérelemtörzset. A DELETE művelethez törzs nem kötelező HTTP.  

 A művelet Leírás, hacsak a kérelem törzsében tartalomtípus lehet az application/json, és tartalmaznia kell egy szerializált JSON objektum szabványos tartalomtípushoz.  

 A művelet Leírás, hacsak az elfogadás kérelem fejléce tartalmaznia kell az application/json adathordozó-típus.  

## <a name="response-body"></a>Választörzs  
 A művelet Leírás, hacsak a szervezet tartalomtípusú választ sikeres és sikertelen műveletek lesz az application/json, és a hibával kapcsolatos részletes információkat tartalmaz.  

## <a name="using-http-post"></a>A HTTP POST használatával  
 Egyes ügyfelek előfordulhat, hogy nem fogja tudni használni a bizonyos HTTP-műveletek, például a javítás vagy törlése. Az Azure Key Vault támogatja a HTTP POST alternatívájaként ezeknél a klienseknél feltéve, hogy az ügyfél is tartalmaz az "X-HTTP-METHOD" fejlécet specifikus az eredeti HTTP-utasítás. Támogatja a HTTP POST minden a dokumentumban meghatározott API kell jegyezni.  

## <a name="error-responses"></a>Hibaüzenetek  
 Hibakezelés HTTP-állapotkódok fogja használni. Tipikus eredmények a következők:  

-   2xx – sikeres: normál művelethez használt. A választörzs a várt eredményt fog tartalmazni.  

-   3xx – átirányítás: feltételes GET teljesítéséhez visszaadott a 304 "nem módosított". Egyéb 3xx kód is használható a jövőben jelezheti a DNS-és elérési útja.  

-   4xx – ügyfél-hiba: hibás kérésekből, a hiányzó kulcsok, a szintaktikai hibákat, a érvénytelen paraméterek, a hitelesítési hibák használt, stb. Az adott válasz törzsének hibával kapcsolatos részletes leírását tartalmazza.  

-   5XX – kiszolgálóhiba: belső kiszolgálóhibákat használatos. Az adott válasz törzsének összesített hibaadatokat fog tartalmazni.  

 A rendszer tervezték a proxy vagy tűzfal mögé. Egy ügyfél fordulhat elő, ezért más hibakódok.  

 Az Azure Key Vault probléma esetén is visszaadása hibainformációk az adott válasz törzse. Az adott válasz törzsének JSON formátumú, és formájában történik:  

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
 Minden kérésnél az Azure Key Vault kell hitelesíteni. Az Azure Key Vault támogatja az Azure Active Directory hozzáférési jogkivonatok használatával OAuth2 kapható [[RFC6749](http://tools.ietf.org/html/rfc6749)]. 
 
 Az alkalmazás regisztrálása és az Azure Key Vault használandó hitelesítésével kapcsolatos további információkért lásd: [regisztrálni az ügyfélalkalmazást Azure AD-val](https://docs.microsoft.com/rest/api/index#register-your-client-application-with-azure-ad).
 
 Hozzáférési jogkivonatok el kell küldeni a szolgáltatás használatával a HTTP-hitelesítési fejlécéhez:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Egy hozzáférési jogkivonat nincs megadva, vagy amikor a jogkivonat nem fogadja el a szolgáltatást, HTTP 401-es hiba visszatér az ügyfélhez és a továbbiakban a WWW-Authenticate fejléc például:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 A WWW-Authenticate fejlécet a paraméterek a következők:  

-   engedélyezési: A címet felhasználhatja a kérelem egy hozzáférési jogkivonat beszerzése az OAuth2 engedélyezési szolgáltatás.  

-   erőforrás: a hitelesítési kérelem használandó erőforrás nevét.  

## <a name="see-also"></a>Lásd még:  
 [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
