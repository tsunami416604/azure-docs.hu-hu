---
title: "A webes alkalmazás egyéni DNS-rekordok létrehozása |} Microsoft Docs"
description: "Hogyan hozhat létre egyéni tartományt a webalkalmazás Azure DNS használatával DNS-rekordokat."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d4b0aa817c3fd7f3304b5122ac584166d8079d3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Egyéni tartomány DNS-rekordok webalkalmazás létrehozása

Azure DNS az egyéni tartománynév üzemeltetéséhez a web Apps használatával. Azure-webalkalmazás létrehozásakor és azt szeretné, hogy a felhasználók által eléréséhez például contoso.com, vagy a www.contoso.com teljes tartománynév használatával.

Ehhez hozzon létre két rekordot rendelkezik:

* Válassza a contoso.com gyökérrekordként "A"
* A www-név, amely A-rekordra mutat "CNAME" rekord

Ne feledje, hogy az A rekord egy webalkalmazás létrehozásakor az Azure-ban, az A rekordot kell manuálisan frissíteni, ha az alapul szolgáló IP-cím a webes alkalmazás módosítások.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, először hozzon létre egy DNS-zónát az Azure DNS-, és a zóna delegálása az Azure DNS a tartományregisztráló.

1. Hozzon létre egy DNS-zónát, kövesse a lépéseket a [hozzon létre egy DNS-zóna](dns-getstarted-create-dnszone.md).
2. A DNS, az Azure DNS-delegálását, kövesse a lépéseket a [DNS-tartomány delegálás](dns-domain-delegation.md).

Zóna létrehozása, és azt az Azure DNS-delegálás, után is létrehozhat rögzíti az egyéni tartományhoz.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Az egyéni tartomány az A rekord létrehozása

Az A rekord segítségével hozzárendelhet egy nevet az IP-címét. Az alábbi példa azt fogja rendeljen egy A rekordot egy IPv4-címére:

### <a name="step-1"></a>1. lépés

Az A rekord létrehozása és hozzárendelése egy változó $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>2. lépés

Az IPv4-alapú értékkel adja hozzá a korábban létrehozott rekordkészlet "@" a hozzárendelt $rs változó segítségével. Hozzárendelt IPv4 érték lesz a webalkalmazáshoz tartozó IP-címét.

Az IP-cím keresése a webes alkalmazás, kövesse a lépéseket a [egyéni tartománynév beállítása az Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>3. lépés

A változtatások véglegesítése a rekordhalmaz. Használjon `Set-AzureRMDnsRecordSet` feltölteni az Azure DNS-rekordot a módosításokat:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Az egyéni tartomány CNAME rekord létrehozása

Ha a tartomány már az Azure DNS kezeli (lásd: [DNS-tartomány delegálás](dns-domain-delegation.md), az alábbi contoso.azurewebsites.net CNAME rekord létrehozása a példát.

### <a name="step-1"></a>1. lépés

Nyissa meg a Powershellt, és hozzon létre egy új CNAME rekordkészlete, és rendelje hozzá egy változó $rs. Ebben a példában létrehoz egy "élő idő" rekordhalmaz típus CNAME 600 másodperc neve "contoso.com" DNS-zónában.

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

A CNAME-rekordhalmaz létrehozása után szeretne létrehozni a webalkalmazást mutasson alias értéke.

A korábban hozzárendelt változó "$rs" használatával az alábbi PowerShell-parancsot a webes alkalmazás contoso.azurewebsites.net aliasa létrehozásához.

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

Véglegesítse a módosításokat a `Set-AzureRMDnsRecordSet` parancsmagot:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Ellenőrizheti, hogy a rekord megfelelően lett létrehozva a "www.contoso.com" nslookup, használatával lekérdezésével alább látható módon:

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

## <a name="create-an-awverify-record-for-web-apps"></a>A webalkalmazások egy "awverify" rekord létrehozása

Ha úgy dönt, hogy az A rekord használja a webalkalmazás, haladjon végig a annak érdekében, hogy Ön a tulajdonosa az egyéni tartomány-ellenőrzési folyamatot. Az ellenőrzési lépés "awverify" nevű különleges CNAME rekord létrehozásával történik. Ez a szakasz csak egy rekordok vonatkozik.

### <a name="step-1"></a>1. lépés

A "awverify" rekordot kell létrehozni. Az alábbi példában létre fogunk hozni a "aweverify" rekord contoso.com a tulajdonjogát az egyéni tartomány ellenőrzéséhez.

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

A "awverify" rekordhalmaz létrehozása után rendelje hozzá a CNAME-rekordhalmazt alias. Az alábbi példa azt a CNAMe rekord awverify.contoso.azurewebsites.net alias beállítása rendeli.

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

Véglegesítse a módosításokat a `Set-AzureRMDnsRecordSet cmdlet`, ahogy az alábbi parancsot.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Következő lépések

Kövesse a [beállítása egy egyéni tartománynevet, az App Service](../app-service/app-service-web-tutorial-custom-domain.md) egyéni tartományt webalkalmazás konfigurálása.
