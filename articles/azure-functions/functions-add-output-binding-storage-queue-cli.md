---
title: Azure Functions összekötése az Azure Storage-hoz a parancssori eszközök használatával
description: Megtudhatja, hogyan csatlakoztathatja a Azure Functionst egy Azure Storage-várólistához egy kimeneti kötés hozzáadásával a parancssori projekthez.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201926"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Azure Functions összekötése az Azure Storage-hoz a parancssori eszközök használatával

Ebben a cikkben az Azure Storage-üzenetsor integrálását az [előző](functions-create-first-azure-function-azure-cli.md)rövid útmutatóban létrehozott függvény-és Storage-fiókkal végezheti el. Ezt az integrációt olyan *kimeneti kötés* használatával érheti el, amely http-kérésből adatokat ír a várólistában lévő üzenetbe. Ennek a cikknek a végrehajtásával az előző rövid útmutató néhány USD százalékán felül nem jár további költségekkel. További információ a kötésekről: [Azure functions eseményindítók és kötések fogalmai](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, be kell fejeznie a cikket, gyors [útmutató: Azure functions projekt létrehozása a parancssorból](functions-create-first-azure-function-azure-cli.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.

## <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-beli kapcsolatok karakterláncának beolvasása

Amikor az előző rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban, létrehozta a Storage-fiókot is. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ha a beállítást a *Local. Settings. JSON* fájlba tölti le, akkor a függvény helyileg történő futtatásakor ugyanazzal a fiókkal írja be a kapcsolódást a tárolási várólistába. 

1. A projekt gyökerében futtassa a következő parancsot, és cserélje le a `<APP_NAME>`t az előző rövid útmutatóban szereplő Function alkalmazás nevére. Ez a parancs felülírja a fájlban lévő összes meglévő értéket.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Nyissa meg a *Local. Settings. JSON* fájlt, és keresse meg `AzureWebJobsStorage`nevű értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. A jelen cikk más részeiben a `AzureWebJobsStorage` nevet és a kapcsolatok karakterláncát használja.

> [!IMPORTANT]
> Mivel a *Local. Settings. JSON* az Azure-ból letöltött titkos kulcsokat tartalmaz, mindig zárja ki ezt a fájlt a forrás vezérlőelemből. A helyi functions projekttel létrehozott *. gitignore* fájl alapértelmezés szerint kizárja a fájlt.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Kimeneti kötési definíció hozzáadása a függvényhez

Bár a függvények csak egy triggerrel rendelkezhetnek, több bemeneti és kimeneti kötéssel is rendelkezhet, amelyek lehetővé teszik más Azure-szolgáltatásokhoz és-erőforrásokhoz való csatlakozást az egyéni integrációs kód írása nélkül. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ezeket a kötéseket a Function *. JSON* fájlban deklarálhatja a függvény mappájában. Az előző rövid útmutatóban a *function. JSON* fájl a *HttpExample* mappában két kötést tartalmaz a `bindings` gyűjteményben:  
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
Minden kötéshez legalább egy típus, egy irány és egy név tartozik. A fenti példában az első kötés `httpTrigger` típusú, `in`irányával. A `in` irányában a `name` megadja egy bemeneti paraméter nevét, amelyet a rendszer az eseményindító által meghívott függvénynek továbbít.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A gyűjtemény második kötésének neve `res`. Ez a `http` kötés egy kimeneti kötés (`out`), amely a HTTP-válasz írására szolgál. 

Ha a függvényből szeretne írni egy Azure Storage-várólistába, adjon hozzá egy `queue` nevű `out` kötést a (z) `msg`néven az alábbi kódban látható módon:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A gyűjtemény második kötése `http` típusú, `out`irányával, amely esetben a `$return` speciális `name` azt jelzi, hogy ez a kötés a függvény visszatérési értékét használja ahelyett, hogy bemeneti paramétert adjon meg.

Ha a függvényből szeretne írni egy Azure Storage-várólistába, adjon hozzá egy `queue` nevű `out` kötést a (z) `msg`néven az alábbi kódban látható módon:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A gyűjtemény második kötésének neve `res`. Ez a `http` kötés egy kimeneti kötés (`out`), amely a HTTP-válasz írására szolgál. 

Ha a függvényből szeretne írni egy Azure Storage-várólistába, adjon hozzá egy `queue` nevű `out` kötést a (z) `msg`néven az alábbi kódban látható módon:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ebben az esetben `msg` van megadva a függvény kimeneti argumentumként. `queue` típus esetén meg kell adnia a várólista nevét is `queueName`, és meg kell adnia az Azure Storage-kapcsolatok *nevét* (a *Local. Settings. JSON*fájlból) a `connection`ban. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

További információ a kötések részleteiről: [Azure functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md) és [üzenetsor kimenetének konfigurálása](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötés használatához

A *function. JSON*fájlban megadott üzenetsor-kötéssel frissítheti a függvényt, hogy megkapja a `msg` kimeneti paramétert, és üzeneteket írjon a várólistába.

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

Figyelje meg, hogy *nem* kell kódot írnia a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. Az összes ilyen integrációs feladat kényelmesen kezelhető a Azure Functions futtatókörnyezetben és a várólista kimeneti kötésében.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage-várólistában

Az üzenetsor a [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) vagy a [Microsoft Azure Storage Explorerban](https://storageexplorer.com/)tekinthető meg. Az üzenetsor az Azure CLI-ben is megtekinthető az alábbi lépések szerint:

1. Nyissa meg a Function projekt *Local. Setting. JSON* fájlt, és másolja a kapcsolatok karakterláncának értékét. Egy terminál-vagy parancssori ablakban futtassa a következő parancsot egy `AZURE_STORAGE_CONNECTION_STRING`nevű környezeti változó létrehozásához, és illessze be az adott kapcsolódási karakterláncot a `<MY_CONNECTION_STRING>`helyére. (Ez a környezeti változó azt jelenti, hogy a `--connection-string` argumentum használatával nem kell megadnia a kapcsolódási karakterláncot minden további parancshoz.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. Választható A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) parancs használatával megtekintheti a fiókban található tárolási várólistákat. A parancs kimenetének tartalmaznia kell egy `outqueue`nevű várólistát, amely akkor jött létre, amikor a függvény első üzenetét írta az adott várólistára.
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. A [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) parancs használatával olvassa el az üzenetet ebből a sorból, amely a függvény korábbi teszteléséhez használt keresztnév. A parancs beolvassa és eltávolítja az első üzenetet a várólistából. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ez a szkript a certutil használatával dekódolja a Base64 kódolású üzenetek gyűjteményét egy helyi ideiglenes fájlból. Ha nincs kimenet, próbálja meg eltávolítani `> NUL` a parancsfájlból a certutil-kimenet letiltásának leállítására, ha hiba történt. 
    
    ---
    
    Mivel az üzenettörzs tárolása [Base64 kódolású](functions-bindings-storage-queue-trigger.md#encoding), az üzenetet a Megjelenítés előtt dekódolni kell. `az storage message get`végrehajtása után a rendszer eltávolítja az üzenetet a várólistából. Ha a `outqueue`ban csak egy üzenet található, akkor nem fog beolvasni egy üzenetet, ha a parancsot Másodszor futtatja, hanem hibaüzenetet kap.

## <a name="redeploy-the-project-to-azure"></a>A projekt újbóli üzembe helyezése az Azure-ban

Most, hogy ellenőrizte a helyileg, hogy a függvény üzenetet írt az Azure Storage-várólistába, a projekt újbóli üzembe helyezésével frissítheti az Azure-on futó végpontot.

1. A *LocalFunctionsProj* mappában a [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) parancs használatával telepítse újra a projektet, és cserélje le a`<APP_NAME>`t az alkalmazás nevére.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Ahogy az előző rövid útmutatóban is, az újratelepített függvény teszteléséhez használjon böngészőt vagy CURL-t.

    # <a name="browser"></a>[Böngésző](#tab/browser)
    
    Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Functions`. A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

    ![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Futtassa [`curl`](https://curl.haxx.se/) a **MEGhívási URL-címmel**, és illessze be a paramétert `&name=Functions`. A parancs kimenetének a "Hello functions" szövegnek kell lennie.
    
    ![A függvény kimenete az Azure-on a CURL használatával fut](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Az előző szakaszban leírtak szerint vizsgálja meg újra a tárolási várólistát annak ellenőrzéséhez, hogy az tartalmazza-e a várólistára írt új üzenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett, a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>További lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. Most már többet is megtudhat a függvények parancssorból való fejlesztéséről a Core Tools és az Azure CLI használatával:

+ [Azure Functions Core Tools használata](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Példák a teljes körű függvények projektjeire a alkalmazásban C# ](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Functions C# fejlesztői dokumentáció](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Példák a teljes körű függvények projektjeire a JavaScriptben](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions JavaScript fejlesztői útmutató](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Példák a teljes körű Function-projektekre az írógéppel](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions írógéppel – fejlesztői útmutató](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Példák a Pythonban elérhető teljes körű függvények projektjeire](/samples/browse/?products=azure-functions&languages=python).

+ [Azure Functions Python fejlesztői útmutató](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Példák a PowerShellben elérhető teljes függvények projektjeire](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions PowerShell fejlesztői útmutató](functions-reference-powershell.md) 
::: zone-end
+ [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

+ [Functions – díjszabási oldal](https://azure.microsoft.com/pricing/details/functions/)

+ [A fogyasztási terv költségeinek becslése](functions-consumption-costs.md) 
