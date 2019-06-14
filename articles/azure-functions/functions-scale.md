---
title: Az Azure Functions méretezése és üzemeltetése |} A Microsoft Docs
description: Útmutató az Azure Functions Használatalapú tarifacsomagjának és a prémium szintű csomag közül választhat.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, használatalapú csomag, prémium szintű csomag, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3253cc7e379ae63880d533f14bc76e7af5a4425a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050555"
---
# <a name="azure-functions-scale-and-hosting"></a>Az Azure Functions méretezése és üzemeltetése

Ha függvényalkalmazást hoz létre az Azure-ban, ki kell választania egy szolgáltatási csomagot az alkalmazáshoz. Nincsenek elérhető Azure Functions szolgáltatási csomagok három: [Használatalapú csomag](#consumption-plan), [prémium szintű csomag](#premium-plan), és [App Service-csomag](#app-service-plan).

Úgy dönt, a üzemeltetési terv szabja meg az alábbi viselkedés tapasztalható:

* Hogyan betűmérethez a függvényalkalmazást.
* Minden függvény alkalmazáspéldány számára elérhető erőforrások.
* Speciális szolgáltatásokra, például a virtuális hálózatok közötti kapcsolat támogatása.

Használat és a prémium csomagok automatikusan adja hozzá a számítási teljesítményt, ha a kódja fut. Az alkalmazás horizontálisan felskálázott, ha a terhelés kezeléséhez szükséges, és ha leállítja a kódot futtató vertikálisan leskálázni. A Használatalapú csomagok esetében a is nem kell fizetnie a tétlen virtuális gépeket vagy foglalhat le előre a kapacitás.  

Prémium szintű csomag további szolgáltatásokat is nyújt, mint például a prémium szintű számítási példányok, a példányok meleg tartani határozatlan ideig lehetősége és a virtuális hálózatok közötti kapcsolat.

App Service-csomag lehetővé teszi, hogy dedikált infrastruktúra, amely felügyelt. A függvényalkalmazás nem méretezhető események alapján, ami azt jelenti, akkor soha nem méretezhető nullára. (Ehhez meg kell adni [mindig a](#always-on) engedélyezve van.)

> [!NOTE]
> Használat és a prémium csomagban elérhető, a függvény alkalmazás-erőforrást a terv tulajdonság módosításával között válthat.

## <a name="hosting-plan-support"></a>Üzemeltetési terv támogatása

Funkciók támogatása esik a következő két kategóriába sorolhatók:

* _Általánosan elérhető (GA)_ : teljes körűen támogatott, és éles használatra jóváhagyott.
* _Előzetes verzió_: még nem támogatott, és hagyja jóvá, éles környezetben való használatra.

Az alábbi táblázat a három szolgáltatási csomagok támogatását aktuális szintjét jelzi, ha a Windows vagy Linux rendszeren futó:

| | Használatalapú csomag | Prémium szintű csomag | Dedikált terv |
|-|:----------------:|:------------:|:----------------:|
| Windows | FE | előzetes verzió | FE |
| Linux | előzetes verzió | – | FE |

## <a name="consumption-plan"></a>Használatalapú csomag

A Használatalapú csomag használatakor a példányok az Azure Functions-gazdagép dinamikusan hozzáadása és eltávolítása a bejövő események száma alapján. A kiszolgáló nélküli csomag automatikusan skálázható, és kell fizetnie a számítási erőforrásokat, csak akkor, amikor a függvények futnak. A Használatalapú csomag a függvény végrehajtási időkorlátja konfigurálható bizonyos idő után.

Végrehajtások száma, végrehajtási időt, és a használt memória alapján számoljuk. A számlázás belül függvényalkalmazás a függvények összesített érték. További információkért lásd: a [Azure Functions díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/functions/).

A Használatalapú szolgáltatási csomag alapértelmezett és a következő előnyöket biztosítja:

* Fizetés csak amikor a függvények futnak
* Automatikus skálázáshoz, még akkor is magas időszakokban betöltése

Függvényalkalmazások ugyanabban a régióban a Használatalapú csomagot is hozzárendelhető. Nincs hátulütője vagy hatással az ugyanazon Használatalapú csomagban fut több alkalmazás kellene. Több alkalmazás hozzárendelése a ugyanolyan használatalapú csomag nem befolyásolja a rugalmasság, a skálázhatóság vagy a minden alkalmazás megbízhatóságát.

## <a name="premium-plan"></a>Prémium szintű csomag (előzetes verzió)

Ha a prémium csomag használata esetén példányok az Azure Functions-gazdagép hozzáadása és eltávolítása, csakúgy, mint a Használatalapú csomag bejövő események száma alapján.  Prémium szintű csomag támogatja a következő funkciókat:

* Bármely hidegindítási elkerülése érdekében mindvégig meleg példányok
* Virtuális hálózatok közötti kapcsolat
* Korlátlan számú végrehajtási időtartama
* Prémium szintű példányméretek (egymagos, két fő és négy core példányok)
* Több kiszámítható díjszabás
* Nagy sűrűségű alkalmazás foglalási több függvényalkalmazás-csomagok

Ezek a beállítások konfigurálásához információk megtalálhatók a [prémium csomag a dokumentum az Azure Functions](functions-premium-plan.md).

Végrehajtási és a felhasznált memória / a számlázási helyett a prémium szintű csomag díjszabása alapján maghasználat (másodperc), a végrehajtási idő és a szükséges és a fenntartott példányok között használt memória száma.  Legalább egy példánya mindig kell lennie a meleg at. Ez azt jelenti, hogy nincs-e egy rögzített havi költség / aktív tervet, függetlenül a végrehajtásainak számát.

Fontolja meg az Azure Functions prémium szintű csomag a következő esetekben:

* A függvényalkalmazások futtassa folyamatosan vagy gyakorlatilag, folyamatosan.
* Több CPU és memória lehetőség a Használatalapú csomag által biztosított mi van szüksége.
* A kód futtatásához hosszabb, mint a [engedélyezett maximális végrehajtási ideje](#timeout) a Használatalapú díjcsomag.
* Csak a prémium szintű csomagot, például a virtuális hálózat és VPN-kapcsolat elérhető funkciókat követel meg.

> [!NOTE]
> A prémium szintű csomag előzetes verziója jelenleg csak támogatja az Azure Functions a Windows.

A prémium szintű csomagot futtatja a JavaScript-függvények, amikor egy példányt, amely rendelkezik kevesebb vcpu-k kell kiválasztani. További információkért lásd: a [válassza a prémium csomagok egymagos](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedikált (App Service) megtervezése

A függvényalkalmazások is futtathatja a dedikált virtuális gépekről, más App Service-alkalmazások (alapszintű, Standard, prémium szintű és elkülönített termékváltozatok).

Fontolja meg az App Service-csomag, az alábbi esetekben:

* Rendelkezik már meglévő, azokat az alacsony kihasználtságú virtuális gépeket, amelyek már fut más App Service-példányt.
* Szeretne biztosítani egy egyéni rendszerképet, amelyre a függvények futtatását.

Használatalapú azonos függvény alkalmazások számára az App Service-csomag más App Service-erőforrások, például a web apps esetében tenné. Az App Service-csomag működésével kapcsolatos részletekért lásd: a [Azure App Service díjcsomagjainak részletes áttekintése](../app-service/overview-hosting-plans.md).

Az App Service-csomag manuálisan horizontálisan további Virtuálisgép-példányok hozzáadásával. Automatikus skálázás is engedélyezheti. További információkért lásd: [példányszám manuális vagy automatikus méretezése](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Is skálázhatja egy másik App Service-csomag kiválasztásával. További információkért lásd: [az Azure-beli alkalmazás vertikális felskálázása](../app-service/web-sites-scale.md). 

JavaScript-függvények az App Service-csomag futtatásakor, kevesebb vcpu-k rendelkező csomagot érdemes választania. További információkért lásd: [válassza ki az App Service-csomagok egymagos](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Ha az App Service-csomagot futtatja, engedélyeznie kell a **mindig** beállítást, hogy a függvényalkalmazás megfelelően fut-e. App Service-csomag a functions futtatókörnyezete ismertetett üresjárati néhány perc inaktivitás után, így csak a HTTP-eseményindítók fog "felébresztéséhez" az funkciók. Mindig érhető el csak az App Service-csomag. A Használatalapú csomag a platform aktiválja a függvényalkalmazás automatikusan.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Még az Always On engedélyezve van, az egyes függvények végrehajtási időtúllépésének értéke szabályozza a `functionTimeout` beállítását a [host.json](functions-host-json.md#functiontimeout) soubor projektu.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>A szolgáltatási csomag egy meglévő alkalmazás meghatározása

A szolgáltatási csomag, a függvényalkalmazás által használt megállapításához lásd: **App Service-csomag / tarifacsomag** a a **áttekintése** a függvényalkalmazás a lapon a [az Azure portal](https://portal.azure.com). App Service-csomagok a tarifacsomag is jelzi.

![Méretezési csomag megtekintése a portálon](./media/functions-scale/function-app-overview-portal.png)

Az Azure CLI használatával határozza meg a következő, a terv:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Ha ez a parancs kimenete van `dynamic`, a függvényalkalmazást a Használatalapú csomagban van. Ha ez a parancs kimenete van `ElasticPremium`, a prémium szintű csomag szerepel a függvényalkalmazást. Minden egyéb értékek azt jelzik, hogy az App Service-csomag különböző rétegek.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Minden olyan csomag, a függvényalkalmazás egy általános Azure Storage-fiókot, amely támogatja az Azure Blob, Queue, fájlokat és Table storage van szükség. Ennek az az oka a Functions műveletekhez, például az eseményindítók kezelése és a függvénykivételek naplózása támaszkodjon az Azure Storage, de egyes storage-fiókok nem támogatják az üzenetsorokat és táblákat. Ezek a fiókok, többek között (beleértve a premium storage) csak a blob storage-fiókok vagy általános célú tárfiókok, a zónaredundáns tárolás replikációval, vannak szűrve kibővített a meglévő **Tárfiók** Amikor létrehoz egy függvényalkalmazást beállításokat.

<!-- JH: Does using a Premium Storage account improve perf? -->

További információ a tárfiókok típusairól, lásd: [az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>A használat és a prémium szintű csomag működése

A használat és a prémium csomagok az Azure Functions infrastruktúra méretezi a CPU és memória-erőforrások a Functions gazdagép, az funkciók vannak a kiváltó események száma alapján további példányok hozzáadásával. A Functions gazdagép, a használatalapú csomagban minden példánya 1,5 GB memória- és a egy CPU korlátozódik.  A gazdagép egy példánya a teljes függvényalkalmazás, azaz függvény alkalmazás megosztás erőforrás-példány és a méretezési csoport belül lévő összes függvény egyszerre. A függvényalkalmazások, amely ugyanabban a használatalapú csomagban vannak méretezve, egymástól függetlenül.  A prémium csomag a csomag méretét határozza meg a rendelkezésre álló memória és CPU összes alkalmazás-csomag azon a példányon.  

A funkció fő tárfiókot az Azure Files megosztásainak függvény kódfájlok tárolódnak. Ha a fő tárfiókot a függvényalkalmazás töröl, a függvény kódját fájlok törlődnek, és nem állítható helyre.

> [!NOTE]
> Amikor egy blob eseményindító a Használatalapú csomag használata esetén lehet akár egy 10 perces késleltetést új blobok feldolgozása. Ez a késleltetés következik be, amikor a függvényalkalmazás inaktív volt. Miután a függvényalkalmazás fut, blobok feldolgozása azonnal megtörténik. A hidegindítási késleltetés elkerülése érdekében használja a prémium szintű csomag vagy a [Event Grid-trigger](functions-bindings-event-grid.md). További információkért lásd: [a blob eseményindító kötés áttekintésével foglalkozó cikkben](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Futásidejű méretezése

Az Azure Functions összetevőt használja a *méretezési vezérlő* mértéke a események figyelésére, és határozza meg, hogy horizontálisan felskálázhatja vagy leskálázhatja a. A méretezési csoport vezérlő heurisztika használ minden egyes típusú trigger esetén. Például ha használja az Azure Queue storage eseményindítója, átméreteződik az üzenetsor hossza és a legrégebbi üzenetsori üzenet kora alapján.

Az Azure Functions méretezési egység a függvényalkalmazáshoz. Amikor a függvényalkalmazás horizontálisan, további erőforrások az Azure Functions-gazdagép több példányának futtatása, vannak lefoglalva. Ezzel szemben a számítási igény csökken, a méretezési csoport vezérlő eltávolítja a függvény példányait üzemeltetni. A példányok számát van végül vertikálisan leskálázni nullánál nem működik egy függvényalkalmazás belül futtatásakor.

![Méretezési csoport vezérlő események figyelése és a példány létrehozása](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Skálázási viselkedés ismertetése

Skálázás tényezők és az eseményindító és a kiválasztott nyelvvel eltérően szerint méretezhető, több eltérőek lehetnek. Van néhány jainak részleteivel kellene skálázási viselkedés érdemes figyelembe vennie:

* Egy adott függvényalkalmazás legfeljebb 200 példányig skálázható fel. Egyetlen példány előfordulhat, hogy egynél több üzenet vagy a kérelem egyszerre feldolgozni, ezért nincs párhuzamos végrehajtások száma beállított korlátot.
* HTTP-eseményindítók, az új példányok csak lefoglalt 1 másodpercenként legfeljebb egyszer.
* -HTTP eseményindítók, az új példányok csak lefoglalt 30 másodpercenként legfeljebb egyszer.

Különböző triggereket is rendelkezhetnek, különböző méretezési korlátok, valamint a dokumentált alatt:

* [Event Hub](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Ajánlott eljárások és minták a méretezhető alkalmazások

Számos szempontot, egy függvényalkalmazást, amelyek befolyásolják, arról, hogy ez lesz skálázva, például a gazdagép konfigurálása, a futásidejű erőforrás-igényű és erőforrás-hatékonyság.  További információkért lásd: a [a teljesítmény szempontok cikk méretezhetőség szakasza](functions-best-practices.md#scalability-best-practices). Azt is figyelembe kell, a függvény alkalmazás skálázását követve rugalmasan méretezhető kapcsolatok működése. További információkért lásd: [kezelése az Azure Functions kapcsolatok](manage-connections.md).

### <a name="billing-model"></a>Számlázási modell

A különböző csomagok díjszabása részletes leírása a a [Azure Functions díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/functions/). Használati összesített értéket jelenít meg a függvény alkalmazási szintű, és csak a függvénykódot végrehajtott idő számát. A számlázási egység az alábbiak:

* **Erőforrás-használat gigabájtmásodpercben (GB-s)** . Számított, amely a memóriaméret és a egy függvényalkalmazás lévő összes függvény végrehajtási idejének. 
* **Végrehajtások**. Minden alkalommal, amikor egy függvény végrehajtása, amely egy eseményvezérelt eseményindítóra válasz számítanak.

Hasznos lekérdezések és a számlázással kapcsolatos használati információk találhatók [a számlázási rendszerre – gyakori kérdések](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Szolgáltatási korlátozások

Az alábbi táblázat azt jelzi, hogy a vonatkozó korlátokat tartalmazza a függvényalkalmazásokat a különböző szolgáltatási csomagok való futtatáskor:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
