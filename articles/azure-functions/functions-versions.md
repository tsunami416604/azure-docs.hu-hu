---
title: Az Azure Functions futásidejű verziói – áttekintés
description: Az Azure Functions a futásidejű több verzióját támogatja. Ismerje meg a különbségeket közöttük, és hogyan válassza ki az egyik, hogy az Ön számára megfelelő.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276555"
---
# <a name="azure-functions-runtime-versions-overview"></a>Az Azure Functions futásidejű verziói – áttekintés

Az Azure Functions futásidejű főverziói a .NET azon verziójához kapcsolódnak, amelyen a futásidejű alapul. Az alábbi táblázat a futásidejű aktuális verzióját, a kiadási szintet és a kapcsolódó .NET verziót mutatja be. 

| Futásidejű verzió | <sup>1.</sup> kibocsátási szint | .NET verzió | 
| --------------- | ------------- | ------------ |
| 3.x | FE | .NET Core 3.1 | 
| 2,x | FE | .NET Core 2.2 |
| 1,x | GA<sup>2(2)</sup> | .NET keret rendszer keret 4.6<sup>3</sup> |

<sup>1</sup> GA-kiadások éles forgatókönyvek esetén támogatottak.   
<sup>2</sup> Az 1.x verzió karbantartási üzemmódban van. A fejlesztések csak a későbbi verziókban találhatók.   
<sup>3</sup> Csak az Azure Portalon vagy a Windows-számítógépeken támogatja a fejlesztést.

Ez a cikk részletezi a különböző verziók közötti különbségeket, az egyes verziók létrehozásának módját és a verziók módosításának módját.

## <a name="languages"></a>Nyelvek

A 2.x-es verziótól kezdve a futásidejű egy nyelvi bővíthetőségi modellt használ, és a függvényalkalmazás minden függvényének ugyanazt a nyelvet kell használnia. A függvényalkalmazás függvényeinek nyelve az alkalmazás létrehozásakor kerül kiválasztásra, és a [FUNCTIONS\_WORKER\_RUNTIME](functions-app-settings.md#functions_worker_runtime) beállításban marad meg. 

Az Azure Functions 1.x kísérleti nyelvek nem használhatják az új modellt, így a 2.x nem támogatottak. Az alábbi táblázat azt mutatja, hogy jelenleg mely programozási nyelvek támogatottak az egyes futásidejű verziókban.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információk: [Támogatott nyelvek](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Futtatás egy adott verzión

Alapértelmezés szerint az Azure Portalon és az Azure CLI által létrehozott függvényalkalmazások 3.x-es verzióra vannak beállítva. Ezt a verziót szükség szerint módosíthatja. A futásidejű verziót csak a függvényalkalmazás létrehozása után, de a függvények hozzáadása előtt módosíthatja 1.x-re.  A 2.x és a 3.x közötti váltás még a funkciókkal rendelkező alkalmazásoknál is megengedett, de még mindig ajánlott először egy új alkalmazásban tesztelni.

## <a name="migrating-from-1x-to-later-versions"></a>Áttelepítés 1.x-ről újabb verziókra

Dönthet úgy, hogy egy meglévő alkalmazást telepít át, amely az 1.x-es verziót használja egy újabb verzió használatához. A legtöbb szükséges módosítás a nyelvi futásidejű módosításokhoz kapcsolódik, például a C# API -módosításokhoz a .NET Framework 4.7 és a .NET Core között. Azt is meg kell győződnie arról, hogy a kód és a könyvtárak kompatibilisek a választott nyelvi futásidővel. Végül vegye figyelembe az eseményindítók, kötések és az alább kiemelt funkciók változásait. A legjobb áttelepítési eredmények érdekében hozzon létre egy új függvényalkalmazást egy új verzióban, és portolja a meglévő 1.x funkciókódot az új alkalmazásba.  

Bár lehetséges egy "helybeni" frissítés az alkalmazás konfigurációjának manuális frissítésével, az 1.x-ről a magasabb verzióra való váltás tartalmaz néhány törési módosítást. A C#-ban például a hibakereső objektum `TraceWriter` `ILogger`a következőre változik: . Egy új 3.x verziójú projekt létrehozásával a legújabb 3.x sablonokon alapuló frissített függvényekkel kezdi.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Az 1.x verzió utáni eseményindítók és kötések változásai

A 2.x-es verziótól kezdve telepítenie kell a bővítményeket az alkalmazás funkciói által használt konkrét eseményindítókhoz és kötésekhez. Az egyetlen kivétel a HTTP és időzítő eseményindítók, amelyek nem igényelnek kiterjesztést.  További információt a [Kötésbővítmények regisztrálása és telepítése című témakörben talál.](./functions-bindings-register.md)

Van még néhány változás a *function.json* vagy attribútumok a függvény verziók között. Például az Event `path` Hub `eventHubName`tulajdonság most. Az egyes kötések dokumentációjára mutató hivatkozásokat a [meglévő kötési táblázatban](#bindings) találja.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Változások a funkciókban és a funkciókban az 1.x verzió után

Néhány funkciót eltávolítottunk, frissítettünk vagy kicseréltek az 1.x verzió után. Ez a szakasz részletezi az okat a módosításokat, amelyek et a későbbi verziókban az 1.x verzió használatután látott.

A 2.x verzióban a következő módosítások történtek:

* A HTTP-végpontok hívásához való kulcsok mindig az Azure Blob storage-ban vannak titkosítva tárolva. Az 1.x-es verzióban a kulcsok alapértelmezés szerint az Azure File storage-ban tárolódtak. Amikor egy alkalmazást az 1.x-es verzióról a 2.x-es verzióra frissít, a fájltárolásban lévő meglévő titkos kulcsok alaphelyzetbe állnak.

* A 2.x-es verziójú futásidejű nem tartalmaz beépített támogatást a webhook-szolgáltatók számára. Ez a változás a teljesítmény javítása érdekében történt. Továbbra is használhatja a HTTP-eseményindítók végpontként webhooks.

* A gazdagép konfigurációs fájljának (host.json) üresnek kell lennie, vagy rendelkeznie kell a karakterlánccal. `"version": "2.0"`

* A figyelés javítása érdekében a WebJobs [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) irányítópulta a portálon, amely a [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) beállítást használta, lecseréli az Azure Application Insights, amely a beállítást használja. További információ: [Monitor Azure Functions](functions-monitoring.md).

* A függvényalkalmazás minden függvényének ugyanazt a nyelvet kell használnia. Függvényalkalmazás létrehozásakor ki kell választania egy futásidejű vermet az alkalmazáshoz. A futásidejű verem az [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) alkalmazásbeállításokban megadott érték határozza meg. Ez a követelmény a helyigény és az indítási idő javítása érdekében lett hozzáadva. Helyi fejlesztés esetén ezt a beállítást a [local.settings.json fájlba](functions-run-local.md#local-settings-file)is fel kell tüntetnie.

* Az App Service-csomag funkcióinak alapértelmezett időmeghosszabbítása 30 percre változik. Az időout manuálisan módosíthatja a korlátlan ra a host.json [functionTimeout](functions-host-json.md#functiontimeout) beállításával.

* A HTTP-egyidejűség-szabályozások alapértelmezés szerint a Felhasználási terv függvényeihez vannak megvalósítva, alapértelmezés szerint példányonként 100 egyidejű kérelem. Ezt a [`maxConcurrentRequests`](functions-host-json.md#http) host.json fájlban lévő beállításban módosíthatja.

* A [.NET Core korlátozások](https://github.com/Azure/azure-functions-host/issues/3414)miatt az F# parancsfájl (.fsx) függvények támogatása el lett távolítva. A lefordított F# függvények (.fs) továbbra is támogatottak.

* Az Event Grid eseményindító webhookjainak URL-formátuma a -ra `https://{app}/runtime/webhooks/{triggerName}`változott.

## <a name="migrating-from-2x-to-3x"></a>Áttelepítés 2.x-ről 3.x-re

Az Azure Functions 3.x-es verziója nagyon visszafelé kompatibilis a 2.x-es verzióval.  Számos alkalmazásnak képesnek kell lennie arra, hogy biztonságosan frissítsen a 3.x-re kódmódosítás nélkül.  A 3.x-re való áttérés során érdemes kiterjedt teszteket futtatni, mielőtt módosítaná az éles alkalmazások főverzióját.

### <a name="breaking-changes-between-2x-and-3x"></a>2.x és 3.x közötti változások megtörése

A következőkben a 2.x alkalmazás 3.x-re való frissítése előtt figyelembe veendő módosításokat kell figyelembe venni.

#### <a name="javascript"></a>JavaScript

* A kimeneti kötések, amelyek et átosztottak `context.done` vagy `context.bindings`a visszatérési értékeket, most ugyanúgy viselkednek, mint a beállítás a ban.

* Időzítő trigger objektum camelCase helyett PascalCase

* A bináris anusokkal `dataType` aktivált Event `binary` Hub `string`függvények a helyett egy tömböt kapnak.

* A HTTP-kérelem hasznos adata már `context.bindingData.req`nem érhető el a rendszeren keresztül.  Továbbra is elérhető bemeneti paraméterként, `context.req`és `context.bindings`a alkalmazásban.

* A Node.js 8 fájl már nem támogatott, és nem hajtható végre a 3.x függvényekben.

#### <a name="net"></a>.NET

* [A szinkron kiszolgálói műveletek alapértelmezés szerint le vannak tiltva.](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers)

### <a name="changing-version-of-apps-in-azure"></a>Az alkalmazások verziójának módosítása az Azure-ban

A közzétett alkalmazások által az Azure-ban használt Functions [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) futásidejű verzióját az alkalmazásbeállítás határozza meg. A következő főbb futásidejű verzióértékek támogatottak:

| Érték | Futásidejű cél |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2,x |
| `~1` | 1,x |

>[!IMPORTANT]
> Ne módosítsa önkényesen ezt a beállítást, mert szükség lehet más alkalmazásbeállítások módosítására és a funkciókód módosítására.

### <a name="locally-developed-application-versions"></a>Helyileg kifejlesztett alkalmazásverziók

A következő frissítéseket a függvényalkalmazásokhelyi módosítása érdekében a következő frissítéseket is elláthatja.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio futásidejű verziói

A Visual Studio programban a projekt létrehozásakor kiválaszthatja a futásidejű verziót. A Visual Studio Azure Functions eszközei támogatják a három fő futásidejű verziót. A megfelelő verziót használja a program a projektbeállítások on alapuló hibakereséskor és közzétételkor. A verzióbeállítások a fájlban a `.csproj` következő tulajdonságokban vannak definiálva:

##### <a name="version-1x"></a>1.x verzió

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>2.x verzió

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>3.x verzió

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Az Azure Functions 3.x `Microsoft.NET.Sdk.Functions` és a `3.0.0`.NET használatához a bővítmény nek legalább kell lennie.

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>2.x-alkalmazások frissítése 3.x-re a Visual Studio-ban

A `.csproj` fájl szerkesztésével és a fenti értékek frissítésével megnyithat egy meglévő függvényt, amely a 2.x fájlt célozza meg, és a 3.x-re léphet.  A Visual Studio automatikusan kezeli a futásidejű verziókat a projekt metaadatai alapján.  Azonban lehetséges, ha még soha nem hozott létre 3.x alkalmazást, hogy a Visual Studio még nem rendelkezik a 3.x sablonjaival és futásidejével a számítógépen.  Ez olyan hibaüzenetet okozhat, mint például: "Nincs elérhető függvény futásidő, amely megfelel a projektben megadott verziónak."  A legújabb sablonok és futásidejű lekéréséhez menjen át a felhasználói élményen egy új függvényprojekt létrehozásához.  Amikor a verzió- és sablonválasztó képernyőre ér, várja meg, amíg a Visual Studio befejezi a legújabb sablonok letöltését.  Miután a legújabb .NET Core 3 sablonok elérhetők és megjelennek, képesnek kell lennie a 3.x verzióra konfigurált projektek futtatására és hibakeresésére.

> [!IMPORTANT]
> A 3.x-es verziójú funkciók csak a Visual Studio 16.4-es vagy újabb verziójának használata esetén fejleszthetők.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code és az Azure Functions alapvető eszközei

[Az Azure Functions Core Tools](functions-run-local.md) parancssori fejlesztéshez, valamint a Visual Studio-kód Azure Functions [bővítményéhez](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) is használható. A 3.x verzióval való fejlesztéshez telepítse a Core Tools 3.x verzióját. A 2.x verziós fejlesztéshez a Core Tools 2.x verziója szükséges, és így tovább. További információ: [Install the Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

A Visual Studio-kód fejlesztéséhez szükség lehet a `azureFunctions.projectRuntime` felhasználói beállítások frissítésére is, hogy megfeleljen a telepített eszközök verziójának.  Ez a beállítás a függvényalkalmazás létrehozása során használt sablonokat és nyelveket is frissíti.  Ha alkalmazásokat `~3` szeretne létrehozni, frissítse a `azureFunctions.projectRuntime` felhasználói beállítást a rendszerre. `~3`

![Az Azure Functions bővítmény futásidejű beállítása](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven és Java alkalmazások

A Java-alkalmazásokat a 2.x verzióról a 3.x verzióra telepítheti a helyi futtatáshoz szükséges [alapvető eszközök 3.x verziójának telepítésével.](functions-run-local.md#install-the-azure-functions-core-tools)  Miután meggyőződött arról, hogy az alkalmazás megfelelően működik-e `POM.xml` a 3.x-es verzión, frissítse az alkalmazás fájlját, hogy módosítsa a `FUNCTIONS_EXTENSION_VERSION` beállítást a rendszerre, `~3`ahogy az a következő példában is:

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

A 2.x-es verziótól kezdve a futásidejű egy új [kötésbővíthetőségi modellt](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) használ, amely a következő előnyöket kínálja:

* Harmadik féltől származó kötésbővítmények támogatása.

* A futásidejű és a kötések függetlenítése. Ez a módosítás lehetővé teszi a kötésbővítmények egymástól független verziókba történő kiterjesztését és kiadását. Dönthet például úgy, hogy egy bővítmény olyan verziójára frissít, amely egy mögöttes SDK újabb verziójára támaszkodik.

* Egy könnyebb végrehajtási környezet, ahol csak a használatban lévő kötések ismertek és betöltöttek a futásidejű.

A HTTP és időzítő eseményindítók kivételével minden kötést explicit módon hozzá kell adni a függvényalkalmazás-projekthez, vagy regisztrálni kell a portálon. További információ: [Binding extensions register](./functions-bindings-expressions-patterns.md).

Az alábbi táblázat bemutatja, hogy mely kötések támogatottak az egyes futásidejű verziókban.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
* [Az Azure Functions futásidejű verzióinak megcélzása](set-runtime-version.md)
* [Kibocsátási megjegyzések](https://github.com/Azure/azure-functions-host/releases)
