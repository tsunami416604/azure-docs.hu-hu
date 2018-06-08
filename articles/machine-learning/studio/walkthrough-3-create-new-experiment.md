---
title: 3. lépés:, Hozzon létre egy új gépi tanulási kísérlet |} Microsoft Docs
description: 'A prediktív megoldás bemutatóért Develop 3. lépés: az Azure Machine Learning Studióban hozzon létre egy új tanítási kísérletet.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 95000f9fb57b95bf1edcda9abfba3668b5f5b523
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835739"
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Az útmutató 3. lépése: Új Azure Machine Learning-kísérlet létrehozása
Ez a forgatókönyv harmadik lépése az [az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. **Új kísérlet létrehozása**
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
A forgatókönyv a következő lépés, ha a kísérlet a Machine Learning Studióban az adatkészlet jelenleg feltöltött használó.  

1. A Studióban kattintson **+ új** az ablak alján.
2. Válassza ki **kísérlet**, majd válassza az "Üres kísérlet". 

    ![Új kísérlet létrehozása][0]

2. Válassza ki a kísérlet alapértelmezett nevét a vászon tetején, és módosítsa valami értelmesebbre.

    ![Nevezze át a kísérlet][5]
   
   > [!TIP]
   > Tanácsos töltse ki **összegzés** és **leírás** a kísérleti fázisú funkciókat a a **tulajdonságok** ablaktáblán. Ezeket a tulajdonságokat arra, hogy a dokumentum a kísérletet, hogy bárki, aki ellenőrzi, hogy az azt később megértse a célok és módszert biztosítanak.
   > 
   > ![Kísérlet tulajdonságai][6]
   > 
3. Bontsa ki a modulpalettán bal oldalán a kísérletvászonra, **mentett adatkészletek**.
4. Az adatkészlet alapján létrehozott található **saját adatkészletek** és a vászonra húzva. A név beírásával is tájékozódhat az adatkészletet a **keresési** a paletta fölött.  

    ![Az adatkészlet hozzáadása a kísérlet][7]

## <a name="prepare-the-data"></a>Adatok előkészítése
Megtekintheti az első 100 sor az adatok és néhány statisztikai adatok a teljes adatkészlet: kattintson a kimeneti portra, az adatkészlet (a kis kör a lap alján), és válassza ki **Visualize**.  

Az adatfájl nem kapott oszlopának fejlécére kattintva rendezhető, mert Studio általános fejlécére kattintva rendezhető nyújtott (Oszlop1, Col2, *stb*). Jó fejlécek nem alapvető fontosságú a modell létrehozása, de azok egyszerűbb legyen az adatokat a kísérletben. Is ha azt végül közzéteheti a egy webszolgáltatás-bővítmény, a fejlécére kattintva rendezhető azonosításához az oszlopok a felhasználónak a szolgáltatás.  

Azt is hozzáadhat oszlopának fejlécére kattintva rendezhető, használja a [szerkesztése metaadatok] [ edit-metadata] modul.
Használja a [szerkesztése metaadatok] [ edit-metadata] modul módosítása a DataSet adatkészlet társított metaadatokat. Ebben az esetben használjuk az oszlopfejlécek további rövid nevét. 

Használandó [szerkesztése metaadatok][edit-metadata], először (ebben az esetben az összes azokat.) a módosítandó oszlopok megadásához Ezt követően adja meg az ilyen oszlopokat (ebben az esetben az oszlopfejlécek módosítása.) kell elvégezni a műveletet

1. A modulpalettán, írja be az "metaadatok" a a **keresési** mezőbe. A [szerkesztése metaadatok] [ edit-metadata] modul listájában jelenik meg.

2. Kattintással és húzással vigye a [szerkesztése metaadatok] [ edit-metadata] modult a vászonra, és helyezze a korábban hozzáadott adatkészlet alatt.

3. A DataSet adatkészletben, hogy csatlakozzon a [metaadatok szerkesztése][edit-metadata]: kattintson a kimeneti portra, az adatkészlet (DataSet alján a kis kör), húzza a bemeneti portját a [szerkesztése metaadatok] [ edit-metadata] (a felső részén a modul a kis kör), az oszlopfejlécen. A DataSet adatkészlet és a modul csatlakoztatva tartani akkor is, ha Ön Navigálás vagy a vásznon.
   
   A kísérlet most hasonlóan kell kinéznie ezt:  
   
   ![Szerkesztés metaadatok hozzáadása][1]
   
   A piros felkiáltójel azt jelenti, hogy jelenleg még nem ez a modul tulajdonságainak még. Igazolnia kell végeznie, hogy a Tovább gombra.
   
   > [!TIP]
   > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. Ebben az esetben kattintson duplán a [szerkesztése metaadatok] [ edit-metadata] modul és a Megjegyzés "Add oszlopának fejlécére kattintva rendezhető" típusú. Kattintson a bárhol máshol a vásznon, a szöveg bezárásához. A megjegyzés megjelenítéséhez kattintson a lefelé mutató nyílra a modul.
   > 
   > ![Megjegyzés hozzáadása a metaadatok modul szerkesztése][8]
   > 
4. Válassza ki [szerkesztése metaadatok][edit-metadata], majd a a **tulajdonságok** panelen a vászontól jobbra kattintson **Oszlopválasztás**.

5. Az a **egy oszlopot válasszon ki** párbeszédpanelen válassza ki az összes sort **elérhető oszlopok** kattintson > helyezze el őket a **kijelölt oszlopok**.
   A párbeszédpanel kell kinéznie:

   ![Az összes kijelölt oszlopok Oszlopválasztó][2]

6. Kattintson a **OK** pipára.

7. Vissza a **tulajdonságok** ablaktáblán keresse meg a **új oszlopnevek** paraméter. Ebben a mezőben adja meg az adatkészlet, egymástól vesszővel és az oszlopok sorrendjét 21 oszlopok neveinek listáját. Ezt úgy szerezheti be az oszlopok nevét a dataset dokumentációs a UCI webhelyen, vagy a kényelem másolja és illessze be az alábbi listában:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   A Tulajdonságok panelen így néz ki:
   
   ![A Szerkesztés metaadatok tulajdonságai][3]

> [!TIP]
> Ha szeretné ellenőrizni a oszlopának fejlécére kattintva rendezhető, futtassa a kísérletet (kattintson **futtatása** a kísérletvászon alatt). Amikor futása (zöld pipa jelenik meg [szerkesztése metaadatok][edit-metadata]), kattintson a kimeneti portra, a [metaadatok szerkesztése] [ edit-metadata] modul, és válassza ki **Visualize**. Bármely modul kimenete az adatok a kísérlet állapotának megtekintéséhez azonos módon tekintheti meg.
> 
> 

## <a name="create-training-and-test-datasets"></a>Képzés és az adatkészletek
Igazolnia kell, és az egyes tesztek bizonyos adatok a modell betanításához.
Így a következő lépésben a kísérlet, azt a dataset felosztása két külön adatkészletek: egy a képzési tekinthetők, és egy tesztelési azt.

Ehhez használjuk a [Split Data] [ split] modul.  

1. Keresés a [Split Data] [ split] modult a vászonra húzva, és kösse össze a [szerkesztése metaadatok] [ edit-metadata] modul.

2. Alapértelmezés szerint a megosztási arány érték 0,5 és a **Randomized vegyes** paraméter értéke. Ez azt jelenti, hogy az adatok véletlen fél kimeneti az egyik porton keresztül a [Split Data] [ split] modul, és az egyéb fele. Beállíthatja, hogy ezeket a paramétereket, valamint a **véletlenszerű kezdőérték** paraméter, módosíthatja a felosztás modell betanítására és tesztelésére adatok között. Ebben a példában azt hagyja azokat-van.
   
   > [!TIP]
   > A tulajdonság **az első kimeneti adatkészletnél a sorok** meghatározza, hogy mekkora az adatok kimenetét a *bal oldali* kimeneti port. Például 0,7 állítja be a készlethez, majd 70 %-át az adatokat esetén a bal oldali porton keresztül kimeneti – 30 % a jobb oldali porton keresztül.  
   > 
   > 

3. Kattintson duplán a [Split Data] [ split] modul, és adja meg a megjegyzést, "képzési/tesztelési adatok felosztása 50 %". 

A kimenetének is használhatók a [Split Data] [ split] modul azonban azt hasonló, de most használata mellett dönt a bal oldali kimeneti betanítási adatok, és a jobb oldali kimeneti, tesztelési adatokat.  

Ahogyan az a [előző lépésben](walkthrough-2-upload-data.md), alacsony, magas hitelkockázat misclassifying költsége ötször magasabb, mint egy alacsony hitelkockázat, nagy misclassifying költségét. Ez a fiók, azt létrehozni, amely tükrözi a költség függvény új adatkészlet. Új adatkészlet egyes magas kockázatú példa replikálja a rendszer ötször, amíg alacsony kockázat példákban a rendszer nem replikálja.   

A replikáció tehetünk ennek R-kód használatával:  

1. Keresse meg, és húzza a [R-parancsfájl végrehajtása] [ execute-r-script] modul a kísérlet vászonra. 

2. A bal oldali kimeneti portjára csatlakozzon a [Split Data] [ split] első bemeneti portját ("Dataset1") modult a [R-parancsfájl végrehajtása] [ execute-r-script] modul.

3. Kattintson duplán a [R-parancsfájl végrehajtása] [ execute-r-script] modul, és írja be a Megjegyzés "Set költség helyesbítése".

4. Az a **tulajdonságok** panelen, törölje az alapértelmezett szöveget a **R-parancsfájl** paraméter, és írja be ezt a parancsfájlt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Az R-parancsfájl végrehajtása modul R-parancsfájl][9]

Igazolnia kell a replikálási műveletet minden egyes kimenetéhez tegye a [Split Data] [ split] modul, hogy a képzés és tesztelési adatokat ugyanazon költség módosítása. Ennek a legegyszerűbb módja másolásával az [R-parancsfájl végrehajtása] [ execute-r-script] most végzett modul, és csatlakozik a másik kimeneti portját a [Split Data] [ split] modul.

1. Kattintson a jobb gombbal a [R-parancsfájl végrehajtása] [ execute-r-script] modul, és válassza ki **másolási**.

2. Kattintson a jobb gombbal a kísérletvászonra, és válassza ki **Beillesztés**.

3. Új modul húzzon egy helyen, és csatlakoztassa a jobb oldali kimeneti portját a [Split Data] [ split] modul az első bemeneti porthoz ezen új [R-parancsfájl végrehajtása] [ execute-r-script] modul. 

4. Kattintson a vászon alján **futtatása**. 

> [!TIP]
> Az R-parancsfájl végrehajtása modul másolatát tartalmazza ugyanazt a parancsfájlt, az eredeti modulként. Másolja és illessze be egy modult a vásznon, a másolat megőrzi az eredeti összes tulajdonságát.  
> 
> 

A kísérletben most már a következőhöz hasonló:

![Vegyes modul, és az R parancsfájlok hozzáadása][4]

Az R parancsfájlok használata a kísérleti további információkért lásd: [kiterjesztése az r kísérletbe](extend-your-experiment-with-r.md).

**Következő: [tanítási és a modell kiértékelése](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
