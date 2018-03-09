---
title: "Olyan hozzáférési jogkivonatot szerezni egy Azure virtuális gép felügyelt szolgáltatás identitás használatával"
description: "Lépésenkénti útmutatás és példák a használatát, az Azure virtuális gép MSI szerezzen be egy OAuth hozzáférési tokent."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 0aec1ed570ba688288be4e7fcd9b74513234ea3d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Egy Azure virtuális gép felügyelt szolgáltatás Identity (MSI) használata a token beszerzése 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk példákat különböző kód és parancsfájl-token beszerzése, valamint útmutatást, például a jogkivonat lejáratáról és HTTP-hibák kezelése a fontos kérdésekben.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha azt tervezi, a cikkben az Azure PowerShell-példák használni, ügyeljen arra, hogy telepítse a legújabb verzióját [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Minden minta kódot, a parancsfájl a cikkben azt feltételezi, hogy az ügyfél egy MSI-kompatibilis virtuális gépen futó. A virtuális gép "Csatlakozás" szolgáltatást használja az Azure-portálon távolról csatlakozni a virtuális Gépet. A virtuális gép MSI engedélyezésével kapcsolatos részletekért lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](qs-configure-portal-windows-vm.md), vagy a variant cikkekben (a PowerShell, CLI, sablon vagy egy Azure SDK használatával). 

## <a name="overview"></a>Áttekintés

Egy ügyfélalkalmazás kérhet egy olyan MSI Csomaghoz [csak alkalmazás-hozzáférési jogkivonat](../develop/active-directory-dev-glossary.md#access-token) egy adott erőforráshoz való hozzáférést illetően. A jogkivonat [az MSI-szolgáltatás egyszerű alapján](overview.md#how-does-it-work). Így nincs szükség az regisztrálja magát az ügyfél a saját egyszerű szolgáltatásnév a hozzáférési token beszerzése. A lexikális elem egy tulajdonosi jogkivonatot a használhatók [szolgáltatások közötti hívások igénylő ügyfél hitelesítő adatait](../develop/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Szolgáltatáshitelesítést egy token HTTP-n keresztül](#get-a-token-using-http) | Az MSI-jogkivonat végpontjához protokoll részletei |
| [C# használatával jogkivonat beolvasása](#get-a-token-using-c) | Az MSI REST-végpont egy C# ügyfél használata – példa |
| [Szolgáltatáshitelesítést egy token használatával nyissa meg](#get-a-token-using-go) | Az MSI REST-végpont Ugrás ügyfélről használatának példája |
| [Az Azure PowerShell jogkivonat beolvasása](#get-a-token-using-azure-powershell) | Az MSI REST-végpont egy PowerShell-ügyfél használata – példa |
| [Szolgáltatáshitelesítést egy token használata CURL használatával](#get-a-token-using-curl) | Az MSI REST-végpont a Bash/CURL ügyfélben használatának példája |
| [Kezelési jogkivonat lejáratáról](#handling-token-expiration) | Útmutató a lejárt hozzáférési jogkivonatok kezelése |
| [Hibakezelés](#error-handling) | Útmutató a token MSI-végpont által visszaadott HTTP-hibák kezelése |
| [Erőforrás-azonosítók az Azure-szolgáltatások](#resource-ids-for-azure-services) | Erőforrás-azonosítók támogatott Azure-szolgáltatásokat az beszerzése |

## <a name="get-a-token-using-http"></a>Szolgáltatáshitelesítést egy token HTTP-n keresztül 

Az alapvető kezelőfelület egy hozzáférési jogkivonat beszerzése REST, így elérhető bármely ügyfél alkalmazás számára, amelyekkel HTTP REST-hívások a virtuális gépen alapul. Ez az az Azure AD-programozási modell hasonló, kivéve az ügyfél használja a localhost végpont a virtuális gépen (és az Azure AD-végpont).

Mintakérelem:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-műveletet, amely azt jelzi, hogy szeretne lekérdezni a végpont adatait. Ebben az esetben az OAuth hozzáférési tokent. | 
| `http://localhost:50342/oauth2/token` | Az MSI végpont, ahol 50342 az alapértelmezett port és konfigurálható. |
| `resource` | A lekérdezési karakterlánc paraméterként, az App ID URI a célerőforrás jelző. Emellett megjelenik a `aud` (célközönség) jogcím a kiállított jogkivonat. Ez a példa kérelmek rendelkezik egy App ID URI https://management.azure.com/ az Azure Resource Manager hozzáférési jogkivonatot. |
| `Metadata` | Egy HTTP kérelem fejlécmező, mint a kiszolgáló oldalán kérelem hamisítására (SSRF) támadások elleni megoldás MSI szükséges. Ez az érték "true", az összes kisbetű értékre kell állítani.

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
| `access_token` | A kért hozzáférési jogkivonat. Egy védett REST API felület meghívásakor, a rendszer beágyazza a a jogkivonatot a `Authorization` kérelem fejlécmező a "tulajdonos" jogkivonatként, amely lehetővé teszi az API-t a hitelesítéshez a hívó. | 
| `refresh_token` | MSI-fájl nem használja. |
| `expires_in` | A hozzáférési jogkivonat továbbra is érvényes, mielőtt lejár, a kiállítási megjelenésétől másodpercek számát. Kiállítási idején a jogkivonat található `iat` jogcímek. |
| `expires_on` | A timespan, ha a hozzáférési jogkivonat lejár. A dátum jelzi a másodpercek száma "1970-01-01T0:0:0Z UTC" (a token megfelel `exp` jogcím). |
| `not_before` | A timespan, ha a hozzáférési jogkivonat lép érvénybe, és elfogadható. A dátum jelzi a másodpercek száma "1970-01-01T0:0:0Z UTC" (a token megfelel `nbf` jogcím). |
| `resource` | Az erőforrás, a hozzáférési jogkivonat szükséges, mely megfelel a `resource` lekérdezési karakterlánc a kérelem. |
| `token_type` | A jogkivonatot, amely a "Tulajdonos" hozzáférési jogkivonatot, amely azt jelenti, hogy az erőforrás hozzáférést biztosíthat a token tulajdonosi típusa. |

## <a name="get-a-token-using-c"></a>C# használatával jogkivonat beolvasása

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
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

## <a name="get-a-token-using-go"></a>Szolgáltatáshitelesítést egy token használatával nyissa meg

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
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://localhost:50342/oauth2/token")
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

    // Call MSI /token endpoint
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

## <a name="get-a-token-using-azure-powershell"></a>Az Azure PowerShell jogkivonat beolvasása

A következő példa bemutatja, hogyan használhatja a PowerShell ügyfélszámítógépről MSI REST-végpont:

1. Szerezzen be egy hozzáférési jogkivonatot.
2. A hozzáférési jogkivonat segítségével hívja az Azure Resource Manager REST API-k és a virtuális gép adatainak beolvasása. Ügyeljen arra, hogy az előfizetés-azonosító, erőforráscsoport-név és a virtuális gép neve helyettesítő `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, és `<VM-NAME>`, illetve.

```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Szolgáltatáshitelesítést egy token használata CURL használatával

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Kezelési jogkivonat lejáratáról

A helyi MSI alrendszer gyorsítótárazza a jogkivonatokat. Ezért hívása, amilyen gyakran csak van lehetősége, és az Azure AD egy a tömörített hívás eredménye csak akkor, ha:
- a gyorsítótár-tévesztései miatt nem jogkivonat a gyorsítótárban történik
- a jogkivonat érvényessége lejárt

Ha a jogkivonat gyorsítótárba helyezi a kódban, helyzetek kezelésére, ahol az erőforrás azt jelzi, hogy a jogkivonat lejárt felkészültnek kell lennie.

## <a name="error-handling"></a>Hibakezelés 

Az MSI-végpont jelzi a hibákat a HTTP üzenet válaszfejléc, az állapot kód mezője révén 4xx vagy 5xx hibaként:

| Állapotkód | Hiba oka | Hogyan legyen kezelve |
| ----------- | ------------ | ------------- |
| 4xx hiba történt a kérelem. | Egy vagy több a kérelemben szereplő paraméterek helytelen volt. | Nem próbálja meg újra.  Vizsgálja meg a hiba részleteiben talál további információt.  4xx olyan tervezési idejű hibákat tartalmaznak.|
| 5XX átmeneti hiba szolgáltatásból. | Az MSI-alrendszer vagy az Azure Active Directory átmeneti hibát adott vissza. | Biztonságos legalább 1 másodperc várakozás után ismételje meg a legyen.  Ha túl gyakran vagy túl gyorsan újra, az Azure AD előfordulhat, hogy hibaüzenetet egy arány korlátot (429).|

Ha hiba lép fel, a megfelelő HTTP-válasz törzsében a hiba részletes adatait a JSON-adatokat tartalmaz:

| Elem | Leírás |
| ------- | ----------- |
| error   | Hiba azonosítója. |
| error_description | Hiba részletes leírását. **Hiba leírása bármikor módosíthatja. Ne írja ki a kódot, amely ágak a hibaleírás értékei alapján.**|

### <a name="http-response-reference"></a>HTTP-válasz referencia

Ez a szakasz a lehetséges hibaválaszok dokumentumokat. A "200 OK" állapota a sikeres válasz, és a hozzáférési jogkivonat tartalmazza az adott válasz törzsének JSON, a access_token elemben.

| Állapotkód | Hiba | Hibaleírás | Megoldás |
| ----------- | ----- | ----------------- | -------- |
| 400 Hibás kérés | invalid_resource | AADSTS50001: Az alkalmazás nevű  *\<URI\>*  nem található a bérlő nevű  *\<TENANT-ID\>*. Ez akkor fordulhat elő, ha az alkalmazás nem lett telepítve a rendszergazda a bérlő által vagy a bérlő bármely felhasználó hozzájárulását. Előfordulhat, hogy elküldött a hitelesítési kérést a megfelelő bérlő számára. \ | (Csak Linux) |
| 400 Hibás kérés | bad_request_102 | Nincs megadva a szükséges metaadat-fejléccel | Vagy a `Metadata` kérelem mező hiányzik a kérelemből, vagy helytelenül van formázva. Az értéket kell megadni, `true`, az összes kisbetű. A "kérelemmintát" című része a [REST szakasz megelőző](#rest) példát.|
| 401 nem engedélyezett | unknown_source | Az ismeretlen forrásból  *\<URI\>* | Győződjön meg arról, hogy a HTTP GET kérelem URI-azonosítója helytelenül van formázva. A `scheme:host/resource-path` részét kell megadni, `http://localhost:50342/oauth2/token`. A "kérelemmintát" című része a [REST szakasz megelőző](#rest) példát.|
|           | invalid_request | A kérés egyik kötelező paraméter hiányzik, érvénytelen paramétert tartalmaz, egy paraméter egynél többször tartalmazza vagy egyéb rosszul megformázva. |  |
|           | unauthorized_client | Az ügyfél nem jogosult egy hozzáférési jogkivonatot: Ezzel a módszerrel igényelni. | Oka a kérelmeket, amelyek nem a helyi visszacsatolási hívni a bővítményt, vagy egy virtuális gépen, amely nem rendelkezik egy olyan MSI Csomaghoz, megfelelően konfigurálva. Lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](qs-configure-portal-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége. |
|           | access_denied | Az erőforrás tulajdonosa vagy a hitelesítési kiszolgáló megtagadta a kérelmet. |  |
|           | unsupported_response_type | A hitelesítési kiszolgáló nem támogatja egy hozzáférési jogkivonatot: Ezzel a módszerrel lehet beszerezni. |  |
|           | invalid_scope | A kért hatóköre érvénytelen, ismeretlen vagy nem megfelelően formázott. |  |
| 500 belső kiszolgálóhiba | ismeretlen | Nem sikerült jogkivonatot lekérdezni az Active Directory címtárban. További információ: a naplók  *\<fájl elérési útja\>* | Győződjön meg arról, hogy MSI engedélyezve van a virtuális Gépen. Lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](qs-configure-portal-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége.<br><br>Azt is ellenőrizze, hogy a HTTP GET kérés URI formátuma megfelelő, különösen az erőforrás URI megadva a lekérdezésben. A "kérelemmintát" című része a [REST szakasz megelőző](#rest) példát, vagy [Azure-szolgáltatások, hogy támogatja az Azure AD hitelesítési](overview.md#azure-services-that-support-azure-ad-authentication) szolgáltatások és a megfelelő erőforrás-azonosítók listáját.

## <a name="resource-ids-for-azure-services"></a>Erőforrás-azonosítók az Azure-szolgáltatások

Lásd: [Azure-szolgáltatások, hogy támogatja az Azure AD hitelesítési](overview.md#azure-services-that-support-azure-ad-authentication) erőforrásokat, amelyek támogatják az Azure AD, és az MSI lettek tesztelve, és a megfelelő erőforrás-azonosítók listáját.


## <a name="related-content"></a>Kapcsolódó tartalom

- Egy Azure virtuális gépen az MSI engedélyezéséről [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](qs-configure-portal-windows-vm.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.








