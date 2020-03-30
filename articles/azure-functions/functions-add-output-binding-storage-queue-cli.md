---
title: Az Azure Functions csatlakoztatása az Azure Storage szolgáltatáshoz parancssori eszközökkel
description: Ismerje meg, hogyan csatlakoztathatja az Azure Functionst egy Azure Storage-várólistához egy kimeneti kötés hozzáadása a parancssori projekthez.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 9181caf516d5c2003cfe99b125d2921732cbbb9d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473387"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Az Azure Functions csatlakoztatása az Azure Storage szolgáltatáshoz parancssori eszközökkel

Ebben a cikkben integrálja az Azure Storage-várólistát [az előző rövid útmutatóban](functions-create-first-azure-function-azure-cli.md)létrehozott függvény- és tárfiókkal. Ezt az integrációt egy *kimeneti kötés* használatával érheti el, amely http-kérelemből adatokat ír a várólistában lévő üzenetekbe. A cikk végrehajtása nem jár további költségekkel az előző rövid útmutató néhány USD centje in túl. Ha többet szeretne megtudni a kötésekről, olvassa el az [Azure Functions eseményindítók és kötések fogalmait.](functions-triggers-bindings.md)

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, ki kell töltenie a [rövid útmutató: Hozzon létre egy Azure Functions projektet a parancssorból](functions-create-first-azure-function-azure-cli.md). Ha már megtisztította az erőforrásokat a cikk végén, ismét hajtsa végre a lépéseket a függvényalkalmazás és a kapcsolódó erőforrások újbóli létrehozásához az Azure-ban.

## <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-kapcsolati karakterlánc lekérése

Amikor az előző rövid útmutatóban létrehozott egy függvényalkalmazást az Azure-ban, egy Storage-fiókot is létrehozott. A fiók kapcsolati karakterlánca biztonságosan tárolódik az Azure-beli alkalmazásbeállításokban. Ha letölti a beállítást a *local.settings.json* fájlba, akkor a helyi futtatásakor használhatja ezt a kapcsolatírást ugyanazon a fiókban lévő Storage várólistára. 

1. A projekt gyökeréből futtassa `<APP_NAME>` a következő parancsot, és cserélje le a függvényalkalmazás nevét az előző rövid útmutatóból. Ez a parancs felülírja a fájlban lévő értékeket.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Nyissa meg a *local.settings.json webhelyet,* és keresse meg a nevű `AzureWebJobsStorage`értéket, amely a Storage-fiók kapcsolati karakterlánca. A cikk `AzureWebJobsStorage` más szakaszaiban a nevet és a kapcsolati karakterláncot használhatja.

> [!IMPORTANT]
> Mivel *a local.settings.json* az Azure-ból letöltött titkokat tartalmaz, mindig zárja ki ezt a fájlt a forrásellenőrzésből. A helyi függvényprojekttel létrehozott *.gitignore* fájl alapértelmezés szerint kizárja a fájlt.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Kimenetkötésdefiníció hozzáadása a függvényhez

Bár egy függvény csak egy eseményindítóval rendelkezhet, több bemeneti és kimeneti kötést is használhat, amelyek lehetővé teszik, hogy más Azure-szolgáltatásokhoz és erőforrásokhoz egyéni integrációs kód írása nélkül csatlakozzon. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ezeket a kötéseket a függvénymappában lévő *function.json* fájlban deklarálja. Az előző rövid útmutatóból a *HttpExample* mappában lévő `bindings` *function.json* fájl két kötést tartalmaz a gyűjteményben:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Minden kötésnek van legalább egy típusa, egy iránya és egy neve. A fenti példában az első `httpTrigger` kötés típusa `in`az irányával . Az `in` irányhoz `name` adja meg egy bemeneti paraméter nevét, amelyet a rendszer az eseményindító meghívásakor a függvénynek küld.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A gyűjtemény második kötése `res`a . Ez `http` a kötés egy`out`kimeneti kötés ( ), amely a HTTP-válasz írására szolgál. 

Ha ebből a funkcióból szeretne írni `out` egy `queue` Azure Storage-várólistába, adjon hozzá egy típusú kötést a nevével, `msg`amint az az alábbi kódban látható:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A gyűjtemény második kötése `http` az `out`irányával ellátott típusú, `name` `$return` amely esetben a speciális azt jelzi, hogy ez a kötés a függvény visszatérési értékét használja a bemeneti paraméter megadása helyett.

Ha ebből a funkcióból szeretne írni `out` egy `queue` Azure Storage-várólistába, adjon hozzá egy típusú kötést a nevével, `msg`amint az az alábbi kódban látható:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A gyűjtemény második kötése `res`a . Ez `http` a kötés egy`out`kimeneti kötés ( ), amely a HTTP-válasz írására szolgál. 

Ha ebből a funkcióból szeretne írni `out` egy `queue` Azure Storage-várólistába, adjon hozzá egy típusú kötést a nevével, `msg`amint az az alábbi kódban látható:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ebben az `msg` esetben a függvény kimeneti argumentumként jelenik meg. Egy `queue` típus esetében meg kell adnia a `queueName` várólistának a nevét is, és meg kell adnia `connection`az Azure Storage-kapcsolat *nevét* (a *local.settings.json-ból)* a alkalmazásban. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

A kötések részleteiről az [Azure Functions elindítja és kötéseket indítja el](functions-triggers-bindings.md) és ismerteti a sorok [kimeneti konfigurációját.](functions-bindings-storage-queue-output.md#configuration)

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötés használatához

A *function.json*ban megadott várólista-kötéssel most frissítheti a függvényt, hogy megkapja a `msg` kimeneti paramétert, és üzeneteket írjon a várólistába.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

Vegye figyelembe, hogy *nem* kell kódot írnia a hitelesítéshez, a várólista-referenciák beszerzéséhez vagy az adatok írásához. Mindezek az integrációs feladatok kényelmesen kezelik az Azure Functions futásidejű és a várólista kimeneti kötés.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage várólistájában

A várólista az [Azure Portalon](../storage/queues/storage-quickstart-queues-portal.md) vagy a [Microsoft Azure Storage Explorerben](https://storageexplorer.com/)tekinthető meg. A várólista az Azure CLI-ben is megtekinthető, a következő lépésekben leírtak szerint:

1. Nyissa meg a függvényprojekt *local.setting.json* fájlját, és másolja a kapcsolati karakterlánc értékét. Terminál- vagy parancsablakban futtassa a következő `AZURE_STORAGE_CONNECTION_STRING`parancsot a program névre `<MY_CONNECTION_STRING>`szóló környezeti változó létrehozásához, és illessze be az adott kapcsolati karakterláncot a helyére. (Ez a környezeti változó azt jelenti, hogy nem kell megadnia a kapcsolati karakterláncot minden további parancshoz az `--connection-string` argumentum használatával.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Nem kötelező) A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) parancs segítségével megtekintheti a storage várólistákat a fiókjában. A parancs kimenetének tartalmaznia `outqueue`kell egy várólistát, amely akkor jött létre, amikor a függvény az első üzenetét a várólistára írta.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. A [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) parancs segítségével olvassa el az üzenetet ebből a várólistából, amely a funkció korábbi tesztelésekén használt első név nek kell lennie. A parancs beolvassa és eltávolítja az első üzenetet a várólistából. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ez a parancsfájl a certutil segítségével dekódolja a base64 kódolású üzenetgyűjteményt egy helyi ideiglenes fájlból. Ha nincs kimenet, próbálja meg `> NUL` eltávolítani a parancsfájlból a certutil kimenet letiltásához, ha hiba történik. 
    
    ---
    
    Mivel az üzenet törzse [base64 kódolású,](functions-bindings-storage-queue-trigger.md#encoding)az üzenetet dekódolni kell, mielőtt megjelenik. A végrehajtás `az storage message get`után az üzenet törlődik a várólistából. Ha csak egy üzenet `outqueue`volt a alkalmazásban, akkor a parancs második futtatásakor nem fog beolvasni egy üzenetet, és ehelyett hibaüzenetet kap.

## <a name="redeploy-the-project-to-azure"></a>A projekt újratelepítése az Azure-ba

Most, hogy helyileg ellenőrizte, hogy a függvény üzenetet írt az Azure Storage-várólistába, újratelepítheti a projektet az Azure-ban futó végpont frissítéséhez.

1. A *LocalFunctionsProj* mappában [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) a paranccsal újratelepítheti a projektet, és lecserélheti`<APP_NAME>` az alkalmazás nevét.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Az előző rövid útmutatóhoz, használja a böngészőt vagy a CURL-t az újratelepített függvény teszteléséhez.

    # <a name="browser"></a>[Böngésző](#tab/browser)
    
    Másolja a közzétételi parancs kimenetében látható teljes **Invoke URL-címet** a `&name=Functions`böngésző címsorába, hozzáfűzve a lekérdezési paramétert . A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

    ![A függvény kimenete az Azure-on fut egy böngészőben](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Futtassa [`curl`](https://curl.haxx.se/) az **Invoke URL-címmel,** hozzáfűzve a paramétert. `&name=Functions` A parancs kimenetének a "Hello Functions" szövegnek kell lennie.
    
    ![A függvény kimenete az Azure-on fut a CURL használatával](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Vizsgálja meg újra a tárolási várólistát az előző szakaszban leírtak szerint, és ellenőrizze, hogy az tartalmazza-e a várólistába írt új üzenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte a parancsot, törölje az erőforráscsoportot és annak összes tartalmazott erőforrását, hogy elkerülje a további költségeket.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>További lépések

Frissítette a HTTP-aktivált függvényt, hogy adatokat írjon egy tárolási várólistába. Most már többet is megtudhat a Funkciók fejlesztéséről a parancssorból a Core Tools és az Azure CLI használatával:

+ [Az Azure Functions alapvető eszközeinek használata](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Példák a C# teljes függvényprojektjeire.](/samples/browse/?products=azure-functions&languages=csharp)

+ [Azure Functions C# fejlesztői útmutató](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Példák a teljes függvényprojektekre JavaScript-ben.](/samples/browse/?products=azure-functions&languages=javascript)

+ [Az Azure Functions JavaScript fejlesztői útmutatója](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Példák a TypeScript teljes függvényprojektjeire.](/samples/browse/?products=azure-functions&languages=typescript)

+ [Az Azure Functions TypeScript fejlesztői útmutatója](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Példák a python-beli teljes függvényprojektekre.](/samples/browse/?products=azure-functions&languages=python)

+ [Az Azure Functions Python fejlesztői útmutatója](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Példák a PowerShell teljes függvényprojektjeire.](/samples/browse/?products=azure-functions&languages=azurepowershell)

+ [Az Azure Functions PowerShell fejlesztői útmutatója](functions-reference-powershell.md) 
::: zone-end
+ [Az Azure Functions aktiválódik és kötéseket köt](functions-triggers-bindings.md)

+ [Funkciók árképzési lapja](https://azure.microsoft.com/pricing/details/functions/)

+ [A felhasználási terv költségeinek becslése](functions-consumption-costs.md) 
