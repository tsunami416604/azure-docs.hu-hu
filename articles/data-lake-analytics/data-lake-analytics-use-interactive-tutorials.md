---
title: "A Data Lake Analytics és a U-SQL megismerése az Azure portál interaktív oktatóanyagaival | Microsoft Docs"
description: "A Data Lake Analytics és a U-SQL megismerésének első lépései. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 31399d47-e937-4c43-ab0a-6aea8875378d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 194b5d79505afbfd0208f63dd182a0e03227ba69
ms.openlocfilehash: 36677be6bc5599f55f1f15bc145c59033ad20e0a


---
# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Az Azure Data Lake Analytics interaktív oktatóanyagainak használata
Az Azure portál egy interaktív oktatóanyaggal segíti a Data Lake Analytics megismerését. Ez a cikk azt ismerteti, hogyan haladhat végig a webhelynaplók elemzését bemutató oktatóanyagon.

> [!NOTE]
> Ha a Visual Studio használatával szeretné elvégezni ugyanezeket az oktatóanyagokat, tekintse meg a [Analyze website logs using Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelynaplók elemzése a Data Lake Analytics használatával) című cikket.
> A portálra várhatóan további interaktív oktatóanyagok kerülnek fel.
> 
> 

Egyéb oktatóanyagok:

* [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
* [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a .NET SDK-val](data-lake-analytics-get-started-net-sdk.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md) 

**Előfeltételek**

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Data Lake Analytics-fiók**.  Lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása
A feladatok futtatásához rendelkeznie kell egy Data Lake Analytics-fiókkal.

Minden Data Lake Analytics-fiók egy [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)-fióktól, az alapértelmezett Data Lake Store-fióktól függ.  Ebben az oktatóanyagban az alapértelmezett Data Lake Store-fiókot az Analytics-fiókkal együtt fogja létrehozni, de korábban is létrehozható.

**Data Lake Analytics-fiók létrehozása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. A kezdőpult megnyitásához kattintson a bal felső sarokban található **Microsoft Azure** elemre.
3. Kattintson a **Piactér** csempére.  
4. A **Minden erőforrás** panelen írja be a keresőmezőbe az **Azure Data Lake Analytics** kifejezést, és nyomja le az **ENTER** gombot. A listában megjelenik az **Azure Data Lake Analytics** elem.
5. Kattintson a listában az **Azure Data Lake Analytics** elemre.
6. Kattintson a panel alján lévő **Létrehozás** parancsra.
7. Írja be, vagy válassza ki az alábbiakat:
   
    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **Név:** Nevezze el az Analytics-fiókot.
   * **Data Lake Store:** Minden Data Lake Analytics-fiókhoz tartozik egy függőségként kezelt Data Lake Store-fiók. A Data Lake Analytics-fióknak és a függő Data Lake Store-fióknak ugyanabban az Azure-adatközpontban kell lennie. Az útmutatást követve hozzon létre egy Data Lake Store-fiókot, vagy válasszon ki egy már meglévőt.
   * **Előfizetés:** Válassza ki az Analytics-fiókhoz használt Azure-előfizetést.
   * **Erőforráscsoport**. Válasszon ki egy meglévő Azure-erőforráscsoportot, vagy hozzon létre egy újat. Az alkalmazások általában számos összetevőből állnak, például webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból, tárolóból és külső szolgáltatásokból. Az Azure Resource Manager (ARM) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat egy Azure-erőforráscsoportként nevezett csoportként használja. Az alkalmazás erőforrásait egyetlen, koordinált műveletben telepítheti, frissítheti, figyelheti vagy törölheti. A telepítéshez egy sablont használ, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. Tisztázhatja a szervezete számlázását a teljes csoport összegzett költségeinek megtekintésével. További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése). 
   * **Hely**. Válasszon egy Azure-adatközpontot az Azure Data Lake Analytics-fiókhoz. 
8. Válassza **A kezdőpulton rögzít** lehetőséget. Ez szükséges az oktatóanyag lépéseinek követéséhez.
9. Kattintson a **Létrehozás** gombra. Ezzel továbblép a kezdőpult portálra. A kezdőpulton megjelenik egy új csempe „Az Azure Data Lake Analytics telepítése” címkével. A Data Lake Analytics-fiók létrehozása igénybe vehet pár másodpercet. Ha a fiók kész, a portál megnyitja a fiókot egy új panelen.
   
    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

## <a name="run-website-log-analysis-interactive-tutorial"></a>A Webhelynapló elemzése interaktív oktatóanyag futtatása
**A Webhelynapló elemzése interaktív oktatóanyag megnyitása**

1. A portál bal oldali menüjében kattintson a **Microsoft Azure** elemre a kezdőpult megnyitásához.
2. Kattintson a Data Lake Analytics-fiókhoz társított csempére.
3. Kattintson az **Alapvető erőforrások** sávon az **Interaktív oktatóanyagok kezelése** lehetőségre.
   
    ![A Data Lake Analytics interaktív oktatóanyagok használata](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)
4. Ha meglát egy narancssárga figyelmeztetést, amely szerint „A minták nincsenek beállítva, kattintson...”, kattintson a **Mintaadatok másolása** lehetőségre a mintaadatok alapértelmezett Data Lake Store-fiókba való másolásához. Az interaktív oktatóanyag futtatásához szükség van az adatokra.
5. Az **Interaktív oktatóanyagok** panelen kattintson a **Webhelynapló elemzése** lehetőségre. A portál megnyitja az oktatóanyagot egy új portálpanelen.
6. Kattintson a **Bevezetés** elemre, és kövesse az útmutatást

## <a name="see-also"></a>Lásd még:
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
* [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md)




<!--HONumber=Dec16_HO2-->


