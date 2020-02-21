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
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523657"
---
# <a name="point-in-time-snapshot"></a>Adott időpontban készült pillanatképek

Az Azure-alkalmazás konfigurációja nyilvántartja a kulcs-érték párokban történt módosításokat. Ez a rekord a kulcs-érték változások ütemezését jeleníti meg. Bármely kulcs-érték előzményeit rekonstruálhatja, és az előző hét nap során bármikor megadhatja a korábbi értékét. Ezzel a szolgáltatással visszafelé is lekérheti az időpontot, és lekérheti a régi kulcs értékét. Például helyreállíthatja a legutóbbi telepítés előtt használt konfigurációs beállításokat, hogy visszaállítsa az alkalmazást az előző konfigurációra.

## <a name="key-value-retrieval"></a>Kulcs-érték lekérése

A korábbi kulcsok értékeinek lekéréséhez Azure PowerShellt használhat.  A szükséges értékek beolvasásához használja a `az appconfig revision list`t, és adja hozzá a megfelelő paramétereket.  Adja meg az Azure-alkalmazás konfigurációs példányát úgy, hogy az áruház nevét (`--name {app-config-store-name}`) vagy egy kapcsolatok karakterláncát (`--connection-string {your-connection-string}`) használja. Korlátozza a kimenetet egy adott időpontok (`--datetime`) megadásával és a visszaadni kívánt elemek maximális számának megadásával (`--top`).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A Key-Values összes rögzített módosításának beolvasása.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Beolvassa az összes rögzített módosítást a kulcs `environment` és a címkéket `test` és `prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

A hierarchikus kulcs területének `environment:prod`összes rögzített módosításának beolvasása.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

A kulcs összes rögzített módosításának beolvasása egy adott időpontban `color`.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Kérje le az utolsó 10 rögzített módosítást a kulcs-értékekre, és csak a `key`, `label`és `last-modified` időbélyeg értékét adja vissza.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core Webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
