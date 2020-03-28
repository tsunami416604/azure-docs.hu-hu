---
title: Adatcímkézési projekt létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre és futtathat címkézési projekteket a gépi tanulás adatainak címkézéséhez.  Az eszközök közé ml támogatott címkézés, vagy az emberi a hurok címkézés, hogy segítse a feladatot.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: d39cf8745c6f53cb11bb12561fd452325fe52ac6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296948"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Adatcímkézési projekt létrehozása és címkék exportálása 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A gépi tanulási projektekben a terjedelmes adatok címkézése gyakran fejfájást okoz. A számítógépes látás-összetevővel ( például képbesorolással vagy objektumészleléssel) rendelkező projektek általában több ezer kép címkéjét igénylik.
 
[Az Azure Machine Learning](https://ml.azure.com/) központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. Segítségével koordinálhat adatokat, címkéket és csapattagokat a címkézési feladatok hatékony kezeléséhez. A Machine Learning támogatja a lemezképek besorolását, akár többcímkét, akár többosztályos, és az objektumazonosítást kötött mezőkkel.

A Machine Learning nyomon követi a folyamatot, és fenntartja a befejezetlen címkézési feladatok várólistáját. A címkézőknek nincs szükségük Azure-fiókra a részvételhez. Miután a Microsoft-fiókkal vagy az [Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)való hitelesítést követően annyi címkézést tehetnek, amennyit az idejük megenged.

Indítsa el és állítsa le a projektet, vegyen fel és távolítson el címkézőket és csapatokat, és figyelje a címkézés folyamatát. A címkézett adatokcoco formátumban vagy Azure Machine Learning-adatkészletként exportálhatók.

> [!Important]
> Jelenleg csak a képbesorolási és objektumazonosítási címkézési projektek támogatottak. Emellett az adatlemezképeknek elérhetőnek kell lenniük egy Azure blob-adattárban. (Ha nem rendelkezik meglévő adattárban, képeket tölthet fel a projekt létrehozása során.) 

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> * Projekt létrehozása
> * A projekt adatainak és szerkezetének megadása
> * A projekten dolgozó csapatok és személyek kezelése
> * A projekt futtatása és figyelése
> * A címkék exportálása


## <a name="prerequisites"></a>Előfeltételek

* A címkét címkézni kívánt adatok, helyi fájlokban vagy az Azure blob storage-ban.
* Az alkalmazni kívánt címkék készlete.
* A címkézésre vonatkozó utasítások.
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://aka.ms/AMLFree) mielőtt elkezdené.
* Machine Learning-munkaterület. Lásd: [Azure Machine Learning-munkaterület létrehozása.](how-to-manage-workspace.md)

## <a name="create-a-labeling-project"></a>Címkézési projekt létrehozása

A címkézési projektek et az Azure Machine Learning kezeli. A **Projektek címkézése** lapon kezelheti a projekteket és a személyeket. Egy projekthez egy vagy több csapat van rendelve, és egy csapathoz egy vagy több ember van hozzárendelve.

Ha az adatok már az Azure Blob storage, elérhetővé kell tennie, mint egy adattár létrehozása előtt a címkézési projekt. További információt az [Adattárak létrehozása és regisztrálása című témakörben talál.](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)

Projekt létrehozásához válassza a **Projekt hozzáadása**lehetőséget. Adjon megfelelő nevet a projektnek, és válassza **a Címkézési tevékenységtípust.**

![A projektkészítő varázsló címkézése](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Válassza **a Többosztályos képbesorolást** a projektekhez, ha csak *egy osztályt* szeretne alkalmazni egy osztálykészletből egy képre.
* Válassza **a Képbesorolás többcímkét** a projektekhez, ha egy vagy *több* címkét szeretne alkalmazni egy osztálycsoportból egy képre. Például, egy fénykép -ból egy kutya erő lenni címkével ellátott -val mindkettő *kutya* és *nappali*.
* Válassza **az Objektumazonosítás (Határolókeret)** lehetőséget a projektekhez, ha osztályt és határolókeretet szeretne rendelni a kép minden objektumához.

Válassza a **Tovább gombot,** ha készen áll a folytatásra.

## <a name="specify-the-data-to-label"></a>A címkényként megcímkézni kívánt adatok megadása

Ha már létrehozott egy adatkészletet, amely tartalmazza az adatokat, jelölje ki azt a **Meglévő adatkészlet kiválasztása** legördülő listából. Vagy válassza **az adatkészlet létrehozása** egy meglévő Azure-adattár használatához vagy helyi fájlok feltöltéséhez.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Adatkészlet létrehozása Azure-adattárból

Sok esetben, ez rendben van, hogy csak feltölteni a helyi fájlokat. [Az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) azonban gyorsabb és robusztusabb módot biztosít a nagy mennyiségű adat átvitelére. A fájlok áthelyezésének alapértelmezett módja a Storage Explorer használatát javasoljuk.

Adatkészlet létrehozása az Azure Blob storage-ban már tárolt adatokból:

1. Válassza **az Adatkészlet** > létrehozása**az adattárból**lehetőséget.
1. **Rendeljen nevet** az adatkészlethez.
1. Válassza a **Fájl** **adatkészlettípusként lehetőséget.**  
1. Jelölje ki az adattaboltot.
1. Ha az adatok a blobstorage egy almappájában vannak, válassza a **Tallózás** gombot az elérési út kiválasztásához.
    * Fűzz "/**" az elérési úthoz, hogy a kijelölt görbe almappáiban lévő összes fájl szerepeljen.
    * A "**/*.*" hozzáfűzése az aktuális tárolóban és almappáiban lévő összes adatfelvételhez.
1. Adja meg az adatkészlet leírását.
1. Válassza a **Tovább lehetőséget.**
1. Erősítse meg a részleteket. A beállítások módosításához válassza a **Vissza** lehetőséget, vagy a **Létrehozás gombot** az adatkészlet létrehozásához.

> [!NOTE]
> A kiválasztott adatok betöltődnek a projektbe.  Ha további adatokat ad hozzá az adattárhoz, a projekt létrehozása után nem jelenik meg a projektben.  

### <a name="create-a-dataset-from-uploaded-data"></a>Adatkészlet létrehozása feltöltött adatokból

Az adatok közvetlen feltöltése:

1. Válassza **az Adatkészlet** > létrehozása**helyi fájlokból**lehetőséget.
1. **Rendeljen nevet** az adatkészlethez.
1. **Az adatkészlet típusaként**válassza a "Fájl" lehetőséget.
1. *Nem kötelező:* Válassza **a Speciális beállítások lehetőséget** az adattár, a tároló és az adatok elérési útjának testreszabásához.
1. A **Tallózás gombra** a feltöltendő helyi fájlok kijelöléséhez válassza a tallózás lehetőséget.
1. Adja meg az adatkészlet leírását.
1. Válassza a **Tovább lehetőséget.**
1. Erősítse meg a részleteket. A beállítások módosításához válassza a **Vissza** lehetőséget, vagy a **Létrehozás gombot** az adatkészlet létrehozásához.

Az adatok a Machine Learning-munkaterület alapértelmezett blobtárolójára ("workspaceblobstore") lesznek feltöltve.

## <a name="specify-label-classes"></a>Címkeosztályok megadása

A **Címkeosztályok** lapon adja meg az adatok kategorizálásához kívánt osztályok készletét. Ehhez óvatosan, mert a címkézők pontosságát és sebességét befolyásolja, hogy képesek választani az osztályok között. Például ahelyett, hogy a növények vagy állatok teljes nemét és faját írnák, használjon mezőkódot, vagy rövidítse ki a nemet.

Soronként egy címkét adjon meg. A **+** gombbal új sort adhat hozzá. Ha 3-nál több vagy 4 címkével rendelkezik, de kevesebb, mint 10, akkor a neveket számokkal ("1: ", "2: ") előtagozhatja, hogy a címkézők a számbillentyűkkel felgyorsíthassák a munkájukat.

## <a name="describe-the-labeling-task"></a>A címkézési feladat leírása

Fontos, hogy egyértelműen magyarázza a címkézési feladatot. A **Címkézési utasítások** lapon hozzáadhat egy külső webhelyre mutató hivatkozást a címkézési utasításokhoz, vagy utasításokat adhat a lap szerkesztési mezőjében. Tartsa az utasításokat feladat-orientált és megfelelő a közönség számára. Tekintsük át ezeket a kérdéseket:

* Milyen címkéket fognak látni, és hogyan fognak választani közülük? Van-e hivatkozási szöveg, amelyre hivatkozni lehet?
* Mit tegyenek, ha nincs megfelelő címke?
* Mit tegyenek, ha több címke tűnik megfelelőnek?
* Milyen megbízhatósági küszöbértéket kell alkalmazni a címkére? Szeretné, hogy a "legjobb hiszem", ha nem biztos benne?
* Mit tegyenek a részben elzárt vagy egymást átfedő érdekes tárgyakkal?
* Mit tegyenek, ha egy érdekes tárgyat a kép széle vág le?
* Mit kell tenniük, miután benyújtják a címkét, ha úgy gondolják, hogy hibát követtek el?

A határolódobozok esetében a fontos kérdések a következők:

* Hogyan van definiálva a határolókeret ehhez a tevékenységhez? Legyen teljesen a belső a tárgy, vagy kell, hogy a külső? A lehető legszorosabban kell-e levágni, vagy elfogadható-e valamilyen távolság?
* Milyen szintű gondossággal és konzisztenciával kell ellátni a címkézőket a határolódobozok meghatározásában?
* Hogyan kell címkézni a részben látható objektumot a képen? 
* Hogyan kell címkézni az objektumot, amelyet részben más objektum borít?

>[!NOTE]
> Ügyeljen arra, hogy vegye figyelembe, hogy a címkézők képesek lesznek kiválasztani az első 9 címkék segítségével számgombok 1-9.

## <a name="use-ml-assisted-labeling"></a>Ml-es rásegítéses címkézés használata

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Az **ML-es nyelvben támogatott címkézési** lap lehetővé teszi az automatikus gépi tanulási modellek indítását a címkézési feladat felgyorsítása érdekében. A címkézési projekt elején a képek véletlenszerű sorrendbe kerülnek, hogy csökkentsék a potenciális elfogultságot. Azonban az adatkészletben jelen lévő előítéletek tükröződni fognak a betanított modellben. Ha például a képek 80%-a egyetlen osztályba tartozik, akkor a modell betanításához használt adatok körülbelül 80%-a az adott osztályhoz fog tartozni. Ez a képzés nem foglalja magában az aktív tanulást.

Ez a funkció képbesorolási (többosztályos vagy többcímkés) feladatokhoz érhető el.  

Válassza *a Ml-es támogatott címkézés engedélyezése* lehetőséget, és adjon meg egy GPU-t a támogatott címkézés engedélyezéséhez, amely két fázisból áll:
* Fürtözés
* Előre címkézés

A támogatott címkézés elindításához szükséges címkézett képek pontos száma nem rögzített szám.  Ez jelentősen eltérhet az egyik címkézési projekttől a másikig. Egyes projektek esetében néha 300 lemezkép manuális címkézése után is meglehet tekinteni az előcímke- vagy fürtfeladatokat. A ml-es nyelvben támogatott címkézés a *Transfer Learning*nevű technikát használja, amely egy előre betanított modellt használ a betanítási folyamat ugrásindításához. Ha az adatkészlet osztályai hasonlóak az előre betanított modellosztályaihoz, előfordulhat, hogy az előcímkék csak néhány száz manuálisan címkézett lemezkép után érhetők el. Ha az adatkészlet jelentősen eltér a modell előzetes betanításához használt adatoktól, sokkal tovább is eltarthat.

Mivel a végleges címkék még mindig támaszkodnak bemenet a címkéző, ez a technológia néha *emberi a hurok* címkézés.

### <a name="clustering"></a>Fürtözés

Bizonyos számú címke elküldése után a gépi tanulási modell elkezdi csoportosítani a hasonló képeket.  Ezek a hasonló képek jelennek meg a címkézők ugyanazon a képernyőn, hogy gyorsítsák fel a kézi címkézés. A fürtözés különösen akkor hasznos, ha a címkéző 4, 6 vagy 9 képből álló rácsot tekint meg. 

Miután egy gépi tanulási modell be tanítása a manuálisan címkézett adatok, a modell csonkolt az utolsó teljesen csatlakoztatott réteg. A címkézetlen képek ezután áthaladnak a csonkolt modellen egy olyan folyamatban, amelyet általánosan "beágyazásnak" vagy "jellemzőnek" neveznek. Ez minden képet egy nagy dimenziós térbe ágyaz, amelyet ez a modellréteg határoz meg. A terület legközelebbi szomszédjai a fürtözési feladatokhoz használatosak. 

### <a name="prelabeling"></a>Előre címkézés

Miután több képcímkék et küldött, a rendszer besorolási modellt használ a képcímkék előrejelzésére.  A címkéző most antól látja azokat az oldalakat, amelyek előre jelzett címkéket tartalmaznak, amelyek már jelen vannak az egyes képeken.  A feladat ezután, hogy vizsgálja felül ezeket a címkéket, és javítsa ki a hibásan címkézett képeket, mielőtt elküldi az oldalt.  

Miután egy gépi tanulási modell betanítása a manuálisan címkézett adatok, a modell kiértékelése egy tesztkészlet manuálisan címkézett képek pontosságának meghatározásához a különböző megbízhatósági küszöbértékek. Ez az értékelési folyamat egy megbízhatósági küszöbérték meghatározására szolgál, amely felett a modell elég pontos az előzetes címkék megjelenítéséhez. A modell ezután kiértékelése a címkézetlen adatok alapján. Az előre címkézéshez az előrejelzéseket tartalmazó, ebben a küszöbértékben magabiztosabb képeket használnak.

> [!NOTE]
> A ml-es rendszerű címkézés **csak** az Enterprise edition munkaterületeken érhető el.

## <a name="initialize-the-labeling-project"></a>A címkézési projekt inicializálása

A címkézési projekt inicializálása után a projekt bizonyos aspektusai nem módosíthatók. A tevékenység típusa vagy adatkészlete nem módosítható. *Módosíthatja* a címkéket és a feladat leírásának URL-címét. A projekt létrehozása előtt alaposan tekintse át a beállításokat. A projekt elküldése után visszatér az **Adatcímkézés** kezdőlapjára, amely a projektet **inicializálásként**jeleníti meg. Ez a lap nem frissül automatikusan. Így egy szünet után manuálisan frissítse a lapot, hogy a projekt **állapota Létrehozva**lesz.

## <a name="manage-teams-and-people"></a>Csapatok és személyek kezelése

Alapértelmezés szerint minden létrehozott címkézési projekt egy új csapatot kap Önnel, mint taggal. De a csapatok is megoszthatók a projektek között. És a projekteknek több csapata is lehet. Csapat létrehozásához válassza a **Csapat hozzáadása lehetőséget** a **Csapatok** lapon.

Ön kezelheti az embereket a **Személyek** lapon. Személyek felvétele és eltávolítása e-mail cím szerint. Minden címkéző hitelesítést kell hitelesíteni a Microsoft-fiók vagy az Azure Active Directory, ha használja.  

Miután hozzáadott egy személyt, hozzárendelheti az illetőt egy vagy több csapathoz: Lépjen a **Csapatok** lapra, jelölje ki a csapatot, majd válassza a **Személyek hozzárendelése** vagy **A személyek eltávolítása lehetőséget.**

Ha e-mailt szeretne küldeni a csapatnak, válassza ki a **csapatot** a Csapat részletei lap megtekintéséhez. Ezen a lapon válassza az **E-mail csapat** lehetőséget egy e-mail-vázlat megnyitásához a csapat minden tagjának címével.

## <a name="run-and-monitor-the-project"></a>A projekt futtatása és figyelése

A projekt inicializálása után az Azure megkezdi a projekt futtatását. Jelölje ki a projektet a fő **adatcímkézési** lapon a **Project részletei című lapra.** Az **Irányítópult** lapon látható a címkézési feladat előrehaladása.

Az **Adatok** lapon megtekintheti az adatkészletet, és áttekintheti a címkézett adatokat. Ha helytelenül címkézett adatokat lát, jelölje ki, és válassza az Elutasítás lehetőséget, amely **eltávolítja**a címkéket, és visszahelyezi az adatokat a címkézetlen várólistába.

A **Csapat** lapon csapatokat rendelhet hozzá vagy rendelhet vissza a projekttől.

A projekt szüneteltetéséhez vagy újraindításához kattintson a**Start** **szüneteltetése**/gombra. Az adatokcsak akkor címkézhetők, ha a projekt fut.

Az adatokat közvetlenül a **Projekt részletei** lapon címkézheti a **Címkeadatok**lehetőség kiválasztásával.

## <a name="add-new-label-class-to-a-project"></a>Új címkeosztály hozzáadása a projekthez

A címkézési folyamat során előfordulhat, hogy további címkékre van szükség a képek besorolásához.  Előfordulhat például, hogy egy "Ismeretlen" vagy "Egyéb" címkét szeretne hozzáadni a zavaros képek jelzésére.

Az alábbi lépésekkel egy vagy több címkét adhat a projekthez:

1. Jelölje ki a projektet a fő **adatfeliratozási** lapon.
1. A lap tetején válassza a **Szünet elévülése** lehetőséget, ha le szeretné állítani a címkézők tevékenységét.
1. Válassza a **Részletek** fület.
1. A bal oldali listában válassza a **Címkeosztályok**lehetőséget.
1. A lista tetején válassza a **+ Címkék hozzáadása Címke** ![hozzáadása lehetőséget.](media/how-to-create-labeling-projects/add-label.png)
1. Az űrlapon adja hozzá az új címkét, és válassza ki a folytatás módját.  Mivel módosította egy kép elérhető címkéit, ön dönti el, hogyan kezelje a már címkézett adatokat:
    * Kezdje újra, távolítsa el az összes meglévő címkét.  Akkor válassza ezt a lehetőséget, ha a címkézést az elejétől az új teljes címkekészlettel szeretné kezdeni. 
    * Kezdje elölről, megtartva az összes meglévő címkét.  Akkor válassza ezt a lehetőséget, ha az összes adatot címkézetlenként szeretné megjelölni, de a meglévő címkéket megtartja alapértelmezett címkeként a korábban címkézett képeknél.
    * Folytassa az összes meglévő címkét. Akkor válassza ezt a lehetőséget, ha meg szeretné tartani az összes már címkézett adatot, és el szeretné kezdeni használni az új címkét a még nem címkézett adatokhoz.
1. Szükség szerint módosítsa az útmutató lapot az új címkéhez.
1. Miután felvette az összes új címkét, a lap tetején válassza a **Start** gombot a projekt újraindításához.  

## <a name="export-the-labels"></a>A címkék exportálása

A Machine Learning-kísérletezés címkeadatait bármikor exportálhatja. A képcímkék [COCO formátumban](http://cocodataset.org/#format-data) vagy Azure Machine Learning-adatkészletként exportálhatók. Használja az **Exportálás** gombot a címkeprojekt **Projekt részletei** lapján.

A COCO-fájl az Azure Machine Learning-munkaterület alapértelmezett blobtárolójában jön létre az *exportálás/kakaó*mappájában. Az exportált Azure Machine Learning-adatkészlet a Machine Learning **adatkészletek** szakaszában érhető el. Az adatkészlet részletei lap is biztosít mintakódot a címkék eléréséhez a Python.

![Exportált adatkészlet](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>További lépések

* Képek címkézése [képbesoroláshoz vagy objektumészleléshez](how-to-label-images.md)
* További információ az [Azure Machine Learning és a Machine Learning Studio (klasszikus)](compare-azure-ml-to-studio-classic.md)
