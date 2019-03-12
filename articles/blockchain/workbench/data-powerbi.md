---
title: Azure Blockchain Workbench-adatok használata a Microsoft Power BI-ban
description: Megtudhatja, hogy hogyan töltheti be és tekintheti meg az Azure Blockchain Workbench SQL-adatbázisának adatait a Microsoft Power BI-ban.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e12af686a450d39332c37700b9a14b9eb620307
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530892"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Azure Blockchain Workbench-adatok használata a Microsoft Power BI-ban

A Microsoft Power BI segítségével könnyedén hozhat létre modern jelentéseket SQL-adatbázisokból a Power BI Desktop alkalmazással, majd közzéteheti őket a [https://www.powerbi.com](https://www.powerbi.com) szolgáltatásba.

Ebből a cikkből részletesen megismerheti, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához a PowerBI Desktop alkalmazásból, illetve hogyan hozhat létre és tehet közzé jelentéseket a powerbi.com-on.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Az Azure Blockchain Workbench alkalmazásban adatokhoz való csatlakozásról a Power bi-ban

1.  Nyissa meg a Power BI Desktop alkalmazást.
2.  Válassza az **Adatok lekérése** lehetőséget.

    ![Adatok lekérése](./media/data-powerbi/get-data.png)
3.  Válassza az **SQL Server** lehetőséget az adatforrástípusok listájából.

4.  Adja meg a kiszolgáló és az adatbázis nevét a megjelenő párbeszédpanelen. Adja meg, hogy importálni akarja-e az adatokat, vagy **DirectQuery**-lekérdezést szeretne végrehajtani. Kattintson az **OK** gombra.

    ![Select SQL Server](./media/data-powerbi/select-sql.png)

5.  Adja meg az adatbázis hitelesítő adatait az Azure Blockchain Workbench eléréséhez. Válassza az **Adatbázis** lehetőséget, és adja meg a hitelesítő adatait.

    Ha az Azure Blockchain Workbench telepítési folyamata során létrehozott hitelesítő adatokat használja, akkor a felhasználónév **dbadmin**, a jelszó pedig a telepítés során megadott jelszó.

    ![SQL-adatbázis beállításai](./media/data-powerbi/db-settings.png)

6.  Az adatbázishoz való csatlakozás után a **Kezelő** párbeszédpanel megjeleníti az adatbázisban elérhető táblákat és nézeteket. A nézetek jelentéskészítéshez lettek tervezve, és mind **vw** előtaggal kezdődnek.

    ![Kezelő](./media/data-powerbi/navigator.png)

7.  Jelölje ki a belefoglalni kívánt nézeteket. Bemutatási célokból tartalmazza **vwContractAction**, amely részletesen került sor egy szerződés a műveleteket.

    ![Nézetek kiválasztása](./media/data-powerbi/select-views.png)

Ezután létrehozhatja és közzéteheti a jelentéseket a Power BI-ban megszokott munkafolyamatot követve.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)