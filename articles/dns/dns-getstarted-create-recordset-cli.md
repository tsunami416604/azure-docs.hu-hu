---
title: "DNS-rekordok létrehozása az Azure CLI használatával | Microsoft Docs"
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>DNS-rekordok létrehozása az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

Ez a cikk végigvezeti a rekordok és a rekordhalmazok Azure CLI használatával történő létrehozásának folyamatán.

## <a name="introduction"></a>Introduction (Bevezetés)

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.

## <a name="create-a-record-set-and-record"></a>Rekordhalmaz és rekord létrehozása

Ez a témakör a DNS-rekordok létrehozását ismerteti az Azure DNS-ben. A példák feltételezik, hogy már [telepítette az Azure parancssori felületet (CLI), bejelentkezett, és létrehozott egy DNS-zónát](dns-getstarted-create-dnszone-cli.md).

Az oldalon található összes példa az „A” DNS-rekordtípust használja. A további rekordtípusokkal, illetve a DNS-rekordok és -rekordhalmazok kezelésével kapcsolatban tekintse meg [A DNS-rekordok és -rekordhalmazok kezelése az Azure parancssori felület használatával](dns-operations-recordsets-cli.md) című cikket.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekordokat az `azure network dns record-set add-record` paranccsal lehet létrehozni. További segítségért lásd: `azure network dns record-set add-record -h`.

Egy rekord létrehozásakor meg kell adni az erőforráscsoport, a zóna és a rekordhalmaz nevét, a rekordtípust és a létrehozandó rekord részletes adatait.

Ha a rekordhalmaz még nem létezik, akkor a parancs létrehozza. Ha az adott rekordhalmaz már létezik, a parancs felveszi a megadott rekordot a meglévő rekordhalmazba. 

Új rekordhalmaz létrehozásakor az alapértelmezett élettartam (time-to-live, TTL) értéke 3600 lesz. A különböző TTL-ek használati utasításait az a cikk tartalmazza, amely a [DNS-rekordok Azure CLI-vel végzett kezelésével foglalkozik az Azure DNS-ben](dns-operations-recordsets-cli.md).

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoport *contoso.com* zónájában egy *www* nevű, „A” típusú rekordot hoz létre. Az „A” rekord IP-címe: *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Ha a zóna (ebben az esetben a „contoso.com”) legfelső pontján szeretne létrehozni egy rekordhalmazt, használja a "@", rekordnevet (az idézőjelekkel együtt):

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

A rekordadatok megadásához használt paraméterek a rekord típusától függnek. Az „A” típusú rekordok esetén például a `-a <IPv4 address>` paraméterrel lehet megadni az IPv4-címet. A további rekordtípusok paramétereivel kapcsolatos információkért lásd: `azure network dns record-set add-record -h`. Példák az egyes rekordtípusokra: [A DNS-rekordok és -rekordhalmazok kezelése az Azure CLI használatával](dns-operations-recordsets-cli.md).


## <a name="verify-name-resolution"></a>Névfeloldás ellenőrzése

Az Azure DNS-névkiszolgálókon található DNS-rekordokat a DNS-eszközök – például az nslookup, a DIG vagy a [Resolve-DnsName PowerShell-parancsmag](https://technet.microsoft.com/library/jj590781.aspx) – segítségével tesztelheti.

Ha még nem delegálta a tartományát az új zóna használatára az Azure DNS-ben, a [DNS-lekérdezést közvetlenül a zóna egyik névkiszolgálójához kell irányítani](dns-getstarted-create-dnszone.md#test-name-servers). Ügyeljen arra, hogy az alábbi parancsba a rekordzóna megfelelő értékeit helyettesítse be.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Következő lépések

[Tartománynév delegálása az Azure DNS-névkiszolgálókra](dns-domain-delegation.md)

[DNS-zónák kezelése az Azure parancssori felület használatával](dns-operations-dnszones-cli.md)

[DNS-rekordok és -rekordhalmazok kezelése az Azure parancssori felület használatával](dns-operations-recordsets-cli.md)




<!--HONumber=Dec16_HO3-->


