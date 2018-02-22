---
title: "Kapcsolódás Azure Analysis Services Excel |} Microsoft Docs"
description: "Útmutató az Azure Analysis Services-kiszolgálóhoz kapcsolódni az Excel használatával."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: f5f77b70874f10a29b4ea4dba307a67361c8e2bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="connect-with-excel"></a>Csatlakozás az Excellel

Miután elkészítette a kiszolgáló Azure-ban, és telepített egy táblázatos modell, készen áll csatlakozni, és az adatok megkezdéséhez.


## <a name="connect-in-excel"></a>Csatlakozás az Excel programban

Adatok beolvasása használata az Excel 2016 az Excel programban a kiszolgálóhoz való kapcsolódás esetén támogatott. Nem támogatott a Kapcsolódás a Power Pivot tábla importálása varázsló használatával. 

**Az Excel 2016 kapcsolódni**

1. Az Excel 2016-ot, a **adatok** menüszalag, kattintson a **külső adatok beolvasása** > **egyéb forrásokból származó** > **Analysis Services** .

2. Az Adatkapcsolat varázsló a **kiszolgálónév**, adja meg a kiszolgáló nevét, többek között a protokollt és URI-t. Ezt követően a **bejelentkezési adatok**, jelölje be **használja a következő felhasználónév és jelszó**, és írja be a szervezeti felhasználók nevét, például nancy@adventureworks.com, és a jelszót.

    ![Az Excel bejelentkezéstől csatlakozás](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. A **adatbázis és tábla kijelölése**, az adatbázis és a modell vagy perspektíva, és kattintson a **Befejezés**.
   
    ![Kapcsolódás Excel válassza modellből](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Lásd még
[Ügyfél-függvénytárak](analysis-services-data-providers.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)     


