---
title: A Microsoft Power bi-ban Azure Blockchain munkaterület adatok használata
description: Ismerje meg, hogyan tölthetők be, és a Microsoft Power bi-ban Azure Blockchain munkaterület SQL DB adatok megtekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 2b909c0a8441010b87c913e5937d25c8127058f1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Azure Blockchain munkaterület adatokat a Microsoft Power BI használatával

Microsoft Power BI lehetővé teszi a könnyedén generálhatnak hatékony jelentések használata a Power BI Desktop SQL DB adatbázisból, és tegye közzé őket [ https://www.powerbi.com ](http://www.powerbi.com).

A cikkben egy részletes útmutató arról, hogyan csatlakozzon az Azure Blockchain munkaterület SQL Database a Power bi desktop belül, hozzon létre egy jelentést és a jelentés központi telepítése a powerbi.com webhelyre.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le [Power bi Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-powerbi-to-data-in-azure-blockchain-workbench"></a>Power bi Azure Blockchain munkaterület-adatokhoz való kapcsolódásról

1.  Nyissa meg a Power BI Desktop.
2.  Válassza ki **adatok**.

    ![Adatok lekérése](media/blockchain-workbench-data-powerbi/get-data.png)
3.  Válassza ki **SQL Server** adatforrásokat a listából.

4.  Adja meg a kiszolgáló és az adatbázis nevét, a párbeszédpanelen. Adja meg, hogy szeretné-e az adatok importálása, vagy hajtsa végre a **DirectQuery**. Kattintson az **OK** gombra.

    ![Select SQL Server](media/blockchain-workbench-data-powerbi/select-sql.png)

5.  Adja meg az adatbázis-hitelesítő adatok Azure Blockchain munkaterület eléréséhez. Válassza ki **adatbázis** hitelesítő adataival.

    Ha az Azure Blockchain munkaterület központi telepítési folyamat során létrehozott hitelesítő adatok használata esetén a felhasználónév az **dbadmin** , a jelszó pedig a telepítés során megadottal.

    ![SQL-adatbázis beállításai](media/blockchain-workbench-data-powerbi/db-settings.png)

6.  Kapcsolódás után az adatbázis, a **Navigator** párbeszédpanel megjeleníti a táblák és nézetek érhető el az adatbázisban. A nézetek készültek, jelentéskészítési és azok minden hozzáfűzik **vw**.

    ![Navigator](media/blockchain-workbench-data-powerbi/navigator.png)

7.  Válassza ki a szerepeltetni kívánt nézeteket. Bemutatási célokra magában foglalja az **vwContractAction**, amelynek részleteit a egy egyezményen végrehajtott műveletekről.

    ![Válassza ki a nézetek](media/blockchain-workbench-data-powerbi/select-views.png)

Mostantól létrehozhat és a szokásos módon a Power BI a jelentések közzététele.

## <a name="next-steps"></a>További lépések

* [Adatbázis nézetekhez Azure Blockchain munkaterület](blockchain-workbench-database-views.md)