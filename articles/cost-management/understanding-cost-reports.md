---
title: "Azure költség felügyeleti költség jelentések ismertetése |} Microsoft Docs"
description: "Ez a cikk segít megérteni a Cloudyn költség felügyeleti jelentések alapszintű struktúrát és funkcióit."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 03/07/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: bc2c696dceb3ed4741c10a5c611bd2d438b71bd5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="understanding-cost-management-reports"></a>Költség jelentések ismertetése

Ez a cikk segít megérteni a Cloudyn költség felügyeleti jelentések alapszintű struktúrát és funkcióit. A legtöbb Cloudyn jelentések intuitív és egy egységes megjelenés és működés. Ez a cikk elolvasása után készen áll a költség felügyeleti jelentéseket használhatja. Számos szabványos szolgáltatást a különböző jelentéseket, lehetővé téve, keresse meg a jelentések kihívásokra keresztül érhetőek el. Jelentések testre szabható, és kiszámításához ki és jelenítheti meg az eredmények több lehetőség közül választhat.

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

    Ez egy lista beépített csoportok jelentésekben:

    - **Költségtípus**
      - Válasszon ki egy költségtípus vagy többféle költség, vagy válassza ki az összes. Költség típusok a következők:
        - Egyszeri díj
        - Támogatás
        - Felhasználás költsége
    - **Ügyfél**
        - Válassza ki az adott ügyfélhez, több ügyfélhez, vagy válassza ki az összes ügyfél számára.
    - **Fióknév**
        - A fiók vagy előfizetés nevét. Az Azure az Azure-előfizetés nevét is.
    - **Fiók nem**
        - Jelöljön ki egy fiókot, a több fiók vagy a fiókokhoz. Az Azure az Azure-előfizetés GUID is.
    - **Szülő**
        - Válassza ki a szülő, több fiók, vagy válassza ki az összes.
    - **Szolgáltatás**
        - Jelöljön ki egy szolgáltatást, több szolgáltatás vagy az összes szolgáltatás.
    - **Szolgáltató**
        - A felhő szolgáltató, ahol eszközök és a is társítva.
    - **Régió**
        - Az régió, ahol az erőforrás tárolása.
    - **Rendelkezésre állási zóna**
        - Elkülönített AWS helyek régión belül.
    - **Erőforrás típusa**
        - A használatban lévő erőforrás típusát.
    - **Sub-Type**
        - Válassza ki a altípusa.
    - **Művelet**
        - Válassza ki a műveletet, vagy **az összes megjelenítése**.
    - **Árlista modell**
        - Minden előzetes megfizetése esetén
        - Előzetes megfizetése esetén nem.
        - Részleges előzetes megfizetése esetén
        - Igény szerinti
        - Foglalás
        - Helyszíni
    - **Ingyenesen elérhető típusa**
        - Válassza ki a negatív vagy pozitív kell fizetni típus vagy mindkettő.
    - **Bérlős működés támogatása**
        - Hogy a számítógép egy dedikált gépként fut.
    -   **Használat típusa**
          - Használat típusa lehet díjak egyszeri vagy ismétlődő díjakat.

5. **szűrők**

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

12.   **megjelenítési formátum**

    Megjelenítési formátum segítségével válassza ki a grafikon vagy tábla különböző nézeteket.

    ![megjelenítési formátum](./media/understanding-cost-reports/display-formats.png)

13. **Multi-color**

    A jelentésben szereplő diagramok színének beállításához használja több szín.

14. **Műveletek**

    Műveletek segítségével mentése, exportálhatja vagy ütemezni a jelentést.

## <a name="save-and-schedule-reports"></a>Mentse és jelentések ütemezése

Miután létrehozott egy jelentést, mentheti későbbi használatra. Mentett jelentések érhetők el **saját eszközök** > **jelentések**. Ha módosítja egy meglévő jelentést, és menti, a jelentés mentése új verzióként. Vagy új jelentésként mentheti.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Jelentés mentése a Cloudyn portálra

Kattintson a jelentés megtekintésekor **műveletek** , és válassza **saját jelentések mentése**. A jelentés neve és majd adjon hozzá egy saját URL-cím vagy az automatikusan létrehozott URL-címet használja. Igény szerint is **megosztása** nyilvánosan másokkal a szervezet vagy a jelentés megoszthatja az entitáshoz. Ha nem ugyanazt a jelentést, személyes jelentés marad, és megtekintheti, hogy csak. Mentse a jelentést.


### <a name="save-a-report-to-cloud-provider-storage"></a>A tárolási szolgáltató felhőbe jelentés mentése

Ahhoz, hogy a jelentés mentése a felhőbeli szolgáltatás szolgáltatója, már konfigurálnia kell egy tárfiókot. Kattintson a jelentés megtekintésekor **műveletek** , és válassza **ütemezni a jelentést**. A jelentés neve és majd adjon hozzá egy saját URL-cím vagy az automatikusan létrehozott URL-címet használja. Válassza ki **tárolási mentése** majd válassza ki a tárfiókot, vagy vegyen fel egy másikat. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a fürt megosztott kötetei szolgáltatás vagy a JSON formátumú fájlokat, és mentse a jelentést.

### <a name="schedule-a-report"></a>Ütemezhet egy jelentést

Rendszeres időközönként jelentéseket futtathat, és képes címzett lista vagy a felhőbeli szolgáltatás szolgáltatója tárfiók küldött. Kattintson a jelentés megtekintésekor **műveletek** , és válassza **ütemezni a jelentést**. E-mailben elküldi a jelentést, és a storage-fiók mentése. A **ütemezés**, válassza ki az időköz (napi, heti vagy havi). Heti és havi válassza ki a napot vagy a dátumok kézbesíti, és válassza ki azt az időpontot. Mentse az ütemezett jelentést. Ha az Excel jelentés formátumot, a program mellékletként küldi a jelentést. E-mailek tartalomformátumra kiválasztásakor jelentés eredményeinek formátuma a diagramon megjelenő érkeznek, mint egy grafikonon.

### <a name="export-a-report-as-a-csv-file"></a>Jelentés exportálása CSV-fájlként

Kattintson a jelentés megtekintésekor **műveletek** , és válassza **összes jelentésadatok exportálása**. Megjelenik egy előugró ablak, és egy CSV-fájl letöltése.

## <a name="next-steps"></a>További lépések

- Ha még nem fejezte első oktatóanyaga, amely költség-kezelésre, olvassa el a [tekintse át a használati és költségek](tutorial-review-usage.md).
