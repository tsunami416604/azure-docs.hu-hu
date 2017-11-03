---
title: "Az Azure DNS - Azure CLI 1.0 DNS-zónák kezelése |} Microsoft Docs"
description: "DNS-zónák Azure CLI 1.0 használatával kezelheti. Ez a cikk bemutatja, hogyan frissítése, törlése és a DNS-zóna létrehozása az Azure DNS szolgáltatásra."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.openlocfilehash: 588c87749f049eff5b9e0729f6769c8367ba41e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>Az Azure DNS az Azure CLI 1.0 használata DNS-zónák kezelése

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Ez az útmutató bemutatja, hogyan kezelheti a DNS-zónák platformfüggetlen Azure CLI 1.0 elérhető a Windows, Mac és Linux. Emellett kezelhetők a DNS-zónák használatával [Azure PowerShell](dns-operations-dnszones.md) vagy az Azure-portálon.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók

A következő CLI-verziók egyikével elvégezheti a feladatot:

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez.
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="introduction"></a>Bevezetés

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>Segítségkérés

Indítsa el az Azure DNS-vonatkozó összes CLI 1.0 parancsok `azure network dns`. Súgó áll rendelkezésre egyes parancs használatával a `--help` beállítás (rövid alak `-h`).  Példa:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `azure network dns zone create` parancs használatával hozható létre. További segítségért lásd: `azure network dns zone create -h`.

Az alábbi példa létrehoz egy DNS-zónát *contoso.com* erőforráscsoportban nevű *MyResourceGroup*:

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>A DNS-zónát címkékkel létrehozása

A következő példa bemutatja, hogyan hozzon létre egy DNS-zónát és két [Azure Resource Manager címkéket](dns-zones-records.md#tags), *project = demo* és *env = test*, segítségével a `--tags` paraméter ( rövid alak `-t`):

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>A DNS-zóna beolvasása

A DNS-zónák lekéréséhez használja `azure network dns zone show`. További segítségért lásd: `azure network dns zone show -h`.

Az alábbi példában a DNS-zóna adja vissza *contoso.com* és kapcsolódó adataik erőforráscsoportból *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

A következő példa a válasz.

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Megjegyzés: a DNS-rekordokat nem által visszaadott `azure network dns zone show`. A DNS-rekordok listában használja `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Lista DNS-zónák

Operációs rendszer DNS-zónák, használjon `azure network dns zone list`. További segítségért lásd: `azure network dns zone list -h`.

Adja meg az erőforráscsoport csak az erőforráscsoporton belül zónák sorolja fel:

```azurecli
azure network dns zone list MyResourceGroup
```

Az előfizetés minden zóna az erőforráscsoport kihagyásával sorolja fel:

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>A DNS-zóna frissítéséhez

DNS-zóna erőforráshoz módosítás használatával `azure network dns zone set`. További segítségért lásd: `azure network dns zone set -h`.

Ez a parancs frissíti a DNS-rekordhalmazok a zónán belül (lásd: [kezelése DNS-rekordok hogyan](dns-operations-recordsets-cli-nodejs.md)). Csak a zóna erőforrás maga tulajdonságainak frissítésére szolgál. Ezeket a tulajdonságokat a rendszer jelenleg csak a [Azure Resource Manager "címke"](dns-zones-records.md#tags) a zóna erőforrás.

A következő példa bemutatja, hogyan a címke van megadva a DNS-zónák frissítéséhez. A meglévő címkék megadott helyett.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>A DNS-zóna törlése

DNS-zónák törölhetők segítségével `azure network dns zone delete`. További segítségért lásd: `azure network dns zone delete -h`.

> [!NOTE]
> Is egy DNS-zóna törlésével törli az összes DNS-rekordokat a zónán belül. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zóna szolgáltatásokat sikertelen lesz a zóna törlődik.
>
>A zóna véletlen törlés elleni védelem érdekében, lásd: [hogyan védi a DNS-zónák és rekordok](dns-protect-zones-recordsets.md).

Ez a parancs felszólítja megerősítést kér. A választható `--quiet` váltás (rövid alak `-q`) letiltja a parancssorhoz.

A következő példa bemutatja, hogyan törölni a zónát *contoso.com* erőforráscsoportból *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [rekordhalmazokat és rekordokat kezelése](dns-getstarted-create-recordset-cli-nodejs.md) a DNS-zónában.

Megtudhatja, hogyan [tartomány delegálása az Azure DNS-](dns-domain-delegation.md).

