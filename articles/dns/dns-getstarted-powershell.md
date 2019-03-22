---
title: Rövid útmutató – Azure DNS-zóna és -rekord létrehozása az Azure PowerShell használatával
description: A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti rövid útmutató, amellyel az Azure PowerShell használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 75ac8a45eb49ac5c4ec3b39667542f4f454a9954
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110324"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Gyors útmutató: Hozzon létre egy Azure DNS-zóna és -rekord Azure PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezzel a rövid útmutatóval létrehozza első DNS-zónáját és -rekordját az Azure PowerShell használatával. Ezek a lépések az [Azure Portal](dns-getstarted-portal.md) vagy az [Azure CLI](dns-getstarted-cli.md) használatával is elvégezhetők. 

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

Az Azure DNS saját tartományok létrehozását is támogatja. Ha részletes útmutatót keres az első saját DNS-zóna és -rekord létrehozásához, lásd: [Az Azure DNS saját zónáival kapcsolatos első lépések PowerShell-lel](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Mielőtt létrehozná a DNS-zónát, hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzDnsZone` parancsmag használatával hozható létre. A következő példában létrehozunk egy DNS-zónát *contoso.xyz* az erőforráscsoport neve *MyResourceGroup*. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

Rekordhalmazt a `New-AzDnsRecordSet` parancsmag használatával hozhat létre. Az alábbi példa létrehoz egy rekordot a DNS-zóna "contoso.xyz", "MyResourceGroup" erőforráscsoport "www" relatív névvel. A beállított rekord teljes neve nem "www.contoso.xyz". A rekord típusa "A", "10.10.10.10" IP-címmel, az Élettartama pedig 3600 másodperc.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy a teszt DNS-zóna egy teszt "A" rekord, ezt kipróbálhatja a névfeloldás nevű eszközt *nslookup*. 

**DNS-névfeloldás tesztelése:**

1. Futtassa a következő parancsmagot a zóna névkiszolgálói listájának lekérése:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
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

Az állomásnév **www\.contoso.xyz** mutat **10.10.10.10**, ugyanúgy, mint a be vannak állítva ezek. Ezt az eredményt ellenőrzi, hogy a névfeloldás megfelelően működik-e.

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ha már nincs rájuk szükség, a rövid útmutatóban létrehozott erőforrásokat az erőforráscsoport törlésével távolíthatja el:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta első DNS-zónáját és -rekordját az Azure PowerShell használatával, létrehozhat webalkalmazás-rekordokat is egyéni tartományokban.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)

