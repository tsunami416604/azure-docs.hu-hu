---
title: Az Azure Functions runtime verziók áttekintése
description: Az Azure Functions futtatókörnyezet több verzióit támogatja. Ismerje meg azokat, és hogyan választható ki a másik pedig az Önnek megfelelő közötti különbségeket.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: d4a0d53c3438be9ea4e0229e4a456ed6f5960996
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249060"
---
# <a name="azure-functions-runtime-versions-overview"></a>Az Azure Functions runtime verziók áttekintése

 Az Azure Functions Runtime két fő verziója: 1.x és a 2.x. A jelenlegi verziót, ahol új funkció a munkahelyi és fejlesztések kerül sor az 2.x, abban az esetben, ha mindkét üzemi forgatókönyvek esetén támogatottak.  Az alábbi részletek néhány különbséget a két, hogyan hozhat létre minden egyes verziója, és frissítse a 1.x a 2.x-es.

> [!NOTE] 
> Ez a cikk az Azure Functions felhőalapú szolgáltatásra vonatkozik. Az előzetes verzióban üzemel, amely lehetővé teszi Azure Functions helyi futtatását kapcsolatos információkért tekintse meg a [Azure Functions Runtime áttekintése](functions-runtime-overview.md).

## <a name="creating-1x-apps"></a>1.x alkalmazások létrehozása

Az Azure Portalon létrehozott új alkalmazások vannak állítva 2.x alapértelmezés szerint ez az a legfrissebb verziót, és hol kerül sor az új funkció befektetések.  Az alábbiak szerint azonban v1.x alkalmazások továbbra is létrehozhat.

1. Azure-függvény létrehozása az Azure Portalról
1. Nyissa meg a létrehozott alkalmazást, és bár üres nyissa meg a **Függvénybeállítások**
1. A verzió megváltoztatása ~ 2-től ~ 1.  *Ezzel a kapcsolóval letiltásra kerül, ha az alkalmazás funkciói*.
1. Kattintson a Mentés gombra, és indítsa újra az alkalmazást.  Az összes sablon most kell létrehozni és 1.x futtathatja.

## <a name="cross-platform-development"></a>Platformfüggetlen fejlesztés

Futtatókörnyezet 1.x támogatja a fejlesztési és üzemeltetési csak a portálon vagy a Windows. Futásidejű 2.x Futtatás a .NET Core 2, ami azt jelenti, .NET Core, beleértve a macOS és Linux által támogatott összes platformokon futtatható. Ez lehetővé teszi a platformfüggetlen fejlesztésekhez és szolgáltatási forgatókönyvek esetében.

## <a name="languages"></a>Nyelvek

Futásidejű 2.x új nyelvi bővíthetőségi modellt használ. Emellett azokat az eszközöket és a teljesítmény javítása érdekében minden egyes 2.x alkalmazás korlátozódik csak egy nyelven a függvények van. A 2.x jelenleg támogatott nyelvek a következők: C#, F #, JavaScript és Java. Az Azure Functions 1.x kísérleti nyelvek még nem lett frissítve az új modellt használ, így a 2.x-es nem támogatottak. Az alábbi táblázat azt jelzi, hogy az egyes verze modulu runtime támogatott programozási nyelvek.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információkért lásd: [támogatott nyelvek](supported-languages.md).

## <a name="migrating-from-1x-to-2x"></a>Áttelepítés a 2.x 1.x

Előfordulhat, hogy szeretne áthelyezni a 1.x 2.x írt, meglévő alkalmazások.  A verziók közötti áthelyezése szükséges szempontok a legtöbb a nyelvi futtatókörnyezet módosításához fent (például C# .NET Core 2 .NET-keretrendszer 4.7 áthelyezését) kapcsolódó.  Győződjön meg arról, hogy a kódot kell, és a szalagtárak kompatibilisek a használt nyelvi futtatókörnyezetet.  Emellett feltétlenül ellenőrizze, hogy módosításainak eseményindító, kötéseit és szolgáltatások emelve.

### <a name="changes-in-triggers-and-bindings"></a>Eseményindítók és kötések változásai

Az eseményindítót és a kötési tulajdonságokat és a konfigurációk többsége verziók változatlanok maradnak, amíg a 2.x-es, telepítenie kell minden olyan eseményindítóval vagy kötéssel az alkalmazáshoz. Ez az egyetlen kivétel HTTP- és időzítő eseményindító.  Lásd: [regisztrálása és a kötési bővítményeket telepítse](./functions-triggers-bindings.md#register-binding-extensions).  Vegye figyelembe, hogy lehetnek a változik a `function.json` vagy a verziók közötti függvény attribútumok (például az, cosmos DB `connection` tulajdonság már `ConnectionStringSetting`).  Referencia a [létező kötés táblázat](#bindings) dokumentáció minden kötéshez mutató hivatkozásokat.

### <a name="changes-in-features-available"></a>Elérhető szolgáltatások változásai

Nyelvek és -kötések változásai kívül néhány szolgáltatás eltávolítva, frissítése vagy cseréje verziói között.  Az alábbiakban néhány, a fő szempontok, ha 2.x kezdve 1.x használata után.  A 2.x a következő változások történtek:

* Közönségét egy függvény kulcsainak mindig titkosított blob storage-ban kell tárolni. Az 1.x alapértelmezés szerint, file storage-ban és sikerült áthelyezni a blobba, ha például a tárolóhelyek szolgáltatások engedélyezését.  Ha egy 1.x jellegű parancsot a(z) app frissítése 2.x-es és a titkos kulcsok jelenleg file storage-ban, alaphelyzetbe állítja.
* A teljesítmény javítása érdekében "webhook" típusú triggerek eltávolítja és lecseréli az "HTTP"-triggerekkel.
* Gazdagép-konfigurációja (`host.json`) kell lennie üres, vagy tartalmazhat `version` , `2.0` az egyik tulajdonsága.
* Figyelés és observability, a WebJobs-irányítópulttal (`AzureWebJobsDashboard`) helyére [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* Alkalmazásbeállítások (`local.settings.json`) értéket kell megadni a tulajdonság `FUNCTIONS_WORKER_RUNTIME` , amely leképezi a nyelvet, az alkalmazás `dotnet | node | java | python`.
    * Erőforrás-igényű és indítási idő javítása érdekében alkalmazások pedig csak egyetlen nyelvet. Több alkalmazás funkciói a megoldásban a különböző nyelveken teheti közzé.
* A functions az app service-csomag alapértelmezett időtúllépési érték 30 perc.  Még mindig manuálisan beállítható korlátlan.
* [Miatt a .NET core korlátozásokkal](https://github.com/Azure/azure-functions-host/issues/3414), `.fsx` parancsfájlok, F #-függvények el lett távolítva. Lefordított F #-függvények továbbra is támogatottak.
* Webhookokon alapuló triggereket (Példa: Event Grid) formátumát változott `https://{app}/runtime/webhooks/{triggerName}`

### <a name="upgrading-a-locally-developed-application"></a>Egy helyileg fejlesztésű alkalmazás frissítése

Ha v1.x alkalmazását helyileg fejlesztették ki, módosíthatja az alkalmazás vagy a projekthez, hogy megfeleljen a v2.  Javasoljuk, hogy hozzon létre egy új alkalmazás és a port keresztül a kód az új alkalmazáshoz.  Noha egy meglévő alkalmazáshoz, hajtsa végre az a hely sikerült végrehajtott változtatásokat frissítési, számos egyéb fejlesztések között v1 és v2, amely a régi kód valószínűleg nem most (például C#-ban a módosítás a `TraceWriter` való `ILogger`) .  

A javasolt útvonal kezdő a v2-sablonok közül, és vigye kód egy új projekt vagy alkalmazás.

#### <a name="visual-studio-runtime-versions"></a>A Visual Studio futtatókörnyezet verziója

A Visual Studióban, válassza ki a futtatókörnyezet-verzió a projekt létrehozásakor.  A Visual Studio a bits mindkét főbb verziók rendelkezik, és dinamikusan használhat fel a projekt a megfelelőt.  Ezek a beállítások származnak az `.csproj` fájlt.  Az 1.x alkalmazások a projekt tulajdonságokkal rendelkezik

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

A v2-ben vannak a projekt tulajdonságai

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Kattintva hibakeresés vagy lesz közzététele megfelelően beállítva a megfelelő verziót a Projektbeállítások között.

#### <a name="vs-code-and-azure-functions-core-tools"></a>A VS Code és az Azure Functions Core Tools

Más helyi azokat az eszközöket az Azure Functions Core Tools támaszkodik.  Ezeket az eszközöket a gépen vannak telepítve, és általában csak egyetlen verziója telepítve van egy fejlesztői gépen egyszerre.  Lásd: [utasításokat verzióról az core tools telepítése](./functions-run-local.md).

A VS Code is szükség lehet a felhasználó vonatkozó beállításainak frissítéséhez a `azureFunctions.projectRuntime` telepített eszközök verziójának megfelelő.  Ez is frissíti, a sablonok és új alkalmazások létrehozása során illesztett nyelveket.

### <a name="changing-version-of-apps-in-azure"></a>Az Azure-beli alkalmazások verziója módosítása

Közzétett alkalmazás verziók az Alkalmazásbeállítás segítségével állíthatók be `FUNCTIONS_EXTENSION_VERSION`.  A beállított érték `~2` v2-alkalmazások esetén és `~1` v1 alkalmazások esetében.  Fontos, hogy ezeket a funkciókat is módosítása nélkül közzétett meglévő függvények futtatókörnyezetének verzióját módosítása nem ajánlott.  A javasolt elérési út, hozzon létre egy új függvényalkalmazást, és állítsa be a megfelelő verzióra, tesztelheti a módosításokat, majd tiltsa le vagy törölje az előző alkalmazást.

## <a name="bindings"></a>Kötések 

Futásidejű 2.x használja egy új [bővíthetőségi modell kötése](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , amely ezeket az előnyöket kínálja:

* Támogatás a külső kötési bővítményeket.
* Elválasztás futtatókörnyezeti és a kötéseket. Ez lehetővé teszi a rendszerverzióval ellátott és egymástól függetlenül kiadott kötési bővítményeket. Például kérheti egy bővítmény, amely az alapul szolgáló SDK újabb verziója támaszkodik a verzióra frissíteni.
* Egy világosabb végrehajtási környezetet, ahol csak a használatban lévő kötéseket ismertek és a futtatókörnyezet által betöltött.

Az összes beépített Azure Functions-bindings elfogadták ezt a modellt, és már nem szerepelnek alapértelmezetten, kivéve az időzített eseményindítóknak és a HTTP-eseményindítóval. Ezek a bővítmények automatikusan települnek, függvények, például a Visual Studio eszközök vagy a portálon keresztül létrehozásakor.

Az alábbi táblázat azt jelzi, hogy melyik kötések támogatottak a minden egyes modul verzióját.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
* [Az Azure Functions runtime verziók bemutatásához](set-runtime-version.md)
* [Kiadási megjegyzések](https://github.com/Azure/azure-functions-host/releases)
