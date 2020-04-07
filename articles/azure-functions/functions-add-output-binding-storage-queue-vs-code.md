---
title: Az Azure Functions csatlakoztatása az Azure Storage szolgáltatáshoz a Visual Studio-kód használatával
description: Ismerje meg, hogyan csatlakoztathatja az Azure Functionst egy Azure Storage-várólistához egy kimeneti kötés hozzáadásával a Visual Studio Code projekthez.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c32f98fc1b3de98592f8e7ceb43c17aa8a9049f7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673470"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Az Azure Functions csatlakoztatása az Azure Storage szolgáltatáshoz a Visual Studio-kód használatával

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan használhatja a Visual Studio-kódot az [előző rövid útmutató cikkben](functions-create-first-function-vs-code.md) létrehozott függvény azure Storage-hoz való csatlakoztatása. A függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure Queue storage-várólistában lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a Functions a kötött szolgáltatás eléréséhez használ. A könnyebb, használja a Storage-fiók, amely a függvényalkalmazással létrehozott. A fiókhoz való kapcsolat már a neve `AzureWebJobsStorage`s. alkalmazásbeállításban van tárolva.  

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

A cikk megkezdése előtt meg kell felelnie az alábbi követelményeknek:

* Telepítse az [Azure Storage bővítményt a Visual Studio-kódhoz.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)

* Telepítse [az Azure Storage Explorert](https://storageexplorer.com/). A Storage Explorer egy olyan eszköz, amellyel a kimeneti kötés által létrehozott várólistaüzeneteket fogja vizsgálni. A Storage Explorer macOS, Windows és Linux alapú operációs rendszereken támogatott.

::: zone pivot="programming-language-csharp"
* Telepítse a [.NET Core CLI eszközöket](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Hajtsa végre a [Visual Studio-kód rövid útmutatójának 1.](functions-create-first-function-vs-code.md) 

Ez a cikk feltételezi, hogy már be van jelentkezve az Azure-előfizetésbe a Visual Studio-kódból. A parancspalettáról `Azure: Sign In` való futással jelentkezhet be. 

## <a name="download-the-function-app-settings"></a>A függvényalkalmazás beállításainak letöltése

Az [előző rövid útmutató cikkben](functions-create-first-function-vs-code.md)létrehozott egy függvényalkalmazást az Azure-ban a szükséges storage-fiókkal együtt. A fiók kapcsolati karakterlánca biztonságosan tárolódik az Azure-beli alkalmazásbeállításokban. Ebben a cikkben üzeneteket ír egy storage-várólistába ugyanabban a fiókban. Ha a funkció helyi futtatásakor szeretne csatlakozni a tárfiókhoz, le kell töltenie az alkalmazásbeállításokat a local.settings.json fájlba. 

1. Nyomja le az F1 billentyűt a parancspaletta `Azure Functions: Download Remote Settings....`megnyitásához, majd keresse meg és futtassa a parancsot . 

1. Válassza ki az előző cikkben létrehozott függvényalkalmazást. A meglévő helyi beállítások felülírásához válassza az Igen lehetőséget az **összeshez.** 

    > [!IMPORTANT]  
    > Mivel titkos kulcsokat tartalmaz, a local.settings.json fájl soha nem lesz közzétéve, és ki van zárva a forrásellenőrzésből.

1. Másolja az `AzureWebJobsStorage`értéket , amely a Storage-fiók kapcsolati karakterláncának kulcsa. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel várólista-tároló kimeneti kötést használ, a projekt futtatása előtt telepítenie kell a Storage kötésbővítményt. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

A projekt [bővítménycsomagok](functions-bindings-register.md#extension-bundles)használatára lett konfigurálva, amely automatikusan telepíti a bővítménycsomagok előre meghatározott készletét. 

A bővítménykötegek engedélyezve vannak a project gyökerében található host.json fájlban, amely a következőképpen néz ki:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

A HTTP és az időzítő eseményindítók kivételével a kötések bővítménycsomagokként vannak megvalósítva. Futtassa a következő [dotnet add package](/dotnet/core/tools/dotnet-add-package) parancsot a Terminál ablakban a Storage bővítménycsomag projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Most hozzáadhatja a tárolási kimenetkötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A Függvényekben minden kötéstípushoz a `direction`, `type`és egy egyedi `name` szükséges a function.json fájlban. Az attribútumok definiálásának módja a függvényalkalmazás nyelvétektől függ.

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

A kötés definiálása után a `name` kötés segítségével attribútumként érheti el a függvényaláírásban. Egy kimeneti kötés használatával nem kell használni az Azure Storage SDK-kódot a hitelesítéshez, a várólista-referencia beszerzése vagy adatok írása. A Functions futásidejű és a várólista kimeneti kötése ezeket a feladatokat elvégezheti.

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

A kimeneti kötés első használatakor a Functions futásidejű új **várólistát** hoz létre a tárfiókban. A Storage Explorer segítségével ellenőrizze, hogy a várólista az új üzenettel együtt jött-e létre.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>A Storage Explorer csatlakoztatása a fiókjához

Hagyja ki ezt a szakaszt, ha már telepítette az Azure Storage Explorert, és csatlakoztatta az Azure-fiókjához.

1. Futtassa az [Azure Storage Explorer] eszközt, válassza a bal oldali csatlakozás ikont, és válassza **a Fiók hozzáadása**lehetőséget.

    ![Azure-fiók hozzáadása a Microsoft Azure Storage Explorer hez](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. A **Csatlakozás** párbeszédpanelen válassza az **Azure-fiók hozzáadása**lehetőséget, válassza ki az **Azure-környezetet,** és válassza **a Bejelentkezés...** lehetőséget. 

    ![Jelentkezzen be az Azure-fiókjába](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Miután sikeresen bejelentkezett a fiókjába, láthatja a fiókjához társított összes Azure-előfizetést.

### <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studio-kódban nyomja le az F1 billentyűt a `Azure Storage: Open in Storage Explorer` parancspaletta megnyitásához, majd keresse meg és futtassa a parancsot, és válassza ki a Tárfiók nevét. A tárfiók megnyílik az Azure Storage Explorerben.  

1. Bontsa ki az **Üzenetsorok** csomópontot, majd válassza ki az **outqueue** nevű üzenetsort. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha az alapértelmezett *Azure*`name` értékkel hívta meg a függvényt, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

    ![Várólista-üzenet az Azure Storage Explorer ben](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a funkciót, küldjön egy másik kérelmet, és megjelenik egy új üzenet a várólistában.  

Most itt az ideje, hogy újra közzétegye a frissített függvényalkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újratelepítése és ellenőrzése

1. A Visual Studio Code alkalmazásban nyomja le az F1 billentyűt a parancspaletta megnyitásához. A parancspalettán keresse `Azure Functions: Deploy to function app...`meg és válassza a lehetőséget.

1. Válassza ki az első cikkben létrehozott függvényalkalmazást. Mivel a projektet ugyanabba az alkalmazásba helyezi át, a **Telepítés gombra** választva elvetheti a fájlok felülírásával kapcsolatos figyelmeztetést.

1. A telepítés befejezése után ismét használhatja a cURL-t vagy a böngészőt az újratelepített függvény teszteléséhez. A hogy korábban is, fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez, ahogy az a következő példában is:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ismét [tekintse meg az üzenetet a tárolóvárólistában,](#examine-the-output-queue) és ellenőrizze, hogy a kimeneti kötés ismét új üzenetet hoz-e létre a várólistában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. Az erőforrások *erőforráscsoportokba* vannak csoportosítva, és az adott csoport törlésével a benne lévő összes erőforrás törölhető.

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP-aktivált függvényt, hogy adatokat írjon egy tárolási várólistába. Mostantól többet is megtudhat a Funkciók visual studio-kód használatával történő fejlesztéséről:

+ [Azure-függvények fejlesztése a Visual Studio-kód használatával](functions-develop-vs-code.md)
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
+ [Az Azure Functions elindítja és kötéseket.](functions-triggers-bindings.md)
+ [Funkciók árképzési lapja](https://azure.microsoft.com/pricing/details/functions/)
+ [A Felhasználási terv költségeinek becslése.](functions-consumption-costs.md)
