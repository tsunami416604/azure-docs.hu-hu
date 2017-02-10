---
title: "DNS-zóna létrehozása a parancssori felület (CLI) használatával| Microsoft Docs"
description: "Ez a témakör a DNS-zónák létrehozását ismerteti az Azure DNS-ben. Egy lépésenkénti útmutató, amelynek segítségével az Azure parancssori felületen keresztül létrehozhatja és kezelheti az első DNS-zónáját."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Azure DNS-zóna létrehozása a parancssori felület használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

Ez a cikk bemutatja a DNS-zónák létrehozásának lépéseit a platformfüggetlen Azure parancssori felület (CLI) használatával, amely Windows, Mac és Linux platformokon is elérhető. A DNS-zónákat az [Azure PowerShell](dns-getstarted-create-dnszone.md) használatával, illetve az [Azure Portalon](dns-getstarted-create-dnszone-portal.md) is létrehozhatja.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `azure network dns zone create` parancs használatával hozható létre. A paranccsal kapcsolatos súgó megtekintéséhez írja be a következőt: `azure network dns zone create -h`.

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoportban létrehozza a *contoso.com* DNS-zónát. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>A DNS-zóna ellenőrzése

### <a name="view-records"></a>A rekordok megtekintése

A DNS-zónák létrehozásával a következő DNS-rekordok is létrejönnek:

* Az *SOA*-rekord. Ez a rekord minden DNS-zóna gyökerében megtalálható.
* A mérvadó névkiszolgálói (NS-) rekordok. Ezek a rekordok jelenítik meg, hogy mely névkiszolgálók üzemeltetik a zónát. Az Azure DNS névkiszolgálókból álló készletet használ, így a különböző névkiszolgálók különböző zónákhoz tartozhatnak az Azure DNS szolgáltatásban. További információ: [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md).

A rekordok megtekintéséhez a `azure network dns-record-set list` parancsot használhatja:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> A DNS-zóna gyökerében (más néven *legfelső pontján*) található rekordhalmazok az **@** karaktert használják a rekordhalmaz neveként.

### <a name="test-name-servers"></a>Névkiszolgálók tesztelése

Az Azure DNS-névkiszolgálókon található DNS-zónákat a DNS-eszközök – például az nslookup, a dig vagy a `Resolve-DnsName` PowerShell-parancsmag – segítségével tesztelheti.

Ha még nem delegálta a tartományát az új zóna használatára az Azure DNS-ben, a DNS-lekérdezést közvetlenül a zónája egyik névkiszolgálójához kell irányítani. A zóna névkiszolgálói a mérvadó névkiszolgálói rekordokban vannak megadva, az `azure network dns record-set list` paranccsal megjelenített módon.

Az alábbi példa a „dig” parancs segítségével és a DNS-zónához hozzárendelt névkiszolgálók használatával kérdezi le a contoso.com tartományt. Ügyeljen arra, hogy a saját zónája megfelelő értékeit helyettesítse be.

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>Következő lépések

Miután létrehozta a DNS-zónát, [hozzon létre benne DNS-rekordhalmazokat és -rekordokat](dns-getstarted-create-recordset-cli.md).




<!--HONumber=Feb17_HO1-->


