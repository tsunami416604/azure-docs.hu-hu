---
title: Az Azure Functions prémium szintű csomag (előzetes verzió) |} A Microsoft Docs
description: Részletek és a konfigurációs beállításokat (VNet, nem hidegindítási, korlátlan végrehajtási időtartama) prémium szintű Azure Functions tervezze meg.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: jehollan
ms.openlocfilehash: ca65b6a1691a870054682b36109f2bdc10d4ad98
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918705"
---
# <a name="azure-functions-premium-plan-preview"></a>Az Azure Functions prémium szintű csomag (előzetes verzió)

Az Azure Functions prémium szintű csomag üzemeltetési beállítás alkalmazások esetében. A prémium szintű csomag olyan szolgáltatások, mint a virtuális hálózatok közötti kapcsolat, nem hidegindítási és a prémium szintű hardvert biztosít.  A prémium szintű csomagot több függvényalkalmazás telepíthetők, és a csomag lehetővé teszi, hogy konfigurálja a számítási példány mérete, alapszintű csomag méretét és a csomag maximális méretét.  A prémium szintű csomag és az egyéb terv és a típusok üzemeltető összehasonlításáért lásd: [függvény méretezése és üzemeltetése beállítások](functions-scale.md).

> [!NOTE]
> A Premium csomag előzetes funkciók keresztül Windows infrastruktúra a .NET, a Node vagy a Java futtató jelenleg támogatja.

## <a name="create-a-premium-plan"></a>Hozzon létre egy prémium szintű csomag

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Prémium szintű csomagot is létrehozhat az Azure parancssori felületen

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Szolgáltatások

A következő funkciók érhetők el a függvényalkalmazásokat üzembe helyezett egy prémium szintű csomag.

### <a name="pre-warmed-instances"></a>Előre fűtéssel példányok

Ha nincsenek események és a végrehajtás még ma a Használatalapú csomag fel, a rendszer az alkalmazás lehetséges, hogy méretezhető nulla-példányokhoz. Amikor új események származnak, egy új példányt kell lehet kifejezetten az azon futó alkalmazását.  Specializace új példányokat az alkalmazástól függően hosszabb időt is igénybe vehet.  Erre a további késésre első hívásakor alkalmazás hidegindítási gyakran nevezik.

A prémium szintű csomag az alkalmazás a megadott számú példányok előmelegíteni is rendelkezhet.  Példányok előre fűtéssel is lehetővé teszi az előre egy alkalmazást, mielőtt nagy terhelés. Az alkalmazás elvégzi a horizontális felskálázást, azt először arányban a előre fűtéssel példányok be. További példányok horizontális fel- és azonnal a következő skálázási művelet előkészítésekor a meleg puffer továbbra is. Előre fűtéssel példányok puffer sablonkonfigurációkat hatékonyan elkerülheti a hidegindítás késéseket.  Előre fűtéssel példányok csak a prémium szintű csomag, a és továbbra is szeretné legalább egy példánya fut és elérhető minden alkalommal a terv aktív.

Konfigurálhatja előzetesen fűtéssel példányok az Azure Portalon kiválasztásával **horizontális Felskálázás** a a **Platformfunkciók** fülre.

![Rugalmas méretezés beállításai](./media/functions-premium-plan/scale-out.png)

Az Azure CLI használatával is konfigurálhatja az alkalmazás előre fűtéssel példányok

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Privát hálózati kapcsolatok

Az Azure Functions üzembe helyezett egy prémium szintű csomag kihasználja [új VNet-integráció, a web apps](../app-service/web-sites-integrate-with-vnet.md#new-vnet-integration).  Ha konfigurálva, az alkalmazás képes kommunikálni a virtuális hálózaton belüli erőforrásokhoz vagy Szolgáltatásvégpontok védelméről.  Az IP-korlátozások az alkalmazásnak, hogy a bejövő forgalom korlátozása is elérhetők.

A függvényalkalmazás a prémium szintű csomagban lévő alhálózat hozzárendelése, amikor szüksége elegendő IP-cím az alhálózat minden lehetséges példány. Az előzetes verzió ideje alatt a példányok maximális száma eltérőek lehetnek, ha szükség van legalább 100 elérhető címekkel rendelkező olyan IP-Címblokk.

További információkat lásd: [a függvényalkalmazás integrálása egy virtuális hálózat](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Gyors, rugalmas méretezéssel

További számítási példányokat a rendszer automatikusan hozzáadja az alkalmazáshoz, mint a Használatalapú csomag gyors méretezési logikák felhasználásával.  Hogyan méretezési működésével kapcsolatos további információkért lásd: [függvény méretezése és üzemeltetése](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Futtatás időtartama unbounded

Az Azure Functions használatalapú korlátozva, egy egyetlen végrehajtási 10 perc.  A prémium csomag a Futási időtartam alapértelmezés szerint 30 percre sorozatos végrehajtások elkerülése érdekében. Azonban továbbra is [host.json konfigurációjának módosítása](./functions-host-json.md#functiontimeout) , hogy a korlátlan streameken működő, a prémium szintű csomag alkalmazások.

## <a name="plan-and-sku-settings"></a>Csomag és a Termékváltozat-beállítások

A csomag létrehozásakor a két beállítás konfigurálása: példányok (vagy a csomag méretét) minimális számát és a maximális burst korlátot.  A minimális példányok egy prémium szintű csomagot: 1, és 20 a maximális adatlöketek, az előzetes verzió ideje alatt.  Minimális példányai fenntartott és mindig futnak.

> [!IMPORTANT]
> Az egyes példányok lefoglalva a minimális példányszám függetlenül ha funkciók állnak végrehajtás alatt, vagy nem terheli.

Ha az alkalmazásának példányok mellett a csomag méretét, az továbbra is horizontális felskálázás, amíg a példányok száma eléri a maximális burst korlátot.  Számítunk fel díjat a példány túl a csomag méretét csak során fut, és az Ön bérelt.  A legjobb erőfeszítés található meg a meghatározott maximális korlátot, az alkalmazás méretezése használunk, mivel az alkalmazás a minimális csomagpéldányt garantáltan.

Beállíthatja, hogy a csomag méretét, és maximális értékeket az Azure Portalon, a kiválasztott a **horizontális Felskálázás** a szolgáltatáscsomag vagy a csomag üzembe helyezett függvényalkalmazás beállításainak (alatt **Platformfunkciók**).

Növelje a hirtelen maximális korlátot, az Azure parancssori felületen:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Elérhető példányhoz termékváltozatok

Amikor hoz létre, a méretezés a tervet, választhat a három példányméretek.  Díjköteles a magok és a másodpercenként felhasznált memória teljes száma.  Az alkalmazás is automatikusan horizontális felskálázás több példányra igény szerint.  

|SKU|Processzormagok|Memory (Memória)|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14GB|250 GB|

## <a name="regions"></a>Régiók

Az alábbiakban a jelenleg támogatott régiókat a nyilvános előzetes verzió.

|Régió|
|--|
|Kelet-Ausztrália|
|Ausztrália Souteast|
|Közép-Kanada|
|Közép-India|
|USA középső régiója|
|Kelet-Ázsia|
|USA 2. keleti régiója|
|Közép-Franciaország|
|Nyugat-Japán|
|Korea középső régiója|
|Észak-Európa|
|USA déli középső régiója|
|Dél-India|
|Délkelet-Ázsia|
|Az Egyesült Királyság nyugati régiója|
|Nyugat-Európa|
|Nyugat-India|
|USA nyugati régiója|

## <a name="known-issues"></a>Ismert problémák

Ismert problémák az állapotának nyomon követheti a [nyilvános előzetes verzióban a Githubon](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Functions méretezése és üzemeltetési lehetőségeinek ismertetése](functions-scale.md)
