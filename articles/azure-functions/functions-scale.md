---
title: Az Azure Functions méretezése és üzemeltetése
description: Megtudhatja, hogyan választhat Azure Functions fogyasztási terv és a Prémium csomag között.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5504416d09cf6b3f75d02e29cc93b0278cc42386
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85117131"
---
# <a name="azure-functions-scale-and-hosting"></a>Az Azure Functions méretezése és üzemeltetése

Ha az Azure-ban hoz létre egy Function alkalmazást, ki kell választania egy üzemeltetési csomagot az alkalmazáshoz. A Azure Functionshoz három alapszintű üzemeltetési csomag érhető el: a [fogyasztási csomag](#consumption-plan), a [Prémium csomag](#premium-plan)és a [dedikált (App Service) csomag](#app-service-plan). Minden üzemeltetési csomag általánosan elérhető a Linux és a Windows rendszerű virtuális gépeken egyaránt.

A kiválasztott üzemeltetési csomag a következő viselkedéseket diktálja:

* A Function alkalmazás skálázása.
* Az egyes functions app-példányok számára elérhető erőforrások.
* Speciális funkciók támogatása, például Azure Virtual Network-kapcsolat.

A használat és a Prémium csomag is automatikusan hozzáadja a számítási teljesítményt a kód futásakor. Az alkalmazás a terhelés kezeléséhez, illetve a kód futásának leállításakor skálázáshoz szükséges. A használati terv esetében nem kell fizetnie az üresjáratban lévő virtuális gépekért, vagy a kapacitást előre kell foglalni.  

A Prémium csomag további funkciókat kínál, például a prémium szintű számítási példányokat, a példányok határozatlan idejű megtartásának és VNet-kapcsolatának lehetőségét.

A App Service-csomag lehetővé teszi, hogy kihasználhassa az Ön által felügyelt dedikált infrastruktúrát. A Function alkalmazás nem méretezhető az események alapján, ami azt jelenti, hogy soha nem méretezi a nulla értéket. (Megköveteli, hogy [a always on](#always-on) engedélyezve legyen.)

A különböző üzemeltetési csomagok (beleértve a Kubernetes-alapú üzemeltetést is) részletes összehasonlítását lásd: [üzemeltetési csomagok összehasonlítása szakasz](#hosting-plans-comparison).

## <a name="consumption-plan"></a>Használatalapú csomag

A használati terv használatakor a rendszer a Azure Functions gazdagép példányait dinamikusan hozzáadja és eltávolítja a bejövő események száma alapján. Ezek a kiszolgáló nélküli csomagok automatikusan méreteznek, és csak akkor kell fizetni a számítási erőforrásokért, amikor a függvények futnak. Használatalapú csomag esetében egy függvény végrehajtása túllépi a konfigurált időkorlátot.

A számlázás a végrehajtások száma, a végrehajtási idő és a felhasznált memória alapján történik. A számlázási folyamat a függvényalkalmazás összes függvényét figyelembe veszi. További információkért tekintse meg a [Azure functions díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/functions/).

A felhasználási terv az alapértelmezett üzemeltetési csomag, amely a következő előnyöket kínálja:

* Csak akkor kell fizetnie, ha a függvények futnak
* Automatikus méretezés automatikusan, akár nagy terhelésű időszakok esetén is

Az azonos régióban található Function apps ugyanahhoz a használati tervhez is hozzárendelhető. Nincs hátránya, hogy több alkalmazás is fut ugyanabban a használati csomagban. Ha több alkalmazást rendel ugyanahhoz a használati tervhez, az egyes alkalmazások rugalmasságát, méretezhetőségét és megbízhatóságát nem befolyásolja.

Ha többet szeretne megtudni arról, hogyan becsülheti fel a költségeket a használati tervekben, tekintse meg a [felhasználási terv költségeinek megismerése](functions-consumption-costs.md)című témakört.

## <a name="premium-plan"></a><a name="premium-plan"></a>Prémium szintű csomag

A Prémium csomag használatakor a rendszer a Azure Functions gazdagép példányait a beérkező események száma alapján adja hozzá és távolítja el, a használati tervhez hasonlóan.  A Prémium csomag a következő funkciókat támogatja:

* Állandóan meleg példányok a hideg indítás elkerüléséhez
* VNet-kapcsolat
* Korlátlan végrehajtás időtartama (60 perc garantált)
* Prémium példányok méretei (egy mag, két mag és négy fő példány)
* További kiszámítható díjszabás
* Nagy sűrűségű alkalmazások kiosztása több Function-alkalmazással rendelkező csomagokhoz

A következő témakörben megtudhatja, hogyan hozhat létre egy Function-alkalmazást egy prémium csomagban: [Azure functions Prémium csomag](functions-premium-plan.md).

A számlázás és a felhasznált memória helyett a Prémium csomag számlázása a szükséges és az előre bemelegített példányok által használt fő másodpercek és memória számától függ. Legalább egy példánynak minden esetben melegnek kell lennie. Ez azt jelenti, hogy a végrehajtások számától függetlenül az aktív csomagokra vonatkozó minimális havi költségnek kell lennie. Ne feledje, hogy a prémium szintű csomag összes funkciója előre bemelegítő és aktív példányokat oszt meg.

Vegye figyelembe a Azure Functions prémium csomagot a következő helyzetekben:

* A Function apps folyamatosan vagy csaknem folyamatosan fut.
* Nagy számú kisméretű végrehajtással rendelkezik, és magas végrehajtási számlával rendelkezik, de a használati terv alacsony GB-os, második számlával rendelkezik.
* Több CPU-vagy memória-beállításra van szüksége, mint amit a használati terv biztosít.
* A kódnak hosszabb ideig kell futnia, mint a felhasználási tervben [engedélyezett maximális végrehajtási idő](#timeout) .
* Olyan funkciókat kell megkövetelni, amelyek csak prémium csomagon, például virtuális hálózati kapcsolaton érhetők el. 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Dedikált (App Service) csomag

A Function apps ugyanazon a dedikált virtuális gépeken is futtatható, mint a többi App Service alkalmazás (alapszintű, standard, prémium és elkülönített SKU).

A következő helyzetekben vegye fontolóra App Service tervet:

* Vannak olyan meglévő, nem használt virtuális gépek, amelyek már futtatnak más App Service-példányokat.
* Egyéni rendszerképet szeretne megadni a függvények futtatásához.

Ugyanezt a funkciót a App Service csomagban lévő Function apps esetében is megfizeti, mint más App Service-erőforrásokhoz, például a webalkalmazásokhoz. Az App Service-csomag működésével kapcsolatos részletekért tekintse [meg a Azure app Service tervek részletes áttekintését](../app-service/overview-hosting-plans.md).

App Service csomaggal a további virtuálisgép-példányok hozzáadásával manuálisan is felskálázást hajthat végre. Az autoscale is engedélyezhető. További információ: a [Példányszám manuális vagy automatikus skálázása](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Egy másik App Service terv kiválasztásával is méretezhető. További információ: alkalmazás vertikális [Felskálázása az Azure-ban](../app-service/manage-scale-up.md). 

Ha a JavaScript-függvényeket App Service csomagon futtatja, olyan csomagot válasszon, amelynek kevesebb vCPU van. További információ: [Choose Single-core app Service Plans](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

A [app Service Environment](../app-service/environment/intro.md) (bevezetéssel) való futtatása lehetővé teszi a függvények teljes elkülönítését és a nagy léptékű előnyök kihasználását.

### <a name="always-on"></a><a name="always-on"></a>Always on

Ha App Service csomagot futtat, engedélyezze a **mindig** beállítást, hogy a Function alkalmazás megfelelően fusson. Egy App Service-csomag esetében a függvények futtatókörnyezete néhány perc inaktivitás után üresjáratba lép, így csak a HTTP-eseményindítók fognak felébredni a függvényekből. Az Always on csak App Service csomagon érhető el. A platform egy használati tervben automatikusan aktiválja a Function Apps szolgáltatást.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Még ha a mindig engedélyezve van, az egyes függvények végrehajtási időtúllépését a `functionTimeout` Project fájl [host.jsjának](functions-host-json.md#functiontimeout) beállítása vezérli.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Meglévő alkalmazás üzemeltetési tervének meghatározása

A Function app által használt üzemeltetési csomag meghatározásához tekintse meg a [Azure Portal](https://portal.azure.com)a Function alkalmazás **áttekintés** lapján található **app Service terv** című részt. Az árképzési csomag megjelenítéséhez válassza ki a **app Service terv**nevét, majd a bal oldali ablaktáblán válassza a **Tulajdonságok** lehetőséget.

![Méretezési terv megtekintése a portálon](./media/functions-scale/function-app-overview-portal.png)

Az Azure CLI használatával is meghatározhatja a tervet, a következőképpen:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Ha a parancs kimenete a `dynamic` , a Function alkalmazás a használati tervben van. Ha a parancs kimenete a `ElasticPremium` , a Function alkalmazás a prémium csomagban van. Az összes többi érték egy App Service terv különböző szintjeire utal.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Bármely csomag esetében a Function alkalmazáshoz egy általános Azure Storage-fiók szükséges, amely támogatja az Azure Blob, a üzenetsor, a fájlok és a Table Storage szolgáltatást. Ennek az az oka, hogy Azure Functions az Azure Storage-ra támaszkodik olyan műveletekre, mint az eseményindítók és a naplózási függvények végrehajtása, de egyes tárolási fiókok nem támogatják a várólistákat és a táblákat. Ezek a fiókok, amelyek csak blob Storage-fiókokat (beleértve a Premium Storage-t) és az általános célú Storage-fiókokat, amelyek zóna-redundáns tárolási replikációval rendelkeznek, a rendszer kiszűri a meglévő **Storage** -fiókok kiválasztásával, amikor létrehoz egy Function alkalmazást.

A Function alkalmazás által használt Storage-fiókot az eseményindítók és kötések is felhasználhatják az alkalmazásadatok tárolásához. A tárolási igényű műveletek esetében azonban külön Storage-fiókot kell használnia.  

Több Function-alkalmazás is lehetséges, hogy problémák nélkül megoszthatja ugyanazt a Storage-fiókot. (Ennek jó példája, ha több alkalmazást fejleszt a helyi környezetben az Azure Storage Emulator használatával, amely egy Storage-fiókhoz hasonlóan működik.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

További információ a Storage-fiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#core-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>A felhasználás és a Prémium csomag működése

A használat és a prémium csomagok esetében a Azure Functions infrastruktúra a funkciók gazdagépének további példányainak hozzáadásával méretezi a processzor-és memória-erőforrásokat a függvények által aktivált események száma alapján. A functions gazdagép összes példánya a használati tervben legfeljebb 1,5 GB memóriával és egy PROCESSZORral rendelkezik.  A gazdagép egy példánya a teljes Function alkalmazás, ami azt jelenti, hogy a Function app-ban található összes függvény egy adott példányon belül található, és egy időben méretezhető. Az azonos felhasználási csomaggal rendelkező alkalmazások egymástól függetlenül méretezhetők.  A Prémium csomag esetében a csomag mérete határozza meg az adott példányon lévő összes alkalmazás rendelkezésre álló memóriáját és PROCESSZORát.  

A függvény kódjának fájljai a függvény fő Storage-fiókján Azure Files megosztásokon tárolódnak. Ha törli a Function alkalmazás fő Storage-fiókját, a rendszer törli a függvény kódjának fájljait, és nem állítható helyre.

### <a name="runtime-scaling"></a>Futtatókörnyezet skálázása

A Azure Functions a *skálázási vezérlő* nevű összetevővel figyeli az események sebességét, és meghatározza, hogy a méretezést ki kell-e bővíteni. A skálázási vezérlő heurisztikus adattípust használ az egyes triggerekhez. Ha például egy Azure üzenetsor-tárolási triggert használ, az a várólista hossza és a legrégebbi üzenetsor-üzenet kora alapján méretezhető.

A Azure Functions méretezési egysége a Function alkalmazás. A Function alkalmazás skálázásakor a rendszer további erőforrásokat foglal le a Azure Functions gazdagép több példányának futtatásához. Fordítva, ahogy a számítási igény csökken, a skálázási vezérlő eltávolítja a Function Host-példányokat. A példányok száma végül nulla értékre van *méretezve* , ha egyetlen függvény sem fut a Function alkalmazásban.

![Vezérlő-figyelési események méretezése és példányok létrehozása](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>Hidegindító

Azt követően, hogy a függvény alkalmazása több percig is tétlen volt, a platform méretezheti a példányok számát, amelyeken az alkalmazás nullára fut. A következő kérelem a nullára való skálázás késleltetését adja meg. Ezt a késést a rendszer _hideg indításnak_nevezzük. A Function alkalmazás által betöltendő függőségek száma hatással lehet a hideg kezdési időpontra. A hidegebb indítás több problémát jelent a szinkron műveletek, például a HTTP-eseményindítók esetében, amelyeknek választ kell visszaadniuk. Ha a ritkán használt funkciók hatással vannak a függvényekre, érdemes lehet egy prémium csomagon vagy egy dedikált csomagban futtatni, amely mindig engedélyezve van.   

### <a name="understanding-scaling-behaviors"></a>A skálázási viselkedés ismertetése

A skálázás több tényezőn is változhat, és a kiválasztott trigger és nyelv alapján különbözőképpen méretezhető. Van néhány bonyolult méretezési mód az alábbiakkal kapcsolatban:

* Egyetlen Function-alkalmazás csak legfeljebb 200 példányra méretezhető. Egyetlen példány egyszerre több üzenetet vagy kérelmet is feldolgozhat, így az egyidejű végrehajtások száma nem megengedett.
* HTTP-eseményindítók esetén az új példányok lefoglalása legfeljebb egyszer, másodpercenként történik.
* A nem HTTP-triggerek esetében az új példányok lefoglalása legfeljebb 30 másodpercenként történik. A skálázás gyorsabb, ha [prémium](#premium-plan)szintű csomagban fut.
* Service Bus eseményindítók esetében a leghatékonyabb skálázáshoz használja az erőforrások _kezelése_ jogosultságokat. A _figyelési_ jogosultságok használata esetén a skálázás nem annyira pontos, mert a várólista hossza nem használható a skálázási döntések tájékoztatására. Ha többet szeretne megtudni a Service Bus hozzáférési házirendekben található jogosultságok beállításáról, tekintse meg a [megosztott hozzáférés engedélyezési házirendjét](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Az Event hub-eseményindítók esetében lásd a [méretezési útmutatót](functions-bindings-event-hubs-trigger.md#scaling) a dokumentációban. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Ajánlott eljárások és minták méretezhető alkalmazásokhoz

A Function alkalmazásnak számos aspektusa van, amely hatással lesz a méretezésre, beleértve a gazdagép konfigurációját, a futásidejű lábnyomot és az erőforrás-hatékonyságot.  További információ: a [teljesítmény szempontjairól szóló cikk méretezhetőségi szakasza](functions-best-practices.md#scalability-best-practices). Azt is figyelembe kell vennie, hogy a kapcsolatok hogyan viselkednek, mint a Function app Scales. További információ: a [kapcsolatok kezelése a Azure Functionsban](manage-connections.md).

A Python és a Node.js méretezésével kapcsolatos további információkért lásd: [Azure functions Python fejlesztői útmutató – skálázás és Egyidejűség](functions-reference-python.md#scaling-and-concurrency) és [Azure functions Node.js fejlesztői útmutató – skálázás és Egyidejűség](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Számlázási modell

A különböző csomagokra vonatkozó számlázást részletesen ismertetjük a [Azure functions díjszabási oldalán](https://azure.microsoft.com/pricing/details/functions/). A függvény az alkalmazás szintjén összesíti a használatot, és csak a függvény kódjának végrehajtásához tartozó időt számítja ki. A számlázási egységek a következők:

* **Erőforrás-használat GB-ban (GB-s)**. A függvény alkalmazásban található összes függvényhez a memória mérete és a végrehajtási idő kombinációja számítja ki. 
* **Végrehajtások**. Minden alkalommal, amikor egy függvény egy esemény eseményindítóra válaszol.

Hasznos lekérdezések és információk arról, hogyan értelmezhető a használati számla a [Számlázási GYIK](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)szolgáltatásban.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>Szolgáltatási csomagok összehasonlítása

Az alábbi összehasonlító táblázat a Azure Functions app üzemeltetési csomag választásának meghozatalához szükséges fontos szempontokat mutatja be:

### <a name="plan-summary"></a>Csomag összegzése
| | |
| --- | --- |  
|**[Felhasználási terv](#consumption-plan)**| Automatikusan méretezhető, és csak a számítási erőforrásokért kell fizetnie, ha a függvények futnak. A használati terv a függvények gazdagépének példányait dinamikusan hozzáadja és eltávolítja a bejövő események száma alapján.<br/> ✔ Alapértelmezett üzemeltetési csomag.<br/>A ✔ csak akkor kell fizetnie, ha a függvények futnak.<br/>a ✔ automatikusan kibővíthető, akár nagy terhelésű időszakok esetén is.|  
|**[Prémium szintű csomag](#premium-plan)**|Míg az automatikus skálázás igény szerint történik, az előre betöltött feldolgozók az üresjárat után késedelem nélkül futtathatják az alkalmazásokat, és a virtuális hálózatok-hez csatlakoznak. Vegye figyelembe a Azure Functions prémium csomagot az alábbi helyzetekben, a App Service-csomag összes funkciója mellett: <br/>✔ A Function apps folyamatosan, vagy majdnem folyamatosan fut.<br/>✔ Nagy mennyiségű kis végrehajtással rendelkezik, és magas végrehajtási számlával rendelkezik, de a használati terv alacsony GB-os, második számlával rendelkezik.<br/>✔ Több CPU-vagy memória-beállításra van szüksége, mint amit a használati terv biztosít.<br/>✔ A kódnak hosszabb ideig kell futnia, mint a felhasználási tervben engedélyezett maximális végrehajtási idő.<br/>✔ Olyan funkciókat kell megkövetelni, amelyek csak a [prémium csomaggal, például a virtuális hálózati kapcsolattal használhatók.|  
|**[Dedikált](#app-service-plan)**<sup>1</sup> . csomag|A függvényeket egy App Service csomagon belül futtathatja normál App Service csomag díjszabásával. Jó illeszkedés a hosszú ideig futó műveletekhez, valamint a prediktív skálázás és a költségek kiszámításához. A következő helyzetekben vegye fontolóra App Service tervet:<br/>✔ Rendelkezik olyan meglévő, nem használt virtuális gépekkel, amelyek már futtatnak más App Service példányokat.<br/>✔ Szeretné megadni a függvények futtatására szolgáló egyéni rendszerképet.|  
|**[ASE](#app-service-plan)**<sup>1</sup> . kiegészítő|A App Service Environment (benyújtó) egy App Service funkció, amely teljesen elkülönített és dedikált környezetet biztosít a App Service alkalmazások biztonságos, nagy léptékű futtatásához. A ASE megfelelőek a következőket igénylő alkalmazás-munkaterhelésekhez: <br/>✔ Nagyon nagy léptékű.<br/>✔ Elkülönítés és biztonságos hálózati hozzáférés.<br/>✔ A nagy memória kihasználtsága.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | A Kubernetes egy teljesen elkülönített és dedikált környezetet biztosít, amely a Kubernetes platformon fut.  A Kubernetes a következőket igénylő alkalmazás-munkaterhelések esetén megfelelő: <br/>✔ Egyéni hardverkövetelmények.<br/>✔ Elkülönítés és biztonságos hálózati hozzáférés.<br/>✔ Lehetőség hibrid vagy többfelhős környezetben való futtatásra.<br/>✔ Fut a meglévő Kubernetes-alkalmazások és-szolgáltatások mellett.|  

<sup>1</sup> a különböző app Service-csomagokra vonatkozó beállításokra vonatkozó meghatározott korlátokat a [app Service csomag korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című részben tekintheti meg.

### <a name="operating-systemruntime"></a>Operációs rendszer/futtatókörnyezet

| | <sup>1</sup> . Linux<br/>Csak kód | Windows<sup>2</sup><br/>Csak kód | Linux<sup>1, 3</sup><br/>Docker-tároló |
| --- | --- | --- | --- |
| **[Felhasználási terv](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Nincs támogatás  |
| **[Prémium szintű csomag](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| <sup>4</sup> . **[dedikált terv](#app-service-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)**<sup>4</sup> . kiegészítő | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n.a. | n.a. |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> A Linux az egyetlen támogatott operációs rendszer a Python futtatókörnyezeti verem számára.  
<sup>2</sup> A Windows az egyetlen támogatott operációs rendszer a PowerShell futásidejű verem számára.   
<sup>3</sup> A Linux az egyetlen támogatott operációs rendszer a Docker-tárolók számára.
<sup>4</sup> a különböző app Servicei csomagokra vonatkozó beállításokra vonatkozó korlátozásokat a [app Service csomag korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című részben tekintheti meg.

### <a name="scale"></a>Méretezés

| | Horizontális felskálázás | Példányok maximális száma |
| --- | --- | --- |
| **[Felhasználási terv](#consumption-plan)** | Esemény vezérelt. Automatikus méretezés automatikusan, akár nagy terhelésű időszakok esetén is. A Azure Functions infrastruktúra a függvények által aktivált események száma alapján méretezi a processzor-és memória-erőforrásokat a functions gazdagép további példányainak hozzáadásával. | 200 |
| **[Prémium szintű csomag](#premium-plan)** | Esemény vezérelt. Automatikus méretezés automatikusan, akár nagy terhelésű időszakok esetén is. A Azure Functions infrastruktúra a függvények által aktivált események száma alapján méretezi a processzor-és memória-erőforrásokat a functions gazdagép további példányainak hozzáadásával. |100|
| **[Dedikált](#app-service-plan)**<sup>1</sup> . csomag | Manuális/automatikus méretezés |10-20|
| **[ASE](#app-service-plan)**<sup>1</sup> . kiegészítő | Manuális/automatikus méretezés |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Eseményvezérelt, az [KEDA](https://keda.sh)-t használó Kubernetes-fürtök esetén a-alapú autoskálázás. | &nbsp;A &nbsp; fürttől függően változik.&nbsp;&nbsp;|

<sup>1</sup> a különböző app Service-csomagokra vonatkozó beállításokra vonatkozó meghatározott korlátokat a [app Service csomag korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című részben tekintheti meg.

### <a name="cold-start-behavior"></a>A hidegindító viselkedése

|    |    | 
| -- | -- |
| **[Felhasználási &nbsp; terv](#consumption-plan)** | Az alkalmazások akár nulla is lehetnek, ha egy adott időszak tétlensége üresjáratban van, ami azt jelenti, hogy egyes kérések további késéssel is rendelkezhetnek indításkor.  A használati tervnek van néhány optimalizációja, amelyekkel csökkenthető a hideg kezdési idő, beleértve az olyan előre bemelegítő függvényekből való húzást is, amelyek már rendelkeznek a Function Host és a nyelvi folyamatok futtatásával. |
| **[Prémium szintű csomag](#premium-plan)** | Állandóan meleg példányok, hogy elkerülje a hideg indítást. |
| **[Dedikált](#app-service-plan)**<sup>1</sup> . csomag | Ha dedikált csomagban fut, a functions-gazdagép folyamatosan futhat, ami azt jelenti, hogy a hideg indítás nem igazán jelent problémát. |
| **[ASE](#app-service-plan)**<sup>1</sup> . kiegészítő | Ha dedikált csomagban fut, a functions-gazdagép folyamatosan futhat, ami azt jelenti, hogy a hideg indítás nem igazán jelent problémát. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | A KEDA konfigurációjától függ. Az alkalmazások konfigurálható úgy, hogy mindig fussanak, és soha ne legyenek hidegen elindítva, vagy úgy legyenek beállítva, hogy nullára legyenek állítva, ami az új eseményeken a hidegindító 

<sup>1</sup> a különböző app Service-csomagokra vonatkozó beállításokra vonatkozó meghatározott korlátokat a [app Service csomag korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című részben tekintheti meg.

### <a name="service-limits"></a>Szolgáltatási korlátozások

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>Hálózatkezelési funkciók

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>Számlázás

| | | 
| --- | --- |
| **[Felhasználási terv](#consumption-plan)** | Csak a függvények futtatásának idejére kell fizetnie. A számlázás a végrehajtások száma, a végrehajtási idő és a felhasznált memória alapján történik. |
| **[Prémium szintű csomag](#premium-plan)** | A Prémium csomag a szükséges és az előre bemelegített példányok által használt fő másodpercek és memória számán alapul. A csomagoknak legalább egy példányát mindig melegen kell tartani. Ez a csomag előre jelezhető díjszabást biztosít. |
| **[Dedikált](#app-service-plan)**<sup>1</sup> . csomag | Ugyanezt a funkciót a App Service csomagban lévő Function apps esetében is megfizeti, mint más App Service-erőforrásokhoz, például a webalkalmazásokhoz.|
| **[ASE](#app-service-plan)**<sup>1</sup> . kiegészítő | az infrastruktúráért fizetett és a közszolgáltatási infrastruktúra méretével nem módosítható a havi díj. Ezen felül App Service díjcsomag vCPU is. Egy ASE környezeten belül az összes üzemeltetett alkalmazás az elkülönített díjszabású termékváltozatba tartozik. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Csak a Kubernetes-fürt költségeit kell fizetnie; a függvények további számlázása nem történik meg. A Function alkalmazás alkalmazás-számítási feladatként fut a fürt tetején, ugyanúgy, mint egy normál alkalmazás. |

<sup>1</sup> a különböző app Service-csomagokra vonatkozó beállításokra vonatkozó meghatározott korlátokat a [app Service csomag korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című részben tekintheti meg.

## <a name="next-steps"></a>További lépések

+ [Gyors útmutató: Azure Functions projekt létrehozása a Visual Studio Code használatával](functions-create-first-function-vs-code.md)
+ [Üzembe helyezési technológiák Azure Functions](functions-deployment-technologies.md) 
+ [Azure Functions – fejlesztői útmutató](functions-reference.md)
