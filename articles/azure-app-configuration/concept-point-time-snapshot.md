---
title: Kulcs-érték párok beolvasása egy adott időpontban
titleSuffix: Azure App Configuration
description: Régi kulcs-érték párok lekérése az Azure-alkalmazás konfigurációjában időponthoz kötődő Pillanatképek használatával
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523657"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure-alkalmazás konfigurációja nyilvántartja a kulcs-érték párokban történt módosításokat. Ez a rekord a kulcs-érték változások ütemezését jeleníti meg. Bármely kulcs-érték előzményeit rekonstruálhatja, és az előző hét nap során bármikor megadhatja a korábbi értékét. Ezzel a szolgáltatással visszafelé is lekérheti az időpontot, és lekérheti a régi kulcs értékét. Például helyreállíthatja a legutóbbi telepítés előtt használt konfigurációs beállításokat, hogy visszaállítsa az alkalmazást az előző konfigurációra.

## <a name="key-value-retrieval"></a>Kulcs-érték lekérése

A korábbi kulcsok értékeinek lekéréséhez Azure PowerShellt használhat.  Használja `az appconfig revision list` a megfelelő paraméterek hozzáadását a szükséges értékek lekéréséhez.  Adja meg az Azure-alkalmazás konfigurációs példányát úgy, hogy az áruház nevét ( `--name {app-config-store-name}` ) vagy egy () adatforrást használ `--connection-string {your-connection-string}` . Korlátozza a kimenetet egy adott időpontot () megadva `--datetime` , valamint a visszaadni kívánt elemek maximális számának megadásával ( `--top` ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A Key-Values összes rögzített módosításának beolvasása.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

A kulcs és a címkék összes rögzített módosításának beolvasása `environment` `test` `prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

A hierarchikus kulcs területének összes rögzített módosításának beolvasása `environment:prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

A kulcs összes rögzített módosításának beolvasása `color` egy adott időpontban.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Kérje le az utolsó 10 rögzített módosítást a kulcs-értékekre, és csak a `key` , és az időbélyegző értékét adja vissza `label` `last-modified` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
