---
title: Méretezés és üzemeltetés Azure Functions
description: Megtudhatja, hogyan választhat Azure Functions fogyasztási terv és a Prémium csomag között.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4ff3ebf6239f9b62409ff0885f23115711e33cb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77584541"
---
# <a name="azure-functions-scale-and-hosting"></a>Méretezés és üzemeltetés Azure Functions

Ha az Azure-ban hoz létre egy Function alkalmazást, ki kell választania egy üzemeltetési csomagot az alkalmazáshoz. A Azure Functions három üzemeltetési csomag érhető el: a használati [terv](#consumption-plan), a [Prémium csomag](#premium-plan)és a [dedikált (App Service) csomag](#app-service-plan).

A kiválasztott üzemeltetési csomag a következő viselkedéseket diktálja:

* A Function alkalmazás skálázása.
* Az egyes functions app-példányok számára elérhető erőforrások.
* Speciális funkciók támogatása, például Azure Virtual Network-kapcsolat.

A használat és a Prémium csomag is automatikusan hozzáadja a számítási teljesítményt a kód futásakor. Az alkalmazás a terhelés kezeléséhez, illetve a kód futásának leállításakor skálázáshoz szükséges. A használati terv esetében nem kell fizetnie az üresjáratban lévő virtuális gépekért, vagy a kapacitást előre kell foglalni.  

A Prémium csomag további funkciókat kínál, például a prémium szintű számítási példányokat, a példányok határozatlan idejű megtartásának és VNet-kapcsolatának lehetőségét.

A App Service-csomag lehetővé teszi, hogy kihasználhassa az Ön által felügyelt dedikált infrastruktúrát. A Function alkalmazás nem méretezhető az események alapján, ami azt jelenti, hogy soha nem méretezi a nulla értéket. (Megköveteli, hogy [a always on](#always-on) engedélyezve legyen.)

## <a name="hosting-plan-support"></a>Üzemeltetési csomag támogatása

A szolgáltatások támogatása a következő két kategóriába tartozik:

* _Általánosan elérhető (GA)_ : teljes mértékben támogatott és jóváhagyott éles használatra.
* _Előzetes_verzió: még nem teljes mértékben támogatott, és nem engedélyezett éles használatra.

A következő táblázat a három üzemeltetési csomag jelenlegi támogatását mutatja be Windows vagy Linux rendszeren:

| | Használatalapú csomag | Prémium szintű csomag | Dedikált csomag |
|-|:----------------:|:------------:|:----------------:|
| Windows | FE | FE | FE |
| Linux | FE | FE | FE |

## <a name="consumption-plan"></a>Használatalapú csomag

A használati terv használatakor a rendszer a Azure Functions gazdagép példányait dinamikusan hozzáadja és eltávolítja a bejövő események száma alapján. Ez a kiszolgáló nélküli csomag automatikusan méretezi a számítási erőforrásokat, és csak akkor kell fizetnie, ha a függvények futnak. Egy használati tervben a függvény végrehajtásának időtúllépése egy konfigurálható idő után.

A számlázás a végrehajtások száma, a végrehajtás ideje és a felhasznált memória alapján történik. A számlázás a Function alkalmazás összes függvényében összesítve történik. További információkért tekintse meg a [Azure functions díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/functions/).

A felhasználási terv az alapértelmezett üzemeltetési csomag, amely a következő előnyöket kínálja:

* Csak akkor kell fizetnie, ha a függvények futnak
* Automatikus méretezés automatikusan, akár nagy terhelésű időszakok esetén is

Az azonos régióban található Function apps ugyanahhoz a használati tervhez is hozzárendelhető. Nincs hátránya, hogy több alkalmazás is fut ugyanabban a használati csomagban. Ha több alkalmazást rendel ugyanahhoz a használati tervhez, az egyes alkalmazások rugalmasságát, méretezhetőségét és megbízhatóságát nem befolyásolja.

Ha többet szeretne megtudni arról, hogyan becsülheti fel a költségeket a használati tervekben, tekintse meg a [felhasználási terv költségeinek megismerése](functions-consumption-costs.md)című témakört.

## <a name="premium-plan"></a>Prémium csomag

A Prémium csomag használatakor a rendszer a Azure Functions gazdagép példányait a beérkező események száma alapján adja hozzá és távolítja el, a használati tervhez hasonlóan.  A Prémium csomag a következő funkciókat támogatja:

* Állandóan meleg példányok a hideg indítás elkerüléséhez
* VNet-kapcsolat
* Korlátlan végrehajtás időtartama
* Prémium példányok méretei (egy mag, két mag és négy fő példány)
* További kiszámítható díjszabás
* Nagy sűrűségű alkalmazások kiosztása több Function-alkalmazással rendelkező csomagokhoz

A beállítások konfigurálásának módjával kapcsolatban a [Azure functions Premium csomag dokumentációjában](functions-premium-plan.md)találhat további információt.

A számlázás és a felhasznált memória helyett a Prémium csomag számlázása a szükséges és az előre bemelegített példányok által használt fő másodpercek és memória számától függ. Legalább egy példánynak minden esetben melegnek kell lennie. Ez azt jelenti, hogy a végrehajtások számától függetlenül az aktív csomagokra vonatkozó minimális havi költség. Ne feledje, hogy a prémium szintű csomag összes funkciója előre bemelegítő és aktív példányokat oszt meg.

Vegye figyelembe a Azure Functions prémium csomagot a következő helyzetekben:

* A Function apps folyamatosan vagy csaknem folyamatosan fut.
* Nagy számú kisméretű végrehajtással rendelkezik, és magas végrehajtási számlával rendelkezik, de a használati terv alacsony GB-os, második számlával rendelkezik.
* Több CPU-vagy memória-beállításra van szüksége, mint amit a használati terv biztosít.
* A kódnak hosszabb ideig kell futnia, mint a felhasználási tervben [engedélyezett maximális végrehajtási idő](#timeout) .
* Olyan funkciókat kell megkövetelni, amelyek csak prémium csomagon, például virtuális hálózati kapcsolaton érhetők el.

A JavaScript-függvények prémium csomagon való futtatásakor olyan példányt válasszon, amelynek kevesebb vCPU van. További információ: az [egymagos prémium csomagok kiválasztása](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedikált (App Service) csomag

A Function apps ugyanazon a dedikált virtuális gépeken is futtatható, mint a többi App Service alkalmazás (alapszintű, standard, prémium és elkülönített SKU).

A következő helyzetekben vegye fontolóra App Service tervet:

* Vannak olyan meglévő, nem használt virtuális gépek, amelyek már futtatnak más App Service-példányokat.
* Egyéni rendszerképet szeretne megadni a függvények futtatásához.

Ugyanezt a funkciót a App Service csomagban lévő Function apps esetében is megfizeti, mint más App Service-erőforrásokhoz, például a webalkalmazásokhoz. Az App Service-csomag működésével kapcsolatos részletekért tekintse [meg a Azure app Service tervek részletes áttekintését](../app-service/overview-hosting-plans.md).

App Service csomaggal a további virtuálisgép-példányok hozzáadásával manuálisan is felskálázást hajthat végre. Az autoscale is engedélyezhető. További információ: a [Példányszám manuális vagy automatikus skálázása](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Egy másik App Service terv kiválasztásával is méretezhető. További információ: alkalmazás vertikális [Felskálázása az Azure-ban](../app-service/manage-scale-up.md). 

Ha a JavaScript-függvényeket App Service csomagon futtatja, olyan csomagot válasszon, amelynek kevesebb vCPU van. További információ: [Choose Single-core app Service Plans](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Always on

Ha App Service csomagot futtat, engedélyezze a **mindig** beállítást, hogy a Function alkalmazás megfelelően fusson. Egy App Service-csomag esetében a függvények futtatókörnyezete néhány perc inaktivitás után üresjáratba lép, így csak a HTTP-eseményindítók fognak felébredni a függvényekből. Az Always on csak App Service csomagon érhető el. A platform egy használati tervben automatikusan aktiválja a Function Apps szolgáltatást.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Még ha a mindig engedélyezve van, az egyes függvények végrehajtási időtúllépését a [Host. JSON](functions-host-json.md#functiontimeout) projektfájl `functionTimeout` beállítása vezérli.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Meglévő alkalmazás üzemeltetési tervének meghatározása

A Function app által használt üzemeltetési csomag meghatározásához tekintse meg a [Azure Portal](https://portal.azure.com)a Function alkalmazás **áttekintés** lapján a **app Service terv/árképzési szintet** . App Service csomagok esetében a díjszabási szintet is jelezni kell.

![Méretezési terv megtekintése a portálon](./media/functions-scale/function-app-overview-portal.png)

Az Azure CLI használatával is meghatározhatja a tervet, a következőképpen:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Ha a parancs kimenete `dynamic`, a Function alkalmazás a használati tervben van. Ha a parancs kimenete `ElasticPremium`, a Function alkalmazás a prémium csomagban van. Az összes többi érték egy App Service terv különböző szintjeire utal.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Bármely csomag esetében a Function alkalmazáshoz egy általános Azure Storage-fiók szükséges, amely támogatja az Azure Blob, a üzenetsor, a fájlok és a Table Storage szolgáltatást. Ennek az az oka, hogy a függvények az Azure Storage-on alapulnak olyan műveletekre, mint az eseményindítók és a naplózási függvények végrehajtása, de egyes Storage-fiókok nem támogatják a várólistákat és a táblákat. Ezek a fiókok, amelyek csak blob Storage-fiókokat (beleértve a Premium Storage-t) és az általános célú Storage-fiókokat, amelyek zóna-redundáns tárolási replikációval rendelkeznek, a rendszer kiszűri a meglévő **Storage** -fiókok kiválasztásával, amikor létrehoz egy Function alkalmazást.

A Function alkalmazás által használt Storage-fiókot az eseményindítók és kötések is felhasználhatják az alkalmazásadatok tárolásához. A tárolási igényű műveletek esetében azonban külön Storage-fiókot kell használnia.  

Minden esetben lehetséges, hogy több Function-alkalmazás is ugyanazzal a Storage-fiókkal rendelkezik, problémák nélkül. (Ennek jó példája, ha több alkalmazást fejleszt a helyi környezetben az Azure Storage Emulator használatával, amely egy Storage-fiókhoz hasonlóan működik.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

További információ a Storage-fiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>A felhasználás és a Prémium csomag működése

A használat és a prémium csomagok esetében a Azure Functions infrastruktúra a funkciók gazdagépének további példányainak hozzáadásával méretezi a processzor-és memória-erőforrásokat a függvények által aktivált események száma alapján. A functions gazdagép összes példánya a használati tervben legfeljebb 1,5 GB memóriával és egy PROCESSZORral rendelkezik.  A gazdagép egy példánya a teljes Function alkalmazás, ami azt jelenti, hogy a Function app-ban található összes függvény egy adott példányon belül található, és egy időben méretezhető. Az azonos felhasználási csomaggal rendelkező alkalmazások egymástól függetlenül méretezhetők.  A Prémium csomag esetében a csomag mérete határozza meg az adott példányon lévő összes alkalmazás rendelkezésre álló memóriáját és PROCESSZORát.  

A függvény kódjának fájljai a függvény fő Storage-fiókján Azure Files megosztásokon tárolódnak. Ha törli a Function alkalmazás fő Storage-fiókját, a rendszer törli a függvény kódjának fájljait, és nem állítható helyre.

### <a name="runtime-scaling"></a>Futtatókörnyezet skálázása

A Azure Functions a *skálázási vezérlő* nevű összetevővel figyeli az események sebességét, és meghatározza, hogy a méretezést ki kell-e bővíteni. A skálázási vezérlő heurisztikus adattípust használ az egyes triggerekhez. Ha például egy Azure üzenetsor-tárolási triggert használ, az a várólista hossza és a legrégebbi üzenetsor-üzenet kora alapján méretezhető.

A Azure Functions méretezési egysége a Function alkalmazás. A Function alkalmazás skálázásakor a rendszer további erőforrásokat foglal le a Azure Functions gazdagép több példányának futtatásához. Fordítva, ahogy a számítási igény csökken, a skálázási vezérlő eltávolítja a Function Host-példányokat. A példányok száma végül nulla értékre van *méretezve* , ha egyetlen függvény sem fut a Function alkalmazásban.

![Vezérlő-figyelési események méretezése és példányok létrehozása](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>A skálázási viselkedés ismertetése

A skálázás több tényezőn is változhat, és a kiválasztott trigger és nyelv alapján különbözőképpen méretezhető. Van néhány bonyolult méretezési mód az alábbiakkal kapcsolatban:

* Egyetlen Function-alkalmazás csak legfeljebb 200 példányra méretezhető. Egyetlen példány egyszerre több üzenetet vagy kérelmet is feldolgozhat, így az egyidejű végrehajtások száma nem megengedett.
* HTTP-eseményindítók esetén az új példányok csak a legfeljebb 1 másodpercenként lesznek lefoglalva.
* A nem HTTP-triggerek esetében az új példányok csak 30 másodpercenként egyszer lesznek lefoglalva.

A különböző eseményindítók eltérő skálázási korlátokkal is rendelkezhetnek, és az alábbiakban dokumentálva vannak:

* [Event Hub](functions-bindings-event-hubs-trigger.md#scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Ajánlott eljárások és minták méretezhető alkalmazásokhoz

A Function alkalmazásnak számos aspektusa van, amely hatással lesz a méretezésre, beleértve a gazdagép konfigurációját, a futásidejű lábnyomot és az erőforrás-hatékonyságot.  További információ: a [teljesítmény szempontjairól szóló cikk méretezhetőségi szakasza](functions-best-practices.md#scalability-best-practices). Azt is figyelembe kell vennie, hogy a kapcsolatok hogyan viselkednek, mint a Function app Scales. További információ: a [kapcsolatok kezelése a Azure Functionsban](manage-connections.md).

A Python és a Node. js méretezésével kapcsolatos további információkért lásd: [Azure functions Python fejlesztői útmutató – skálázás és Egyidejűség](functions-reference-python.md#scaling-and-concurrency) és [Azure functions Node. js fejlesztői útmutató – skálázás és Egyidejűség](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Számlázási modell

A különböző csomagokra vonatkozó számlázást részletesen ismertetjük a [Azure functions díjszabási oldalán](https://azure.microsoft.com/pricing/details/functions/). A függvény az alkalmazás szintjén összesíti a használatot, és csak a függvény kódjának végrehajtásához tartozó időt számítja ki. A számlázási egységek a következők:

* **Erőforrás-használat GB-ban (GB-s)** . A függvény alkalmazásban található összes függvényhez a memória mérete és a végrehajtási idő kombinációja számítja ki. 
* **Végrehajtások**. Minden alkalommal, amikor egy függvény egy esemény eseményindítóra válaszol.

Hasznos lekérdezések és információk arról, hogyan értelmezhető a használati számla a [Számlázási GYIK](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)szolgáltatásban.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>A szolgáltatásra vonatkozó korlátozások

A következő táblázat azokat a korlátozásokat mutatja be, amelyek a Function apps alkalmazásra vonatkoznak a különböző üzemeltetési csomagokban való futtatáskor:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
