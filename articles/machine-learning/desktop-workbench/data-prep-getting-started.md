---
title: Adatok előkészítése az Azure Machine Learning bevezetés használatába |} Microsoft Docs
description: Ez az első lépésekről szóló útmutatót az adatok előkészítő szakaszra AML munkaterület
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: b0fbb0af433cfad6693b022d7a00373dc39533aa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="getting-started-with-data-preparation"></a>Adatok előkészítése az első lépések

Üdvözli az adatok előkészítése az első lépések. 

Adatok előkészítése hatékonyan felfedezése, ismertetése és a problémák kijavítása adatok eszközöket biztosít. Lehetővé teszi több formában az adatokat, és adott adatok átalakítására jobban megfelel az alsóbb rétegbeli használati kitisztítanak adatokká.

Adatok előkészítése az Azure Machine Learning-munkaterület élmény részeként telepíthető.  Adatok előkészítése helyileg használja, vagy célfürt telepítése, és a felhő a runtime vagy a végrehajtási környezet.

A tervezési idő futásidejű bővítési Python használ, és különböző Python szalagtárak például Pandas függ. A többi összetevővel Azure ML-munkaterület van Python telepítése nem szükséges, az telepíti a rendszer. Azonban ha további könyvtárak telepítenie kell ezeket a kódtárakat kell lennie az Azure ML munkaterület Python könyvtárban telepített ne a szokásos Python könyvtárat. További részletes információt csomagok található [Itt](data-prep-python-extensibility-overview.md).

A projekt szükség erre a projektre a létrehozása után adatok Prep használatba vétele előtt, előfordulhat, hogy készítse elő az adatokat. 

Az ikon kiválasztásával keresse meg a projekt adatszakasza ![forrás ikon](media/data-prep-getting-started/data-source-icon.png) a képernyő bal oldalon található.  Kattintson a "+" újra **adatforrás hozzáadása**. Az adatforrás varázsló kell elindítani, és hozzáadja a **adatforrás** a varázsló befejezése után a projekt (.dsource) fájlt. Alapértelmezés szerint az adatok ábrázolása a rács. A rács fent akkor is metrikák nézet kiválasztásához. A metrikák nézetben statisztikák jelennek meg.  Miután megtekintette a statisztikák, kattintson a **Prepare** a képernyő tetején. [További információ az adatforrás varázsló](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Adatok előkészítése építőelemei ##
### <a name="the-package"></a>A csomag ###

A csomag a munka elsődleges tárolójának. A csomag, amely menti, és a lemezről betöltött összetevő. Az ügyfél belül működő, miközben a csomag nincs folyamatosan automatikusan mentett a háttérben. 

A kimeneti/eredményeket egy csomag is vizsgálják, Python vagy Jupyter Notebook segítségével.

A csomag több futtatókörnyezetek, beleértve a helyi Python, Spark (beleértve a Docker) és a HDInsight keresztül hajtható végre.

A csomag tartalmaz egy vagy több Dataflows, amely azokat a lépéseket és átalakítások adatokra.

A csomag egy olyan másik csomaghoz használhatja egy adatforrás (néven adatfolyam referencia).

### <a name="the-dataflow"></a>Az adatfolyamban ###
Egy adatfolyam van egy, és amely vannak elrendezve révén azokat a lépéseket és választható célok választható átalakítja. Egy lépésben kattintson újra hajtja végre a forrás és a átalakítások előtt, beleértve a kiválasztott lépést is.  A lépés az átalakított adatok a rácson belül jelenik meg. Lépéseket felvehető, áthelyezni, és egy adatfolyam a lépés lista keresztül belül törlődnek.

A lépés lista az ügyfél jobb oldalán megnyitották, és arra, hogy nagyobb területet a képernyő bezárása.

Egyszerre több Dataflows létezhet a felhasználói felületen, minden egyes Adatfolyamblokk egy jelenik meg a lap a felhasználói felületen.

### <a name="the-source"></a>A forrás
A forrás, ahol az adatok származik, és a formátum szerepel. Előkészítő adatcsomag mindig adatforrásokat egy másik adatok Flow(Data Source) annak adatait. Ez a hivatkozás adatfolyam, amely tartalmazza az adatokat is. Minden forrás azt úgy kell konfigurálni, hogy egy másik felhasználói felület rendelkezik. A forrás "téglalap alakú" / táblázatos nézetben, az adatok eredményez. Ha a forrásadatok eredetileg "egyenetlen jobbra", majd a struktúra van normalizálni kell a "téglalap alakú." [A támogatott adatforrások aktuális listáját jeleníti meg, 2. függelékben](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Az átalakítás ###
Átalakítások adatokat egy adott formátumban, néhány műveletet végezni az adatok (például az adattípus módosítása), és ezután eredményezett az adatokat az új formátumban. Minden egyes átalakítás a saját felhasználói felület és a behavior(s) rendelkezik. Adatok előkészítése funkcióinak core több átalakítások együtt láncolás lépéseket az adatfolyamban keresztül. [A támogatott átalakítások aktuális listáját jeleníti meg, 3. függelék](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>A megtekintő ###

Ellenőrök képi megjelenítések az adatok és az adatok megértése rendelkezésre állnak.  Az adatok és az adatok ismertetése minőségi problémákkal segítségével eldöntheti, milyen műveleteket (átalakítások) kell végezni. Néhány ellenőrök támogatja a műveleteket, amelyek átalakításokat. Például az értékek száma Inspector teszi kiválaszthat egy értéket, és alkalmazhatja a szűrő tartalmazza ezt az értéket, vagy zárja ki ezt az értéket. Ellenőrök az átalakítások is biztosít a környezetben. Például egy vagy több oszlop kiválasztása módosítja, a lehetséges átalakítások alkalmazható.

Egy oszlop lehet több ellenőrök bármikor idő (például oszlop statisztikai adatainak és hisztogram). Is lehet egy Inspector példányai között több oszlopba. Összes numerikus oszlopot például rendelkezhetnek a hisztogram egyszerre.

Ellenőrök jelennek meg a profilkészítési is a képernyő alján.  Teljes méret ellenőrök nagyobb a fő tartalom területen láthatók. Érdemes a program az adatrács, mint az alapértelmezett felügyelő. Bármely Inspector bővíthetők a fő tartalom területre. A fő tartalom területen belül ellenőrök méretűvé a profilkészítési is. Testre egy Inspector belül a Inspector ceruza ikonra kattint. Újrarendelési ellenőrök belül is használatával áthúzása.

Néhány ellenőrök támogatja a "Halo" módot. Ebben a módban alkalmazása az utolsó átalakítás előtti érték vagy állapotát tartalmazza. A régi érték jelenik meg a szürke és az aktuális értékét az előtérben, és bemutatja az átalakítás hatását. [Függelék: 4 támogatott ellenőrök aktuális listáját jeleníti meg](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>A cél
 Egy célja ahol Ön írási/export az adatok rendelkezésre állnak a adatfolyamban. Egy adott Adatfolyamblokk több helyre is rendelkezhetnek. Minden cél azt úgy kell konfigurálni, hogy egy másik felhasználói felület rendelkezik. A cél adatforrástól "téglalap alakú" / táblázatos formátumban, és néhány helyre az adott formátumban írja azt. [5. függelék által támogatott célok aktuális listáját jeleníti meg](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Adatok előkészítése használatával ###
Adatok előkészítése azt feltételezi, hogy egy alapszintű öt lépés módszertana/megközelítése adatok előkészítése.

#### <a name="step-1-ingestion"></a>1. lépés: adatfeldolgozást ####
Adatok előkészítése az adatok importálása használatával a **adatforrás hozzáadása** lehetősége a projekt nézetben.  Az adatok minden kezdeti adatfeldolgozást az adatforrás varázsló segítségével kezel.

#### <a name="step-2-understandprofile-the-data"></a>2. lépés: Megértéséhez/profil értékét a adatok ####

Először tekintse meg a minőségi adatsáv minden oszlop tetején. Zöld jelenti. a sor. Szürke jelöli soraiban egy hiányzó érték null, stb. Vörös állapot azt jelzi, hogy hiba értékeket. A sáv elemleírást pontos mennyiségű sort beolvasni minden a három gyűjtők mutasson. A minőségi adatsáv használatát a Logaritmikus skála ezért mindig ellenőrizze a tényleges számokat betekintést nyerhet abba nyers, a hiányzó adatok mennyiségét.

![oszlopok](media/data-prep-getting-started/columns.png)

Ezután használja a más ellenőrök plusz a rács jobb megértése érdekében adatok jellemzőit.  Indítsa el a további elemzés szükséges adatok előkészítésével kapcsolatos feltételezéseket kidolgozásában. A legtöbb ellenőrök működik a csak egy oszlop vagy egy kis számú oszlopot.  

Valószínű, hogy több oszlopra több ellenőrök szükséges adatokat. A profilkészítési jól különböző ellenőrök görgetésével. A well belül is áthelyezheti ellenőrök a lista vezetője ahhoz, hogy lássák az azonnal látható területen.

![ellenőrök](media/data-prep-getting-started/inspectors.PNG)

Különböző ellenőrök folyamatos vs kategorikus változók/oszlopok célokat szolgálnak. A Inspector menü lehetővé teszi, és letiltja a beállítások van oszlopok/változók típusától függően.

Sok oszlop rendelkező nagy adatkészletekkel dolgozik, a gyakorlati módszert is, amely részhalmazának használata esetén ajánlott. Ezt a módszert használja (például 5-10) oszlopok kis számú összpontosító. előkészítése őket, majd használatához, áttekintve a többi oszlop tartalmazza. A rács inspector támogatja a függőleges particionálásból, oszlopok és, ha több mint 300 oszlopok akkor szükséges "lap" révén azokat.
 

#### <a name="step-3-transform-the-data"></a>3. lépés: Az adatok átalakítása ####
Átalakítások változtatja meg az adatokat, és lehetővé teszik az adatok az aktuális működő feltevése végrehajtása. A jobb oldalon a lépés listában lépéseket átalakítások jelennek meg. Akkor lehet "idő utazhatnak" végig a lépés listát a lépés lista bármely tetszőleges pontjára kattintva.

A bal oldalra, egy adott lépés zöld ikonnal azt jelzi, hogy futás az adatokat az átalakítás végrehajtásának tükrözi. A függőleges vonal lépés balra a ellenőrök adatok aktuális állapotát jelzi.

![lépések](media/data-prep-getting-started/steps.PNG)

Próbálja meg a kis gyakran módosítja az adatokat, és a feltevése fejlődésének (4. lépés) érvényesítéséhez minden egyes módosítása után.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>4. lépés: Ellenőrizze az átalakítás az hatását. 
Döntse el, ha megfelelő volt-e a alapul. Ha helyes, fejlesztése a következő alapul, és ismételje meg a 2-3 lépést az újjal. Helytelen, ha az utolsó átalakítást visszavonása és fejlesztése egy új alapul, majd ismételje meg a lépéseket 2-3.

Az elsődleges határozza meg, ha az átalakítás a megfelelő hatást módja használni ellenőrök. Használja a meglévő. A hatás engedélyezve van, vagy indítsa el az adatok megtekintéséhez több ellenőrök a Halo használata ellenőrök pontok megadott időben.

![Halo inspector](media/data-prep-getting-started/halo1.PNG) ![Halo inspector](media/data-prep-getting-started/halo2.PNG)

Átalakítás visszavonásához, nyissa meg a lépéseket lista a felhasználói felület jobb oldalán. (A lépéseket lista panel esetleg újra ki kell előtti. Az megnyitásához kattintson a dupla sávnyíl mutató bal oldali). A panelen válassza ki, hogy végre lett hajtva, szeretné visszavonni a transzformáció. Válassza ki a legördülő listán a felhasználói felület blokk a jobb oldalon. Válassza ki vagy **szerkesztése** módosításokat vagy **törlése** az átalakítás eltávolítása a lépéseket és az adatfolyamban.

#### <a name="step-5-output"></a>5. lépés: kimeneti 
Ha befejezte az adatok előkészítése, egy kimeneti írhat az adatfolyamban. Egy adatfolyam sok kimenetek is rendelkezhetnek. A átalakítások menüben kiválaszthatja, amely kimeneti azt szeretné, hogy az adatkészlet írható a. Igény szerint kiválaszthatja a kimeneti cél. 

## <a name="list-of-appendices"></a>Mellékletek listája 
[A támogatott adatforrások 2 - függelék](data-prep-appendix2-supported-data-sources.md)  
[Függelék: 3 - támogatott átalakítja](data-prep-appendix3-supported-transforms.md)  
[A függelék – 4 támogatott ellenőrök](data-prep-appendix4-supported-inspectors.md)  
[Függelék 5 - támogatott célok](data-prep-appendix5-supported-destinations.md)  
[6 - minta szűrőkifejezéseket pythonban függelék](data-prep-appendix6-sample-filter-expressions-python.md)  
[Függelék 7 - minta átalakítási Python Adatfolyamblokk kifejezések](data-prep-appendix7-sample-transform-data-flow-python.md)  
[8 - minta adatforrások Python függelék](data-prep-appendix8-sample-source-connections-python.md)  
[9 - minta cél kapcsolatok a Python függelék](data-prep-appendix9-sample-destination-connections-python.md)  
[Függelék: 10 - minta oszlop átalakítja a Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Lásd még:

[Speciális előkészítése oktatóanyag](tutorial-bikeshare-dataprep.md)