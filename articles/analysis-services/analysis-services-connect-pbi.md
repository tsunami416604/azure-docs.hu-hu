---
title: Kapcsolódás a Azure Analysis Serviceshoz Power BI használatával | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat egy Azure Analysis Services-kiszolgálóhoz Power BI használatával. A csatlakozást követően a felhasználók megismerhetik a modellre vonatkozó adatgyűjtést.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573103"
---
# <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

Miután létrehozott egy kiszolgálót az Azure-ban, és egy táblázatos modellt helyezett üzembe, a szervezet felhasználói készen állnak a kapcsolódásra, és megkezdik az adatgyűjtés megkezdését. 

> [!TIP]
> Ügyeljen arra, hogy a [Power bi Desktop](https://powerbi.microsoft.com/desktop/)legújabb verzióját használja.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Csatlakozás a Power BI Desktopban

1. A Power BI Desktopban kattintson az **Adatok lekérése** > **Azure** > **Azure Analysis Services-adatbázis** elemre.

2. A **kiszolgáló**mezőben adja meg a kiszolgáló nevét. Ügyeljen arra, hogy a teljes URL-címet tartalmazza; például: asazure://westcentralus.asazure.windows.net/advworks.

3. Ha ismeri annak a táblázatos modellnek az adatbázisának vagy perspektívájának a **nevét, amelyhez**csatlakozni szeretne, illessze be ide. Ellenkező esetben hagyja üresen ezt a mezőt, és válasszon ki egy adatbázist vagy perspektívát később.

4. Válassza ki a kapcsolat lehetőséget, majd kattintson a **Csatlakoztatás**gombra. 

    Az **élő** és az **importálási** beállítások egyaránt támogatottak. Javasoljuk azonban, hogy élő kapcsolatokat használjon, mivel az importálási mód bizonyos korlátozásokkal rendelkezik; a legtöbb esetben az importálás során hatással lehet a kiszolgáló teljesítményére. Továbbá, ha a modellt frissíteni kell a Power BI szolgáltatásban, a **hozzáférés engedélyezése Power bi** beállítás csak akkor érvényes, ha az **élő kapcsolat**lehetőséget választja.

5. Ha a rendszer kéri, adja meg a bejelentkezési adatait. 

6. A **Navigátorban**bontsa ki a kiszolgálót, majd válassza ki azt a modellt vagy perspektívát, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás**elemre. Kattintson egy modellre vagy perspektívára az adott nézet összes objektumának megjelenítéséhez.

    A modell a jelentés nézetben egy üres jelentéssel nyílik meg Power BI Desktop. A mezők lista megjeleníti az összes nem rejtett modell objektumot. A csatlakozás állapota a jobb alsó sarokban látható.

## <a name="connect-in-power-bi-service"></a>Kapcsolódjon Power BI (szolgáltatás)

1. Hozzon létre egy Power BI Desktop fájlt, amely élő kapcsolatban áll a modellel a-kiszolgálón.
2. [Power bi](https://powerbi.microsoft.com)kattintson az adat > **fájlok** **lekérése** elemre, majd keresse meg és válassza ki a. pbix fájlt.

## <a name="see-also"></a>Lásd még
[Kapcsolódás Azure Analysis Services](analysis-services-connect.md)   
[Ügyfélkódtárak](analysis-services-data-providers.md)

