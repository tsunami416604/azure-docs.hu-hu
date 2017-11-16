---
title: "Az Azure DNS az Azure CLI 2.0 használatával DNS-rekordok kezelése |} Microsoft Docs"
description: "DNS-rekordhalmazok és az Azure DNS-rekordok kezelése esetén az Azure DNS-tartomány. Minden CLI 2.0 parancsok rekordhalmazokat és rekordokat műveleteket."
services: dns
documentationcenter: na
author: subsarma
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: subsarma
ms.openlocfilehash: 47be36aee053b81913286f0119edb6c8caa7c456
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli-20"></a>DNS-rekordok és az az Azure CLI 2.0 verziót használja Azure DNS rekordhalmazok kezelése

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan DNS-rekordokat a DNS-zóna kezelése a platformok közötti Azure parancssori felület (CLI) 2.0-s, amelyik elérhető a Windows, Mac és Linux használatával. A DNS-rekordok segítségével is kezelheti [Azure PowerShell](dns-operations-recordsets.md) vagy a [Azure-portálon](dns-operations-recordsets-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók

A következő CLI-verziók egyikével elvégezheti a feladatot:

* [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez.
* [Azure CLI 2.0](dns-operations-recordsets-cli.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

Ebben a cikkben szereplő példák azt feltételezik, hogy már rendelkezik [jelentkezik be, az Azure CLI 2.0 és a DNS-zóna létrehozásakor](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekord létrehozásához használja a `az network dns record-set <record-type> set-record` parancs (ahol `<record-type>` rekord, ilyen típusú Egytényezős egy, srv, txt, stb.) További segítségért lásd: `az network dns record-set --help`.

Egy rekord létrehozásakor meg kell adni az erőforráscsoport, a zóna és a rekordhalmaz nevét, a rekordtípust és a létrehozandó rekord részletes adatait. A megadott rekordhalmaz nevének kell lennie egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét.

Ha a rekordhalmaz még nem létezik, akkor a parancs létrehozza. Ha az adott rekordhalmaz már létezik, a parancs felveszi a megadott rekordot a meglévő rekordhalmazba.

Új rekordhalmaz létrehozásakor az alapértelmezett élettartam (time-to-live, TTL) értéke 3600 lesz. Különböző TTLs használatával, lásd: [DNS rekordhalmaz létrehozása](#create-a-dns-record-set).

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoport *contoso.com* zónájában egy *www* nevű, „A” típusú rekordot hoz létre. Az „A” rekord IP-címe: *1.2.3.4*.

```azurecli
az network dns record-set a set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Ha a zóna (ebben az esetben a „contoso.com”) legfelső pontján szeretne létrehozni egy rekordot, használja a „@” rekordnevet (az idézőjelekkel együtt):

```azurecli
az network dns record-set a set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>A DNS-rekordhalmaz létrehozása

A fenti példák a DNS-rekord vagy adtak hozzá egy meglévő rekordhalmaz, vagy a rekordhalmaz létrehozásának *implicit módon*. A rekordhalmaz is létrehozhat *explicit módon* rekordok hozzáadása előtt. Az Azure DNS támogatja az "empty" rekordhalmazok, amely működhet, és egy helyőrző lefoglalhat egy DNS-nevet a DNS-rekordok létrehozása előtt. Üres rekordhalmazok az Azure DNS-vezérlő vezérlősík látható, de nem jelennek meg az Azure DNS névkiszolgálóit.

Rekordhalmazok használatával hozhatók létre a `az network dns record-set <record-type> create` parancsot. További segítségért lásd: `az network dns record-set <record-type> create --help`.

Explicit módon a rekordhalmaz létrehozása lehetővé teszi annak megadását, a rekordhalmaz tulajdonságok, mint a [idő-Élettartam (TTL)](dns-zones-records.md#time-to-live) és metaadatokat. [A rekordhalmaz metaadatok](dns-zones-records.md#tags-and-metadata) rendelje hozzá az alkalmazás-specifikus adatok minden rekordhalmaz, a kulcs-érték párként használható.

A következő példa egy üres rekordhalmaz 60 másodperces TTL, "A" típusú használatával hozza létre a `--ttl` paraméter (rövid alak `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Az alábbi példa létrehoz két metaadat-bejegyzést, rekordkészlet "osztály pénzügyi =" és "környezet éles =", segítségével a `--metadata` paraméter:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Egy üres rekordhalmaz hozunk létre, rekordok segítségével adhatók `azure network dns record-set <record-type> set-record` leírtak [hozzon létre egy DNS-rekord](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Hogy látott részletesen "A" rekordok létrehozása, a következő példák szemléltetik Azure DNS által támogatott más rekord típusú rekordot kell létrehozni.

A rekordadatok megadásához használt paraméterek a rekord típusától függnek. Az „A” típusú rekordok esetén például a `--ipv4-address <IPv4 address>` paraméterrel lehet megadni az IPv4-címet. A különböző rekordtípusú paramétereinek is listázva lehet használatával `az network dns record-set <record-type> set-record --help`.

Minden esetben azt mutatják be egyetlen rekordot kell létrehozni. A bejegyzés kerül a meglévő rekordkészlet vagy implicit módon létrehozott rekordhalmaz. További információ a rekordhalmazok létrehozásához, és definiáló rekordot paraméter explicit módon, olvassa el [DNS rekordhalmaz létrehozása](#create-a-dns-record-set).

Egy példa egy SOA típusú rekordhalmaz létrehozása óta SOAs jönnek létre nem felállításához és minden DNS-zóna törlődik, és nem hozható létre vagy törölt külön-külön. Azonban [módosíthatják a SOA, egy újabb példában látható módon](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Az AAAA-rekord létrehozása

```azurecli
az network dns record-set aaaa set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Hozzon létre egy CAA rekordot

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Hozzon létre egy CNAME rekordot

> [!NOTE]
> A DNS-szabványokból nem teszik lehetővé a CNAME rekordot a zóna tetején (`--Name "@"`), és nem teszik egynél több rekordot tartalmazó rekordhalmazok.
> 
> További információkért lásd: [CNAME rekordok](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Az MX-rekord létrehozása

Ebben a példában a „@” rekordhalmaznevet használjuk az MX-rekord zóna felső pontjánál történő létrehozásához (amely ebben az esetben: contoso.com).

```azurecli
az network dns record-set mx set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS-rekord létrehozása

```azurecli
az network dns record-set ns set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>A PTR típusú rekord létrehozása

Ebben az esetben "my-arpa-zónában (Zone.com)" a ARPA zóna az IP-címtartomány képviselő jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg.  A rekordnév a "10" Ez a bejegyzés által képviselt IP-címtartományon belül az IP-cím utolsó oktett.

```azurecli
az network dns record-set ptr set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Az SRV rekord létrehozása

Létrehozásakor egy [SRV-rekordhalmaz](dns-zones-records.md#srv-records), adja meg a  *\_szolgáltatás* és  *\_protokoll* a rekordhalmaz-neve. Nincs szükség közé tartoznak a "@" a rekordhalmaz nevében, ha a zóna felső pontja az SRV rekord létrehozása beállítva.

```azurecli
az network dns record-set srv set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>TXT-rekord létrehozása

A következő példa bemutatja, hogyan TXT rekord létrehozásához. A maximális hossz támogatja a TXT-rekord kapcsolatos további információkért lásd: [TXT rekord](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Rekordkészlet beolvasása

Egy meglévő rekordhalmaz lekéréséhez használja `az network dns record-set <record-type> show`. További segítségért lásd: `az network dns record-set <record-type> show --help`.

Rekord vagy rekordhalmaz létrehozása, ha a megadott rekordhalmaz nevének kell lennie egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét. Meg kell adnia a rekordtípust, a rekordhalmaz és a zóna tartalmazó erőforráscsoportot tartalmazó zóna is.

A következő példa a rekordot be *www* , adjon meg egy zónából *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lista rekordhalmazok

Minden rekordot a DNS-zónák használatával listázhatja a `az network dns record-set list` parancsot. További segítségért lásd: `az network dns record-set list --help`.

Ez a példa adja vissza az összes rekordot a zónában beállítja *contoso.com*, erőforráscsoportban *MyResourceGroup*, név vagy rekordtípus függetlenül:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Ebben a példában, amelyek megfelelnek a megadott rekordtípus (ebben az esetben az "A" rekordok) minden rekordkészletet ad vissza:

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Rekord hozzáadása egy meglévő rekordkészlete

Használhat `az network dns record-set <record-type> set-record` mindkét új Rekordkészletben lévő rekordot kell létrehozni vagy hozzáadni egy rekordot egy meglévő rekordhalmaz.

További információkért lásd: [hozzon létre egy DNS-rekord](#create-a-dns-record) és [más típusú rekordok létrehozásának](#create-records-of-other-types) felett.

## <a name="remove-a-record-from-an-existing-record-set"></a>Távolítsa el a rekord egy meglévő rekordhalmaz.

Egy meglévő rekordkészlet a DNS-rekord eltávolításához használja `az network dns record-set <record-type> remove-record`. További segítségért lásd: `az network dns record-set <record-type> remove-record -h`.

Törli a DNS-rekordot a rekordhalmaz. A utolsó rekordját a rekordhalmaz törlése, a rekordhalmaz maga is törlődik. Helyette üres rekordhalmaz megtartásához használni a `--keep-empty-record-set` lehetőséget.

Meg kell adni a törölni kívánt rekordot, és a zóna törölni kell, a telepítést, azonos paraméterekkel, egy rekord segítségével létrehozásakor `az network dns record-set <record-type> set-record`. Ezek a paraméterek ismertetett [DNS-rekord létrehozása](#create-a-dns-record) és [más típusú rekordok létrehozásának](#create-records-of-other-types) fent.

A következő példa törli az A rekordot "1.2.3.4" a rekordból beállítása a nevesített értékű *www* a zónában *contoso.com*, erőforráscsoportban *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Módosíthatja egy meglévő rekordkészlete

Minden rekordhalmaz tartalmaz egy [idő-Élettartam (TTL)](dns-zones-records.md#time-to-live), [metaadatok](dns-zones-records.md#tags-and-metadata), és a DNS-rekordokat. Az alábbi szakaszok ismertetik, hogyan módosíthatja a ezeket a tulajdonságokat.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>A, AAAA, CAA, MX, NS, PTR, SRV és TXT-rekord módosítása

Adjon meg egy, AAAA, CAA, MX, NS, PTR, SRV és TXT meglévő bejegyzés módosításához kell először adjon hozzá egy új rekordot, és ezután törölje a meglévő. Részletes útmutatást a Törlés és rekordok hozzáadásához Ez a cikk a korábbi szakaszban talál.

A következő példa bemutatja, hogyan lehet módosítani az "A" rekord, a következő IP-1.2.3.4 5.6.7.8 IP-cím:

```azurecli
az network dns record-set a set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nem hozzáadása, eltávolítása vagy módosítása a rekordokat az automatikusan létrehozott NS-rekord a zóna felső pontja beállított (`--Name "@"`, beleértve az ajánlat jelek). A rekordhalmaz a megengedett csak módosítások, a rekord módosítása beállítva a TTL-t és a metaadatok.

### <a name="to-modify-a-cname-record"></a>Egy CNAME rekord módosítása

Ellentétben a legtöbb más rekordtípusokhoz egy olyan CNAME-rekordhalmazt csak egyetlen rekordot tartalmazhat.  Felvesz egy új bejegyzést, és a meglévő rekord, mint a más rekordtípusokhoz eltávolításával, ezért nem cserélhető le a jelenlegi érték.

Ehelyett egy olyan CNAME rekordot módosításához használható `az network dns record-set cname set-record`. Útmutatásért lásd:`az network dns record-set cname set-record --help`

A példa módosítja a CNAME rekord *www* a zónában *contoso.com*, erőforráscsoportban *MyResourceGroup*, mutasson a "www.fabrikam.net" helyett a meglévő értéket:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Egy SOA-rekord módosítása

Ellentétben a legtöbb más rekordtípusokhoz egy olyan CNAME-rekordhalmazt csak egyetlen rekordot tartalmazhat.  Felvesz egy új bejegyzést, és a meglévő rekord, mint a más rekordtípusokhoz eltávolításával, ezért nem cserélhető le a jelenlegi érték.

A SOA-rekord módosítása, inkább `az network dns record-set soa update`. További segítségért lásd: `az network dns record-set soa update --help`.

A következő példa bemutatja, hogyan állítsa be a "e-mail" tulajdonságot a SOA-rekord a zóna *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>A zóna felső pontja a Névkiszolgálói rekordok módosítása

Állítsa be a zóna felső pontja NS-rekord automatikusan létrejön minden DNS-zóna. Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza.

Hozzáadhat további névhez a kiszolgálók e NS-rekord, támogatja a párhuzamos üzemeltetési tartományok egynél több DNS-szolgáltatónál. A TTL-t és a metaadatok a rekordhalmaz is módosíthatók. Azonban nem lehet eltávolítani, vagy módosítsa az előre megadott Azure DNS névkiszolgálóit.

Vegye figyelembe, hogy csak vonatkozik az NS típusú rekordhalmaz zóna tetején. A zónában (a használt gyermekzónákhoz delegálása) más NS-rekordhalmazok lehet módosítani, korlátozás nélkül.

A következő példa bemutatja, hogyan hozzáadása egy további nevét, a zóna felső pontja Névkiszolgálói rekordhalmazt:

```azurecli
az network dns record-set ns set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>A TTL-t egy meglévő rekordkészlet módosítása

A TTL-t egy meglévő rekordkészlet módosításához használható `azure network dns record-set <record-type> update`. További segítségért lásd: `azure network dns record-set <record-type> update --help`.

A következő példa bemutatja, hogyan lehet módosítani egy rekordhalmaz TTL, ebben az esetben 60 másodperc:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Egy meglévő rekordhalmaz metaadatainak módosítása

[A rekordhalmaz metaadatok](dns-zones-records.md#tags-and-metadata) rendelje hozzá az alkalmazás-specifikus adatok minden rekordhalmaz, a kulcs-érték párként használható. A metaadatok egy meglévő rekordkészlet módosításához használható `az network dns record-set <record-type> update`. További segítségért lásd: `az network dns record-set <record-type> update --help`.

A következő példa bemutatja, hogyan lehet módosítani egy rekordot a két metaadat-bejegyzést, "osztály pénzügyi =" és "környezet éles =". Vegye figyelembe, hogy a meglévő metaadatokat *helyett* által megadott értéket.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>A rekordhalmaz törlése

Rekordhalmazok használatával törölheti a `az network dns record-set <record-type> delete` parancsot. További segítségért lásd: `azure network dns record-set <record-type> delete --help`. A rekordhalmaz összes rekordján is rekordhalmaz törlése törli.

> [!NOTE]
> Nem lehet törölni a SOA és NS-rekord beállítja a zóna felső pontja (`--name "@"`).  Ezek automatikusan létrejönnek a zóna mikor jött létre, és a zóna törlésekor automatikusan törlődik.

A következő példa törli a rekordhalmaz elnevezett *www* típus a zónából származó *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

A delete művelet megerősítését kéri. Ne jelenjen meg többé ez a kérdés, használja a `--yes` váltani.

## <a name="next-steps"></a>Következő lépések

További információ [zónák és az Azure DNS-rekordok](dns-zones-records.md).
<br>
Megtudhatja, hogyan [védelme a zóna és a rekordok](dns-protect-zones-recordsets.md) Azure DNS használata esetén.
