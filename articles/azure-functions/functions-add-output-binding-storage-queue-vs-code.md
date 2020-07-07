---
title: Azure Functions összekötése az Azure Storage-ba a Visual Studio Code használatával
description: Megtudhatja, hogyan csatlakoztathatja a Azure Functionst egy Azure Storage-várólistához a Visual Studio Code projekthez tartozó kimeneti kötés hozzáadásával.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: d41c018e07f792fd0af4027229449d8352aa6c55
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849977"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Azure Functions összekötése az Azure Storage-ba a Visual Studio Code használatával

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan használható a Visual Studio Code az előző rövid útmutató [cikkében](functions-create-first-function-vs-code.md) létrehozott függvény Azure Storage-ba való összekapcsolásához. Az ehhez a függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure üzenetsor-tárolási várólistán lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A könnyebb kezelhetőség érdekében használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy nevű alkalmazás-beállításban van tárolva `AzureWebJobsStorage` .  

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

A cikk elindítása előtt a következő követelményeknek kell megfelelnie:

* Telepítse az [Azure Storage-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)-hoz.

* Telepítse a [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer egy eszköz, amellyel megvizsgálhatja a kimeneti kötés által létrehozott üzenetsor-üzeneteket. A Storage Explorer macOS-, Windows-és Linux-alapú operációs rendszereken támogatott.

::: zone pivot="programming-language-csharp"
* [A .net Core parancssori felülete eszközök](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)telepítése.
::: zone-end

* Hajtsa végre a [Visual Studio Code](functions-create-first-function-vs-code.md)rövid útmutatójának 1. részében ismertetett lépéseket. 

Ez a cikk azt feltételezi, hogy már bejelentkezett az Azure-előfizetésbe a Visual Studio Code-ból. `Azure: Sign In`A parancs futtatásával a parancssorból is bejelentkezhet. 

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

Az [előző](functions-create-first-function-vs-code.md)rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az alkalmazás beállításait a fájl local.settings.js. 

1. Nyomja le az F1 billentyűt a parancs palettájának megnyitásához, majd keresse meg és futtassa a parancsot `Azure Functions: Download Remote Settings....` . 

1. Válassza ki az előző cikkben létrehozott Function alkalmazást. A meglévő helyi beállítások felülírásához válassza az **Igen** lehetőséget. 

    > [!IMPORTANT]  
    > Mivel titkokat tartalmaz, a fájl local.settings.jssoha nem kerül közzétételre, és ki van zárva a verziókövetés alól.

1. Másolja ki az értéket `AzureWebJobsStorage` , amely a Storage-fiókhoz tartozó kapcsolatok karakterlánc-értékének kulcsa. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel a várólista-tároló kimeneti kötését használja, a projekt futtatása előtt telepítenie kell a Storage-kötések bővítményt. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

A projekt a [bővítmények](functions-bindings-register.md#extension-bundles)használatára lett konfigurálva, amely automatikusan telepíti a bővítmények előre meghatározott készletét. 

A bővítmény-csomagok használata engedélyezve van a projekt gyökérkönyvtárában lévő host.jsfájlban, amely a következőképpen jelenik meg:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. Futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a terminál ablakban a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions szolgáltatásban minden típusú kötéshez a, `direction` `type` , és egyedi `name` érték szükséges a fájl function.js. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés meghatározása után a `name` kötést használhatja a függvény aláírása attribútumként való eléréséhez. Kimeneti kötés használatával nem szükséges az Azure Storage SDK-kód használata hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy az adatírás. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

A **rendszer létrehoz** egy új üzenetsor-várólistát a Storage-fiókban a functions futtatókörnyezetben a kimeneti kötés első használatakor. A Storage Explorer segítségével ellenőrizheti, hogy a várólista létrejött-e az új üzenettel együtt.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>A Storage Explorer csatlakoztatása a fiókjához

Hagyja ki ezt a szakaszt, ha már telepítette Azure Storage Explorer és csatlakoztatta azt az Azure-fiókjához.

1. Futtassa a [Azure Storage Explorer] eszközt, válassza a bal oldali csatlakozási ikont, majd válassza a **fiók hozzáadása**lehetőséget.

    ![Azure-fiók hozzáadása a Microsoft Azure Storage Explorerhoz](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. A **kapcsolat** párbeszédpanelen válassza az **Azure-fiók hozzáadása**lehetőséget, válassza ki az **Azure-környezetet**, és válassza a **Bejelentkezés**lehetőséget. 

    ![Jelentkezzen be az Azure-fiókjába](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Miután sikeresen bejelentkezett a fiókjába, megjelenik a fiókjához társított összes Azure-előfizetés.

### <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához, majd keresse meg és futtassa a parancsot, `Azure Storage: Open in Storage Explorer` és válassza ki a Storage-fiók nevét. A Storage-fiók a Azure Storage Explorerban nyílik meg.  

1. Bontsa ki az **Üzenetsorok** csomópontot, majd válassza ki az **outqueue** nevű üzenetsort. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha az alapértelmezett *Azure*`name` értékkel hívta meg a függvényt, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

    ![Üzenetsor-üzenet látható Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a függvényt, küldjön egy másik kérést, és megjelenik egy új üzenet a várólistában.  

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újbóli üzembe helyezése és ellenőrzése

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Deploy to function app...` .

1. Válassza ki az első cikkben létrehozott Function alkalmazást. Mivel a projekt ugyanarra az alkalmazásba való újbóli üzembe helyezését végzi, válassza a **telepítés** lehetőséget a fájlok felülírásával kapcsolatos figyelmeztetés elvetéséhez.

1. Az üzembe helyezés befejezése után a cURL vagy egy böngésző használatával tesztelheti az újratelepített függvényt. Ahogy korábban is, fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez, az alábbi példában látható módon:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ismét [tekintse meg az üzenetet a Storage-várólistán](#examine-the-output-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Az Azure-ban az *erőforrások* a Function apps, a functions, a Storage-fiókok és így tovább. *Erőforráscsoportokba*vannak csoportosítva, és a csoport törlésével törölheti a csoport összes elemét.

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. Most már többet is megtudhat a függvények a Visual Studio Code használatával történő fejlesztéséről:

+ [Azure Functions fejlesztése a Visual Studio Code használatával](functions-develop-vs-code.md)
::: zone pivot="programming-language-csharp"  
+ [Példák a teljes körű függvények projektjeire a C#-ban](/samples/browse/?products=azure-functions&languages=csharp).
+ [Azure Functions C# – fejlesztői dokumentáció](functions-dotnet-class-library.md)  
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
+ [Azure functions eseményindítók és kötések](functions-triggers-bindings.md).
+ [Functions – díjszabási oldal](https://azure.microsoft.com/pricing/details/functions/)
+ A [fogyasztási terv költségeinek becslése](functions-consumption-costs.md) .
