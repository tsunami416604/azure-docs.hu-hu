---
title: Az Azure DNS – Azure CLI 2.0 DNS-zónák kezelése |} A Microsoft Docs
description: DNS-zónák az Azure CLI 2.0-val kezelheti. Ez a cikk bemutatja, hogyan frissítése, törlése és az Azure DNS DNS-zónák létrehozását.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: victorh
ms.openlocfilehash: c81d8add7ae37e03a98da0ad86deaa1d9d7ec8e0
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172619"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-20"></a>Hogyan kezelheti a DNS-zónák az Azure DNS az Azure CLI 2.0 használatával

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)


Ez az útmutató bemutatja, hogyan kezelheti a DNS-zónák használatával a platformfüggetlen Azure parancssori felület, amely Windows, Mac és Linux rendszereken érhető el. Emellett a DNS-zónák használata kezelheti [Azure PowerShell-lel](dns-operations-dnszones.md) vagy az Azure Portalon.

Ez az útmutató kifejezetten a nyilvános DNS-zónák foglalkozik. Kezelheti az Azure DNS saját zónák az Azure parancssori felület használatával kapcsolatos információkért lásd: [Ismerkedés az Azure CLI 2.0 használatával az Azure DNS Private Zones](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Bevezetés

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Az Azure CLI 2.0 beállítása az Azure DNS-hez

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Telepítse a legújabb verzióját, az Azure CLI 2.0, elérhető Windows, Linux és Mac rendszerre. További információt az [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-az-cli2) című cikkben olvashat.

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyisson meg egy konzolablakot, adja meg a saját hitelesítő adatait. További információt az [Azure parancssori felületből (CLI) Azure-ba történő bejelentkezést ismertető cikkben](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) talál.

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

### <a name="optional-to-installuse-azure-dns-private-zones-feature-public-preview"></a>Választható lehetőség: Telepítése és használata az Azure DNS saját zónák funkció (nyilvános előzetes verzió)
Az Azure DNS saját zóna funkció nyilvános előzetes verziója az Azure CLI bővítményeként jelent meg. A „dns” Azure CLI-bővítmény telepítése 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Segítség kérése

Az összes CLI 2.0-parancsok az Azure DNS-vonatkozó kezdje `az network dns`. Súgó érhető el minden parancs használatával a `--help` lehetőséget (krátká forma `-h`).  Példa:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. További segítségért lásd: `az network dns zone create -h`.

A következő példában létrehozunk egy DNS-zónát *contoso.com* az erőforráscsoport neve *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>A címkékkel rendelkező DNS-zóna létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre két DNS-zóna [Azure Resource Manager-címkék](dns-zones-records.md#tags), *project = demo* és *env = test*, használatával a `--tags` paraméter () rövid alak `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>DNS-zóna lekérése

DNS-zóna lekéréséhez használja `az network dns zone show`. További segítségért lásd: `az network dns zone show --help`.

Az alábbi példa adja vissza a DNS-zóna *contoso.com* és a hozzá tartozó adatokat erőforráscsoportból *MyResourceGroup*. 

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

Vegye figyelembe, hogy az `az network dns zone show` nem ad vissza DNS-rekordokat. A DNS-rekordok listázásához az `az network dns record-set list` használható.


## <a name="list-dns-zones"></a>DNS-zónák listázása

A DNS-zónák felsorolásához használja az `az network dns zone list` parancsot. További segítségért lásd: `az network dns zone list --help`.

Az erőforráscsoport megadásakor a rendszer csak az adott csoporton belüli zónákat sorolja fel:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Az erőforráscsoport kihagyásakor a rendszer az előfizetésben található összes zónát felsorolja:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS-zóna frissítése

A DNS-zóna erőforrásai az `az network dns zone update` paranccsal módosíthatók. További segítségért lásd: `az network dns zone update --help`.

Ez a parancs nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd: [DNS-rekordok kezelése](dns-operations-recordsets-cli.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Ezek a tulajdonságok akkor jelenleg csak a [Azure Resource Manager "címkék"](dns-zones-records.md#tags) a zóna erőforrás.

Az alábbi példa bemutatja, hogyan frissítheti a a címkéket a DNS-zóna. A meglévő címkéket helyébe a megadott érték.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

A DNS-zónák törléséhez használja az `az network dns zone delete` parancsot. További segítségért lásd: `az network dns zone delete --help`.

> [!NOTE]
> A DNS-zónák törlésével a zónában található összes DNS-rekord is törlődni fog. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zónát használó szolgáltatások futtatása meghiúsul a zóna törlésekor.
>
>A zónák véletlen törlése elleni védelemről további információt talál a [DNS-zónák és -rekordok védelme](dns-protect-zones-recordsets.md) című szakaszban.

Ez a parancs megerősítést kér. A választható `--yes` kapcsoló kiiktatja a megerősítés kérését.

Az alábbi példa bemutatja, hogyan lehet törölni a zóna *contoso.com* erőforráscsoportból *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [rekordhalmazok és -rekordok kezelése](dns-getstarted-create-recordset-cli.md) a DNS-zónában.

Ismerje meg, hogyan [tartomány delegálása az Azure DNS](dns-domain-delegation.md).

