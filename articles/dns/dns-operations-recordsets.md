---
title: DNS-rekordok kezelése az Azure DNS-ben az Azure PowerShell használatával | Microsoft dokumentumok
description: DNS-rekordkészletek és rekordok kezelése az Azure DNS-en, amikor a tartományt az Azure DNS-en üzemelteti. A rekordhalmazokon és rekordokon végzett műveletekhez szükséges összes PowerShell-parancs.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: b9244d9b2bdc9cb20195bbc103c0b1eb48a9de63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932536"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>DNS-rekordok és rekordkészletek kezelése az Azure DNS-ben az Azure PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure klasszikus parancssori felület](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-rekordok a DNS-zóna az Azure PowerShell használatával. A DNS-rekordok a platformfüggetlen [Azure CLI](dns-operations-recordsets-cli.md) vagy az Azure Portal használatával is [kezelhetők.](dns-operations-recordsets-portal.md)

A cikkben szereplő példák feltételezik, hogy már [telepítette az Azure PowerShellt, bejelentkezett, és létrehozott egy DNS-zónát.](dns-operations-dnszones.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.


## <a name="create-a-new-dns-record"></a>Új DNS-rekord létrehozása

Ha az új rekord neve és típusa megegyezik egy meglévő bejegyzés nevével, akkor hozzá kell [adnia a meglévő rekordkészlethez.](#add-a-record-to-an-existing-record-set) Ha az új rekord neve és típusa eltér az összes meglévő rekordtól, új rekordkészletet kell létrehoznia. 

### <a name="create-a-records-in-a-new-record-set"></a>"A" rekordok létrehozása új rekordkészletben

Rekordhalmazt a `New-AzDnsRecordSet` parancsmag használatával hozhat létre. Rekordkészlet létrehozásakor meg kell adnia a rekordhalmaz nevét, a zónát, az életidőt (TTL), a bejegyzéstípusát és a létrehozandó rekordokat.

A rekordok rekordhalmazhoz adásának paraméterei a rekordhalmaz típusától függően eltérnek. Ha például "A" típusú rekordhalmazt használ, az IP-címet `-IPv4Address`a paraméter rel kell megadnia. Más bejegyzéstípusokhoz más paraméterek et is használnak. A további bejegyzéstípus-példákat lásd a részletekért.

A következő példa létrehoz egy "www" relatív nevű rekordot a "contoso.com" DNS-zónában. A rekordkészlet teljesen minősített neve "www.contoso.com". A rekordtípus "A", a TTL pedig 3600 másodperc. A rekordkészlet egyetlen rekordot tartalmaz, "1.2.3.4" IP-címmel.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Ha egy zóna "csúcsán" (ebben az esetben "contoso.com") szeretne rekordhalmazt létrehozni, használja a rekordkészlet nevét '\@(idézőjelek nélkül):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Ha egynél több rekordot tartalmazó rekordot kell létrehoznia, először hozzon létre egy `New-AzDnsRecordSet` helyi tömböt, és adja hozzá a rekordokat, majd adja át a tömböt a következőknek:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[A rekordkészlet metaadatai](dns-zones-records.md#tags-and-metadata) segítségével alkalmazásspecifikus adatokat társíthat minden rekordkészlethez kulcsérték-párként. A következő példa bemutatja, hogyan hozhat létre rekordkészletet két metaadat-bejegyzéssel, az "dept=finance" és a "environment=production" bejegyzésekkel.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Az Azure DNS támogatja az "üres" rekordkészleteket is, amelyek helyőrzőként működhetnek a DNS-nevek lefoglalásához a DNS-rekordok létrehozása előtt. Üres rekordhalmazok láthatók az Azure DNS-vezérlő síkban, de az Azure DNS-névkiszolgálókon jelennek meg. A következő példa üres rekordkészletet hoz létre:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Az "A" rekordok létrehozásának részletes bemutatása után az alábbi példák bemutatják, hogyan hozhat létre az Azure DNS által támogatott más rekordtípusok rekordjait.

Minden esetben bemutatjuk, hogyan hozhat létre egyetlen rekordot tartalmazó rekordhalmazt. Az "A" rekordok korábbi példái módosíthatók úgy, hogy más típusú rekordhalmazokat hozzanak létre, amelyek több rekordot tartalmaznak, metaadatokkal, vagy üres rekordkészleteket hoznak létre.

Nem adunk példát SOA rekordkészlet létrehozására, mivel a SOA-k minden DNS-zónával jönnek létre és törlődnek, és nem hozhatók létre vagy törölhetők külön.We do not a example to create a SOA record set, since SOAs are created and deleted with each DNS zone and not be created or deleted separate ly. A [SOA azonban módosítható, amint az egy későbbi példában is látható.](#to-modify-an-soa-record)

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó AAAA típusú rekordhalmaz létrehozása

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>CAA rekordkészlet létrehozása egyetlen rekorddal

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó CNAME típusú rekordhalmaz létrehozása

> [!NOTE]
> A DNS-szabványok nem engedélyezik a CNAME rekordokat a zóna csúcsán (`-Name '@'`), és nem engedélyezik az egynél több rekordot tartalmazó rekordkészleteket sem.
> 
> További információ: [CNAME records](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó MX típusú rekordhalmaz létrehozása

Ebben a példában a rekordhalmaz\@nevét ' ' használjuk egy MX rekord létrehozásához a zóna csúcsán (ebben az esetben a "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó NS típusú rekordhalmaz létrehozása

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó PTR típusú rekordhalmaz létrehozása

Ebben az esetben a "my-arpa-zone.com" az Ön IP-tartományát képviselő ARPA névkeresési zónát jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg. A "10" rekordnév a rekord által képviselt IP-tartományon belüli IP-cím utolsó oktettje.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó SRV típusú rekordhalmaz létrehozása

[SRV rekordkészlet](dns-zones-records.md#srv-records)létrehozásakor adja meg a * \_szolgáltatást* és * \_* a protokollt a rekordhalmaz nevében. A zóna csúcsán beállított\@SRV rekordkészlet létrehozásakor nincs szükség ' ' ' felvételére a rekordhalmaz nevében.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>TXT rekordkészlet létrehozása egyetlen rekorddal

A következő példa bemutatja, hogyan hozhat létre TXT rekordot. A TXT-rekordokban támogatott maximális karakterlánchosszról a [TXT-rekordok című témakörben olvashat bővebben.](dns-zones-records.md#txt-records)

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Rekordkészlet beszereznie

Meglévő rekordkészlet beolvasásához `Get-AzDnsRecordSet`használja a használatát. Ez a parancsmag egy helyi objektumot ad vissza, amely az Azure DNS-ben beállított rekordot jelöli.

A `New-AzDnsRecordSet`mezőhöz is a megadott rekordhalmaz nevének *relatív* névnek kell lennie, ami azt jelenti, hogy ki kell zárnia a zónanevét. Meg kell adnia a bejegyzés típusát és a rekordhalmazt tartalmazó zónát is.

A következő példa bemutatja, hogyan lehet beolvasni egy rekordkészletet. Ebben a példában a zóna `-ZoneName` a `-ResourceGroupName` és a paraméterek használatával van megadva.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Azt is megteheti, hogy a zónát egy `-Zone` zónaobjektum mal adja meg, amelyet a paraméter rel adnak át.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Listarekord-készletek

A zóna `Get-AzDnsZone` rekordhalmazait is felsorolhatja, `-Name` ha kihagyja `-RecordType` az és/vagy paramétereket.

A következő példa a zónában lévő összes rekordhalmazt visszaadja:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

A következő példa bemutatja, hogyan lehet egy adott típusú rekordhalmazokat beolvasni a rekordtípus megadásával, a rekordkészlet nevének kihagyásával:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Az összes adott nevű rekordsorozat beolvasásához rekordtípusok között be kell olvasnia az összes rekordsorozatot, majd szűrnie kell az eredményeket:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

A fenti példákban a zóna megadható a `-ZoneName` `-ResourceGroupName`és a paraméterek (a mint látható) használatával, vagy egy zónaobjektum megadásával:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Rekord hozzáadása meglévő rekordkészlethez

Ha rekordot szeretne hozzáadni egy meglévő rekordkészlethez, kövesse az alábbi három lépést:

1. A meglévő rekordkészlet beszerezése

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adja hozzá az új rekordot a helyi rekordkészlethez. Ez egy off-line művelet.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Véglegesítse a módosítást az Azure DNS-szolgáltatásban. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

A `Set-AzDnsRecordSet` használata *lecseréli* a meglévő rekordot az Azure DNS-ben (és az összes benne lévő rekordok) a megadott rekordkészlettel. [Az etag-ellenőrzések](dns-zones-records.md#etags) biztosítják, hogy az egyidejű módosítások ne legyenek felülírva. A választható `-Overwrite` kapcsolóval letilthatja ezeket az ellenőrzéseket.

Ez a műveletsorozat is *cső,* ami azt jelenti, hogy adja át a rekord halmaz objektum segítségével a cső helyett halad át azt paraméterként:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

A fenti példák bemutatják, hogyan lehet "A" rekordot hozzáadni egy "A" típusú rekordkészlethez. Hasonló műveletsorozatot használ a rekordok hozzáadása más típusú halmazok rögzítéséhez, a `-Ipv4Address` paramétert az egyes bejegyzéstípusokra jellemző más `Add-AzDnsRecordConfig` paraméterekkel helyettesítve. Az egyes bejegyzéstípusok paraméterei megegyeznek `New-AzDnsRecordConfig` a parancsmag paramétereivel, ahogy az a fenti További bejegyzéstípus-példákban látható.

A "CNAME" vagy "SOA" típusú rekordhalmazok nem tartalmazhatnak egynél több rekordot. Ez a megkötés a DNS-szabványokból ered. Ez nem korlátozza az Azure DNS-t.

## <a name="remove-a-record-from-an-existing-record-set"></a>Rekord eltávolítása meglévő rekordkészletből

A rekordrekord-készletből való eltávolításának folyamata hasonló ahhoz a folyamathoz, amelysorán rekord ot ad egy meglévő rekordkészlethez:

1. A meglévő rekordkészlet beszerezése

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Távolítsa el a rekordot a helyi rekordkészlet-objektumból. Ez egy off-line művelet. Az eltávolítandó rekordnak pontosan egyeznie kell az összes paraméter meglévő rekordjával.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Véglegesítse a módosítást az Azure DNS-szolgáltatásban. A választható `-Overwrite` kapcsolóval letilthatja az [Etag-ellenőrzéseket](dns-zones-records.md#etags) az egyidejű módosításokra vonatkozóan.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Ha a fenti sorrendet használja az utolsó rekord rekordkészletből való eltávolítására, az nem törli a rekordkészletet, hanem üres rekordkészletet hagy. A rekordhalmazteljes eltávolításáról a [Rekordkészlet törlése (Rekordhalmaz törlése) (Rekordkészlet törlése) (Rekordhalmaz törlése) témakörben](#delete-a-record-set)van.

A rekordhalmazok rekordkészlethez való hozzáadásához hasonlóan a rekordhalmazok eltávolításához végzett műveletek sorrendje is csőbe állítható:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

A különböző bejegyzéstípusokat támogatja a megfelelő típusspecifikus paraméterek átadása a rendszernek. `Remove-AzDnsRecordSet` Az egyes bejegyzéstípusok paraméterei megegyeznek `New-AzDnsRecordConfig` a parancsmag paramétereivel, ahogy az a fenti További bejegyzéstípus-példákban látható.


## <a name="modify-an-existing-record-set"></a>Meglévő rekordkészlet módosítása

A meglévő rekordhalmazok módosításának lépései hasonlóak a rekordok rekordkészletből való hozzáadásakor vagy eltávolításakor tett lépésekhez:

1. A meglévő rekordkészlet `Get-AzDnsRecordSet`beolvasása a használatával.
2. A helyi rekordkészlet-objektum módosítása:
    * Rekordok hozzáadása és eltávolítása
    * A meglévő rekordok paramétereinek módosítása
    * A rekordkészlet metaadatainak és az élő időnek (TTL) módosítása
3. A módosítások véglegesítése a `Set-AzDnsRecordSet` parancsmag használatával. Ez *lecseréli* az Azure DNS-ben meglévő rekordot a megadott rekordkészlettel.

A `Set-AzDnsRecordSet`használatakor az [Etag-ellenőrzések](dns-zones-records.md#etags) biztosítják, hogy az egyidejű módosítások ne legyenek felülírva. A választható `-Overwrite` kapcsolóval letilthatja ezeket az ellenőrzéseket.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Rekord frissítése meglévő rekordhalmazban

Ebben a példában megváltoztatjuk egy meglévő "A" rekord IP-címét:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>SOA rekord módosítása

Az automatikusan létrehozott SOA rekordkészletből nem lehet rekordokat`-Name "@"`hozzáadni vagy eltávolítani a zóna csúcsán ( idézőjeleket is beleértve). A SOA rekord (kivéve a "Host") és a Rekordkészlet TTL paramétereit azonban módosíthatja.

A következő példa bemutatja, hogyan módosíthatja a SOA rekord *E-mail* tulajdonságát:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS-rekordok módosítása a zóna csúcsán

A zóna csúcsán beállított NS rekord automatikusan létrejön az egyes DNS-zónákkal. A zónához rendelt Azure DNS-névkiszolgálók nevét tartalmazza.

A ns rekordkészlethez további névkiszolgálókat is hozzáadhat, így több DNS-szolgáltatóval is támogathatja a tartományok közös üzemeltetését. A rekordkészlet TTL- és metaadatait is módosíthatja. Az előre kitöltött Azure DNS-névkiszolgálók azonban nem távolíthatók el és nem módosíthatók.

Ne feledje, hogy ez csak a zóna csúcsán beállított NS rekordra vonatkozik. A zónában lévő egyéb ns rekordkészletek (a gyermekzónák delegálásához használt) korlátozás nélkül módosíthatók.

A következő példa bemutatja, hogyan adhat hozzá további névkiszolgálót az NS-rekordkészlethez a zóna csúcsán:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Rekordkészlet metaadatainak módosítása

[A rekordkészlet metaadatai](dns-zones-records.md#tags-and-metadata) segítségével alkalmazásspecifikus adatokat társíthat minden rekordkészlethez kulcsérték-párként.

A következő példa bemutatja, hogyan lehet módosítani egy meglévő rekordkészlet metaadatait:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Rekordkészlet törlése

A rekordkészletek a `Remove-AzDnsRecordSet` parancsmag használatával törölhetők. A rekordhalmaz törlésével a rekordhalmazösszes rekordja is törlődik.

> [!NOTE]
> A SOA és az NS rekordkészletek nem`-Name '@'`törölhetők a zóna csúcsán ( ).  Az Azure DNS ezeket automatikusan létrehozta a zóna létrehozásakor, és automatikusan törli őket a zóna törlésekor.

A következő példa bemutatja, hogyan lehet törölni egy rekordkészletet. Ebben a példában a rekordhalmaz neve, a rekordkészlet típusa, a zónaneve és az erőforráscsoport explicit módon van megadva.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Másik lehetőségként a rekordkészlet megadható név és típus szerint, és a megadott zóna pedig egy objektum használatával:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Harmadik lehetőségként maga a rekordkészlet is megadható rekordkészlet-objektum használatával:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Ha rekordkészlet-objektum mal adja meg a törölni kívánt rekordkészletet, az [Etag-ellenőrzések](dns-zones-records.md#etags) biztosítják, hogy az egyidejű módosítások ne törlődjenek. A választható `-Overwrite` kapcsolóval letilthatja ezeket az ellenőrzéseket.

A rekordkészlet-objektum paraméterként való átadás a paraméterek helyett is csőbe állítható:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` és `Remove-AzDnsRecordSet` parancsmagok mind támogatják a megerősítési kérések használatát.

Minden parancsmag megerősítést kér, `$ConfirmPreference` ha a PowerShell-beállítási változó értéke `Medium` vagy alacsonyabb. Mivel az alapértelmezett `$ConfirmPreference` `High`érték, ezek a kérések nem adják meg az alapértelmezett PowerShell-beállítások használatakor.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést. 

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](/powershell/module/microsoft.powershell.core/about/about_preference_variables) tartalmazó részben talál.

## <a name="next-steps"></a>További lépések

További információ [az Azure DNS zónáiról és rekordjairól.](dns-zones-records.md)
<br>
Ismerje meg, hogyan [védheti meg a zónákat és a rekordokat az](dns-protect-zones-recordsets.md) Azure DNS használata során.
<br>
Tekintse át az [Azure DNS PowerShell referenciadokumentációját.](/powershell/module/az.dns)
