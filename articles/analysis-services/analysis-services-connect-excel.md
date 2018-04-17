---
title: Kapcsolódás Azure Analysis Services Excel |} Microsoft Docs
description: Útmutató az Azure Analysis Services-kiszolgálóhoz kapcsolódni az Excel használatával.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8414597c2c394e0b642ff47cba79c87488f56b24
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-with-excel"></a>Csatlakozás az Excellel

Miután elkészítette a kiszolgáló Azure-ban, és telepített egy táblázatos modell, készen áll csatlakozni, és az adatok megkezdéséhez.


## <a name="connect-in-excel"></a>Csatlakozás az Excel programban

Adatok beolvasása használata az Excel 2016 az Excel programban a kiszolgálóhoz való kapcsolódás esetén támogatott. Nem támogatott a Kapcsolódás a Power Pivot tábla importálása varázsló használatával. 

**Az Excel 2016 kapcsolódni**

1. Az Excel 2016-ot, a **adatok** menüszalag, kattintson a **külső adatok beolvasása** > **egyéb forrásokból származó** > **Analysis Services** .

2. Az Adatkapcsolat varázsló a **kiszolgálónév**, adja meg a kiszolgáló nevét, többek között a protokollt és URI-t. Ezt követően a **bejelentkezési adatok**, jelölje be **használja a következő felhasználónév és jelszó**, és írja be a szervezeti felhasználók nevét, például nancy@adventureworks.com, és a jelszót.

    > [!NOTE]
    > Ha jelentkezik be egy Microsoft Account, Live ID, Yahoo, Gmail, stb., vagy jelentkezzen be a multi-factor authentication kell, hagyja üresen a jelszó mező. Kéri a jelszót után kattintson a Tovább gombra.

    ![Az Excel bejelentkezéstől csatlakozás](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. A **adatbázis és tábla kijelölése**, az adatbázis és a modell vagy perspektíva, és kattintson a **Befejezés**.
   
    ![Kapcsolódás Excel válassza modellből](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Lásd még
[Ügyfél-függvénytárak](analysis-services-data-providers.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)     


