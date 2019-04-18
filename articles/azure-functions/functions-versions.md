---
title: Az Azure Functions runtime verziók áttekintése
description: Az Azure Functions futtatókörnyezet több verzióit támogatja. Ismerje meg azokat, és hogyan választható ki a másik pedig az Önnek megfelelő közötti különbségeket.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: 6988fb547b07f81891efea3caad8bf34f4c8a476
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58889753"
---
# <a name="azure-functions-runtime-versions-overview"></a>Az Azure Functions runtime verziók áttekintése

 Nincsenek az Azure Functions runtime két fő verziói: az 1.x és a 2.x. A jelenlegi verziót, ahol új funkció a munkahelyi és fejlesztések kerül sor az 2.x, abban az esetben, ha mindkét üzemi forgatókönyvek esetén támogatottak.  Az alábbi részletek néhány különbséget a két, hogyan hozhat létre minden egyes verziója, és frissítse a 1.x a 2.x-es.

> [!NOTE]
> Ez a cikk az Azure Functions felhőalapú szolgáltatásra vonatkozik. Az előzetes verzióban üzemel, amely lehetővé teszi Azure Functions helyi futtatását kapcsolatos információkért tekintse meg a [Azure Functions Runtime áttekintése](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Platformfüggetlen fejlesztés

A verzió 2.x verziójú futtatókörnyezet futtat a .NET Core 2, amely lehetővé teszi, hogy a .NET Core, beleértve a macOS és Linux által támogatott platformokon futtatható. .NET Core-on futó lehetővé teszi a platformfüggetlen fejlesztésekhez és a szolgáltatási forgatókönyvek esetében.

Ezzel a verzió 1.x modul fejlesztési és üzemeltetési az Azure Portalon vagy a Windows-számítógépeken csak támogatja.

## <a name="languages"></a>Languages

A verzió 2.x verziójú futtatókörnyezet új nyelvi bővíthetőségi modellt használ. A verzió 2.x-es, a függvényalkalmazás a függvények meg kell osztani ugyanazt a nyelvet. A függvényalkalmazás a függvények kiválasztott az alkalmazás létrehozásakor.

Az Azure Functions 1.x kísérleti nyelvek kell használni az új modell 2.x-es nem támogatottak azok nem frissülnek. Az alábbi táblázat azt jelzi, hogy minden egyes modul verzióját a jelenleg támogatott programozási nyelvek.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információk: [Támogatott nyelvek](supported-languages.md).

## <a name="creating-1x-apps"></a>Futtassa a verzió 1.x

Alapértelmezés szerint az Azure Portalon létrehozott függvényalkalmazás verzióra vannak beállítva 2.x. Ha lehetséges, használjon a futtatókörnyezet verziója hol kerül sor az új funkció-befektetéseit. Ha kell, 1.x verzió modulban továbbra is futtathatja a függvényalkalmazást. A függvényalkalmazás létrehozása után, de függvényeinek hozzáadása előtt a futtatókörnyezet-verzió csak módosíthatja. A futtatókörnyezet 1.x verzióra rögzítése kapcsolatban lásd: [megtekintése és frissítése az aktuális futtatókörnyezet-verzió](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-2x"></a>Áttelepítés a 2.x 1.x

Dönthet úgy, hogy használja inkább a verzió a verzió 1.x futtatókörnyezet használatával írt, meglévő alkalmazások áttelepítéséhez 2.x. A legtöbb végre kell hajtania a módosításokat a nyelvi futtatókörnyezetet, például C# API .NET Core 2 és a .NET-keretrendszer 4.7 között a módosításához kapcsolódó. Is kell, hogy a kód és a szalagtárak kompatibilisek-e a választott nyelvi futtatókörnyezetet. Végül mindenképpen vegye figyelembe a módosításokat az eseményindító, kötéseit és emelve a funkciók. A legjobb eredmények áttelepítési hozzunk létre egy új függvényalkalmazást verzió 2.x és port, a meglévő verzió 1.x függvény kód az új alkalmazáshoz.  

### <a name="changes-in-triggers-and-bindings"></a>Eseményindítók és kötések változásai

Verzió 2.x meghatározott eseményindítók és kötések az alkalmazás a functions által használt a bővítmények telepítését igényli. A HTTP- és időzítő eseményindítóik, amelyek a bővítmény nem igénylik az egyetlen kivétel.  További információkért lásd: [regisztrálása és a kötési bővítményeket telepítse](./functions-bindings-register.md).

Vált továbbá néhány módosításával a `function.json` vagy attribútumok verziók között a függvénynek. Ha például az Event Hubs `path` tulajdonság már `eventHubName`. Tekintse meg a [létező kötés táblázat](#bindings) dokumentáció minden kötéshez mutató hivatkozásokat.

### <a name="changes-in-features-and-functionality"></a>Szolgáltatások és funkciók változásai

Néhány funkcióval, amely szintén el lettek távolítva, frissített vagy az új verzióban cserélni. Ez a szakasz részletesen verzió látható a módosítások után kellene használt verzió 2.x 1.x.

A verzió 2.x-es, a következő változások történtek:

* A HTTP-végpontok hívása kulcsok mindig tárolja titkosított formában az Azure Blob storage-ban. Verzió 1.x, kulcsokat is tárolja az Azure File storage lehet alapértelmezett. Ha egy alkalmazás-es verzióról 1.x verzió 2.x, file storage-ban már meglévő titkokat alapértékre állnak vissza.

* A verzió 2.x verziójú futtatókörnyezet nem tartalmaz beépített támogatást nyújt a webhook-szolgáltatók. Ez a változás történt a teljesítmény javítása. A webhookok végpontként továbbra is használhatja a HTTP-eseményindítók.

* A gazdagép-konfigurációs fájl (host.json) legyen üres vagy tartalmazza a következő karakterláncot `"version": "2.0"`.

* Figyelés, a WebJobs-irányítópulttal, a portálon használt javítása érdekében a [ `AzureWebJobsDashboard` ](functions-app-settings.md#azurewebjobsdashboard) beállítás váltja fel az Azure Application insights segítségével, amely használja a [ `APPINSIGHTS_INSTRUMENTATIONKEY` ](functions-app-settings.md#appinsights_instrumentationkey) beállítás. További információkért lásd: [figyelése az Azure Functions](functions-monitoring.md).

* A függvényalkalmazás a függvények kell osztani ugyanazt a nyelvet. Amikor létrehoz egy függvényalkalmazást, ki kell választania egy futtatókörnyezeti verem az alkalmazáshoz. A futtatókörnyezeti verem által meghatározott a [ `FUNCTIONS_WORKER_RUNTIME` ](functions-app-settings.md#functions_worker_runtime) értékét az alkalmazás beállításait. Ez a követelmény csökkenthetik a méretük és indítási lett hozzáadva. Amikor helyileg fejlesztésével, ezt a beállítást is fel kell a [local.settings.json fájljában](functions-run-local.md#local-settings-file).

* A functions az App Service-csomag alapértelmezett időtúllépési érték 30 perc értékre módosul. Manuálisan módosíthatja az időtúllépési vissza a korlátlan használatával a [functionTimeout](functions-host-json.md#functiontimeout) host.json beállítását.

* HTTP-egyidejűség szabályozások vannak megvalósítva, példányonként 100 egyidejű kérelmek alapértelmezés szerint a használatalapú csomag funkciók alapértelmezés szerint. Ez az a [ `maxConcurrentRequests` ](functions-host-json.md#http) beállítása a host.json fájlban.

* Mert [.NET core korlátozások](https://github.com/Azure/azure-functions-host/issues/3414), támogatja a F# parancsfájl (.fsx) funkciók el lettek távolítva. Lefordított F# függvények (.fs) továbbra is támogatottak.

* Event Grid eseményindító webhookok URL-cím formátuma módosult `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Áttelepítés egy helyi fejlesztésű alkalmazás

Előfordulhat, hogy meglévő függvény használatával helyben 1.x verzió futásidejű fejlesztett alkalmazás projektek. Verzióra való frissítéshez 2.x, hozzunk létre helyi függvényalkalmazás-projekt verziója elleni 2.x és port, a meglévő kód az új alkalmazásba. Sikerült manuálisan frissíti a meglévő projekt és a kód, a "helyszíni" frissítés egy rendezés alapjaként. Vannak azonban egyéb fejlesztések verziója számos 1.x és verzió 2.x, győződjön meg arról, hogy továbbra is szükséges. Például a C#-ban a hibakeresési objektum módosult `TraceWriter` való `ILogger`. Létrehoz egy új verzió 2.x projektet, először a legújabb verzió 2.x sablonok alapján, frissített funkciókkal.

#### <a name="visual-studio-runtime-versions"></a>A Visual Studio futtatókörnyezet verziója

A Visual Studióban, válassza ki a futtatókörnyezet-verzió a projekt létrehozásakor. Az Azure Functions tools for Visual Studio támogatja a mindkét fő futásidejű verzióját. A megfelelő verziójú akkor használatos, ha a Hibakeresés és közzétételi projekt beállításai alapján. A verzió beállítások vannak meghatározva a `.csproj` fájlt a következő tulajdonságokkal:

##### <a name="version-1x"></a>Verzió 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Verzió 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Hibakeresés és a projekt közzétételekor, a futásidejű megfelelő verziója szolgál.

#### <a name="vs-code-and-azure-functions-core-tools"></a>A VS Code és az Azure Functions Core Tools

[Az Azure Functions Core Tools](functions-run-local.md) parancssori fejlesztési és a is szolgál a [Azure Functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a Visual Studio Code. Verzió elleni fejleszthet 2.x-es, a telepítés verzió 2.x verzióját az Core Tools. Verzió 1.x fejlesztési verziója szükséges a Core Tools 1.x-es. További információkért lásd: [az Azure Functions Core Tools telepítése](functions-run-local.md#install-the-azure-functions-core-tools).

A Visual Studio Code-fejlesztéshez is szükség lehet a felhasználó vonatkozó beállításainak frissítéséhez a `azureFunctions.projectRuntime` telepített eszközök verziójának megfelelő.  Ez a beállítás is frissíti a sablonokat és a függvény létrehozása során használt nyelv.

### <a name="changing-version-of-apps-in-azure"></a>Az Azure-beli alkalmazások verziója módosítása

A Functions futtatókörnyezete az Azure-ban közzétett alkalmazások által használt verziójának szabja a [ `FUNCTIONS_EXTENSION_VERSION` ](functions-app-settings.md#functions_extension_version) nastavení aplikace. Érték `~2` célozza meg, a verzió 2.x verziójú futtatókörnyezet és `~1` 1.x verzió futásidejű célozza. Tetszőlegesen ne módosítsa ezt a beállítást, mert más alkalmazás-beállítások módosításai és a kód módosításait az a funkciók valószínűleg szüksége. A függvényalkalmazás át egy másik futtatókörnyezet-verzió ajánlott módja kapcsolatos további információkért lásd: [bemutatásához az Azure Functions runtime verziók](set-runtime-version.md).

## <a name="bindings"></a>Kötések

A verzió 2.x verziójú futtatókörnyezet használja egy új [bővíthetőségi modell kötése](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , amely ezeket az előnyöket kínálja:

* Támogatás a külső kötési bővítményeket.

* Elválasztás futtatókörnyezeti és a kötéseket. Ez a változás lehetővé teszi, hogy a rendszerverzióval ellátott és egymástól függetlenül kiadott kötési bővítményeket. Például kérheti egy bővítmény, amely az alapul szolgáló SDK újabb verziója támaszkodik a verzióra frissíteni.

* Egy világosabb végrehajtási környezetet, ahol csak a használatban lévő kötéseket ismertek és a futtatókörnyezet által betöltött.

HTTP- és időzítő eseményindító kivételével minden kötések kell explicit módon hozzá a függvényalkalmazás projektjét, vagy regisztrálva a portálon. További információkért lásd: [regisztrálja a kötési bővítményeket](./functions-bindings-expressions-patterns.md).

Az alábbi táblázat mutatja, melyik kötések támogatottak a minden egyes modul verzióját.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
* [Az Azure Functions runtime verziók bemutatásához](set-runtime-version.md)
* [Kibocsátási megjegyzések](https://github.com/Azure/azure-functions-host/releases)
