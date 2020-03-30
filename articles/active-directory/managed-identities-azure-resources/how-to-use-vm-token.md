---
title: Felügyelt identitások használata virtuális gépen hozzáférési jogkivonat beszerzéséhez – Azure AD
description: Lépésenkénti utasítások és példák az Azure-erőforrások felügyelt identitások egy virtuális gépeken oauth hozzáférési jogkivonat megszerzéséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58103bad3914bd0c0c6e70f8e3d2882271e1070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049199"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Felügyelt identitások használata Azure-erőforrásokhoz egy Azure virtuális gépen egy hozzáférési jogkivonat beszerzéséhez 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ez a cikk különböző kód- és parancsfájlpéldákat tartalmaz a jogkivonat-beszerzéshez, valamint útmutatást nyújt olyan fontos témakörökhöz, mint a jogkivonat lejárati és HTTP-hibáinak kezelése. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha az Azure PowerShell-példákat szeretné használni ebben a cikkben, telepítse az [Azure PowerShell](/powershell/azure/install-az-ps)legújabb verzióját.


> [!IMPORTANT]
> - Ebben a cikkben az összes mintakód/parancsfájl feltételezi, hogy az ügyfél egy felügyelt identitásokkal rendelkező virtuális gépen fut az Azure-erőforrásokhoz. Használja a virtuális gép "Connect" funkció az Azure Portalon, távolról csatlakozhat a virtuális géphez. Az Azure-erőforrások felügyelt identitásainak virtuális gépeken való engedélyezéséről az [Azure-erőforrások felügyelt identitásainak konfigurálása az Azure Portalon,](qs-configure-portal-windows-vm.md)vagy az egyik változatcikk (PowerShell, CLI, sablon vagy Egy Azure SDK használatával) című témakörben olvashat. 

> [!IMPORTANT]
> - Az Azure-erőforrások felügyelt identitásainak biztonsági határa az az erőforrás, amelyen használják. A virtuális gépen futó összes kód/parancsfájl kérheti és lekérheti a rajta elérhető felügyelt identitások jogkivonatait. 

## <a name="overview"></a>Áttekintés

Egy ügyfélalkalmazás kérheti felügyelt identitások az Azure-erőforrások [csak alkalmazás-hozzáférési jogkivonat](../develop/developer-glossary.md#access-token) egy adott erőforrás eléréséhez. A jogkivonat [az Azure resources egyszerű szolgáltatásszolgáltatásának felügyelt identitásain alapul.](overview.md#how-does-the-managed-identities-for-azure-resources-work) Mint ilyen, nincs szükség arra, hogy az ügyfél regisztrálja magát egy hozzáférési jogkivonat megszerzéséhez saját szolgáltatásnév alatt. A jogkivonat alkalmas az [ügyfél hitelesítő adatait igénylő szolgáltatás-szolgáltatás hívások](../develop/v2-oauth2-client-creds-grant-flow.md)tulajdonosi jogkivonataként való használatra.

|  |  |
| -------------- | -------------------- |
| [Token beszereznie HTTP-n keresztül](#get-a-token-using-http) | Az Azure-erőforrások tokenvégpontjának felügyelt identitásai protokollrészletei |
| [Token beszereznie a Microsoft.Azure.Services.AppAuthentication könyvtár segítségével.](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Példa a Microsoft.Azure.Services.AppAuthentication könyvtár .NET-ügyfélből történő használatára
| [Token beszereznie C használatával #](#get-a-token-using-c) | Példa felügyelt identitások használatára az Azure Resources REST-végponthoz c# ügyfélből |
| [Token beszereznie Java használatával](#get-a-token-using-java) | Példa felügyelt identitások használatára az Azure Resources REST-végponthoz Java-ügyféltől |
| [Token beszereznie az Ugrás használatával](#get-a-token-using-go) | Példa felügyelt identitások használatára az Azure Resources REST-végponthoz egy Go-ügyféltől |
| [Token beszereznie az Azure PowerShell használatával](#get-a-token-using-azure-powershell) | Példa felügyelt identitások használatára az Azure-erőforrások REST-végpontja powershell-ügyfélből |
| [Token beszereznie a CURL használatával](#get-a-token-using-curl) | Példa felügyelt identitások használatára az Azure-erőforrások REST-végpontja számára egy Bash/CURL-ügyféltől |
| Token-gyorsítótárazás kezelése | Útmutató a lejárt hozzáférési jogkivonatok kezeléséhez |
| [Hibakezelés](#error-handling) | Útmutató az Azure-erőforrások tokenvégpontja felügyelt identitásaiból visszaadott HTTP-hibák kezeléséhez |
| [Az Azure-szolgáltatások erőforrás-azonosítói](#resource-ids-for-azure-services) | Hol kaphat erőforrásazonosítókat a támogatott Azure-szolgáltatásokhoz? |

## <a name="get-a-token-using-http"></a>Token beszereznie HTTP-n keresztül 

A hozzáférési jogkivonat beszerzésének alapvető felülete a REST-en alapul, így a virtuális számítógépen futó minden olyan ügyfélalkalmazás számára elérhetővé válik, amely HTTP REST-hívásokat tud kezdeményezni. Ez hasonló az Azure AD programozási modellhez, kivéve, hogy az ügyfél egy végpontot használ a virtuális gépen (szemben egy Azure AD-végpont).

Mintakérelem az Azure Instance metaadatszolgáltatás (IMDS) végpontját használva *(ajánlott)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy adatokat szeretne beolvasni a végpontból. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Az Azure-erőforrások végpontja felügyelt identitások a példány metaadat-szolgáltatás. |
| `api-version`  | Egy lekérdezési karakterlánc-paraméter, amely az IMDS-végpont API-verzióját jelzi. Használja az `2018-02-01` API-verziót vagy annak nagyobb verzióját. |
| `resource` | A lekérdezési karakterlánc paraméter, amely a célerőforrás alkalmazásazonosítóuri-ját jelzi. A kiadott jogkivonat `aud` (közönség) jogcímében is megjelenik. Ez a példa egy jogkivonatot kér az Azure Resource `https://management.azure.com/`Manager eléréséhez, amely alkalmazásazonosító URI-val rendelkezik. |
| `Metadata` | Egy HTTP-kérelem fejlécmezője, amelyet az Azure-erőforrások felügyelt identitásai igényelnek a kiszolgálóoldali kérelemhamisítás (SSRF) támadáselleni megoldásként. Ezt az értéket "igaz" értékre kell állítani, minden kisbetűvel. |
| `object_id` | (Nem kötelező) Egy lekérdezési karakterlánc paraméter, amely a felügyelt identitás object_id jelzi, amelyhez a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitások rendelkezik.|
| `client_id` | (Nem kötelező) Egy lekérdezési karakterlánc paraméter, amely a felügyelt identitás client_id jelzi, amelyhez a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitások rendelkezik.|
| `mi_res_id` | (Nem kötelező) Egy lekérdezési karakterlánc-paraméter, amely a felügyelt identitás mi_res_id (Azure Resource ID) jelzi, amelyhez a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitások rendelkezik. |

Mintakérelem az Azure-erőforrások virtuálisgép-bővítményvégpontjának felügyelt identitásait használva *(2019 januárjában evevésre tervezve)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy adatokat szeretne beolvasni a végpontból. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `http://localhost:50342/oauth2/token` | Az Azure-erőforrások végpontjának felügyelt identitásai, ahol az 50342 az alapértelmezett port, és konfigurálható. |
| `resource` | A lekérdezési karakterlánc paraméter, amely a célerőforrás alkalmazásazonosítóuri-ját jelzi. A kiadott jogkivonat `aud` (közönség) jogcímében is megjelenik. Ez a példa egy jogkivonatot kér az Azure Resource `https://management.azure.com/`Manager eléréséhez, amely alkalmazásazonosító URI-val rendelkezik. |
| `Metadata` | Egy HTTP-kérelem fejlécmezője, amelyet az Azure-erőforrások felügyelt identitásai igényelnek a kiszolgálóoldali kérelemhamisítás (SSRF) támadáselleni megoldásként. Ezt az értéket "igaz" értékre kell állítani, minden kisbetűvel.|
| `object_id` | (Nem kötelező) Egy lekérdezési karakterlánc paraméter, amely a felügyelt identitás object_id jelzi, amelyhez a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitások rendelkezik.|
| `client_id` | (Nem kötelező) Egy lekérdezési karakterlánc paraméter, amely a felügyelt identitás client_id jelzi, amelyhez a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitások rendelkezik.|

Mintaválasz:

```json
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
| `access_token` | A kért hozzáférési jogkivonat. Biztonságos REST API hívása kor a `Authorization` jogkivonat "tulajdonosi" jogkivonatként van beágyazva a kérelem fejlécmezőjébe, lehetővé téve az API számára a hívó hitelesítését. | 
| `refresh_token` | Az Azure-erőforrások felügyelt identitásai nem használják. |
| `expires_in` | A hozzáférési jogkivonat a kiállítás időpontjától számított másodpercek száma, mielőtt lejárna. A kiállítás ideje megtalálható a jogkivonat jogcímében. `iat` |
| `expires_on` | A hozzáférési jogkivonat lejáratának időtartománya. A dátum az "1970-01-01T0:0:0Z UTC" (a jogkivonat jogcímének `exp` megfelelő) másodpercek számaként jelenik meg. |
| `not_before` | A hozzáférési jogkivonat érvénybe lépésének és elfogadott időtartamának. A dátum az "1970-01-01T0:0:0Z UTC" (a jogkivonat jogcímének `nbf` megfelelő) másodpercek számaként jelenik meg. |
| `resource` | Az erőforrás, amelyhez hozzáférési jogkivonatot kértek, amely megfelel a `resource` kérelem lekérdezési karakterlánc paraméterének. |
| `token_type` | A jogkivonat típusa, amely egy "tulajdonos" hozzáférési jogkivonat, ami azt jelenti, hogy az erőforrás hozzáférést biztosíthat a jogkivonat tulajdonosához. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Token beszereznie a Microsoft.Azure.Services.AppAuthentication könyvtár segítségével.

A .NET-alkalmazások és -függvények esetében az Azure-erőforrások felügyelt identitásaival való munka legegyszerűbb módja a Microsoft.Azure.Services.AppAuthentication csomag. Ez a könyvtár azt is lehetővé teszi, hogy tesztelje a kódot helyileg a fejlesztői gépen, a Visual Studio, az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)vagy az Active Directory integrált hitelesítés felhasználói fiókjának használatával. A tár helyi fejlesztési lehetőségeiről a [Microsoft.Azure.Services.AppAuthentication hivatkozáscímű témakörben lehet további további adatokat.](/azure/key-vault/service-to-service-authentication) Ez a szakasz bemutatja, hogyan kezdheti el a kódban lévő tárt.

1. Hivatkozások hozzáadása a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet csomagokhoz az alkalmazáshoz.

2.  Adja hozzá a következő kódot az alkalmazáshoz:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Ha többet szeretne megtudni a Microsoft.Azure.Services.AppAuthentication szolgáltatásról és az általa elérhetővé tett műveletekről, olvassa el a [Microsoft.Azure.Services.AppAuthentication referencia-](/azure/key-vault/service-to-service-authentication) és az [Azure-erőforrások .NET-mintájával rendelkező Alkalmazásszolgáltatást és KeyVault-ot.](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

## <a name="get-a-token-using-c"></a>Token beszereznie C használatával #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
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

## <a name="get-a-token-using-java"></a>Token beszereznie Java használatával

Ezzel a [JSON-kódtárral](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) javadat kérhet be.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Token beszereznie az Ugrás használatával

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

## <a name="get-a-token-using-azure-powershell"></a>Token beszereznie az Azure PowerShell használatával

A következő példa bemutatja, hogyan használhatja az Azure-erőforrások REST-végpontjának felügyelt identitásait egy PowerShell-ügyféltől a következőkhöz:

1. Szerezzen be egy hozzáférési jogkivonatot.
2. A hozzáférési jogkivonat használatával hívja meg az Azure Resource Manager REST API-t, és a virtuális gép ről szerezzen be információkat. Ügyeljen arra, hogy az előfizetés-azonosítót, az erőforráscsoport `<RESOURCE-GROUP>`nevét `<VM-NAME>`és a virtuálisgép nevét helyettesítse a, `<SUBSCRIPTION-ID>`illetve a.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Példa a hozzáférési jogkivonat elemzésére a válaszból:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Token beszereznie a CURL használatával

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Példa a hozzáférési jogkivonat elemzésére a válaszból:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Token gyorsítótárazása

Míg a felügyelt identitások az Azure-erőforrások alrendszer használatban van (IMDS/felügyelt identitások az Azure-erőforrások virtuálisgép-bővítmény) nem gyorsítótár-jogkivonatok, azt is javasoljuk, hogy a kódban a token-gyorsítótárazás. Ennek eredményeképpen elő kell készítenie olyan esetekre, ahol az erőforrás azt jelzi, hogy a jogkivonat lejárt. 

Az Azure AD-be irányuló vezetékközbeni hívások csak akkor eredményeznek eredményt, ha:
- gyorsítótár-tévesztés az Azure-erőforrások alrendszer-gyorsítótárának felügyelt identitásaiban lévő token hiánya miatt következik be
- a gyorsítótárazott token lejárt

## <a name="error-handling"></a>Hibakezelés

Az Azure-erőforrások végpontjának felügyelt identitásai hibákat jeleznek a HTTP-válaszüzenet fejlécének állapotkód mezőjén keresztül, 4xx vagy 5xx hibaként:

| Állapotkód | Hiba oka | Hogyan kell kezelni |
| ----------- | ------------ | ------------- |
| 404 Nem található. | Az IMDS-végpont frissítése. | Próbálkozzon újra az Expontential Backoff segítségével. Lásd az alábbi útmutatást. |
| 429 Túl sok kérés. |  IMDS-szabályozási korlát elérése. | Próbálkozzon újra exponenciális visszalépéssel. Lásd az alábbi útmutatást. |
| 4xx Hiba kérésre. | Egy vagy több kérelemparaméter helytelen volt. | Ne próbálkozzon újra.  További információkért vizsgálja meg a hiba részleteit.  A 4xx hibák tervezési idejű hibák.|
| 5xx Átmeneti hiba a szolgáltatásból. | Az Azure-erőforrások alrendszer vagy az Azure Active Directory felügyelt identitások átmeneti hibát adott vissza. | Legalább 1 másodperc várakozás után biztonságosan újrapróbálkozhat.  Ha túl gyorsan vagy túl gyakran próbálja meg újra, az IMDS és/vagy az Azure AD sebességkorlátozási hibát (429) adhat vissza.|
| timeout | Az IMDS-végpont frissítése. | Próbálkozzon újra az Expontential Backoff segítségével. Lásd az alábbi útmutatást. |

Hiba esetén a megfelelő HTTP-választörzs JSON-t tartalmaz a következő hibarészleteivel:

| Elem | Leírás |
| ------- | ----------- |
| error   | Hibaazonosító. |
| error_description | A hiba részletes leírása. **A hibaleírások bármikor változhatnak. Ne írjon olyan kódot, amely a hibaleírásban lévő értékek alapján ágazik.**|

### <a name="http-response-reference"></a>HTTP-válasz hivatkozása

Ez a szakasz a lehetséges hibaválaszokat dokumentálja. A "200 OK" állapot sikeres válasz, és a hozzáférési jogkivonat a JSON választörzsben található, a access_token elemben.

| Állapotkód | Hiba | Hiba leírása | Megoldás |
| ----------- | ----- | ----------------- | -------- |
| 400 rossz kérés | invalid_resource | AADSTS50001: Az * \<URI\> * nevű alkalmazás nem található a * \<tenant-id\>* nevű bérlőben. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy a bérlő bármely felhasználója hozzájárult hozzá. Lehet, hogy nem a megfelelő bérlőnek küldte el a hitelesítési kérelmet.\ | (Csak Linux) |
| 400 rossz kérés | bad_request_102 | A szükséges metaadat-fejléc nincs megadva | Vagy `Metadata` hiányzik a kérelem fejlécmezője a kérésből, vagy helytelenül van formázva. Az értéket úgy kell `true`megadni, hogy minden kisbetűs. Tekintse meg a "Minta kérelem" az előző REST szakaszban egy példa.|
| 401 Jogosulatlan | unknown_source | Ismeretlen forrás * \<URI-ja\>* | Ellenőrizze, hogy a HTTP GET-kérelem URI-ja megfelelően van-e formázva. Az `scheme:host/resource-path` adagot a `http://localhost:50342/oauth2/token`ban kell megadni. Tekintse meg a "Minta kérelem" az előző REST szakaszban egy példa.|
|           | invalid_request | A kérelemből hiányzik egy kötelező paraméter, érvénytelen paraméterértéket tartalmaz, egynél többször tartalmaz paramétert, vagy más módon hibásan formázott. |  |
|           | unauthorized_client | Az ügyfél nem jogosult hozzáférési jogkivonat ot kérni ezzel a módszerrel. | Egy kérelem, amely nem használja a helyi visszacsatolás a bővítmény hívásához, vagy egy virtuális gép, amely nem rendelkezik felügyelt identitások az Azure-erőforrások megfelelően konfigurált. Lásd: [Felügyelt identitások konfigurálása az Azure-erőforrások egy virtuális gép az Azure Portalon,](qs-configure-portal-windows-vm.md) ha segítségre van szüksége a virtuális gép konfigurációját. |
|           | access_denied | Az erőforrás tulajdonosa vagy az engedélyezési kiszolgáló megtagadta a kérést. |  |
|           | unsupported_response_type | Az engedélyezési kiszolgáló nem támogatja a hozzáférési jogkivonat beszerzését ezzel a módszerrel. |  |
|           | invalid_scope | A kért hatókör érvénytelen, ismeretlen vagy hibásan formázott. |  |
| 500 Belső kiszolgálóhiba | Ismeretlen | Nem sikerült beolvasni a jogkivonatot az Active directoryból. További részletek a * \<fájlok elérési útjának naplózása\>* | Ellenőrizze, hogy az Azure-erőforrások felügyelt identitások engedélyezve van-e a virtuális gépen. Lásd: [Felügyelt identitások konfigurálása az Azure-erőforrások egy virtuális gép az Azure Portalon,](qs-configure-portal-windows-vm.md) ha segítségre van szüksége a virtuális gép konfigurációját.<br><br>Ellenőrizze azt is, hogy a HTTP GET-kérelem URI-ja megfelelően van-e formázva, különösen a lekérdezési karakterláncban megadott erőforrás-URI.Also verify that your HTTP GET request URI is formated correctly, especially the resource URI specified in the query string. Tekintse meg a "Mintakérelem" az előző REST szakaszban egy példa, vagy [az Azure-szolgáltatások, amelyek támogatják az Azure AD-hitelesítés](services-support-msi.md) a szolgáltatások listáját és a megfelelő erőforrás-azonosítók.

## <a name="retry-guidance"></a>Újrapróbálkozási útmutató 

Javasoljuk, hogy próbálkozzon újra, ha 404-es, 429-es vagy 5xx-es hibakódot kap (lásd a fenti [Hibakezelés](#error-handling) című témakört).

A sávszélesség-szabályozási korlátok az IMDS-végpontra irányuló hívások számára vonatkoznak. A sávszélesség-szabályozási küszöbérték túllépése esetén az IMDS-végpont korlátozza a további kérelmeket, amíg a szabályozás érvényben van. Ebben az időszakban az IMDS-végpont a 429-es HTTP-állapotkódot adja vissza ("Túl sok kérés"), és a kérelmek sikertelenek lesznek. 

Az újrapróbálkozáshoz a következő stratégiát javasoljuk: 

| **Újrapróbálkozási stratégia** | **Beállítások** | **Értékek** | **Működés** |
| --- | --- | --- | --- |
|ExponentialBackoff |Ismétlések száma<br />Visszatartás (min.)<br />Visszatartás (max.)<br />Visszatartás (változás)<br />Első gyors újrapróbálkozás |5<br />0 másodperc<br />60 másodperc<br />2 másodperc<br />hamis |1. kísérlet – 0 mp. késleltetés<br />2. kísérlet – kb. 2 mp. késleltetés<br />3. kísérlet – kb. 6 mp. késleltetés<br />4. kísérlet – kb. 14 mp. késleltetés<br />5. kísérlet – kb. 30 mp. késleltetés |

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói

Tekintse meg az [Azure-szolgáltatásokat, amelyek támogatják az Azure AD-hitelesítést](services-support-msi.md) az Azure AD-t támogató és felügyelt identitásokkal az Azure-erőforrásokkal és a megfelelő erőforrás-azonosítókkal tesztelt erőforrások listájáért.


## <a name="next-steps"></a>További lépések

- Ha engedélyezni szeretné az Azure-erőforrások felügyelt identitások egy Azure virtuális gép, [az Azure-erőforrások felügyelt identitások konfigurálása egy virtuális gép en az Azure Portalon.](qs-configure-portal-windows-vm.md)








