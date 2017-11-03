---
title: "Az Azure Advisor költség javaslatok |} Microsoft Docs"
description: "Azure Advisor segítségével optimalizálhatja az Azure-környezetekhez költségét."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 51320d93689da3e37c0946d8877b27a11793d9c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-cost-recommendations"></a>Költség javaslatokat biztosít

Az Advisor segítségével optimalizálhatja, és csökkentheti a teljes Azure töltött túl magas és üresjárati erőforrások azonosításával. Akkor is beolvasása költség javaslatokat a **költség** az Advisor irányítópult fület.

![Az Advisor költség lap](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>Optimalizálhatja a túl példányok átméretezésével töltött virtuális gép 
Bár egyes alkalmazás-forgatókönyvekre eredményezhet alacsony kihasználtságú úgy lett kialakítva, gyakran mentheti pénz való kezelésekor a a virtuális gépek száma és mérete. Az Advisor figyeli a virtuális gépek használatának 14 napja, és majd azonosítja a kis-kihasználtságának virtuális gépek. Virtuális gépek, amelyek CPU-felhasználás csak 5 % vagy kevesebb és hálózati aktivitás a 7 MB vagy kisebb a négy vagy több napot minősülnek alacsony használt virtuális gépek.

Az Advisor jeleníti meg, hogy ha szeretné leállítani, vagy méretezze át a virtuális gép futtatásához Folytatás becsült költségét.  

![Az Advisor költségeket, virtuális gépek átméretezésével kapcsolatos ajánlások](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Költséghatékony megoldás segítségével Teljesítménycélok több SQL-adatbázisok kezelése
Az Advisor azonosítja az SQL server-példányokat, amelyek kihasználhatják a rugalmas adatbáziskészlet létrehozása. A rugalmas adatbáziskészletek adja meg, amely egyszerű és költséghatékony megoldást teljesítményértékeket rendelkező, különböző használati minták több adatbázis kezelésére. Az Azure rugalmas készletek kapcsolatos további információkért lásd: [Mi az Azure rugalmas készletek?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

![Az Advisor rugalmas adatbáziskészletek javaslatok költsége](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Költség javaslatok az Azure Advisor elérése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Kattintson a bal oldali ablaktáblában **további szolgáltatások**.

3. A szolgáltatás menü ablaktáblán alatt **figyelés és felügyelet**, kattintson a **Azure Advisor**.  
 Az Advisor irányítópult jelenik meg.

4. Az Advisor irányítópultján kattintson a **költség** fülre.

5. Válassza ki az előfizetést, amely javaslatokat kap, és kattintson a kívánt **javaslatok beszerzése**.

> [!NOTE]
> Advisor-javaslatokra szeretne használni, először *az előfizetés regisztrálása* az Advisor szolgáltatásban. Egy előfizetés regisztrálva amikor egy *előfizetés tulajdonosának* elindítja az Advisor irányítópulton, és rákattint a **javaslatok beszerzése** gombra. Ez egy *egyszeri művelet*. Az előfizetés regisztrálása után érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* előfizetés, egy erőforráscsoport vagy egy adott erőforrás.

## <a name="next-steps"></a>Következő lépések

Az Advisor-javaslatokra kapcsolatos további információkért lásd:
* [Az Advisor bemutatása](advisor-overview.md)
* [Első lépések](advisor-get-started.md)
* [Teljesítmény javaslatokat biztosít](advisor-cost-recommendations.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-cost-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-cost-recommendations.md)
