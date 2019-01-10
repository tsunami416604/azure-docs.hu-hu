---
title: Csatlakozhat az Azure Analysis Services az Excelben |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Analysis Services-kiszolgáló az Excel használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5c46d4e4d23744cf07ccf7857a33990bf405a6a1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189014"
---
# <a name="connect-with-excel"></a>Csatlakozás az Excellel

Miután létrehozott egy kiszolgálót, és üzembe helyezett egy táblázatos modellhez, az ügyfelek csatlakozhat és megkezdje az adatok. 

## <a name="before-you-begin"></a>Előkészületek

A fiók, akkor jelentkezzen be a modell az adatbázis-szerepkörhöz kell tartoznia, legalább olvasási engedéllyel. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Az Excel csatlakoztatása

Kapcsolódás az Excel-kiszolgálóhoz támogatja az adatok lekérése, az Excel 2016 vagy újabb. Tábla importálása varázsló segítségével a Power Pivotban kapcsolódó nem támogatott. 

1. Az Excel a a **adatok** menüszalagra, majd **külső adatok beolvasása** > **egyéb forrásokból származó** > **az Analysis Services**.

2. Az Adatkapcsolat varázsló a **kiszolgálónév**, adja meg a kiszolgáló nevét, többek között a protokoll és URI-t. Ha például asazure://westcentralus.asazure.windows.net/advworks. Ezt követően a **bejelentkezési adatok**válassza **használja a következő felhasználónév és jelszó**, majd írja be a szervezeti felhasználó nevét, például nancy@adventureworks.com, és a jelszót.

    > [!IMPORTANT]
    > Ha jelentkezik be egy Microsoft-Account, Live ID, Yahoo, Gmail, stb., vagy jelentkezzen be a multi-factor authentication nélkül, a jelszó mezőt üresen. Tovább gombra kattintás után a jelszó megerősítését. 

    ![Kapcsolódás az Excel-bejelentkezés](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. A **adatbázis és tábla kijelölése**, válassza ki az adatbázis és a modell vagy perspektíva, és kattintson **Befejezés**.
   
    ![Válassza ki az Excel-modellből csatlakoztatása](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Lásd még

[Ügyfélkódtárak](analysis-services-data-providers.md)   
[Kiszolgáló kezelése](analysis-services-manage.md)     


