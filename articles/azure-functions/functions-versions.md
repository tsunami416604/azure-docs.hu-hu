---
title: Azure Functions futtatókörnyezet-verziók áttekintése
description: Azure Functions támogatja a futtatókörnyezet több verzióját. Megtudhatja, hogy miben különböznek egymástól, és hogyan választhatja ki az Önnek legmegfelelőbbet.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21722d66129224038df20a0a1ef2bff989221224
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539501"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions futtatókörnyezet-verziók áttekintése

Az Azure Functions futtatókörnyezet főverziói a futtatókörnyezet alapjául szolgáló .NET-verzióhoz kapcsolódnak. A következő táblázat a futtatókörnyezet aktuális verzióját, a kiadási szintet és a kapcsolódó .NET-verziót mutatja. 

| Futtatókörnyezet verziója | <sup>1</sup> . kiadási szint | .NET-verzió | 
| --------------- | ------------- | ------------ |
| 3.x | FE | .NET Core 3,1 | 
| 2. x | FE | .NET Core 2.2 |
| 1. x | <sup>2</sup> . ga | .NET-keretrendszer 4,6<sup>3</sup> |

<sup>1</sup> a ga-kiadások éles környezetben is támogatottak.   
<sup>2</sup> az 1. x verzió karbantartási módban van. A fejlesztéseket csak a későbbi verziókban biztosítjuk.   
<sup>3</sup> a csak a Azure Portal és a Windows rendszerű számítógépeken való helyi fejlesztést támogatja.

Ez a cikk a különböző verziók közötti különbségeket ismerteti, hogyan hozhatja létre az egyes verziókat, és hogyan módosíthatja a verziókat.

## <a name="languages"></a>Nyelvek

A 2. x verziótól kezdődően a futtatókörnyezet nyelvi bővíthetőségi modellt használ, és a Function app összes funkciójának ugyanazzal a nyelvvel kell rendelkeznie. A functions alkalmazásban a függvények nyelve az alkalmazás létrehozásakor van kiválasztva, és a [\_worker\_RUNTIME](functions-app-settings.md#functions_worker_runtime) beállításban található functions. 

Azure Functions 1. x kísérleti nyelvek nem használhatják az új modellt, így azok nem támogatottak 2. x-ben. A következő táblázat azt mutatja be, hogy mely programozási nyelvek támogatottak az egyes futtatókörnyezet-verziókban.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információk: [Támogatott nyelvek](supported-languages.md).

## <a name="creating-1x-apps"></a>Futtatás adott verzión

Alapértelmezés szerint a Azure Portal és az Azure CLI-ben létrehozott Function apps a 3. x verzióra van beállítva. Igény szerint módosíthatja ezt a verziót. A futásidejű verziót csak 1. x értékre módosíthatja, miután létrehozta a Function alkalmazást, de a függvények hozzáadása előtt is.  A 2. x és 3. x közötti váltás akkor is engedélyezett, ha a functions alkalmazással rendelkezik, de az új alkalmazásban való tesztelés még ajánlott.

## <a name="migrating-from-1x-to-later-versions"></a>Migrálás 1. x verzióról a későbbi verziókra

Dönthet úgy is, hogy egy meglévő, az 1. x verziójú futtatókörnyezet használatára írt alkalmazást telepít át, hogy ehelyett egy újabb verziót használjon. A szükséges módosítások többsége a nyelvi futtatókörnyezet változásaihoz kapcsolódik, például az C# API-változásokat a .NET-keretrendszer 4,7 és a .net Core között. Azt is ellenőriznie kell, hogy a kód és a kódtárak kompatibilisek-e a kiválasztott nyelvi futtatókörnyezettel. Végezetül jegyezze fel a trigger, a kötések és az alábbiakban kiemelt funkciók változásait. A legjobb áttelepítési eredményekhez hozzon létre egy új Function-alkalmazást egy új verzióban, és a meglévő 1. x-es verziójú függvény kódját adja meg az új alkalmazásnak.  

Habár lehetséges, hogy a "helyben" történő frissítést manuálisan frissíti az alkalmazás konfigurációját, az 1. x-ről a magasabb verzióra. A alkalmazásban C#például a hibakeresési objektum `TraceWriter`ról `ILogger`ra módosul. Egy új 3. x-es verziójú projekt létrehozásával a legújabb 3. x verziójú sablonok alapján kezdi meg a frissített funkciókat.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Az eseményindítók és kötések változásai az 1. x verzió után

A 2. x verziótól kezdődően telepítenie kell a bővítményeket az alkalmazás funkciói által használt egyes eseményindítók és kötések számára. Az egyetlen kivétel a HTTP-és időzítő-eseményindítók esetében, amelyek nem igényelnek kiterjesztést.  További információ: [kötési bővítmények regisztrálása és telepítése](./functions-bindings-register.md).

A *function. JSON* vagy a függvény attribútumai a verziók között is módosulnak. Például az Event hub `path` tulajdonsága már `eventHubName`. Az egyes kötésekhez kapcsolódó dokumentációra mutató hivatkozásokat a [meglévő kötési táblázat](#bindings) tartalmazza.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>A szolgáltatások és a funkciók változásai a 1. x verzió után

Néhány szolgáltatást eltávolítottak, frissítették vagy cseréltek az 1. x verzió után. Ez a szakasz az 1. x verzió használata után a későbbi verziókban megjelenő módosításokat részletezi.

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

## <a name="migrating-from-2x-to-3x"></a>Migrálás 2. x és 3. x között

Azure Functions 3. x verzió a 2. x verzióval kompatibilis.  Számos alkalmazásnak képesnek kell lennie arra, hogy a kód módosítása nélkül is biztonságosan frissítsen 3. x-re.  A 3. x-re való áttérés javasolt, hogy az éles alkalmazások főverziójának módosítása előtt futtasson kiterjedt teszteket.

### <a name="breaking-changes-between-2x-and-3x"></a>A 2. x és 3. x közötti változások megszakítása

A 2. x alkalmazás 3. x verzióra való frissítése előtt a következő módosításokat kell figyelembe vennie:

#### <a name="javascript"></a>JavaScript

* A `context.done` vagy visszatérési értékek által hozzárendelt kimeneti kötések mostantól ugyanúgy viselkednek, mint a `context.bindings`.

* Az időzítő trigger objektum camelCase helyett a PascalCase

* Az Event hub által aktivált függvények `dataType` bináris `binary` helyett a `string`tömbjét kapják meg.

* A HTTP-kérések adattartalma már nem érhető el `context.bindingData.req`on keresztül.  Továbbra is elérhető bemeneti paraméterként, `context.req`ként és `context.bindings`.

* A Node. js 8 már nem támogatott, és nem lesz végrehajtva 3. x függvényben.

#### <a name="net"></a>.NET

* A [szinkron kiszolgáló műveletei alapértelmezés szerint le vannak tiltva](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Alkalmazások verziójának módosítása az Azure-ban

Az Azure-ban közzétett alkalmazások által használt functions futtatókörnyezet verzióját a [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) alkalmazás-beállítás határozza meg. A következő fő futásidejű verziók értékei támogatottak:

| Érték | Futtatókörnyezet célja |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2. x |
| `~1` | 1. x |

>[!IMPORTANT]
> Ne változtassa meg önkényesen ezt a beállítást, mert előfordulhat, hogy más alkalmazás-beállítások is módosulnak, és a függvény kódjának módosítása szükséges.

### <a name="locally-developed-application-versions"></a>Helyileg fejlesztett alkalmazások verziói

A következő frissítések alkalmazásával a megtervezett verziókat helyileg módosíthatja.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio Runtime-verziók

A Visual Studióban a projekt létrehozásakor kiválaszthatja a futtatókörnyezet verzióját. A Azure Functions Tools for Visual Studio támogatja a három fő futtatókörnyezet-verziót. A megfelelő verzió a projekt beállításai alapján történő hibakeresés és közzététel során használatos. A verzió beállításait a `.csproj` fájlban a következő tulajdonságok határozzák meg:

##### <a name="version-1x"></a>1\. x verzió

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>2-es verzió. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>3\. x verzió

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x és a .NET esetében a `Microsoft.NET.Sdk.Functions` bővítménynek legalább `3.0.0`nak kell lennie.

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>2\. x alkalmazások frissítése a Visual Studióban 3. x-re

Megnyithatja a 2. x-es meglévő függvényt, és áthelyezheti azt 3. x-re a `.csproj` fájl szerkesztésével és a fenti értékek frissítésével.  A Visual Studio automatikusan kezeli a futásidejű verzióit a projekt metaadatai alapján.  Ha azonban még soha nem hozott létre egy 3. x alkalmazást, mielőtt a Visual Studio még nem rendelkezik a 3. x-es sablonokkal és futtatókörnyezettel a gépen.  Ez a következőhöz hasonló hibával járhat: "nincs elérhető functions Runtime, amely megfelel a projektben megadott verziónak."  A legújabb sablonok és futtatókörnyezet beolvasásához folytassa az új functions-projekt létrehozásával kapcsolatos élményt.  A verzió és a sablon kiválasztása képernyőn várjon, amíg a Visual Studio befejezi a legújabb sablonok beolvasását.  Ha a legújabb .NET Core 3-sablonok elérhetők, és megjelenik a 3. x verzióhoz konfigurált összes projekt futtatása és hibakeresése.

> [!IMPORTANT]
> A 3. x verziójú függvények csak akkor fejleszthetők a Visual Studióban, ha a Visual Studio 16,4-es vagy újabb verzióját használja.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS kód és Azure Functions Core Tools

[Azure functions Core Tools](functions-run-local.md) a parancssori fejlesztéshez, valamint a Visual Studio Code-hoz készült [Azure functions-bővítményhez](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) is használható. A 3. x verzióval való fejlesztéshez telepítse a Core Tools 3. x verzióját. A 2. x verzió fejlesztéséhez a Core Tools 2. x verziója szükséges, és így tovább. További információ: [install the Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

A Visual Studio Code fejlesztéséhez szükség lehet a `azureFunctions.projectRuntime` felhasználói beállításának frissítésére is, hogy megfeleljen a telepített eszközök verziójának.  Ezzel a beállítással a Function app létrehozásakor használt sablonokat és nyelveket is frissíti.  Ha `~3` szeretné létrehozni az alkalmazásokat, a `azureFunctions.projectRuntime` felhasználói beállítást `~3`ra kell frissítenie.

![Azure Functions bővítmény futtatókörnyezetének beállítása](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven és Java-alkalmazások

A Java-alkalmazásokat áttelepítheti a 2. x verzióról 3. x-re a helyi futtatáshoz szükséges [alapvető eszközök 3. x verziójának telepítésével](functions-run-local.md#install-the-azure-functions-core-tools) .  Miután meggyőződött arról, hogy az alkalmazás megfelelően működik-e a 3. x verzióban, frissítse az alkalmazás `POM.xml` fájlját, hogy módosítsa a `FUNCTIONS_EXTENSION_VERSION` beállítást `~3`re, ahogy az alábbi példában látható:

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

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következőket:

* [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
* [A Azure Functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md)
* [Kibocsátási megjegyzések](https://github.com/Azure/azure-functions-host/releases)
