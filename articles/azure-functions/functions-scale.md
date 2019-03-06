---
title: Az Azure Functions méretezése és üzemeltetése |} A Microsoft Docs
description: Útmutató az Azure Functions használatalapú és App Service-csomag közül választhat.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, használatalapú csomag, app service-csomag, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/28/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cd3f7f1f1f17d6dedea0157760b03c7e55e3d8a
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410094"
---
# <a name="azure-functions-scale-and-hosting"></a>Az Azure Functions méretezése és üzemeltetése

Az Azure Functions két különböző módban futtatható: Használatalapú csomag és az Azure App Service-csomag. A Használatalapú csomag automatikusan lefoglalja a számítási erőforrásokat a kód futtatásakor. Az alkalmazás horizontálisan felskálázott, ha a terhelés kezeléséhez szükséges, és vertikálisan leskálázni, ha nem fut a kódot. Nem kell fizetnie a tétlen virtuális gépeket vagy foglalhat le előre a kapacitás.

> [!NOTE]  
> Használatalapú csomag Linux rendszer [jelenleg nyilvános előzetes verzióban](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/).

Ha nem ismeri az Azure Functions, tekintse meg a [Azure Functions áttekintő](functions-overview.md).

Amikor létrehoz egy függvényalkalmazást, válassza ki a Functions szolgáltatási csomag az alkalmazásban. A terv vagy egy példányát a *Azure Functions gazdagép* végrehajtja a feladatokat. A terv vezérlők típusa:

* Hogyan példányait üzemeltetni horizontálisan felskálázott.
* Minden állomás számára elérhető erőforrások.

> [!IMPORTANT]
> Választania kell a szolgáltatási csomag a függvényalkalmazás létrehozása során típusát. Ezt követően nem módosítható.

Az App Service-csomag méretezhető különböző mennyiségű erőforrás lefoglalásához rétegek között. A Használatalapú csomagok esetében az Azure Functions automatikusan kezeli az összes erőforrás-elosztás. 

## <a name="consumption-plan"></a>Használatalapú csomag

A Használatalapú csomag használatakor példányok az Azure Functions-gazdagép dinamikusan hozzáadása és eltávolítása a bejövő események száma alapján. A kiszolgáló nélküli csomag automatikusan skálázható, és kell fizetnie a számítási erőforrásokat, csak akkor, amikor a függvények futnak. A Használatalapú csomag a függvény végrehajtási időkorlátja konfigurálható bizonyos idő után.

Végrehajtások száma, végrehajtási időt, és a használt memória alapján számoljuk. A számlázás belül függvényalkalmazás a függvények összesített érték. További információkért lásd: a [Az Azure Functions díjszabását ismertető lapon].

A Használatalapú szolgáltatási csomag alapértelmezett és a következő előnyöket biztosítja:

* A fizetés csak amikor a függvények futnak.
* Automatikus skálázáshoz, akár nagy időszakokban betölteni.

## <a name="app-service-plan"></a>App Service-csomag

Dedikált virtuális gépeken, a Basic, Standard, prémium szintű és izolált változatában, futtassa a függvényalkalmazásokat a dedikált App Service-csomag, amely megegyezik más App Service-alkalmazások. A függvényalkalmazás, ami azt jelenti, hogy az a funkciók gazdagép is lehet dedikált virtuális gépek kiosztott [mindig fut](#always-on). App Service-csomagok támogatják a Linux.

Fontolja meg az App Service-csomag, a következő esetekben:

* Rendelkezik már meglévő, azokat az alacsony kihasználtságú virtuális gépeket, amelyek már fut más App Service-példányt.
* A függvényalkalmazások futtassa folyamatosan vagy gyakorlatilag, folyamatosan. Ebben az esetben egy App Service-csomag költséghatékonyabban helyezhetők.
* Több CPU és memória lehetőség milyen áll rendelkezésre a Használatalapú díjcsomag szükséges.
* A kód futtatásához hosszabb, mint a [engedélyezett maximális végrehajtási ideje](#timeout) a Használatalapú díjcsomag.
* Csak az App Service-csomag, például az App Service Environment-környezet, a virtuális hálózat és VPN-kapcsolat és a nagyobb Virtuálisgép-méretek támogatása elérhető funkciókat követel meg.
* Szeretné futtatni a függvényalkalmazást linuxon, vagy szeretne biztosítani egy egyéni rendszerképet, amelyre a függvények futtatását.

Virtuális gép költséggel jár a végrehajtások száma, végrehajtási időt, és a használt memória leválasztja. Ennek eredményeképpen nem kell fizetnie több, mint a Virtuálisgép-példány lefoglalandó költsége. Az App Service-csomag működésével kapcsolatos részletekért lásd: a [Azure App Service díjcsomagjainak részletes áttekintése](../app-service/overview-hosting-plans.md). 

Az App Service-csomag manuálisan horizontális felskálázása további Virtuálisgép-példányok hozzáadásával, vagy engedélyezheti az automatikus méretezés. További információkért lásd: [példányszám manuális vagy automatikus méretezése](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Is skálázhatja egy másik App Service-csomag kiválasztásával. További információkért lásd: [az Azure-beli alkalmazás vertikális felskálázása](../app-service/web-sites-scale.md). 

JavaScript-függvények az App Service-csomag futtatásakor, kevesebb vcpu-k rendelkező csomagot érdemes választania. További információkért lásd: [válassza ki az App Service-csomagok egymagos](functions-reference-node.md#choose-single-vcpu-app-service-plans).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

###<a name="always-on"></a> Always On

Ha az App Service-csomagot futtatja, engedélyeznie kell a **mindig** beállítást, hogy a függvényalkalmazás megfelelően fut-e. App Service-csomag a functions futtatókörnyezete ismertetett üresjárati néhány perc inaktivitás után, így csak a HTTP-eseményindítók fog "felébresztéséhez" az funkciók. Mindig érhető el csak az App Service-csomag. A Használatalapú csomag a platform aktiválja a függvényalkalmazás automatikusan.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="what-is-my-hosting-plan"></a>Mit jelent a szolgáltatási csomag

A szolgáltatási csomag, a függvényalkalmazás által használt megállapításához lásd: **App Service-csomag / tarifacsomag** a a **áttekintése** a függvényalkalmazás a lapon a [az Azure portal](https://portal.azure.com). App Service-csomagok a tarifacsomag is jelzi. 

![Méretezési csomag megtekintése a portálon](./media/functions-scale/function-app-overview-portal.png)

Az Azure CLI használatával határozza meg a következő, a terv:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Ha ez a parancs kimenete van `dynamic`, a függvényalkalmazást a Használatalapú csomagban van. Minden más értékek azt jelzik, hogy az App Service-csomag szinten.

Még az Always On engedélyezve van, az egyes függvények végrehajtási időtúllépésének értéke szabályozza a `functionTimeout` beállítását a [host.json](functions-host-json.md#functiontimeout) soubor projektu.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Használatalapú és App Service-csomag, a függvényalkalmazás egy általános Azure Storage-fiókot, amely támogatja az Azure Blob, Queue, fájlokat és Table storage van szükség. Ennek az az oka a függvények és eseményindítók kezelése és a függvénykivételek naplózása hasonló műveletek céljából támaszkodik Azure Storage, de egyes storage-fiókok nem támogatják az üzenetsorokat és táblákat. Ezek a fiókok, többek között (beleértve a premium storage) csak a blob storage-fiókok vagy általános célú tárfiókok, a zónaredundáns tárolás replikációval, vannak szűrve kibővített a meglévő **Tárfiók** Amikor létrehoz egy függvényalkalmazást beállításokat.

<!-- JH: Does using a Premium Storage account improve perf? -->

További információ a tárfiókok típusairól, lásd: [az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>A Használatalapú csomag működése

A méretezési csoport vezérlő a Használatalapú csomag automatikusan méretezi magát CPU és memória-erőforrások a Functions gazdagép, az funkciók vannak a kiváltó események száma alapján további példányok hozzáadásával. A Functions gazdagép minden példánya 1,5 GB memória korlátozódik.  A gazdagép egy példányát a függvényalkalmazást, azaz függvény alkalmazás megosztás erőforrás-példány és a méretezési csoport belül lévő összes függvény egyszerre. A függvényalkalmazások, amely ugyanabban a Használatalapú csomagban vannak méretezve, egymástól függetlenül.  

A Használatalapú szolgáltatási csomag használatakor a funkció fő tárfiókot az Azure Files megosztásainak függvény kódfájlok tárolja. Ha a fő tárfiókot a függvényalkalmazás töröl, a függvény kódját fájlok törlődnek, és nem állítható helyre.

> [!NOTE]
> Amikor egy blob eseményindító a Használatalapú csomag használata esetén lehet akár egy 10 perces késleltetést új blobok feldolgozása. Ez a késleltetés következik be, amikor a függvényalkalmazás inaktív volt. Miután a függvényalkalmazás fut, blobok feldolgozása azonnal megtörténik. A hidegindítási késleltetés elkerülése érdekében használja az App Service-csomag **Always On** engedélyezve van, vagy az Event Grid eseményindító használata. További információkért lásd: [a blob eseményindító kötés áttekintésével foglalkozó cikkben](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Futásidejű méretezése

Az Azure Functions összetevőt használja a *méretezési vezérlő* mértéke a események figyelésére, és határozza meg, hogy horizontálisan felskálázhatja vagy leskálázhatja a. A méretezési csoport vezérlő heurisztika használ minden egyes típusú trigger esetén. Például ha használja az Azure Queue storage eseményindítója, átméreteződik az üzenetsor hossza és a legrégebbi üzenetsori üzenet kora alapján.

A skálázási egység a függvényalkalmazáshoz. Amikor a függvényalkalmazás horizontálisan, további erőforrások az Azure Functions-gazdagép több példányának futtatása, vannak lefoglalva. Ezzel szemben a számítási igény csökken, a méretezési csoport vezérlő eltávolítja a függvény példányait üzemeltetni. A példányok számát van végül vertikálisan leskálázni nullánál nem működik egy függvényalkalmazás belül futtatásakor.

![Méretezési csoport vezérlő események figyelése és a példány létrehozása](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Skálázási viselkedés ismertetése

Skálázás tényezők és az eseményindító és a kiválasztott nyelvvel eltérően szerint méretezhető, több eltérőek lehetnek. Van azonban néhány skálázási szempont, amely már ma is jelen van a rendszerben:

* Egy adott függvényalkalmazás legfeljebb 200 példányig skálázható fel. Egyetlen példány előfordulhat, hogy egynél több üzenet vagy a kérelem egyszerre feldolgozni, ezért nincs párhuzamos végrehajtások száma beállított korlátot.
* HTTP-eseményindítók, az új példányok csak lefoglalt 1 másodpercenként legfeljebb egyszer.
* -HTTP eseményindítók, az új példányok csak lefoglalt 30 másodpercenként legfeljebb egyszer.

Különböző triggereket is rendelkezhetnek, különböző méretezési korlátok, valamint a dokumentált alatt:

* [Event Hub](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Ajánlott eljárások és minták a méretezhető alkalmazások

Számos szempontot, egy függvényalkalmazást, amelyek befolyásolják, arról, hogy ez lesz skálázva, például a gazdagép konfigurálása, a futásidejű erőforrás-igényű és erőforrás-hatékonyság.  További információkért lásd: a [a teljesítmény szempontok cikk méretezhetőség szakasza](functions-best-practices.md#scalability-best-practices). Azt is figyelembe kell, a függvény alkalmazás skálázását követve rugalmasan méretezhető kapcsolatok működése. További információkért lásd: [kezelése az Azure Functions kapcsolatok](manage-connections.md).

### <a name="billing-model"></a>Számlázási modell

A Használatalapú díjcsomag részletes leírása a számlázás a [Az Azure Functions díjszabását ismertető lapon]. Használati összesített értéket jelenít meg a függvény alkalmazási szintű, és csak a függvénykódot végrehajtott idő számát. A számlázási egység az alábbiak:

* **Erőforrás-használat gigabájtmásodpercben (GB-s)**. Számított, amely a memóriaméret és a egy függvényalkalmazás lévő összes függvény végrehajtási idejének. 
* **Végrehajtások**. Minden alkalommal, amikor egy függvény végrehajtása, amely egy eseményvezérelt eseményindítóra válasz számítanak.

[Az Azure Functions díjszabását ismertető lapon]: https://azure.microsoft.com/pricing/details/functions
