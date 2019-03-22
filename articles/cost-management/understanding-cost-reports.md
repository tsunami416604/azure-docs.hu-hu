---
title: Understanding Cloudyn cost jelentések az Azure-ban |} A Microsoft Docs
description: Ez a cikk segítségével megismerheti a Cloudyn cost management jelentések alapszintű struktúrát és funkciók.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 92ce35e88cc73c16e3d3670e07ff8b996192b825
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109814"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Understanding Cloudyn cost management jelentések

Ez a cikk segítségével megismerheti a Cloudyn cost management jelentések alapszintű struktúrát és funkciók. A legtöbb Cloudyn-jelentések intuitív, és rendelkezik egy egységes megjelenés és működés. Miután ez a cikk a cost management jelentésekre készen áll. A különböző jelentéseket, így egyszerűen nyissa meg a jelentések teljes számos standard funkciók érhetők el. Jelentésekhez testre szabható, és alapján számítja ki, és az eredmények megjelenítése több lehetőség közül választhat.

## <a name="report-fields-and-options"></a>Jelentésmezők és beállítások

Íme egy példa az időalapú költségeket tartalmazó jelentést egy pillantást. A legtöbb a Cloudyn jelentésekben hasonló elrendezés rendelkezik.

![A leírások megfelelő számozott területeket időalapú költségeket tartalmazó jelentést – példa](./media/understanding-cost-reports/sample-report.png)

Minden egyes számozott terület az előző képen leírt részletei a következő információkat:

1. **Dátumtartomány**

    A dátumtartomány lista használatával adja meg az előre definiált vagy egyéni jelentés időközt.
2. **Mentett szűrő**

    A mentett lista használatával az aktuális csoportok és a jelentésben alkalmazott szűrők mentése. Költség- és jelentéseket, beleértve a különböző mentett szűrők közül választhat:

      - Költségelemzés
      - Felosztás
      - Eszközgazdálkodás
      - Optimalizálás

   Írja be a szűrő nevét, és kattintson a **mentése**.

3. **Címkék**

    Használja a címkék területen csoporthoz címke kategóriák szerint. A menüben szereplő címkék Azure részleg vagy költség center címkéket és azok a Cloudyn költség entitás- és előfizetés-címkék. Válassza ki a címkékkel szűrheti az eredményeket. Írja be a címke neve (kulcsszó) szűrheti az eredményeket is.

    ![Példa a szűrheti az eredményeket a címkék listája](./media/understanding-cost-reports/select-options.png)

    Kattintson a **Hozzáadás** új szűrő hozzáadásához.

    ![Beállítások és a feltételek szerinti szűrést, a Szűrő mezőbe hozzáadása](./media/understanding-cost-reports/add-filter.png)

    Címkét, csoportosítást, vagy a szűrés nem vonatkozik Azure-erőforrások vagy erőforrás-csoport címkék.

    A költséglefoglalás címke csoportosítás és a szűrés érhető el a **csoportok** menüpont.

4. **Csoportokkal a jelentésekben**

    Csoportok használata költségelemzést a standard szintű, hogy a jelentések részletezett kategóriák a számlázás a jelentésben.  A Költséglefoglalási jelentések megjelenítése a csoportokat azonban címke-alapú kategóriák megtekintéséhez. Címke-alapú kategóriák a költséglefoglalási modell és a standard tételes kategóriák az elszámolási adatok vannak definiálva.

    ![Címkék szerint példa listája](./media/understanding-cost-reports/groups-tags01.png)

    ![Második példa címkelista, amely szerint](./media/understanding-cost-reports/groups-tags02.png)

    Költségek felosztási jelentésekben csoportok csoporthoz címke-alapú kategóriák a következők lehetnek:
      - Címkék
      - erőforráscímkék csoport
      - A Cloudyn-entitáscímkék költség
      - Előfizetés címke kategóriák költségek felosztási célokra

   Példák a következők lehetnek:
   - Költséghely
   - Részleg
   - Alkalmazás
   - Környezet
   - A Cost kód

     Íme a jelentésekben elérhető beépített csoportok listája:

     - **Költségtípus**
     - Válassza ki a költségek típus vagy több költségtípusok, vagy válassza ki az összes. A Cost típusok a következők:
       - Egyszeri
       - Támogatás
       - Használatért díjat kell fizetni
     - **Ügyfél**
       - Jelöljön ki egy adott ügyfél több ügyfelet, vagy minden ügyfélnek.
     - **Fiók neve**
       - A fiók vagy előfizetés nevét. Az Azure-ban az Azure-előfizetés neve.
     - **Fiók nélkül**
       - Válasszon egy fiókot, több fiókot, vagy minden fiók. Az Azure-ban akkor az Azure-előfizetés GUID.
     - **Szülő-fiók**
       - Válassza ki a fölérendelt, több fiókot, vagy válassza ki minden.
     - **Szolgáltatás**
       - Jelöljön ki egy szolgáltatást, több szolgáltatást, vagy az összes szolgáltatás.
     - **Szolgáltató**
       - A felhőszolgáltató, ahol a eszközök és a költségek tartoznak.
     - **Régió**
       - Régió, amelyen az erőforrás található.
     - **Rendelkezésre állási zónában**
       - Az AWS elkülönített helyek egy adott régión belül.
     - **Erőforrás típusa**
       - A használatban lévő erőforrás típusát.
     - **Sub-Type**
       - Válassza ki a altípusa.
     - **Művelet**
       - Válassza ki a műveletet, vagy **az összes megjelenítése**.
     - **Díjszabási modell**
       - Összes előre
       - Előre nem.
       - Részlegesen előre
       - Igény szerinti
       - Foglalás
       - Helyszíni
     - **Díj típusa**
       - Válassza ki a negatív és pozitív díj típus vagy mindkettőt.
     - **Bérleti**
       - Egy gép van futtat-e egy dedikált géppel.
     - **Használat típusa**
       - Használati típus lehet egyszeri díjak vagy ismétlődő díjak.

5. **Szűrők**

    Tartományok megadása a kiválasztott értékek egyetlen vagy többszörös kiválasztási szűrőit használhatja. Állítson be egy szűrőt, kattintson a **Hozzáadás** , majd a szűrés kategóriákat és az értékeket.

6. **A Cost modell**

    Költségmodell segítségével válassza ki a Cost Allocation 360 korábban létrehozott költségmodellt. Lehetséges, hogy a Cloudyn cost több modell, a költségek felosztási követelményeitől függően. Előfordulhat, hogy a szervezeti csoportok némelyike költség foglalási követelményeknek, amelyek eltérnek a mások. Minden egyes csapat rendelkezhet saját dedikált költségmodellt.

    Foglalási költség modell definícióját létrehozásával kapcsolatos információkért lásd: [költségek lefoglalása egyéni címkék használatával](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortizáció**

    Nem használati adatok megtekintéséhez a Költséglefoglalási jelentésekben használható amortizációs szolgáltatás költség vagy egyszeri fizetendő alapulnak, és azok költségei eloszlatva egyenletesen azok élettartama során. Példák egyszeri díjak a következők lehetnek:
    - Éves támogatási díjak
    - Éves biztonsági összetevők díjak
    - Fenntartott példányok vásárlására díjak
    - Néhány Azure Marketplace-elemek.

   Amortizáció, válassza a **amortizált költségek** vagy **tényleges költségek**.

8. **Felbontás**

    Megoldás segítségével válassza ki a alkalommal feloldási belül a kijelölt dátumtartományban. A alkalommal feloldási határozza meg, hogyan egységek jelennek meg a jelentést, és lehet:
    - Napi
    - Hetente
    - Havi
    - Negyedévente
    - Éves

9. **Lefoglalási szabályokat**

    Foglalási szabályok segítségével a alkalmazni, vagy tiltsa le a költségek felosztási költségek újraszámítása. Engedélyezheti vagy letilthatja a költségek felosztási újraszámítás számlázási adatok. A jelentés a kiválasztott kategóriákra az újraszámítás vonatkozik. Lehetővé teszi a költségek felosztási újraszámítás hatásának nyers számlázási adatok.

10. **Kategorizálatlan**

    Kategorizálatlan használatával bevonhat vagy kizárhat a jelentésben szereplő Kategorizálatlan költségeket.

11. **Mezők megjelenítése/elrejtése**

    A megjelenítése/elrejtése beállítás nincs hatással a jelentésekben.

12. **Megjelenítési formátum**

    Megjelenítési formátum segítségével válassza ki a különböző graph vagy tábla nézetek.

    ![Megjelenítési formátum kiválasztható, szimbólumok](./media/understanding-cost-reports/display-formats.png)

13. **Multi-color**

    Több szín használatával a jelentésben szereplő diagramok színének beállítását.

14. **Műveletek**

    Mentse, exportálását és a jelentés ütemezése műveletek használata.

15. **Szabályzat**

    Nem szerepel, bár egyes jelentések plánované náklady számítási szabályzat közé tartozik. A jelentésekben a **konszolidált** házirend az összes fiók és -előfizetések az aktuális entitás, például a Microsoft regisztrációs vagy AWS fizető javaslatait mutatja be. A **önálló** házirend egy fiók vagy előfizetés javaslatait mutatja be, mint ha nincs más előfizetés. A szabályzat kiválasztott függ attól, a szervezet által használt optimalizálási stratégia. A Cost leképezések az elmúlt 30 nap használati alapulnak.

## <a name="save-and-schedule-reports"></a>Mentés és a jelentések ütemezése

Miután létrehozott egy jelentést, mentheti későbbi használatra. Mentett jelentések érhetők el a **saját eszközök** > **jelentések mappa**. Ha módosítja egy meglévő jelentés, és mentheti, a jelentés mentése új verzióként. Vagy új jelentésként is mentheti.

### <a name="save-a-report-to-the-cloudyn-portal"></a>A Cloudyn portálhoz a jelentés mentése

Kattintson bármely jelentés megtekintésekor **műveletek** majd **saját jelentések mentése**. Nevezze el a jelentést, és ezután adjon hozzá egy saját URL-cím vagy az automatikusan létrehozott URL-címet használja. Igény szerint is **megosztása** nyilvánosan másokkal a szervezetben, vagy a jelentést megoszthatja azt az entitást. Ha nem oszt meg a jelentést, személyes jelentést is marad, és megtekintheti, hogy csak. Mentse a jelentést.


### <a name="save-a-report-to-cloud-provider-storage"></a>A felhőalapú tárolási szolgáltató jelentés mentése

Annak érdekében, hogy a jelentés mentése a felhőszolgáltatóhoz, már konfigurálnia kell egy storage-fiókot. Kattintson bármely jelentés megtekintésekor **műveletek** majd **jelentés ütemezése**. Nevezze el a jelentést, és ezután adjon hozzá egy saját URL-cím vagy az automatikusan létrehozott URL-címet használja. Válassza ki **Storage mentése** majd válassza ki a tárfiókot, vagy vegyen fel egy másikat. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a CSV vagy JSON-fájl formátumát, és mentse a jelentést.

### <a name="schedule-a-report"></a>Jelentés ütemezése

Rendszeres időközönként jelentéseket futtathat, és képes címzett lista vagy a cloud service provider tárfiók küldött. Kattintson bármely jelentés megtekintésekor **műveletek** majd **jelentés ütemezése**. A jelentés elküldése e-mailben, és mentse egy tárfiókba. A **ütemezés**, válassza ki az időköz (naponta, hetente vagy havonta). A heti és havi válassza ki a nap vagy dátumokat elküldésére, és válassza ki az időpontot. Mentse az ütemezett jelentést. Ha az Excel-jelentés formátuma választja, a jelentést küld mellékletként. E-mail formátum tartalmak kiválasztásakor a diagram formájában jelenik meg a jelentés eredményeket gráfként érkeznek.

### <a name="export-a-report-as-a-csv-file"></a>Jelentések exportálása CSV-fájlként

Kattintson bármely jelentés megtekintésekor **műveletek** , majd **összes jelentések adatainak exportálása**. Ekkor megjelenik egy előugró ablak, és a egy CSV-fájl letöltése.

## <a name="next-steps"></a>További lépések

- Ismerje meg a jelentéseket, amelyek szerepelnek a Cloudyn [használja a Cloudyn-jelentések](use-reports.md).
- Megtudhatja, hogyan hozhat létre a jelentések segítségével [irányítópultok](dashboards.md).
