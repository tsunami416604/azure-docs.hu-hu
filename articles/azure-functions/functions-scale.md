---
title: "Az Azure Functions méretezése és üzemeltetéséhez |} Microsoft Docs"
description: "Útmutató az Azure Functions használat terv és az App Service-csomag választhat."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure funkciók, Funkciók, fogyasztás terv, app service-csomag, esemény feldolgozása, webhookokkal, dinamikus számítási, kiszolgáló nélküli architektúrája"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83431c58fedd85e469ab1bf2903fd517e6338e15
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="azure-functions-scale-and-hosting"></a>Az Azure Functions méretezése és üzemeltetéséhez

Az Azure Functions két különböző módban is futtathatja: fogyasztás terv és az Azure App Service-csomag. A felhasználási terv automatikusan osztja ki a számítási teljesítményt, a kódja fut, kimenő terhelés kezelésére, szükség szerint arányosan, és majd arányosan csökken, amikor a kód nem fut. Nem kell fizetni a tétlen virtuális gépeket, és nem kell előzetesen tartalékkapacitás. Ez a cikk foglalkozik a fogyasztás terv egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) az app model. Az App Service-csomag működésével kapcsolatos részletekért lásd: a [Azure App Service-csomagok részletes áttekintése](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

>[!NOTE]  
> [Linux futtató](functions-create-first-azure-function-azure-cli-linux.md) jelenleg csak az App Service-csomag érhető el.

Ha nem ismeri az Azure Functions, tekintse meg a [Azure Functions áttekintése](functions-overview.md).

Amikor létrehoz egy függvény alkalmazást, konfigurálnia kell az alkalmazást tartalmazó funkciók üzemeltetési terv. Mindkét üzemmódban, egy példányát a *Azure Functions állomás* feladatokat hajt végre. A terv vezérlők típusa:

* Hogyan állomás példányok is kiterjeszthető.
* Minden állomás számára elérhető erőforrások.

Válasszon jelenleg üzemeltetési terv a függvény alkalmazás létrehozásakor típusú. Ezt követően nem módosítható. 

Az App Service-csomag a méretezhető különböző mennyiségű erőforrást lefoglalni rétegek között. A felhasználási terv Azure Functions automatikusan kezeli az összes erőforrás-elosztás.

## <a name="consumption-plan"></a>Használatalapú csomag

Amikor egy fogyasztás tervet használja, az Azure Functions állomás példányai dinamikusan felvétele, illetve eltávolítása a bejövő események száma alapján. Ez a csomag automatikusan méretezi, és van szó, a számítási erőforrások csak akkor, ha a függvények futnak. Felhasználás tervezze a függvény legfeljebb 10 perc futtathatja. 

> [!NOTE]
> Az alapértelmezett időtúllépési fogyasztás tervezze függvények érték 5 perc. Az érték növelhető 10 perc a függvény alkalmazás tulajdonságának módosításával `functionTimeout` a a [host.json](functions-host-json.md#functiontimeout) projektfájlt.

Számlázási végrehajtások, a végrehajtási idő és a felhasznált memória alapul. Számlázási összesíti egy függvény alkalmazásból minden funkciók között. További információkért lásd: a [árképzést ismertető oldalra Azure Functions].

A felhasználási tervben az üzemeltetési terv alapértelmezett, és a következő előnyöket biztosítja:
- Után kell fizetnie, csak ha a függvények futnak.
- Horizontális felskálázás automatikusan, még nagy idején betölteni.

## <a name="app-service-plan"></a>App Service-csomag

Az App Service-csomag a függvény alkalmazások futtassa a Basic, Standard, Premium és elkülönített SKU, hasonlók a Web Apps, az API-alkalmazások és a Mobile Apps dedikált virtuális gépeken. Dedikált virtuális gépek számára kiosztott az App Service-alkalmazásokhoz, ami azt jelenti, hogy a funkciók állomás mindig működik. App Service-csomagokról Linux támogatja.

Fontolja meg az App Service-csomag a következő esetekben:
- Rendelkezik már meglévő, a túl többi App Service-példány már fut a virtuális gépek.
- A függvény alkalmazások futtatásához folyamatosan vagy majdnem folyamatosan várt. Ebben az esetben egy App Service-csomag lehet költséghatékonyabb.
- További CPU és memória beállítások, mint mi biztosítja a fogyasztás terven van szüksége.
- Hosszabb, mint a maximális végrehajtási idő (10 perc) a fogyasztás tervét engedélyezett futtatásához szükséges.
- Csak az App Service-csomagot, például támogatja az App Service Environment-környezet, a virtuális hálózat és a VPN-kapcsolat és a nagyobb Virtuálisgép-méretek elérhető funkciókat követel meg. 
- A függvény app Linux rendszeren futtatja, vagy lehetővé szeretné tenni a funkciók futtatására egyéni lemezkép.

A virtuális gépek leválasztja a végrehajtások, a végrehajtási idő és a felhasznált memória több költség. Emiatt nem kell fizetnie több, mint a Virtuálisgép-példány lefoglalandó költségét. Az App Service-csomag működésével kapcsolatos részletekért lásd: a [Azure App Service-csomagok részletes áttekintése](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Az App Service-csomagot lehet manuálisan horizontálisan további Virtuálisgép-példányok hozzáadásával, vagy engedélyezheti az automatikus skálázási. További információkért lásd: [méretezése példányszám manuális vagy automatikus](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). Is költenie válasszon egy másik App Service-csomagra. További információkért lásd: [vertikális felskálázás az Azure alkalmazásban](../app-service/web-sites-scale.md). 

Ha azt tervezi, JavaScript-funkcióként futhat az App Service-csomagot, válasszon egy tervet, amely kevesebb Vcpu rendelkezik. További információkért lásd: a [válassza ki az App Service-csomagokról egymagos](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
###Always On

Ha az App Service-csomagot futtatja, engedélyeznie kell a **mindig a** beállítása, hogy a függvény alkalmazás megfelelő működését. Az App Service-csomagot a functions futtatókörnyezete kerül üresjárati tétlen, néhány perc múlva, csak a HTTP-eseményindítók fogja "felébreszteni" függvényeit. Ez hasonlít hogyan WebJobs rendelkeznie kell mindig engedélyezve van. 

Always On érhető csak az App Service-csomag. Felhasználás tervezze a platform aktiválja függvény alkalmazások automatikusan.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

A felhasználási terv vagy az App Service-csomag a függvény az alkalmazás csak a egy általános Azure Storage-fiók, amely támogatja az Azure Blob, Queue, fájlok és Table storage. Belsőleg az Azure Functions Azure tárolást használ műveletek, például eseményindítók kezelése és naplózási funkciót végrehajtások. Néhány tárfiókok nem támogatják az üzenetsorok és táblák, például csak a blob storage-fiókok (beleértve a prémium szintű storage) és az általános célú tárfiókok zónaredundáns tárolás replikáció. Ezek a fiókok kiszűri a **Tárfiók** panel egy függvény alkalmazás létrehozásakor.

<!-- JH: Does using a PRemium Storage account improve perf? -->

Tárfióktípusokat kapcsolatos további információkért lásd: [az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>A felhasználási terv működése

A felhasználási tervben a skála vezérlő automatikusan méretezi CPU és memória-erőforrások hozzáadásával további példányait a funkciók gazdagép, a funkciók által kiváltott a várakozó események száma alapján. A funkciók állomás minden példánya 1,5 GB memória korlátozódik.  A gazdagép egy példány a függvény App, tehát függvényen belüli összes funkciójának app erőforrások megosztása belül egy példány és a skála egyszerre.

A felhasználás üzemeltetési terv használatakor függvény kódfájlok Azure fájlmegosztásokat a funkció fő tárfiók tárolja. A fő tárfiókot, a függvény alkalmazás törlése, a függvény kód fájlok törlődnek, és nem állítható helyre.

> [!NOTE]
> Egy blob eseményindító használatakor a fogyasztás terven lehet legfeljebb 10 perces késleltetést új blobok feldolgozása, ha egy függvény app inaktív állapotba került. A függvény alkalmazás futtatása után blobok feldolgozása azonnal megtörténik. A kezdeti késleltetés elkerülése érdekében fontolja meg az alábbi lehetőségek közül:
> - Gazdagép a függvény alkalmazást, az App Service-csomagot, a mindig engedélyezve van.
> - Egy másik mechanizmus használatával indul el, a blob feldolgozására, például egy esemény rács előfizetés vagy egy üzenetsor-üzenetet, amely tartalmazza a blob neve. Egy vonatkozó példáért lásd: a [C# a parancsfájlt és a JavaScript-példák a BLOB bemeneti és kimeneti kötések](functions-bindings-storage-blob.md#input--output---example).

### <a name="runtime-scaling"></a>Futásidejű skálázás

Az Azure Functions összetevőt használja a *méretezési vezérlő* események sebessége figyelésére, és szükség van-e ki, vagy a méretezhető. A skála vezérlő heurisztikát alkalmaz az egyes eseményindító. Például az Azure Queue storage eseményindító használatakor méretezés a várólista hossza és a legrégebbi üzenetsor korát alapján.

A skálázási egység a függvény alkalmazást. Ha a függvény app horizontálisan, további erőforrásokat az Azure Functions gazdagép több példányát futtatni. Ezzel ellentétben a számítási igény szerinti csökken, a méretezési vezérlő függvény állomás példányok eltávolítja. A példányok száma van végül méretezhető nulla nem működik a funkció alkalmazások futtatásakor.

![Skála vezérlő események figyelése és példányok létrehozása](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Skálázási viselkedés ismertetése

Skálázás tényezők és a menübeállításoktól függően az eseményindító és a kiválasztott nyelvvel méretezést számú eltérőek lehetnek. Vannak azonban néhány szempontja a méretezés, amely létezik a rendszerben ma:
* Egy egyetlen függvény alkalmazást csak legfeljebb 200 példányok lesz skálázva. Egyetlen példány előfordulhat, hogy több üzenetet vagy kérelem az egyszerre feldolgozandó azonban, nem áll rendelkezésre a készlet egyidejű végrehajtások számára vonatkozó korlátozást.
* Új példányok csak oszt ki 10 másodpercenként legfeljebb egyszer.

Különböző eseményindítók is rendelkezhetnek, különböző méretezési korlátok, valamint az olyan dokumentált alatt:

* [Event Hub](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Ajánlott eljárásairól és mintáiról méretezhető alkalmazások

Számos szempontot, amelyek befolyásolják, mennyire azt lehessen méretezni, többek között a gazdagép-konfigurálás, futásidejű erőforrásigényét és erőforrás-hatékonyság függvény alkalmazás.  Nézet a [méretezhetőség című szakaszban a teljesítmény szempontok](functions-best-practices.md#scalability-best-practices) további információt.

### <a name="billing-model"></a>Számlázási modell

A felhasználási terv részletes leírását lásd a számlázási a [árképzést ismertető oldalra Azure Functions]. Használati függvény alkalmazásszinten összesített értéket, és csak arra az időre funkciókódot végrehajtott számát. Számlázási egységei a következők: 
* **Erőforrás-felhasználás (GB-s) GB-másodperc**. Számított, a memóriaméret és a végrehajtási idő összes funkciójának függvény alkalmazásokban. 
* **Végrehajtások**. Minden alkalommal, amikor egy függvény végrehajtása eseményindító válaszul számítanak.

[árképzést ismertető oldalra Azure Functions]: https://azure.microsoft.com/pricing/details/functions
