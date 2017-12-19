---
title: "Az Azure DNS az Azure PowerShell DNS-rekordok kezelése |} Microsoft Docs"
description: "DNS-rekordhalmazok és az Azure DNS-rekordok kezelése esetén az Azure DNS-tartomány. Az összes PowerShell-parancsokat rekordhalmazokat és rekordokat műveleteket."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.openlocfilehash: fee96a77436f09e5cf2841b36b244e2d03f57f74
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>DNS-rekordok és az Azure PowerShell használata Azure DNS rekordhalmazok kezelése

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-rekordokat a DNS-zóna Azure PowerShell használatával. DNS-rekordokat is kezelhetők a platformfüggetlen használatával [Azure CLI](dns-operations-recordsets-cli.md) vagy a [Azure-portálon](dns-operations-recordsets-portal.md).

Ebben a cikkben szereplő példák azt feltételezik, hogy már rendelkezik [Azure PowerShell jelentkezik be, és a DNS-zóna létrehozásakor](dns-operations-dnszones.md).

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.


## <a name="create-a-new-dns-record"></a>Hozzon létre egy új DNS-rekord

Ha az új rekord tartalmazza-e ilyen nevű és típusú, mint egy már létező rekordot, akkor [vegye fel a meglévő rekordkészlet](#add-a-record-to-an-existing-record-set). Ha az új rekord tartalmazza-e egy másik nevet, az összes meglévő rekordok be, akkor kell új rekord létrehozása. 

### <a name="create-a-records-in-a-new-record-set"></a>"A" rekordokat létrehozni egy új rekordkészlete

Rekordhalmazt a `New-AzureRmDnsRecordSet` parancsmag használatával hozhat létre. A rekordhalmaz létrehozásakor meg kell adnia a rekordhalmaz a nevét, a zóna a, az élettartam (TTL), a rekordtípust, és a rekordokat létrehozni.

A rekordok rekordhalmazhoz adásának paraméterei a rekordhalmaz típusától függően eltérnek. Például egy "A" típusú rekordkészlet használatakor kell az IP-cím, a paraméter használatával `-IPv4Address`. Más paramétereket az egyéb típusú bejegyzés használnak. Lásd: [további rekordtípusokra](#additional-record-type-examples) részleteiről.

Az alábbi példa létrehoz egy rekordot a DNS-zónában "contoso.com" a "www" relatív névvel beállítása. A rekordhalmaz teljesen minősített neve "www.contoso.com". A típus az "A", és az élettartam 3600 másodperc. A rekordhalmaz egyetlen rekordot, IP-cím "1.2.3.4" tartalmaz.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Állítsa be a "csúcs" zóna rekord létrehozásához (ebben az esetben "contoso.com"), használja a következő rekordhalmaznevet "@" (idézőjelek nélkül):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Ha szeretné beállítani a több rekordot tartalmazó rekordot kell létrehozni, először hozzon létre egy helyi tömb adja hozzá a rekordokat, majd adja át a tömbben `New-AzureRmDnsRecordSet` az alábbiak szerint:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[A rekordhalmaz metaadatok](dns-zones-records.md#tags-and-metadata) rendelje hozzá az alkalmazás-specifikus adatok minden rekordhalmaz, a kulcs-érték párként használható. A következő példa bemutatja, hogyan hozzon létre két metaadat-bejegyzést, rekordkészlet "osztály pénzügyi =" és "környezet éles =".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Az Azure DNS "empty" rekordhalmazok, amely működhet-e a DNS-név DNS-rekordok létrehozása előtt lefoglalni helyőrzőként is támogatja. Üres rekordhalmazok az Azure DNS-vezérlő vezérlősík látható, de jelennek meg az Azure DNS névkiszolgálóit. Az alábbi példa létrehoz egy üres rekordhalmaz:

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Hogy látott részletesen "A" rekordok létrehozása, a következő példák szemléltetik Azure DNS által támogatott más rekord típusú rekordok létrehozásához.

Minden esetben megmutatjuk, hogyan rekordhalmaz egyetlen rekordot tartalmazó létrehozásához. A korábbi példák "A" rekordok hozzáigazítható üres rekordhalmazok létrehozásához vagy más típusú metaadatokkal, a több rekordot tartalmazó rekordhalmazok létrehozásához.

Egy példa egy SOA típusú rekordhalmaz létrehozása óta SOAs jönnek létre nem felállításához és minden DNS-zóna törlődik, és nem hozható létre vagy törölt külön-külön. Azonban [módosíthatják a SOA, egy újabb példában látható módon](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó AAAA típusú rekordhalmaz létrehozása

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó CAA rekordhalmaz létrehozása

```powershell
New-AzureRmDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó CNAME típusú rekordhalmaz létrehozása

> [!NOTE]
> A DNS-szabványokból nem teszik lehetővé a CNAME rekordot a zóna tetején (`-Name '@'`), és nem teszik egynél több rekordot tartalmazó rekordhalmazok.
> 
> További információkért lásd: [CNAME rekordok](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó MX típusú rekordhalmaz létrehozása

Ebben a példában a következő rekordhalmaznevet használjuk "@" lehet létrehozni az MX-rekord a zóna felső pontja (ebben az esetben a "contoso.com").


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó NS típusú rekordhalmaz létrehozása

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó PTR típusú rekordhalmaz létrehozása

Ebben az esetben "my-arpa-zónában (Zone.com)" a ARPA-névkeresési zónát, az IP-címtartomány képviselő jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg. A rekordnév a "10" Ez a bejegyzés által képviselt IP-címtartományon belül az IP-cím utolsó oktett.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó SRV típusú rekordhalmaz létrehozása

Létrehozásakor egy [SRV-rekordhalmaz](dns-zones-records.md#srv-records), adja meg a  *\_szolgáltatás* és  *\_protokoll* a rekordhalmaz-neve. Nincs szükség felvenni "@" a rekordhalmaz nevében, ha a zóna felső pontja az SRV rekord létrehozása beállítva.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó TXT rekordhalmaz létrehozása

A következő példa bemutatja, hogyan TXT rekord létrehozásához. A maximális hossz támogatja a TXT-rekord kapcsolatos további információkért lásd: [TXT rekord](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Rekordkészlet beolvasása

Egy meglévő rekordhalmaz lekéréséhez használja `Get-AzureRmDnsRecordSet`. Ez a parancsmag egy helyi objektum az az Azure DNS-rekordot jelölő adja vissza.

A `New-AzureRmDnsRecordSet`, a megadott rekordhalmaz nevének kell lennie egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét. Is meg kell adnia a rekordtípust, és a zóna a rekordot tartalmazó állítsa be.

A következő példa bemutatja, hogyan rekordkészlet beolvasása. Ebben a példában a zóna adott meg a `-ZoneName` és `-ResourceGroupName` paraméterek.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Másik lehetőségként azt is megadhatja a zóna egy zóna objektummal, használja az átadott a `-Zone` paraméter.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista rekordhalmazok

Is `Get-AzureRmDnsZone` lista parancs zónában, kihagyva a `-Name` és/vagy `-RecordType` paraméterek.

Az alábbi példa adja vissza, az összes rekordot a zónában állítja be:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

A következő példa bemutatja, hogyan összes rekordkészletek egy adott típusú lekérhető a rekordtípus megadásával, amíg a rekord kihagyásával nevének megadása:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Ezzel a névvel, minden rekordkészletet típusok között lekéréséhez kell minden rekordkészletet kérnie és el kell majd az eredmények szűréséhez:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Az összes fenti példákban a zóna adható meg valamelyikét a `-ZoneName` és `-ResourceGroupName`paraméterek (ahogy), vagy adjon meg egy zóna objektum:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Rekord hozzáadása egy meglévő rekordkészlete

Egy meglévő rekordhalmaz hozzáadni egy rekordot, kövesse az alábbi három lépést:

1. A meglévő rekordkészlet beolvasása

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Az új bejegyzés hozzáadása a helyi rekordhalmaz. Ez az offline művelet.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. A módosítás véglegesítése vissza az Azure DNS szolgáltatást. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Használatával `Set-AzureRmDnsRecordSet` *lecseréli* a meglévő Azure DNS-ben (és a benne található összes rekord) tartalmazó rekordhalmaz megadott rekordhalmaz. [ETag ellenőrzések](dns-zones-records.md#etags) biztosítják az egyidejű változtatások nem íródnak felül. A választható használható `-Overwrite` kapcsoló ezen ellenőrzések letiltásához.

Ez a feladatütemezési műveletek is lehet *adatcsatornán*, ami azt jelenti, használja a csőről való átadása paraméterként helyett adja meg a rekordhalmaz objektum:

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

A fenti példák bemutatják, hogyan egy "A" bejegyzés hozzáadása egy meglévő rekordhalmaz "A" típusú. A hasonló feladatütemezési műveletek használatával adja hozzá rekordokat rekordhalmazok más típusú, és a `-Ipv4Address` paramétere `Add-AzureRmDnsRecordConfig` különböző rekordtípusú jellemző más paraméterekkel. Az egyes rekordok paraméterei megegyezik a a `New-AzureRmDnsRecordConfig` parancsmag, ahogy az [további rekordtípusokra](#additional-record-type-examples) felett.

"CNAME" vagy "SOA" típusú rekordhalmazok nem tartalmazhat egynél több rekordot. Ennél a határértéknél a DNS-szabványokból ered. Az Azure DNS korlátozása nincs.

## <a name="remove-a-record-from-an-existing-record-set"></a>Távolítsa el a rekord egy meglévő rekordkészlete

A folyamat egy olyan rekordot eltávolítása rekordhalmaz hozzáadni egy rekordot egy meglévő rekordhalmaz a folyamat hasonlít:

1. A meglévő rekordkészlet beolvasása

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. A helyi rekordhalmaz objektum adatainak eltávolítása. Ez az offline művelet. Az eltávolított kell bejegyzést terméknévnek pontosan egyeznie kell a meglévő bejegyzés minden paraméterek között.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. A módosítás véglegesítése vissza az Azure DNS szolgáltatást. Használja az opcionális `-Overwrite` kapcsolót, hogy ne jelenjen meg többé [Etag ellenőrzi](dns-zones-records.md#etags) egyidejű változásait.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

A fenti feladatütemezési segítségével rekordhalmaz utolsó adatainak eltávolítása nem törli a rekordhalmaz, inkább egy üres rekordhalmaz elhagyják. A rekordhalmaz teljesen eltávolítja, lásd: [rekordhalmaz törlése](#delete-a-record-set).

Hasonló módon való hozzáadásakor rekordok rekordhalmaz, a rekordhalmaz eltávolítása műveletek sorrendjét is átirányítható:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Különböző típusok támogatottak úgy, hogy a megfelelő típus-specifikus paramétereket `Remove-AzureRmDnsRecordSet`. Az egyes rekordok paraméterei megegyezik a a `New-AzureRmDnsRecordConfig` parancsmag, ahogy az [további rekordtípusokra](#additional-record-type-examples) felett.


## <a name="modify-an-existing-record-set"></a>Módosíthatja egy meglévő rekordkészlete

Egy meglévő rekordhalmaz módosítása lépései hasonlóak lépései hozzáadása vagy eltávolítása a bejegyzések rekordhalmaz:

1. A meglévő rekordhalmazt beolvasása `Get-AzureRmDnsRecordSet`.
2. A helyi rekordhalmaz objektum által módosítható:
    * Hozzáadása vagy eltávolítása a bejegyzések
    * A meglévő rekordok paraméterek módosítása
    * Élettartam (TTL) a metaadatok és az idő beállítása a rekord módosítása
3. A változtatások véglegesítése a határidő használatával a `Set-AzureRmDnsRecordSet` parancsmag. Ez *lecseréli* a meglévő Azure DNS-ben tartalmazó rekordhalmaz megadott rekordhalmaz.

Használata esetén `Set-AzureRmDnsRecordSet`, [Etag ellenőrzi](dns-zones-records.md#etags) biztosítják az egyidejű változtatások nem íródnak felül. A választható használható `-Overwrite` kapcsoló ezen ellenőrzések letiltásához.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Egy meglévő rekordhalmaz egy rekord módosítása

Ebben a példában az IP-cím, egy létező "A" rekord módosítjuk:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Egy SOA-rekord módosítása

Nem adja hozzá vagy rekordok eltávolítása az automatikusan létrehozott SOA típusú rekordját, állítsa be a zóna felső pontja (`-Name "@"`, beleértve az ajánlat jelek). Azonban módosíthatja a SOA típusú rekordját (kivéve az "állomás") belül paraméterek egyikét, és a rekordhalmaz TTL-t.

A következő példa bemutatja, hogyan módosíthatja a *E-mail* SOA típusú rekordjának tulajdonsága:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>A zóna felső pontja a Névkiszolgálói rekordok módosítása

Állítsa be a zóna felső pontja NS-rekord automatikusan létrejön minden DNS-zóna. Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza.

Hozzáadhat további névhez a kiszolgálók e NS-rekord, támogatja a párhuzamos üzemeltetési tartományok egynél több DNS-szolgáltatónál. A TTL-t és a metaadatok a rekordhalmaz is módosíthatók. Azonban nem lehet eltávolítani, vagy módosítsa az előre megadott Azure DNS névkiszolgálóit.

Vegye figyelembe, hogy csak vonatkozik az NS típusú rekordhalmaz zóna tetején. A zónában (a használt gyermekzónákhoz delegálása) más NS-rekordhalmazok lehet módosítani, korlátozás nélkül.

A következő példa bemutatja, hogyan hozzáadása egy további nevét, a zóna felső pontja Névkiszolgálói rekordhalmazt:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Rekordhalmaz metaadatok módosítása

[A rekordhalmaz metaadatok](dns-zones-records.md#tags-and-metadata) rendelje hozzá az alkalmazás-specifikus adatok minden rekordhalmaz, a kulcs-érték párként használható.

A következő példa bemutatja, hogyan lehet módosítani egy meglévő rekordhalmaz metaadatait:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>A rekordhalmaz törlése

Rekordhalmazok használatával törölheti a `Remove-AzureRmDnsRecordSet` parancsmag. A rekordhalmaz összes rekordján is rekordhalmaz törlése törli.

> [!NOTE]
> Nem lehet törölni a SOA és NS-rekord beállítja a zóna felső pontja (`-Name '@'`).  Az Azure DNS automatikusan létrehozza ezeket a beállítást, ha a zóna lett létrehozva, és automatikusan törli azokat az a zóna törlődik.

A következő példa bemutatja, hogyan rekordhalmaz törlése. Ebben a példában a rekordhalmazának neve, a rekordhalmaz típusa, a zóna nevét és a erőforráscsoport minden egyes megadott explicit módon.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Másik lehetőségként a rekordhalmaz neve és típusa és egy objektummal megadott zóna adható meg:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Egy harmadik beállítás, mert a rekordhalmaz maga adható meg egy rekordhalmaz-objektummal:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Amikor megad egy rekordhalmaz objektum használatával a törlendő rekordhalmaz [Etag ellenőrzi](dns-zones-records.md#etags) biztosítják az egyidejű változtatások nem törlődnek. A választható használható `-Overwrite` kapcsoló ezen ellenőrzések letiltásához.

A rekordhalmaz objektum is átirányítható paraméterként átadott helyett:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Megerősítés

A `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet`, és `Remove-AzureRmDnsRecordSet` parancsmagok minden megerősítés támogatja.

Minden parancsmagot jóváhagyást kérni fogja, ha a `$ConfirmPreference` PowerShell preferenciaváltozót értéke `Medium` vagy alacsonyabb. Az alapértelmezett érték óta `$ConfirmPreference` van `High`, ezek az üzenetek nem adja az alapbeállításokat PowerShell használata esetén.

Ha szeretné felülbírálni az aktuális `$ConfirmPreference` használatának beállítása a `-Confirm` paraméter. Ha megad `-Confirm` vagy `-Confirm:$True` , a parancsmag megerősítést kér, mielőtt futtatja. Ha megad `-Confirm:$False` , a parancsmag nem figyelmeztet megerősítést kér. 

További információ `-Confirm` és `$ConfirmPreference`, lásd: [kapcsolatos Preferenciaváltozók](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Következő lépések

További információ [zónák és az Azure DNS-rekordok](dns-zones-records.md).
<br>
Megtudhatja, hogyan [védelme a zóna és a rekordok](dns-protect-zones-recordsets.md) Azure DNS használata esetén.
<br>
Tekintse át a [Azure DNS PowerShell referenciadokumentációt](/powershell/module/azurerm.dns).
