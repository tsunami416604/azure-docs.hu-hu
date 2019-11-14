---
title: Kapcsolódás az Azure Adatkezelőhoz ODBC használatával
description: Ebből a cikkből megtudhatja, hogyan állíthat be nyílt adatbázis-kapcsolati (ODBC) kapcsolatot az Azure Adatkezelőhoz.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034031"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Kapcsolódás az Azure Adatkezelőhoz ODBC használatával

Az Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) egy széles körben elfogadott alkalmazásprogramozási felület (API) az adatbázis-hozzáféréshez. Az ODBC használatával kapcsolódjon az Azure Adatkezelő olyan alkalmazásokból, amelyek nem rendelkeznek dedikált összekötővel.

A színfalak mögött az Applications az ODBC felületen hívja meg a functions függvényt, amely az adatbázis-specifikus, *illesztőprogramok*nevű modulokban lett implementálva. Az Azure Adatkezelő támogatja a SQL Server kommunikációs protokoll ([MS-TDS](/azure/kusto/api/tds/)) egy részhalmazát, így az ODBC-illesztőt is használhatja a SQL Serverhoz.

Az alábbi videó használatával megtudhatja, hogyan hozhat létre ODBC-kapcsolatokat. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Azt is megteheti, hogy az alábbi módon [konfigurálja az ODBC-adatforrást](#configure-the-odbc-data-source) . 

A cikkből megtudhatja, hogyan használhatja az SQL Server ODBC-illesztőt, így az ODBC-t támogató bármely alkalmazásból csatlakozhat az Azure Adatkezelőhoz. 

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szüksége:

* Az operációs rendszer [17.2.0.1 vagy újabb verziója Microsoft ODBC Driver for SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server) .

## <a name="configure-the-odbc-data-source"></a>Az ODBC-adatforrás konfigurálása

Az alábbi lépések végrehajtásával konfigurálhat egy ODBC-adatforrást az SQL Server ODBC-illesztőprogramjának használatával.

1. A Windowsban keressen *ODBC-adatforrásokat*, és nyissa meg az ODBC-adatforrások asztali alkalmazást.

1. Válassza a **Hozzáadás** lehetőséget.

    ![Adatforrás hozzáadása](media/connect-odbc/add-data-source.png)

1. Válassza **a 17. ODBC-illesztőt SQL Server majd a** **Befejezés gombra**.

    ![Illesztőprogram kiválasztása](media/connect-odbc/select-driver.png)

1. Adja meg a kapcsolat nevét és leírását, valamint azt a fürtöt, amelyhez csatlakozni szeretne, majd válassza a **tovább**lehetőséget. A fürt URL-címének *\<ClusterName\>.\<régió\>. kusto.Windows.net*formátumúnak kell lennie.

    ![Kiszolgáló kiválasztása](media/connect-odbc/select-server.png)

1. Válassza a **Active Directory integrált** lehetőséget, majd a **Tovább gombra**.

    ![Integrált Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Válassza ki az adatbázist a mintaadatok közül, majd a **Tovább gombra**.

    ![Alapértelmezett adatbázis módosítása](media/connect-odbc/change-default-database.png)

1. A következő képernyőn hagyja meg az összes beállítást alapértelmezett értékként, majd válassza a **Befejezés**gombot.

1. Válassza **az adatforrás tesztelése**lehetőséget.

    ![Adatforrás tesztelése](media/connect-odbc/test-data-source.png)

1. Ellenőrizze, hogy a teszt sikeres volt-e, majd kattintson **az OK gombra**. Ha a teszt nem sikerült, ellenőrizze az előző lépésekben megadott értékeket, és ellenőrizze, hogy rendelkezik-e megfelelő engedélyekkel a fürthöz való kapcsolódáshoz.

    ![A teszt sikerült](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódás az Azure Adatkezelőhoz a tablóból](tableau.md)