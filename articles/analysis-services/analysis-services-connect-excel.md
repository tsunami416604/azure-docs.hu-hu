---
title: Csatlakozás az Azure Analysis Services szolgáltatáshoz az Excelsegítségével | Microsoft dokumentumok
description: Megtudhatja, hogy miként csatlakozhat azure Analysis Services-kiszolgálóhoz az Excel használatával. A csatlakozásután a felhasználók kimutatásokat hozhatnak létre az adatok feltárásához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b5c46c8b85af59efaf611300e2ab7129b3ef3cde
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411387"
---
# <a name="connect-with-excel"></a>Kapcsolódás Excellel

Miután létrehozott egy kiszolgálót, és üzembe helyezett egy táblázatos modellt, az ügyfelek csatlakozhatnak, és megkezdhetik az adatok feltárását. 

## <a name="before-you-begin"></a>Előkészületek

A fiók, amelyen bejelentkezik, legalább olvasási engedélyekkel rendelkező modelladatbázis-szerepkörhöz kell tartoznia. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Csatlakozás az Excelben

Az Excelben lévő kiszolgálóhoz való csatlakozást az Adatok beszereznie az Excel 2016-ban és újabb verzióban is támogatja. A Power Pivot Tábla importálása varázslójával történő csatlakozás nem támogatott. 

1. Az Excel **Ben** kattintson az Adatok menüszalagOn a Külső adatok >  **beszedése****más forrásokból** > **az Analysis Services szolgáltatásból**elemre.

2. Az Adatkapcsolat varázsló **Kiszolgálónév mezőbe**írja be a kiszolgáló nevét a protokollés az URI segítségével. Például asazure://westcentralus.asazure.windows.net/advworks. Ezután a **Bejelentkezési hitelesítő adatok**csoportban válassza **a Felhasználónév és jelszó használata** nancy@adventureworks.comlehetőséget, majd írja be a szervezeti felhasználónevet (például a) és a jelszót.

    > [!IMPORTANT]
    > Ha Microsoft-fiókkal, Live ID azonosítóval, Yahoo-val, Gmail-jel stb. A Tovább gombra kattintás után a rendszer jelszót kér. 

    ![Csatlakozás az Excelből bejelentkezéskor](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Az **Adatbázis és tábla kiválasztása csoportban**jelölje ki az adatbázist és a modellt vagy perspektívát, majd kattintson a Befejezés **gombra.**
   
    ![Csatlakozás az Excelből válasszon modellt](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Lásd még

[Ügyféltárak](analysis-services-data-providers.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)     


