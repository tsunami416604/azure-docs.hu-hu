---
title: Nyelvi bővítmények kezelése az Azure Data Explorer-fürtben.
description: A nyelvi bővítmény használatával más nyelveket is integrálhat az Azure Data Explorer KQL-lekérdezéseibe.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522471"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Nyelvi bővítmények kezelése az Azure Data Explorer-fürtben (előzetes verzió)

A nyelvi bővítmények funkció lehetővé teszi, hogy a nyelvi bővítmény beépülő modul használatával integráljon más nyelveket az Azure Data Explorer KQL-lekérdezéseibe. Ha egy felhasználó által definiált függvényt (UDF) futtat egy megfelelő parancsfájl használatával, a parancsfájl táblázatos adatokat kap bemenetként, és várhatóan táblázatos kimenetet fog eredményezni. A bővítmény futtatókörnyezete egy [homokozóban](/azure/kusto/concepts/sandboxes)található, egy elszigetelt és biztonságos környezetben, amely a fürt csomópontjain fut. Ebben a cikkben kezelheti a nyelvi bővítmények et az Azure Data Explorer-fürtben az Azure Portalon belül.

> [!NOTE]
> A jelenleg támogatott Azure Data Explorer nyelvi bővítmények a Python és az R.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Hozzon létre [egy Azure Data Explorer-fürtöt és -adatbázist.](create-cluster-database-portal.md)

## <a name="enable-language-extensions-on-your-cluster"></a>Nyelvi bővítmények engedélyezése a fürtön

> [!WARNING]
> A nyelvi bővítmény engedélyezése előtt tekintse át a [korlátozásokat.](#limitations)

A nyelvi bővítmények fürtön való engedélyezéséhez tegye a következő lépéseket:

1. Az Azure Portalon nyissa meg az Azure Data Explorer-fürt. 
1. A **Beállítások csoportban**válassza a **Konfigurációk**lehetőséget. 
1. A **Konfigurációk** ablaktáblán válassza **a Be** lehetőséget a nyelvi bővítmény engedélyezéséhez.
1. Kattintson a **Mentés** gombra.
 
    ![nyelvi kiterjesztés](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> A nyelvi bővítmény folyamatának engedélyezése eltarthat néhány percig. Ez idő alatt a fürt fel lesz függesztve.
 
## <a name="run-language-extension-integrated-queries"></a>Nyelvi bővítménybe integrált lekérdezések futtatása

* További információ a [Python-integrált KQL-lekérdezések futtatásáról.](/azure/kusto/query/pythonplugin)
* További információ az [R integrált KQL-lekérdezések futtatásáról.](/azure/kusto/query/rplugin) 

## <a name="disable-language-extensions-on-your-cluster"></a>Nyelvi bővítmények letiltása a fürtön

> [!NOTE]
> A nyelvi bővítmények letiltása eltarthat néhány percig.

A nyelvi bővítmények fürtön való letiltásához tegye a következő lépéseket:

1. Az Azure Portalon nyissa meg az Azure Data Explorer-fürt. 
1. A **Beállítások csoportban**válassza a **Konfigurációk**lehetőséget. 
1. A **Konfigurációk** ablaktáblán válassza a **Ki** lehetőséget a nyelvi bővítmény letiltásához.
1. Kattintson a **Mentés** gombra.

    ![A nyelvi kiterjesztés kikapcsolva](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Korlátozások

* A nyelvi bővítmények szolgáltatás nem támogatja [a lemeztitkosítást.](manage-cluster-security.md) 
* A language extensions runtime sandbox foglal helyet lemezterület akkor is, ha nincs lekérdezés fut a hatókörben a megfelelő nyelvet.

