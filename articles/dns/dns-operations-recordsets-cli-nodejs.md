---
title: "Az Azure DNS az Azure CLI 1.0 használata DNS-rekordok kezelése |} Microsoft Docs"
description: "DNS-rekordhalmazok és az Azure DNS-rekordok kezelése esetén az Azure DNS-tartomány. Minden CLI 1.0 parancsok rekordhalmazokat és rekordokat műveleteket."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: jonatul
ms.openlocfilehash: 307b327e4c04a0461e39930114eb193791cbda9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>Az Azure DNS az Azure CLI 1.0 használata DNS-rekordok kezelése

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan DNS-rekordokat a DNS-zóna kezelése a platformok közötti Azure parancssori felület (CLI), amely megtalálható a Windows, Mac és Linux használatával. A DNS-rekordok segítségével is kezelheti [Azure PowerShell](dns-operations-recordsets.md) vagy a [Azure-portálon](dns-operations-recordsets-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók

A következő CLI-verziók egyikével elvégezheti a feladatot:

* [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez.
* [Azure CLI 2.0](dns-operations-recordsets-cli.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

Ebben a cikkben szereplő példák azt feltételezik, hogy már rendelkezik [az Azure CLI 1.0-s jelentkezik be, és a DNS-zóna létrehozásakor](dns-operations-dnszones-cli-nodejs.md).

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekordokat az `azure network dns record-set add-record` paranccsal lehet létrehozni. További segítségért lásd: `azure network dns record-set add-record -h`.

Egy rekord létrehozásakor meg kell adni az erőforráscsoport, a zóna és a rekordhalmaz nevét, a rekordtípust és a létrehozandó rekord részletes adatait. A megadott rekordhalmaz nevének kell lennie egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét.

Ha a rekordhalmaz még nem létezik, akkor a parancs létrehozza. Ha a rekordkészlet már létezik, a parancs adda a meg kell adnia a meglévő rekord rekordhalmaz.

Új rekordhalmaz létrehozásakor az alapértelmezett élettartam (time-to-live, TTL) értéke 3600 lesz. Különböző TTLs használatával, lásd: [DNS rekordhalmaz létrehozása](#create-a-dns-record-set).

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoport *contoso.com* zónájában egy *www* nevű, „A” típusú rekordot hoz létre. Az „A” rekord IP-címe: *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

A zóna tetején a rekord létrehozásához (ebben az esetben "a contoso.com"), használja az "@", idézőjelekkel együtt:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>A DNS-rekordhalmaz létrehozása

A fenti példák a DNS-rekord vagy adtak hozzá egy meglévő rekordhalmaz, vagy a rekordhalmaz létrehozásának *implicit módon*. A rekordhalmaz is létrehozhat *explicit módon* rekordok hozzáadása előtt. Az Azure DNS támogatja az "empty" rekordhalmazok, amely működhet, és egy helyőrző lefoglalhat egy DNS-nevet a DNS-rekordok létrehozása előtt. Üres rekordhalmazok az Azure DNS-vezérlő vezérlősík látható, de nem jelennek meg az Azure DNS névkiszolgálóit.

Rekordhalmazok használatával hozhatók létre a `azure network dns record-set create` parancsot. További segítségért lásd: `azure network dns record-set create -h`.

Explicit módon a rekordhalmaz létrehozása lehetővé teszi annak megadását, a rekordhalmaz tulajdonságok, mint a [idő-Élettartam (TTL)](dns-zones-records.md#time-to-live) és metaadatokat. [A rekordhalmaz metaadatok](dns-zones-records.md#tags-and-metadata) rendelje hozzá az alkalmazás-specifikus adatok minden rekordhalmaz, a kulcs-érték párként használható.

Az alábbi példa létrehoz egy üres rekordhalmaz 60 másodperces TTL, segítségével a `--ttl` paraméter (rövid alak `-l`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

Az alábbi példa létrehoz két metaadat-bejegyzést, rekordkészlet "osztály pénzügyi =" és "környezet éles =", segítségével a `--metadata` paraméter (rövid alak `-m`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

Egy üres rekordhalmaz hozunk létre, rekordok segítségével adhatók `azure network dns record-set add-record` leírtak [hozzon létre egy DNS-rekord](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Hogy látott részletesen "A" rekordok létrehozása, a következő példák szemléltetik Azure DNS által támogatott más rekord típusú rekordot kell létrehozni.

A rekordadatok megadásához használt paraméterek a rekord típusától függnek. Az „A” típusú rekordok esetén például a `-a <IPv4 address>` paraméterrel lehet megadni az IPv4-címet. A különböző rekordtípusú paramétereinek is listázva lehet használatával `azure network dns record-set add-record -h`.

Minden esetben azt mutatják be egyetlen rekordot kell létrehozni. A bejegyzés kerül a meglévő rekordkészlet vagy implicit módon létrehozott rekordhalmaz. További információ a rekordhalmazok létrehozásához, és definiáló rekordot paraméter explicit módon, olvassa el [DNS rekordhalmaz létrehozása](#create-a-dns-record-set).

Egy példa egy SOA típusú rekordhalmaz létrehozása óta SOAs jönnek létre nem felállításához és minden DNS-zóna törlődik, és nem hozható létre vagy törölt külön-külön. Azonban [módosíthatják a SOA, egy újabb példában látható módon](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Az AAAA-rekord létrehozása

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-cname-record"></a>Hozzon létre egy CNAME rekordot

> [!NOTE]
> A DNS-szabványokból nem teszik lehetővé a CNAME rekordot a zóna tetején (`-Name "@"`), és nem teszik egynél több rekordot tartalmazó rekordhalmazok.
> 
> További információkért lásd: [CNAME rekordok](dns-zones-records.md#cname-records).

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Az MX-rekord létrehozása

Ebben a példában a „@” rekordhalmaznevet használjuk az MX-rekord zóna felső pontjánál történő létrehozásához (amely ebben az esetben: contoso.com).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS-rekord létrehozása

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>A PTR típusú rekord létrehozása

Ebben az esetben "my-arpa-zónában (Zone.com)" a ARPA zóna az IP-címtartomány képviselő jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg.  A rekordnév a "10" Ez a bejegyzés által képviselt IP-címtartományon belül az IP-cím utolsó oktett.

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>Az SRV rekord létrehozása

Létrehozásakor egy [SRV-rekordhalmaz](dns-zones-records.md#srv-records), adja meg a  *\_szolgáltatás* és  *\_protokoll* a rekordhalmaz-neve. Nincs szükség közé tartoznak a "@" a rekordhalmaz nevében, ha a zóna felső pontja az SRV rekord létrehozása beállítva.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>TXT-rekord létrehozása

A következő példa bemutatja, hogyan TXT rekord létrehozásához. A maximális hossz támogatja a TXT-rekord kapcsolatos további információkért lásd: [TXT rekord](dns-zones-records.md#txt-records).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>Rekordkészlet beolvasása

Egy meglévő rekordhalmaz lekéréséhez használja `azure network dns record-set show`. További segítségért lásd: `azure network dns record-set show -h`.

Rekord vagy rekordhalmaz létrehozása, ha a megadott rekordhalmaz nevének kell lennie egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét. Meg kell adnia a rekordtípust, a rekordhalmaz és a zóna tartalmazó erőforráscsoportot tartalmazó zóna is.

A következő példa a rekordot be *www* , adjon meg egy zónából *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>Lista rekordhalmazok

Minden rekordot a DNS-zónák használatával listázhatja a `azure network dns record-set list` parancsot. További segítségért lásd: `azure network dns record-set list -h`.

Ez a példa adja vissza az összes rekordot a zónában beállítja *contoso.com*, erőforráscsoportban *MyResourceGroup*, név vagy rekordtípus függetlenül:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

Ebben a példában, amelyek megfelelnek a megadott rekordtípus (ebben az esetben az "A" rekordok) minden rekordkészletet ad vissza:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>Rekord hozzáadása egy meglévő rekordkészlete

Használhat `azure network dns record-set add-record` mindkét új Rekordkészletben lévő rekordot kell létrehozni vagy hozzáadni egy rekordot egy meglévő rekordhalmaz.

További információkért lásd: [hozzon létre egy DNS-rekord](#create-a-dns-record) és [más típusú rekordok létrehozásának](#create-records-of-other-types) felett.

## <a name="remove-a-record-from-an-existing-record-set"></a>Távolítsa el a rekord egy meglévő rekordhalmaz.

Egy meglévő rekordkészlet a DNS-rekord eltávolításához használja `azure network dns record-set delete-record`. További segítségért lásd: `azure network dns record-set delete-record -h`.

Törli a DNS-rekordot a rekordhalmaz. A utolsó rekordját a rekordhalmaz törlése, ha van-e a rekordhalmaz maga **nem** törölték. Ehelyett egy üres rekordhalmaz marad. Ehelyett a rekordhalmaz törlése, lásd: [rekordhalmaz törlése](#delete-a-record-set).

Meg kell adni a törölni kívánt rekordot, és a zóna törölni kell, a telepítést, azonos paraméterekkel, egy rekord segítségével létrehozásakor `azure network dns record-set add-record`. Ezek a paraméterek ismertetett [DNS-rekord létrehozása](#create-a-dns-record) és [más típusú rekordok létrehozásának](#create-records-of-other-types) fent.

Ez a parancs felszólítja megerősítést kér. Ez a kérdés is letiltja, használja a `--quiet` váltás (rövid alak `-q`).

A következő példa törli az A rekordot "1.2.3.4" a rekordból beállítása a nevesített értékű *www* a zónában *contoso.com*, erőforráscsoportban *MyResourceGroup*. A megerősítést kérő le van tiltva.

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>Módosíthatja egy meglévő rekordkészlete

Minden rekordhalmaz tartalmaz egy [idő-Élettartam (TTL)](dns-zones-records.md#time-to-live), [metaadatok](dns-zones-records.md#tags-and-metadata), és a DNS-rekordokat. Az alábbi szakaszok ismertetik, hogyan módosíthatja a ezeket a tulajdonságokat.

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>A, AAAA, MX, NS, PTR, SRV és TXT-rekord módosítása

Adjon meg egy, AAAA, MX, NS, PTR, SRV és TXT meglévő bejegyzés módosításához kell először adjon hozzá egy új rekordot, és ezután törölje a meglévő. Részletes útmutatást a Törlés és rekordok hozzáadásához Ez a cikk a korábbi szakaszban talál.

A következő példa bemutatja, hogyan lehet módosítani az "A" rekord, a következő IP-1.2.3.4 5.6.7.8 IP-cím:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

### <a name="to-modify-a-cname-record"></a>Egy CNAME rekord módosítása

Egy olyan CNAME rekordot módosításához használható `azure network dns record-set add-record` hozzáadása az új rekord értéket. Egyéb típusú bejegyzés eltérően a CNAME rekordkészlete csak egyetlen rekordot tartalmazhat. A meglévő rekord ezért *helyett* amikor az új rekordban kerül, és nem kell külön-külön kell hagyni.  Kérni fogja a csere fogadásához.

A példa módosítja a CNAME rekord *www* a zónában *contoso.com*, erőforráscsoportban *MyResourceGroup*, mutasson a "www.fabrikam.net" helyett a meglévő értéket:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Egy SOA-rekord módosítása

Használjon `azure network dns record-set set-soa-record` módosítása a megadott DNS-zónák SOA. További segítségért lásd: `azure network dns record-set set-soa-record -h`.

A következő példa bemutatja, hogyan állítsa be a "e-mail" tulajdonságot a SOA-rekord a zóna *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```


### <a name="to-modify-ns-records-at-the-zone-apex"></a>A zóna felső pontja a Névkiszolgálói rekordok módosítása

Állítsa be a zóna felső pontja NS-rekord automatikusan létrejön minden DNS-zóna. Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza.

Hozzáadhat további névhez a kiszolgálók e NS-rekord, támogatja a párhuzamos üzemeltetési tartományok egynél több DNS-szolgáltatónál. A TTL-t és a metaadatok a rekordhalmaz is módosíthatók. Azonban nem lehet eltávolítani, vagy módosítsa az előre megadott Azure DNS névkiszolgálóit.

Vegye figyelembe, hogy csak vonatkozik az NS típusú rekordhalmaz zóna tetején. A zónában (a használt gyermekzónákhoz delegálása) más NS-rekordhalmazok lehet módosítani, korlátozás nélkül.

A következő példa bemutatja, hogyan hozzáadása egy további nevét, a zóna felső pontja Névkiszolgálói rekordhalmazt:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>A TTL-t egy meglévő rekordkészlet módosítása

A TTL-t egy meglévő rekordkészlet módosításához használható `azure network dns record-set set`. További segítségért lásd: `azure network dns record-set set -h`.

A következő példa bemutatja, hogyan lehet módosítani egy rekordhalmaz TTL, ebben az esetben 60 másodperc:

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Egy meglévő rekordhalmaz metaadatainak módosítása

[A rekordhalmaz metaadatok](dns-zones-records.md#tags-and-metadata) rendelje hozzá az alkalmazás-specifikus adatok minden rekordhalmaz, a kulcs-érték párként használható. A metaadatok egy meglévő rekordkészlet módosításához használható `azure network dns record-set set`. További segítségért lásd: `azure network dns record-set set -h`.

A következő példa bemutatja, hogyan lehet módosítani egy rekordot a két metaadat-bejegyzést, "osztály pénzügyi =" és "környezet éles =", segítségével a `--metadata` paraméter (rövid alak `-m`). Vegye figyelembe, hogy a meglévő metaadatokat *helyett* által megadott értéket.

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>A rekordhalmaz törlése

Rekordhalmazok használatával törölheti a `azure network dns record-set delete` parancsot. További segítségért lásd: `azure network dns record-set delete -h`. A rekordhalmaz összes rekordján is rekordhalmaz törlése törli.

> [!NOTE]
> Nem lehet törölni a SOA és NS-rekord beállítja a zóna felső pontja (`-Name "@"`).  Ezek automatikusan létrejönnek a zóna mikor jött létre, és a zóna törlésekor automatikusan törlődik.

A következő példa törli a rekordhalmaz elnevezett *www* típus a zónából származó *contoso.com* erőforráscsoportban *MyResourceGroup*:

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

A delete művelet megerősítését kéri. Ne jelenjen meg többé ez a kérdés, használja a `--quiet` váltás (rövid alak `-q`).

## <a name="next-steps"></a>Következő lépések

További információ [zónák és az Azure DNS-rekordok](dns-zones-records.md).
<br>
Megtudhatja, hogyan [védelme a zóna és a rekordok](dns-protect-zones-recordsets.md) Azure DNS használata esetén.
