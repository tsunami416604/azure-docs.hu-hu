---
title: Az Azure PowerShell-lel az Azure DNS DNS-rekordok kezelése |} A Microsoft Docs
description: Az Azure DNS-tartomány üzemeltetése esetén kezelése a DNS-rekordhalmazok és rekordok az Azure DNS szolgáltatásra. Az összes PowerShell-parancsokat rekordhalmazokat és rekordokat műveleteket.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: victorh
ms.openlocfilehash: 199843be72df473ae501eaf6bd47dce41b079c94
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58906106"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Kezelheti a DNS-rekordok és -rekordhalmazok az Azure DNS Azure powershellel

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure klasszikus parancssori felület](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan kezelheti DNS-rekordokat a DNS-zóna Azure PowerShell használatával. DNS-rekordok is kezelhetők a platformok közötti használatával [Azure CLI-vel](dns-operations-recordsets-cli.md) vagy a [az Azure portal](dns-operations-recordsets-portal.md).

Ebben a cikkben szereplő példák feltételezik, hogy már [telepítette az Azure Powershellt, bejelentkezett, és létrehozott egy DNS-zónát](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.


## <a name="create-a-new-dns-record"></a>Hozzon létre új DNS-rekord

Ha az új rekord tartalmazza-e az azonos neve és típusa megegyezik egy meglévő rekordját, akkor kell [adja hozzá a létező rekordhalmazhoz](#add-a-record-to-an-existing-record-set). Ha az új rekord egy másik nevet, írja be, egyetlen létező rekordéval, szeretne létrehozni egy új rekordhalmazt. 

### <a name="create-a-records-in-a-new-record-set"></a>"A" rekordok létrehozása az új rekordhalmaz

Rekordhalmazt a `New-AzDnsRecordSet` parancsmag használatával hozhat létre. Egy rekordhalmaz létrehozásakor meg kell adnia a rekordhalmaz-neve, a zónát, az idő Élettartam (TTL), a rekordtípust és a rekordok létrehozását.

A rekordok rekordhalmazhoz adásának paraméterei a rekordhalmaz típusától függően eltérnek. Például egy "A" típusú rekordhalmazok használata esetén szüksége adja meg az IP-címet a paraméter használatával `-IPv4Address`. Más rekordtípusok más paramétereket használják. Tekintse meg a további rekordtípusokra részleteiről.

Az alábbi példa egy "contoso.com" DNS-zóna a "www" relatív nevű rekordot hoz létre. A beállított rekord teljes neve "www.contoso.com". A rekord típusa "A", akkor az Élettartama pedig 3600 másodperc. A rekordhalmaz egyetlen rekordot, az IP-címe "1.2.3.4" tartalmazza.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Létrehozni egy rekordot a legfelső pontján:"zóna (ebben az esetben a" contoso.com"), a rekordhalmaz nevének használata"\@"(idézőjelek nélkül):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Ha szeretne létrehozni egy rekordot egynél több rekordot tartalmazó, először hozzon létre egy helyi tömb rekordok hozzáadásához, majd továbbítja a tömböt az `New-AzDnsRecordSet` módon:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[A rekordhalmaz metaadatainak](dns-zones-records.md#tags-and-metadata) minden rekordhalmaz kulcs-érték párok, alkalmazás-specifikus adatok társítása is használható. Az alábbi példa bemutatja, hogyan hozhat létre a két metaadat-bejegyzéseket, "részleg = pénzügyi" és "környezet éles =".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Az Azure DNS támogatja a "empty" rekordhalmazok, amelyek szolgálhatnak egy helyőrző lefoglalása egy DNS-nevet a DNS-rekordok létrehozása előtt is. Üres rekordhalmazok az Azure DNS vezérlősík láthatók, de jelenik meg az Azure DNS névkiszolgálóit. Az alábbi példa létrehoz egy üres rekordhalmaz:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Kellene látható részletesebben "A" rekordok létrehozása, az alábbi példák bemutatják, hogyan további rekordtípusok támogatja az Azure DNS-rekordok létrehozása.

Minden esetben bemutatjuk, hogyan hozhat létre a rekordhalmaz egyetlen rekordot tartalmazó. "A" rekordok a korábbi példákban is kell igazítani, más típusú, a metaadatok, több rekordot tartalmazó rekordhalmazok létrehozásához vagy üres rekordhalmazok létrehozásához.

Nem biztosítunk lehet például egy SOA típusú rekordhalmaz létrehozása, mivel SOAs jönnek létre, és törölni minden DNS-zónát, és nem hozható létre vagy külön-külön törölve. Azonban [módosíthatja a SOA típusú, újabb példában látható módon](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó AAAA típusú rekordhalmaz létrehozása

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>A CAA rekordhalmaz egyetlen rekordot tartalmazó létrehozása

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó CNAME típusú rekordhalmaz létrehozása

> [!NOTE]
> A DNS-szabványok nem engedélyeznek CNAME-rekordokat a zóna tetején található rekordokra (`-Name '@'`), és nem teszik egynél több rekordot tartalmazó rekordhalmazok.
> 
> További információkért lásd: [CNAME-rekordokat](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó MX típusú rekordhalmaz létrehozása

Ebben a példában a rekordhalmaznevet használjuk "\@" hozhat létre az MX-rekord a zóna legfelső pontján (ebben az esetben a "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó NS típusú rekordhalmaz létrehozása

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó PTR típusú rekordhalmaz létrehozása

Ebben az esetben "my-arpa-zone.com" az az IP-címtartományt képviselő ARPA-névkeresési zónát jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg. A rekord neve "10" az utolsó oktettet IP-cím az IP-címtartomány, ez a bejegyzés által képviselt belül.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó SRV típusú rekordhalmaz létrehozása

Létrehozásakor egy [SRV rekordhalmaz](dns-zones-records.md#srv-records), adja meg a  *\_szolgáltatás* és  *\_protokoll* a rekordhalmaz-neve. Nem kell felvenni a(z)\@"a rekordhalmaz nevét, ha létrehozása egy SRV-rekordot a zóna legfelső pontján.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó txt típusú rekordhalmaz létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre egy txt típusú rekordot. További információ a támogatott txt típusú rekordok a karakterlánc maximális hossza: [txt típusú rekordok](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Rekordhalmaz beolvasása

Egy meglévő rekordhalmaz kérheti `Get-AzDnsRecordSet`. Ez a parancsmag egy helyi Azure DNS-ben a rekordot képviselő objektumot adja vissza.

A `New-AzDnsRecordSet`, az adott rekordhalmaz nevének kell lennie egy *relatív* neve, ami azt jelenti, azt kell zárnia a zóna nevét. Is meg kell adnia a rekordtípust, és a rekordot tartalmazó zóna beállítása.

Az alábbi példa bemutatja, hogyan rekordhalmaz lekéréséhez. Ebben a példában a zóna van megadva a `-ZoneName` és `-ResourceGroupName` paramétereket.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Másik lehetőségként azt is megadhatja a zóna egy zóna objektummal, az átadott használatával a `-Zone` paraméter.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista rekordhalmazok

Is `Get-AzDnsZone` a lista rekordhalmazok zónában, felsorolhatja az `-Name` és/vagy `-RecordType` paramétereket.

Az alábbi példa adja vissza az összes rekordot a zónában állítja be:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Az alábbi példa bemutatja, hogyan összes rekordhalmazok egy adott típusú kérhető elhagyásával a bejegyzést csoportjának a neve, adja meg a rekord típusa:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Ezzel a névvel, minden rekordhalmazok különböző rekordtípusok lekéréséhez kell lekérni az összes rekordhalmazt, és szűrjön rá az eredmények:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Az összes fenti példákban a zóna adható meg keresztül a `-ZoneName` és `-ResourceGroupName`paraméterek (szerint), vagy egy zóna objektum megadásával:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adjon hozzá egy rekordot egy meglévő rekordhalmazhoz

Adjon hozzá egy rekordot egy meglévő rekordhalmazhoz, kövesse az alábbi három lépést:

1. A meglévő rekordhalmazhoz beolvasása

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. A helyi rekordhalmazhoz adja hozzá az új rekordban. Ez a művelet végezze a rögzítést.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Véglegesítse a módosítást, térjen vissza az Azure DNS szolgáltatásban. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Használatával `Set-AzDnsRecordSet` *váltja fel* a meglévő rekordhalmazt Azure DNS-ben (és a benne található összes rekord) a beállított rekord megadott. [Az ETag-ellenőrzések](dns-zones-records.md#etags) biztosítva a párhuzamos módosítások nem lettek felülírva. Használhatja a választható `-Overwrite` kapcsoló le ezeket az ellenőrzéseket.

Ez a feladatütemezési műveletek is lehet *parancsoknak*, ami azt jelenti, át kell adnia a rekordhalmaz objektum paraméterként való átadásával helyett használja a függőleges vonal:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

A fenti példák bemutatják az "A" rekord hozzáadása egy meglévő rekordhalmazhoz "A" típusú. A hasonló feladatütemezési műveletek segítségével adhatja hozzá a rekordhalmazokhoz rekordhalmazok más típusú, és cserélje le a `-Ipv4Address` paraméterében `Add-AzDnsRecordConfig` adott, az egyes rekordtípusokra más paraméterekkel. Az egyes rekordtípusokra paraméterei megegyezik a a `New-AzDnsRecordConfig` parancsmagot, a fenti további rekordtípus példákban szemléltetett módon.

"CNAME" vagy "SOA" típusú rekordhalmazok nem tartalmazhat egynél több rekordot. Ezt a korlátozást a DNS-szabványok ered. Már nem az Azure DNS egy korlátozás.

## <a name="remove-a-record-from-an-existing-record-set"></a>Egy meglévő rekordhalmaz bejegyzés eltávolítása

A folyamat a bejegyzés eltávolítása a rekordhalmaz adjon hozzá egy rekordot egy meglévő rekordhalmazhoz a folyamat hasonlít:

1. A meglévő rekordhalmazhoz beolvasása

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Távolítsa el a rekordot a helyi rekordhalmaz objektum. Ez a művelet végezze a rögzítést. Az eltávolítandó rekord minden paraméter egy létező rekord adataival pontos egyezésűnek kell lennie.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Véglegesítse a módosítást, térjen vissza az Azure DNS szolgáltatásban. A választható `-Overwrite` le kapcsoló [Etag ellenőrzi](dns-zones-records.md#etags) egyidejű módosításait.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Az utolsó rekord rekordhalmaz eltávolítása a fenti feladatütemezés használatával nem törli a rekordhalmaz, inkább akkor hagyja, hogy egy üres rekordhalmaz. Távolítsa el teljesen egy rekordot, lásd: [egy rekordhalmaz törlése](#delete-a-record-set).

Hasonlóképpen a rekordok rekordhalmazhoz ad, a rekordhalmaz eltávolítása műveletek sorrendjét is átadható olyan parancsoknak:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Különböző rekordtípusok támogatottak tartalmában való böngészéshez illessze a megfelelő típus-specifikus paramétereket `Remove-AzDnsRecordSet`. Az egyes rekordtípusokra paraméterei megegyezik a a `New-AzDnsRecordConfig` parancsmagot, a fenti további rekordtípus példákban szemléltetett módon.


## <a name="modify-an-existing-record-set"></a>Módosíthatja egy meglévő rekordhalmaz

A lépéseket, amikor az hozzáadását és eltávolítását rekordok rekordhalmazhoz módosítja egy létező rekordhalmazhoz lépései hasonlók:

1. Beolvasni a meglévő rekordhalmazt `Get-AzDnsRecordSet`.
2. Módosítsa a helyi rekordhalmaz objektum által:
    * Hozzáadásával vagy eltávolításával rekordok
    * A paraméterek a meglévő rekordok módosítása
    * Élettartam (TTL) a metaadatok és az idő beállítása a rekord módosítása
3. A változtatások véglegesítése a használatával a `Set-AzDnsRecordSet` parancsmagot. Ez *váltja fel* a beállított rekord megadott Azure DNS-ben meglévő rekordhalmazt.

Használata esetén `Set-AzDnsRecordSet`, [Etag ellenőrzi](dns-zones-records.md#etags) biztosítva a párhuzamos módosítások nem lettek felülírva. Használhatja a választható `-Overwrite` kapcsoló le ezeket az ellenőrzéseket.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Egy rekord egy meglévő rekordhalmaz módosítása

Ebben a példában egy meglévő "A" rekord IP-címének módosítása:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>SOA-rekord módosítása

Nem adhat hozzá és rekordok eltávolítása az automatikusan létrehozott, állítsa be a zóna legfelső pontján SOA típusú rekordját (`-Name "@"`, ajánlat együtt). Azonban módosíthatja a SOA típusú rekordját (kivéve a "Host") belül paraméterek egyikét, és a rekordhalmaz TTL.

A következő példa bemutatja, hogyan módosíthatja a *E-mail* a SOA típusú rekordját tulajdonságát:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Módosíthatja a Névkiszolgálói rekordokat a zóna legfelső pontján

Az NS-rekord a zóna legfelső pontján állítsa be a rendszer automatikusan létrehoz minden DNS-zónát. Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza.

Hozzáadhat további névhez e NS-rekord-kiszolgálókat, támogatja a közös üzemeltetési tartomány több DNS-szolgáltatónál. Az élettartam és a rekordhalmaz metaadatait is módosíthatja. Azonban nem távolítsa el vagy módosítsa az ki van töltve az Azure DNS névkiszolgálóit.

Vegye figyelembe, hogy ez csak érvényes az NS-rekord a zóna legfelső pontján állítsa be. Más Névkiszolgálói rekordhalmazt a zónában (a használt gyermek zónák delegálása) korlátozás nélkül módosíthatók.

Az alábbi példa bemutatja, hogyan az NS-rekord a zóna legfelső pontján állítsa be a további neve kiszolgáló hozzáadása:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>A rekordhalmaz metaadatainak módosítása

[A rekordhalmaz metaadatainak](dns-zones-records.md#tags-and-metadata) minden rekordhalmaz kulcs-érték párok, alkalmazás-specifikus adatok társítása is használható.

Az alábbi példa bemutatja, hogyan módosíthatja egy meglévő rekordhalmaz metaadatait:

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


## <a name="delete-a-record-set"></a>A rekordhalmaz törlése

Rekordhalmazok használatával törölhetők a `Remove-AzDnsRecordSet` parancsmagot. Rekordhalmaz törlése is törli a beállított rekord összes rekordján.

> [!NOTE]
> Nem lehet törölni a SOA és NS-rekord zóna felső pontjánál állítja be (`-Name '@'`).  Az Azure DNS automatikusan létrehozza ezeket a beállítást, ha a zóna lett létrehozva, és automatikusan törli azokat a zóna törlésekor.

Az alábbi példa bemutatja, hogyan egy rekordhalmaz törlése. Ebben a példában a neve, rekordhalmaz típusa, zóna neve és erőforráscsoport minden egyes megadott explicit módon.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Azt is megteheti a rekordhalmaz nevét és típusát és a egy objektummal megadott zóna szerint adható meg:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Harmadik lehetőségként rekordhalmaz magát adható meg a rekordhalmaz-objektummal:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Amikor megad egy rekordhalmaz objektum használatával a törlendő rekordhalmaz [Etag ellenőrzi](dns-zones-records.md#etags) biztosítva a párhuzamos módosítások nem törlődnek. Használhatja a választható `-Overwrite` kapcsoló le ezeket az ellenőrzéseket.

A rekordhalmaz objektum is átadható olyan parancsoknak, mint egy paraméter helyett:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` és `Remove-AzDnsRecordSet` parancsmagok mind támogatják a megerősítési kérések használatát.

Minden parancsmag megerősítést kérni fogja, ha a `$ConfirmPreference` PowerShell-preferenciaváltozó értéke `Medium` vagy kisebb. Az alapértelmezett érték a óta `$ConfirmPreference` van `High`, ezek a felkérések nem adják meg az alapértelmezett PowerShell beállítások használatakor.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést. 

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) tartalmazó részben talál.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [zónák és -rekordok az Azure DNS](dns-zones-records.md).
<br>
Ismerje meg, hogyan [a zónák és -rekordok védelme](dns-protect-zones-recordsets.md) Azure DNS használata esetén.
<br>
Tekintse át a [Azure DNS PowerShell-referenciáinak dokumentációja](/powershell/module/az.dns).
