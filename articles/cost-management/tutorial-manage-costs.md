---
title: "Azure költség kezelése beépülő modullal költségeinek kezelése |} Microsoft Docs"
description: "Költség foglalás és a visszajelzés és a jóváírási jelentések költségek kezelésére."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/21/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bfbcded98814500a03b2b79b0248c84f8f043dc0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="manage-costs-by-using-azure-cost-management"></a>Azure költség kezelése beépülő modullal költségeinek kezelése

Költségek kezelésére, és az Azure költség Management Cloudyn a visszajelzési jelentéseket készíthet a címkék alapján kiosztásával. Költség foglalási folyamatán a felhasznált felhőalapú erőforrások költségek rendel hozzá. Költségek teljesen foglal le, ha az erőforrások szerint vannak kategóriába címkékkel. Költségek le van foglalva, miután biztosíthat visszajelzés vagy jóváírási a felhasználók számára az irányítópultokat és jelentéseket. Azonban számos olyan forrás lehet címkézetlen vagy untaggable költség felügyeleti indításakor.

Például előfordulhat, hogy kívánt mérnöki költségek térített beolvasása. Tudja megjeleníteni a mérnöki csapathoz, amelyekre szüksége van egy adott értékre, erőforrás költségek alapján kell. Megjelenítheti őket a jelentés a felhasznált erőforrások címkézett *mérnöki*.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni címkék használata költségek lefoglalni.
> * Visszajelzés és a jóváírási jelentések létrehozásához.

## <a name="use-custom-tags-to-allocate-costs"></a>Egyéni címkék használata költségek lefoglalni

Költség foglalási indításakor a először, a hatókör definiálásához költség-modell használatával. A költségek modell nem változtatja meg a költségek, továbbítja azokat. Költség modell létrehozásakor szegmenseket egy, az adatok költség entitás, fiók vagy előfizetés, és több címkék alapján. Általános példa címkék közé tartozik a egy számlázási kódot, a költségközpont vagy a csoport nevét. A címkék segítségével hajtsa végre a visszajelzési vagy a szervezet többi részével jóváírások is.

Egy egyéni költség foglalási modell létrehozásához válassza **költség** &gt; **költség felügyeleti** &gt; **költség foglalási 360°** a jelentés menüben.

![Költség foglalási 360 kiválasztása](./media/tutorial-manage-costs/cost-allocation-360.png)

Az a **költség foglalási 360** lapon jelölje be **Hozzáadás** és írja be nevét és leírását, a költség modell. Válassza ki az összes fiókok vagy egyedi fiókokat. Ha azt szeretné, egyedi fiókokat használ, több fiók választhat több felhőszolgáltatók. Ezután kattintson **kategorizálási** , a költség adatok kategorizálása felderített címkék kiválasztásához. Válassza ki a modell szerepeltetni kívánt címkéket (kategóriák). A következő példában a **egység** címke van kiválasztva.

![Példa költség modell kategorizálási](./media/tutorial-manage-costs/cost-model01.png)



A példa bemutatja, hogy $14,444 Kategorizálatlan (nélkül).

Válassza ki, **Kategorizálatlan erőforrások** , és válassza ki, amely nem lefoglalt költségek rendelkezni szolgáltatásokat. Ezután határozzon meg szabályok költségek lefoglalni.

Például érdemes az Azure storage költségei és felosztani a költségeket egyaránt az Azure virtuális gépek (VM). Ehhez az szükséges, válassza ki a **Azure tárolási** szolgáltatás, válassza **Categorized arányos**, majd válassza ki **Azure vagy Virtuálisgép**. Ezt követően válassza **létrehozása**.

![Példa költség modell felosztási szabály egyenlő terjesztéshez](./media/tutorial-manage-costs/cost-model02.png)



Egy másik példa lehet szükség, az Azure-hálózat költségeit lefoglalása egy adott részleg a szervezet számára. Ehhez az szükséges, válassza ki a **Azure/hálózati** szolgáltatást, majd **Explicit terjesztési**. Ezután, a terjesztési-százalék beállítása 100, és válassza a részleg –**G&amp;A** az alábbi képen:

![Példa költség modell felosztási szabály egy adott üzleti egység](./media/tutorial-manage-costs/cost-model03.png)



Az összes többi Kategorizálatlan erőforrás kiegészítő szabályokat létrehozni.

Ha le nem foglalt Amazon Web Services (AWS) fenntartott példányok, hozzárendelheti azokat a címkézett kategóriákhoz **fenntartott példányok**.

Válassza ki, ha a megadott költségek lefoglalni választások információ **összegzés**. Menti az adatokat, és később folytathatja a munkát a további szabályok, válassza ki a **mentése vázlatként megjelölt**. Vagy menti az adatokat, és indítsa el, a költség foglalási modell feldolgozása Cloudyn rendelkezik, válasszon **mentéséhez, majd aktiválja**.

A költségek modellek jeleníti meg az új költség modellel **feldolgozás állapota**. Egy ideig, amíg a Cloudyn adatbázis frissül, a költség modellel is igénybe vehet. Nem hajtja végre, amikor az állapot frissül **befejezve**. A költségek elemző jelentés alapján, a költség modellből tekinthetnek **kiterjesztett szűrők** &gt; **költség modell**.

### <a name="category-manager"></a>Kategória Manager

Kategória Manager olyan adatok tisztítására eszköz, amely segít több kategóriák (címke) hozzon létre újakat értékének egyesítése. Ez egy olyan egyszerű szabályalapú eszköz válasszon egy kategóriát, és ahol a egyesítése a létező értéket a szabályt hoz létre. Például lehetséges, hogy a meglévő kategóriák **R&amp;D** és **fejlesztői** ahol mindkettő határoz meg a fejlesztési csoport.

A Cloudyn portálon kattintson a felső, jobb és válassza ki a fogaskerék szimbólum **kategória Manager**. Hozzon létre új kategóriát, válassza ki a pluszjelre (**+**). Adjon meg egy nevet a kategória, majd a **kulcsok**, adja meg az új kategória szerepeltetni kívánt kategória kulcsok.

Ha egy szabály, vagy feltétel megadásával több értéket is hozzáadhat. Néhány alapvető karakterlánc műveleteket is végezhet. Mindkét esetben kattintson a három pont szimbólum (**...** ) jobb oldalán **szabály**.

Adjon meg egy új szabályt, hogy a **szabályok** terület, hozzon létre egy új szabályt. Adja meg például **fejlesztői** alatt **szabályok** és írja be **R&amp;D** alatt **műveletek**. Amikor elkészült, mentse az új kategóriát.

Az alábbi ábrán egy példa egy új kategóriát a létrehozott szabályok **munkaterhelés**:

![Kategória – példa](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Címke források és jelentések

Címke adatait Cloudyn jelentések látható három helyen származik:

- Felhő szolgáltató erőforrásai API-k
- API-k számlázási felhőszolgáltatóként
- Manuálisan létrehozott címkék a következő forrásból:
    - Cloudyn entitáscímkék - Cloudyn entitások alkalmazott felhasználói metaadatai
    - Kategória-kezelő – a adatok tisztítására eszköz, amely meglévő címkék alkalmazott szabályok alapján új címkék létrehozása

Felhő szolgáltató címkék Cloudyn költség jelentésekben megjelenített egyéni költség foglalási-modellben költség foglalási 360 kell létrehoznia. Ehhez nyissa meg a **költség** > **költség felügyeleti** > **költség foglalási 360**, válassza ki a kívánt címkét, és adja a szabályok kezelésének címkézetlen költségek. Ezután hozzon létre egy új költség modell. Ezután jelentések megtekintéséhez költség foglalási elemzés nézet, szűrési és rendezési az Azure erőforrás-címkéket.

Az Azure erőforrás-címkék csak akkor jelennek meg **költség foglalási elemzés** jelentéseket.

Felhő szolgáltató számlázási címkék összes költség jelentések jelennek meg.

Cloudyn entitáscímkék és címkék manuálisan létrehozott összes költség jelentések jelennek meg.


## <a name="create-showback-and-chargeback-reports"></a>Visszajelzés és a jóváírási jelentések létrehozása

A metódus visszajelzési és jóváírási használó szervezetek jelentősen változhat. Azonban használhatja a irányítópultokat és jelentéseket a Cloudyn portálon alapját képező vagy célra. Megadhatja a felhasználói hozzáférés bárki számára a szervezetében, hogy az igény szerinti adatmegjelenítésre irányítópultokat és jelentéseket. Az összes költség elemzési jelentések visszajelzési támogatja, mert azok által felhasznált erőforrások megjelenítése felhasználók. És lehetővé teszik a költség, vagy a használati adatok a szervezeten belül a csoporthoz adott elemezze.

Költség foglalási eredményeinek megtekintéséhez nyissa meg a költség elemző jelentés, és válassza ki a létrehozott költség modell. Adja hozzá egy vagy több, a címkék, a költség modell kiválasztott csoportosítása.

![Költség Teljesítményelemzésének jelentése.](./media/tutorial-manage-costs/cost-analysis.png)

Egyszerűen létrehozása és mentése a jelentések, amelyek arra utalnak a meghatározott csoportok által használt egyes szolgáltatások. Például lehetséges, hogy a részleg által nagymértékben használt Azure virtuális gépeken. Használati és költségek megjelenítendő Azure virtuális gépeken szűrt jelentést hozhat létre.

Ha meg kell adnia a pillanatkép-adatok más csoportokhoz, PDF- vagy CSV formátumú jelentései exportálhatók.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egyéni címkék használata költségek lefoglalni.
> * Visszajelzés és a jóváírási jelentések létrehozásához.



Ismerkedés a Cloudyn kapcsolatos további, és a funkciók használatához előzetes Cloudyn dokumentációját.

> [!div class="nextstepaction"]
> [Cloudyn dokumentáció](https://support.cloudyn.com/hc/)
