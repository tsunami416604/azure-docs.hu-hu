---
title: Adatok csoportosítása intervallumokba
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a csoportosítási adatok a Raktárhelyek modulba a számok csoportosításához vagy a folyamatos adatok eloszlásának módosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: b33aa3d2ab22fc986100d6141dd03d5547ef1862
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853826"
---
# <a name="group-data-into-bins"></a>Adatok csoportosítása intervallumokba

Ez a cikk azt ismerteti, hogyan használhatók a [csoportba](group-data-into-bins.md) foglalt adatok a Azure Machine learning Designerben (előzetes verzió), a számok csoportosításához vagy a folyamatos adatok eloszlásának módosításához.

A [csoportosítási adatként](group-data-into-bins.md) a dobozolási több beállítást is támogat. Testre szabhatja a Raktárhelyek szegélyének beállítását, valamint azt, hogy az értékek hogyan legyenek kiosztva a rekeszekben. Például a következőket teheti:  

+ Adja meg manuálisan az értékek egy sorozatát, amely raktárhely határként szolgál.  
+ Értékeket rendelhet a raktárhelyekhez a *quantiles*vagy a percentilis sorainak használatával.  
+ Az értékek egyenletes eloszlásának kényszerítése a raktárhelyekre.  

### <a name="more-about-binning-and-grouping"></a>További információ a dobozolási és a csoportosításról

A *dobozolási* vagy csoportosítási adatok (más néven *kvantálás*) fontos eszközei a gépi tanulás numerikus adatok előkészítésének, és a következő esetekben hasznosak:

+ A folytonos számok egy oszlopa túl sok egyedi értéket tartalmaz a modellhez, így automatikusan vagy manuálisan rendeli hozzá az értékeket a csoportokhoz, hogy kisebb különálló tartományokat hozzon létre.

+ Egy olyan számokból álló oszlop cseréje, amely meghatározott tartományokat jelképező kategorikus értékekkel rendelkezik.

    Előfordulhat például, hogy az értékeket egy Age oszlopban szeretné csoportosítani egyéni tartományok (például 1-15, 16-22, 23-30) megadásával, és így tovább a felhasználói demográfiai adatokhoz.

+ Az adatkészlet néhány szélsőséges értékkel rendelkezik, amelyek mindegyike a várt tartományon kívül esik, és ezek az értékek a betanított modellre is hatással vannak. A modell torzulásának enyhítése érdekében a quantiles metódus használatával átalakíthatja az adategységeket egységes eloszlásba.

    Ezzel a módszerrel a [csoportosított](group-data-into-bins.md) adattárolók modul meghatározza az ideális raktárhelyek helyét és a raktárhely szélességét, így biztosítva, hogy nagyjából azonos számú minta legyen az egyes raktárhelyekre. Ezt követően a kiválasztható normalizálás módszertől függően a rendszer a Raktárhelyek értékeit átalakítja a százalékokra, vagy hozzárendeli egy raktárhely számához.

### <a name="examples-of-binning"></a>Példák dobozolási

Az alábbi ábrán a dobozolási előtti és utáni numerikus értékek eloszlása látható a **quantiles** metódussal. Figyelje meg, hogy a bal oldali nyers adatmennyiséggel összehasonlítva az adatmennyiséget dobozolni, és átalakítottuk a normál méretre.  

' A folyamat futtatásának eredménye alapján a következő példa látható: https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net "

Mivel az adatok csoportosítása számos módon történik, az összes testreszabható, javasoljuk, hogy kísérletezzen különböző módszerekkel és értékekkel. 

## <a name="how-to-configure-group-data-into-bins"></a>A csoportosítási adatkészletek beállítása a raktárhelyekre

1. Adja hozzá a **csoport adatait a tárolók** modulhoz a folyamathoz a Designerben (előzetes verzió). Ez a modul az **adatátalakítás**kategóriában található.

2. Kösse össze a numerikus adatokat tartalmazó adatkészletet a bin értékkel.  A kvantálás csak numerikus értékeket tartalmazó oszlopokra alkalmazható. 

    Ha az adatkészlet nem numerikus oszlopokat tartalmaz, az oszlopok [kiválasztása az adatkészlet-](select-columns-in-dataset.md) modulban elem kiválasztásával válassza ki a használni kívánt oszlopok egy részhalmazát.

3. A dobozolási mód meghatározása. A dobozolási mód meghatározza a többi paramétert, ezért ügyeljen arra, hogy először válassza ki a **dobozolási mód** lehetőséget. A következő típusú dobozolási támogatottak:

    **Quantiles**: a quantile metódus értékeket rendel a raktárhelyekhez a percentilis sorai alapján. Az Quantiles más néven egyenlő magasságú dobozolási.

    **Egyenlő szélesség**: ezzel a beállítással a Raktárhelyek teljes számát kell megadnia. Az adatoszlop értékeit a rendszer a raktárhelyekre helyezi, így az egyes raktárhelyek megegyeznek a kezdő és a záró értékek közötti intervallummal. Ennek eredményeképpen előfordulhat, hogy egyes raktárhelyek több értékkel rendelkeznek, ha az adatok egy adott pont körül vannak összerakásban.

    **Egyéni élek**: megadhatja az egyes raktárhelyeket megkezdő értékeket. A peremhálózati érték mindig a raktárhely alsó határa.  Tegyük fel például, hogy az értékeket két raktárhelyre szeretné csoportosítani, egyet pedig nullánál nagyobb értékekkel, egyet pedig nullánál kisebb vagy azzal egyenlő értékkel. Ebben az esetben a Raktárhelyek széleinél a 0 értéket kell megírnia a **bin széleinek vesszővel tagolt listájában**. A modul kimenete 1 és 2 lesz, ami azt jelzi, hogy az egyes sorok értéke a bin index. Vegye figyelembe, hogy a vesszővel tagolt értékek listájának növekvő sorrendben kell lennie, például 1, 3, 5, 7.

4. **Raktárhelyek száma**: Ha a **Quantiles**és az **egyenlő szélességű** dobozolási módot használja, ezzel a beállítással adhatja meg, hogy hány raktárhelyet vagy *Quantiles*kíván létrehozni.

5. Az **oszlopok raktárhelyre**való kiválasztásához használja az oszlop-választót, és válassza ki azokat az oszlopokat, amelyeknek a raktárhelyét szeretné kijelölni. Az oszlopoknak numerikus adattípusnak kell lenniük.

    Ugyanazt a dobozolási szabályt alkalmazza a rendszer az összes kiválasztott oszlopra. Ezért ha egyes oszlopokat más módszer használatával kell [kiosztania, a csoportosított](group-data-into-bins.md) adathalmazok külön példányát kell használnia az egyes oszlopok oszlopaihoz.

    > [!WARNING]
    > Ha olyan oszlopot választ, amely nem engedélyezett típusú, futásidejű hiba jön létre. A modul hibát ad vissza, amint megkeresi a nem engedélyezett típusok bármelyik oszlopát. Ha hibaüzenetet kap, tekintse át az összes kijelölt oszlopot. A hiba nem sorolja fel az összes érvénytelen oszlopot.

6. **Kimeneti mód**esetén adja meg, hogyan szeretné kiadni a kvantálási értékeket.

      + **Hozzáfűzés**: egy új oszlopot hoz létre a dobozolni értékekkel, és hozzáfűzi a bemeneti táblához.

      + **Helyben**: az eredeti értékeket az adatkészlet új értékeivel helyettesíti.

      + **ResultOnly**: csak az eredmény oszlopokat adja vissza.

7. Ha a **Quantiles** dobozolási módot választja, akkor a **Quantile normalizálása** beállítással meghatározhatja, hogy az értékek hogyan legyenek normalizálva a Quantiles való rendezés előtt. Vegye figyelembe, hogy az értékek normalizálása átalakítja az értékeket, de a Raktárhelyek végső számát nem befolyásolja.

    A következő normalizáló típusok támogatottak:

    + **Százalék**: az értékek normalizálva vannak a tartományon belül [0100]

    + **PQuantile**: az értékek a tartományon belül normalizálva vannak [0, 1].

    + **QuantileIndex**: az értékek az [1, a Raktárhelyek száma] tartományon belül normalizálva vannak.

8. Ha a **Custom EDGeS (egyéni élek** ) lehetőséget választja, írja be a bin *élekként* használandó számok vesszővel tagolt listáját a **bin EDGeS szövegmező + vesszővel tagolt listájában** .  Az értékek megjelölik a raktárhelyeket felosztó pontot, így ha egy bin Edge értéket ad meg, a rendszer két raktárhelyet hoz létre; Ha két bin Edge-értéket ad meg, három raktárhely jön létre, és így tovább.

    Az értékeket a Raktárhelyek létrehozásához kell rendezni, a legalacsonyabbtól a legmagasabbig.

10. **Oszlopok címkézése kategorikusként**: válassza ezt a beállítást, ha azt szeretné jelezni, hogy a kvantálási oszlopokat kategorikus változóként kell kezelni.

11. A folyamat elküldése.

### <a name="results"></a>Results (Eredmények)

A [csoportosítási adatok a rekeszek](group-data-into-bins.md) modulba olyan adatkészletet ad vissza, amelyben az egyes elemek a megadott mód alapján dobozolni. 

Egy **dobozolási-transzformációt**is visszaad, amely egy olyan függvény, amely átadható az [Adatátalakítási](apply-transformation.md) modulnak az új minták az adatoknak az azonos dobozolási mód és paraméterek használatával történő kiosztásához.  

> [!TIP]
> Ne feledje, hogy ha a dobozolási használja a betanítási adataihoz, ugyanazt a dobozolási módszert kell használnia a teszteléshez és előrejelzéshez használt adataihoz. Ez magában foglalja a dobozolási metódust, a raktárhely helyét és a raktárhely szélességét. 
> 
> Annak biztosítása érdekében, hogy az adatokat mindig ugyanazzal a dobozolási módszerrel alakítsa át, javasoljuk, hogy mentse a hasznos adatátalakításokat, majd alkalmazza őket más adatkészletekbe az [átalakítás alkalmazása](apply-transformation.md) modul használatával.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
