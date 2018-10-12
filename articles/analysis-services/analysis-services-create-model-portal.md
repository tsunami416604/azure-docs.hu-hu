---
title: Egy táblázatos modell létrehozása az Azure Analysis Services webes Tervező használatával |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Azure Analysis Services táblázatos modell a webes tervezőben az Azure portal használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 837601a55bbe97e904f3dece9f4c87563d6dd4e9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092677"
---
# <a name="create-a-model-in-azure-portal"></a>Modell létrehozása az Azure Portalon

Az Azure Portalon az Azure Analysis Services webes Tervező (előzetes verzió) funkció lehetővé teszi gyors és egyszerű létrehozása és szerkesztése a táblázatos modellek, és lekérdezheti a modell adatait közvetlenül a böngészőben. 

> [!IMPORTANT]
> Ez a funkció elavult. Előfordulhat, hogy azt eltávolították, vagy egy jövőbeli frissítéssel jelentősen módosult. Ajánlott, hogy megszakítja a meglévő és új projektek a funkciók segítségével fenntartja a jövőbeli frissítések kompatibilitást. Speciális modell fejlesztési és tesztelési célszerű a Visual Studio (SSDT) és az SQL Server Management Studio (SSMS) használja.


## <a name="before-you-begin"></a>Előkészületek

- Az Azure Analysis Services-kiszolgáló, a Standard vagy a fejlesztői csomagra kell lennie. A webes Tervező használatával létrehozott új modell DirectQuery, csak ezek a csomagok által támogatott.
- Egy Azure SQL Database, Azure SQL Data Warehouse vagy egy adatforrást a Power BI Desktop (.pbix) fájlt. A Power BI Desktop-fájlok támogatása az Azure SQL Database és az Azure SQL Data Warehouse létrehozása az új modellek.
- Egy SQL Server-fiókot és jelszót kapcsolódás az Azure SQL Database vagy Azure SQL Data Warehouse-adatforrásokhoz.
- Egy új modell létrehozásához a kiszolgáló rendszergazdai jogosultságokat kell rendelkeznie. Adatbázis-rendszergazdai jogosultságok szükségesek, szerkesztése és a egy modell lekérdezése a Tervező használatával.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Egy új táblázatos modell létrehozása

1. A kiszolgáló **áttekintése** > **webes Tervező**, kattintson a **nyílt**.

    ![Modell létrehozása az Azure Portalon](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. A **webes Tervező** > **modellek**, kattintson a **+ Hozzáadás**.

    ![Modell létrehozása az Azure Portalon](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. A **új modell**, és írja be a modell neve, majd válasszon ki egy adatforrást.

    ![Az Azure portal az új modell párbeszédpanel](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. A **Connect**, adja meg a kapcsolat tulajdonságait. Felhasználónév és jelszó egy SQL Server-fióknak kell lennie.

     ![Csatlakozás az Azure portal párbeszédpanel](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. A **táblák és nézetek**, válassza ki a belefoglalni a modellbe, majd kattintson a táblázatokat **létrehozás**. Egy kulcsból álló kulcspárt tartalmazó táblák közötti kapcsolatok automatikusan létrejönnek.

     ![Táblák és nézetek kijelölése](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Az új modell jelenik meg a böngészőben. Itt a következőket teheti:   

- Modell adatait kérdezi le a Lekérdezéstervezőben mezők húzásával, és vegye fel a szűrőket.
- Új mértékek létrehozása a táblákban.
- Modell metaadatainak szerkesztése a json-szerkesztő használatával.
- Nyissa meg a modell a Visual Studio (SSDT), a Power BI Desktop vagy az Excel.

![Táblák és nézetek kijelölése](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Amikor modell metaadatainak szerkesztése, vagy hozzon létre új mértékek a böngészőben, ezeket a módosításokat menti a modellhez, az Azure-ban. Ha is dolgozik, a modell az SSDT, a Power BI Desktop vagy az Excel, a modell szinkronizálva kérheti le.


## <a name="next-steps"></a>További lépések 
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Kapcsolódás Excellel](analysis-services-connect-excel.md)  


