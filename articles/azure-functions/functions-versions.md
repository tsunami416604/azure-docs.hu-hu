---
title: Azure Functions futtatókörnyezet-verziók áttekintése
description: Azure Functions támogatja a futtatókörnyezet több verzióját. Megtudhatja, hogy miben különböznek egymástól, és hogyan választhatja ki az Önnek legmegfelelőbbet.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 53da5869b4768c95fd225fb15db60f4301e537d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226540"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions futtatókörnyezet-verziók áttekintése

Az Azure Functions futtatókörnyezet főverziói a futtatókörnyezet alapjául szolgáló .NET-verzióhoz kapcsolódnak. A következő táblázat a futtatókörnyezet aktuális verzióját, a kiadási szintet és a kapcsolódó .NET-verziót mutatja. 

| Futtatókörnyezet verziója | <sup>1</sup> . kiadási szint | .NET-verzió | 
| --------------- | ------------- | ------------ |
| 3.x  | előzetes verzió | .NET Core 3. x | 
| 2. x | FE | .NET Core 2.2 |
| 1. x | <sup>2</sup> . ga | .NET-keretrendszer 4,6<sup>3</sup> |

<sup>1</sup> A GA-kiadások éles környezetben is támogatottak.   
<sup>2</sup> Az 1. x verzió karbantartási módban van. A fejlesztéseket csak a későbbi verziókban biztosítjuk.   
<sup>3</sup> A csak a Azure Portal és a Windows rendszerű számítógépeken való helyi fejlesztést támogatja.

>[!NOTE]  
> A functions Runtime 3. x verziója előzetes verzióban érhető el, és éles környezetekben nem támogatott. A 3. x verzió kipróbálásával kapcsolatos további információkért tekintse meg [ezt a közleményt](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

Ez a cikk a különböző verziók közötti különbségeket ismerteti, hogyan hozhatja létre az egyes verziókat, és hogyan módosíthatja a verziókat.

## <a name="languages"></a>Nyelvek

A 2. x verziótól kezdődően a futtatókörnyezet nyelvi bővíthetőségi modellt használ, és a Function app összes funkciójának ugyanazzal a nyelvvel kell rendelkeznie. A functions alkalmazásban a függvények nyelve az alkalmazás létrehozásakor van kiválasztva, és a [\_worker\_RUNTIME](functions-app-settings.md#functions_worker_runtime) beállításban található functions. 

Azure Functions 1. x kísérleti nyelvek nem használhatják az új modellt, így azok nem támogatottak 2. x-ben. A következő táblázat azt mutatja be, hogy mely programozási nyelvek támogatottak az egyes futtatókörnyezet-verziókban.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információk: [Támogatott nyelvek](supported-languages.md).

## <a name="creating-1x-apps"></a>Futtatás adott verzión

Alapértelmezés szerint a Azure Portal és az Azure CLI-ben létrehozott Function apps a 2. x verzióra van beállítva. Ha lehetséges, használja ezt a futásidejű verziót. Ha szükséges, továbbra is futtathat egy Function alkalmazást az 1. x verzió futtatókörnyezetén. A futásidejű verziót csak a Function app létrehozása után, de a függvények hozzáadása előtt is módosíthatja. Ha szeretné megtudni, hogyan rögzítheti a futásidejű verziót 1. x-re, tekintse meg [a jelenlegi futtatókörnyezet-verzió megtekintése és frissítése](set-runtime-version.md#view-and-update-the-current-runtime-version)című témakört.

Az előzetes verzióban elérhető futtatókörnyezet 3. x verziójára is frissíthet. Ezt akkor hajtsa végre, ha a functions szolgáltatást a .NET Core 3. x verzióban kell tudnia futtatni. A 3. x verzióra történő frissítéssel kapcsolatos további információkért lásd: [az aktuális futtatókörnyezet verziójának megtekintése és frissítése](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migrálás 1. x verzióról a későbbi verziókra

Dönthet úgy, hogy áttelepít egy meglévő alkalmazást, hogy az 1. x futtatókörnyezetet használja helyette a 2. x verzió használatára. A szükséges módosítások többsége a nyelvi futtatókörnyezet változásaihoz kapcsolódik, például az C# API-változásokat a .NET-keretrendszer 4,7 és a .net Core 2 között. Azt is ellenőriznie kell, hogy a kód és a kódtárak kompatibilisek-e a kiválasztott nyelvi futtatókörnyezettel. Végezetül jegyezze fel a trigger, a kötések és az alábbiakban kiemelt funkciók változásait. A legjobb áttelepítési eredményekhez hozzon létre egy új Function alkalmazást a 2. x verzióhoz, és a meglévő 1-es verziójú. x függvény kódját adja meg az új alkalmazásnak.  

### <a name="changes-in-triggers-and-bindings"></a>Az eseményindítók és kötések változásai

A 2. x verzióhoz telepítenie kell a bővítményeket az alkalmazásban szereplő függvények által használt egyes eseményindítók és kötések számára. Az egyetlen kivétel a HTTP-és időzítő-eseményindítók esetében, amelyek nem igényelnek kiterjesztést.  További információ: [kötési bővítmények regisztrálása és telepítése](./functions-bindings-register.md).

A függvények `function.json` vagy attribútumai is módosultak a verziók között. Például az Event hub `path` tulajdonsága már `eventHubName`. Az egyes kötésekhez kapcsolódó dokumentációra mutató hivatkozásokat a [meglévő kötési táblázat](#bindings) tartalmazza.

### <a name="changes-in-features-and-functionality"></a>A szolgáltatások és a funkciók változásai

Néhány szolgáltatás, amely az új verzióban is el lett távolítva, frissítve vagy lecserélve. Ez a szakasz az 1. x verzió használata után a 2. x verzióban megjelenő módosításokat részletezi.

A 2. x verzióban a következő módosítások történtek:

* A HTTP-végpontok hívásához szükséges kulcsokat az Azure Blob Storage-ban mindig titkosítva tárolja a rendszer. Az 1. x verzióban a kulcsok tárolása az Azure file Storage-ban alapértelmezett. Ha az 1. x verzióról a 2. x verzióra frissít egy alkalmazást, a file Storage-ban lévő meglévő titkos kulcsok alaphelyzetbe állnak.

* A 2. x verziójú futtatókörnyezet nem tartalmaz beépített támogatást a webhook-szolgáltatók számára. Ez a változás a teljesítmény javítása érdekében történt. A HTTP-eseményindítók továbbra is használhatók webhookok végpontként.

* A gazdagép konfigurációs fájljának (host. JSON) üresnek kell lennie, vagy a karakterláncnak `"version": "2.0"`nak kell lennie.

* A figyelés javítása érdekében a webjobs-irányítópultot a portálon, amely a [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) beállítást használta, a [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) beállítást használó Azure Application Insights helyére kerül. További információ: [Azure functions figyelése](functions-monitoring.md).

* A Function alkalmazás összes függvényének ugyanazt a nyelvet kell megosztania. Function-alkalmazás létrehozásakor ki kell választania egy futásidejű veremet az alkalmazáshoz. A futásidejű verem értékét az Alkalmazásbeállítások [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) értéke határozza meg. Ez a követelmény a lábnyom és az indítási idő javítására lett hozzáadva. Helyi fejlesztés esetén ezt a beállítást a [Local. Settings. JSON fájlban](functions-run-local.md#local-settings-file)is fel kell venni.

* Egy App Service csomagban lévő függvények alapértelmezett időtúllépése 30 percre módosul. A Host. JSON fájl [functionTimeout](functions-host-json.md#functiontimeout) beállításával manuálisan módosíthatja az időtúllépést a korlátlan értékre.

* A HTTP-párhuzamosságok szabályozása alapértelmezés szerint a használati terv funkcióival valósul meg, alapértelmezés szerint a 100 egyidejű kérések száma. Ezt a Host. JSON fájlban lévő [`maxConcurrentRequests`](functions-host-json.md#http) beállításban módosíthatja.

* A [.net Core korlátozásai](https://github.com/Azure/azure-functions-host/issues/3414)miatt a rendszer F# eltávolította a script (. fsx) függvények támogatását. A F# lefordított függvények (. FS) továbbra is támogatottak.

* Event Grid trigger-webhookok URL-formátuma `https://{app}/runtime/webhooks/{triggerName}`ra módosult.

### <a name="migrating-a-locally-developed-application"></a>Helyileg fejlesztett alkalmazások áttelepítése

Előfordulhat, hogy az 1. x verzióban helyileg fejlesztett, meglévő Function app-projekteket használ. A 2. x verzióra való frissítéshez létre kell hoznia egy helyi function alkalmazás-projektet a 2. x verzióra, és a meglévő kódot az új alkalmazásba kell bejelentkeznie. Manuálisan frissítheti a meglévő projektet és kódot, a "helyi" frissítést. Az 1. x és a 2. x verzió között azonban még számos további újításra lehet szükség. C# A hibakeresési objektum például `TraceWriter`ról `ILogger`ra módosult. Egy új 2-es verziójú. x projekt létrehozásával a legújabb 2. x verziójú sablonok alapján kezdi meg a frissített funkciókat.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio Runtime-verziók

A Visual Studióban a projekt létrehozásakor kiválaszthatja a futtatókörnyezet verzióját. Azure Functions Tools for Visual Studio támogatja a főbb futtatókörnyezet-verziókat is. A megfelelő verzió a projekt beállításai alapján történő hibakeresés és közzététel során használatos. A verzió beállításait a `.csproj` fájlban a következő tulajdonságok határozzák meg:

##### <a name="version-1x"></a>1\. x verzió

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>2-es verzió. x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

A projekt hibakeresése vagy közzététele során a rendszer a futtatókörnyezet megfelelő verzióját használja.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS kód és Azure Functions Core Tools

[Azure functions Core Tools](functions-run-local.md) a parancssori fejlesztéshez, valamint a Visual Studio Code-hoz készült [Azure functions-bővítményhez](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) is használható. A 2. x verzióval való fejlesztéshez telepítse a Core Tools 2. x verzióját. Az 1. x verzió fejlesztéséhez a Core Tools 1. x verziója szükséges. További információ: [install the Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

A Visual Studio Code fejlesztéséhez szükség lehet a `azureFunctions.projectRuntime` felhasználói beállításának frissítésére is, hogy megfeleljen a telepített eszközök verziójának.  Ezzel a beállítással a Function app létrehozásakor használt sablonokat és nyelveket is frissíti.

### <a name="changing-version-of-apps-in-azure"></a>Alkalmazások verziójának módosítása az Azure-ban

Az Azure-ban közzétett alkalmazások által használt functions futtatókörnyezet verzióját a [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) alkalmazás-beállítás határozza meg. `~2` érték a 2. x verziójú futtatókörnyezetet célozza meg, és `~1` az 1. x verziójú futtatókörnyezetet célozza meg. Ne módosítsa ezt a beállítást, mert valószínű, hogy más alkalmazás-beállításokat módosítanak, és a függvények programkódjának módosításai valószínűleg szükségesek. A Function app különböző futtatókörnyezet-verzióra való áttelepítésének ajánlott módjáról a következő témakörben talál további információt: [Azure functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md).

## <a name="bindings"></a>Kötések

A 2. x verziótól kezdődően a futtatókörnyezet egy új [kötési bővíthetőségi modellt](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) használ, amely a következő előnyöket kínálja:

* Harmadik féltől származó kötési bővítmények támogatása.

* Futtatókörnyezet és kötések leválasztása. Ez a módosítás lehetővé teszi, hogy a kötési bővítmények verziója és kiadása egymástól függetlenül történjen. Például megadhatja, hogy a bővítmény olyan verziójára frissítsen, amely egy mögöttes SDK újabb verziójára támaszkodik.

* Egy világosabb végrehajtási környezet, ahol csak a használatban lévő kötések ismertek és töltődnek be a futtatókörnyezet.

A HTTP-és időzítő-eseményindítók kivételével az összes kötést explicit módon fel kell venni a Function app-projektbe, vagy regisztrálni kell a portálon. További információ: [kötési bővítmények regisztrálása](./functions-bindings-expressions-patterns.md).

A következő táblázat bemutatja, hogy az egyes futásidejű verziók milyen kötéseket támogatnak.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következőket:

* [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
* [A Azure Functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md)
* [Kibocsátási megjegyzések](https://github.com/Azure/azure-functions-host/releases)
