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
ms.openlocfilehash: b706b5d5ec68daa10fd6eac237b7b7416764167b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830104"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure-alkalmazás konfigurációja a kulcs-értékeken végrehajtott módosítások rekordját tárolja. Ez a rekord a kulcs-érték változások ütemezését jeleníti meg. A kulcs-érték előzményeit újra létrehozhatja, és a korábbi értékeket megadhatja a legfontosabb előzmények időszakában (7 nap az ingyenes csomagok esetében, vagy 30 nap a standard szintű tárolók számára). Ezzel a szolgáltatással visszafelé is lekérheti az időpontot, és lekérheti a régi kulcs értékét. Például helyreállíthatja a legutóbbi telepítés előtt használt konfigurációs beállításokat, hogy visszaállítsa az alkalmazást az előző konfigurációra.

## <a name="key-value-retrieval"></a>Kulcs-érték lekérése

A korábbi kulcs-értékek lekéréséhez Azure Portal vagy CLI-t használhat. Az Azure CLI-ben `az appconfig revision list` a szükséges értékek beolvasásához használja a t, és adja hozzá a megfelelő paramétereket.  Adja meg az Azure-alkalmazás konfigurációs példányát úgy, hogy az áruház nevét ( `--name <app-config-store-name>` ) vagy egy () adatforrást használ `--connection-string <your-connection-string>` . Korlátozza a kimenetet egy adott időpontot () megadva `--datetime` , valamint a visszaadni kívánt elemek maximális számának megadásával ( `--top` ).

Ha nem helyileg telepítette az Azure CLI-t, igény szerint Azure Cloud Shell is használhatja.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A Key-Values összes rögzített módosításának beolvasása.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

A kulcs és a címkék összes rögzített módosításának beolvasása `environment` `test` `prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

A hierarchikus kulcs területének összes rögzített módosításának beolvasása `environment:prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

A kulcs összes rögzített módosításának beolvasása `color` egy adott időpontban.

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Kérje le az utolsó 10 rögzített módosítást a kulcs-értékekre, és csak a `key` , és az időbélyegző értékét adja vissza `label` `last_modified` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
