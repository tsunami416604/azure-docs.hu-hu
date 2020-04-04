---
title: Az Azure Functions méretezése és üzemeltetése
description: Ismerje meg, hogyan választhat az Azure Functions fogyasztási csomag és a prémium csomag között.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ac0417e9d8adca168dd68e1721a1c9c890de1c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656934"
---
# <a name="azure-functions-scale-and-hosting"></a>Az Azure Functions méretezése és üzemeltetése

Amikor létrehoz egy függvényalkalmazást az Azure-ban, ki kell választania egy üzemeltetési csomagot az alkalmazáshoz. Az Azure Functions hez három tárhely érhető el: [fogyasztási díjcsomag](#consumption-plan), [prémium csomag](#premium-plan)és [dedikált (App Service) csomag.](#app-service-plan)

A választott tárhelycsomag a következő viselkedéseket diktálja:

* A függvényalkalmazás méretezése.
* Az egyes függvényalkalmazás-példányok számára elérhető erőforrások.
* Speciális funkciók, például az Azure virtuális hálózati kapcsolat támogatása.

Mind a felhasználás, mind a prémium szintű csomagok automatikusan növelik a számítási teljesítményt, amikor a kód fut. Az alkalmazás horizontálisan kivan méretezve, ha szükséges a terhelés kezeléséhez, és a kód futásának leállításakor méretezve. A felhasználási csomag esetében nem kell előre fizetnie az alapjárati virtuális gépekért vagy a tartalékkapacitásért.  

Prémium csomag további funkciókat biztosít, például a prémium szintű számítási példányok, a példányok a végtelenségig melegen tartása, és a virtuális hálózat kapcsolat.

Az App Service-csomag lehetővé teszi, hogy kihasználja a dedikált infrastruktúra, amely kezeli. A függvényalkalmazás nem méretezhető események alapján, ami azt jelenti, hogy soha nem skálázza nullára. (A [Mindig bekapcsolva](#always-on) funkció engedélyezve van.)

## <a name="hosting-plan-support"></a>A tárhelycsomag támogatása

A szolgáltatástámogatás a következő két kategóriába sorolható:

* _Általánosan elérhető (GA):_ teljes mértékben támogatott és jóváhagyott termelési felhasználásra.
* _Előzetes_verzió: még nem teljesen támogatott, és nem engedélyezett éles használatra.

Az alábbi táblázat a három tárhely támogatásának jelenlegi szintjét mutatja, ha Windows vagy Linux rendszeren fut:

| | Használatalapú csomag | Prémium szintű csomag | Dedikált terv |
|-|:----------------:|:------------:|:----------------:|
| Windows | FE | FE | FE |
| Linux | FE | FE | FE |

## <a name="consumption-plan"></a>Használatalapú csomag

A felhasználási csomag használatakor az Azure Functions gazdagép példányai dinamikusan kerülnek hozzáadásra és eltávolításra a bejövő események száma alapján. Ezek a kiszolgáló nélküli csomagok automatikusan méreteznek, és csak akkor kell fizetni a számítási erőforrásokért, amikor a függvények futnak. Használatalapú csomag esetében egy függvény végrehajtása túllépi a konfigurált időkorlátot.

A számlázás a végrehajtások száma, a végrehajtási idő és a felhasznált memória alapján történik. A számlázási folyamat a függvényalkalmazás összes függvényét figyelembe veszi. További információt az [Azure Functions díjszabási oldalán](https://azure.microsoft.com/pricing/details/functions/)talál.

A fogyasztási terv az alapértelmezett tárhelycsomag, és a következő előnyöket kínálja:

* Csak akkor fizessen, ha a funkciók futnak
* Kicsierőre skálázható, még nagy terhelés alatt is

Az ugyanabban a régióban lévő függvényalkalmazások ugyanahhoz a felhasználási tervhez rendelhetők hozzá. Nincs hátránya vagy hatása, hogy több alkalmazás fut ugyanabban a felhasználási tervben. Ha több alkalmazást rendel ugyanahhoz a felhasználási tervhez, az nincs hatással az egyes alkalmazások rugalmasságára, méretezhetőségére vagy megbízhatóságára.

Ha többet szeretne tudni arról, hogy miként becsülhető meg a költségek et a Felhasználási tervben, olvassa [el a Felhasználási terv költségeinek ismertetése című témakört.](functions-consumption-costs.md)

## <a name="premium-plan"></a><a name="premium-plan"></a>Prémium szintű csomag

A prémium csomag használatakor az Azure Functions gazdagép példányai hozzáadódnak és törlődnek a bejövő események száma alapján, hasonlóan a felhasználási tervhez.  A prémium csomag a következő funkciókat támogatja:

* Állandóan meleg példányok a hidegindítás elkerülése érdekében
* Virtuális hálózat-kapcsolat
* Korlátlan végrehajtás időtartama (60 perc garantált)
* Prémium példányméretek (egy mag, két mag és négy fő példány)
* Kiszámíthatóbb árképzés
* Nagy sűrűségű alkalmazásallokáció több funkciós alkalmazással rendelkező csomagokhoz

A beállítások konfigurálásával kapcsolatos információk az [Azure Functions Premium csomag dokumentumában](functions-premium-plan.md)találhatók.

Ahelyett, hogy a számlázási végrehajtás és a felhasznált memória, a prémium csomag számlázása a szükséges és az előmelegített példányok között használt alapvető másodpercek és memória alapján történik. Tervenként legalább egy példánynak melegnek kell lennie. Ez azt jelenti, hogy a végrehajtások számától függetlenül aktív csomagonként i minimális havi költség van. Ne feledje, hogy a Prémium csomag összes funkcióalkalmazása előre felmelegített és aktív példányokat oszt meg.

Fontolja meg az Azure Functions Premium csomagot a következő helyzetekben:

* A funkciós alkalmazások folyamatosan vagy szinte folyamatosan futnak.
* Van egy nagy számú kis végrehajtások és magas végrehajtási számlát, de alacsony GB második számlát a felhasználási tervben.
* Több PROCESSZOR- vagy memóriabeállításokra van szükség, mint a felhasználási tervben.
* A kódnak hosszabb ideig kell futnia, mint a felhasználási tervben [engedélyezett maximális végrehajtási idő.](#timeout)
* Olyan funkciókra van szüksége, amelyek csak prémium szintű csomagon érhetők el, például a virtuális hálózati kapcsolaton.

JavaScript-függvények prémium csomagon futtatásakor olyan példányt kell választania, amely kevesebb vCPU-val rendelkezik. További információt az [Egymagos prémiumcsomagok kiválasztása](functions-reference-node.md#considerations-for-javascript-functions)című témakörben talál.  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Dedikált (App Service) csomag

A függvényalkalmazások is futtathatók ugyanazon a dedikált virtuális gépeken, mint más App Service-alkalmazások (Alapszintű, Standard, Prémium és Elkülönített Termékkészlet).

Fontolja meg egy App Service-csomag a következő helyzetekben:

* Meglévő, kihasználatlan virtuális gépekkel rendelkezik, amelyek már futnak más App Service-példányok.
* Egyéni lemezképet szeretne biztosítani, amelyen a függvényeket futtatni szeretné.

Ugyanazt a függvényalkalmazásokat kell fizetnie egy App Service-csomagban, mint más App Service-erőforrásokért, például a webalkalmazásokért. Az App Service-csomag működéséről az [Azure App Service-csomagok részletes áttekintésében olvashat részletesen.](../app-service/overview-hosting-plans.md)

Egy App Service-csomag, manuálisan horizontális felskálázás további virtuálisgép-példányok hozzáadásával. Az automatikus skálázást is engedélyezheti. További információt a [Példányszám manuális vagy automatikus méretezése című témakörben talál.](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) A felskálázást egy másik App Service-csomag kiválasztásával is felskálázhatja. További információt az [Alkalmazás felskálázása az Azure-ban című témakörben talál.](../app-service/manage-scale-up.md) 

JavaScript-függvények futtatásakor egy App Service-csomag, válasszon egy csomagot, amely kevesebb vCPU-k. További információ: [Egymagos App Service-csomagok kiválasztása.](functions-reference-node.md#choose-single-vcpu-app-service-plans) 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Mindig bekapcsolva

Ha egy App Service-csomagon futtatja, engedélyeznie kell a **Mindig a** beállítást, hogy a függvényalkalmazás megfelelően fusson. Egy App Service-csomag, a függvények futásidejű megy tétlen néhány perc inaktivitás után, így csak a HTTP-eseményindítók "felébreszti" a funkciókat. Mindig be van kapcsolva csak egy App Service-csomag. A felhasználási csomag esetén a platform automatikusan aktiválja a függvényalkalmazásokat.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Még ha mindig be van kapcsolva is, az `functionTimeout` egyes függvények végrehajtási időmeghosszabbítását a [host.json](functions-host-json.md#functiontimeout) projektfájlban lévő beállítás szabályozza.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Meglévő alkalmazás üzemeltetési tervének meghatározása

A függvényalkalmazás által használt üzemeltetési csomag meghatározásához tekintse meg az **App Service-csomag** az [Azure Portalon](https://portal.azure.com)a függvényalkalmazás **Áttekintés** lapján című témakört. A tarifacsomag megtekintéséhez válassza ki az **App Service-csomag**nevét, majd válassza a **Tulajdonságok lehetőséget** a bal oldali ablaktáblában.

![Méretezési terv megtekintése a portálon](./media/functions-scale/function-app-overview-portal.png)

Az Azure CLI segítségével is meghatározhatja a csomagot, az alábbiak szerint:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Ha a kimenet e `dynamic`parancs, a függvényalkalmazás a felhasználási tervben van. Ha a kimenet e `ElasticPremium`parancs, a függvényalkalmazás a Prémium csomag. Minden más érték az App Service-csomag különböző szintjeit jelzi.

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Minden csomag, egy függvényalkalmazás igényel egy általános Azure Storage-fiók, amely támogatja az Azure Blob, Várólista, Fájlok és Table storage. Ennek az az oka, hogy az Azure Functions az Azure Storage-ra támaszkodik olyan műveletekhez, mint az eseményindítók kezelése és a naplózási függvények végrehajtása, de egyes tárfiókok nem támogatják a várólistákat és a táblákat. Ezek a fiókok, amelyek magukban foglalják a blob-csak tárfiókok (beleértve a prémium szintű storage) és az általános célú storage-fiókok zónaredundáns storage replikáció, szűrjük ki a meglévő **tárfiók** választás, amikor létrehoz egy függvényalkalmazást.

A függvényalkalmazás által használt tárfiók az eseményindítók és kötések is használhatják az alkalmazásadatok tárolására. Azonban a nagy tárban végzett műveletekhez külön tárfiókot kell használnia.  

Minden bizonnyal lehetséges, hogy több funkciós alkalmazás is megoszthassa ugyanazt a tárfiókot problémák nélkül. (Egy jó példa erre, ha több alkalmazást fejleszt a helyi környezetben az Azure Storage-emulátor használatával, amely úgy viselkedik, mint egy tárfiók.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

A tárfiók-típusokról az [Azure Storage-szolgáltatások bemutatása](../storage/common/storage-introduction.md#azure-storage-services)című témakörben olvashat bővebben.

## <a name="how-the-consumption-and-premium-plans-work"></a>A fogyasztási és prémium csomagok működésének megmunkálata

A felhasználás és a prémium szintű csomagok, az Azure Functions infrastruktúra méretezi a CPU és a memória erőforrásait a Functions gazdagép további példányainak hozzáadásával, a függvények által kiváltott események száma alapján. A Felhasználás csomag Ban a Functions gazdagép minden példánya 1,5 GB memóriára és egy PROCESSZORra korlátozódik.  A gazdagép egy példánya a teljes függvényalkalmazás, ami azt jelenti, hogy egy függvényalkalmazáson belüli összes függvény egy példányon belül megosztja az erőforrást, és egyszerre méretezhető. Az azonos használati csomaggal megegyező funkcióalkalmazások méretezése egymástól függetlenül történik.  A prémium csomag mérete határozza meg a rendelkezésre álló memória és a CPU az adott példányösszes alkalmazás az adott példányban.  

A függvénykódfájlok az Azure Files-megosztásokon tárolódnak a függvény fő tárfiókjában. A függvényalkalmazás fő tárfiókjának törlésekor a függvénykódfájlok törlődnek, és nem állíthatók helyre.

### <a name="runtime-scaling"></a>Futásidejű méretezés

Az Azure Functions egy *skálázási vezérlő* nevű összetevőt használ az események sebességének figyeléséhez, és annak meghatározásához, hogy horizontális felskálázásvagy méretezés. A méretezési vezérlő heurisztikát használ minden eseményindító típushoz. Ha például egy Azure Queue storage-eseményindítót használ, az a várólista hossza és a legrégebbi várólista-üzenet kora alapján méretezhető.

Az Azure Functions méretezési egysége a függvényalkalmazás. A függvényalkalmazás horizontális felskálázása kor további erőforrások kerülnek kiosztásra az Azure Functions gazdagép több példányának futtatásához. Ezzel szemben a számítási igény csökkentése, a méretezési vezérlő eltávolítja a függvénygazda példányok. A példányok száma végül nullára *van méretezve,* ha egy függvényalkalmazáson belül nem futnak függvények.

![Vezérlőfigyelési események méretezése és példányok létrehozása](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>A méretezési viselkedés ismertetése

A skálázás számos tényezőtől függően változhat, és az eseményindító és a kiválasztott nyelv alapján eltérő méretezést tehet lehet. Van néhány bonyolult skálázás imázsa, hogy tudatában legyünk:

* Egy függvényalkalmazás csak legfeljebb 200 példányra skálázódik. Egy példány azonban egyszerre több üzenetet vagy kérést is feldolgozhat, így nincs meghatározott korlát az egyidejű végrehajtások számára.
* A HTTP-eseményindítók esetében az új példányok legutolsó másodpercenként egyszer kerülnek kiosztásra.
* A nem HTTP-s eseményindítók esetében az új példányok legbénák, legbelül 30 másodpercenként egyszer kerülnek kiosztásra. A méretezés gyorsabb, ha [prémium szintű csomagban](#premium-plan)fut.
* A Service Bus-eseményindítók esetén használja a leghatékonyabb méretezéshez használja az erőforrásokhoz szükséges jogok _kezelése._ _A listen_ jogosultságok méretezése nem olyan pontos, mert a várólista hossza nem használható a skálázási döntések tájékoztatására. Ha többet szeretne tudni a Service Bus hozzáférési házirendjeiben a jogok beállításáról, olvassa el a [Megosztott hozzáférés engedélyezési házirendje című témakört.](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)
* Az Event Hub-eseményindítók, tekintse meg a [méretezési útmutató](functions-bindings-event-hubs-trigger.md#scaling) a referenciacikkben. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Gyakorlati tanácsok és minták méretezhető alkalmazásokhoz

A függvényalkalmazás számos szempontból, amely hatással lesz, hogy milyen jól fog méretezni, beleértve a gazdagép konfigurációja, futásidejű lábnyom, és az erőforrás-hatékonyság.  További információt a [teljesítményre vonatkozó szempontokról szóló cikk méretezhetőségi szakaszában talál.](functions-best-practices.md#scalability-best-practices) Azt is tisztában kell lennie azzal, hogy a kapcsolatok hogyan viselkednek, ahogy a függvényalkalmazás méretezi. További információt a Kapcsolatok kezelése az Azure Functionsben című [témakörben talál.](manage-connections.md)

A Python és a Node.js méretezésével kapcsolatos további információkért tekintse meg az [Azure Functions Python fejlesztői útmutatóját - Méretezés és egyidejűség](functions-reference-python.md#scaling-and-concurrency) és [az Azure Functions Node.js fejlesztői útmutató – Méretezés és egyidejűség](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Számlázási modell

A különböző csomagok számlázását részletesen ismertetjük az [Azure Functions díjszabási lapján.](https://azure.microsoft.com/pricing/details/functions/) A használat a függvényalkalmazás szintjén összesítve történik, és csak a függvénykód végrehajtásának idejét számolja. A számlázási egységek a következők:

* **Erőforrás-felhasználás gigabájtmásodpercben (GB-s).** A függvényalkalmazáson belüli összes függvény memóriaméretének és végrehajtási idejének kombinációjaként számítva. 
* **Kivégzések**. Minden alkalommal megkell számolni, amikor egy függvényt egy eseményeseményindítóra válaszul hajt végre.

Hasznos lekérdezések és információk a fogyasztási számla megértéséhez [a számlázási GYIK-ben](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)találhatók.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Szolgáltatási korlátozások

Az alábbi táblázat azokat a korlátokat mutatja be, amelyek a függvényalkalmazásokra vonatkoznak a különböző üzemeltetési csomagokban való futtatáskor:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
