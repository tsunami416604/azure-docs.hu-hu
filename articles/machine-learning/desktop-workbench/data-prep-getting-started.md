---
title: Ismerkedés az Azure Machine Learning adat-előkészítési |} A Microsoft Docs
description: Ez az AML-munkaterület az adatok előkészítési szakaszt a kezdő lépések útmutató
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: ffff3d274e093e9ce4aa32dee7b033bd1ed288bc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645995"
---
# <a name="getting-started-with-data-preparation"></a>Az adat-előkészítés első lépések

Üdvözli az adatok előkészítésének első lépések útmutatója. 

Adat-előkészítési hatékonyan feltárása, ismertetése és a problémák kijavítása adatok eszközöket biztosít. Lehetővé teszi, hogy sok képernyőn felhasználását, és módosíthatja az adatokat a tisztított fejlesztéseink alárendelt használati adatokat nyerhet ki.

Adat-előkészítés az Azure Machine Learning Workbench élmény részeként települ.  Adat-előkészítési helyileg használja vagy célfürt telepítése, és a futtatókörnyezet vagy végrehajtási környezet felhőbeli.

A tervezési időt modul Python használja, a bővítést, és attól függ, például a Pandas különböző Python-kódtárakat. Ahogy a többi összetevővel az Azure ML Workbench nem Python telepítése nem szükséges, telepíti a rendszer az Ön számára. Azonban ha további kódtárak pedig telepíteni kell ezek a kódtárak kell lennie az Azure ML Workbench Python könyvtár telepítve van a nem a szokásos Python könyvtárra. Csomagok telepítésével kapcsolatos további információk találhatók [Itt](data-prep-python-extensibility-overview.md).

A projekt megadása kötelező Adatelőkészítés, hogy a projekt létrehozása után használata előtt, előfordulhat, hogy az adatelőkészítés. 

Az adatok ikonra kattintva keresse meg a projekt adatok szakaszát ![data source ikon](media/data-prep-getting-started/data-source-icon.png) a képernyő a bal oldalon.  Kattintson a "+" újra **adatforrás hozzáadása**. Az adatforrás varázsló futtatnia kell, és hozzáad egy **adatforrás** a varázsló befejezése után a projekt (.dsource) fájlt. Alapértelmezés szerint a nézet az adatok a rács. A rács fölötti akkor is mérőszám nézetben kijelölendő. Metrikák megtekintése, az összefoglaló statisztikák jelennek meg.  Az összefoglaló statisztikák áttekintése után kattintson a **előkészítése** a képernyő tetején. [További információ az adatforrás varázsló](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Adat-előkészítési építőelemei ##
### <a name="the-package"></a>A csomag ###

A csomag nincs a munka elsődleges tárolója. A csomag nincs az összetevő, amely menti, és betölti a lemezről. Az ügyfél belül működő, miközben a csomag nincs folyamatosan megduplázódhat a háttérben. 

A csomag kimeneti/eredményeit a Pythonban vagy egy Jupyter Notebookot vizsgálhatók meg.

A csomag helyi Python, a Spark (a Dockerrel együtt) és a HDInsight többek között több futtatókörnyezetben is végrehajthatók.

A csomag tartalmaz egy vagy több adatfolyamok, amelyek a lépéseket és az adatokra alkalmazott átalakítások.

A csomag egy másik csomagot használhatja egy adatforrás (néven adatfolyam referencia).

### <a name="the-dataflow"></a>Az adatfolyam ###
Egy adatfolyam forrás tartozik, és amely vannak elrendezve ismerteti azokat a lépéseket és a választható destinations keresztül választható alakítja át. Valamely lépésére kattintva újra hajtja végre a forrás és a átalakítások előtt a kijelölt lépés is beleértve.  Ezt a lépést az átalakított adatokat a rács belül jelenik meg. Lépéseket hozzáadva, áthelyezése és egy adatfolyam végig a lépés listáján belül törölt.

A lépés lista jobb oldalán, az ügyfél megnyitható és lezárt, így több képernyős hely.

Egyszerre több adatfolyamot létezhet a felhasználói felületen, minden egyes adatfolyam jelenik meg a lapon, a felhasználói felületen.

### <a name="the-source"></a>A forrás
Egy adatforrás, ahol származnak az adatok a formátumban van. Adat-előkészítő csomag mindig forrásokból származó adatok egy másik Flow(Data Source) adatokat. Ez a hivatkozás adatfolyam információt tartalmazó. Minden adatforrás rendelkezik egy másik felhasználói élményt, hogy kell konfigurálni. A forrás hoz létre az adatok egy "téglalap alakú" / táblázatos nézetben. Ha a forrásadatok eredetileg "hézagos jobb", majd a struktúra van normalizálva kell a "téglalap alakú." [2. függelék: a támogatott források aktuális listáját jeleníti meg](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Az átalakítás ###
Átalakítások felhasználását az adott formátumban, az adatokat (például az adattípus módosítása) a valamilyen műveletet végrehajtani, és majd a az új formátum az adatok előállításához. Minden átalakítási saját felhasználói felület és a behavior(s) rendelkezik. Adat-előkészítés a funkciókat biztosítja az alapvető több átalakítások együtt láncolási lépések végrehajtásával az adatfolyamban. [3. függelék támogatott átalakítások aktuális listáját jeleníti meg](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>A vizsgáló ###

Ellenőrök Vizualizációk az adatok és az adatok megértése rendelkezésre állnak.  Az adatok és-adatok minőségi problémák segítségével eldöntheti, milyen műveleteket végezzen (átalakító) kell tenni. Néhány vizsgálók átalakítások generáló műveleteket támogatja. Például az értékek száma vizsgáló lehetővé teszi, hogy válasszon ki egy értéket, és alkalmazhatja a szűrő tartalmazza ezt az értéket, vagy zárja ki ezt az értéket. Vizsgálók átalakítások környezetben is megadhat. Például egy vagy több oszlop kiválasztása módosítja a lehetséges átalakítások alkalmazható.

Egy oszlop lehet több vizsgálók bármikor idő (például a Oszlopstatisztika és a egy hisztogram). Is lehet egy vizsgáló példányai között több oszlopot. Az összes numerikus oszlopok például hisztogramok egyszerre lehet.

Ellenőrök jelennek meg a profilkészítés is a képernyő alján.  Maximalizálja a vizsgálók nagyobb a fő tartalom területen láthatók. Úgy az adatrácsban, mint az alapértelmezett vizsgáló. Bármely vizsgáló a fő tartalomterület bővíthet. Fő tartalom területen belül vizsgálók minimalizálása érdekében a profilkészítés is. Testre szabhatja egy vizsgáló belül a vizsgáló a ceruza ikonra kattintva. Átrendezési vizsgálók belül a jól áthúzása.

Néhány vizsgálók egy "Halo" módot támogatja. Ebben a módban az értéket vagy az állapot mutatja, az utolsó átalakítás alkalmazása előtti. A régi értéket szürke jelenik meg a jelenlegi érték az előtérben történik és, és megjeleníti az egy-egy átalakítási hatását. [4. függelék listáját jeleníti meg aktuális, támogatott vizsgálók](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>A cél
 A cél az ahol, írási/Exportálás az adatokat, miután azt előkészítette egy adatfolyam. Egy adott adatfolyam rendelkezhet több célhelyre. Minden cél van egy másik felhasználói élményt, hogy kell konfigurálni. A cél "téglalap alakú" / táblázatos formátumú adatokat használ fel, és a egy helyre az adott formátumban írja azokat. [5. függelék által támogatott célok aktuális listáját jeleníti meg](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Adat-előkészítési használatával ###
Adat-előkészítési feltételezi, hogy egy alapszintű öt módszertan/megközelítését adat-előkészítési.

#### <a name="step-1-ingestion"></a>1. lépés: feldolgozó ####
Adatok importálása az Adatelőkészítés-használatával a **adatforrás hozzáadása** lehetősége a projekt nézetben.  Az adatok az összes kezdeti betöltési kezeli az adatforrás varázsló.

#### <a name="step-2-understandprofile-the-data"></a>2. lépés: Ismertetése/profilt az adatok ####

Első lépésként tekintse meg a sávjában minden oszlop tetején. Zöld értékeket tartalmazó sorok jelöli. Szürke egy hiányzó érték null, stb-sorok jelöli. Piros szín jelzi a hiba értékeket. A sáv elemleírást pontos mennyiségű sort beolvasni az egyes a három gyűjtők fölé. A sávjában használja a Logaritmikus skála ezért mindig ellenőrizze a tényleges számokat a nyers betekintést nyerhet abba, a hiányzó adatok mennyisége.

![Oszlopok](media/data-prep-getting-started/columns.png)

Ezután használja a többi vizsgálók plusz a rács adatjellemzők segít jobban megérteni.  Indítsa el a vonatkozó további elemzés szükséges adat-előkészítési feltételezéseket kidolgozásában. A legtöbb vizsgálók működik egy oszlop vagy egy kis számú oszlopot.  

Valószínű, hogy a több vizsgálók több oszlopra van szükség az adatok megismeréséhez. A profilkészítés jól különböző vizsgálók görgetéssel áttekintheti. A jól belül is áthelyezheti vizsgálók a lista a fő a azonnal látható területen láthatók.

![vizsgálók](media/data-prep-getting-started/inspectors.PNG)

A folyamatos vs kategorikus változók/oszlopok különböző vizsgálók biztosított. A vizsgáló menü engedélyezése és letiltása lehetőség van oszlopok/változók típusától függően.

Sok oszlop rendelkező nagy adatkészletekkel dolgozik, egy pragmatikus módszert is, amely részhalmazának használata esetén ajánlott. Ez a megközelítés oszlopokat (Ha például 5-10) kis számú kellene összpontosítaniuk. előkészítése őket, majd működik a többi oszlopot tartalmaz. A rács vizsgáló támogatja a vertikális particionálás az oszlopokat, ezért ha több mint 300 oszlopok akkor kell "lap" ezeket.
 

#### <a name="step-3-transform-the-data"></a>3. lépés: Az adatok átalakítása ####
Átalakítások módosítja az adatokat, és lehetővé teszi az adatok az aktuális működő elmélet támogatásához végrehajtását. Átalakítások lépéseket a lépés lista jobb oldalán jelennek meg. Lehetőség a "utazási idő" a lépés lista keresztül a lépés lista bármely tetszőleges pontjára kattintva.

Egy adott lépés bal oldalán zöld ikonnal jelzi, hogy futott, és a megjelennek-e az átalakítás végrehajtása. Egy függőleges oszlopra, hogy a bal oldalon, a lépés a vizsgálók az adatok aktuális állapotát jelzi.

![lépések](media/data-prep-getting-started/steps.PNG)

Próbálja meg a kis gyakran módosítja az adatokat, és (4. lépés) az elmélet haladásával minden módosítás után érvényesíteni.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>4. lépés: Ellenőrizze az átalakítást a hatását. 
Döntse el, ha a feltételezést megfelelő volt-e. Helyes, ha a következő elmélet fejleszthet, és ismételje meg a 2-3 lépést az újat. Helytelen, ha ezután az utolsó átalakítást visszavonása és fejlesztése egy új változat, és ismételje meg a 2-3 lépést.

Az elsődleges módja határozza meg, ha az átalakítás a megfelelő hatást, hogy a vizsgálók. Meglévő használata. A hatás effektus engedélyezve van, vagy indítsa el az adatok megtekintéséhez több vizsgálók használata felügyelőknek pontok adott időben.

![a Halo vizsgáló](media/data-prep-getting-started/halo1.PNG) ![a Halo vizsgáló](media/data-prep-getting-started/halo2.PNG)

Átalakítás visszavonásához keresse fel a lépések listája a felhasználói felület jobb oldalán. (A lépések listapanel kell újra ki kell megjelenő. A megnyitáshoz kattintson a dupla sávnyíl mutat bal oldali). A panelen válassza ki az átalakítás végrehajtott, amelyeket meg szeretne visszavonni. Válassza ki a legördülő a felhasználói felület blokk jobb oldalán. Ezek közül bármelyikre **szerkesztése** módosításokat vagy **törlése** az átalakítás eltávolítása a lépéseket és az adatfolyam.

#### <a name="step-5-output"></a>5. lépés: kimenet 
Amikor végzett a adat-előkészítés, az adatfolyam-kimenetre írhat. Egy adatfolyam számos kimenetek is rendelkezhet. Az átalakítások menü kiválaszthatja azt szeretné, mert az adatkészlet kimeneti amely. A kimenet cél lehetőség kiválasztásával. 

## <a name="list-of-appendices"></a>Mellékletek listája 
[2. függelék – a támogatott adatforrások](data-prep-appendix2-supported-data-sources.md)  
[Függelék: 3 – támogatott átalakítások](data-prep-appendix3-supported-transforms.md)  
[4. függelék – támogatott vizsgálók](data-prep-appendix4-supported-inspectors.md)  
[5. függelék – támogatott célhelyek](data-prep-appendix5-supported-destinations.md)  
[Függelék: 6 – minta Szűrőkifejezésekben a Pythonban](data-prep-appendix6-sample-filter-expressions-python.md)  
[7 – minta függelék átalakítása adatfolyamot kifejezések a Pythonban](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Függelék: 8 - minta az adatforrásokat a Pythonban](data-prep-appendix8-sample-source-connections-python.md)  
[Függelék: 9 - minta cél kapcsolatok a Pythonban](data-prep-appendix9-sample-destination-connections-python.md)  
[10 – függelék: minta oszlop átalakításokra a Pythonban](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Lásd még:

[Fejlett adat-előkészítési oktatóanyagot](tutorial-bikeshare-dataprep.md)