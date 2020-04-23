---
title: Azure Blockchain Workbench-adatok használata a Microsoft Power BI-ban
description: Megtudhatja, hogy hogyan töltheti be és tekintheti meg az Azure Blockchain Workbench SQL-adatbázisának adatait a Microsoft Power BI-ban.
ms.date: 04/22/2020
ms.topic: article
ms.reviewer: sunri
ms.openlocfilehash: 4245603fee5b3d24488426c3dc2d026a3c0d7848
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082526"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Azure Blockchain Workbench-adatok használata a Microsoft Power BI-ban

A Microsoft Power BI lehetővé teszi, hogy a Power BI Desktop segítségével [https://www.powerbi.com](https://www.powerbi.com)egyszerűen generáljon hatékony jelentéseket az SQL DB-adatbázisokból, majd közzétegye azokat a rendszeren.

Ebből a cikkből részletesen megismerheti, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához a PowerBI Desktop alkalmazásból, illetve hogyan hozhat létre és tehet közzé jelentéseket a powerbi.com-on.

## <a name="prerequisites"></a>Előfeltételek

* A [Power BI Desktop letöltése](https://powerbi.microsoft.com/desktop/).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>A Power BI csatlakoztatása adatokhoz az Azure Blockchain Workbench szolgáltatásban

1.  Nyissa meg a Power BI Desktopot.
2.  Válassza az **Adatok lekérése** lehetőséget.

    ![Adatok lekérése](./media/data-powerbi/get-data.png)
3.  Válassza az **SQL Server** lehetőséget az adatforrástípusok listájából.

4.  Adja meg a kiszolgáló és az adatbázis nevét a megjelenő párbeszédpanelen. Adja meg, hogy importálni akarja-e az adatokat, vagy **DirectQuery**-lekérdezést szeretne végrehajtani. Válassza **az OK gombot.**

    ![Select SQL Server](./media/data-powerbi/select-sql.png)

5.  Adja meg az adatbázis hitelesítő adatait az Azure Blockchain Workbench eléréséhez. Válassza az **Adatbázis** lehetőséget, és adja meg a hitelesítő adatait.

    Ha az Azure Blockchain Workbench telepítési folyamata során létrehozott hitelesítő adatokat használja, akkor a felhasználónév **dbadmin**, a jelszó pedig a telepítés során megadott jelszó.

    ![SQL-adatbázis beállításai](./media/data-powerbi/db-settings.png)

6.  Az adatbázishoz való csatlakozás után a **Kezelő** párbeszédpanel megjeleníti az adatbázisban elérhető táblákat és nézeteket. A nézetek jelentéskészítéshez lettek tervezve, és mind **vw** előtaggal kezdődnek.

    ![Kezelő](./media/data-powerbi/navigator.png)

7.  Jelölje ki a belefoglalni kívánt nézeteket. Demonstrációs célokra a **vwContractAction-t**is bevezetjük, amely részletesen ismerteti a szerződéssel kapcsolatos műveleteket.

    ![Nézetek kiválasztása](./media/data-powerbi/select-views.png)

Ezután létrehozhatja és közzéteheti a jelentéseket a Power BI-ban megszokott munkafolyamatot követve.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)