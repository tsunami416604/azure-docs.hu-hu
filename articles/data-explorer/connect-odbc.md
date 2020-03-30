---
title: Csatlakozás az Azure Data Explorerhez az ODBC segítségével
description: Ebből a cikkből megtudhatja, hogyan állíthat be egy open database connectivity (ODBC) kapcsolatot az Azure Data Explorerrel.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034031"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Csatlakozás az Azure Data Explorerhez az ODBC segítségével

Az Open Database Connectivity[(ODBC](/sql/odbc/reference/odbc-overview)) egy széles körben elfogadott alkalmazásprogramozási felület (API) az adatbázis-hozzáféréshez. Az ODBC használatával olyan alkalmazásokból csatlakozhat az Azure Data Explorerhez, amelyek nem rendelkeznek dedikált összekötővel.

A színfalak mögött, alkalmazások hívás funkciók az ODBC felület, amelyek végre adatbázis-specifikus modulok úgynevezett *vezetők*. Az Azure Data Explorer támogatja az SQL Server kommunikációs protokoll ([MS-TDS)](/azure/kusto/api/tds/)egy részét, így használhatja az ODBC-illesztőprogramot az SQL Serverhez.

Az alábbi videó segítségével megtudhatja, hogyan hozhat létre ODBC-kapcsolatot. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Azt is [megteheti, hogy az ODBC adatforrást az](#configure-the-odbc-data-source) alábbiakban részletezett módon konfigurálja. 

A cikkben megtudhatja, hogyan használhatja az SQL Server ODBC-illesztőprogramot, így bármely ODBC-t támogató alkalmazásból csatlakozhat az Azure Data Explorerhez. 

## <a name="prerequisites"></a>Előfeltételek

A következőkre van szüksége:

* [Microsoft ODBC illesztőprogram az SQL Server 17.2.0.1-es vagy újabb verziójához](/sql/connect/odbc/download-odbc-driver-for-sql-server) az operációs rendszerhez.

## <a name="configure-the-odbc-data-source"></a>Az ODBC adatforrás konfigurálása

Az alábbi lépésekkel konfigurálhat ODBC adatforrást az SQL Server ODBC illesztőprogramjával.

1. A Windows rendszerben keressen *ODBC adatforrások*, és nyissa meg az ODBC Adatforrások asztali alkalmazást.

1. Válassza a **Hozzáadás** lehetőséget.

    ![Adatforrás hozzáadása](media/connect-odbc/add-data-source.png)

1. Válassza **az ODBC Driver 17 lehetőséget az SQL Server kiszolgálóhoz,** majd fejezze be a **programot.**

    ![Illesztőprogram kiválasztása](media/connect-odbc/select-driver.png)

1. Adja meg a kapcsolat és a csatlakozni kívánt fürt nevét és leírását, majd válassza a **Tovább**gombot. A fürt URL-címének * \<fürtnév\>formában kell lennie.\< Régió\>.kusto.windows.net*.

    ![Kiszolgáló kiválasztása](media/connect-odbc/select-server.png)

1. Válassza **az Active Directory Integrated lehetőséget,** majd a Tovább **lehetőséget.**

    ![Active Directory integrált](media/connect-odbc/active-directory-integrated.png)

1. Jelölje ki a mintaadatokat tartalmazó adatbázist, majd **a Tovább**lehetőséget.

    ![Alapértelmezett adatbázis módosítása](media/connect-odbc/change-default-database.png)

1. A következő képernyőn hagyja az összes beállítást alapértelmezettként, majd válassza a **Befejezés**lehetőséget.

1. Válassza az **Adatforrás tesztelése**lehetőséget.

    ![Adatforrás tesztelése](media/connect-odbc/test-data-source.png)

1. Ellenőrizze, hogy a teszt sikeres volt-e, majd válassza **az OK gombot.** Ha a teszt nem sikerült, ellenőrizze az előző lépésekben megadott értékeket, és győződjön meg arról, hogy rendelkezik-e a fürthöz való csatlakozáshoz szükséges engedélyekkel.

    ![A teszt sikeres volt](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>További lépések

* [Csatlakozás az Azure Data Explorerhez a Tableau-ból](tableau.md)