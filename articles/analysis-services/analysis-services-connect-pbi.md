---
title: Csatlakozás az Azure Analysis Services szolgáltatáshoz a Power BI-val | Microsoft dokumentumok
description: Megtudhatja, hogy miként csatlakozhat egy Azure Analysis Services-kiszolgálóhoz a Power BI használatával. A csatlakozás után a felhasználók felfedezhetik a modelladatokat.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573103"
---
# <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

Miután létrehozott egy kiszolgálót az Azure-ban, és üzembe helyezett egy táblázatos modellt, a szervezet felhasználói készen állnak a csatlakozásra és az adatok feltárásának megkezdésére. 

> [!TIP]
> Ügyeljen arra, hogy a [Power BI Desktop](https://powerbi.microsoft.com/desktop/)legújabb verzióját használja.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Csatlakozás a Power BI Desktopban

1. A Power BI Desktopban kattintson > a Data**Azure** > **Analysis Services adatbázisának** **beszereznie**elemre.

2. A **Kiszolgáló mezőbe**írja be a kiszolgáló nevét. Ügyeljen arra, hogy tartalmazza a teljes URL-t; például asazure://westcentralus.asazure.windows.net/advworks.

3. Ha **Database**ismeri annak a táblázatos modelladatbázisnak vagy perspektívanak a nevét, amelyhez csatlakozni szeretne, illessze be ide. Ellenkező esetben üresen hagyhatja ezt a mezőt, és később kijelölhet egy adatbázist vagy perspektívát.

4. Válasszon egy csatlakozási beállítást, majd nyomja le a **Csatlakozás gombot.** 

    A **Connect live** és az **Import** beállítások is támogatottak. Javasoljuk azonban az élő kapcsolatok használatát, mert az importálási módnak vannak bizonyos korlátai; leginkább a kiszolgáló teljesítményét befolyásolhatja az importálás során. Ha a modellt frissíteni szeretné a Power BI szolgáltatásban, akkor a Hozzáférés engedélyezése a **Power BI-ból** beállítás csak akkor érvényes, ha az **Élő csatlakoztatása**lehetőséget választja.

5. Ha a rendszer kéri, adja meg bejelentkezési adatait. 

6. A **Navigátor**ban bontsa ki a kiszolgálót, jelölje ki azt a modellt vagy perspektívát, amelyhez csatlakozni szeretne, majd kattintson a **Csatlakozás gombra.** Kattintson egy modellre vagy perspektívára az adott nézet összes objektumának megjelenítéséhez.

    A modell a Power BI Desktopban nyílik meg, és jelentés nézetben üres jelentéssel nyílik meg. A Mezők listában minden nem rejtett modellobjektum megjelenik. A csatlakozás állapota a jobb alsó sarokban látható.

## <a name="connect-in-power-bi-service"></a>Csatlakozás a Power BI-ban (szolgáltatás)

1. Hozzon létre egy Olyan Power BI Desktop fájlt, amely élő kapcsolatban áll a modellel a kiszolgálón.
2. A [Power BI-ban](https://powerbi.microsoft.com)kattintson az **Adatfájlok** > beszereznie**gombra,** majd keresse meg és jelölje ki a .pbix fájlt.

## <a name="see-also"></a>Lásd még
[Csatlakozás az Azure Analysis Services szolgáltatáshoz](analysis-services-connect.md)   
[Ügyfélkódtárak](analysis-services-data-providers.md)

