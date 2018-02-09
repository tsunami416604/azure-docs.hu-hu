---
title: "Költségek kezelése az Azure Cost Managementtel | Microsoft Docs"
description: "Költségek kezelése költséglefoglalási, költséghelyi visszacsatolási és költséghelyi elszámolási jelentésekkel."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 804b50d6ba054bbb0eb60b659c98f161ea5272ee
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="manage-costs-by-using-azure-cost-management"></a>Költségek kezelése az Azure Cost Managementtel

Az Azure Cost Management by Cloudynben a költségeket címkék alapján foglalhatja le a költségek kezelése és a visszacsatolási jelentések előállítása érdekében. A költséglefoglalási folyamat során költségeket rendel hozzá a felhasznált felhőalapú erőforrásokhoz. A költségek teljes lefoglalása akkor történik meg, ha az összes erőforrást kategóriákba sorolta címkék segítségével. A költségek lefoglalását követően irányítópultokon és jelentéseken keresztül biztosíthat költséghelyi visszacsatolást és elszámolást a felhasználóknak. Azonban a Cost Management használatba vételekor előfordulhat, hogy számos erőforrás nem rendelkezik vagy nem látható el címkével.

Például előfordulhat, hogy szeretné megtéríttetni a műszaki tervezési költségeket. Be kell tudnia mutatni a műszaki csapat számára az adott igényelt összeget az erőforrásköltségek alapján. Mutathat nekik egy jelentést, amely a *műszaki tervezés* címkével ellátott összes felhasznált erőforrást tartalmazza.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Költségek lefoglalása egyéni címkék használatával.
> * Költséghelyi visszacsatolási és költséghelyi elszámolási jelentések létrehozása.

## <a name="use-custom-tags-to-allocate-costs"></a>Költségek lefoglalása egyéni címkék használatával

A költséglefoglalás első lépéseként a hatókört kell definiálni egy költségmodell segítségével. A költségmodell nem módosítja a költségeket, csak leosztja azokat. A költségmodell létrehozásakor az adatokat költségentitás, fiók vagy előfizetés, valamint több címke alapján szegmentálja. A címkék általában lehetnek számlázási kódok, költséghelyek vagy csoportnevek. A címkék emellett abban is segítenek, hogy költséghelyi visszacsatolást és elszámolást végezzen a vállalat egyéb részeire vonatkozóan.

Egyéni költséglefoglalási modell létrehozásához válassza a **Költségek** &gt; **Költségkezelés** &gt; **Cost Allocation 360°** lehetőséget a jelentés menüjében.

![A Cost Allocation 360 kiválasztása](./media/tutorial-manage-costs/cost-allocation-360.png)

A **Cost Allocation 360** lapon kattintson a **Hozzáadás** elemre, majd adja meg a költségmodell nevét és leírását. Válassza ki az összes fiókot vagy válasszon egy egyénit. Egyéni fiókok kiválasztása esetén egyszerre több fiókot is választhat akár különböző felhőszolgáltatóktól is. Ezután kattintson a **Kategorizálás** gombra a költségadatok kategorizálásához használt felderített címkék kiválasztásához. Válassza ki a modellbe felvenni kívánt címkéket (kategóriákat). A következő példában az **Egység** címke van kiválasztva.

![Példa a költségmodellek kategorizálására](./media/tutorial-manage-costs/cost-model01.png)



A példában látható, hogy 14 444 dollár nincs kategorizálva (nem rendelkezik címkével).

Ezután kattintson a **Kategorizálatlan erőforrások** gombra, és válassza ki a szolgáltatásokat, amelyekhez le nem lefoglalt költségek tartoznak. Ezután definiáljon szabályokat a költségek lefoglalásához.

Például előfordulhat, hogy az Azure Storage költségeit szeretné egyenletesen elosztani az Azure-beli virtuális gépek (VM-ek) között. Ehhez válassza az **Azure/Storage** szolgáltatást, **A kategorizáltakkal arányosan**, majd az **Azure/VM** lehetőséget. Ezután kattintson a **Létrehozás** elemre.

![Példa az egyenlő leosztáshoz létrehozott költségmodell-lefoglalási szabályra](./media/tutorial-manage-costs/cost-model02.png)



Egy másik példa kedvéért tegyük fel, hogy az Azure-hálózattal kapcsolatos összes költséget a vállalat egy adott üzleti egységéhez szeretné rendelni. Ehhez válassza az **Azure/hálózat** szolgáltatást, majd a **Kifejezett leosztás** lehetőséget. Ezután állítsa a leosztás százalékos arányát 100-ra, és válassza ki az üzleti egységet – ez a **G&amp;A** a következő képen:

![Példa egy adott üzleti egységhez létrehozott költségmodell-lefoglalási szabályra](./media/tutorial-manage-costs/cost-model03.png)



Hozzon létre további lefoglalási szabályokat a többi kategorizálatlan erőforráshoz is.

Ha ki nem osztott Amazon Web Services (AWS) lefoglalt példányokkal rendelkezik, a **Lefoglalt példányok** lehetőséggel címkézett kategóriákhoz rendelheti azokat.

A költségek lefoglalásával kapcsolatos választásainak áttekintéséhez kattintson az **Összefoglalás** gombra. Ha mentené az adatokat, és később térne vissza a további szabályok létrehozásához, kattintson a **Mentése vázlatként** gombra. Választhatja a **Mentés és aktiválás** lehetőséget is az adatok mentéséhez. Ekkor a Cloudyn megkezdi a költséglefoglalási modell feldolgozását.

A költségmodellek listájában az új költségmodell **Feldolgozás alatt** állapotúként jelenik meg. Beletelhet némi időbe, mire a Cloudyn adatbázisa frissül a költségmodelljével. A feldolgozás végeztével a modell **Kész** állapotúra vált. Ezt követően a költségmodell adatai a Költségelemzés jelentésben tekinthetők meg a **Bővített szűrők** &gt; **Költségmodell** menüpont alatt.

### <a name="category-manager"></a>Kategóriakezelő

A Kategóriakezelő egy adattisztító eszköz, amellyel több kategória (címke) értékeinek egyesítésével új kategóriák hozhatóak létre. Ez egy egyszerű szabályalapú eszköz, amelyben egy kategóriát kiválasztva szabályokat hozhat létre a meglévő értékek egyesítésére. Például rendelkezhet az **R&amp;D** és a **dev** meglévő kategóriákkal, ahol mindkettő a fejlesztői csapatot jelöli.

A Cloudyn portálon kattintson a fogaskerék ikonra a jobb felső sarokban, és válassza a **Kategóriakezelő** elemet. Új kategória létrehozásához kattintson a plusz (**+**) jelre. Adja meg a kategória nevét, majd a **Kulcsok** mezőben adja meg a kategóriakulcsokat, amelyeket fel szeretne venni az új kategóriába.

A szabályok definiálásakor több értéket is hozzáadhat az OR (VAGY) feltétel használatával. Emellett néhány alapvető sztringműveletet is végrehajthat. Mindkét esetben kattintson a három pontra (**…**) a **Szabály** elem jobb oldalán.

Új szabály definiálásához a **Szabályok** területen hozza létre az új szabályt. Például adja meg a **dev** elemet a **Szabályok** alatt, majd az **R&amp;D** elemet a **Műveletek** alatt. Miután végzett, mentse az új kategóriát.

Az alábbi képen a **Work-Load** elnevezésű új kategóriához létrehozott szabályokat bemutató példa látható.

![Példa kategória](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Címkék forrásai és jelentések

A Cloudyn jelentésekben szereplő címkeadatok három helyről származhatnak:

- A felhőszolgáltatók erőforrásainak API-jai
- A felhőszolgáltatók számlázási API-jai
- A következő forrásokból származó manuálisan létrehozott címkék:
    - Cloudyn-entitáscímkék – a Cloudyn-entitásokra alkalmazott felhasználói metaadatok
    - Kategóriakezelő – a meglévő címkékre alkalmazott szabályok alapján új címkéket létrehozó adattisztító eszköz

A felhőszolgáltatói címkék a Cloudyn-költségjelentésekben való megjelenítéséhez egyéni költséglefoglalási modellt kell létrehoznia a Cost Allocation 360 használatával. Ehhez nyissa meg a **Költségek** > **Költségkezelés** > **Cost Allocation 360** eszközt, válassza ki a kívánt címkéket, majd adja meg a szabályokat a címkézetlen költségek kezeléséhez. Ezután hozzon létre egy új költségmodellt. Ezt követően a Költséglefoglalási elemzésben elérhető jelentésekben megtekintheti, szűrheti és rendezheti az Azure-erőforráscímkéket.

Az Azure-erőforráscímkék kizárólag a **Költséglefoglalási elemzés** jelentéseiben jelennek meg.

A felhőszolgáltatói számlázási címkék az összes költségjelentésben megjelennek.

A Cloudyn-entitáscímkék és a manuálisan létrehozott címkék az összes költségjelentésben megjelennek.


## <a name="create-showback-and-chargeback-reports"></a>Költséghelyi visszacsatolási és költséghelyi elszámolási jelentések létrehozása

Az egyes vállalatok egészen különböző módszereket alkalmazhatnak a költséghelyi visszacsatolások és a költséghelyi elszámolások készítéséhez. Azonban a Cloudyn portál bármely irányítópultja és jelentése használható mindkét célra. A vállalat bármely tagja számára adhat felhasználói hozzáférést, így igény szerint megtekinthetik az irányítópultokat és jelentéseket. A visszacsatolást az összes Költségelemzési jelentés támogatja, mivel megmutatják a felhasználók számára, hogy mely erőforrásokat használták fel. Emellett lehetővé teszik, hogy a felhasználók a saját vállalati egységükhöz tartozó költség- és használati adatokat részleteikben is megtekintsék.

A költséglefoglalási eredmények megtekintéséhez nyissa meg a Költségelemzési jelentést, és válassza ki a létrehozott költségmodellt. Ezután hozzon létre csoportosítást a költségmodellben kiválasztott egy vagy több címke alapján.

![Költségelemzési jelentés](./media/tutorial-manage-costs/cost-analysis.png)

Egyszerűen hozhat létre és menthet az adott csoportok által használt adott szolgáltatásokra összpontosító jelentéseket. Például elképzelhető, hogy az egyik részleg jelentős mértékben használ Azure-beli virtuális gépeket. Létrehozhat egy, az Azure-beli virtuális gépekre szűrt jelentést a használat mértékének és a költségeknek a megjelenítéséhez.

Amennyiben szeretné a csapatokat a pillanatnyi állapotokat mutató jelentésekkel ellátni, a jelentéseket exportálhatja PDF- vagy CSV-formátumba.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Költségek lefoglalása egyéni címkék használatával.
> * Költséghelyi visszacsatolási és költséghelyi elszámolási jelentések létrehozása.



A Cloudyn használatának első lépéseivel és a szolgáltatásaival kapcsolatos további információkért lépjen tovább a Cloudyn dokumentációjára.

> [!div class="nextstepaction"]
> [A Cloudyn dokumentációja](https://support.cloudyn.com/hc/)
