---
title: A Cloudyn-kezelési jelentések ismertetése az Azure-ban | Microsoft Docs
description: Ez a cikk segítséget nyújt a Cloudyn-kezelési jelentések alapvető szerkezetének és funkcióinak megismerésében.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: fff08bc30e0d1bae77a2aee641f6e211cea40beb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989110"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Cloudyn-kezelési jelentések ismertetése

Ez a cikk segítséget nyújt a Cloudyn-kezelési jelentések alapvető szerkezetének és funkcióinak megismerésében. A legtöbb Cloudyn-jelentés intuitív, és egységes megjelenéssel és működéssel rendelkezik. A cikk elolvasása után készen áll az összes Cost Management-jelentés használatára. Számos szabványos szolgáltatás érhető el a különböző jelentésekben, így könnyedén böngészhet a jelentésekben. A jelentések testreszabhatók, és az eredmények kiszámításához és megjelenítéséhez több lehetőség közül választhat.

## <a name="report-fields-and-options"></a>Jelentés mezői és beállításai

Íme egy példa a bekerülési idő jelentésre. A legtöbb Cloudyn-jelentés hasonló elrendezésű.

![Példa a jelentésekhez tartozó, a leírásoknak megfelelő számú területtel járó időpontra](./media/understanding-cost-reports/sample-report.png)

Az előző rendszerkép minden egyes számozott területén részletes leírást talál a következő információkban:

1. **Dátumtartomány**

    A dátumtartomány listával határozhatja meg a jelentés időintervallumát egy előre definiált vagy egyéni érték használatával.
2. **Mentett szűrő**

    A mentett szűrő lista használatával mentheti a jelentésre alkalmazott aktuális csoportokat és szűrőket. A mentett szűrők a díjak és a teljesítmény jelentésekben érhetők el, beleértve a következőket:

      - Költségelemzés
      - Memóriafoglalás
      - Eszközgazdálkodás
      - Optimalizálás

   Írja be a szűrő nevét, majd kattintson a **Mentés**gombra.

3. **Címkék**

    Címkézett kategóriák szerint csoportosítsa a címkék területeket. A menüben felsorolt címkék az Azure Department vagy a Cost Center címkéi, vagy a Cloudyn és az előfizetés címkéi. Az eredmények szűréséhez válassza a címkék lehetőséget. Az eredmények szűréséhez a címke neve (kulcsszó) is megadható.

    ![Példa az eredmények szűréséhez használandó címkék listájára](./media/understanding-cost-reports/select-options.png)

    Új szűrő hozzáadásához kattintson a **Hozzáadás** gombra.

    ![A szűréshez használandó beállításokat és feltételeket megjelenítő szűrő hozzáadása](./media/understanding-cost-reports/add-filter.png)

    A címkék csoportosítása vagy szűrése nem kapcsolódik az Azure-erőforrásokhoz vagy az erőforráscsoport-címkékkel.

    A lefoglalási címke csoportosítása és szűrése a **csoportok** menüpontban érhető el.

4. **Csoportok a jelentésekben**

    A Cost Analysis-jelentésekben lévő csoportok használatával szabványos, részletezett kategóriákat jeleníthet meg a jelentés számlázási adataiból.  A Cost foglalási jelentésekben azonban csoportok láthatók a címkéken alapuló kategóriák megjelenítéséhez. A címkén alapuló kategóriák a számlázási adatokból származó költségfelosztás modellben és a szabványos részletezett kategóriákban vannak meghatározva.

    ![A csoportosítható címkék első példáinak listája](./media/understanding-cost-reports/groups-tags01.png)

    ![A csoportosítható címkék második példája](./media/understanding-cost-reports/groups-tags02.png)

    A Cost foglalási jelentésekben a címkéken alapuló csoportok csoportjai a következők lehetnek:
      - Címkék
      - erőforráscsoport-Címkék
      - Cloudyn-entitások címkéi
      - Előfizetési címke kategóriái a Cost allokációs célokra

   Ilyenek például az alábbiak:
   - Költséghely
   - Részleg
   - Jelentkezés
   - Környezet
   - Költségadatok

     Itt látható a jelentésekben elérhető beépített csoportok listája:

     - **Cost típusa**
     - Válassza ki a Cost típust vagy a többszörös bekerülési értéket, vagy válassza az összes lehetőséget. A Cost típusai a következők:
       - Egyszeri díj
       - Támogatás
       - Használati díj
     - **Ügyfél**
       - Válasszon ki egy adott ügyfelet, több ügyfelet, vagy válassza az összes ügyfél lehetőséget.
     - **Fiók neve**
       - A fiók vagy az előfizetés neve. Az Azure-ban ez az Azure-előfizetés neve.
     - **Fiók nem**
       - Válasszon ki egy fiókot, több fiókot vagy az összes fiókot. Az Azure-ban ez az Azure-előfizetés GUID-azonosítója.
     - **Fölérendelt fiók**
       - Válassza ki a fölérendelt fiókot, több fiókot, vagy válassza az összes lehetőséget.
     - **Service**
       - Válasszon ki egy szolgáltatást, több szolgáltatást, vagy válassza a minden szolgáltatás lehetőséget.
     - **Szolgáltató**
       - A felhőalapú szolgáltató, amelyben az eszközök és a költségek társítva vannak.
     - **Régió**
       - Az a régió, ahol az erőforrás üzemeltetve van.
     - **Rendelkezésre állási zóna**
       - A régión belüli AWS elkülönített telephelyek.
     - **Erőforrás típusa**
       - A használatban lévő erőforrás típusa.
     - **Altípus**
       - Válassza ki az altípust.
     - **Művelet**
       - Válassza ki a műveletet, vagy az **összes megjelenítése**lehetőséget.
     - **Ár modell**
       - Minden előzetes
       - Nincs előzetes
       - Részleges kezdeti
       - Igény szerinti
       - Foglalás
       - Spot
     - **Díj típusa**
       - Válassza a negatív vagy a pozitív töltési típust vagy mindkettőt.
     - **Bérleti**
       - Azt jelzi, hogy a gép dedikált számítógépként fut-e.
     - **Használat típusa**
       - A használati típus lehet egyszeri díj vagy ismétlődő díj.

5. **Szűrők**

    Egy vagy több kiválasztható szűrő használatával tartományokat állíthat be a kijelölt értékekre. Szűrő beállításához kattintson a **Hozzáadás** elemre, majd válassza a kategóriák és értékek szűrése lehetőséget.

6. **Cost Model**

    A Cost Model használatával kiválaszthatja azt a Cost modellt, amelyet korábban a 360-es Cost-foglalással hozott létre. A költségfelosztás követelményeitől függően több Cloudyn is rendelkezhet. A szervezeti csapatok némelyike a többitől eltérő díjszabási követelményekkel rendelkezhet. Minden csapat rendelkezhet saját dedikált Cost-modellel.

    A Cost foglalási modell definíciójának létrehozásával kapcsolatos információkért lásd: [egyéni címkék használata a költségek lefoglalásához](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortizáció**

    A Cost allokációs jelentésekben a nem használatos szolgáltatási díjak, illetve az egyszeri fizetendő költségek és a költségek az élettartamuk során egyenletes elosztása után is kiterjeszthetők. Az egyszeri díjak például a következők lehetnek:
    - Éves támogatási díjak
    - Az éves biztonsági összetevők díjai
    - Fenntartott példányok beszerzési díjai
    - Néhány Azure Marketplace-elem.

   Az amortizáció alatt válassza ki az elszámolt **költségeket** vagy a **tényleges költségeket**.

8. **Felbontás**

    A felbontás használatával válassza ki a kijelölt dátumtartomány időpontját. Az idő felbontása határozza meg, hogy az egységek hogyan jelenjenek meg a jelentésben, és a következők lehetnek:
    - Napi
    - Hetente
    - Havi
    - Negyedévente
    - Éves

9. **Foglalási szabályok**

    A foglalási szabályok segítségével alkalmazhatja vagy letilthatja a költségmegosztás költségeit. Engedélyezheti vagy letilthatja a számlázási információkhoz tartozó költségfelosztás újraszámítását. Az újraszámítás a jelentés kiválasztott kategóriáira vonatkozik. Lehetővé teszi, hogy felmérje a foglalások újraszámításának hatását a nyers számlázási információkra.

10. **Kategorizálatlan**

    A Uncategorized használatával belefoglalhatja vagy kizárhatja a Uncategorized költségeit a jelentésben.

11. **Mezők megjelenítése/elrejtése**

    A Megjelenítés/elrejtés lehetőség nem befolyásolja a jelentéseket.

12. **Megjelenítési formátumok**

    Különböző Graph-vagy táblázatos nézetek kiválasztásához használjon megjelenítési formátumokat.

    ![A kiválasztható megjelenítési formátumok szimbólumai](./media/understanding-cost-reports/display-formats.png)

13. **Több szín**

    A többszínű beállítással beállíthatja a jelentésekben szereplő diagramok színét.

14. **Műveletek**

    A jelentés mentéséhez, exportálásához vagy beütemezett műveleteihez használhatja a műveleteket.

15. **Szabályzat**

    Bár a kép nem látható, néhány jelentés tartalmaz egy előre jelzett költségszámítási szabályzatot. Ezekben a jelentésekben az **összevont** szabályzat az aktuális entitáshoz tartozó összes fiókra és előfizetésre vonatkozó javaslatokat jelenít meg, például a Microsoft beléptetését vagy az AWS-megbízót. Az **önálló** házirend egy fiókra vagy előfizetésre vonatkozó javaslatokat jelenít meg, mintha nem léteznek más előfizetések. A kiválasztott szabályzat a szervezet által használt optimalizálási stratégiától függ. A díjak kivetítése a használat utolsó 30 napján alapul.

## <a name="save-and-schedule-reports"></a>Jelentések mentése és ütemezhetve

Miután létrehozta a jelentést, mentheti későbbi használatra. A mentett jelentések a **saját eszközök** > **saját jelentések**érhetők el. Ha módosít egy meglévő jelentést, és menti azt, a jelentés új verzióként lesz mentve. Azt is megteheti, hogy új jelentésként menti.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Jelentés mentése a Cloudyn-portálra

A jelentések megtekintése közben kattintson a **műveletek** elemre, majd válassza a **Mentés a saját jelentésekhez**lehetőséget. Nevezze el a jelentést, majd adjon hozzá egy saját URL-címet, vagy használja az automatikusan létrehozott URL-címet. Igény szerint **megoszthatja** a jelentést a szervezet többi részével, vagy megoszthatja azt az entitással. Ha nem osztja meg a jelentést, továbbra is személyes jelentés marad, és csak megtekintheti. Mentse a jelentést.


### <a name="save-a-report-to-cloud-provider-storage"></a>Jelentés mentése a felhőalapú szolgáltatói tárolóba

Ahhoz, hogy egy jelentést a Cloud Service-szolgáltatóhoz mentsen, már konfigurálva kell lennie egy Storage-fióknak. A jelentések megtekintése közben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. Nevezze el a jelentést, majd adjon hozzá egy saját URL-címet, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Mentés a tárolóba** lehetőséget, majd válassza ki a Storage-fiókot, vagy adjon hozzá egy újat. Adjon meg egy előtagot, amely hozzáfűzve lesz a jelentési fájl nevéhez. Válasszon ki egy CSV-vagy JSON-fájlformátumot, majd mentse a jelentést.

### <a name="schedule-a-report"></a>Jelentés beosztása

A jelentéseket ütemezett időközönként is futtathatja, és elküldheti azokat egy címzettlista vagy felhőalapú szolgáltatói Storage-fiókba. A jelentések megtekintése közben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. A jelentést e-mailben is elküldheti, és mentheti a Storage-fiókba. Az **ütemterv**területen válassza ki az intervallumot (naponta, hetente vagy havonta). Hetente és havonta válassza ki a kézbesíteni kívánt napot vagy dátumokat, és válassza ki az időpontot. Mentse az ütemezett jelentést. Ha az Excel-jelentés formátumát választja, a rendszer mellékletként küldi el a jelentést. Ha az e-mail-tartalom formátumot választja, a diagram formátumban megjelenő jelentések eredményei gráfként lesznek továbbítva.

### <a name="export-a-report-as-a-csv-file"></a>Jelentés exportálása CSV-fájlként

A jelentések megtekintése közben kattintson a **műveletek** elemre, majd válassza **az összes jelentés-információ exportálása**lehetőséget. Megjelenik egy előugró ablak, és a rendszer letölti a CSV-fájlt.

## <a name="next-steps"></a>Következő lépések

- További információ a Cloudyn-ben található, a [Cloudyn-jelentések használatáról](../../cost-management/use-reports.md)szóló jelentésekről.
- Útmutató az [irányítópultok](../../cost-management/dashboards.md)létrehozásához a jelentések használatával.
