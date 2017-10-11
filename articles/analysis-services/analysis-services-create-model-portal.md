---
title: "A táblázatos modellek létrehozása az Azure Analysis Services Web-tervezővel |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre egy Azure Analysis Services táblázatos modell a webalkalmazás-tervező használata Azure-portálon."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/21/2017
ms.author: owend
ms.openlocfilehash: bd58f1845dabf6afb47ce27236d14479677a8808
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-model-in-azure-portal"></a>A modell létrehozása Azure-portálon

Az Azure Analysis Services-webalkalmazás designer (előzetes verzió) Azure-portálon szolgáltatás biztosítja az gyorsan és egyszerűen és szerkesztése a táblázatos modellek és lekérdezi a modell adatok jobb a böngészőben. 

Ne feledje, a webes Designer **előzetes**. Új funkciók hozzáadott folyamatosan, kép, amíg a funkciók korlátozva. A speciális modell fejlesztéshez és teszteléshez érdemes használni a Visual Studio (SSDT) és az SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Előfeltételek

- Egy, a Standard vagy fejlesztői réteg Azure Analysis Services-kiszolgálóhoz. A webalkalmazás-tervezővel létrehozott új modellek DirectQuery, csak ezek a rétegek támogatja.
- Egy Azure SQL Database, Azure SQL Data warehouse-bA vagy egy adatforrást a Power BI Desktop (.pbix) fájlt. A Power BI Desktop fájlok támogatása az Azure SQL Database, Azure SQL Data Warehouse, Oracle és Teradata adatforrások létrehozott új modellek.
- Egy SQL Server-fiókkal és jelszóval csatlakozás az Azure SQL Database vagy az Azure SQL Data Warehouse-adatforrásokhoz.

## <a name="to-create-a-new-tabular-model"></a>Egy új táblázatos modell létrehozása

1. A Server **áttekintése** panel > **webes Tervező**, kattintson a **nyitott**.

    ![A modell létrehozása Azure-portálon](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. A **webes Tervező** > **modellek**, kattintson a **+ Hozzáadás**.

    ![A modell létrehozása Azure-portálon](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. A **új modell**, írja be a modell neve, majd válassza ki egy adatforrást.

    ![Azure-portálon az új modell párbeszédpanel](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. A **Connect**, adja meg a kapcsolat tulajdonságait. Felhasználónév és jelszó SQL Server fióknak kell lennie.

     ![Csatlakozás Azure-portálon párbeszédpanel](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. A **táblák és nézetek**, válassza ki a modell tartalmazza, és kattintson a táblákat **létrehozása**. Egy kulcspár rendelkező táblák közötti kapcsolatok jönnek létre automatikusan.

     ![Válassza ki a táblák és nézetek](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Az új modell jelenik meg a böngészőben. Itt a következőket teheti:   

- Query modelladatok húzzon mezőket a Lekérdezéstervezőben, és vegye fel a szűrőket.
- Hozzon létre új mértékek táblában.
- Módosítsa a modell metaadatait a json-szerkesztő segítségével.
- Nyissa meg a modellt a Visual Studio (SSDT), a Power BI Desktop vagy az Excel.

![Válassza ki a táblák és nézetek](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Modell metaadatainak szerkesztése, illetve a böngészőben új mértékek létrehozása menti ezeket a módosításokat az Azure-ban a modellhez. Ha még dolgozik a modell SSDT, a Power BI Desktop vagy az Excel, a modell szinkronban kérheti le.


## <a name="next-steps"></a>Következő lépések 
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Kapcsolódás Excellel](analysis-services-connect-excel.md)  


