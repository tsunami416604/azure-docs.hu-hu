---
title: "DNS-zóna létrehozása az Azure CLI 2.0 használatával| Microsoft Docs"
description: "Ez a témakör a DNS-zónák létrehozását ismerteti az Azure DNS-ben. Egy lépésenkénti útmutató, amelynek segítségével az Azure CLI 2.0 használatával létrehozhatja és kezelheti az első DNS-zónáját."
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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 02/28/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Azure DNS-zóna létrehozása az Azure CLI 2.0 használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

Ez a cikk bemutatja a DNS-zónák létrehozásának lépéseit a platformfüggetlen Azure parancssori felület (CLI) használatával, amely Windows, Mac és Linux platformokon is elérhető. A DNS-zónákat az [Azure PowerShell](dns-getstarted-create-dnszone.md) használatával, illetve az [Azure Portalon](dns-getstarted-create-dnszone-portal.md) is létrehozhatja.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók

A következő CLI-verziók egyikével elvégezheti a feladatot:

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez.
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="introduction"></a>Bevezetés

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Az Azure CLI 2.0 beállítása az Azure DNS-hez

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Telepítse az Azure parancssori felület (CLI) legújabb verzióját, amely Windows, Linux és Mac platformon is elérhető. További információt az [Azure CLI 2.0 telepítése](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2) című cikkben olvashat.

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyisson meg egy konzolablakot, adja meg a saját hitelesítő adatait. További információt az Azure parancssori felületből (CLI) Azure-ba történő bejelentkezést ismertető cikkben talál.

```azurecli
az login
```

### <a name="select-the-subscription"></a>Válassza ki az előfizetést

Keresse meg a fiókot az előfizetésekben.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Válassza ki, hogy melyik Azure előfizetést fogja használni.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. A paranccsal kapcsolatos súgó megtekintéséhez írja be a következőt: `az network dns zone create --help`.

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoportban létrehozza a *contoso.com* DNS-zónát. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>A DNS-zóna ellenőrzése

### <a name="view-records"></a>A rekordok megtekintése

A DNS-zónák létrehozásával a következő DNS-rekordok is létrejönnek:

* Az *SOA*-rekord. Ez a rekord minden DNS-zóna gyökerében megtalálható.
* A mérvadó névkiszolgálói (NS-) rekordok. Ezek a rekordok jelenítik meg, hogy mely névkiszolgálók üzemeltetik a zónát. Az Azure DNS névkiszolgálókból álló készletet használ, így a különböző névkiszolgálók különböző zónákhoz tartozhatnak az Azure DNS szolgáltatásban. További információ: [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md).

A rekordok megtekintéséhez a `az network dns record-set list` parancsot használhatja:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

A(z) `az network dns record-set list` válasza az alábbiakban látható:

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> A DNS-zóna gyökerében (más néven *legfelső pontján*) található rekordhalmazok az **@** karaktert használják a rekordhalmaz neveként.

### <a name="test-name-servers"></a>Névkiszolgálók tesztelése

Az Azure DNS-névkiszolgálókon található DNS-zónákat a DNS-eszközök – például az nslookup, a dig vagy a `Resolve-DnsName` PowerShell-parancsmag – segítségével tesztelheti.

Ha még nem delegálta a tartományát az új zóna használatára az Azure DNS-ben, a DNS-lekérdezést közvetlenül a zónája egyik névkiszolgálójához kell irányítani. A zóna névkiszolgálói a mérvadó névkiszolgálói rekordokban vannak megadva, az `az network dns record-set list` paranccsal megjelenített módon.

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


