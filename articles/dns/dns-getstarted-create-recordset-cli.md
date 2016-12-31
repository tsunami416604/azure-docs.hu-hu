---
title: "Rekordhalmaz és rekordok létrehozása egy DNS-zóna számára a parancssori felület használatával| Microsoft Docs"
description: "Állomásrekordok létrehozása az Azure DNS számára. Rekordhalmazok és rekordok beállítása a parancssori felület használatával"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: e377f176fe24a8e7e42d409f86d6b0093ce5e7c4

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>DNS-rekordhalmazok és -rekordok létrehozása a parancssori felület használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

Ez a cikk végigvezeti a rekordok és a rekordhalmazok parancssori felület használatával történő létrehozásának folyamatán. Ehhez tisztában kell lennie a DNS-rekordok és a rekordhalmazok jelentésével.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Ez a témakör a DNS-rekordok létrehozását ismerteti az Azure DNS-ben. A példák feltételezik, hogy már [telepítette az Azure parancssori felületet (CLI), bejelentkezett, és létrehozott egy DNS-zónát](dns-getstarted-create-dnszone-cli.md).

Az oldalon található összes példa az „A” DNS-rekordtípust használja. A további rekordtípusokkal, illetve a DNS-rekordok és -rekordhalmazok kezelésével kapcsolatban tekintse meg [A DNS-rekordok és -rekordhalmazok kezelése az Azure parancssori felület használatával](dns-operations-recordsets-cli.md) című cikket.

## <a name="create-a-record-set-and-record"></a>Rekordhalmaz és rekord létrehozása

Ebben a szakaszban egy rekordhalmaz és néhány rekord létrehozását mutatjuk be. A példában egy olyan rekordhalmazt hozunk létre, amely a „www” relatív névvel rendelkezik a „contoso.com” DNS-zónában. A rekordok teljesen minősített neve „www.contoso.com”. A rekordtípus „A”, az élettartam (TTL) pedig 60 másodperc. A lépés befejezése után egy üres rekordhalmaz fog a rendelkezésére állni.

Ha a zóna (ebben az esetben a „contoso.com”) legfelső pontján szeretne létrehozni egy rekordot, használja az "@", rekordnevet (az idézőjelekkel együtt). Ez a DNS-rendszerben általános egyezmény.

### <a name="1-create-a-record-set"></a>1. Rekordhalmaz létrehozása

Ha az új rekord neve és típusa megegyezik egy már létező rekordéval, akkor a létező rekordhalmazhoz kell hozzáadnia. Ezt a lépést kihagyhatja, és továbbléphet az alábbi [Rekordok hozzáadása](#add-records) részhez. Ha az új rekord neve és típusa nem egyezik egyetlen létező rekordéval sem, akkor egy új rekordhalmazt kell létrehoznia.

Rekordhalmazt a `azure network dns record-set create` parancs használatával hozhat létre. További segítségért lásd: `azure network dns record-set create -h`.  

Egy rekordhalmaz létrehozásakor meg kell adnia a rekordhalmaz nevét, a zónát, az élettartamot és a rekordtípust. 

```azurecli
azure network dns record-set create myresourcegroup contoso.com www A 60
```

A lépés befejezése után egy üres „www” rekordhalmaz fog a rendelkezésére állni. Az újonnan létrehozott „www” rekordhalmaz használatához először rekordokat kell hozzáadni.

### <a name="2-add-records"></a>2. Rekordok hozzáadása

A rekordokat az `azure network dns record-set add-record` parancs használatával adhatja hozzá a rekordhalmazokhoz. További segítségért lásd: `azure network dns record-set add-record -h`.

A rekordok rekordhalmazhoz adásának paraméterei a rekordhalmaz típusától függően eltérnek. Az „A” típusú rekordhalmazok használata esetén például csak az `-a <IPv4 address>` paraméterrel lehet megadni a rekordokat. A további rekordtípusok paramétereivel kapcsolatos információkért lásd: `azure network dns record-set add-record -h`.

A „www” rekordhalmazhoz a következő paranccsal adhat hozzá egy A típusú rekordot:

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 1.2.3.4
```

### <a name="verify-name-resolution"></a>Névfeloldás ellenőrzése

Az Azure DNS-névkiszolgálókon található DNS-rekordokat a DNS-eszközök – például az nslookup, a DIG vagy a [Resolve-DnsName PowerShell-parancsmag](https://technet.microsoft.com/library/jj590781.aspx) – segítségével tesztelheti.

Ha még nem delegálta a tartományát az új zóna használatára az Azure DNS-ben, a [DNS-lekérdezést közvetlenül a zóna egyik névkiszolgálójához kell irányítani](dns-getstarted-create-dnszone.md#test-name-servers). Ügyeljen arra, hogy az alábbi parancsba a rekordzóna megfelelő értékeit helyettesítse be.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Következő lépések

[Tartománynév delegálása az Azure DNS-névkiszolgálókra](dns-domain-delegation.md)

[DNS-zónák kezelése az Azure parancssori felület használatával](dns-operations-dnszones-cli.md)

[DNS-rekordok és -rekordhalmazok kezelése az Azure parancssori felület használatával](dns-operations-recordsets-cli.md)




<!--HONumber=Dec16_HO2-->


