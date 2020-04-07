---
title: Az Azure Functions csatlakoztatása az Azure Storage szolgáltatáshoz parancssori eszközökkel
description: Ismerje meg, hogyan csatlakoztathatja az Azure Functionst egy Azure Storage-várólistához egy kimeneti kötés hozzáadása a parancssori projekthez.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f9d9573523083b6355f423b7b3db94b795d8657f
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673323"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Az Azure Functions csatlakoztatása az Azure Storage szolgáltatáshoz parancssori eszközökkel

Ebben a cikkben integrálja az Azure Storage-várólistát [az előző rövid útmutatóban](functions-create-first-azure-function-azure-cli.md)létrehozott függvény- és tárfiókkal. Ezt az integrációt egy *kimeneti kötés* használatával érheti el, amely http-kérelemből adatokat ír a várólistában lévő üzenetekbe. A cikk végrehajtása nem jár további költségekkel az előző rövid útmutató néhány USD centje in túl. Ha többet szeretne megtudni a kötésekről, olvassa el az [Azure Functions eseményindítók és kötések fogalmait.](functions-triggers-bindings.md)

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, ki kell töltenie a [rövid útmutató: Hozzon létre egy Azure Functions projektet a parancssorból](functions-create-first-azure-function-azure-cli.md). Ha már megtisztította az erőforrásokat a cikk végén, ismét hajtsa végre a lépéseket a függvényalkalmazás és a kapcsolódó erőforrások újbóli létrehozásához az Azure-ban.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

A kötések részleteiről az [Azure Functions elindítja és kötéseket indítja el](functions-triggers-bindings.md) és ismerteti a sorok [kimeneti konfigurációját.](functions-bindings-storage-queue-output.md#configuration)

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötés használatához

A várólista-kötés definiálva most frissítheti `msg` a függvényt, hogy megkapja a kimeneti paramétert, és üzeneteket írjon a várólistába.

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

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Vegye figyelembe, hogy *nem* kell kódot írnia a hitelesítéshez, a várólista-referenciák beszerzéséhez vagy az adatok írásához. Mindezek az integrációs feladatok kényelmesen kezelik az Azure Functions futásidejű és a várólista kimeneti kötés.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage várólistájában

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>A projekt újratelepítése az Azure-ba

Most, hogy helyileg ellenőrizte, hogy a függvény üzenetet írt az Azure Storage-várólistába, újratelepítheti a projektet az Azure-ban futó végpont frissítéséhez.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
A *LocalFunctionsProj* mappában [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) a paranccsal újratelepítheti a projektet, és lecserélheti`<APP_NAME>` az alkalmazás nevét.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

A helyi projekt mappában a következő Maven paranccsal tegye közzé újra a projektet:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Ellenőrzés az Azure-ban

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
