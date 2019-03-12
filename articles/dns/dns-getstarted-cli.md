---
title: Rövid útmutató – Azure DNS-zóna és -rekord létrehozása az Azure CLI használatával
description: Rövid útmutató – A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel az Azure CLI használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b24b06ab121b1484e458a9e725fd90590af05c08
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761497"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Gyors útmutató: Hozzon létre egy Azure DNS-zóna és -rekordhalmazok Azure CLI használatával

Ez a cikk bemutatja az első DNS-zóna és -rekord létrehozásának lépéseit az Azure CLI használatával, amely Windows, Mac és Linux platformokon is elérhető. Ezeket a lépéseket az [Azure Portal](dns-getstarted-portal.md) vagy az [Azure PowerShell](dns-getstarted-powershell.md) használatával is elvégezheti.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

Az Azure DNS már támogatja a privát DNS-zónák használatát (jelenleg nyilvános előzetes verzióban érhető el). További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md). További információt a privát DNS-zónák létrehozásáról [az Azure DNS privát zónái CLI segítségével történő használatának első lépéseit](./private-dns-getstarted-cli.md) ismertető cikkben olvashat.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Mielőtt létrehozná a DNS-zónát, hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. A paranccsal kapcsolatos súgó megtekintéséhez írja be a következőt: `az network dns zone create -h`.

A következő példában létrehozunk egy DNS-zónát *contoso.xyz* erőforráscsoportban *MyResourceGroup*. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekordokat az `az network dns record-set [record type] add-record` paranccsal lehet létrehozni. Az A-rekordokkal kapcsolatos segítségért lásd: `azure network dns record-set A add-record -h`.

Az alábbi példa létrehoz egy rekordot a DNS-zóna a "MyResourceGroup" erőforráscsoport "contoso.xyz" a "www" relatív névvel. A beállított rekord teljes neve nem "www.contoso.xyz". A rekord típusa "A", akkor az IP-cím "10.10.10.10" és a egy alapértelmezett élettartam 3600 másodperces (1 óra).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy a teszt DNS-zóna egy teszt "A" rekord, ezt kipróbálhatja a névfeloldás nevű eszközt *nslookup*. 

**DNS-névfeloldás tesztelése:**

1. Futtassa a következő parancsmagot a zóna névkiszolgálói listájának lekérése:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Másolja ki az egyik a Névkiszolgálók neveit az előző lépés kimenetéből származó.

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Példa:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Hiba a következő képernyőhöz hasonlóan kell megjelennie:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Az állomásnév **www.contoso.xyz** mutat **10.10.10.10**, ugyanúgy, mint a be vannak állítva ezek. Ezt az eredményt ellenőrzi, hogy a névfeloldás megfelelően működik-e.

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ha már nincs rájuk szükség, a rövid útmutatóban létrehozott erőforrásokat az erőforráscsoport törlésével távolíthatja el:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta első DNS-zónáját és -rekordját az Azure CLI használatával, létrehozhat webalkalmazás-rekordokat is egyéni tartományokban.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
