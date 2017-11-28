---
title: "Azure költség felügyeleti költség jelentések ismertetése |} Microsoft Docs"
description: "Ez a cikk segít megérteni a Cloudyn jelentések alapszintű struktúrát és funkcióit."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/27/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: df2108a6e2a01195340a09eacf1c56f9d738c923
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="understanding-cost-reports"></a>Jelentések költségek ismertetése

Ez a cikk segít megérteni a Cloudyn jelentések alapszintű struktúrát és funkcióit. A legtöbb Cloudyn jelentések intuitív és egy egységes megjelenés és működés. Ez a cikk elolvasása után készen áll a jelentésekre. Számos szabványos szolgáltatást a különböző jelentéseket, lehetővé téve, keresse meg a jelentések kihívásokra keresztül érhetőek el. Jelentések testre szabható, és kiszámításához ki és jelenítheti meg az eredmények több lehetőség közül választhat.

## <a name="report-fields-and-options"></a>Jelentésmezők és beállítások

Íme egy példa a költség keresztül idő a jelentés egy pillantást. A legtöbb Cloudyn jelentések csak hasonló elrendezése lehet.

![a minta-jelentés](./media/understanding-cost-reports/sample-report.png)

Az előző ábrán minden számozott területen a következő információ részletes leírását lásd:

1. **Dátumtartomány**

    A dátumtartományt lista segítségével adja meg az előre definiált vagy egyéni jelentés időközt.
2. **Mentett szűrőkészlet**

    A mentett lista használatával mentheti azokat a csoportokat és a jelentésre alkalmazott szűrőket. Mentett szűrők a költségeket és a teljesítmény jelentések, beleértve a különböző érhetők el:

      - Költség elemzés
      - Foglalási
      - Eszközgazdálkodás
      - Optimalizálás

  Adja meg egy szűrőnevet, és kattintson a **mentése**.

3. **Címkék**

    A címkék területen csoport címke kategóriák használatához. A menüben szereplő címkék Azure részleg vagy költség center címkék vagy azok Cloudyn tartozó költség entitás és az előfizetés címkék. Válassza ki a címkék eredmények szűrésére. A címke nevét (kulcsszó) eredmények szűrését is megadhatja.

    ![Válassza ki a beállítások](./media/understanding-cost-reports/select-options.png)

    Kattintson a **Hozzáadás** új szűrő hozzáadásához.

    ![szűrő hozzáadása](./media/understanding-cost-reports/add-filter.png)

    Címke csoportosítás, vagy a szűrés nem vonatkozik a Azure-erőforrások vagy erőforráscímkék csoport.

    Költség foglalási címke csoportosítás és a szűrés érhetők el a **csoportok** menüjét.

4. **Csoportokkal a jelentésekben**

    Csoportok használata költség elemzés-jelentést a szokásos, megjelenítése a jelentésben szereplő adatok számlázási kategóriákról részletezett.  Azonban a csoportokat az költség foglalási jelentések megjelenítése nézetkategóriák címke-alapú. Címke-alapú kategóriák költség foglalási modellben és számlázási adatok szabványos részletezett kategóriákról vannak definiálva.

    ![csoportok címkék](./media/understanding-cost-reports/groups-tags01.png)

    ![csoportok címkék](./media/understanding-cost-reports/groups-tags02.png)

    Jelentések költség, a kód alapú csoport kategóriákban csoportok lehetnek:
      - Címkék
      - az erőforráscímkék csoport
      - Cloudyn entitáscímkék költsége
      - Előfizetés címke kategóriák költség foglalási célokra

  Példák a következők lehetnek:
     - Költséghely
     - Részleg
     - Alkalmazás
     - Környezet
     - Költség kódot

5. **Szűrők**

    Egyetlen vagy többszörös kiválasztási szűrők segítségével beállíthatja a kijelölt értékekre tartományokat. Szeretne szűrőt beállítani, kattintson a **Hozzáadás** , és válassza ki a szűrő kategóriákat és az értékeket.

6. **Költség modell**

    Költség-modell segítségével költség foglalási 360 korábban létrehozott költség modell kiválasztása. Lehetséges, hogy több Cloudyn költség modellek, a költség foglalási követelményektől függően. Előfordulhat, hogy néhány szervezeti csoport költség eltér a többi foglalási követelményeinek. Minden team rendelkezhet saját dedikált költség modell.

    Egy költség foglalási model definition létrehozásával kapcsolatos további információkért lásd: [egyéni címkék használata költségek lefoglalni](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Sablon**

    Nem használat megtekintéséhez a költség jelentések használata sablon alapú szolgáltatás költség vagy egyszeri fizetendő, és azok költségét eloszlatva egyenletesen az élettartamot közben. Egyszeri díjak példái a következők lehetnek:
    - Éves támogatási díjak
    - Éves biztonsági összetevők díjak
    - Fenntartott példányok beszerzési díjak
    - Bizonyos Azure piactéren elemek.

  A sablon, válassza a **költség Amortized** vagy **tényleges költség**.

8. **Megoldás**

    Megoldás segítségével válassza ki a kijelölt dátumtartományban idő felbontása. A idő felbontása határozza meg, hogyan jelennek meg a jelentés egységeket, és lehet:
    - Napi
    - Hetente
    - Havi
    - Negyedévente
    - Éves

9. **Elosztási szabályai**

    Felosztási szabályok segítségével alkalmazni, vagy tiltsa le a költség foglalási költség újraszámításkor módosulnak. Engedélyezheti vagy letilthatja a költség elosztási számítás számlázási adatok. A számítás a jelentés a kiválasztott kategóriákra vonatkozik. Lehetővé teszi a költség foglalási újraszámítás hatásának nyers számlázási adatok alapján.

10. **Kategorizálatlan**

    Kategorizálatlan segítségével bevonhat vagy kizárhat a jelentésben nem kategorizált költségeket.

11. **Mezők megjelenítése/elrejtése**

    A megjelenítése/elrejtése beállítás nincs hatással a jelentésekben.

12.   **Megjelenítési formátum**

    Megjelenítési formátum segítségével válassza ki a grafikon vagy tábla különböző nézeteket.

    ![megjelenítési formátum](./media/understanding-cost-reports/display-formats.png)

13. **Több szín**

    A jelentésben szereplő diagramok színének beállításához használja több szín.

14. **Műveletek**

    Műveletek segítségével mentése, exportálhatja vagy ütemezni a jelentést.

## <a name="next-steps"></a>Következő lépések

- Ha még nem fejezte első oktatóanyaga, amely költség-kezelésre, olvassa el a [tekintse át a használati és költségek](tutorial-review-usage.md).
