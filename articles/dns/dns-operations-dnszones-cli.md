---
title: "Az Azure DNS - Azure CLI 2.0 DNS-zónák kezelése |} Microsoft Docs"
description: "Kezelheti a DNS-zónákat az Azure CLI 2.0 verziót használja. Ez a cikk bemutatja, hogyan frissítése, törlése és a DNS-zóna létrehozása az Azure DNS szolgáltatásra."
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: kumud
ms.openlocfilehash: 2042d9c2864a4f8da474e0df38882414bfe3417e
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-20"></a>Az Azure DNS az Azure CLI 2.0 használatával DNS-zónák kezelése

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)


Ez az útmutató bemutatja a platformok közötti Azure parancssori felület használatával, amely érhető el a Windows, Mac és Linux a DNS-zónák kezelése. Emellett kezelhetők a DNS-zónák használatával [Azure PowerShell](dns-operations-dnszones.md) vagy az Azure-portálon.

## <a name="introduction"></a>Bevezetés

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Az Azure CLI 2.0 beállítása az Azure DNS-hez

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Az Azure CLI 2.0-t, Windows, Linux és Mac legújabb verziójának telepítése További információt az [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-az-cli2) című cikkben olvashat.

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyisson meg egy konzolablakot, adja meg a saját hitelesítő adatait. További információt az Azure parancssori felületből (CLI) Azure-ba történő bejelentkezést ismertető cikkben talál.

```
az login
```

### <a name="select-the-subscription"></a>Válassza ki az előfizetést

Keresse meg a fiókot az előfizetésekben.

```
az account list
```

Válassza ki, hogy melyik Azure előfizetést fogja használni.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Segítségkérés

Indítsa el az Azure DNS-vonatkozó összes CLI 2.0 parancsok `az network dns`. Súgó áll rendelkezésre egyes parancs használatával a `--help` beállítás (rövid alak `-h`).  Példa:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. További segítségért lásd: `az network dns zone create -h`.

Az alábbi példa létrehoz egy DNS-zónát *contoso.com* erőforráscsoportban nevű *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>A DNS-zónát címkékkel létrehozása

A következő példa bemutatja, hogyan hozzon létre egy DNS-zónát és két [Azure Resource Manager címkéket](dns-zones-records.md#tags), *project = demo* és *env = test*, segítségével a `--tags` paraméter ( rövid alak `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>A DNS-zóna beolvasása

A DNS-zónák lekéréséhez használja `az network dns zone show`. További segítségért lásd: `az network dns zone show --help`.

Az alábbi példában a DNS-zóna adja vissza *contoso.com* és kapcsolódó adataik erőforráscsoportból *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

A következő példa a válasz.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Megjegyzés: a DNS-rekordokat nem által visszaadott `az network dns zone show`. A DNS-rekordok listában használja `az network dns record-set list`.


## <a name="list-dns-zones"></a>Lista DNS-zónák

Operációs rendszer DNS-zónák, használjon `az network dns zone list`. További segítségért lásd: `az network dns zone list --help`.

Adja meg az erőforráscsoport csak az erőforráscsoporton belül zónák sorolja fel:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Az előfizetés minden zóna az erőforráscsoport kihagyásával sorolja fel:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>A DNS-zóna frissítéséhez

DNS-zóna erőforráshoz módosítás használatával `az network dns zone update`. További segítségért lásd: `az network dns zone update --help`.

Ez a parancs frissíti a DNS-rekordhalmazok a zónán belül (lásd: [kezelése DNS-rekordok hogyan](dns-operations-recordsets-cli.md)). Csak a zóna erőforrás maga tulajdonságainak frissítésére szolgál. Ezeket a tulajdonságokat a rendszer jelenleg csak a [Azure Resource Manager "címke"](dns-zones-records.md#tags) a zóna erőforrás.

A következő példa bemutatja, hogyan a címke van megadva a DNS-zónák frissítéséhez. A meglévő címkék megadott helyett.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>A DNS-zóna törlése

DNS-zónák törölhetők segítségével `az network dns zone delete`. További segítségért lásd: `az network dns zone delete --help`.

> [!NOTE]
> Is egy DNS-zóna törlésével törli az összes DNS-rekordokat a zónán belül. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zóna szolgáltatásokat sikertelen lesz a zóna törlődik.
>
>A zóna véletlen törlés elleni védelem érdekében, lásd: [hogyan védi a DNS-zónák és rekordok](dns-protect-zones-recordsets.md).

Ez a parancs felszólítja megerősítést kér. A választható `--yes` letiltja a parancssorhoz.

A következő példa bemutatja, hogyan törölni a zónát *contoso.com* erőforráscsoportból *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [rekordhalmazokat és rekordokat kezelése](dns-getstarted-create-recordset-cli.md) a DNS-zónában.

Megtudhatja, hogyan [tartomány delegálása az Azure DNS-](dns-domain-delegation.md).

