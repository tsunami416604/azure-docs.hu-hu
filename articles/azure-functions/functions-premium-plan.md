---
title: Prémium szintű Azure Functions-csomag
description: Részletek és konfigurációs beállítások (VNet, nem hideg indítás, korlátlan végrehajtási időtartam) a Azure Functions Premium csomaghoz.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: 5f6825243b7e410b49b54d04a028b5d71610ea68
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561954"
---
# <a name="azure-functions-premium-plan"></a>Prémium szintű Azure Functions-csomag

A Azure Functions Prémium csomag (más néven a rugalmas Prémium csomag) egy üzemeltetési lehetőség a Function apps számára. A Prémium csomag olyan szolgáltatásokat nyújt, mint például a VNet-kapcsolat, a hidegindító és a prémium szintű hardverek.  Több Function apps is telepíthető ugyanarra a prémium csomagra, és a csomag lehetővé teszi a számítási példány méretének, az alapcsomag méretének és a maximális méretnek a konfigurálását.  A Prémium csomag és az egyéb csomag-és üzemeltetési típusok összehasonlítását lásd: a [függvények méretezési és üzemeltetési lehetőségei](functions-scale.md).

## <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Prémium szintű csomagot az az [functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) paranccsal is létrehozhat az Azure CLI-ben. Az alábbi példa egy _rugalmas prémium 1_ szintű csomagot hoz létre:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Ebben a példában cserélje le a `<RESOURCE_GROUP>`t az erőforráscsoporthoz, és `<PLAN_NAME>` az erőforráscsoport egyedi csomagjának nevére. Válasszon egy [támogatott `<REGION>`](#regions). A Linuxot támogató Prémium csomag létrehozásához adja meg a `--is-linux` lehetőséget.

A terv létrehozásakor az [az functionapp Create](/cli/azure/functionapp#az-functionapp-create) paranccsal hozhatja létre a Function alkalmazást. A portálon a csomag és az alkalmazás is egyszerre jön létre. A teljes Azure CLI-szkriptre vonatkozó példát a [Function app létrehozása prémium](scripts/functions-cli-create-premium-plan.md)szintű csomagban talál.

## <a name="features"></a>Jellemzők

A következő szolgáltatások használhatók a prémium csomagba telepített alkalmazások működéséhez.

### <a name="pre-warmed-instances"></a>Előre bemelegítő példányok

Ha a használati tervben jelenleg nem fordulnak elő események és végrehajtások, az alkalmazás nulla példányra csökkentheti a méretét. Új események beolvasásakor egy új példányra van szükség a rajta futó alkalmazáshoz.  Az új példányok specializálása az alkalmazástól függően hosszabb időt is igénybe vehet.  Ezt a további késleltetést az első hívásnál gyakran az App Cold Start metódusnak nevezik.

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

További információk: [a Function app integrálása VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Gyors rugalmas skála

Az alkalmazáshoz további számítási példányok is automatikusan hozzáadódnak a használati tervvel megegyező gyors skálázási logikával.  Ha többet szeretne megtudni a skálázás működéséről, tekintse meg a [függvények méretezése és üzemeltetése](./functions-scale.md#how-the-consumption-and-premium-plans-work)című témakört.

### <a name="longer-run-duration"></a>Hosszabb Futtatás időtartama

A Azure Functions a használati terv egyetlen végrehajtás esetén 10 percre van korlátozva.  A Prémium csomag esetében a futtatási időtartam alapértelmezett értéke 30 perc, hogy megakadályozza a Runaway végrehajtást. [A Host. JSON-konfiguráció azonban módosítható](./functions-host-json.md#functiontimeout) úgy, hogy ezt a 60 percet biztosítsa a prémium szintű csomag alkalmazásai számára.

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

|SKU (Cikkszám)|Processzormagok|Memória|Adattárolás|
|--|--|--|--|
|EP1|1|3.5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>Térségek

Az alábbiakban láthatók az egyes operációs rendszerek jelenleg támogatott régiói.

|Region (Régió)| Windows | Linux |
|--| -- | -- |
|Ausztrália középső régiója| ✔<sup>1</sup> | |
|Ausztrália 2. középső régiója| ✔<sup>1</sup> | |
|Ausztrália keleti régiója| ✔ | |
|Délkelet-Ausztrália | ✔ | ✔<sup>1</sup> |
|Dél-Brazília| ✔<sup>2</sup> |  |
|Közép-Kanada| ✔ |  |
|USA középső régiója| ✔ |  |
|Kelet-Ázsia| ✔ |  |
|USA keleti régiója | ✔ | ✔<sup>1</sup> |
|USA 2. keleti régiója| ✔ |  |
|Közép-Franciaország| ✔ |  |
|Középnyugat-Németország| ✔ | |
|Kelet-Japán| ✔ | ✔<sup>1</sup> |
|Nyugat-Japán| ✔ | |
|Korea középső régiója| ✔ |  |
|USA északi középső régiója| ✔ |  |
|Észak-Európa| ✔ | ✔<sup>1</sup> |
|USA déli középső régiója| ✔ | ✔<sup>1</sup> |
|Dél-India | ✔ | |
|Délkelet-Ázsia| ✔ | ✔<sup>1</sup> |
|Egyesült Királyság déli régiója| ✔ | |
|Egyesült Királyság nyugati régiója| ✔ |  |
|Nyugat-Európa| ✔ | ✔<sup>1</sup> |
|Nyugat-India| ✔ |  |
|USA nyugati régiója| ✔ | ✔<sup>1</sup> |
|USA 2. nyugati régiója| ✔ |  |

<sup>1</sup> A maximális méretezés legfeljebb 20 példányra korlátozódik.  
<sup>2</sup> A maximális felskálázás 60 példányra korlátozódik.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Azure Functions méretezési és üzemeltetési lehetőségeinek megismerése](functions-scale.md)
