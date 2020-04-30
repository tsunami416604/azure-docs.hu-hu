---
title: Prémium csomag Azure Functions
description: Részletek és konfigurációs beállítások (VNet, nem hideg indítás, korlátlan végrehajtási időtartam) a Azure Functions Premium csomaghoz.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276906"
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

Ebben a példában cserélje le `<RESOURCE_GROUP>` az erőforráscsoportot és `<PLAN_NAME>` az erőforráscsoport egyedi csomagjának nevére. Válasszon egy [támogatottat `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). A Linuxot támogató Prémium csomag létrehozásához adja meg a `--is-linux` lehetőséget.

A terv létrehozásakor az [az functionapp Create](/cli/azure/functionapp#az-functionapp-create) paranccsal hozhatja létre a Function alkalmazást. A portálon a csomag és az alkalmazás is egyszerre jön létre. A teljes Azure CLI-szkriptre vonatkozó példát a [Function app létrehozása prémium](scripts/functions-cli-create-premium-plan.md)szintű csomagban talál.

## <a name="features"></a>Szolgáltatások

A következő szolgáltatások használhatók a prémium csomagba telepített alkalmazások működéséhez.

### <a name="pre-warmed-instances"></a>Előre bemelegítő példányok

Ha a használati tervben jelenleg nem fordulnak elő események és végrehajtások, az alkalmazás a nulla példányra is méretezhető. Új események beolvasásakor egy új példányra van szükség a rajta futó alkalmazáshoz.  Az új példányok specializálása az alkalmazástól függően hosszabb időt is igénybe vehet.  Ezt a további késleltetést az első hívásnál gyakran az App Cold Start metódusnak nevezik.

A prémium csomaggal előre bemelegítheti az alkalmazást egy adott számú példányra, a minimálisan szükséges méretig.  Az előre bemelegített példányok lehetővé teszik az alkalmazások előre méretezését a nagy terhelés előtt. Az alkalmazás kiskálázásakor először az előre bemelegítő példányokra méretezi a folyamatokat. A további példányok továbbra is kiállnak a pufferbe, és azonnal melegen vesznek részt a következő skálázási művelet előkészítésében. Az előre bemelegített példányok pufferével hatékonyan elkerülheti a hideg indítási késéseket.  Az előre bemelegített példányok a Prémium csomag egyik funkciója, és legalább egy példányon futnia kell, és a csomag aktív állapotban kell lennie.

A Azure Portal előre bemelegítő példányok számát úgy is beállíthatja, hogy kiválasztja a **függvényalkalmazás**, majd a **platform szolgáltatásai** lapra kattint, és kiválasztja a **kibővíthető** lehetőségeket. Az alkalmazás szerkesztése ablakban az előre felmelegedett példányok az adott alkalmazásra vonatkoznak, de a minimális és a maximális példányszám a teljes tervre vonatkozik.

![Rugalmas méretezési beállítások](./media/functions-premium-plan/scale-out.png)

Az Azure CLI-vel is konfigurálhat előre bemelegített példányokat az alkalmazáshoz.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Magánhálózati kapcsolat

A prémium csomagra telepített Azure Functions a [webalkalmazások új VNet-integrációját](../app-service/web-sites-integrate-with-vnet.md)is kihasználja.  Ha be van állítva, az alkalmazás képes kommunikálni a VNet lévő erőforrásokkal, vagy szolgáltatási végpontokon keresztül.  Az alkalmazásban az IP-korlátozások is elérhetők a bejövő forgalom korlátozására.

Amikor prémium szintű csomagban rendel hozzá egy alhálózatot a Function alkalmazáshoz, szüksége van egy alhálózatra, amely minden lehetséges példányhoz elegendő IP-címmel rendelkezik. Legalább 100 rendelkezésre álló címmel rendelkező IP-blokkra van szükség.

További információ: [a Function app integrálása VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Gyors rugalmas skála

Az alkalmazáshoz további számítási példányok is automatikusan hozzáadódnak a használati tervvel megegyező gyors skálázási logikával.  Ha többet szeretne megtudni a skálázás működéséről, tekintse meg a [függvények méretezése és üzemeltetése](./functions-scale.md#how-the-consumption-and-premium-plans-work)című témakört.

### <a name="longer-run-duration"></a>Hosszabb Futtatás időtartama

A Azure Functions a használati terv egyetlen végrehajtás esetén 10 percre van korlátozva.  A Prémium csomag esetében a futtatási időtartam alapértelmezett értéke 30 perc, hogy megakadályozza a Runaway végrehajtást. Azonban [módosíthatja a Host. JSON-konfigurációt](./functions-host-json.md#functiontimeout) , hogy ez a prémium szintű csomag alkalmazásai számára is elérhető legyen (garantált 60 perc).

## <a name="plan-and-sku-settings"></a>Csomag-és SKU-beállítások

A terv létrehozásakor két beállítást kell beállítania: a példányok minimális száma (vagy a csomag mérete) és a maximális burst korlát.  A minimális példányok le vannak foglalva, és mindig futnak.

> [!IMPORTANT]
> A rendszer minden egyes, a példányok minimális száma alatt lefoglalt példány díját terheli, függetlenül attól, hogy a függvények végrehajtása történik-e.

Ha az alkalmazás a csomag méretétől meghaladó példányokat igényel, akkor továbbra is kibővíthető, amíg a példányok száma eléri a maximális burst korlátot.  A csomagon kívüli példányok díját csak akkor számítjuk fel, ha a rendszert futtatják és bérbe adják.  A legjobb megoldás az, ha az alkalmazást a meghatározott maximális korlátig méretezni, míg a minimálisan szükséges csomag példányai garantáltak az alkalmazás számára.

A csomag méretének és Azure Portal maximális értékének konfigurálásához válassza ki a csomag **kibővítő** lehetőségeit vagy az adott tervhez üzembe helyezett Function alkalmazást (a **platform szolgáltatásai**alatt).

Az Azure CLI maximális burst korlátját is megnövelheti:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Rendelkezésre álló példányok SKU-ban

A csomag létrehozásakor vagy skálázásakor három példány mérete közül választhat.  A rendszer a másodpercenként felhasznált magok és memória teljes számát számlázza.  Az alkalmazás igény szerint automatikusan több példányra is kibővíthető.  

|SKU|Cores|Memory (Memória)|Storage|
|--|--|--|--|
|EP1|1|3.5 GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14 GB|250GB|

### <a name="memory-utilization-considerations"></a>Memóriahasználat szempontjai
A több memóriával rendelkező gépeken való futtatás nem mindig jelenti azt, hogy a Function alkalmazás az összes rendelkezésre álló memóriát fogja használni.

A JavaScript-függvények alkalmazásait például korlátozza a Node. js alapértelmezett memória-korlátja. A rögzített memória korlátjának növeléséhez adja hozzá az alkalmazás `languageWorkers:node:arguments` értékét a következő értékkel: `--max-old-space-size=<max memory in MB>`.

## <a name="region-max-scale-out"></a>Régió maximális felskálázása

Az alábbiakban láthatók az egyes régiók és operációs rendszerek konfigurációjának egyetlen csomagjának jelenleg támogatott maximális méretezési értékei. Ha növekményt szeretne kérni, nyisson meg egy támogatási jegyet.

Tekintse meg a függvények teljes regionális elérhetőségét itt: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Régió| Windows | Linux |
|--| -- | -- |
|Ausztrália középső régiója| 20 | Nem érhető el |
|Ausztrália 2. középső régiója| 20 | Nem érhető el |
|Kelet-Ausztrália| 100 | 20 |
|Délkelet-Ausztrália | 100 | 20 |
|Dél-Brazília| 60 | 20 |
|Közép-Kanada| 100 | 20 |
|USA középső régiója| 100 | 20 |
|Kelet-Ázsia| 100 | 20 |
|USA keleti régiója | 100 | 20 |
|USA 2. keleti régiója| 100 | 20 |
|Közép-Franciaország| 100 | 20 |
|Középnyugat-Németország| 100 | Nem érhető el |
|Kelet-Japán| 100 | 20 |
|Nyugat-Japán| 100 | 20 |
|Dél-Korea középső régiója| 100 | 20 |
|USA északi középső régiója| 100 | 20 |
|Észak-Európa| 100 | 20 |
|Kelet-Norvégia| 20 | 20 |
|USA déli középső régiója| 100 | 20 |
|Dél-India | 100 | Nem érhető el |
|Délkelet-Ázsia| 100 | 20 |
|Az Egyesült Királyság déli régiója| 100 | 20 |
|Az Egyesült Királyság nyugati régiója| 100 | 20 |
|Nyugat-Európa| 100 | 20 |
|Nyugat-India| 100 | 20 |
|USA nyugati középső régiója| 20 | 20 |
|USA nyugati régiója| 100 | 20 |
|USA nyugati régiója, 2.| 100 | 20 |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Azure Functions méretezési és üzemeltetési lehetőségeinek megismerése](functions-scale.md)
