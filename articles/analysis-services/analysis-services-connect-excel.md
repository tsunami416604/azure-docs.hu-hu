---
title: Kapcsolódás Azure Analysis Services Excel |} Microsoft Docs
description: Útmutató az Azure Analysis Services-kiszolgálóhoz kapcsolódni az Excel használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6585648cbb461f52a22bc6ea239c0899caa2c2e7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597263"
---
# <a name="connect-with-excel"></a>Csatlakozás az Excellel

Miután elkészítette a kiszolgáló, és telepített egy táblázatos modell, ügyfelek csatlakozni, és kezdje az adatok. 

## <a name="before-you-begin"></a>Előkészületek
A fiókkal jelentkezik be kell tartoznia a modell adatbázis-szerepkör legalább olvasási engedéllyel. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Csatlakozás az Excel programban

Adatok beolvasása használata az Excel 2016 az Excel programban a kiszolgálóhoz való kapcsolódás esetén támogatott. Nem támogatott a Kapcsolódás a Power Pivot tábla importálása varázsló használatával. 

**Az Excel 2016 kapcsolódni**

1. Az Excel 2016-ot, a **adatok** menüszalag, kattintson a **külső adatok beolvasása** > **egyéb forrásokból származó** > **Analysis Services** .

2. Az Adatkapcsolat varázsló a **kiszolgálónév**, adja meg a kiszolgáló nevét, többek között a protokollt és URI-t. Például asazure://westcentralus.asazure.windows.net/advworks. Ezt követően a **bejelentkezési adatok**, jelölje be **használja a következő felhasználónév és jelszó**, és írja be a szervezeti felhasználók nevét, például nancy@adventureworks.com, és a jelszót.

    > [!IMPORTANT]
    > Bejelentkezés Microsoft-Account, Live ID, Yahoo, Gmail, stb., vagy jelentkezzen be a multi-factor Authentication hitelesítés szükséges, ha a jelszó mezőt hagyja üresen. Kéri a jelszót után kattintson a Tovább gombra. 

    ![Az Excel bejelentkezéstől csatlakozás](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. A **adatbázis és tábla kijelölése**, az adatbázis és a modell vagy perspektíva, és kattintson a **Befejezés**.
   
    ![Kapcsolódás Excel válassza modellből](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Lásd még
[Ügyfél-függvénytárak](analysis-services-data-providers.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)     


