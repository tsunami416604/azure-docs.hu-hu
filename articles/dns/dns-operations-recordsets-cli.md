---
title: Az Azure CLI használatával az Azure DNS a DNS-rekordok kezelése |} A Microsoft Docs
description: Az Azure DNS-tartomány üzemeltetése esetén kezelése a DNS-rekordhalmazok és rekordok az Azure DNS szolgáltatásra.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: victorh
ms.openlocfilehash: 1f1ee4f69cc1ab656df04ed30cae6f4c3e55bfa7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963815"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Kezelheti a DNS-rekordok és -rekordhalmazok az Azure DNS az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zóna DNS-rekordok használatával a platformfüggetlen Azure parancssori felület, amely Windows, Mac és Linux rendszereken érhető el. DNS-rekordjait is kezelhetők [Azure PowerShell-lel](dns-operations-recordsets.md) vagy a [az Azure portal](dns-operations-recordsets-portal.md).

Ebben a cikkben szereplő példák feltételezik, hogy már [telepítve van az Azure CLI-jelentkezett be, és létrehozott egy DNS-zónát](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekord létrehozásához használja a `az network dns record-set <record-type> add-record` parancsot (ahol `<record-type>` a típusú rekordot, azaz egy, srv, txt stb.) További segítségért lásd: `az network dns record-set --help`.

Egy rekord létrehozásakor meg kell adni az erőforráscsoport, a zóna és a rekordhalmaz nevét, a rekordtípust és a létrehozandó rekord részletes adatait. A megadott rekordhalmaz nevének kell lennie egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét.

Ha a rekordhalmaz még nem létezik, akkor a parancs létrehozza. Ha az adott rekordhalmaz már létezik, a parancs felveszi a megadott rekordot a meglévő rekordhalmazba.

Új rekordhalmaz létrehozásakor az alapértelmezett élettartam (time-to-live, TTL) értéke 3600 lesz. A különböző TTLs használatát útmutatásért lásd: [hozzon létre egy DNS-rekordhalmaz](#create-a-dns-record-set).

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoport *contoso.com* zónájában egy *www* nevű, „A” típusú rekordot hoz létre. Az „A” rekord IP-címe: *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Hozzon létre egy rekordot a zóna tetején található rekordokra való (ebben az esetben "contoso.com"), használja a "\@", az idézőjelekkel együtt:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>A DNS-rekordhalmaz létrehozása

A fenti példákban a DNS-rekord vagy hozzáadni egy meglévő rekordhalmazhoz vagy a beállított rekord létrehozásának *implicit módon*. A rekordhalmaz is létrehozhat *explicit módon* rekordok hozzáadása előtt. Az Azure DNS támogatja a "empty" rekordhalmazok, amely működhet-e fenn a DNS-név DNS-rekordok létrehozása előtt helyettesíti. Üres rekordhalmazok az Azure DNS vezérlősík láthatók, de nem jelennek meg az Azure DNS névkiszolgálóit.

Rekordhalmazok használatával jön létre a `az network dns record-set <record-type> create` parancsot. További segítségért lásd: `az network dns record-set <record-type> create --help`.

Explicit módon a rekordhalmaz létrehozása lehetővé teszi annak megadását, a rekordhalmaz tulajdonságai például a [idő – Élettartam (TTL)](dns-zones-records.md#time-to-live) és azok metaadatait. [A rekordhalmaz metaadatainak](dns-zones-records.md#tags-and-metadata) minden rekordhalmaz kulcs-érték párok, alkalmazás-specifikus adatok társítása is használható.

Az alábbi példa létrehoz egy üres rekordhalmaz 60 másodperc TTL, "A" típusú használatával a `--ttl` paraméter (krátká forma `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Az alábbi példa létrehoz egy rekordot a két metaadat-bejegyzéseket, "részleg = pénzügyi" és "környezet éles =", használatával a `--metadata` paramétert:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Létrehozott egy üres rekordhalmaz kellene rekordokat is hozzáadhatók használatával `azure network dns record-set <record-type> add-record` leírtak szerint [DNS-rekord létrehozása](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Kellene látható részletesebben "A" rekordok létrehozása, a következő példák bemutatják az Azure DNS által támogatott további rekordtípusok rekord létrehozása.

A rekordadatok megadásához használt paraméterek a rekord típusától függnek. Az „A” típusú rekordok esetén például a `--ipv4-address <IPv4 address>` paraméterrel lehet megadni az IPv4-címet. Az egyes rekordtípusokra a paraméterek használatával is megadható `az network dns record-set <record-type> add-record --help`.

Minden esetben bemutatjuk, hogyan hozhat létre egy rekordot. A bejegyzés kerül a meglévő rekordhalmazt, vagy implicit módon létrehozott rekordhalmaz. További tájékoztatást a rekordhalmazok létrehozásához, és bejegyzést meghatározó paraméter explicit módon, olvassa el [hozzon létre egy DNS-rekordhalmaz](#create-a-dns-record-set).

Nem biztosítunk lehet például egy SOA típusú rekordhalmaz létrehozása, mivel SOAs jönnek létre, és törölni minden DNS-zónát, és nem hozható létre vagy külön-külön törölve. Azonban [módosíthatja a SOA típusú, újabb példában látható módon](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Hozzon létre egy AAAA típusú rekordot

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>A CAA-rekord létrehozása

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Hozzon létre egy CNAME-rekordot

> [!NOTE]
> A DNS-szabványok nem engedélyeznek CNAME-rekordokat a zóna tetején található rekordokra (`--Name "@"`), és nem teszik egynél több rekordot tartalmazó rekordhalmazok.
> 
> További információkért lásd: [CNAME-rekordokat](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Az MX-rekord létrehozása

Ebben a példában a rekordhalmaznevet használjuk "\@" hozhat létre az MX-rekord a zóna legfelső pontján (ebben az esetben "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS-rekord létrehozása

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>PTR típusú rekord létrehozása

Ebben az esetben "my-arpa-zone.com" a az IP-címtartományt képviselő ARPA-zónát jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg.  A rekord neve "10" az utolsó oktettet IP-cím az IP-címtartomány, ez a bejegyzés által képviselt belül.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Hozzon létre egy SRV-rekordot

Létrehozásakor egy [SRV rekordhalmaz](dns-zones-records.md#srv-records), adja meg a  *\_szolgáltatás* és  *\_protokoll* a rekordhalmaz-neve. Nem kell felvenni "\@" a rekordhalmaz nevét, ha létrehozása egy SRV-rekordot a zóna legfelső pontján.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Hozzon létre egy txt típusú rekordot

Az alábbi példa bemutatja, hogyan hozhat létre egy txt típusú rekordot. További információ a támogatott txt típusú rekordok a karakterlánc maximális hossza: [txt típusú rekordok](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Rekordhalmaz beolvasása

Egy meglévő rekordhalmaz kérheti `az network dns record-set <record-type> show`. További segítségért lásd: `az network dns record-set <record-type> show --help`.

Egy rekord vagy egy rekordhalmaz létrehozásakor, a rekordhalmaz nevének megadott kell lenniük egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét. Emellett szüksége határozza meg a rekord típusát, a zóna, a rekordhalmaz és az erőforráscsoport, amely tartalmazza a zóna tartalmazó.

Az alábbi példa lekéri a rekord *www* , adjon meg egy zónából *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lista rekordhalmazok

Használatával listázhatja az összes rekordot a DNS-zóna az `az network dns record-set list` parancsot. További segítségért lásd: `az network dns record-set list --help`.

Ebben a példában adja vissza az összes rekordot a zónában beállítja *contoso.com*, erőforráscsoportban *MyResourceGroup*, nevét és a rekordtípus függetlenül:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Ebben a példában minden rekordhalmazok, amelyek megfelelnek a megadott rekordtípus (ebben az esetben az "A" rekordok) adja vissza:

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adjon hozzá egy rekordot egy meglévő rekordhalmazhoz

Használhat `az network dns record-set <record-type> add-record` mindkét rekordot hozhat létre egy új rekordhalmazt, vagy adjon hozzá egy rekordot egy meglévő rekordhalmaz.

További információkért lásd: [DNS-rekord létrehozása](#create-a-dns-record) és [más típusú rekordok létrehozása](#create-records-of-other-types) felett.

## <a name="remove-a-record-from-an-existing-record-set"></a>Távolítsa el egy rekordot egy meglévő rekordhalmazt.

Egy meglévő rekordhalmaz DNS-rekord eltávolításához használja `az network dns record-set <record-type> remove-record`. További segítségért lásd: `az network dns record-set <record-type> remove-record -h`.

Ez a parancs egy DNS-rekord törlése egy rekordhalmaz. A legutóbbi bejegyzést a rekordhalmaz törlése esetén a rekordot maga is törlődik. Tartsa meg helyette üres rekordhalmazt, használja a `--keep-empty-record-set` lehetőséget.

Meg kell adni a törölni kívánt rekord, és a zóna törölni kell, ugyanazokat a paramétereket, használatával, egy a rekord létrehozásakor `az network dns record-set <record-type> add-record`. Ezek a paraméterek leírását [DNS-rekord létrehozása](#create-a-dns-record) és [más típusú rekordok létrehozása](#create-records-of-other-types) felett.

A következő példa törli az A rekord "1.2.3.4" a rekordból nevű értékkel *www* zónában *contoso.com*, az erőforráscsoport *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Módosíthatja egy meglévő rekordhalmaz

Minden rekordhalmaz tartalmaz egy [idő – Élettartam (TTL)](dns-zones-records.md#time-to-live), [metaadatok](dns-zones-records.md#tags-and-metadata), és a DNS-rekordjait. Az alábbi szakaszok azt ismertetik, hogyan módosíthatja a ezeket a tulajdonságokat.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Egy A, AAAA, CAA, MX, NS, PTR, SRV és txt típusú rekord módosítása

Egy már létező rekord nevével írja be A, AAAA, CAA, MX, NS, PTR, SRV vagy TXT módosításához kell először adjon hozzá egy új rekordot, és ezután törölje a meglévő rekord. A Törlés és a rekordok hozzáadása részletes utasításokért lásd: Ez a cikk korábbi szakaszait.

Az alábbi példa bemutatja, hogyan lehet módosítani az "A" rekord, IP-címről 1.2.3.4 5.6.7.8 IP-címre:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nem hozzáadása, eltávolítása vagy módosítása a rekordokat az automatikusan létrehozott NS-rekord a zóna legfelső pontján állítsa (`--Name "@"`, ajánlat együtt). A rekordhalmaz az engedélyezett csak történt változások, a rekord módosításához állítsa a TTL és azok metaadatait.

### <a name="to-modify-a-cname-record"></a>Egy CNAME rekord módosítása

Ellentétben a legtöbb más rekordtípusok egy CNAME-rekordhalmazok csak egy rekordot tartalmazhatnak.  Új rekord hozzáadásával és eltávolításával a meglévő rekord, mint a többi rekordtípusokat, ezért nem cserélje le a jelenlegi érték.

Ehelyett egy CNAME rekord módosításához használja `az network dns record-set cname set-record`. További segítségért lásd: `az network dns record-set cname set-record --help`

A példa módosítja a beállított CNAME rekord *www* zónában *contoso.com*, erőforráscsoportban *MyResourceGroup*, mutasson az "www.fabrikam.net" helyett a meglévő érték:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>SOA-rekord módosítása

Ellentétben a legtöbb más rekordtípusok egy CNAME-rekordhalmazok csak egy rekordot tartalmazhatnak.  Új rekord hozzáadásával és eltávolításával a meglévő rekord, mint a többi rekordtípusokat, ezért nem cserélje le a jelenlegi érték.

A SOA típusú rekordját módosításához használjon inkább `az network dns record-set soa update`. További segítségért lásd: `az network dns record-set soa update --help`.

Az alábbi példa bemutatja, hogyan állítsa be a "e-mail" tulajdonságot a SOA típusú rekord a zóna *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Módosíthatja a Névkiszolgálói rekordokat a zóna legfelső pontján

Az NS-rekord a zóna legfelső pontján állítsa be a rendszer automatikusan létrehoz minden DNS-zónát. Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza.

Hozzáadhat további névhez e NS-rekord-kiszolgálókat, támogatja a közös üzemeltetési tartomány több DNS-szolgáltatónál. Az élettartam és a rekordhalmaz metaadatait is módosíthatja. Azonban nem távolítsa el vagy módosítsa az ki van töltve az Azure DNS névkiszolgálóit.

Vegye figyelembe, hogy ez csak érvényes az NS-rekord a zóna legfelső pontján állítsa be. Más Névkiszolgálói rekordhalmazt a zónában (a használt gyermek zónák delegálása) korlátozás nélkül módosíthatók.

Az alábbi példa bemutatja, hogyan az NS-rekord a zóna legfelső pontján állítsa be a további neve kiszolgáló hozzáadása:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Az élettartam egy meglévő rekordhalmazra módosítása

Az élettartam egy meglévő rekordhalmazra módosításához használjon `azure network dns record-set <record-type> update`. További segítségért lásd: `azure network dns record-set <record-type> update --help`.

Az alábbi példa egy rekordhalmazra TTL, ebben az esetben módosítani szeretne 60 másodperc mutatja be:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Módosíthatja egy meglévő rekordhalmaz metaadatait

[A rekordhalmaz metaadatainak](dns-zones-records.md#tags-and-metadata) minden rekordhalmaz kulcs-érték párok, alkalmazás-specifikus adatok társítása is használható. Egy meglévő rekordhalmaz metaadatait módosításához használjon `az network dns record-set <record-type> update`. További segítségért lásd: `az network dns record-set <record-type> update --help`.

Az alábbi példa bemutatja, hogyan lehet módosítani egy rekordot a két metaadat-bejegyzéseket, "részleg = pénzügyi" és "környezet éles =". Vegye figyelembe, hogy a meglévő metaadatokat *cserélni* megadott értékekkel.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>A rekordhalmaz törlése

Rekordhalmazok használatával törölhetők a `az network dns record-set <record-type> delete` parancsot. További segítségért lásd: `azure network dns record-set <record-type> delete --help`. Rekordhalmaz törlése is törli a beállított rekord összes rekordján.

> [!NOTE]
> Nem lehet törölni a SOA és NS-rekord zóna felső pontjánál állítja be (`--name "@"`).  Ezek automatikusan jönnek létre a zónát amikor lett létrehozva, és a zóna törlésekor automatikusan törlődnek.

Az alábbi példával törölhet nevű rekordhalmaz *www* , adja meg a zónából *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

A törlési művelet megerősítését kéri. Ez a kérdés mellőzése, használja a `--yes` váltani.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [zónák és -rekordok az Azure DNS](dns-zones-records.md).
<br>
Ismerje meg, hogyan [a zónák és -rekordok védelme](dns-protect-zones-recordsets.md) Azure DNS használata esetén.
