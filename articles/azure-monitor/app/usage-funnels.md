---
title: Azure Application Insights-csatornák
description: Ismerje meg, hogyan használhatja a Csatornákat annak felfedezéséhez, hogy az ügyfelek hogyan használják az alkalmazást.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671052"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Ismerje meg, hogyan használják az ügyfelek az alkalmazást az Application Insights-csatornákkal

Az ügyfélélmény megértése rendkívül fontos vállalkozása számára. Ha az alkalmazás több szakaszból áll, tudnia kell, hogy a legtöbb ügyfél halad-e a teljes folyamaton keresztül, vagy egy bizonyos ponton leáll-e a folyamat. A webalkalmazás számos lépésének előrehaladását *tölcsérnek nevezzük.* Az Azure Application Insights-csatornák használatával betekintést nyerhet a felhasználókba, és figyelheti a részletes konverziós arányokat. 

## <a name="create-your-funnel"></a>A csatorna létrehozása
A csatorna létrehozása előtt döntse el, hogy melyik kérdésre szeretne válaszolni. Például érdemes lehet tudni, hogy hány felhasználó tekinti meg a kezdőlapot, tekinti meg az ügyfélprofilt, és hoz létre egy jegyet. Ebben a példában a Fabrikam Fiber cég tulajdonosai szeretnék tudni, hogy az ügyfelek hány százaléka, akik sikeresen létrehozegy ügyféljegyet.

Itt vannak a lépéseket, hogy hozzon létre a csatorna.

1. Az Application Insights-csatornák eszközben válassza az **Új lehetőséget.**
1. Az **Időtartomány legördülő** menüben válassza az **Elmúlt 90 nap**lehetőséget. Válassza a **Saját csatornák** vagy **a Megosztott csatornák lehetőséget.**
1. Az **1.** **Index** 
1. A **2.** **Customer**
1. A **3.** **Create**
1. Adjon nevet a csatornához, és válassza a **Mentés gombot.**

A következő képernyőképen egy példa látható arra, hogy milyen típusú adatokat hoz létre a Csatornák eszköz. A Fabrikam tulajdonosok láthatják, hogy az elmúlt 90 napban a kezdőlapot felvásárló ügyfeleik 54,3 százaléka létrehozott egy ügyféljegyet. Azt is láthatják, hogy 2700 vásárlójuk jutott el az indexhez a kezdőlapról. Ez frissítési problémát jelezhet.


![Képernyőkép: A Csatornák eszköz adatokkal](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Csatornák jellemzői
Az előző képernyőkép öt kiemelt területet tartalmaz. Ezek a tölcsérek jellemzői. Az alábbi lista a képernyőképen található minden egyes megfelelő területről további információkkal egészül ki:
1. Ha az alkalmazás mintavételre kerül, megjelenik egy mintavételi szalagcím. A szalagcím kiválasztásakor megnyílik egy környezetablaktábla, amely bemutatja, hogyan lehet kikapcsolni a mintavételezést. 
2. A csatornát exportálhatja a [Power BI-ba.](../../azure-monitor/app/export-power-bi.md )
3. Válasszon ki egy lépést a jobb oldali további részletek megtekintéséhez. 
4. Az előzménykonverziós grafikon az elmúlt 90 nap konverziós arányait mutatja. 
5. A felhasználók eszköz elérésével jobban megismerheti a felhasználókat. A szűrőket minden lépésben használhatja. 

## <a name="next-steps"></a>További lépések
  * [Használat – áttekintés](usage-overview.md)
  * [Felhasználók, munkamenetek és események](usage-segmentation.md)
  * [Megőrzés](usage-retention.md)
  * [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
  * [Felhasználói környezet hozzáadása](usage-send-user-context.md)
  * [Power BI-exportálás](../../azure-monitor/app/export-power-bi.md )

