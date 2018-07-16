---
title: Webalkalmazások egyéni DNS-rekordok létrehozása |} A Microsoft Docs
description: Hogyan hozhat létre egyéni tartományt a webalkalmazást az Azure DNS használatával DNS-rekordjait.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: 7ee3dbdcd4d8b2627273a871aec94583b6c5dd6a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059801"
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>A webalkalmazás DNS-rekordok létrehozása az egyéni tartomány

Az Azure DNS segítségével a webalkalmazásokhoz egyéni tartományt üzemeltetéséhez. Például egy Azure-webalkalmazást hoz létre, és azt szeretné, hogy a felhasználók által eléréséhez a contoso.com vagy www.contoso.com az FQDN-ként.

Ehhez hozzon létre két rekordot rendelkezik:

* A contoso.com mutató egy gyökérszintű "A" rekord
* Egy "CNAME" rekord, amely a-rekordra mutat www neve

Ne feledje, hogy ha egy A rekordot egy webalkalmazást hoz létre az Azure-ban, az A rekord manuálisan kell frissíteni, ha az alapul szolgáló IP-cím a web app módosítások.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, először hozzon létre egy DNS-zóna Azure DNS-ben, és a zóna delegálása az Azure DNS-be a regisztráló.

1. DNS-zóna létrehozásához kövesse a lépéseket a [DNS-zóna létrehozása](dns-getstarted-create-dnszone.md).
2. Az Azure DNS a DNS-kiszolgáló delegálása, kövesse [tartomány DNS-delegálás](dns-domain-delegation.md).

Miután létrehozta a zónát, és azt az Azure DNS-delegálás, majd az egyéni tartomány hozhat létre rekordokat.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Hozzon létre egy A rekordot az egyéni tartomány

A rekord segítségével-név leképezése az IP-címét. A következő példa hozzárendeli \@ , egy A rekordot egy IPv4-címre:

### <a name="step-1"></a>1. lépés

Hozzon létre egy rekordot, és rendelje hozzá egy változóhoz $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>2. lépés

Adja hozzá az IPv4-értéket a korábban létrehozott rekordhalmazhoz "\@" hozzárendelt $rs változó használatával. Hozzárendelt IPv4 érték lesz az IP-cím a webalkalmazás számára.

Webes alkalmazásokhoz az IP-cím megkereséséhez kövesse [egyéni tartománynév konfigurálása az Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>3. lépés

A módosítások véglegesítéséhez, a rekordhalmaz. Használat `Set-AzureRMDnsRecordSet` tölthet fel a módosításokat az Azure DNS-rekordot:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Hozzon létre egy CNAME rekordot, az egyéni tartományhoz

Ha a tartomány már kezeli az Azure DNS (lásd: [tartomány DNS-delegálás](dns-domain-delegation.md), a következőt használhatja a példát, hogy hozzon létre egy CNAME rekordot a contoso.azurewebsites.net.

### <a name="step-1"></a>1. lépés

Nyissa meg a Powershellt, és hozzon létre egy új CNAME-rekordhalmazt, és rendelje hozzá egy változóhoz $rs. Ebben a példában létrehoz egy rekordhalmaz típus CNAME és a "Live idő" 600 másodperc "contoso.com" nevű DNS-zónát.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

A következő példa a válasz.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>2. lépés

Beállított CNAME rekord létrehozása után létre kell hozzon létre egy alias értéket, amely a web app mutasson.

Használja az előzőleg hozzárendelt változó "$rs" használhatja az alábbi PowerShell-paranccsal hozhat létre a webes alkalmazás contoso.azurewebsites.net alias.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

A következő példa a válasz.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>3. lépés

Hajtsa végre a módosításokat a `Set-AzureRMDnsRecordSet` parancsmagot:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

A rekord ellenőrizheti a "www.contoso.com" használatával az nslookup, ahogy az alábbi lekérdezésével megfelelően lett létrehozva:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Hozzon létre egy "awverify" rekordot, a web apps

Ha úgy dönt, hogy a webalkalmazás egy A rekordot használja, haladjon végig a annak érdekében, hogy Ön a tulajdonosa az egyéni tartomány-ellenőrzési folyamatot. Az ellenőrzési lépés végezhető el "awverify" nevű speciális CNAME rekord létrehozásához. Ez a szakasz csak olyan rekordokat vonatkozik.

### <a name="step-1"></a>1. lépés

Hozzon létre a "awverify" rekordot. Az alábbi példában hozunk létre a "aweverify" rekord, a contoso.com, az egyéni tartomány tulajdonjogának ellenőrzéséhez.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

A következő példa a válasz.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>2. lépés

A "awverify" rekordhalmaz létrehozása után rendeljen alias beállítása a CNAME-rekord. Az alábbi példában hozzárendeljük awverify.contoso.azurewebsites.net alias beállítása a CNAMe-rekord.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

A következő példa a válasz.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>3. lépés

Hajtsa végre a módosításokat a `Set-AzureRMDnsRecordSet cmdlet`, ahogyan az az alábbi parancsot.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>További lépések

Kövesse a [az App Service egyéni tartománynév beállítása](../app-service/app-service-web-tutorial-custom-domain.md) egy egyéni tartományt a WebApp konfigurálásához.
