---
title: '1. oktatóanyag: Hitelkockázat előrejelzése'
titleSuffix: Azure Machine Learning Studio
description: Hitelkockázat értékelésére szolgáló prediktív elemzési megoldás létrehozása az Azure Machine Learning Studio bemutató részletes oktatóanyag. Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat része.  Ez bemutatja, hogyan hozzon létre egy munkaterületet, az adatok feltöltése és a egy kísérlet létrehozásához.
keywords: hitelkockázat, prediktív elemzési megoldás,kockázatértékelés
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: c067b0e6a85e0b5c4bd4cbb582de13bb1bc87774
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453613"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio"></a>1. oktatóanyag: Hitelkockázat - Azure Machine Learning Studio előrejelzése

Ebben az oktatóanyagban a folyamat egy prediktív elemzési megoldás fejlesztése tekintjük igénybe vehet. A Machine Learning Studio egy egyszerű modellt fejleszt.  A modell Azure Machine Learning-webszolgáltatásként majd rendszerbe.  Ez a modell üzembe helyezett végezhet előrejelzéseket új adatokkal. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat része**.

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat értékelése összetett probléma, de ebben az oktatóanyagban leegyszerűsíti azt egy kicsit. Ez a példa használatával a Microsoft Azure Machine Learning Studio prediktív elemzési megoldások létrehozásának módját fogja használni. Az Azure Machine Learning Studio és a egy Machine Learning webszolgáltatás ehhez a megoldáshoz fogja használni.  

A háromrészes oktatóanyag megkezdése nyilvánosan elérhető hitelkockázati adatokkal.  Fejlesztés és betanítunk egy prediktív modellt.  Végül, a modell üzembe webszolgáltatásként.

Az oktatóanyag ezen részében meg: 
 
> [!div class="checklist"]
> * A Machine Learning Studio-munkaterület létrehozása
> * Meglévő adatok feltöltése
> * Kísérlet létrehozása

Ez a kísérlet, használhatja [a 2. rész-modellek betanításához](tutorial-part2-credit-risk-train.md) , majd [telepítené őket a 3. rész](tutorial-part3-credit-risk-deploy.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy valamennyire tisztában a gépi tanulás fogalmaival és a Machine Learning Studiót legalább egyszer használta már. Az útmutató azonban nem feltételezi, hogy a fent említett területeken szakértő lenne.

Ha még nem használt **Azure Machine Learning Studio** előtt érdemes kezdeni a rövid útmutatóban [hozzon létre az első adatelemzési kísérlet az Azure Machine Learning Studio](create-experiment.md). Ez a rövid útmutató végigvezeti a Machine Learning Studio első alkalommal. Bemutatja az alapokat, azt, hogy hogyan húzhat be modulokat a kísérletbe és kapcsolhatja össze azokat, és hogyan futtathatja a kísérletet és tekintheti meg az eredményeket.


> [!TIP] 
> Ebben az oktatóanyagban a fejleszt kísérlet működő példányát is megtalálhatja a [Azure AI-katalógusban](https://gallery.azure.ai). Lépjen a **[oktatóanyag – kredit előrejelzése kockázati](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** kattintson **Megnyitás a Studióban** Ha szeretné letölteni a kísérlet a Machine Learning Studio munkaterületre.
> 


## <a name="create-a-machine-learning-studio-workspace"></a>A Machine Learning Studio-munkaterület létrehozása

A Machine Learning Studio használatához szüksége van egy Microsoft Azure Machine Learning Studio-munkaterület. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.  

Hozzon létre egy munkaterületet, lásd: [létrehozása és megosztása egy Azure Machine Learning Studio-munkaterület](create-workspace.md).

A munkaterület létrehozását követően nyissa meg a Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Ha egynél több munkaterületet, akkor válassza ki a munkaterületet az eszköztáron az ablak jobb felső sarkában.

![Válassza ki a munkaterületet a Studióban](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Ha a munkaterület tulajdonosa, megoszthatja a rendszeren dolgozik által meghívott mások a munkaterületen kísérletek. Ehhez a Machine Learning Studióban található a **beállítások** lapot. Egyszerűen a Microsoft-fiókjával vagy szervezeti fiók minden felhasználó számára.
> 
> Az a **beállítások** kattintson **felhasználók**, majd kattintson a **több felhasználó MEGHÍVÁSA** az ablak alján.
> 

## <a name="upload"></a>Meglévő adatok feltöltése

A hitelkockázat kiszámításához prediktív modellek fejlesztése, adatok, amelyek segítségével betanítására és tesztelésére a modell van szükség. A jelen oktatóanyag esetében a "UCI Statlog (német adatokból) Data Set" fogja használni a UC Irvine Machine Learning-adattárból. Található itt:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

A fájlt használni kívánt **german.data**. Töltse le a fájlt a merevlemez-meghajtóról.  

A **german.data** adatkészlet a 1000 elmúlt kérelmezők számára ajánlatbeli kreditek 20 változók sort tartalmaz. Ezeket a változókat 20 jelölik az adatkészlet bizonyos funkcióit (a *funkció vektor*), amely lehetővé teszi azonosító jellemzőinek minden kredit kérelmezőnek. Minden egyes sorban egy további oszlopot a kérelmező számított hitelkockázat, a 700 kérelmezők azonosította az eseményt alacsony hitelkockázat és 300 másodperc, a magas kockázatú jelöli.

A szolgáltatás vektor ezen adatok jellemzőit ismerteti, a UCI webhelyén. Ezeket az adatokat a pénzügyi adatokat, kreditelőzmények, állapota és személyes adatokat tartalmaz. Az egyes kérelmező bináris minősítést lett adott, amely azt jelzi, hogy azok egy alacsony és magas kockázati kreditet. 

Ezek az adatok betanítunk egy prediktív elemzési modellt fogja használni. Ha elkészült, a modell fogadja el a szolgáltatás vektor egy új egyedi, és hogy ő-e a kis vagy nagy hitelkockázat előrejelzése képesnek kell lennie.  

Íme egy érdekes twist.

A UCI webhelyen az adatkészlet leírása említi, amit Ha Ön egy személy hitelkockázatáról misclassify költségei.
Ha a modell magas hitelkockázat előrejelzi a személy ténylegesen alacsony hitelkockázat, a modell által végrehajtott egy téves besorolás.

A fordított téves besorolás ötször költségesebb, a pénzügyi intézmény, de: Ha a modell egy alacsony hitelkockázat előrejelzi a személy ténylegesen magas hitelkockázat.

Így szeretné, hogy a költség, ez utóbbi típusú téves besorolás ötször magasabb, mint a más módon misclassifying modellje betanításához.

A legegyszerűbb megoldás ehhez a kísérlet során a modell tanítása esetén (ötször) tételekhez valaki nagy hitelkockázat-képviselő másolásával. 

Ezután ha a modell misclassifies valaki alacsony hitelkockázat, ha ténylegesen magas kockázatú, a modell elvégzi, hogy ugyanazon téves besorolás ötször, egyszer minden ismétlődő. Ez növeli a hiba a betanítási eredmények költsége.


### <a name="convert-the-dataset-format"></a>Az adatkészlet formátum konvertálása

Az eredeti adathalmazból egy üres tagolt formátumot használja. A Machine Learning Studio jobban működik egy vesszővel tagolt (CSV) fájl, így fog konvertálja a az adatkészlet a szóközök cseréje vesszővel válassza el egymástól.  

Számos módon konvertálni az adatokat. Egyik módja a következő Windows PowerShell-parancs használatával:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Egy másik módja a Unix csökkentésének parancs használatával:  

    sed 's/ /,/g' german.data > german.csv  

Mindkét esetben egy nevű fájlban lévő adatokat egy vesszővel tagolt verziója létrehozott **german.csv** , amelyet használhat a kísérlet során.

### <a name="upload-the-dataset-to-machine-learning-studio"></a>Töltse fel az adatkészlet Machine Learning Studióban

Miután az adatok CSV formátumban lett konvertálva, fel kell töltenie, Machine Learning studióba. 

1. Nyissa meg a Machine Learning Studio kezdőlapját ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Kattintson a menü ![menü](./media/tutorial-part1-credit-risk/menu.png) az ablak bal felső sarkában kattintson **Azure Machine Learning**, jelölje be **Studio**, és jelentkezzen be.

3. Kattintson a **+ új** az ablak alján.

4. Válassza ki **ADATKÉSZLET**.

5. Válassza ki **helyi FÁJLBÓL**.

    ![Adatkészlet hozzáadása egy helyi fájlból](./media/tutorial-part1-credit-risk/add-dataset.png)

6. Az a **töltse fel egy új adatkészlet** párbeszédpanelen kattintson a Tallózás gombra, majd keresse meg a **german.csv** létrehozott fájlt.

7. Adja meg az adatkészlet nevét. Ebben az oktatóanyagban meghívására "UCI német hitelkártya adatok".

8. Adattípus kiválasztása **fejléc nélküli általános CSV-fájl (. nh.csv)**.

9. Ha szeretné, adjon meg egy leírást.

10. Kattintson a **OK** pipa jelre.  

    ![Töltse fel az adatkészlet](./media/tutorial-part1-credit-risk/upload-dataset.png)

Ez feltölti az adatokat az adatkészlet a modul, amely egy kísérletet is használhatja.

Adatkészleteket, amelyek az Ön által feltöltött Studio kattintva kezelheti a **ADATKÉSZLETEK** fülre, és a Studio ablak bal oldalán.

![Adatkészletek kezelése](./media/tutorial-part1-credit-risk/dataset-list.png)

Más típusú adatok importálása egy kísérlet kapcsolatos további információkért lásd: [a betanítási adatok importálása az Azure Machine Learning studióba](import-data.md).

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Ebben az oktatóanyagban a következő lépés, ha kísérlet a Machine Learning Studióban, amely a feltöltött adatkészletet használja.  

1. A Studióban, kattintson a **+ új** az ablak alján.
1. Válassza ki **kísérlet**, majd válassza az "Üres kísérlet". 

    ![Új kísérlet létrehozása](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Válassza ki a kísérlet alapértelmezett nevét a vászon tetején, és módosítsa valami értelmesebbre.

    ![Kísérlet átnevezése](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Meg kell adni egy célszerű **összegzése** és **leírás** a kísérlet a a **tulajdonságok** ablaktáblán. Ezek a tulajdonságok lehetővé teszik, dokumentálni a kísérletet, így bárki, aki később úgy tűnik, hogy tisztában van a célok és módszertanokon az esélye.
   > 
   > ![Kísérlet tulajdonságai](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. A modulpaletta bal oldalán a kísérletvászonra, bontsa ki **mentett adatkészletek**.
1. Keresse meg az adatkészlet alapján létrehozott **saját adatkészletek** , és húzza a vászonra. Az adatkészlet keresse meg a név megadásával a **keresési** a paletta fölött.  

    ![Adja hozzá az adathalmazt a kísérletvászonra](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Az adatok előkészítése

Az első 100 sor az adatok és a teljes adatkészlet néhány statisztikai adatot tekintheti meg: Kattintson a kimeneti portra, az adatkészlet (a kis kör alul), és válassza ki **Visualize**.  

Az adatfájl nem kapott oszlopfejléceket, mert Studio biztosított általános fejlécek (Col1, Col2, *stb.*). Jó fejlécek nem alapvető fontosságú a modell létrehozásához, de azok egyszerűbb legyen az adatok a kísérletben. Is ha végül tesz közzé ezt a modellt egy web service-ben, a fejlécek segít azonosítani a felhasználónak a szolgáltatás az oszlopok.  

Használatával oszlopfejléceket is hozzáadhat a [metaadatainak szerkesztése] [ edit-metadata] modul.

Használja a [metaadatainak szerkesztése] [ edit-metadata] modult, módosítsa az adatkészlet társított metaadatokat. Ebben az esetben használhatja azt oszlopfejlécek további rövid nevét. 

Használandó [metaadatainak szerkesztése][edit-metadata], először megadott oszlopok módosítása (ebben az esetben az összes.) Ezután adja meg a műveletet úgy, hogy hajtható végre ezek az oszlopok (ebben az esetben az oszlopfejlécek módosítása.)

1. A modulpaletta, írja be "metaadatok" a **keresési** mezőbe. A [metaadatainak szerkesztése] [ edit-metadata] modul listájában jelenik meg.

1. Kattintással és húzással vigye a [metaadatainak szerkesztése] [ edit-metadata] modult a vászonra és engedje el a korábban hozzáadott adatkészlet alatt.

1. Az adatkészlet csatlakoztatása a [metaadatainak szerkesztése][edit-metadata]: kattintson a kimeneti portra, az adatkészlet (a kis kör alján, az adatkészlet), és húzza át a bemeneti portjával [metaadatainak szerkesztése] [ edit-metadata] (a felső részén a modul a kis kör), majd engedje el az egér gombját. Az adatkészlet és a modul is csatlakoztatott, még akkor is, ha áthelyezi vagy a vásznon.
 
    A kísérlet kell kinéznie ehhez hasonló:  

    ![Adding Edit Metadata](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    A vörös felkiáltójelből azt jelzi, hogy ez a modul tulajdonságai még nem állított be. Azt fogjuk tenni.

    > [!TIP]
    > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. Ebben az esetben kattintson duplán a [metaadatainak szerkesztése] [ edit-metadata] modul, és írja be az "Add oszlopok fejléceit" Megjegyzés. Kattintson bármely más gombra kattintva zárja be a szövegmezőbe a vásznon. A megjegyzés megjelenítéséhez kattintson a lefelé mutató nyílra az modulban.
    > 
    > ![Megjegyzés hozzáadása a metaadatok modul szerkesztése](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Válassza ki [metaadatainak szerkesztése][edit-metadata], majd a a **tulajdonságok** panelen a vászontól jobbra kattintson **Oszlopválasztás indítása**.

1. Az a **oszlopok kijelölése** párbeszédpanelen válassza ki az összes sort **elérhető oszlopok** kattintson > áthelyezni őket **kijelölt oszlopok**.
   A párbeszédpanel kell kinéznie:

   ![Oszlopválasztó az összes kijelölt oszlop.](./media/tutorial-part1-credit-risk/select-columns.png)


1. Kattintson a **OK** pipa jelre.

1. Térjen vissza a **tulajdonságok** ablaktáblán keresse meg a **új oszlopnevek** paraméter. Ebben a mezőben adja meg az adatkészlet, egymástól elválasztva és az oszlopok sorrendjét a 21-én oszlopok neveinek listáját. Az adatkészlet-dokumentáció a UCI webhelyen is kaphat oszlopainak neveit, vagy a kényelem másolja és illessze be az alábbi lista:  

  ```   
  Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
  ```

  A Tulajdonságok panelen így néz ki:

  ![A Szerkesztés metaadatokat tulajdonságai](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

  > [!TIP]
  > Ha szeretné ellenőrizni az oszlopfejléceket, futtassa a kísérletet (kattintson **futtatása** a kísérletvászon alatt). Amikor lejáratot követően újrainduljon (egy zöld pipa jelenik meg a [metaadatainak szerkesztése][edit-metadata]), a kimeneti portjára, kattintson a [metaadatainak szerkesztése] [ edit-metadata] modul Válassza ki **Visualize**. Bármely modul kimenetét az adatokat a kísérlet állapotának megtekintéséhez megegyező módon tekintheti meg.
  > 
  > 

### <a name="create-training-and-test-datasets"></a>Képzés és az adatkészletek

Néhány adatot a modell betanítását és néhány teszteléséhez szükséges.
Így a kísérlet a következő lépésben, az adatkészlet felosztása két külön adatkészletet: egyet, és a egy vizsgálja, hogy az a modell betanításához.

Ehhez használja a [Split Data] [ split] modul.  

1. Keresse meg a [Split Data] [ split] modult, húzza a vászonra, és kösse össze a [metaadatainak szerkesztése] [ edit-metadata] modul.

1. Alapértelmezés szerint a megosztási arány a 0.5-ös és a **Randomized split** paraméter értéke. Azt jelenti, hogy az adatok véletlen felében kimeneti az egyik porton keresztül a [Split Data] [ split] modul, és a segítségével a másik fele. Beállíthatja, hogy ezeket a paramétereket, valamint a **véletlenszerű kezdőérték** paramétert, módosíthatja a felosztás betanítására és tesztelésére az adatok között. Ebben a példában hagyja őket-van.
   
   > [!TIP]
   > A tulajdonság **az első kimeneti adatkészletnél a sorok** meghatározza, hogy mekkora az adatok kimenetét a *bal oldali* kimeneti port. Például ha 0,7 beállította az arányt, 70 %-a az adatok akkor kimeneti a bal oldali port és a megfelelő port 30 %-os.  
   > 
   > 

1. Kattintson duplán a [Split Data] [ split] modul, és adja meg a megjegyzést, "képzési tesztelési adatok felosztása 50 %-a". 

Használhatja a kimeneteinek a [Split Data] [ split] modul azonban hasonló, de most válassza a bal oldali kimeneti használatára, mert a betanítási adatok, és jobb kimeneti például tesztelési adatokat.  

Említetteknek megfelelően az [előző lépésben](tutorial-part1-credit-risk.md#upload), egy alacsony, magas hitelkockázat misclassifying költsége ötször magasabb, mint egy nagy, alacsony hitelkockázat misclassifying költsége. Ez a fiók, hozzon létre egy új adatkészlet, amely tükrözi a költségek függvény. Az új adatkészletre, az egyes magas kockázatú példa replikálja a rendszer ötször, míg egyes alacsony kockázat a példában a rendszer nem replikálja.   

A replikáció teheti az R-kód használatával:  

1. Keresse meg és húzza a [R-szkript végrehajtása] [ execute-r-script] modul a kísérlet vászonra. 

1. A bal oldali kimeneti portjára, csatlakozzon a [Split Data] [ split] modul az első bemeneti porthoz ("Dataset1"), a [R-szkript végrehajtása] [ execute-r-script] modul.

1. Kattintson duplán a [R-szkript végrehajtása] [ execute-r-script] modul, és adja meg a megjegyzést, "Költségeket az adatelosztást Set".

1. Az a **tulajdonságok** panelen, törölje az alapértelmezett szöveget a **R-szkript** paraméter, és adja meg ezt a parancsfájlt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R-szkript az R-szkript végrehajtása modul](./media/tutorial-part1-credit-risk/execute-r-script.png)

Kell tennie minden kimenetének azonos replikációs művelet a [Split Data] [ split] modult, hogy a képzés és tesztelési adatokat az azonos költség illesztését. Ennek legegyszerűbb módja a másolásával a [R-szkript végrehajtása] [ execute-r-script] frissen létrehozott modul, és csatlakozik a másik kimeneti portjára, a [Split Data] [ split] modul.

1. Kattintson a jobb gombbal a [R-szkript végrehajtása] [ execute-r-script] modul, és válassza ki **másolási**.

1. Kattintson a jobb gombbal a kísérletvászonra, és válassza ki **beillesztési**.

1. Az új modulba húzza a helyére, és a jobb oldali kimeneti portjára, majd csatlakozzon a [Split Data] [ split] első bemeneti portjával Ez új modul [R-szkript végrehajtása] [ execute-r-script] modul. 

1. Kattintson a vászon alján **futtatása**. 

> [!TIP]
> Az R-szkript végrehajtása modul másolatát tartalmazza ugyanazt a parancsprogramot, mint az eredeti modul. Másolja és illessze be egy modult a vásznon, a Másolás megőrzi az eredeti összes tulajdonságát.  
> 
>

A kísérletet most alábbihoz hasonlóan jelenik meg:

![Split modul és R-szkriptek hozzáadása](./media/tutorial-part1-credit-risk/experiment.png)

A kísérletek R-parancsfájlokkal további információkért lásd: [kísérlet kiterjesztése az R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban elvégezte ezeket a lépéseket: 
 
> [!div class="checklist"]
> * A Machine Learning Studio-munkaterület létrehozása
> * Meglévő adatok feltöltése a munkaterületre
> * Kísérlet létrehozása

Most már készen áll az adatok a modellek betanítása és kiértékelése.

> [!div class="nextstepaction"]
> [Az oktatóanyag 2 – Train modellek és kiértékelése](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/