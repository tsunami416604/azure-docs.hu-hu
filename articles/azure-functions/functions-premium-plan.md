---
title: Azure Functions Premium csomag
description: Részletek és konfigurációs lehetőségek (virtuális hálózat, nincs hidegindítás, korlátlan végrehajtási időtartam) az Azure Functions Premium csomaghoz.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276906"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium csomag

Az Azure Functions Premium csomag (más néven Rugalmas Prémium csomag) egy üzemeltetési lehetőség a függvényalkalmazások. A prémium csomag olyan funkciókat biztosít, mint a virtuális hálózat kapcsolata, nincs hidegindítás és prémium szintű hardver.  Több függvényalkalmazások is telepíthetők ugyanarra a prémium csomagra, és a csomag lehetővé teszi a számítási példány méretének, az alapterv méretének és a maximális csomagméret konfigurálását.  A Prémium csomag és más csomag- és üzemeltetési típusok összehasonlítását lásd a [függvényskálán és az üzemeltetési lehetőségekben.](functions-scale.md)

## <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Prémium csomagot is létrehozhat az [a functionapp-terv létrehozása](/cli/azure/functionapp/plan#az-functionapp-plan-create) az Azure CLI használatával. A következő példa létrehoz egy _Elastic Premium 1_ szintű csomagot:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Ebben a példában cserélje `<RESOURCE_GROUP>` le `<PLAN_NAME>` az erőforráscsoportot, és a terv, amely egyedi az erőforráscsoportban. Adjon meg egy [ `<REGION>`támogatottat. ](https://azure.microsoft.com/global-infrastructure/services/?products=functions) A Linuxot támogató Prémium csomag létrehozásához adja meg ezt a `--is-linux` lehetőséget.

A terv létrehozása, használhatja [az functionapp létrehozása](/cli/azure/functionapp#az-functionapp-create) a függvényalkalmazás létrehozásához. A portálon a csomag és az alkalmazás egy időben jön létre. Egy példa egy teljes Azure CLI-parancsfájl, [lásd: Függvényalkalmazás létrehozása prémium csomagban.](scripts/functions-cli-create-premium-plan.md)

## <a name="features"></a>Szolgáltatások

A következő funkciók érhetők el a prémium csomagba telepített funkcióalkalmazások számára.

### <a name="pre-warmed-instances"></a>Előmelegített példányok

Ha ma nem történik esemény és végrehajtás a felhasználási tervben, az alkalmazás nullára méretezhető. Amikor új események érkeznek, egy új példányt kell specializálódni a rajta futó alkalmazással.  Az alkalmazástól függően az új példányok specializálása eltarthat egy ideig.  Ezt a további késést az első hívás gyakran nevezik app hidegindítás.

A Prémium csomagban az alkalmazást előre felmelegítheti egy adott számú példányon, a minimális csomagméretig.  Az előre felmelegített példányok lehetővé teszik egy alkalmazás előzetes méretezését a nagy terhelés előtt. Az alkalmazás horizontális felskálázása, először az előre felmelegített példányok. További példányok továbbra is puffer ki, és azonnal meleg előkészítése a következő méretezési művelet. Az előmelegített példányok puffere hatékonyan elkerülheti a hidegindítási késéseket.  Az előmelegített példányok a Prémium csomag egyik szolgáltatása, és legalább egy példányt futtatni és elérhetővé kell tenni mindenkor, amikor a csomag aktív.

Az előmelegített példányok számát az Azure Portalon a **kiválasztott függvényalkalmazással**konfigurálhatja, a **Platformszolgáltatások** lapon, és a **Horizontális felskálázási** beállítások kiválasztásával. A függvényalkalmazás szerkesztési ablakában az előre felmelegített példányok az adott alkalmazásra jellemzőek, de a minimális és maximális példányok a teljes csomagra vonatkoznak.

![Rugalmas méretezési beállítások](./media/functions-premium-plan/scale-out.png)

Az Azure CLI-vel egy alkalmazás előmelegített példányait is konfigurálhatja.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Magánhálózati kapcsolat

A Prémium csomagba telepített Azure Functions kihasználja a [webalkalmazások új virtuális hálózat-integrációját.](../app-service/web-sites-integrate-with-vnet.md)  Ha konfigurálva van, az alkalmazás kommunikálhat a virtuális hálózaton belüli vagy a szolgáltatásvégpontokon keresztül védett erőforrásokkal.  Az IP-korlátozások az alkalmazásban is elérhetők a bejövő forgalom korlátozására.

Ha egy prémium csomagban alhálózatot rendel a függvényalkalmazáshoz, minden lehetséges példányhoz elegendő IP-címmel rendelkező alhálózatra van szüksége. Legalább 100 elérhető címmel rendelkező IP-blokkra van szükségünk.

További információt [a függvényalkalmazás integrálása virtuális hálózattal](functions-create-vnet.md)című témakörben talál.

### <a name="rapid-elastic-scale"></a>Gyors rugalmas skála

További számítási példányok automatikusan hozzáadódik az alkalmazás hoz ugyanazt a gyors skálázási logika, mint a fogyasztási terv.  Ha többet szeretne megtudni a méretezés működéséről, olvassa el a [Függvényméretezés és -üzemeltetési témakört.](./functions-scale.md#how-the-consumption-and-premium-plans-work)

### <a name="longer-run-duration"></a>Hosszabb futási időtartam

Az Azure Functions egy felhasználási csomagban egyetlen végrehajtás esetén 10 percre van korlátozva.  A prémium szintű csomagban a futtatási időtartam alapértelmezés szerint 30 perc a szökevény végrehajtások megelőzése érdekében. A [host.json konfigurációját](./functions-host-json.md#functiontimeout) azonban módosíthatja úgy, hogy ez a prémium csomagalkalmazások (garantáltan 60 perc) nem legyen korlátozás nélkül.

## <a name="plan-and-sku-settings"></a>Terv és Termékváltozat beállításai

A terv létrehozásakor két beállítást kell beállítania: a példányok minimális számát (vagy a csomag méretét) és a maximális burst korlátot.  A minimális példányok le vannak foglalva, és mindig futnak.

> [!IMPORTANT]
> A minimális példányszámban lefoglalt minden példányért díjat számítunk fel, függetlenül attól, hogy a függvények végrehajtása folyamatban van-e vagy sem.

Ha az alkalmazás a csomag méretén túli példányokat igényel, továbbra is skálázható, amíg a példányok száma el nem éri a maximális burst korlátot.  A csomag méretén túli példányok ért számlázása csak akkor, ha azok futnak és bérbe adják.  Mindent megteszünk annak érdekében, hogy az alkalmazást a megadott maximális korlátra skálázjuk, míg a minimális csomagpéldányok garantáltak az alkalmazásszámára.

A csomag méretét és a maximumokat az Azure Portalon konfigurálhatja a csomag horizontális **felskálázási** beállításainak kiválasztásával, vagy az adott tervre telepített függvényalkalmazás kiválasztásával (a **Platform features)** területen.

Az Azure CLI maximális burst limitjét is növelheti:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Elérhető példánysulaka

A terv létrehozásakor vagy méretezésekor három példányméret közül választhat.  A magok és a másodpercenként felhasznált memória teljes számát kiszámlázjuk.  Az alkalmazás szükség szerint automatikusan több példányra is skálázható.  

|SKU|Cores|Memory (Memória)|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB-os|
|EP2 (EP2)|2|7 GB-os|250 GB-os|
|EP3 (EP3)|4|14 GB|250 GB-os|

### <a name="memory-utilization-considerations"></a>A memória kihasználtságának szempontjai
A több memóriával rendelkező gépen való futtatás nem mindig jelenti azt, hogy a függvényalkalmazás az összes rendelkezésre álló memóriát használni fogja.

Egy JavaScript-függvényalkalmazást például a Node.js alapértelmezett memóriakorlátkorlátozza. A rögzített memóriakorlát növeléséhez adja `languageWorkers:node:arguments` hozzá a `--max-old-space-size=<max memory in MB>`.

## <a name="region-max-scale-out"></a>Régió maximális horizontális felskálázása

Az alábbiakban a jelenleg támogatott maximális horizontális felskálázási értékek et egy terv minden régióban és operációsrendszer-konfigurációban. Emelés igényléséhez nyisson meg egy támogatási jegyet.

Lásd a funkciók teljes regionális elérhetőségét itt: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

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
|Németország Nyugat-Közép| 100 | Nem érhető el |
|Kelet-Japán| 100 | 20 |
|Nyugat-Japán| 100 | 20 |
|Dél-Korea középső régiója| 100 | 20 |
|USA északi középső régiója| 100 | 20 |
|Észak-Európa| 100 | 20 |
|Norvégia Kelet| 20 | 20 |
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
> [Az Azure Functions méretezési és üzemeltetési beállításainak megismerése](functions-scale.md)
