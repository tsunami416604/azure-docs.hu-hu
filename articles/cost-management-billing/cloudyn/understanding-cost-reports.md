---
title: A Cloudyn Cost Management jelentéseinek ismertetése
description: Ez a cikk segít megismerni a Cloudyn Cost Management jelentéseinek alapszerkezetét és funkcióit.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 5d4f8e26aef5fbfb20c1721d1851a4940863121b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474543"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>A Cloudyn Cost Management jelentéseinek ismertetése

Ez a cikk segít megismerni a Cloudyn Cost Management jelentéseinek alapszerkezetét és funkcióit. A legtöbb Cloudyn-jelentés könnyen érthető, illetve egységes megjelenéssel és működéssel rendelkezik. A cikk elolvasása után Ön készen áll az összes Cost Management-jelentés használatára. Számos szabványos funkció érhető el a különböző jelentésekben, amelyekben így könnyedén böngészhet. A jelentések testreszabhatók, és az eredmények kiszámításához és megjelenítéséhez több lehetőség közül választhat.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-fields-and-options"></a>Jelentésmezők és beállítások

Vessünk egy pillantást a Cost Over Time (Költségek az adott időszakban) jelentésre. A legtöbb Cloudyn-jelentés hasonló elrendezésű.

![Példa a Cost Over Time (Költségek az adott időszakban) jelentésre, ahol a számozott területek leírásokat jelölnek](./media/understanding-cost-reports/sample-report.png)

Az előző képen látható számozott területek részletes leírását a következő információlista tartalmazza:

1. **Date Range** (Dátumtartomány)

    A Date Range (Dátumtartomány) listával határozhatja meg a jelentés időintervallumát egy előre meghatározott vagy egyéni érték használatával.
2. **Saved Filter** (Mentett szűrő)

    A Saved Filter (Mentett szűrő) lista használatával mentheti a jelentésre alkalmazott, jelenlegi csoportokat és szűrőket. A mentett szűrők más költség- és a teljesítményjelentésekben is elérhetők, beleértve a következőket:

      - Cost Analysis (Költségelemzés)
      - Allocation (Memóriafoglalás)
      - Asset Management (Eszközkezelés)
      - Optimalizálás

   Adja meg egy szűrő nevét, majd kattintson a **Save** (Mentés) elemre.

3. **Címkék**

    A Tags (Címkék) terület a címkekategóriák szerinti csoportosítást teszi lehetővé. A menüben felsorolt címkék az Azure-részleg vagy a költségközpont címkéi, vagy a Cloudyn költségentitási és előfizetési címkéi lehetnek. Az eredmények szűrése címkék kiválasztásával végezhető el. Emellett az eredmények szűrése egy címkenév (kulcsszó) beírásával is lehetséges.

    ![Példák az eredmények szűréséhez használható címkékre](./media/understanding-cost-reports/select-options.png)

    Új szűrő hozzáadásához kattintson az **Add** (Hozzáadás) elemre.

    ![A szűréshez használandó beállításokat és feltételeket megjelenítő szűrőmező hozzáadása](./media/understanding-cost-reports/add-filter.png)

    A címkecsoportosítás vagy -szűrés nincs összefüggésben az Azure-erőforrások vagy -erőforráscsoportok címkéivel.

    A költséglefoglalási címkecsoportosítás és -szűrés a **Groups** (Csoportok) menübeállításban érhető el.

4. **Csoportok a jelentésekben**

    A Cost Analysis-jelentésekben lévő csoportok használatával szabványos, tételes kategóriákat jeleníthet meg a jelentés számlázási adataiból.  A költséglefoglalási jelentésekben azonban a csoportok címkealapú kategóriákat jelenítenek meg. A címkealapú kategóriák a költséglefoglalási modellben és a számlázási adatokon alapuló, szabványos tételes kategóriákban vannak meghatározva.

    ![A csoportosításhoz használható címkék első példalistája](./media/understanding-cost-reports/groups-tags01.png)

    ![A csoportosításhoz használható címkék második példalistája](./media/understanding-cost-reports/groups-tags02.png)

    A költséglefoglalási jelentésekben a címkealapú csoportkategóriákban szereplő csoportok a következők lehetnek:
      - Címkék
      - erőforráscsoport-címkék
      - Cloudyn-költségentitás-címkék
      - Előfizetési címkekategóriák költséglefoglalási célokra

   Példák erre vonatkozóan:
   - Költséghely
   - Részleg
   - Alkalmazás
   - Környezet
   - Költségkód

     Íme egy lista a jelentésekben benne foglalt csoportokról:

     - **Cost Type** (Költségtípus)
     - Kiválaszthat egy vagy több költségtípust, vagy kijelölheti az összeset. A költségtípusok a következők lehetnek:
       - Egyszeri díj
       - Támogatás
       - Használati költség
     - **Customer** (Ügyfél)
       - Kiválaszthat egy bizonyos ügyfelet, több ügyfelet, vagy kijelölheti az összeset.
     - **Account Name** (Fióknév)
       - A fiók vagy az előfizetés neve. Az Azure esetében ez az Azure-előfizetés neve.
     - **Account No** (Fiókszám)
       - Kiválaszthat egy vagy több fiókot, vagy kijelölheti az összeset. Az Azure esetében ez az Azure-előfizetés bérlőazonosítója (GUID).
     - **Parent Account** (Szülőfiók)
       - Kiválaszthat egy vagy több szülőfiókot, vagy kijelölheti az összeset.
     - **Service**
       - Kiválaszthat egy vagy több szolgáltatást, vagy kijelölheti az összeset.
     - **Szolgáltató**
       - Az a felhőszolgáltató, amelyhez az adategységek és költségek kapcsolódnak.
     - **Régió**
       - Az a régió, ahol az erőforrás üzemeltetése történik.
     - **Availability Zone** (Rendelkezésreállási zóna)
       - Az AWS izolált helyei egy régión belül.
     - **Erőforrás típusa**
       - A használatban lévő erőforrás típusa.
     - **Sub-Type** (Altípus)
       - Kiválaszthatja az altípust.
     - **Művelet**
       - A kívánt művelet kiválasztása vagy **Show All** (Az összes megjelenítése).
     - **Price Model** (Ármodell)
       - Teljes előzetes fizetés
       - Nincs előzetes fizetés
       - Részleges előzetes fizetés
       - Igény szerint
       - Foglalás
       - Kihasználatlan
     - **Charge Type** (Díj típusa)
       - Választhatja a negatív vagy a pozitív díjtípust, vagy mindkettőt.
     - **Tenancy** (Bérlő)
       - Meghatározza, hogy a számítógép dedikált eszközként fut-e.
     - **Usage Type** (Használat típusa)
       - A használat típusa lehet egyszeri díj vagy ismétlődő díj.

5. **Filters** (Szűrők)

    Egy vagy több kiválasztható szűrő használatával tartományokat állíthat be a kijelölt értékekhez. Szűrő beállításához kattintson az **Add** (Hozzáadás) elemre, majd válassza ki a szűrőkategóriákat és értékeket.

6. **Cost Model** (Költségmodell)

    A Költségmodell használatával kiválaszthatja azt a költségmodellt, amelyet korábban hozott létre a Cost Allocation 360 eszközzel. A költséglefoglalási követelményektől függően több Cloudyn-költségmodellel is rendelkezhet. A szervezeti csapatok némelyike a többitől eltérő költséglefoglalási követelményekkel rendelkezhet. Minden egyes csapat saját dedikált költségmodellel rendelkezhet.

    A költséglefoglalási modellek definíciójának létrehozásával kapcsolatos információkat lásd: [Költségek lefoglalása egyéni címkék használatával](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortization** (Amortizáció)

    A költséglefoglalási jelentésekben az Amortizáció a nem használatalapú szolgáltatási díjakat vagy az egyszer kifizetendő költségeket jeleníti meg, és egyenletesen elosztja a folyamatos költségeket az élettartamuk során. Az egyszeri díjak például a következők lehetnek:
    - Éves támogatási díjak
    - A biztonsági összetevők éves díjai
    - Fenntartott példányok beszerzési illetékei
    - Néhány Azure Marketplace-elem.

   Az Amortization (Amortizáció) területen válassza az **Amortized cost** (Amortizált költség) vagy az **Actual Cost** (Tényleges költség) lehetőséget.

8. **Resolution** (Osztás)

    Az Osztás használatával a kijelölt dátumtartományon belül meghatározhatja az időosztást. Az időosztás meghatározza, hogy az egységek hogyan jelenjenek meg a jelentésben, és a következő lehet:
    - Napi
    - Heti
    - Havonta
    - Negyedévi
    - Évi

9. **Allocation rules** (Lefoglalási szabályok)

    A lefoglalási szabályok segítségével alkalmazhatja vagy letilthatja a költséglefoglalás költségeinek újraszámítását. Engedélyezheti vagy letilthatja a költséglefoglalás újraszámítását a számlázási adatok vonatkozásában. Az újraszámítás a jelentésben kijelölt kategóriákra vonatkozik. Lehetővé teszi a költséglefoglalás újraszámítása által okozott hatás kiértékelését a nyers számlázási adatok vonatkozásában.

10. **Uncategorized** (Kategorizálatlan)

    A Kategorizálatlan lehetőség használatával belefoglalhatja a kategorizálatlan költségeket a jelentésbe vagy kizárhatja őket onnan.

11. **Show/hide fields** (Mezők megjelenítése/elrejtése)

    A Megjelenítés/elrejtés beállításnak nincs hatása a jelentésekben.

12. **Display formats** (Megjelenítési formátumok)

    A Megjelenítési formátumok használatával különféle grafikonok és táblázatos nézetek közül választhat.

    ![A választható megjelenítési formátumok szimbólumai](./media/understanding-cost-reports/display-formats.png)

13. **Multi-color** (Többszínű)

    A Többszínű használatával beállíthatja a jelentésben szereplő diagramok színét.

14. **Műveletek**

    A Műveletek a jelentés mentéséhez, exportálásához vagy beütemezett műveleteihez használható.

15. **Szabályzat**

    Bár az ábrán nem szerepel, néhány jelentés tartalmaz egy előre jelzett költségszámítási szabályzatot. Ezekben a jelentésekben a **Consolidated** (Konszolidált) szabályzat az aktuális entitáshoz tartozó összes fiókra és előfizetésre vonatkozóan javaslatokat jelenít meg, például a Microsoft-regisztráció vagy az AWS-fizető esetében is. A **Standalone** (Különálló) szabályzat egy fiókra vagy előfizetésre vonatkozóan jelenít meg javaslatokat, mintha más előfizetés nem létezne. A kiválasztott szabályzat a szervezet által alkalmazott optimalizálási stratégiától függ. A költség-előrejelzések az elmúlt 30 nap használati adatain alapulnak.

## <a name="save-and-schedule-reports"></a>A jelentések mentése és ütemezése

Miután létrehozott egy jelentést, mentheti későbbi használatra. A mentett jelentések elérhetők a **My Tools** (Saját eszközök) > **My Reports** (Saját jelentések) között. Egy meglévő jelentés módosítása és mentése esetén a jelentés mentése új verzióként történik. Emellett a jelentést újként is mentheti.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Jelentés mentése a Cloudyn portálon

Egy jelentés megtekintése közben kattintson az **Actions** (Műveletek) elemre, majd válassza a **Save to my reports** (Mentés a saját jelentésekbe) lehetőséget. Nevezze el a jelentést, majd adja hozzá a saját URL-címét, vagy használja az automatikusan létrehozott URL-címet. Választhatja a nyilvános **Share** (Megosztás) lehetőségét is, hogy a szervezetben mások is láthassák a jelentést, vagy a saját entitásával is megoszthatja. Ha nem osztja meg a jelentést, az továbbra is személyes jelentés marad, és csak Ön tekintheti meg. Mentse a jelentést.


### <a name="save-a-report-to-cloud-provider-storage"></a>Jelentés mentése a felhőszolgáltató tárhelyére

Ha a felhőszolgáltató tárhelyére kíván menteni egy jelentést, ehhez egy már konfigurált tárfiókkal kell rendelkeznie. Egy jelentés megtekintése közben kattintson az **Actions** (Műveletek) gombra, majd válassza a **Schedule report** (Jelentés ütemezése) lehetőséget. Nevezze el a jelentést, majd adja hozzá a saját URL-címét, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Save to storage** (Mentés tárolóba) elemet, majd válassza ki a tárfiókot, vagy adjon hozzá egy újat. Adjon meg egy előtagot, amelyet a rendszer a jelentés fájlnevéhez fűz. Válassza ki a CSV vagy a JSON formátumot, majd mentse a jelentést.

### <a name="schedule-a-report"></a>Jelentések ütemezése

A jelentéseket ütemezett időközönként is futtathatja, és elküldheti őket egy címzettlistára vagy egy felhőszolgáltatói tárfiókba. Egy jelentés megtekintése közben kattintson az **Actions** (Műveletek) gombra, majd válassza a **Schedule report** (Jelentés ütemezése) lehetőséget. A jelentést elküldheti e-mailben, és mentheti egy tárfiókba. A **Schedule** (Ütemezés) lehetőségnél válassza ki az időszakot (napi, heti vagy havi). A heti és a havi beállítás esetén válassza ki a kívánt kézbesítési napot vagy dátumokat, és adja meg az időpontot. Mentse az ütemezett jelentést. Ha az Excel jelentésformátumát választja, a rendszer mellékletként küldi el a jelentést. Ha az e-mail-tartalom formátumot választja, a jelentés diagramként megjelenített eredményei grafikonként lesznek továbbítva.

### <a name="export-a-report-as-a-csv-file"></a>Jelentés exportálása CSV-fájlként

Egy jelentés megtekintése közben kattintson az **Actions** (Műveletek) elemre, majd válassza az **Export all report data** (Összes jelentésadat exportálása) lehetőséget. Megjelenik egy előugró ablak, és a rendszer letölt egy CSV-fájlt.

## <a name="next-steps"></a>További lépések

- Ismerje meg a Cloudyn részeként elérhető jelentéseket [A Cloudyn jelentéseinek használata](../../cost-management/use-reports.md) című fejezetből.
- Ismerje meg, hogyan készíthet [irányítópultokat](../../cost-management/dashboards.md) a jelentések használatával.
