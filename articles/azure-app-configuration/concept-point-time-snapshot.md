---
title: Kulcs-érték párok beolvasása időpontban
titleSuffix: Azure App Configuration
description: Régi kulcs-érték párok lekérése az Azure App konfigurációjának időponthoz szerzett pillanatképeivel
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523657"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure App Configuration nyilvántartást vezet a kulcs-érték párok módosításairól. Ez a rekord a kulcsérték-változások ütemtervét tartalmazza. Bármely kulcsérték előzményeit rekonstruálhatja, és az előző hét napon belül bármikor megadhathatja annak korábbi értékét. Ezzel a funkcióval visszafelé "időutazással" visszafelé haladhat, és lekérheti a régi kulcsértéket. Például helyreállíthatja a legutóbbi telepítés előtt használt konfigurációs beállításokat, hogy visszaállítsa az alkalmazást az előző konfigurációra.

## <a name="key-value-retrieval"></a>Kulcs-érték visszakeresése

Az Azure PowerShell segítségével lekérheti a korábbi kulcsértékeket.  Használja `az appconfig revision list`a , megfelelő paramétereket adva a szükséges értékek beolvasásához.  Adja meg az Azure App konfigurációs példányát az üzlet nevének (`--name {app-config-store-name}`) vagy egy kapcsolati karakterlánc (`--connection-string {your-connection-string}`használatával. Korlátozza a kimenetet egy adott időpont`--datetime`( ) megadásával és a visszaküldendő elemek maximális számának megadásával ( ).`--top`

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A kulcsértékek összes rögzített módosításának beolvasása.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

A kulcs `environment` és a címkék összes `test` `prod`rögzített módosításának beolvasása és a.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

A hierarchikus kulcstér összes `environment:prod`rögzített módosításának beolvasása.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

A kulcs `color` összes rögzített módosításának beolvasása egy adott időpontban.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

A kulcsértékek utolsó 10 rögzített módosításának beolvasása, `label`és `last-modified` csak a `key`, és az időbélyegző értékeit adja vissza.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
