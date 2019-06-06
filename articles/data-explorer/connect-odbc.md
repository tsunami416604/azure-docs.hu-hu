---
title: Csatlakozás az Azure Data Explorer ODBC
description: Ebből a cikkből elsajátíthatja az Adatkezelőbe az Azure egy megnyitott adatbázis-kapcsolat (ODBC) kapcsolat beállításához.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494854"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Csatlakozás az Azure Data Explorer ODBC

Nyissa meg az adatbázis-kapcsolat ([ODBC](/sql/odbc/reference/odbc-overview)) van egy széles körben elfogadott alkalmazásprogramozási felületet (API) az adatbázis eléréséhez. ODBC segítségével csatlakozzon az Adatkezelőbe az Azure-alkalmazásokból, amelyek nem rendelkeznek egy dedikált összekötőt.

A színfalak mögött alkalmazások hívja az ODBC-felületén, függvények, amelyek az adatbázis-specifikus modulokat, vagyis úgynevezett megvalósított *illesztőprogramok*. Az Azure Data Explorer támogatja az SQL Server kommunikációs protokoll egy részét ([MS-TDS](/azure/kusto/api/tds/)), ezért az ODBC-illesztőprogram SQL Serverhez is használ.

Ebből a cikkből megismerheti, hogyan kell használni az Azure Data Explorer bármely olyan alkalmazásból, amely támogatja a ODBC csatlakozhat az SQL Server ODBC-illesztőprogramját.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* [Microsoft ODBC-illesztő az SQL Server 17.2.0.1 verzió vagy újabb](/sql/connect/odbc/download-odbc-driver-for-sql-server) , az operációs rendszernek.

## <a name="configure-the-odbc-data-source"></a>ODBC-adatforrás konfigurálása

Kövesse az alábbi lépéseket az ODBC-illesztő használatával SQL Serverhez készült ODBC-adatforrás konfigurálása.

1. A Windows, keresse meg *ODBC adatforrások*, és nyissa meg a ODBC adatforrások asztali alkalmazást.

1. Válassza a **Hozzáadás** lehetőséget.

    ![Adatforrás hozzáadása](media/connect-odbc/add-data-source.png)

1. Válassza ki **ODBC-illesztőprogram 17 az SQL Server** majd **Befejezés**.

    ![Illesztőprogram kiválasztása](media/connect-odbc/select-driver.png)

1. Adjon meg egy nevet és leírást a kapcsolat és a fürt, amelyhez csatlakozni kíván, majd válassza ki **tovább**. A fürt URL-címet kell lennie a képernyő  *\<ClusterName\>.\< Régió\>. kusto.windows.net*.

    ![Kiszolgáló kiválasztása](media/connect-odbc/select-server.png)

1. Válassza ki **Active Directory integrált** majd **tovább**.

    ![Active Directory-integrációt](media/connect-odbc/active-directory-integrated.png)

1. Ezután válassza ki az adatbázist a mintaadatok **tovább**.

    ![Alapértelmezett adatbázis váltása](media/connect-odbc/change-default-database.png)

1. Minden beállítás a következő képernyőn, hagyja, mert alapértelmezés szerint, majd válassza ki **Befejezés**.

1. Válassza ki **tesztelése adatforrás**.

    ![Teszt adatforrás](media/connect-odbc/test-data-source.png)

1. Győződjön meg arról, hogy a teszt sikeres volt-e majd **OK**. Ha a teszt nem sikerült, ellenőrizze az előző lépésben megadott értékeket, és ellenőrizze, hogy megfelelő engedélyekkel a fürthöz való csatlakozáshoz.

    ![Teszt sikerült](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>További lépések

* [Csatlakozás a Tableau az Azure Data Explorer](tableau.md)