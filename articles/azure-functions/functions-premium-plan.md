---
title: Azure Functions Prémium csomag (előzetes verzió) | Microsoft Docs
description: Részletek és konfigurációs beállítások (VNet, nem hideg indítás, korlátlan végrehajtási időtartam) a Azure Functions Premium csomaghoz.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: 2cc6493d01508d439d8dcef2d12ca1ea40632d81
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096231"
---
# <a name="azure-functions-premium-plan-preview"></a>Azure Functions Premium csomag (előzetes verzió)

A Azure Functions Premium csomag egy üzemeltetési lehetőség a Function apps alkalmazásokhoz. A Prémium csomag olyan szolgáltatásokat nyújt, mint például a VNet-kapcsolat, a hidegindító és a prémium szintű hardverek.  Több Function apps is telepíthető ugyanarra a prémium csomagra, és a csomag lehetővé teszi a számítási példány méretének, az alapcsomag méretének és a maximális méretnek a konfigurálását.  A Prémium csomag és az egyéb csomag-és üzemeltetési típusok összehasonlítását lásd: a [függvények méretezési és üzemeltetési lehetőségei](functions-scale.md).

## <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Prémium szintű csomagot az az [functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) paranccsal is létrehozhat az Azure CLI-ben. Az alábbi példa egy _rugalmas prémium 1_ szintű csomagot hoz létre:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Ebben a példában cserélje le `<RESOURCE_GROUP>` az erőforráscsoportot és `<PLAN_NAME>` az erőforráscsoport egyedi csomagjának nevére. Válasszon egy [ `<REGION>`támogatottat ](#regions). A Linuxot támogató Prémium csomag létrehozásához adja meg a `--is-linux` lehetőséget.

A terv létrehozásakor az [az functionapp Create](/cli/azure/functionapp#az-functionapp-create) paranccsal hozhatja létre a Function alkalmazást. A portálon a csomag és az alkalmazás is egyszerre jön létre. 

## <a name="features"></a>Szolgáltatások

A következő szolgáltatások használhatók a prémium csomagba telepített alkalmazások működéséhez.

### <a name="pre-warmed-instances"></a>Előre bemelegítő példányok

Ha a használati tervben jelenleg nem fordulnak elő események és végrehajtások, az alkalmazás nulla példányra csökkentheti a méretét. Új események beolvasásakor egy új példányra van szükség a rajta futó alkalmazáshoz.  Az új példányok specializálása az alkalmazástól függően hosszabb időt is igénybe vehet.  Ezt a további késleltetést az első hívásnál gyakran az App Cold Start metódusnak nevezik.

A prémium csomaggal előre bemelegítheti az alkalmazást egy adott számú példányra, a minimálisan szükséges méretig.  Az előre bemelegített példányok lehetővé teszik az alkalmazások előre méretezését a nagy terhelés előtt. Az alkalmazás kiskálázásakor először az előre bemelegítő példányokra méretezi a folyamatokat. A további példányok továbbra is kiállnak a pufferbe, és azonnal melegen vesznek részt a következő skálázási művelet előkészítésében. Az előre bemelegített példányok pufferével hatékonyan elkerülheti a hideg indítási késéseket.  Az előre bemelegített példányok a Prémium csomag egyik funkciója, és legalább egy példányon futnia kell, és a csomag aktív állapotban kell lennie.

A Azure Portal előre bemelegítő példányok számát úgy is beállíthatja, hogy kiválasztja a **függvényalkalmazás**, majd a **platform szolgáltatásai** lapra kattint, és kiválasztja a kibővíthető lehetőségeket. Az alkalmazás szerkesztése ablakban az előre felmelegedett példányok az adott alkalmazásra vonatkoznak, de a minimális és a maximális példányszám a teljes tervre vonatkozik.

![Rugalmas méretezési beállítások](./media/functions-premium-plan/scale-out.png)

Az Azure CLI-vel is konfigurálhat előre bemelegített példányokat az alkalmazáshoz

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Magánhálózati kapcsolat

A prémium csomagra telepített Azure Functions a webalkalmazások [új VNet](../app-service/web-sites-integrate-with-vnet.md)-integrációját is kihasználja.  Ha be van állítva, az alkalmazás képes kommunikálni a VNet lévő erőforrásokkal, vagy szolgáltatási végpontokon keresztül.  Az alkalmazásban az IP-korlátozások is elérhetők a bejövő forgalom korlátozására.

Amikor prémium szintű csomagban rendel hozzá egy alhálózatot a Function alkalmazáshoz, szüksége van egy alhálózatra, amely minden lehetséges példányhoz elegendő IP-címmel rendelkezik. Bár az előzetes verzióban a példányok maximális száma eltérő lehet, a rendszer legalább 100 rendelkezésre álló címmel rendelkező IP-blokkot igényel.

További információk: [a Function app integrálása VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Gyors rugalmas skála

Az alkalmazáshoz további számítási példányok is automatikusan hozzáadódnak a használati tervvel megegyező gyors skálázási logikával.  Ha többet szeretne megtudni a skálázás működéséről, tekintse meg a [függvények méretezése és üzemeltetése](./functions-scale.md#how-the-consumption-and-premium-plans-work)című témakört.

### <a name="unbounded-run-duration"></a>Nem kötött Futtatás időtartama

A Azure Functions a használati terv egyetlen végrehajtás esetén 10 percre van korlátozva.  A Prémium csomag esetében a futtatási időtartam alapértelmezett értéke 30 perc, hogy megakadályozza a Runaway végrehajtást. [A Host. JSON-konfiguráció](./functions-host-json.md#functiontimeout) azonban módosítható úgy, hogy ez a prémium szintű csomag alkalmazásai számára is elérhető legyen.

Az előzetes verzióban az időtartam nem garantált az elmúlt 12 percben, és a lehető legkevesebb időt vesz igénybe, ha az alkalmazás nem méretezhető a feldolgozók minimális száma felett.

## <a name="plan-and-sku-settings"></a>Csomag-és SKU-beállítások

A terv létrehozásakor két beállítást kell beállítania: a példányok minimális száma (vagy a csomag mérete) és a maximális burst korlát.  A Prémium csomag minimális példányai 1, az előzetes verzióban pedig a maximális burst érték 20.  A minimális példányok le vannak foglalva, és mindig futnak.

> [!IMPORTANT]
> A rendszer minden egyes, a példányok minimális száma alatt lefoglalt példány díját terheli, függetlenül attól, hogy a függvények végrehajtása történik-e.

Ha az alkalmazás a csomag méretétől meghaladó példányokat igényel, akkor továbbra is kibővíthető, amíg a példányok száma eléri a maximális burst korlátot.  A csomagon kívüli példányok díját csak akkor számítjuk fel, ha a rendszert futtatják és bérbe adják.  A legjobb megoldás az, ha az alkalmazást a meghatározott maximális korlátig méretezni, míg a minimálisan szükséges csomag példányai garantáltak az alkalmazás számára.

A terv méretének és Azure Portal maximális értékének konfigurálásához válassza ki a csomag kibővíthető lehetőségeit vagy az adott tervhez üzembe helyezett Function alkalmazást (a **platform szolgáltatásai**alatt).

Az Azure CLI maximális burst korlátját is megnövelheti:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Rendelkezésre álló példányok SKU-ban

A csomag létrehozásakor vagy skálázásakor három példány mérete közül választhat.  A rendszer a másodpercenként felhasznált magok és memória teljes számát számlázza.  Az alkalmazás igény szerint automatikusan több példányra is kibővíthető.  

|SKU|Processzormagok|Memory (Memória)|Storage|
|--|--|--|--|
|EP1|1|3.5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>Regions

Az alábbiakban a jelenleg támogatott régiók érhetők el az egyes operációs rendszerekhez tartozó nyilvános előzetes verzióhoz.

|Régió| Windows | Linux |
|--| -- | -- |
|Kelet-Ausztrália| ✔ | |
|Délkelet-Ausztrália | ✔ | ✔ |
|Közép-Kanada| ✔ |  |
|USA középső régiója| ✔ |  |
|Kelet-Ázsia| ✔ |  |
|East US | | ✔ |
|USA 2. keleti régiója| ✔ |  |
|Közép-Franciaország| ✔ |  |
|Kelet-Japán|  | ✔ |
|Nyugat-Japán| ✔ | |
|Korea középső régiója| ✔ |  |
|USA északi középső régiója| ✔ |  |
|Észak-Európa| ✔ | ✔ |
|USA déli középső régiója| ✔ |  |
|Dél-India | ✔ | |
|Délkelet-Ázsia| ✔ | ✔ |
|Az Egyesült Királyság nyugati régiója| ✔ |  |
|Nyugat-Európa| ✔ | ✔ |
|Nyugat-India| ✔ |  |
|USA nyugati régiója| ✔ | ✔ |

## <a name="known-issues"></a>Ismert problémák

Nyomon követheti a GitHubon elérhető [nyilvános előzetes](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues)verzió ismert problémáinak állapotát.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Azure Functions méretezési és üzemeltetési lehetőségeinek megismerése](functions-scale.md)
