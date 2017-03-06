---
title: "DNS-rekordok létrehozása az Azure CLI 2.0 használatával | Microsoft Docs"
description: "Állomásrekordok létrehozása az Azure DNS számára. Rekordhalmazok és rekordok beállítása az Azure CLI 2.0 használatával"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: ca713647a9f795d7803c32eac7fba9b5b6ac95cf
ms.lasthandoff: 02/28/2017

---

# <a name="how-to-create-dns-records-using-the-azure-cli-20"></a>DNS-rekordok létrehozása az Azure CLI 2.0 használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

Ez a cikk végigvezeti a rekordok és a rekordhalmazok Azure CLI használatával történő létrehozásának folyamatán.

## <a name="introduction"></a>Introduction (Bevezetés)

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók

A következő CLI-verziók egyikével elvégezheti a feladatot:

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez.
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="create-a-record-set-and-record"></a>Rekordhalmaz és rekord létrehozása

Ez a témakör a DNS-rekordok létrehozását ismerteti az Azure DNS-ben. A példák feltételezik, hogy már [telepítette az Azure CLI 2.0-t, bejelentkezett, és létrehozott egy DNS-zónát](dns-getstarted-create-dnszone-cli.md).

Az oldalon található összes példa az „A” DNS-rekordtípust használja. A további rekordtípusokkal, illetve a DNS-rekordok és -rekordhalmazok kezelésével kapcsolatban tekintse meg [A DNS-rekordok és -rekordhalmazok kezelése az Azure CLI 2.0 használatával](dns-operations-recordsets-cli.md) című cikket.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekordod létrehozásához használja az `az network dns record-set ? add` parancsot (ahol a ? a rekordtípus). További segítségért lásd: `az network dns record-set --help`.

Egy rekord létrehozásakor meg kell adni az erőforráscsoport, a zóna és a rekordhalmaz nevét, a rekordtípust és a létrehozandó rekord részletes adatait.

Ha a rekordhalmaz még nem létezik, akkor a parancs létrehozza. Ha az adott rekordhalmaz már létezik, a parancs felveszi a megadott rekordot a meglévő rekordhalmazba. 

Új rekordhalmaz létrehozásakor az alapértelmezett élettartam (time-to-live, TTL) értéke 3600 lesz. A különböző TTL-ek használati utasításait az a cikk tartalmazza, amely a [DNS-rekordok Azure CLI 2.0-val végzett kezelésével foglalkozik az Azure DNS-ben](dns-operations-recordsets-cli.md).

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoport *contoso.com* zónájában egy *www* nevű, „A” típusú rekordot hoz létre. Az „A” rekord IP-címe: *1.2.3.4*.

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Ha a zóna (ebben az esetben a „contoso.com”) legfelső pontján szeretne létrehozni egy rekordot, használja a „@” rekordnevet (az idézőjelekkel együtt):

```azurecli
az network dns record-set a --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

A rekordadatok megadásához használt paraméterek a rekord típusától függnek. Az „A” típusú rekordok esetén például a `--ipv4-address <IPv4 address>` paraméterrel lehet megadni az IPv4-címet. A további rekordtípusok paramétereivel kapcsolatos információkért lásd: `az network dns record --help`. Példák az egyes rekordtípusokra: [A DNS-rekordok és -rekordhalmazok kezelése az Azure CLI 2.0 használatával](dns-operations-recordsets-cli.md).


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

[DNS-zónák kezelése az Azure CLI 2.0 használatával](dns-operations-dnszones-cli.md)

[DNS-rekordok és -rekordhalmazok kezelése az Azure CLI 2.0 használatával](dns-operations-recordsets-cli.md)


