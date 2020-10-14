---
title: Kapcsolódás a Azure Analysis Serviceshoz az Excel használatával | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat egy Azure Analysis Services-kiszolgálóhoz az Excel használatával. A csatlakozást követően a felhasználók létrehozhatnak kimutatásokat az adatelemzéshez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4658a4c05aac7932d8a99c3511f21024f19fbe47
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013729"
---
# <a name="connect-with-excel"></a>Kapcsolódás Excellel

Miután létrehozott egy kiszolgálót, és egy táblázatos modellt helyezett üzembe, az ügyfelek csatlakozhatnak, és megkezdhetik az adatfelfedezést. 

## <a name="before-you-begin"></a>Előkészületek

A bejelentkezett fióknak a modell adatbázis-szerepköréhez kell tartoznia legalább olvasási engedéllyel. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Az Excelben való kapcsolat

Az Excel-kiszolgálókhoz való kapcsolódás az Excel 2016-es és újabb verzióiban az adatlekérdezés használatával támogatott. A Power Pivot nem támogatja a csatlakozást a tábla importálása varázsló használatával. 

1. Az Excelben az **adatok** menüszalagon kattintson a **külső adatok beolvasása**  >  **más forrásokból**lehetőségre  >  **Analysis Services**.

2. Az Adatkapcsolat varázslóban, a **kiszolgáló neve**mezőben adja meg a kiszolgáló nevét, beleértve a protokollt és az URI-t. Például: asazure://westcentralus.asazure.windows.net/advworks. Ezután a **bejelentkezési hitelesítő adatok**területen jelölje be **a következő Felhasználónév és jelszó használata**jelölőnégyzetet, majd írja be a szervezeti felhasználónevet, például a nancy@adventureworks.com jelszót.

    > [!IMPORTANT]
    > Ha Microsoft-fiókkal, élő AZONOSÍTÓval, a Yahoo-val, a Gmailben vagy más felhasználóval jelentkezik be, vagy ha be kell jelentkeznie a többtényezős hitelesítéssel, hagyja üresen a jelszó mezőt. A Tovább gombra kattintás után a rendszer jelszót kér. 

    ![Kapcsolódás az Excel-bejelentkezésből](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Az **adatbázis és tábla kiválasztása**lapon válassza ki az adatbázist és a modellt vagy perspektívát, majd kattintson a **Befejezés**gombra.
   
    ![Kapcsolat az Excelből Select Model](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Lásd még

[Ügyféloldali kódtárak](/analysis-services/client-libraries?view=azure-analysis-services-current)   
[A kiszolgáló kezelése](analysis-services-manage.md)