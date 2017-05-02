---
title: "Az Azure DNS használatának első lépései az Azure CLI 2.0-val | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel az Azure CLI 2.0 használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 5cb387c4d1a2a2ae5ee8822241b11e79f53f0d6a
ms.lasthandoff: 04/25/2017

---

# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Az Azure DNS használatának első lépései az Azure CLI 2.0-val

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Ez a cikk bemutatja az első DNS-zóna és -rekord létrehozásának lépéseit a platformfüggetlen Azure CLI 2.0 használatával, amely Windows, Mac és Linux platformokon is elérhető. Ezeket a lépéseket az Azure Portal vagy az Azure PowerShell használatával is elvégezheti.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

Ezek az utasítások feltételezik, hogy már telepítette az Azure CLI 2.0-t, és bejelentkezett. További segítségért lásd [a DNS-zónák az Azure CLI 2.0 használatával való kezelésével kapcsolatos](dns-operations-dnszones-cli.md) témakört.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

A DNS-zóna létrehozása előtt egy erőforráscsoportot kell létrehozni, amely a DNS-zónát tartalmazza majd. Az alábbiakban a parancs látható.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. A paranccsal kapcsolatos súgó megtekintéséhez írja be a következőt: `az network dns zone create -h`.

Az alábbi példaparancs a *MyResourceGroup* nevű erőforráscsoportban létrehozza a *contoso.com* DNS-zónát. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```


## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekordokat az `az network dns record-set [record type] add-record` paranccsal lehet létrehozni. Az A-rekordokkal kapcsolatos segítségért például lásd: `azure network dns record-set A add-record -h`.

Az alábbi példa a „MyResourceGroup” erőforráscsoport „contoso.com” DNS-zónájában egy „www” relatív nevű rekordot hoz létre. A beállított rekord teljes neve „www.contoso.com”. A rekord típusa „A”, az IP-címe „1.2.3.4”, és a rendszer a 3600 másodperces (1 órás) alapértelmezett élettartamot használja.

```azurecli
az network dns record-set A add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Más rekordtípusok, több rekordot tartalmazó rekordhalmazok, alternatív élettartam-értékek és meglévő rekordok módosítása esetén lásd: [DNS-rekordok és -rekordhalmazok kezelése az Azure CLI 2.0 használatával](dns-operations-recordsets-cli.md).


## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```


## <a name="update-name-servers"></a>A névkiszolgálók frissítése

Ha a DNS-zóna és -rekordok megfelelően be lettek állítva, konfigurálnia kell a tartománynevet az Azure DNS-névkiszolgálók használatára. Így más internetes felhasználók megkereshetik a DNS-rekordjait.

A zóna névkiszolgálói az `az network dns zone show` paranccsal vannak megadva. A névkiszolgáló nevek megtekintéséhez használjon JSON-kimenetet az alábbi példában látható módon.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Ezeket a névkiszolgálókat a tartományregisztrálóhoz kell konfigurálni (ahol a tartománynevet vásárolta). A regisztráló felajánlja, hogy beállítja a névkiszolgálókat a tartományhoz. További információért lásd: [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Az összes erőforrás törlése
 
A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépést:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Az Azure DNS-sel kapcsolatos további információért lásd [az Azure DNS áttekintését biztosító](dns-overview.md) cikket.

DNS-zónák az Azure DNS-ben való kezelésével kapcsolatos további információért lásd [a DNS-zónák az Azure DNS-ben az Azure CLI 2.0-val való kezelésével kapcsolatos](dns-operations-dnszones-cli.md) témakört.

DNS-rekordok az Azure DNS-ben való kezelésével kapcsolatos további információért lásd [a DNS-rekordok és -rekordhalmazok az Azure DNS-ben az Azure CLI 2.0-val való kezelésével kapcsolatos](dns-operations-recordsets-cli.md) témakört.

