---
title: Felügyelt identitások használata az Azure-erőforrások virtuális gépen a hozzáférési jogkivonat beszerzése
description: Részletes útmutatás és példák a felügyelt identitások az Azure-erőforrások virtuális gépeken az OAuth hozzáférési jogkivonat beszerzése.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 86830d8a13e4d83ff48bcf7e2f2dfac41d764718
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161424"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Felügyelt identitások használata az Azure-erőforrások egy Azure-beli virtuális gépen a hozzáférési jogkivonat beszerzése 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ez a cikk példákat különböző kód és a parancsfájl a token beszerzéséhez, valamint útmutatást nyújtanak, többek között az jogkivonat lejárati és HTTP-hibák kezelése fontos. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha azt tervezi, ez a cikk az Azure PowerShell-példák használata, ügyeljen arra, hogy telepítse a legújabb verzióját, [Azure PowerShell-lel](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Az összes kódot minta parancsfájl ebben a cikkben azt feltételezi, hogy a felügyelt identitások az Azure-erőforrások virtuális gépen fut az ügyfél. Az Azure Portalon a virtuális gépen "Kapcsolódás" funkció használatával távolról csatlakozzon a virtuális Géphez. Felügyelt identitások a virtuális gép Azure-erőforrások engedélyezésével kapcsolatos részletekért lásd: [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md), vagy az variant cikkekben (PowerShell, CLI, egy sablon vagy az Azure használatával SDK-T). 

> [!IMPORTANT]
> - Felügyelt identitások az Azure-erőforrásokhoz, a biztonsági határokat, hogy az erőforrás a használatos. Összes kód/parancsfájl egy virtuális gépen futó igényelheti és elérhető, a felügyelt identitások-jogkivonatok. 

## <a name="overview"></a>Áttekintés

Egy ügyfélalkalmazás is kérhető az Azure-erőforrások felügyelt identitások [csak az alkalmazásra vonatkozó hozzáférési jogkivonat](../develop/developer-glossary.md#access-token) egy adott erőforráshoz való hozzáférést. A jogkivonat [az Azure-erőforrások egyszerű szolgáltatás a felügyelt identitások alapján](overview.md#how-does-it-work). Emiatt a hiba esetén nem kell regisztrálja magát az ügyfél saját egyszerű szolgáltatás a hozzáférési jogkivonat beszerzése. A token használhatók a tulajdonosi jogkivonattal [szolgáltatások közötti hívások igénylő ügyfél-hitelesítő adatok](../develop/v1-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [HTTP-n keresztül egy token beszerzése](#get-a-token-using-http) | Az Azure-erőforrások felügyelt identitások protokoll részletei jogkivonat-végpont |
| [A .NET-hez a Microsoft.Azure.Services.AppAuthentication kódtár használatával egy token beszerzése](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | A Microsoft.Azure.Services.AppAuthentication kódtár a .NET-kliens használatával – példa
| [C# használatával egy token beszerzése](#get-a-token-using-c) | Felügyelt identitások használatával az Azure-erőforrások REST-végpont egy C# ügyfél – példa |
| [Go használatával egy token beszerzése](#get-a-token-using-go) | Felügyelt identitások használatával az Azure-erőforrások REST-végpont egy Go-ügyfél – példa |
| [Azure PowerShell-lel egy token beszerzése](#get-a-token-using-azure-powershell) | Felügyelt identitások használatával az Azure-erőforrások REST-végpont egy PowerShell-ügyfél – példa |
| [A CURL használatával egy token beszerzése](#get-a-token-using-curl) | Felügyelt identitások használatával az Azure-erőforrások REST-végpont egy Bash vagy a CURL ügyfél – példa |
| [Token-gyorsítótárazási kezelése](#handling-token-caching) | Kezelési útmutató lejárt hozzáférési jogkivonatok |
| [Hibakezelés](#error-handling) | Útmutató a felügyelt identitások az Azure-erőforrások jogkivonat-végpont által visszaadott HTTP-hibák kezelése |
| [Erőforrás-azonosítókat megtalálhatja az Azure-szolgáltatásokhoz](#resource-ids-for-azure-services) | A támogatott Azure-szolgáltatások erőforrás-azonosítók beszerzése |

## <a name="get-a-token-using-http"></a>HTTP-n keresztül egy token beszerzése 

Az alapvető kezelőfelület-hozzáférési token beszerzése a REST, így elérhetők bármely ügyfél alkalmazás számára a virtuális gépen, amely megkönnyíti a HTTP REST-hívások alapján történik. Ez hasonlít az Azure AD-programozási modellt, kivéve az ügyfél használ egy végpontot a virtuális gépen (és az Azure AD-végpont).

Az Azure példány metaadat szolgáltatás (IMDS) végpont használatával mintakérelem *(ajánlott)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-műveletet, amely azt jelzi, hogy szeretne-adatokat lekérni a végpontot. Ebben az esetben az OAuth hozzáférési tokent. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | A felügyelt identitásokból Instance Metadata szolgáltatás az Azure-erőforrások végpont. |
| `api-version`  | A lekérdezési sztring paramétereként, jelezve a IMDS végpont az API-verzió. API-verziót használja `2018-02-01` vagy nagyobb. |
| `resource` | A lekérdezési sztring paramétereként, az Alkalmazásazonosító URI a célként megadott erőforrás-jelző. Emellett megjelenik a `aud` (célközönség) jogcím a kiállított jogkivonat. Ebben a példában az Azure Resource Manager eléréséhez tokent kér az Alkalmazásazonosító URI-t, amelynek https://management.azure.com/. |
| `Metadata` | Egy HTTP kérelem fejléce, kötelező mező által felügyelt identitásokat az Azure-erőforrások, a kiszolgáló kiszolgálóoldali kérelmet hamisítására (SSRF) támadások elleni megoldás. Ezt az értéket állítsa "true", csupa kisbetű szerepel. |
| `object_id` | (Nem kötelező) A lekérdezési sztring paramétereként, a object_id az felügyelt identitás szeretné token jelzi. Szükséges, ha a virtuális gépen több felhasználó által hozzárendelt felügyelt identitást.|
| `client_id` | (Nem kötelező) A lekérdezési sztring paramétereként, a client_id az felügyelt identitás szeretné token jelzi. Szükséges, ha a virtuális gépen több felhasználó által hozzárendelt felügyelt identitást.|

A felügyelt identitások használatával az Azure-erőforrások Virtuálisgép-bővítmény végpont mintakérelem *(elavult. január 2019 a tervezett)*:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-műveletet, amely azt jelzi, hogy szeretne-adatokat lekérni a végpontot. Ebben az esetben az OAuth hozzáférési tokent. | 
| `http://localhost:50342/oauth2/token` | A felügyelt identitások Azure-erőforrások végponton, ahol az alapértelmezett port 50342, és konfigurálható. |
| `resource` | A lekérdezési sztring paramétereként, az Alkalmazásazonosító URI a célként megadott erőforrás-jelző. Emellett megjelenik a `aud` (célközönség) jogcím a kiállított jogkivonat. Ebben a példában az Azure Resource Manager eléréséhez tokent kér az Alkalmazásazonosító URI-t, amelynek https://management.azure.com/. |
| `Metadata` | Egy HTTP kérelem fejléce, kötelező mező által felügyelt identitásokat az Azure-erőforrások, a kiszolgáló kiszolgálóoldali kérelmet hamisítására (SSRF) támadások elleni megoldás. Ezt az értéket állítsa "true", csupa kisbetű szerepel.|
| `object_id` | (Nem kötelező) A lekérdezési sztring paramétereként, a object_id az felügyelt identitás szeretné token jelzi. Szükséges, ha a virtuális gépen több felhasználó által hozzárendelt felügyelt identitást.|
| `client_id` | (Nem kötelező) A lekérdezési sztring paramétereként, a client_id az felügyelt identitás szeretné token jelzi. Szükséges, ha a virtuális gépen több felhasználó által hozzárendelt felügyelt identitást.|


Mintaválasz:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elem | Leírás |
| ------- | ----------- |
| `access_token` | A kért hozzáférési jogkivonatot. Egy biztonságos REST API hívásakor a token be van ágyazva a `Authorization` kérelem fejléce mező "tulajdonosi" jogkivonattal, így az API-t a hitelesítés a hívó. | 
| `refresh_token` | Nem használja a felügyelt identitások az Azure-erőforrásokhoz. |
| `expires_in` | A hozzáférési jogkivonat továbbra is érvényes, mielőtt lejár, a kiállítási idején másodpercek számát. Kiadás időpontja a jogkivonatban található `iat` jogcím. |
| `expires_on` | Az időtartomány, ha a hozzáférési jogkivonat lejár. A dátum jelenik meg a másodpercek számát "1970-01-01T0:0:0Z (UTC)" (felel meg a token `exp` jogcím). |
| `not_before` | Az időtartomány, ha a hozzáférési jogkivonat érvénybe lép, és elfogadható. A dátum jelenik meg a másodpercek számát "1970-01-01T0:0:0Z (UTC)" (felel meg a token `nbf` jogcím). |
| `resource` | Az erőforrás a hozzáférési jogkivonatot a kért, mely megfelel a `resource` lekérdezési karakterlánc paraméter a kérelem. |
| `token_type` | A jogkivonatot, amely a "Tulajdonos" hozzáférési jogkivonatot, ami azt jelenti, hogy az erőforrás segítségével hozzáférést biztosíthat a token a tulajdonosi típusa. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>A .NET-hez a Microsoft.Azure.Services.AppAuthentication kódtár használatával egy token beszerzése

A .NET-alkalmazások és funkciók a legegyszerűbb módja a felügyelt identitások az Azure-erőforrások használata a a Microsoft.Azure.Services.AppAuthentication csomag keresztül történik. Ebben a könyvtárban is lehetővé teszi, hogy a kód a fejlesztői gépen, a felhasználói fiókkal a Visual Studióban a helyi tesztelése az [Azure CLI-vel](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), vagy az Active Directory beépített hitelesítést. Ebben a könyvtárban a helyi fejlesztési lehetőségek, lásd: [Microsoft.Azure.Services.AppAuthentication hivatkozás]. Ez a szakasz bemutatja, hogyan első lépések a kódtárat a programkódba.

1. Adja hozzá hivatkozásokat az [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-csomagok az alkalmazáshoz.

2.  Az alábbi kód hozzáadása az alkalmazáshoz:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Microsoft.Azure.Services.AppAuthentication és teszi elérhetővé a műveletek kapcsolatos további információkért tekintse meg a [Microsoft.Azure.Services.AppAuthentication referencia](/azure/key-vault/service-to-service-authentication) és a [App Service-ben és a KeyVault által felügyelt Azure-erőforrások .NET minta identitásainak](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>C# használatával egy token beszerzése

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>Go használatával egy token beszerzése

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Azure PowerShell-lel egy token beszerzése

A következő példa bemutatja a felügyelt identitásokból használata az Azure-erőforrások PowerShell-ügyfél és REST-végpont:

1. Hozzáférési jogkivonat beszerzése.
2. A hozzáférési jogkivonat segítségével egy Azure Resource Manager REST API-t hívja meg és a virtuális gép adatainak beolvasása. Ügyeljen arra, hogy az előfizetés-azonosító, erőforráscsoport-nevet és a virtuális gép neve helyettesítse `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, és `<VM-NAME>`, illetve.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Például hogyan elemezhető a hozzáférési jogkivonatot a válaszból:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>A CURL használatával egy token beszerzése

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Például hogyan elemezhető a hozzáférési jogkivonatot a válaszból:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Token-gyorsítótárazási

Miközben az Azure-erőforrások alrendszer használt (IMDS/felügyelt identitások az Azure-erőforrások Virtuálisgép-bővítmény) a felügyelt identitásokból gyorsítótár jogkivonatok, azt is javasoljuk megvalósításához a token-gyorsítótárazási a kódban. Ennek eredményeképpen a forgatókönyvek, ahol az erőforrás azt jelzi, hogy a jogkivonat lejárt kell előkészítése. 

Az Azure AD az átvitel közbeni hívások többletköltséggel csak ha:
- gyorsítótár-tévesztés akkor fordul elő, nem az Azure-erőforrások alrendszer gyorsítótár a felügyelt identitásokból jogkivonat miatt
- a gyorsítótárazott jogkivonat lejárt

## <a name="error-handling"></a>Hibakezelés

Az Azure-erőforrások végpont jelek hibák via HTTP-válasz üzenetfejlécének állapot kód mezőjének 4xx vagy 5XX kódú hibaként felügyelt identitások:

| Állapotkód | A hiba oka | Kezelése |
| ----------- | ------------ | ------------- |
| 404 nem található. | IMDS végpont frissítése folyamatban van. | Próbálkozzon újra Expontential leállítást. Tekintse meg az alábbi útmutatást. |
| 429 túl sok kérelmet. |  Elérte a IMDS sávszélesség-szabályozási korlátot. | Ismételje meg az exponenciális visszatartással. Tekintse meg az alábbi útmutatást. |
| 4xx hiba történt a kérelem. | Egy vagy több kérelem paraméter helytelen volt. | Nem próbálja meg újra.  Vizsgálja meg a hibaüzenet részleteiben talál további információt.  4xx olyan tervezési idejű hibákat tartalmaznak.|
| 5XX átmeneti hiba szolgáltatásból. | A felügyelt identitások Azure-erőforrások alrendszer vagy az Azure Active Directory egy átmeneti hibát adott vissza. | Már biztonságosan legalább 1 másodperc várakozás után próbálkozzon újra.  Ha túl gyorsan vagy túl gyakran újbóli IMDS és/vagy az Azure AD előfordulhat, hogy hibaüzenetet ad vissza arány korlát (429-es).|
| timeout | IMDS végpont frissítése folyamatban van. | Próbálkozzon újra Expontential leállítást. Tekintse meg az alábbi útmutatást. |

Ha hiba történik, a megfelelő HTTP-válasz törzsében JSON az a hiba részletes adatait tartalmazza:

| Elem | Leírás |
| ------- | ----------- |
| error   | Hiba azonosítója. |
| error_description | Hiba részletes leírását. **Hiba leírása bármikor módosíthatja. Ne írja ki a kódot, amely a hiba leírása értékei alapján ágak.**|

### <a name="http-response-reference"></a>HTTP-válasz referencia

Ez a szakasz a lehetséges hibaválaszok dokumentumok. A "200 OK" állapota sikeres válasz, és a hozzáférési jogkivonatot a válasz törzse JSON-t, a access_token elem szerepel.

| Állapotkód | Hiba | Hibaleírás | Megoldás |
| ----------- | ----- | ----------------- | -------- |
| 400 Hibás kérés | invalid_resource | AADSTS50001: Nevű alkalmazás *\<URI\>* nem található az nevű bérlőben  *\<TENANT-ID\>*. Ez akkor fordulhat elő, ha az alkalmazás még nem a bérlő rendszergazdája telepítette vagy nem fogadta el a bérlő a egyetlen felhasználója sem. Előfordulhat, hogy a hitelesítési kérést részére elküldött rossz bérlőhöz. \ | (Csak Linux) |
| 400 Hibás kérés | bad_request_102 | Nincs megadva a szükséges metaadat-fejléc | Vagy a `Metadata` kérelem fejléce mező hiányzik a kérelemből, vagy helytelenül van formázva. Az értéket kell megadni, `true`, csupa kisbetű szerepel. A "mintakérelem" jelenik meg a [előző fejezet REST](#rest) példaként.|
| 401-es nem engedélyezett | unknown_source | Ismeretlen forrásból származó  *\<URI\>* | Győződjön meg arról, hogy a HTTP GET kérés URI formátuma helytelen. A `scheme:host/resource-path` részét kell megadni, `http://localhost:50342/oauth2/token`. A "mintakérelem" jelenik meg a [előző fejezet REST](#rest) példaként.|
|           | invalid_request | A kérelem hiányzik egy kötelező paraméter, tartalmaz egy érvénytelen paraméterérték, egy paraméter egynél többször tartalmazza vagy egyéb helytelen formátumú. |  |
|           | unauthorized_client | Az ügyfél nem jogosult ezzel a módszerrel hozzáférési jogkivonat kérése. | Oka egy kérelmet, amely nem a helyi visszacsatolási hívja a bővítményt, vagy egy virtuális gépen, amely nem rendelkezik felügyelt identitások az Azure-erőforrások megfelelően konfigurálva. Lásd: [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége. |
|           | ACCESS_DENIED | Az erőforrás tulajdonosa vagy az engedélyezési kiszolgáló elutasította a kérést. |  |
|           | unsupported_response_type | Az engedélyezési kiszolgáló nem támogatja ezt a módszert használja hozzáférési jogkivonat beszerzése. |  |
|           | invalid_scope | A kért hatóköre érvénytelen, ismeretlen vagy hibás formátumú. |  |
| 500 belső kiszolgálóhiba | ismeretlen | Nem sikerült beolvasni a jogkivonatot az Active Directoryból. További részletekért lásd: a naplók  *\<fájl elérési útja\>* | Győződjön meg arról, hogy a felügyelt identitások az Azure-erőforrások a virtuális gépen engedélyezve van-e. Lásd: [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége.<br><br>Emellett győződjön meg arról, hogy a HTTP GET kérés URI azonosító formátuma megfelelő, különösen az erőforrás-URI-t a lekérdezési karakterláncban megadott. A "mintakérelem" jelenik meg a [előző fejezet REST](#rest) egy vonatkozó példáért vagy [Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés](services-support-msi.md) szolgáltatások és a megfelelő erőforrás-azonosítók listáját.

## <a name="retry-guidance"></a>Újrapróbálkozásokra vonatkozó útmutató 

Javasoljuk, hogy ismételje meg. Ha a 404-es, a 429-es vagy 5xx hibakód (lásd: [hibakezelés](#error-handling) fent).

Szabályozási korlátok vonatkoznak a IMDS végpontra indított hívások száma. A szabályozási küszöbérték túllépésekor IMDS végpont korlátozza a további kéréseket, amíg a szabályozás van érvényben. Ebben az időszakban, a IMDS végpont 429-es HTTP-állapotkódot adja vissza ("túl sok kérés"), és a kérelmek sikertelenek. 

Próbálkozzon újra javasoljuk a következő stratégia: 

| **Újrapróbálkozási stratégia** | **Beállítások** | **Értékek** | **Működési elv** |
| --- | --- | --- | --- |
|ExponentialBackoff |Ismétlések száma<br />Visszatartás (min.)<br />Visszatartás (max.)<br />Visszatartás (változás)<br />Első gyors újrapróbálkozás |5<br />0 másodperc<br />60 másodperc<br />2 másodperc<br />false |1. kísérlet – 0 mp. késleltetés<br />2. kísérlet – kb. 2 mp. késleltetés<br />3. kísérlet – kb. 6 mp. késleltetés<br />4. kísérlet – kb. 14 mp. késleltetés<br />5. kísérlet – kb. 30 mp. késleltetés |

## <a name="resource-ids-for-azure-services"></a>Erőforrás-azonosítókat megtalálhatja az Azure-szolgáltatásokhoz

Lásd: [Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés](services-support-msi.md) , amelyek támogatják az Azure ad-ben, és az Azure-erőforrások és a megfelelő erőforrás-azonosítók felügyelt identitások teszteltük erőforrások listáját.


## <a name="next-steps"></a>További lépések

- Engedélyezheti a felügyelt identitások az Azure-erőforrások egy Azure-beli virtuális gépen [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md).








