---
title: Azure Functions futtatókörnyezet-verziók áttekintése
description: Azure Functions támogatja a futtatókörnyezet több verzióját. Megtudhatja, hogy miben különböznek egymástól, és hogyan választhatja ki az Önnek legmegfelelőbbet.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: 6641461e63d7c9452351f3b0b99a274d2714a92e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208102"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions futtatókörnyezet-verziók áttekintése

A Azure Functions jelenleg a futásidejű gazdagép három verzióját támogatja: 1. x, 2. x és 3. x. Az éles környezetekben mindhárom verzió támogatott.  

> [!IMPORTANT]
> Az 1. x verzió karbantartási módban van, és csak a Azure Portal vagy helyileg a Windows rendszerű számítógépeken támogatja a fejlesztést. A fejlesztéseket csak a későbbi verziókban biztosítjuk. 

Ez a cikk a különböző verziók közötti különbségeket ismerteti, hogyan hozhatja létre az egyes verziókat, és hogyan módosíthatja a verziókat.

## <a name="languages"></a>Nyelvek

A 2. x verziótól kezdődően a futtatókörnyezet nyelvi bővíthetőségi modellt használ, és a Function app összes funkciójának ugyanazzal a nyelvvel kell rendelkeznie. A functions alkalmazásban a függvények nyelvét a rendszer az alkalmazás létrehozásakor választja ki, és a [functions \_ Worker \_ RUNTIME](functions-app-settings.md#functions_worker_runtime) beállításban tartja karban. 

A következő táblázat azt mutatja be, hogy mely programozási nyelvek támogatottak az egyes futtatókörnyezet-verziókban.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információ: [támogatott nyelvek](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Futtatás adott verzión

Alapértelmezés szerint a Azure Portal és az Azure CLI-ben létrehozott Function apps a 3. x verzióra van beállítva. Igény szerint módosíthatja ezt a verziót. A futásidejű verziót csak 1. x értékre módosíthatja, miután létrehozta a Function alkalmazást, de a függvények hozzáadása előtt is.  A 2. x és 3. x közötti váltás akkor is engedélyezett, ha a functions alkalmazással rendelkezik, de az új alkalmazásban való tesztelés még ajánlott.

## <a name="migrating-from-1x-to-later-versions"></a>Migrálás 1. x verzióról a későbbi verziókra

Dönthet úgy is, hogy egy meglévő, az 1. x verziójú futtatókörnyezet használatára írt alkalmazást telepít át, hogy ehelyett egy újabb verziót használjon. A szükséges módosítások többsége a nyelvi futtatókörnyezet változásaihoz kapcsolódik, például C# API-változások a .NET-keretrendszer 4,7 és a .NET Core között. Azt is ellenőriznie kell, hogy a kód és a kódtárak kompatibilisek-e a kiválasztott nyelvi futtatókörnyezettel. Végezetül jegyezze fel a trigger, a kötések és az alábbiakban kiemelt funkciók változásait. A legjobb áttelepítési eredményekhez hozzon létre egy új Function-alkalmazást egy új verzióban, és a meglévő 1. x-es verziójú függvény kódját adja meg az új alkalmazásnak.  

Habár lehetséges, hogy a "helyben" történő frissítést manuálisan frissíti az alkalmazás konfigurációját, az 1. x-ről a magasabb verzióra. A C# nyelvben például a hibakeresési objektum a verzióról a verzióra módosul `TraceWriter` `ILogger` . Egy új 3. x-es verziójú projekt létrehozásával a legújabb 3. x verziójú sablonok alapján kezdi meg a frissített funkciókat.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Az eseményindítók és kötések változásai az 1. x verzió után

A 2. x verziótól kezdődően telepítenie kell a bővítményeket az alkalmazás funkciói által használt egyes eseményindítók és kötések számára. Az egyetlen kivétel a HTTP-és időzítő-eseményindítók esetében, amelyek nem igényelnek kiterjesztést.  További információ: [kötési bővítmények regisztrálása és telepítése](./functions-bindings-register.md).

A függvények *function.js* vagy attribútumai is módosulnak a verziók között. Az Event hub `path` tulajdonság például most `eventHubName` . Az egyes kötésekhez kapcsolódó dokumentációra mutató hivatkozásokat a [meglévő kötési táblázat](#bindings) tartalmazza.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>A szolgáltatások és a funkciók változásai a 1. x verzió után

Néhány szolgáltatást eltávolítottak, frissítették vagy cseréltek az 1. x verzió után. Ez a szakasz az 1. x verzió használata után a későbbi verziókban megjelenő módosításokat részletezi.

A 2. x verzióban a következő módosítások történtek:

* A HTTP-végpontok hívásához szükséges kulcsokat az Azure Blob Storage-ban mindig titkosítva tárolja a rendszer. Az 1. x verzióban a kulcsok alapértelmezett tárolása az Azure file Storage-ban történt. Ha az 1. x verzióról a 2. x verzióra frissít egy alkalmazást, a file Storage-ban lévő meglévő titkos kulcsok alaphelyzetbe állnak.

* A 2. x verziójú futtatókörnyezet nem tartalmaz beépített támogatást a webhook-szolgáltatók számára. Ez a változás a teljesítmény javítása érdekében történt. A HTTP-eseményindítók továbbra is használhatók webhookok végpontként.

* A gazdagép konfigurációs fájljának (host.json) üresnek vagy sztringnek kell lennie `"version": "2.0"` .

* A figyelés javítása érdekében a webjobs-irányítópultot a portálon, amely a beállítást használta, az [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) Azure Application Insights, amely a [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) beállítást használja. További információ: [Azure functions figyelése](functions-monitoring.md).

* A Function alkalmazás összes függvényének ugyanazt a nyelvet kell megosztania. Function-alkalmazás létrehozásakor ki kell választania egy futásidejű veremet az alkalmazáshoz. A futásidejű verem [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) értékét az Alkalmazásbeállítások értéke határozza meg. Ez a követelmény a lábnyom és az indítási idő javítására lett hozzáadva. Helyi fejlesztés esetén ezt a beállítást a [ fájllocal.settings.js](functions-run-local.md#local-settings-file)is tartalmaznia kell.

* Egy App Service csomagban lévő függvények alapértelmezett időtúllépése 30 percre módosul. Manuálisan is módosíthatja az időtúllépést a host.js[functionTimeout](functions-host-json.md#functiontimeout) beállításával.

* A HTTP-párhuzamosságok szabályozása alapértelmezés szerint a használati terv funkcióival valósul meg, alapértelmezés szerint a 100 egyidejű kérések száma. Ezt a [`maxConcurrentRequests`](functions-host-json.md#http) host.jsfájljában lévő beállításban módosíthatja.

* A [.net Core korlátozásai](https://github.com/Azure/azure-functions-host/issues/3414)miatt a F # script (. fsx) függvények támogatása el lett távolítva. A lefordított F # függvények (. FS) továbbra is támogatottak.

* A Event Grid trigger-webhookok URL-formátuma megváltozott a következőre: `https://{app}/runtime/webhooks/{triggerName}` .

## <a name="migrating-from-2x-to-3x"></a>Migrálás 2. x és 3. x között

Azure Functions 3. x verzió a 2. x verzióval kompatibilis.  Számos alkalmazásnak képesnek kell lennie arra, hogy a kód módosítása nélkül is biztonságosan frissítsen 3. x-re.  A 3. x-re való áttérés javasolt, hogy az éles alkalmazások főverziójának módosítása előtt futtasson kiterjedt teszteket.

### <a name="breaking-changes-between-2x-and-3x"></a>A 2. x és 3. x közötti változások megszakítása

A 2. x alkalmazás 3. x verzióra való frissítése előtt a következő módosításokat kell figyelembe vennie:

#### <a name="javascript"></a>JavaScript

* `context.done`A vagy a visszatérési értékekkel hozzárendelt kimeneti kötések mostantól ugyanúgy viselkednek, mint a beállításban `context.bindings` .

* Az időzítő trigger objektum camelCase helyett a PascalCase

* Az Event hub által aktivált, bináris formátumú függvények a `dataType` helyett egy tömböt kapnak `binary` `string` .

* A HTTP-kérések adattartalma már nem érhető el a-n keresztül `context.bindingData.req` .  Továbbra is elérhető bemeneti paraméterként, `context.req` és a-ben `context.bindings` .

* A Node.js 8 már nem támogatott, és nem hajtható végre 3. x függvényben.

#### <a name="net"></a>.NET

* A [szinkron kiszolgáló műveletei alapértelmezés szerint le vannak tiltva](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Alkalmazások verziójának módosítása az Azure-ban

Az Azure-ban közzétett alkalmazások által használt functions Runtime verzióját az Alkalmazásbeállítások írják elő [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) . A következő fő futásidejű verziók értékei támogatottak:

| Érték | Futtatókörnyezet célja |
| ------ | -------- |
| `~3` | 3. x |
| `~2` | 2. x |
| `~1` | 1. x |

>[!IMPORTANT]
> Ne változtassa meg önkényesen ezt a beállítást, mert előfordulhat, hogy más alkalmazás-beállítások is módosulnak, és a függvény kódjának módosítása szükséges.

### <a name="locally-developed-application-versions"></a>Helyileg fejlesztett alkalmazások verziói

A következő frissítések alkalmazásával a megtervezett verziókat helyileg módosíthatja.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio Runtime-verziók

A Visual Studióban a projekt létrehozásakor kiválaszthatja a futtatókörnyezet verzióját. A Azure Functions Tools for Visual Studio támogatja a három fő futtatókörnyezet-verziót. A megfelelő verzió a projekt beállításai alapján történő hibakeresés és közzététel során használatos. A verzió beállításait a következő tulajdonságok határozzák meg a `.csproj` fájlban:

##### <a name="version-1x"></a>1. x verzió

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>2-es verzió. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>3. x verzió

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x és a .NET esetében `Microsoft.NET.Sdk.Functions` legalább a bővítménynek kell lennie `3.0.0` .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>2. x alkalmazások frissítése a Visual Studióban 3. x-re

Megnyithatja a 2. x-es meglévő függvényt, és áthelyezheti azt 3. x-re a fájl szerkesztésével `.csproj` és a fenti értékek frissítésével.  A Visual Studio automatikusan kezeli a futásidejű verzióit a projekt metaadatai alapján.  Ha azonban még soha nem hozott létre egy 3. x alkalmazást, mielőtt a Visual Studio még nem rendelkezik a 3. x-es sablonokkal és futtatókörnyezettel a gépen.  Ez a következőhöz hasonló hibával járhat: "nincs elérhető functions Runtime, amely megfelel a projektben megadott verziónak."  A legújabb sablonok és futtatókörnyezet beolvasásához folytassa az új functions-projekt létrehozásával kapcsolatos élményt.  A verzió és a sablon kiválasztása képernyőn várjon, amíg a Visual Studio befejezi a legújabb sablonok beolvasását.  Ha a legújabb .NET Core 3-sablonok elérhetők, és megjelenik a 3. x verzióhoz konfigurált összes projekt futtatása és hibakeresése.

> [!IMPORTANT]
> A 3. x verziójú függvények csak akkor fejleszthetők a Visual Studióban, ha a Visual Studio 16,4-es vagy újabb verzióját használja.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS kód és Azure Functions Core Tools

[Azure functions Core Tools](functions-run-local.md) a parancssori fejlesztéshez, valamint a Visual Studio Code-hoz készült [Azure functions-bővítményhez](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) is használható. A 3. x verzióval való fejlesztéshez telepítse a Core Tools 3. x verzióját. A 2. x verzió fejlesztéséhez a Core Tools 2. x verziója szükséges, és így tovább. További információ: [install the Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

A Visual Studio Code fejlesztéséhez szükség lehet a felhasználói beállítás módosítására is, hogy az `azureFunctions.projectRuntime` megfeleljen a telepített eszközök verziójának.  Ezzel a beállítással a Function app létrehozásakor használt sablonokat és nyelveket is frissíti.  Ha alkalmazásokat szeretne létrehozni, `~3` frissítse a felhasználói beállítást a következőre: `azureFunctions.projectRuntime` `~3` .

![Azure Functions bővítmény futtatókörnyezetének beállítása](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven és Java-alkalmazások

A Java-alkalmazásokat áttelepítheti a 2. x verzióról 3. x-re a helyi futtatáshoz szükséges [alapvető eszközök 3. x verziójának telepítésével](functions-run-local.md#install-the-azure-functions-core-tools) .  Miután meggyőződött arról, hogy az alkalmazás megfelelően működik a 3. x verzióban, frissítse az alkalmazás `POM.xml` fájlját, hogy módosítsa a `FUNCTIONS_EXTENSION_VERSION` beállítást `~3` , ahogy az alábbi példában is látható:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Kötések

A 2. x verziótól kezdődően a futtatókörnyezet egy új [kötési bővíthetőségi modellt](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) használ, amely a következő előnyöket kínálja:

* Harmadik féltől származó kötési bővítmények támogatása.

* Futtatókörnyezet és kötések leválasztása. Ez a módosítás lehetővé teszi, hogy a kötési bővítmények verziója és kiadása egymástól függetlenül történjen. Például megadhatja, hogy a bővítmény olyan verziójára frissítsen, amely egy mögöttes SDK újabb verziójára támaszkodik.

* Egy világosabb végrehajtási környezet, ahol csak a használatban lévő kötések ismertek és töltődnek be a futtatókörnyezet.

A HTTP-és időzítő-eseményindítók kivételével az összes kötést explicit módon fel kell venni a Function app-projektbe, vagy regisztrálni kell a portálon. További információ: [kötési bővítmények regisztrálása](./functions-bindings-expressions-patterns.md).

A következő táblázat bemutatja, hogy az egyes futásidejű verziók milyen kötéseket támogatnak.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>További lépések

További információkat találhat az alábbi forrásokban:

* [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
* [A Azure Functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md)
* [Kibocsátási megjegyzések](https://github.com/Azure/azure-functions-host/releases)
