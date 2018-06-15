---
title: Az Azure DNS saját zónái használatának első lépései az Azure CLI 2.0-val | Microsoft Docs
description: A cikkből megtudhatja, hogyan hozhat létre saját DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel az Azure CLI 2.0 használatával létrehozhatja és kezelheti az első saját DNS-zónáját és -rekordját.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30191449"
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Az Azure DNS saját zónák használatának első lépései az Azure CLI 2.0-val

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

Ez a cikk bemutatja az első saját DNS-zóna és -rekord létrehozásának lépéseit a platformfüggetlen Azure CLI 2.0 használatával, amely Windows, Mac és Linux platformokon is elérhető. Ezeket a lépéseket az Azure PowerShell használatával is elvégezheti.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. A saját DNS-zóna virtuális hálózaton történő közzétételéhez meg kell adnia azon virtuális hálózatok listáját, amelyek számára engedélyezett a zónán belüli rekordok feloldása.  Ezeket „feloldási virtuális hálózatoknak” nevezzük.  Megadhat olyan virtuális hálózatot is, amelyhez az Azure DNS eszköznévrekordokat biztosít a virtuális gépek létrehozásakor, IP-címének módosításakor vagy megsemmisülésekor.  Ezt „regisztrációs virtuális hálózatnak” nevezzük.

Ezek az utasítások feltételezik, hogy már telepítette az Azure CLI 2.0-t, amelybe be is jelentkezett, valamint telepítette a szükséges CLI-bővítményt, amely támogatja a saját zónákat. További segítségért lásd [a DNS-zónák az Azure CLI 2.0 használatával való kezelésével kapcsolatos](dns-operations-dnszones-cli.md) témakört.

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Az Azure DNS saját zónák funkció (nyilvános előzetes verzió) telepítése és használata
Az Azure DNS saját zóna funkció nyilvános előzetes verziója az Azure CLI bővítményeként jelent meg. A „dns” Azure CLI-bővítmény telepítése 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

A DNS-zóna létrehozása előtt egy erőforráscsoportot kell létrehozni, amely a DNS-zónát tartalmazza majd. Az alábbiakban a parancs látható.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Saját DNS-zóna létrehozása

A saját DNS-zóna az `az network dns zone create` parancs használatával hozható létre. A paranccsal kapcsolatos súgó megtekintéséhez írja be a következőt: `az network dns zone create --help`.

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoportban létrehozza a *contoso.local* nevű saját DNS-zónát, majd elérhetővé teszi (hivatkozza) azt a *MyAzureVnet* nevű virtuális hálózat számára a resolution-vnets paraméterrel. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Megjegyzés: A fenti példában a „MyAzureVnet” virtuális hálózat ugyanahhoz az erőforráscsoporthoz és előfizetéshez tartozik, mint a saját zóna. Ha egy másik erőforráscsoporthoz vagy előfizetéshez tartozó virtuális hálózatra kíván hivatkozni, a --resolution-vnets paraméterhez a teljes Azure Resource Manager-azonosítót kell megadnia, nem elég a virtuális hálózat neve. 

Ha arra van szükség, hogy az Azure automatikusan hozzon létre gazdanév-rekordokat a zónában, a *resolution-vnets* helyett használja a *registration-vnets* paramétert.  A feloldás a regisztrációs virtuális hálózatok számára automatikusan engedélyezett.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekordokat az `az network dns record-set [record type] add-record` paranccsal lehet létrehozni. Az A-rekordokkal kapcsolatos segítségért például lásd: `azure network dns record-set A add-record --help`.

Az alábbi példa a „MyResourceGroup” erőforráscsoport „contoso.local” DNS-zónájában egy „ip1” relatív nevű rekordot hoz létre. A beállított rekord teljes neve „ip1.contoso.local”. A rekord típusa „A”, az IP-címe pedig „10.0.0.1”.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Más rekordtípusok, több rekordot tartalmazó rekordhalmazok, alternatív élettartam-értékek és meglévő rekordok módosítása esetén lásd: [DNS-rekordok és -rekordhalmazok kezelése az Azure CLI 2.0 használatával](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Saját DNS-zóna lekérése

Egy saját DNS-zóna lekéréséhez használja az `az network dns zone show` parancsot. További segítségért lásd: `az network dns zone show --help`.

Az alábbi példa a *contoso.local* DNS-zónát és a hozzá tartozó adatokat adja vissza a *MyResourceGroup* nevű erőforráscsoportból. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

A következő példa a válasz.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
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

Ez a parancs nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd: [DNS-rekordok kezelése](dns-operations-recordsets-cli.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. A saját zónák esetében frissítheti a zónához társított regisztrációs vagy feloldási virtuális hálózatot. 

Az alábbi példa bemutatja, hogyan frissíthető egy saját DNS-zónához társított feloldási virtuális hálózat. A rendszer a meglévő társított feloldási virtuális hálózatot lecseréli a megadott új virtuális hálózatra.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

A DNS-zónák törléséhez használja az `az network dns zone delete` parancsot. További segítségért lásd: `az network dns zone delete --help`.

> [!NOTE]
> A DNS-zónák törlésével a zónában található összes DNS-rekord is törlődni fog. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zónát használó szolgáltatások futtatása meghiúsul a zóna törlésekor.
>
>A zónák véletlen törlése elleni védelemről további információt talál a [DNS-zónák és -rekordok védelme](dns-protect-zones-recordsets.md) című szakaszban.

Ez a parancs megerősítést kér. A választható `--yes` kapcsoló kiiktatja a megerősítés kérését.

Az alábbi példa bemutatja, hogyan lehet törölni a *contoso.local* zónát a *MyResourceGroup* erőforráscsoportból.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Az összes erőforrás törlése
 
A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépést:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>További lépések

Az Azure DNS-sel kapcsolatos további információért lásd [az Azure DNS áttekintését biztosító](dns-overview.md) cikket.

DNS-zónák az Azure DNS-ben való kezelésével kapcsolatos további információért lásd [a DNS-zónák az Azure DNS-ben az Azure CLI 2.0-val való kezelésével kapcsolatos](dns-operations-dnszones-cli.md) témakört.

DNS-rekordok az Azure DNS-ben való kezelésével kapcsolatos további információért lásd [a DNS-rekordok és -rekordhalmazok az Azure DNS-ben az Azure CLI 2.0-val való kezelésével kapcsolatos](dns-operations-recordsets-cli.md) témakört.
