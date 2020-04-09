---
title: Bing Autosuggest Go ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: aahi
ms.openlocfilehash: b352e785673d7c4ed3a9b346758ef0d1fa68b36d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887542"
---
Ismerkedés a Bing Autosuggest ügyféltár go. Az alábbi lépésekkel telepítheti a műsortárat, és kipróbálhatja az alapvető feladatokra vonatkozó példáinkat. 

A Bing Autosuggest ügyfélkódtár az ugráshoz részleges lekérdezési karakterláncok alapján keresési javaslatokat kaphat.

[Referenciadokumentációtár](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [forráskódja](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Mintakód](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója

## <a name="setting-up"></a>Beállítása

### <a name="create-an-azure-resource"></a>Azure-erőforrás létrehozása 

Kezdje el használni a Bing Autosuggest ügyfélkönyvtárat egy Azure-erőforrás létrehozásával. Válassza ki az Ön számára megfelelő erőforrástípust:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

>[!NOTE]
> július 1-je után létrehozott nem próbaerőforrások végpontjai az alábbi egyéni altartomány-formátumot használják. További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) 

A létrehozott erőforrás kulcsának és végpontjának használatával hozzon létre két környezeti változót a hitelesítéshez:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`- A kérések hitelesítéséhez.
* `AUTOSUGGEST_ENDPOINT`- Az erőforrás-végpont API-kérések küldéséhez. Így fog kinézni: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Használja az operációs rendszerre vonatkozó utasításokat.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[macOS](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="create-a-new-go-project"></a>Új Go-projekt létrehozása

Egy konzolablakban (cmd, PowerShell, Terminál, Bash) hozzon létre egy új munkaterületet a Go-projekthez, és keresse meg azt. A munkaterület három mappát tartalmaz: 

* **src** - Ez a könyvtár forráskódot és csomagokat tartalmaz. A `go get` paranccsal telepített csomagok itt fognak elhelyeződni.
* **pkg** - Ez a könyvtár tartalmazza a lefordított Go csomag objektumokat. Ezek a fájlok `.a` mind rendelkeznek kiterjesztéssel.
* **bin** - Ez a könyvtár a futtatáskor `go install`létrehozott bináris végrehajtható fájlokat tartalmazza.

> [!TIP]
> További információ a [Go-munkaterület](https://golang.org/doc/code.html#Workspaces)szerkezetéről. Ez az útmutató `$GOPATH` a `$GOROOT`beállítással és a beállításával kapcsolatos információkat tartalmazza.

Hozzunk létre egy munkaterületet, amelynek `my-app` neve `src` `pkg`és `bin`a szükséges alkönyvtárak a és a:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Az ügyféltár telepítése az Ugráshoz

Most telepítsük az ügyfélkönyvtárat a Go alkalmazáshoz: 

```bash
$ go get -u <library-location-or-url>
```

vagy ha dep-t használ, a társzalag-futtatáson belül:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>A Go alkalmazás létrehozása

Ezután hozzunk létre egy `src/sample-app.go`nevű fájlt:

```bash
$ cd src
$ touch sample-app.go
```

Nyissa `sample-app.go` meg és adja hozzá a csomag nevét, és importálja a következő tárakat:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Hozzon létre `main`egy nevű függvényt. Ezután hozzon létre környezeti változókat a Bing Autosuggest kulcshoz és végponthoz.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódminták bemutatják, hogyan végezhetel el alapvető feladatokat a Bing Autosuggest alkalmazáskódtár gohoz használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [API-kérelem küldése](#send-an-api-request)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE] 
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a Bing autosuggest kulcsához, amelynek neve `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`, és egy a végponthoz. `BING_AUTOSUGGEST_ENDPOINT`

A `main()` függvényben példányosítson meg egy ügyfelet a végponttal és a kulccsal. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>API-kérelem küldése

Ugyanebben a módszerben használja az ügyfél [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) metódusát lekérdezés küldéséhez a Bingnek. Ezután iterate át a javaslatok választ, és [nyomtassa](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) ki az első javaslatot.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a `go run [arguments]` Go alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing Autosuggest-oktatóanyag](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Lásd még

- [Mi a Bing Autosuggest?](../../get-suggested-search-terms.md)
- [A Bing Autosuggest API 7-es verziójának referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
