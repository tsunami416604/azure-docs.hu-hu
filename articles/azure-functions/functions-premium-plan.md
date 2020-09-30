---
title: Prémium csomag Azure Functions
description: Részletek és konfigurációs beállítások (VNet, nem hideg indítás, korlátlan végrehajtási időtartam) a Azure Functions Premium csomaghoz.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
ms.openlocfilehash: a037c903a72ba79b79c7e6b011fe025aefd7b51d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578036"
---
# <a name="azure-functions-premium-plan"></a>Prémium csomag Azure Functions

A Azure Functions Prémium csomag (más néven a rugalmas Prémium csomag) egy üzemeltetési lehetőség a Function apps számára. A Prémium csomag olyan szolgáltatásokat nyújt, mint például a VNet-kapcsolat, a hidegindító és a prémium szintű hardverek.  Több Function apps is telepíthető ugyanarra a prémium csomagra, és a csomag lehetővé teszi a számítási példány méretének, az alapcsomag méretének és a maximális méretnek a konfigurálását.  A Prémium csomag és az egyéb csomag-és üzemeltetési típusok összehasonlítását lásd: a [függvények méretezési és üzemeltetési lehetőségei](functions-scale.md).

## <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Prémium szintű csomagot az az [functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) paranccsal is létrehozhat az Azure CLI-ben. Az alábbi példa egy _rugalmas prémium 1_ szintű csomagot hoz létre:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Ebben a példában cserélje le az `<RESOURCE_GROUP>` erőforráscsoportot és az erőforráscsoport `<PLAN_NAME>` egyedi csomagjának nevére. Válasszon egy [támogatottat `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). A Linuxot támogató Prémium csomag létrehozásához adja meg a `--is-linux` lehetőséget.

A terv létrehozásakor az [az functionapp Create](/cli/azure/functionapp#az-functionapp-create) paranccsal hozhatja létre a Function alkalmazást. A portálon a csomag és az alkalmazás is egyszerre jön létre. A teljes Azure CLI-szkriptre vonatkozó példát a [Function app létrehozása prémium](scripts/functions-cli-create-premium-plan.md)szintű csomagban talál.

## <a name="features"></a>Funkciók

A következő szolgáltatások használhatók a prémium csomagba telepített alkalmazások működéséhez.

### <a name="always-ready-instances"></a>Mindig kész példányok

Ha a használati tervben jelenleg nem fordulnak elő események és végrehajtások, az alkalmazás a nulla példányra is méretezhető. Új események beolvasásakor egy új példányra van szükség a rajta futó alkalmazáshoz.  Az új példányok specializálása az alkalmazástól függően hosszabb időt is igénybe vehet.  Ezt a további késleltetést az első hívásnál gyakran az App Cold Start metódusnak nevezik.

A prémium csomaggal az alkalmazás mindig egy adott számú példányon áll készen.  A mindig kész példányok maximális száma 20.  Amikor az események elkezdik elindítani az alkalmazást, először a mindig kész példányokra irányítják őket.  Ahogy a függvény aktívvá válik, a további példányok pufferként lesznek bemelegítve.  Ez a puffer megakadályozza, hogy a méretezés során új példányok ne legyenek hidegen.  Ezeket a pufferelt példányokat [előre bemelegítő példányoknak](#pre-warmed-instances)nevezzük.  Az Always Ready-példányok és az előre bemelegítő puffer együttes használatával az alkalmazás hatékonyan kiszűrheti a hidegindító-használatot.

> [!NOTE]
> Minden prémium csomagnak legalább egy aktív (számlázott) példánya lesz.

A Azure Portal mindig kész példányok számát beállíthatja úgy, hogy kijelöli a **függvényalkalmazás**, majd a **platform szolgáltatásai** lapra kattint, majd kiválasztja a **kiskálázási** lehetőségeket. Az alkalmazás szerkesztése ablakban a mindig kész példányok az adott alkalmazásra vonatkoznak.

![Rugalmas méretezési beállítások](./media/functions-premium-plan/scale-out.png)

Az Azure CLI-vel is konfigurálhatja az alkalmazások mindig kész példányait.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites 
```

#### <a name="pre-warmed-instances"></a>Előre bemelegítő példányok

Az előre bemelegítő példányok a méretezési és aktiválási események során pufferként beérkező példányok száma.  Az előre bemelegített példányok továbbra is a pufferig maradnak, amíg el nem éri a maximális kibővíthető korlátot.  Az alapértelmezett előre bemelegítő példányok száma 1, a legtöbb esetben pedig 1.  Ha egy alkalmazás hosszú bemelegítőt használ (például egy egyéni tároló képét), akkor érdemes lehet ezt a puffert emelni.  Egy előre bemelegített példány csak akkor válik aktívvá, ha az összes aktív példány megfelelően ki lett használva.

Gondolja át ezt a példát arra, hogy a kész példányok és az előre bemelegítő példányok hogyan működnek együtt.  A Premium Function alkalmazáshoz öt mindig kész példány van konfigurálva, és az alapértelmezett egy előre bemelegítő példány.  Ha az alkalmazás üresjáratban van, és nincs aktiválva esemény, az alkalmazás üzembe helyezése és futtatása öt példányban történik.  Jelenleg nem számítunk fel olyan előre bemelegítő példányt, amelyben a mindig kész példányok nincsenek használatban, és az előre felmelegedett példányok még nem lesznek lefoglalva.

Amint az első trigger bekövetkezik, az öt mindig kész példány válik aktívvá, és egy előre bemelegítő példány van lefoglalva.  Az alkalmazás már hat kiosztott példányon fut: az öt most aktív, mindig kész példány, valamint a hatodik előre bemelegítő és inaktív puffer.  Ha a végrehajtások aránya továbbra is növekszik, az öt aktív példány végül fel lesz használva.  Amikor a platform úgy dönt, hogy öt példányon túli méretezést tesz elérhetővé, az előre bemelegített példányra lesz méretezve.  Ebben az esetben hat aktív példány lesz, és a rendszer azonnal kiépíti a hetedik példányt, és kitölti az előre bemelegítő puffert.  Ez a skálázási és előmelegítési folyamat folytatódni fog, amíg el nem éri az alkalmazáshoz tartozó példányok maximális számát.  Egyetlen példány sem lesz előre bemelegítve vagy aktiválva a maximumon túl.

Az Azure CLI használatával módosíthatja az alkalmazások előre bemelegítő példányainak számát.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites 
```

#### <a name="maximum-instances-for-an-app"></a>Alkalmazások maximális példányai

A [Példányszám maximális száma](#plan-and-sku-settings)mellett az alkalmazások maximális számát is beállíthatja.  Az alkalmazás maximálisan konfigurálható az [alkalmazás méretezési korlátja](./functions-scale.md#limit-scale-out)alapján.

### <a name="private-network-connectivity"></a>Magánhálózati kapcsolat

A prémium csomagra telepített Azure Functions a [webalkalmazások új VNet-integrációját](../app-service/web-sites-integrate-with-vnet.md)is kihasználja.  Ha be van állítva, az alkalmazás képes kommunikálni a VNet lévő erőforrásokkal, vagy szolgáltatási végpontokon keresztül.  Az alkalmazásban az IP-korlátozások is elérhetők a bejövő forgalom korlátozására.

Amikor prémium szintű csomagban rendel hozzá egy alhálózatot a Function alkalmazáshoz, szüksége van egy alhálózatra, amely minden lehetséges példányhoz elegendő IP-címmel rendelkezik. Legalább 100 rendelkezésre álló címmel rendelkező IP-blokkra van szükség.

További információ: [a Function app integrálása VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Gyors rugalmas skála

Az alkalmazáshoz további számítási példányok is automatikusan hozzáadódnak a használati tervvel megegyező gyors skálázási logikával. Az azonos App Service csomagbeli alkalmazások egymástól függetlenül méretezhetők, egy adott alkalmazás igényeitől függően. Ugyanakkor a functions-alkalmazások ugyanabban a App Service csomagban megosztják a virtuális gépek erőforrásait a költségek csökkentése érdekében, ha lehetséges. A virtuális géphez társított alkalmazások száma az egyes alkalmazások lábnyomával és a virtuális gép méretétől függ.

Ha többet szeretne megtudni a skálázás működéséről, tekintse meg a [függvények méretezése és üzemeltetése](./functions-scale.md#how-the-consumption-and-premium-plans-work)című témakört.

### <a name="longer-run-duration"></a>Hosszabb Futtatás időtartama

A Azure Functions a használati terv egyetlen végrehajtás esetén 10 percre van korlátozva.  A Prémium csomag esetében a futtatási időtartam alapértelmezett értéke 30 perc, hogy megakadályozza a Runaway végrehajtást. Azonban [módosíthatja a host.jsa konfigurációban](./functions-host-json.md#functiontimeout) , hogy a prémium szintű csomag alkalmazásaihoz (garantált 60 perc) az időtartam fel legyen kötve.

## <a name="plan-and-sku-settings"></a>Csomag-és SKU-beállítások

A terv létrehozásakor kétféle séma-beállítás létezik: a példányok minimális száma (vagy a csomag mérete) és a maximális burst korlát.

Ha az alkalmazás a mindig kész példányokon túli példányokat igényel, akkor továbbra is kibővíthető, amíg a példányok száma eléri a maximális burst korlátot.  A csomagon kívüli példányok számlázása csak akkor történik meg, ha az Ön által futtatott, illetve az Ön számára lefoglalt, másodpercenként.  A legjobb megoldás, ha az alkalmazást a megadott maximális korlátra szeretné méretezni.

A csomag méretének és Azure Portal maximális értékének konfigurálásához válassza ki a csomag **kibővítő** lehetőségeit vagy az adott tervhez üzembe helyezett Function alkalmazást (a **platform szolgáltatásai**alatt).

Az Azure CLI maximális burst korlátját is megnövelheti:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

Minden csomag minimuma legalább egy példány lesz.  A példányok tényleges minimális száma automatikusan konfigurálva lesz az Ön számára a csomagban lévő alkalmazások által kért mindig kész példányok alapján.  Például ha az alkalmazás egy öt mindig kész példányt kér, és a B alkalmazás két mindig kész példányt kér ugyanabban a csomagban, a minimális terv mérete öt lesz.  Az A alkalmazás mind az 5-öt futtatja, a B alkalmazás pedig csak 2 lesz.

> [!IMPORTANT]
> A rendszer minden egyes, a példányok minimális száma alatt lefoglalt példány díját terheli, függetlenül attól, hogy a függvények végrehajtása történik-e.

A legtöbb esetben ez az autoszámított minimális értéknek elegendőnek kell lennie.  A minimálisan szükséges skálázás azonban a lehető leghatékonyabban történik.  Ez azonban nem valószínű, hogy egy adott időkorláton késleltethető, ha további példányok nem érhetők el.  Ha a minimumnál magasabb értéket állít be, akkor a Kibővítés előtt le kell foglalni a példányokat.

Az Azure CLI használatával megnövelhető egy csomag kiszámított minimuma.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set sku.capacity=<desired_min_instances> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Rendelkezésre álló példányok SKU-ban

A csomag létrehozásakor vagy skálázásakor három példány mérete közül választhat.  Az egyes példányok kiosztása után a rendszer a másodpercenként kiosztott magok és memóriák teljes számát számlázza.  Az alkalmazás igény szerint automatikusan több példányra is kibővíthető.  

|Termékváltozat|Cores|Memória|Storage|
|--|--|--|--|
|EP1|1|3.5 GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14 GB|250GB|

### <a name="memory-utilization-considerations"></a>Memóriahasználat szempontjai
A több memóriával rendelkező gépeken való futtatás nem mindig jelenti azt, hogy a Function alkalmazás az összes rendelkezésre álló memóriát fogja használni.

A JavaScript-függvények alkalmazásait például a Node.js alapértelmezett memória-korlátja korlátozza. A rögzített memória korlátjának növeléséhez adja hozzá az alkalmazás `languageWorkers:node:arguments` értékét a következő értékkel: `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Régió maximális felskálázása

Az alábbiakban láthatók az egyes régiók és operációs rendszerek konfigurációjának egyetlen csomagjának jelenleg támogatott maximális Felskálázási értékei. A növekedés igényléséhez nyisson meg egy támogatási jegyet.

Tekintse meg a függvények teljes regionális elérhetőségét itt: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Ausztrália középső régiója| 100 | Nem érhető el |
|Ausztrália 2. középső régiója| 100 | Nem érhető el |
|Kelet-Ausztrália| 100 | 20 |
|Délkelet-Ausztrália | 100 | 20 |
|Dél-Brazília| 100 | 20 |
|Közép-Kanada| 100 | 20 |
|Az USA középső régiója| 100 | 20 |
|Kelet-Kína 2| 100 | 20 |
|Észak-Kína 2| 100 | 20 |
|Kelet-Ázsia| 100 | 20 |
|USA keleti régiója | 100 | 20 |
|USA 2. keleti régiója| 100 | 20 |
|Közép-Franciaország| 100 | 20 |
|Középnyugat-Németország| 100 | Nem érhető el |
|Kelet-Japán| 100 | 20 |
|Nyugat-Japán| 100 | 20 |
|Dél-Korea középső régiója| 100 | 20 |
|Dél-Korea déli régiója| Nem érhető el | 20 |
|USA északi középső régiója| 100 | 20 |
|Észak-Európa| 100 | 20 |
|Kelet-Norvégia| 100 | 20 |
|USA déli középső régiója| 100 | 20 |
|Dél-India | 100 | Nem érhető el |
|Délkelet-Ázsia| 100 | 20 |
|Észak-Svájc| 100 | Nem érhető el |
|Nyugat-Svájc| 100 | Nem érhető el |
|Az Egyesült Királyság déli régiója| 100 | 20 |
|Az Egyesült Királyság nyugati régiója| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat keleti régiója| 100 | Nem érhető el |
|USNat nyugati régiója| 100 | Nem érhető el |
|Nyugat-Európa| 100 | 20 |
|Nyugat-India| 100 | 20 |
|USA nyugati középső régiója| 100 | 20 |
|USA nyugati régiója| 100 | 20 |
|USA 2. nyugati régiója| 100 | 20 |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Azure Functions méretezési és üzemeltetési lehetőségeinek megismerése](functions-scale.md)
