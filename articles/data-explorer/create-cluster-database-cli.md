---
title: Hozzon létre egy Azure Data Explorer fürtöt és egy adatbázist, CLI-vel
description: Ez a cikk bemutatja, hogyan hozhat létre Azure Data Explorer fürt és adatbázis, Azure CLI használatával
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812684"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Hozzon létre egy Azure Data Explorer fürtöt és egy adatbázist, CLI-vel

Ez a cikk bemutatja, hogyan hozhat létre Azure Data Explorer fürt és adatbázis, Azure CLI használatával.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Mi a különbség a felügyeleti sík és adatsík API-k között

Nincsenek a két különböző API függvénytárai, felügyeleti és az adatsík API-k.
A felügyeleti API-k segítségével felügyelheti az erőforrásokat, például hozzon létre egy fürtöt, hozzon létre egy adatbázist, törlés egy kapcsolatot, módosítsa a számot a példányok száma stb. Az adatsík API-k segítségével használni tudják az adatokat, lekérdezéseket futtatnia, kiolvasni az adatokat stb.

## <a name="configure-the-cli-parameters"></a>A CLI paramétereinek konfigurálása

Bejelentkezés a fiókjába

```Bash
az login
```

Az előfizetés beállításához, hol szeretné létrehozni a fürtben.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

A következő parancsot a fürt létrehozása.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Adja meg a következő értékek

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | név | *azureclitest* | A fürt kívánt nevét.|
   | termékváltozat | *D13_v2* | A Termékváltozat a fürt számára fogja használni. |
   | resource-group | *testrg* | Az a fürt hol hozható létre erőforráscsoport nevét. |

Ha azt szeretné, nincsenek további nem kötelező paraméter, amelyet használhat, például a kapacitás, a fürt stb.

Ellenőrizze, hogy a fürt sikeresen létrejött, futtathatja

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Ha az eredmény tartalmazza a "provisioningState", "Succeeded" értékkel, ez azt jelenti, a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer fürtben

Hozzon létre az adatbázist a következő paranccsal.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Adja meg a következő értékek

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster-name | *azureclitest* | A fürt nevére, a kell létrehozni.|
   | név | *clidatabase* | Az adatbázis kívánt nevét.|
   | resource-group | *testrg* | Az a fürt hol hozható létre erőforráscsoport nevét. |
   | soft-delete-period | *3650:00:00:00* | Mennyi ideig tartó adatokat meg kell őrizni, hogy lekérdezhetők. |
   | hot-cache-period | *3650:00:00:00* | Mennyi ideig tartó adatokat kell tárolni a gyorsítótárban. |

Láthatja, hogy a létrehozott futtatott adatbázis

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

Ennyi az egész most már egy fürt és a egy adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat.

Ha töröl egy fürtöt, benne az adatbázisokat is törli. Használja az alábbi parancs, törölje a fürtöt.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Betölteni az adatokat az Event Hubs az Azure Data Explorer](ingest-data-event-hub.md)
