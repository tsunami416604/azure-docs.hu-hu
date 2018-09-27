---
title: Egy Power BI Desktop-fájl importálása az Azure Analysis Services |} A Microsoft Docs
description: Ismerteti, hogyan importálhatja a Power BI Desktop-fájl (pbix) Azure portal használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a4bb5f59e7aa99a676145ba7a6dbd1883cb860cf
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394319"
---
# <a name="import-a-power-bi-desktop-file"></a>A Power BI Desktop-fájl importálása

Azure Analysis Services adatmodellt a Power BI Desktop-fájl (pbix) importálhatja. Modell metaadatainak, a gyorsítótárazott adatok és az adatforrás-kapcsolatok importálása. Jelentések és Vizualizációk nem lesznek importálva. Importált adatok a Power BI Desktopból modellek az 1400-as kompatibilitási szinten.

**Korlátozások**   

- A portálon, amely a webes Tervező funkcióval egy pbix-fájlból használ **előzetes**. A funkció korlátozódik. Speciális modell fejlesztési és tesztelési célszerű a Visual Studio (SSDT) és az SQL Server Management Studio (SSMS) használja.
- Ha importálása során a következő hibaüzenetet kapja, a pbix-fájl rendelkezik előzetes verziójú funkciók engedélyezve van, amely még nem támogatottak az Azure Analysis Servicesben.

    ![Kompatibilitási szintre vonatkozó figyelmeztetés](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)
- Kiszolgáló-rendszergazdai engedélyek egy pbix-fájlból importálni kell rendelkeznie.
- A pbix-modell csatlakozhat **Azure SQL Database** és **Azure SQL Data Warehouse** csak adatforrásokhoz.
- A pbix-modell nem lehet élő vagy DirectQuery-kapcsolatok. 
- Ha a pbix-adatmodellt tartalmaz, az Analysis Services nem támogatja a metaadatok meghiúsulhat.

## <a name="to-import-from-pbix"></a>Pbix-fájlt importálása

1. A kiszolgáló **áttekintése** > **webes Tervező**, kattintson a **nyílt**.

    ![Modell létrehozása az Azure Portalon](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. A **webes Tervező** > **modellek**, kattintson a **+ Hozzáadás**.

    ![Modell létrehozása az Azure Portalon](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. A **új modell**, írja be a modell neve, és válassza a Power BI Desktop-fájlt.

    ![Az Azure portal az új modell párbeszédpanel](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. A **importálás**, keresse meg és válassza ki a fájlt.

     ![Csatlakozás az Azure portal párbeszédpanel](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>Hitelesítő adatok módosítása

Alapértelmezés szerint a pbix-fájlból importálja egy olyan modell, ha egy adatforráshoz való kapcsolódáshoz használt hitelesítő adatok ServiceAccount vannak állítva. Miután egy pbix-fájlt való importálása megtörtént egy modellt, módosíthatja a hitelesítő adatait a következő módszerekkel:

- Használja a 2018 július (verzió 17.8.1) vagy újabb verziója a hitelesítő adatok szerkesztése az ssms-ben. Ez a legegyszerűbb módja.
- Használja a TMSL [Alter parancsot](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) a a [adatforrások objektum](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl) kapcsolati karakterlánc tulajdonság módosításához. 
- A Visual Studióban nyissa meg a modellt, az adatforrás-kapcsolat hitelesítő adatainak szerkesztése és újra üzembe a modellt.

A hitelesítő adatok módosításához az SSMS használatával. 

1. Az ssms-ben, bontsa ki az adatbázist > **kapcsolatok**. 
2. Kattintson a jobb gombbal az adatbázis-kapcsolat, és kattintson a **frissítési hitelesítő adatok**. 

    ![Hitelesítő adatok frissítése](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. A hitelesítő adatok párbeszédpanelen válassza ki a hitelesítő adatok típusát, és adja meg hitelesítő adatait. Az SQL-hitelesítéshez jelölje ki az adatbázist. Szervezeti fiók (OAuth) válassza ki a Microsoft-fiókjával.
    ![Hitelesítő adatok szerkesztése](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

A Power BI Desktop 2018 július verzióját az adatforrás-engedélyek módosítása egy új szolgáltatást tartalmaz. Az a **kezdőlap** lapra, majd **lekérdezések szerkesztése**  > **adatforrás-beállítások**. Válassza ki az adatforrás-kapcsolatot, és kattintson a **engedélyeinek szerkesztése**.


## <a name="see-also"></a>Lásd még

[Modell létrehozása az Azure Portalon](analysis-services-create-model-portal.md)   
[Csatlakozás az Azure Analysis Services](analysis-services-connect.md)  
