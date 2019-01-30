---
title: Tartalombesoroló létrehozása – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre egy rendszerképet osztályozási modell a Custom Vision webhely használatára.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: 088ca29a2b10845730e986995256eed99caa7401
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210222"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>A Custom Vision besorolás létrehozása

Képek besorolása a Custom Vision Service használatához először egy osztályozó modellt kell létrehozni. Ebben az útmutatóban megismerheti, hogyan hozhat létre egy osztályozó a Custom Vision webhelyen keresztül lesz.

## <a name="prerequisites"></a>Előfeltételek

- Egy érvényes [Microsoft-fiók](https://account.microsoft.com/account) vagy egy Azure Active Directory (AAD) fiókot ("munkahelyi vagy iskolai fiók").

    > [!IMPORTANT] 
    > Az AAD-felhasználók esetében a bejelentkezés [Microsoft Felhőjét](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) jelenleg nem támogatott.
- Képkészlet használandó betaníthatja az. Lásd az alábbi tippek a rendszerképek kiválasztásáról.
- Igény szerint: Azure-előfizetéssel társított Microsoft-fiókjával vagy AAD-fiók. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt. Azure-előfizetés nélkül, csak fogja tudni hozzon létre két __próbaverzió korlátozott__ projektek.

## <a name="create-a-new-project"></a>Új projekt létrehozása

A böngészőben navigáljon a [Custom Vision weblap](https://customvision.ai) válassza __jelentkezzen be a__.

![A bejelentkezési lap képe](./media/browser-home.png)

Ha rendelkezik Azure-fiókkal, kéri a Custom Vision Service betanítási és Predikciós erőforrások létrehozása a [az Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) projekt létrehozása során.

1. Az első-projekt létrehozásához válassza **új projekt**. A **új projekt létrehozása** párbeszédpanel fog megjelenni.

    ![Az új projekt párbeszédpanel mezőinek nevét, leírását és tartományok rendelkezik.](./media/getting-started-build-a-classifier/new-project.png)

1. Adjon meg egy nevet és leírást a projekthez. Ezután válassza ki egy erőforráscsoportot. A bejelentkezett fiókban társítva az Azure-fiók, ha az erőforráscsoport legördülő megjeleníti az összes az Azure-erőforráscsoportok, amelyek tartalmazzák a Custom Vision Service erőforrás. Mindkét esetben választhatja __próbaverzió korlátozott__ lehetőséget a legördülő listából.

1. Válassza ki __besorolási__ alatt __projekttípusok__. Ezután a __besorolási típust__, válasszon **Multilabel** vagy **osztályú**, attól függően, a használati eset. Multilabel besorolás a címkékkel tetszőleges számú közben többosztályos osztályozási (minden egyes kép küld be lesznek rendezve az a legvalószínűbb címke) egyetlen kategóriákba rendezi a lemezképek kép (nulla vagy több), vonatkozik. A besorolás típusának később módosíthatja, ha szeretné tudni fogja.

1. Ezután válasszon az elérhető tartományok közül. Minden egyes tartományhoz optimalizálja az osztályozó által igénybe vett képek, bizonyos típusú, az alábbi táblázatban leírtak szerint. A tartomány később módosíthatja, ha szeretné tudni fogja.

    |Domain|Cél|
    |---|---|
    |__Általános__| Optimalizált széles kép fájlosztályozási feladatokhoz. Ha más tartományok sem megfelelő, vagy Ön nem tudja biztosan, melyik tartománya, válassza ki, válassza ki a általános tartományt. |
    |__Élelmiszer__|Optimalizált csészék fényképeket, látnák őket egy étterem menüben. Ha szeretné besorolni különálló vagy zöldségek fényképek, használja az élelmiszer-tartomány.|
    |__Arcrész__|Könnyen felismerhető névre arcrész természetes és mesterséges optimalizálva. Ez a tartomány akkor működik a legjobban, ha a környezet a fénykép fókuszjelzőknek jól láthatóknak. Ez a tartomány működik, akkor is, ha a környezet némileg fedhetik személyek takarja.|
    |__Kereskedelmi__|Optimalizált rendszerképeket, amelyeket egy vásárlási katalógus vagy a vásárlási webhelyén találhatók. Ha azt szeretné, hogy nagy pontosságú Írisz ruha pants és ing között, használja ezt a tartományt.|
    |__Felnőtt tartalom__|Optimalizált pontosabb meghatározásához a felnőtt tartalom, és nem felnőtt tartalom. Például ha azt szeretné, lemezképek emberek fürdés csomagok blokkolására, ebben a tartományban lehetővé teszi egyéni tartalombesoroló létrehozása valósítható meg.|
    |__Kompakt tartományok__| A korlátozások a mobileszközökön a valós idejű besorolás optimalizálva. A modellek kompakt tartományok által létrehozott helyi futtatásához exportálhatók.|
    
1. Végül válassza __Create project__.

## <a name="choose-training-images"></a>Betanító kép kiválasztása

Legalább javasoljuk, hogy a kezdeti gyakorlókészlethez címke legalább 30 képenként használ. Emellett érdemes teszteli a modellt, ha be van tanítva néhány további képek összegyűjtése.

Annak érdekében, hogy hatékonyan modellje betanításához, használja a képek vizuális különböző. Válassza ki, amely rendszerképek régiónként eltérő:
* kamera szög
* Megvilágítási
* Háttér
* a Vizualizáció stílusa
* személy vagy csoportosított subject(s)
* méret
* type

Ezenkívül győződjön meg arról, mind a betanító kép a következő feltételeknek:
* .bmp, .jpg vagy .png formátumú
* nem lehet nagyobb 6MB-nál (4MB előrejelzési lemezképek esetén)
* a legrövidebb peremhálózaton; legalább 256 képpont Ez rövidebb képeket automatikusan szerint lesz skálázva a Custom Vision Service

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ebben a szakaszban töltse fel, és manuálisan címkézhet meg képeket érdekében az osztályozó által igénybe vett betanításához. 

1. Kép hozzáadásához kattintson a __lemezképeket__ gombra, majd válassza ki __helyi fájlok tallózása__. Válassza ki __nyílt__ szeretne váltani a címkézést. Címke választását választotta tölthet fel, tölthet fel képeket a kívánt címkéket megfelelően különálló csoportokba révén egyszerűbbé vált a képek a teljes csoport lesz alkalmazható. Egyéni képek címkéinek azok feltöltése után is módosíthatja.

    ![A Hozzáadás lemezképek vezérlőelem bal felső és alsó központról gombként jelenik meg.](./media/getting-started-build-a-classifier/add-images01.png)


1. Címke létrehozásához, írja be a szöveget a __saját címkéket__ mezőben, majd nyomja le az Enter billentyűt. Ha már létezik a címkét, meg fog jelenni a legördülő menüben. Multilabel projektben a képeket is hozzáadhat több címke, de a multiclass projektben felvehet csak egy. Töltse fel a képek, használja a __[száma] fájlok feltöltése__ gombra. 

    ![A címke és a feltöltési lap képe](./media/getting-started-build-a-classifier/add-images03.png)

1. Válassza ki __kész__ rendelkezik a képek feltöltése után.

    ![A folyamatjelző mutatja a minden feladat befejeződött.](./media/getting-started-build-a-classifier/add-images04.png)

Tölthet fel képeket egy másik készletét, visszatérek a tetejére, az ebben a szakaszban, és ismételje meg a lépéseket. A projekt valamely pontján, előfordulhat, hogy hozzá kell _minták negatív_ annak érdekében, pontosabb az osztályozó által igénybe vett. Negatív minták azokat, amelyek a nem megfelelő más címkék. Ezek a lemezképek feltöltésekor a alkalmazni a speciális **negatív** címke működnek.

## <a name="train-the-classifier"></a>Az osztályozó betanítása

A besorolás betanítására, válassza ki a **betanításához** gombra. Az osztályozó által igénybe vett összes aktuális rendszerképet használ, amely azonosítja a vizuális minőségű minden címke egy modell létrehozásához.

![A felső train gombot, a weblap fejléc eszköztárának jobb](./media/getting-started-build-a-classifier/train01.png)

A betanítási folyamat kell csak igénybe vehet néhány percet. Ebben az időszakban a betanítási folyamat megjelenik a **teljesítmény** fülre.

![A böngésző ablakához a fő szakasz egy képzési párbeszédpanel](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Az osztályozó által igénybe vett kiértékelése

Képzési befejezését követően a modell teljesítményét becsült és jelenik meg. A Custom Vision Service használja a képek alapján számítja ki a pontosság és a már ismert, a folyamat használatával képzési nevű elküldött [k lépésből keresztellenőrzési](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Pontosság és a visszahívás két különböző mérések hatékonyságát a besorolás:

- **Pontosság** azt jelzi, hogy a százalékértékében mutatkozó azonosított, amelyek korábban megfelelő besorolásokat. Például ha a modell azonosított 100 lemezképeit a kutyájával lenni, és 99 azokat ténylegesen kutyájával lenni, is, majd a pontosság lenne 99 %-os.
- **Idézze** azt jelzi, hogy a százalékértékében mutatkozó tényleges besorolásoknak sikerült helyesen azonosítani. Például ha ténylegesen 100 kép alma volt, és a modell azonosított almák, a 80-as, a visszaírási lenne 80 %.

![A képzési eredmények megjelenítése a teljes precíziós és visszaírási, és a pontosság, és az egyes címkét az osztályozó által igénybe vett visszaírásához.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Valószínűségi küszöbértéke

Megjegyzés: a **valószínűségi küszöbértéke** csúszka bal oldali ablaktábláján a **teljesítmény** fülre. Ez a venni megfelelő számítási pontosság és a visszahívás egy előre jelzett valószínűségi küszöbértéke.

Előrejelzés-hívást egy nagy valószínűséggel küszöbértéket értelmezése általában nagy pontosságú rovására visszaírási az eredményeket (a található besorolások helyesek, de számos nem találhatók); egy alacsony valószínűségi küszöbértéke does ennek az ellenkezője (a tényleges besorolásokat a legtöbb találhatók, de nincsenek téves, hogy a csoporton belül). Ezt szem a valószínűségi küszöbértéke a projekt egyedi igényeinek megfelelően kell beállítania. Később az ügyfél oldalán használjon valószínűségi küszöbértéke ugyanazt az értéket egy szűrőt a modellből származó előrejelzési eredményeket fogadásakor.

## <a name="manage-training-iterations"></a>Képzési ismétléseinek kezelése

Minden alkalommal, amikor Ön betaníthatja az, hozzon létre egy új _iteráció_ a saját frissített teljesítmény-mérőszámon. Az ismétlések összes, a bal oldali panelen megtekintheti az **teljesítmény** fülre. Ha kiválaszt egy, lehetősége van, így a _iteráció alapértelmezett_ kattintva a **alapértelmezett** gombra az oldal tetején. A _iteráció alapértelmezett_ pedig a modellt, amely alapértelmezés szerint lesz használható, ha az előrejelzési API-k lekérdezése (az alkalmazásokból, például). Frissíteni elutasításakor a _iteráció alapértelmezett_, továbbra is a modell betanítását anélkül, hogy befolyásolná az alkalmazás aktuális működésének; majd, ha elégedett a továbbfejlesztett modellt, módosíthatja az alapértelmezett.

A bal oldali panelen is talál a **törlése** gombra, amely egy iterációját törlése, ha az elavult használhatja. Ha töröl egy iterációját, akkor törölje az összes egyedi társított, lemezképet.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban útmutatóból megtudhatta, hogyan hozhat létre, és a egy kép osztályozási modell a Custom Vision webhelyen betanításához. Ezután kérdezze le a további tájékoztatást a modell javításának az iteratív folyamat.

[Modell tesztelése és újratanítása](test-your-model.md)

