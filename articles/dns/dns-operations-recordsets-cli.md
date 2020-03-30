---
title: DNS-rekordok kezelése az Azure DNS-ben az Azure CLI használatával
description: DNS-rekordkészletek és rekordok kezelése az Azure DNS-en, amikor a tartományt az Azure DNS-en üzemelteti.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 4e017dc940e1d32888ff279904e44d34db1fd5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936887"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>DNS-rekordok és rekordhalmazok kezelése az Azure DNS-ben az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-rekordok a DNS-zóna segítségével a platformfüggetlen Azure CLI, amely elérhető a Windows, Mac és Linux. A DNS-rekordokat az [Azure PowerShell](dns-operations-recordsets.md) vagy az [Azure Portal](dns-operations-recordsets-portal.md)használatával is kezelheti.

A cikkben szereplő példák feltételezik, hogy már [telepítette az Azure CLI-t, bejelentkezett, és létrehozott egy DNS-zónát.](dns-operations-dnszones-cli.md)

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekord létrehozásához használja `az network dns record-set <record-type> add-record` a `<record-type>` parancsot (hol van a rekord típusa, azaz a, srv, txt stb.) Ha segítségre `az network dns record-set --help`van szüksége, olvassa el a témakört.

Egy rekord létrehozásakor meg kell adni az erőforráscsoport, a zóna és a rekordhalmaz nevét, a rekordtípust és a létrehozandó rekord részletes adatait. A megadott rekordhalmaz-névnek *relatív* névnek kell lennie, ami azt jelenti, hogy ki kell zárnia a zónanevét.

Ha a rekordhalmaz még nem létezik, akkor a parancs létrehozza. Ha az adott rekordhalmaz már létezik, a parancs felveszi a megadott rekordot a meglévő rekordhalmazba.

Új rekordhalmaz létrehozásakor az alapértelmezett élettartam (time-to-live, TTL) értéke 3600 lesz. A különböző ttl-ek használatáról a [DNS-rekordkészlet létrehozása című](#create-a-dns-record-set)témakörben talál.

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoport *contoso.com* zónájában egy *www* nevű, „A” típusú rekordot hoz létre. Az „A” rekord IP-címe: *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Ha a zóna csúcsán (ebben az esetben "contoso.com" ) rekordhalmazt\@szeretne létrehozni, használja a " ", beleértve az idézőjeleket is:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>DNS-rekordkészlet létrehozása

A fenti példákban a DNS-rekord vagy egy meglévő rekordkészlethez lett hozzáadva, vagy a rekordkészlet *implicit módon*lett létrehozva. A rekordkészletet *explicit módon* is létrehozhatja, mielőtt rekordokat adhozzá. Az Azure DNS támogatja az "üres" rekordhalmazokat, amelyek helyőrzőként működhetnek a DNS-nevek lefoglalásához a DNS-rekordok létrehozása előtt. Az üres rekordkészletek láthatók az Azure DNS-vezérlősíkon, de nem jelennek meg az Azure DNS-névkiszolgálóin.

A rekordkészletek a `az network dns record-set <record-type> create` paranccsal jönnek létre. További segítségért lásd: `az network dns record-set <record-type> create --help`.

A rekordkészlet kifejezett létrehozása lehetővé teszi a rekordkészlet tulajdonságainak megadását, például az [élő (Time-to-Live) és](dns-zones-records.md#time-to-live) a metaadatok megadását. [A rekordkészlet metaadatai](dns-zones-records.md#tags-and-metadata) segítségével alkalmazásspecifikus adatokat társíthat minden rekordkészlethez kulcsérték-párként.

A következő példa egy "A" típusú üres rekordkészletet hoz létre 60 `--ttl` másodperces `-l`TTL-lel a paraméter használatával (rövid űrlap):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

A következő példa két metaadat-bejegyzéssel ("dept=finance" és "environment=production" `--metadata` rekordkészletet hoz létre a paraméter használatával:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Üres rekordkészlet létrehozása után a rekordok `azure network dns record-set <record-type> add-record` a [DNS-rekord létrehozása](#create-a-dns-record)című részben leírtak szerint adhatók hozzá.

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Az "A" rekordok létrehozásának részletes et követően az alábbi példák bemutatják, hogyan hozhat létre rekordot az Azure DNS által támogatott más rekordtípusokról.

A rekordadatok megadásához használt paraméterek a rekord típusától függnek. Az „A” típusú rekordok esetén például a `--ipv4-address <IPv4 address>` paraméterrel lehet megadni az IPv4-címet. Az egyes bejegyzéstípusok paraméterei a `az network dns record-set <record-type> add-record --help`használatával jelenhetnek meg.

Minden esetben megmutatjuk, hogyan hozhat létre egyetlen rekordot. A rekord hozzáadódik a meglévő rekordkészlethez, vagy implicit módon létrehozott rekordhalmaz. A rekordhalmazok létrehozásáról és a rekordkészlet-paraméter explicit definiálásáról a [DNS-rekordkészlet létrehozása](#create-a-dns-record-set)című témakörben talál további információt.

Nem adunk példát SOA rekordkészlet létrehozására, mivel a SOA-k minden DNS-zónával jönnek létre és törlődnek, és nem hozhatók létre vagy törölhetők külön.We do not a example to create a SOA record set, since SOAs are created and deleted with each DNS zone and not be created or deleted separate ly. A [SOA azonban módosítható, amint az egy későbbi példában is látható.](#to-modify-an-soa-record)

### <a name="create-an-aaaa-record"></a>AAAA rekord létrehozása

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>CAA-rekord létrehozása

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>CNAME rekord létrehozása

> [!NOTE]
> A DNS-szabványok nem engedélyezik a CNAME rekordokat a zóna csúcsán (`--Name "@"`), és nem engedélyezik az egynél több rekordot tartalmazó rekordkészleteket sem.
> 
> További információ: [CNAME records](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>MX rekord létrehozása

Ebben a példában a "\@" rekordnév segítségével hozhatjuk létre az MX rekordot a zóna csúcsán (ebben az esetben a "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS-rekord létrehozása

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>PTR-rekord létrehozása

Ebben az esetben a "my-arpa-zone.com" az Ön IP-tartományát képviselő ARPA zónát jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg.  A "10" rekordnév a rekord által képviselt IP-tartományon belüli IP-cím utolsó oktettje.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>SRV rekord létrehozása

[SRV rekordkészlet](dns-zones-records.md#srv-records)létrehozásakor adja meg a * \_szolgáltatást* és * \_* a protokollt a rekordhalmaz nevében. Nincs szükség a "\@" " felvételre a rekordhalmaz nevében, amikor srv rekordkészletet hoz létre a zóna csúcsán.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>TXT rekord létrehozása

A következő példa bemutatja, hogyan hozhat létre TXT rekordot. A TXT-rekordokban támogatott maximális karakterlánchosszról a [TXT-rekordok című témakörben olvashat bővebben.](dns-zones-records.md#txt-records)

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Rekordkészlet beszereznie

Meglévő rekordkészlet beolvasásához `az network dns record-set <record-type> show`használja a használatát. További segítségért lásd: `az network dns record-set <record-type> show --help`.

Rekord vagy rekordkészlet létrehozásakor a megadott rekordhalmaz nevének *relatív* névnek kell lennie, ami azt jelenti, hogy ki kell zárnia a zónanevét. Meg kell adnia a rekord típusát, a rekordhalmazt tartalmazó zónát és a zónát tartalmazó erőforráscsoportot is.

A következő példa a *MyResourceGroup*erőforráscsoport *contoso.com* zónaból olvassa be *az* A típusú rekordot:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Listarekord-készletek

A DNS-zónában lévő összes rekordot a `az network dns record-set list` parancs segítségével listázhatja. További segítségért lásd: `az network dns record-set list --help`.

Ez a példa a *contoso.com*zónában lévő összes rekordhalmazt adja vissza a *MyResourceGroup*erőforráscsoportban, névtől és bejegyzéstípustól függetlenül:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Ez a példa az adott bejegyzéstípusnak megfelelő összes rekordhalmazt adja vissza (ebben az esetben "A" rekordok):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Rekord hozzáadása meglévő rekordkészlethez

Mindkettőt `az network dns record-set <record-type> add-record` használhatja rekord létrehozásához egy új rekordhalmazban, vagy hozzáadhat egy rekordot egy meglévő rekordkészlethez.

További információt a [DNS-rekord létrehozása](#create-a-dns-record) és a fenti más típusú rekordok létrehozása című témakörben [talál.](#create-records-of-other-types)

## <a name="remove-a-record-from-an-existing-record-set"></a>Rekord eltávolítása meglévő rekordkészletből.

Ha egy DNS-rekordot el szeretne `az network dns record-set <record-type> remove-record`távolítani egy meglévő rekordkészletből, használja a használatát. További segítségért lásd: `az network dns record-set <record-type> remove-record -h`.

Ez a parancs dns-rekordot töröl egy rekordkészletből. Ha egy rekordkészlet utolsó rekordja törlődik, maga a rekordkészlet is törlődik. Az üres rekordkészlet megtartásához `--keep-empty-record-set` használja ezt a beállítást.

Meg kell adnia a törölni kívánt rekordot és azt a zónát, amelyből `az network dns record-set <record-type> add-record`törölni kell, ugyanazokat a paramétereket használva, mint amikor a használatával hoz létre egy rekordot. Ezeket a paramétereket a [DNS-rekord létrehozása](#create-a-dns-record) és a fenti más típusú rekordok létrehozása című részben [ismertetik](#create-records-of-other-types) le.

A következő példa törli az "1.2.3.4" értékű A rekordot a *www* nevű rekordhalmazból a *contoso.com*zónában , a *MyResourceGroup*erőforráscsoportban.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Meglévő rekordkészlet módosítása

Minden rekordkészlet tartalmaz egy [élő (TTL)](dns-zones-records.md#time-to-live), [metaadatokat](dns-zones-records.md#tags-and-metadata)és DNS-rekordokat. A következő szakaszok bemutatják, hogyan módosíthatja ezeket a tulajdonságokat.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>A, AAAA, CAA, MX, NS, PTR, SRV vagy TXT rekord módosítása

A meglévő A, AAAA, CAA, MX, NS, PTR, SRV vagy TXT típusú rekord módosításához először vegyen fel egy új rekordot, majd törölje a meglévő rekordot. A rekordok törlésével és hozzáadásával kapcsolatos részletes útmutatást a cikk korábbi szakaszaiban találja.

A következő példa bemutatja, hogyan lehet módosítani az "A" rekordot az 1.2.3.4 IP-címről az 5.6.7.8 IP-címre:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Az automatikusan létrehozott NS rekordkészlet rekordjai nem adhatók hozzá,`--Name "@"`nem távolíthatók el és nem módosíthatók a zóna csúcsán ( idézőjelek et is beleértve). Ennél a rekordkészletnél csak a TTL rekordkészlet és a metaadatok módosítása engedélyezett.

### <a name="to-modify-a-cname-record"></a>CNAME rekord módosítása

A legtöbb bejegyzéstípustól eltérően a CNAME rekordhalmaz csak egyetlen rekordot tartalmazhat.  Ezért az aktuális értéket nem helyettesítheti új rekord hozzáadásával és a meglévő rekord eltávolításával, mint más bejegyzéstípusok esetében.

Ehelyett a CNAME rekord módosításához használja a használatát. `az network dns record-set cname set-record` Ha segítségre van szüksége, olvassa el a`az network dns record-set cname set-record --help`

A példa módosítja a *www* cname rekordkészletet a *contoso.com*zónában a *Sajáterőforrás-csoport*erőforráscsoportban , hogy a meglévő érték helyett a "www.fabrikam.net" értéket mutassa meg:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>SOA rekord módosítása

A legtöbb bejegyzéstípustól eltérően a CNAME rekordhalmaz csak egyetlen rekordot tartalmazhat.  Ezért az aktuális értéket nem helyettesítheti új rekord hozzáadásával és a meglévő rekord eltávolításával, mint más bejegyzéstípusok esetében.

Ehelyett a SOA rekord módosításához használja a használatát. `az network dns record-set soa update` További segítségért lásd: `az network dns record-set soa update --help`.

A következő példa bemutatja, hogyan állíthatja be a zóna SOA-rekordjának "e-mail" *tulajdonságát, contoso.com* a *MyResourceGroup*erőforráscsoportban:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS-rekordok módosítása a zóna csúcsán

A zóna csúcsán beállított NS rekord automatikusan létrejön az egyes DNS-zónákkal. A zónához rendelt Azure DNS-névkiszolgálók nevét tartalmazza.

A ns rekordkészlethez további névkiszolgálókat is hozzáadhat, így több DNS-szolgáltatóval is támogathatja a tartományok közös üzemeltetését. A rekordkészlet TTL- és metaadatait is módosíthatja. Az előre kitöltött Azure DNS-névkiszolgálók azonban nem távolíthatók el és nem módosíthatók.

Ne feledje, hogy ez csak a zóna csúcsán beállított NS rekordra vonatkozik. A zónában lévő egyéb ns rekordkészletek (a gyermekzónák delegálásához használt) korlátozás nélkül módosíthatók.

A következő példa bemutatja, hogyan adhat hozzá további névkiszolgálót az NS-rekordkészlethez a zóna csúcsán:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Meglévő rekordkészlet TTL-jének módosítása

Meglévő rekordkészlet TTL-jének módosításához használja a használatát. `azure network dns record-set <record-type> update` További segítségért lásd: `azure network dns record-set <record-type> update --help`.

A következő példa bemutatja, hogyan lehet módosítani egy rekordkészlet TTL, ebben az esetben 60 másodperc:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Meglévő rekordkészlet metaadatainak módosítása

[A rekordkészlet metaadatai](dns-zones-records.md#tags-and-metadata) segítségével alkalmazásspecifikus adatokat társíthat minden rekordkészlethez kulcsérték-párként. Meglévő rekordkészlet metaadatainak módosításához `az network dns record-set <record-type> update`használja a használatát. További segítségért lásd: `az network dns record-set <record-type> update --help`.

A következő példa bemutatja, hogyan lehet módosítani egy rekordkészletet két metaadat-bejegyzéssel, az "dept=finance" és a "environment=production" bejegyzésekkel. Vegye figyelembe, hogy a meglévő metaadatokat a megadott értékek *helyettesítik.*

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Rekordkészlet törlése

A rekordkészletek a `az network dns record-set <record-type> delete` paranccsal törölhetők. További segítségért lásd: `azure network dns record-set <record-type> delete --help`. A rekordhalmaz törlésével a rekordhalmazösszes rekordja is törlődik.

> [!NOTE]
> A SOA és az NS rekordkészletek nem`--name "@"`törölhetők a zóna csúcsán ( ).  Ezek a zóna létrehozásakor automatikusan létrejönnek, és a zóna törlésekor automatikusan törlődnek.

A következő példa törli a *Www típusú Www* nevű rekordot a *MyResourceGroup*erőforráscsoport *contoso.com* zónájából:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

A rendszer kéri a törlési művelet megerősítését. A kérdés letiltásához `--yes` használja a kapcsolót.

## <a name="next-steps"></a>További lépések

További információ [az Azure DNS zónáiról és rekordjairól.](dns-zones-records.md)
<br>
Ismerje meg, hogyan [védheti meg a zónákat és a rekordokat az](dns-protect-zones-recordsets.md) Azure DNS használata során.
