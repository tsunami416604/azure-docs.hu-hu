---
title: '1. oktatóanyag: Hitelkockázat előrejelzése'
titleSuffix: ML Studio (classic) - Azure
description: Részletes oktatóanyag, amely bemutatja, hogyan hozhat létre prediktív elemzési megoldást a hitelkockázat-értékeléshez az Azure Machine Learning Studio (klasszikus) környezetében. Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat első része.  Bemutatja, hogyan hozhat létre munkaterületet, tölthet fel adatokat, és hozhat létre egy kísérletet.
keywords: hitelkockázat, prediktív elemzési megoldás,kockázatértékelés
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6fd8573c78d80c950bdeb41ec01e2835def3979a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204256"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>1. oktatóanyag: Hitelkockázat előrejelzése – Azure Machine Learning Studio (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Ebben az oktatóanyagban tekintse meg a prediktív elemzési megoldás kifejlesztésének folyamatát. Egy egyszerű modellt fejleszt a Machine Learning Studio (klasszikus) alkalmazásban.  Ezután üzembe helyezi a modellt egy Azure Machine Learning-webszolgáltatásként.  Ez az üzembe helyezett modell új adatok használatával előrejelzéseket készíthet. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat első része.**

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

Hitelkockázat értékelése egy összetett probléma, de ez a bemutató egyszerűsíti azt egy kicsit. Ezt fogja használni példaként arra, hogyan hozhat létre prediktív elemzési megoldást a Microsoft Azure Machine Learning Studio (klasszikus) használatával. Ehhez a megoldáshoz az Azure Machine Learning Studio (klasszikus) és egy Machine Learning-webszolgáltatás használható.  

Ebben a három részes oktatóanyagban a nyilvánosan elérhető hitelkockázati adatokkal kezdheti.  Ezután kifejleszt és betanít egy prediktív modellt.  Végül a modellt webszolgáltatásként telepíti.

Ebben a részben a bemutató, hogy: 
 
> [!div class="checklist"]
> * Machine Learning Studio (klasszikus) munkaterület létrehozása
> * Meglévő adatok feltöltése
> * Kísérlet létrehozása

Ezután használhatja ezt a kísérletet, [deploy them in part 3](tutorial-part3-credit-risk-deploy.md)hogy [betanítsa a modelleket a 2.](tutorial-part2-credit-risk-train.md)

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már legalább egyszer használta a Machine Learning Studio (klasszikus) alkalmazást, és hogy ismeri a gépi tanulási fogalmakat. Az útmutató azonban nem feltételezi, hogy a fent említett területeken szakértő lenne.

Ha még soha nem használta **az Azure Machine Learning Studio (klasszikus)** előtt, érdemes lehet kezdeni a rövid útmutató, Az első [adatelemzési kísérlet létrehozása az Azure Machine Learning Studio (klasszikus)](create-experiment.md). A rövid útmutató első alkalommal vezeti végig a Machine Learning Studio (klasszikus) gépen. Bemutatja az alapokat, azt, hogy hogyan húzhat be modulokat a kísérletbe és kapcsolhatja össze azokat, és hogyan futtathatja a kísérletet és tekintheti meg az eredményeket.


> [!TIP] 
> Az [azure AI-gyűjteményben](https://gallery.azure.ai)található oktatóanyagban megtalálhatja a kifejlesztett kísérlet egy működő példányát. Ugrás **[az oktatóanyagra – A hitelkockázat előrejelzése,](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** majd a Megnyitás a **Stúdióban** gombra kattintva letöltheti a kísérlet egy példányát a Machine Learning Studio (klasszikus) munkaterületére.
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Machine Learning Studio (klasszikus) munkaterület létrehozása

A Machine Learning Studio (klasszikus) használatához rendelkeznie kell egy Microsoft Azure Machine Learning Studio (klasszikus) munkaterülettel. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.  

Munkaterület létrehozásához olvassa el az [Azure Machine Learning Studio (klasszikus) munkaterület létrehozása és megosztása című témakört.](create-workspace.md)

A munkaterület létrehozása után nyissa meg a Machine[https://studio.azureml.net/Home](https://studio.azureml.net/Home)Learning Studio (klasszikus) ( ) alkalmazást. Ha egynél több munkaterülettel rendelkezik, az ablak jobb felső sarkában lévő eszköztáron kijelölheti a munkaterületet.

![Munkaterület kiválasztása a Studio-ban (klasszikus)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Ha Ön a munkaterület tulajdonosa, megoszthatja a kísérleteket, amelyeken dolgozik, ha másokat meghív a munkaterületre. Ezt a Machine Learning Studio (klasszikus) a **BEÁLLÍTÁSOK** lapon. Csak az egyes felhasználók Microsoft-fiókjára vagy szervezeti fiókjára van szüksége.
> 
> A **BEÁLLÍTÁSOK** lapon kattintson a **FELHASZNÁLÓK**elemre, majd az ablak alján kattintson a **További felhasználók meghívása** elemre.
> 

## <a name="upload-existing-data"></a><a name="upload"></a>Meglévő adatok feltöltése

A hitelkockázat prediktív modelljének fejlesztéséhez olyan adatokra van szüksége, amelyek betanításához és a modell teszteléséhez használható. Ebben az oktatóanyagban az UC Irvine Machine Learning adattár "UCI Statlog (német hiteladatok) adatkészletét fogja használni". Megtalálható itt:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

A **german.data**nevű fájlt fogja használni. Töltse le ezt a fájlt a helyi merevlemezre.  

A **german.data** adatkészlet 20 változóból álló sorokat tartalmaz 1000 korábbi hiteligénylő számára. Ez a 20 változó az adatkészlet funkciókészletét (a *szolgáltatásvektort)* jelöli, amely minden egyes hiteligénylő számára azonosító jellemzőket biztosít. Minden sorban egy további oszlop jelöli a kérelmező számított hitelkockázatát, 700 kérelmezőt alacsony hitelkockázatként, 300-at pedig magas kockázatként azonosítottak.

Az UCI webhely e szolgáltatások vektorának attribútumainak leírását tartalmazza. Ezek az adatok magukban foglalnak pénzügyi információkat, hitelelőzményeket, foglalkoztatási állapotot és személyes adatokat. Minden kérelmező esetében bináris minősítést adtak, amely jelzi, hogy alacsony vagy magas hitelkockázatról van-e szó. 

Ezeket az adatokat egy prediktív elemzési modell betanításához fogja használni. Ha elkészült, a modell képesnek kell lennie arra, hogy elfogadja a funkció vektor egy új személy, és megjósolni, hogy azok alacsony vagy magas hitelkockázat.  

Itt egy érdekes csavar.

Az UCI webhelyén található adatkészlet leírása megemlíti, hogy mennyibe kerül, ha tévesen sorolja be egy személy hitelkockázatát.
Ha a modell azt jósolja, hogy magas hitelkockázat egy olyan személy számára, aki valójában alacsony hitelkockázat, a modell téves besorolást adott.

A fordított téves besorolás azonban ötször költségesebb a pénzintézet számára: ha a modell alacsony hitelkockázatot jelez előre egy olyan személy számára, aki valójában magas hitelkockázat.

Tehát azt szeretné beképezni a modellt, hogy az utóbbi típusú téves besorolás költsége ötször magasabb legyen, mint a másik módon történő téves besorolás.

Egy egyszerű módja ennek, ha a modell betanítása a kísérletben a másolás (ötször) azokat a bejegyzéseket, amelyek képviselik a magas hitelkockázat. 

Ezután, ha a modell tévesen minősít valakit, mint egy alacsony hitelkockázat, amikor valójában egy magas kockázatú, a modell nem, hogy ugyanazt a téves besorolás ötször, egyszer minden másodlat. Ez növeli a hiba költségét a képzési eredményekben.


### <a name="convert-the-dataset-format"></a>Az adatkészlet formátumának konvertálása

Az eredeti adatkészlet üresen elválasztott formátumot használ. A Machine Learning Studio (klasszikus) jobban működik egy vesszővel tagolt értékfájllal (CSV), így az adatkészletet úgy konvertálhatja, hogy a szóközöket vesszővel helyettesíti.  

Ezeket az adatokat számos módon konvertálhatja. Ennek egyik módja a következő Windows PowerShell parancs használata:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Egy másik módja a Unix sed parancs használatával:  

    sed 's/ /,/g' german.data > german.csv  

Mindkét esetben létrehozta az adatok vesszővel tagolt változatát egy **german.csv** nevű fájlban, amelyet a kísérletben használhat.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Az adatkészlet feltöltése a Machine Learning Studio-ba (klasszikus)

Miután az adatokcsv-formátumra konvertálódtak, fel kell töltenie őket a Machine Learning Studio (klasszikus) gépi tanulásba. 

1. Nyissa meg a Machine Learning Studio[https://studio.azureml.net](https://studio.azureml.net)(klasszikus) kezdőlapját ( ). 

2. Kattintson ![az](./media/tutorial-part1-credit-risk/menu.png) ablak bal felső sarkában található menüre, kattintson az **Azure Machine Learning parancsra,** válassza a **Stúdió**lehetőséget, és jelentkezzen be.

3. Kattintson az ablak alján a **+ÚJ** gombra.

4. Válassza **az ADATKÉSZLET lehetőséget**.

5. Válassza **a HELYI FÁJLBÓL**lehetőséget .

    ![Adatkészlet hozzáadása helyi fájlból](./media/tutorial-part1-credit-risk/add-dataset.png)

6. Az **Új adatkészlet feltöltése** párbeszédpanelen kattintson a Tallózás gombra, és keresse meg a létrehozott **german.csv** fájlt.

7. Adja meg az adatkészlet nevét. Ebben a bemutató, nevezzük "UCI német hitelkártya-adatok".

8. Adattípus esetén válassza **az Általános CSV-fájl fejléc nélküli (.nh.csv)** lehetőséget.

9. Adjon hozzá leírást, ha szeretne.

10. Kattintson az **OK** gombra.  

    ![Az adatkészlet feltöltése](./media/tutorial-part1-credit-risk/upload-dataset.png)

Ez feltölti az adatokat egy adatkészlet modulba, amely et egy kísérletben használhat.

A Studio (klasszikus) rendszerbe feltöltött adatkészletek kezelését a Studio (klasszikus) ablakbal balra található **ADATKÉSZLETEK** fülre kattintva kezelheti.

![Adatkészletek kezelése](./media/tutorial-part1-credit-risk/dataset-list.png)

Ha további információra van hatással más típusú adatok kísérletbe történő importálásáról, [olvassa el a Betanítási adatok importálása az Azure Machine Learning Studio -ba (klasszikus) című témakört.](import-data.md)

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Az oktatóanyag következő lépése egy kísérlet létrehozása a Machine Learning Studio (klasszikus), amely a feltöltött adatkészletet használja.  

1. A Studio (klasszikus) alkalmazásban kattintson az ablak alján a **+ÚJ** gombra.
1. Válassza **a KÍSÉRLET**lehetőséget, majd az "Üres kísérlet" lehetőséget. 

    ![Új kísérlet létrehozása](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Jelölje ki az alapértelmezett kísérletnevét a vászon tetején, és nevezze át valami értelmesre.

    ![Kísérlet átnevezése](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Célszerű kitölteni a kísérlet **összegzését** és **leírását** a **Tulajdonságok** ablaktáblán. Ezek a tulajdonságok lehetőséget adnak a kísérlet dokumentálására, hogy bárki, aki később megnézi, megértse a céljait és módszertanát.
   > 
   > ![Kísérlet tulajdonságai](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. A kísérletvászontól balra lévő modulpalettán bontsa ki a **Mentett adatkészletek csomópontot.**
1. Keresse meg a **Saját adatkészletek** csoportban létrehozott adatkészletet, és húzza a vászonra. Az adatkészletet úgy is megtalálhatja, hogy beírja a nevet a **paletta feletti Keresőmezőbe.**  

    ![Az adatkészlet hozzáadása a kísérlethez](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Az adatok előkészítése

Megtekintheti az adatok első 100 sorát és néhány statisztikai információt a teljes adatkészletről: Kattintson az adatkészlet kimeneti portjára (az alján lévő kis körre), és válassza a **Visualize**lehetőséget.  

Mivel az adatfájlhoz nem tartoznak oszlopfejlécek, a Studio (klasszikus) általános címsorokat (Col1, Col2 *stb.)* adott meg. A jó címsorok nem elengedhetetlenek a modell létrehozásához, de megkönnyítik a kísérletben lévő adatokkal való munkát. Továbbá, ha végül közzéteszi ezt a modellt egy webszolgáltatásban, a címsorok segítenek azonosítani az oszlopokat a szolgáltatás felhasználója számára.  

A [Metaadatok szerkesztése][edit-metadata] modul segítségével oszlopfejléceket adhat hozzá.

A [Metaadatok szerkesztése][edit-metadata] modul segítségével módosíthatja az adatkészlethez társított metaadatokat. Ebben az esetben arra használhatja, hogy rövidebb neveket adjon meg az oszlopfejlécekszámára. 

A [Metaadatok szerkesztése][edit-metadata]mező használatához először megadhatja, hogy mely oszlopokat kell módosítani (ebben az esetben az összeset).) Ezután adja meg az oszlopokon végrehajtandó műveletet (ebben az esetben az oszlopfejlécek módosítását.)

1. A modulpalettán írja be a "metaadatok" kifejezést a **Keresőmezőbe.** A [Metaadatok szerkesztése][edit-metadata] megjelenik a modullistában.

1. Kattintson a [Metaadatok szerkesztése][edit-metadata] modulra, és húzza a vászonra, és húzza a korábban hozzáadott adatkészlet alá.

1. Csatlakoztassa az adatkészletet a [Metaadatok szerkesztése][edit-metadata]beállításhoz: kattintson az adatkészlet kimeneti portjára (az adatkészlet alján lévő kis körre), húzza az egérmutatót a [Metaadatok szerkesztése][edit-metadata] bemeneti portjára (a modul tetején lévő kis körbe), majd engedje fel az egérgombot. Az adatkészlet és a modul akkor is kapcsolatban marad, ha a vásznon mozog.
 
    A kísérlet most valahogy így néz ki:  

    ![Metaadatok szerkesztése hozzáadása](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    A piros felkiáltójel azt jelzi, hogy még nem állította be a modul tulajdonságait. Ezt a következő lépésben teheti meg.

    > [!TIP]
    > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. Ebben az esetben kattintson duplán a [Metaadatok szerkesztése][edit-metadata] modulra, és írja be az "Oszlopfejlécek hozzáadása" megjegyzést. Kattintson a vászon bármely pontjára a szövegdoboz bezárásához. A megjegyzés megjelenítéséhez kattintson a modulon lévő lefelé mutató nyílra.
    > 
    > ![Metaadat-modul szerkesztése megjegyzéssel](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Válassza [a Metaadatok szerkesztése][edit-metadata]lehetőséget, majd a vászon jobb oldalán található **Tulajdonságok** ablaktáblán kattintson az **Indítás oszlopkijelölő gombra.**

1. Az **Oszlopok kijelölése** párbeszédpanelen jelölje ki az összes sort az **Elérhető oszlopok** párbeszédpanelen, és kattintson > gombra a **Kijelölt oszlopok**elemre való áthelyezéséhez.
   A párbeszédablaknak így kell kinéznie:

   ![Oszlopkijelölő az összes kijelölt oszlopmal](./media/tutorial-part1-credit-risk/select-columns.png)


1. Kattintson az **OK** gombra.

1. A **Tulajdonságok** ablaktáblán keresse meg az **Új oszlopnevek paramétert.** Ebben a mezőben adja meg az adatkészlet 21 oszlopának nevét vesszővel és oszlopsorrendben elválasztva. Az oszlopok nevét az UCI webhelyén található adatkészlet dokumentációjából szerezheti be, vagy az egyszerűség kedvéért másolhatja és beillesztheti a következő listát:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   A Tulajdonságok ablaktábla így néz ki:

   ![Metaadatok szerkesztési tulajdonságai](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Ha ellenőrizni szeretné az oszlopfejléceket, futtassa a kísérletet (kattintson a **FUTTATÁS** gombra a kísérleti vászon alatt). Amikor befejezi a futást (zöld pipa jelenik meg a [Metaadatok szerkesztése][edit-metadata]mezőben), kattintson a [Metaadatok szerkesztése][edit-metadata] modul kimeneti portjára, és válassza a **Visualize**lehetőséget. Bármely modul kimenetét ugyanúgy tekintheti meg, hogy az adatok előrehaladását a kísérleten keresztül megtekinthesse.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Betanítási és tesztelési adatkészletek létrehozása

Szüksége van néhány adata a modell betanításához, és néhányra a teszthez.
Így a kísérlet következő lépésében két külön adatkészletre osztja fel az adatkészletet: az egyiket a modell betanításához, a másikat pedig a teszteléshez.

Ehhez használja a [Split Data][split] modult.  

1. Keresse meg a [Split Data modult,][split] húzza a vászonra, és csatlakoztassa a [Metaadatok szerkesztése][edit-metadata] modulhoz.

1. Alapértelmezés szerint a felosztási arány 0,5, és a **Randomized split** paraméter van beállítva. Ez azt jelenti, hogy az adatok véletlenszerű fele a [Split Data][split] modul egyik portjával, a másik fele pedig a másikon keresztül történik. Ezeket a paramétereket, valamint a **Véletlen mag** paramétert módosíthatja a betanítási és tesztelési adatok közötti felosztás módosításához. Ebben a példában úgy hagyja őket, ahogy van.
   
   > [!TIP]
   > A tulajdonság **Az első kimeneti adatkészlet sorainak töredéke** határozza meg, hogy az adatok mekkora része kerül a bal *oldali* kimeneti porton keresztül. Ha például az arányt 0,7-re állítja, akkor az adatok 70%-a a bal oldali porton keresztül, 30%-a pedig a jobb oldali porton keresztül történik.  
   > 
   > 

1. Kattintson duplán az [Adatok felosztása][split] modulra, és írja be a "Betanítási/tesztelési adatok felosztása 50%" megjegyzést. 

Használhatja a kimenetek a [Split Data][split] modul, ahogy tetszik, de nézzük meg, hogy a bal oldali kimenet betanítási adatok és a megfelelő kimeneti tesztelési adatokként.  

Amint azt az [előző lépésben](tutorial-part1-credit-risk.md#upload)említettük, a magas hitelkockázat alacsonyként való téves besorolásának költsége ötször magasabb, mint az alacsony hitelkockázat magasként történő téves besorolásának költsége. Ennek figyelembevételéhez létrehoz egy új adatkészletet, amely tükrözi ezt a költségfüggvényt. Az új adatkészletben minden magas kockázatú példa replikálódik ötször, míg minden egyes alacsony kockázatú példa nem replikálódik.   

Ezt a replikációt az R-kód használatával teheti meg:  

1. Keresse meg és húzza az [R-parancsfájl végrehajtása][execute-r-script] modult a kísérlet vászonra. 

1. Csatlakoztassa a [Split Data][split] modul bal oldali kimeneti portját az [R-parancsfájl végrehajtása][execute-r-script] modul első bemeneti portjához ("Dataset1").

1. Kattintson duplán az [R-parancsfájl végrehajtása][execute-r-script] modulra, és írja be a "Költséghelyesbítés beállítása" megjegyzést.

1. A **Tulajdonságok** ablaktáblán törölje az alapértelmezett szöveget az **R Script** paraméterben, és írja be ezt a parancsfájlt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R parancsfájl az R-parancsfájl végrehajtása modulban](./media/tutorial-part1-credit-risk/execute-r-script.png)

Ugyanezt a replikációs műveletet kell végrehajtania a [Split Data][split] modul minden kimenetéhez, hogy a betanítási és tesztelési adatok azonos költségkorrekcióval rendelkezzenek. Ennek legegyszerűbb módja az imént készített [R-parancsfájl végrehajtása][execute-r-script] modul megkettőzése és a Split [Data][split] modul másik kimeneti portjához való csatlakoztatása.

1. Kattintson a jobb gombbal az [R-parancsfájl végrehajtása][execute-r-script] modulra, és válassza a **Másolás parancsot.**

1. Kattintson a jobb gombbal a kísérleti vászonra, és válassza **a Beillesztés parancsot.**

1. Húzza az új modult a helyére, majd csatlakoztassa a [Split Data][split] modul jobb kimeneti portját az új [R-parancsfájl végrehajtása][execute-r-script] modul első bemeneti portjához. 

1. A vászon alján kattintson a **Futtatás**gombra. 

> [!TIP]
> Az R parancsfájl végrehajtása modul példánya ugyanazt a parancsfájlt tartalmazza, mint az eredeti modul. Amikor egy modult másol és beilleszt a vászonra, a másolat megőrzi az eredeti összes tulajdonságát.  
> 
>

Kísérletünk most valahogy így néz ki:

![Split modul és R parancsfájlok hozzáadása](./media/tutorial-part1-credit-risk/experiment.png)

Az R parancsfájlok kísérletekben való használatáról a [Kísérlet kiterjesztése az R-rel](extend-your-experiment-with-r.md)című témakörben talál további információt.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alábbi lépéseket hajtotta végre: 
 
> [!div class="checklist"]
> * Machine Learning Studio (klasszikus) munkaterület létrehozása
> * Meglévő adatok feltöltése a munkaterületre
> * Kísérlet létrehozása

Most már készen áll az adatok modellek betanítására és kiértékelésére.

> [!div class="nextstepaction"]
> [2. bemutató - A modellek betanítása és értékelése](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
