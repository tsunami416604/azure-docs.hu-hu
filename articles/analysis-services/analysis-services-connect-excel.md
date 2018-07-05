---
title: Csatlakozhat az Azure Analysis Services az Excelben |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Analysis Services-kiszolgáló az Excel használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 807496584acb3f93fccd3495de005792b769b37f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442998"
---
# <a name="connect-with-excel"></a>Csatlakozás az Excellel

Miután létrehozott egy kiszolgálót, és üzembe helyezett egy táblázatos modellhez, az ügyfelek csatlakozhat és megkezdje az adatok. 

## <a name="before-you-begin"></a>Előkészületek
A bejelentkezés a következővel fiók egy modellt az adatbázis-szerepkörhöz kell tartoznia, legalább olvasási engedéllyel. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Az Excel csatlakoztatása

Kapcsolódás az Excel-kiszolgálóhoz az Excel 2016 adatok lekérése használatával támogatott. Tábla importálása varázsló segítségével a Power Pivotban kapcsolódó nem támogatott. 

**Csatlakozás az Excel 2016-ban**

1. Az Excel 2016-ban a **adatok** menüszalagra, majd **külső adatok beolvasása** > **egyéb forrásokból származó** > **Analysis Services** .

2. Az Adatkapcsolat varázsló a **kiszolgálónév**, adja meg a kiszolgáló nevét, többek között a protokoll és URI-t. Ha például asazure://westcentralus.asazure.windows.net/advworks. Ezt követően a **bejelentkezési adatok**válassza **használja a következő felhasználónév és jelszó**, majd írja be a szervezeti felhasználó nevét, például nancy@adventureworks.com, és a jelszót.

    > [!IMPORTANT]
    > Ha bejelentkezett egy Microsoft-Account, Live ID, Yahoo, Gmail, stb., vagy jelentkezzen be a multi-factor authentication nélkül, a jelszó mezőt üresen. Tovább gombra kattintás után a jelszó megerősítését. 

    ![Kapcsolódás az Excel-bejelentkezés](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. A **adatbázis és tábla kijelölése**, válassza ki az adatbázis és a modell vagy perspektíva, és kattintson **Befejezés**.
   
    ![Válassza ki az Excel-modellből csatlakoztatása](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Lásd még
[Ügyfélkódtárak](analysis-services-data-providers.md)   
[Kiszolgáló kezelése](analysis-services-manage.md)     


