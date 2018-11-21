---
title: '3. lépés: Hozzon létre egy új Machine Learning-kísérlet |} A Microsoft Docs'
description: 'A fejlesztés egy prediktív megoldás bemutatója 3. lépés: új betanítási kísérlet létrehozása az Azure Machine Learning Studióban.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 47126006c96b1bfefe90b030805aac1d6af6c0db
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262245"
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Az útmutató 3. lépése: Új Azure Machine Learning-kísérlet létrehozása
Ez a forgatókönyv harmadik lépésében a [az Azure Machine Learning prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. **Új kísérlet létrehozása**
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
Ez az útmutató a következő lépés, ha kísérlet a Machine Learning Studióban, amely az adatkészlet, hogy a feltöltött használja.  

1. A Studióban, kattintson a **+ új** az ablak alján.
2. Válassza ki **kísérlet**, majd válassza az "Üres kísérlet". 

    ![Új kísérlet létrehozása][0]

2. Válassza ki a kísérlet alapértelmezett nevét a vászon tetején, és módosítsa valami értelmesebbre.

    ![Kísérlet átnevezése][5]
   
   > [!TIP]
   > Meg kell adni egy célszerű **összegzése** és **leírás** a kísérlet a a **tulajdonságok** ablaktáblán. Ezek a tulajdonságok lehetővé teszik, dokumentálni a kísérletet, így bárki, aki később úgy tűnik, hogy tisztában van a célok és módszertanokon az esélye.
   > 
   > ![Kísérlet tulajdonságai][6]
   > 
3. A modulpaletta bal oldalán a kísérletvászonra, bontsa ki **mentett adatkészletek**.
4. Keresse meg az adatkészlet alapján létrehozott **saját adatkészletek** , és húzza a vászonra. Az adatkészlet keresse meg a név megadásával a **keresési** a paletta fölött.  

    ![Adja hozzá az adathalmazt a kísérletvászonra][7]

## <a name="prepare-the-data"></a>Az adatok előkészítése
Az első 100 sor az adatok és a teljes adatkészlet néhány statisztikai adatot is megtekintheti: kattintson a kimeneti portra, az adatkészlet (a kis kör alul), és válassza ki **Visualize**.  

Az adatfájl nem kapott oszlopfejléceket, mert Studio biztosított általános fejlécek (Col1, Col2, *stb.*). Jó fejlécek nem alapvető fontosságú a modell létrehozásához, de azok egyszerűbb legyen az adatok a kísérletben. Is a web service-ben a ebben a modellben végül közzétételekor a fejlécek segít azonosítani az oszlopokat a felhasználónak a szolgáltatás.  

Oszlopfejlécek segítségével hozzáadhatja a [metaadatainak szerkesztése] [ edit-metadata] modul.
Használja a [metaadatainak szerkesztése] [ edit-metadata] modult, módosítsa az adatkészlet társított metaadatokat. Ebben az esetben azt biztosítása érdekében használjuk az oszlopfejlécek további rövid neve. 

Használandó [metaadatainak szerkesztése][edit-metadata], először megadott oszlopok módosítása (ebben az esetben az összes.) Ezután adja meg a műveletet úgy, hogy hajtható végre ezek az oszlopok (ebben az esetben az oszlopfejlécek módosítása.)

1. A modulpaletta, írja be "metaadatok" a **keresési** mezőbe. A [metaadatainak szerkesztése] [ edit-metadata] modul listájában jelenik meg.

2. Kattintással és húzással vigye a [metaadatainak szerkesztése] [ edit-metadata] modult a vászonra és engedje el a korábban hozzáadott adatkészlet alatt.

3. Az adatkészlet csatlakoztatása a [metaadatainak szerkesztése][edit-metadata]: kattintson a kimeneti portra, az adatkészlet (a kis kör alján, az adatkészlet), és húzza át a bemeneti portjával [metaadatainak szerkesztése] [ edit-metadata] (a felső részén a modul a kis kör), majd engedje el az egér gombját. Az adatkészlet és a modul is csatlakoztatott, még akkor is, ha áthelyezi vagy a vásznon.
   
   A kísérlet kell kinéznie ehhez hasonló:  
   
   ![Szerkesztés metaadatok hozzáadása][1]
   
   A vörös felkiáltójelből azt jelzi, hogy azt még nem állítsa be a tulajdonságokat a modul még. Azt fogjuk tenni.
   
   > [!TIP]
   > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. Ebben az esetben kattintson duplán a [metaadatainak szerkesztése] [ edit-metadata] modul, és írja be az "Add oszlopok fejléceit" Megjegyzés. Kattintson bármely más gombra kattintva zárja be a szövegmezőbe a vásznon. A megjegyzés megjelenítéséhez kattintson a lefelé mutató nyílra az modulban.
   > 
   > ![Megjegyzés hozzáadása a metaadatok modul szerkesztése][8]
   > 
4. Válassza ki [metaadatainak szerkesztése][edit-metadata], majd a a **tulajdonságok** panelen a vászontól jobbra kattintson **Oszlopválasztás indítása**.

5. Az a **oszlopok kijelölése** párbeszédpanelen válassza ki az összes sort **elérhető oszlopok** kattintson > áthelyezni őket **kijelölt oszlopok**.
   A párbeszédpanel kell kinéznie:

   ![Oszlopválasztó az összes kijelölt oszlop.][2]

6. Kattintson a **OK** pipa jelre.

7. Térjen vissza a **tulajdonságok** ablaktáblán keresse meg a **új oszlopnevek** paraméter. Ebben a mezőben adja meg az adatkészlet, egymástól elválasztva és az oszlopok sorrendjét a 21-én oszlopok neveinek listáját. Az adatkészlet-dokumentáció a UCI webhelyen is kaphat oszlopainak neveit, vagy a kényelem másolja és illessze be az alábbi lista:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   A Tulajdonságok panelen így néz ki:
   
   ![A Szerkesztés metaadatokat tulajdonságai][3]

> [!TIP]
> Ha szeretné ellenőrizni az oszlopfejléceket, futtassa a kísérletet (kattintson **futtatása** a kísérletvászon alatt). Amikor lejáratot követően újrainduljon (egy zöld pipa jelenik meg a [metaadatainak szerkesztése][edit-metadata]), a kimeneti portjára, kattintson a [metaadatainak szerkesztése] [ edit-metadata] modul Válassza ki **Visualize**. Bármely modul kimenetét az adatokat a kísérlet állapotának megtekintéséhez megegyező módon tekintheti meg.
> 
> 

## <a name="create-training-and-test-datasets"></a>Képzés és az adatkészletek
Néhány adatot a modell betanítását és ki egyes van szükségünk.
Így a következő lépésben a kísérlet, hogy az adatkészlet felosztása két külön adatkészletet: egyet, és a egy vizsgálja, hogy az a modell betanításához.

Ehhez használjuk a [Split Data] [ split] modul.  

1. Keresse meg a [Split Data] [ split] modult, húzza a vászonra, és kösse össze a [metaadatainak szerkesztése] [ edit-metadata] modul.

2. Alapértelmezés szerint a megosztási arány a 0.5-ös és a **Randomized split** paraméter értéke. Azt jelenti, hogy az adatok véletlen felében kimeneti az egyik porton keresztül a [Split Data] [ split] modul, és a segítségével a másik fele. Beállíthatja, hogy ezeket a paramétereket, valamint a **véletlenszerű kezdőérték** paramétert, módosíthatja a felosztás betanítására és tesztelésére az adatok között. Ebben a példában hagyjuk azokat-van.
   
   > [!TIP]
   > A tulajdonság **az első kimeneti adatkészletnél a sorok** meghatározza, hogy mekkora az adatok kimenetét a *bal oldali* kimeneti port. Például ha 0,7 beállította az arányt, 70 %-a az adatok akkor kimeneti a bal oldali port és a megfelelő port 30 %-os.  
   > 
   > 

3. Kattintson duplán a [Split Data] [ split] modul, és adja meg a megjegyzést, "képzési tesztelési adatok felosztása 50 %-a". 

Használhatjuk a kimeneteinek a [Split Data] [ split] modul azonban azt hasonló, de most válassza a bal oldali kimeneti használatára, mert a betanítási adatok, és jobb kimeneti például tesztelési adatokat.  

Említetteknek megfelelően az [előző lépésben](walkthrough-2-upload-data.md), egy alacsony, magas hitelkockázat misclassifying költsége ötször magasabb, mint egy nagy, alacsony hitelkockázat misclassifying költsége. Ez a fiók, az általunk létrehozott egy új adatkészlet, amely tükrözi a költségek függvény. Az új adatkészletre, az egyes magas kockázatú példa replikálja a rendszer ötször, míg egyes alacsony kockázat a példában a rendszer nem replikálja.   

A replikálás elvégezhető az R-kód használatával:  

1. Keresse meg és húzza a [R-szkript végrehajtása] [ execute-r-script] modul a kísérlet vászonra. 

2. A bal oldali kimeneti portjára, csatlakozzon a [Split Data] [ split] modul az első bemeneti porthoz ("Dataset1"), a [R-szkript végrehajtása] [ execute-r-script] modul.

3. Kattintson duplán a [R-szkript végrehajtása] [ execute-r-script] modul, és adja meg a megjegyzést, "Költségeket az adatelosztást Set".

4. Az a **tulajdonságok** panelen, törölje az alapértelmezett szöveget a **R-szkript** paraméter, és adja meg ezt a parancsfájlt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R-szkript az R-szkript végrehajtása modul][9]

Az egyes kimenetének azonos replikációs művelethez szükséges a [Split Data] [ split] modul, hogy a képzés és tesztelési adatokat az azonos költség illesztését. Ennek legegyszerűbb módja a másolásával a [R-szkript végrehajtása] [ execute-r-script] modul épp most módosította, és csatlakozik a másik kimeneti portjára, a [Split Data] [ split] modul.

1. Kattintson a jobb gombbal a [R-szkript végrehajtása] [ execute-r-script] modul, és válassza ki **másolási**.

2. Kattintson a jobb gombbal a kísérletvászonra, és válassza ki **beillesztési**.

3. Az új modulba húzza a helyére, és a jobb oldali kimeneti portjára, majd csatlakozzon a [Split Data] [ split] első bemeneti portjával Ez új modul [R-szkript végrehajtása] [ execute-r-script] modul. 

4. Kattintson a vászon alján **futtatása**. 

> [!TIP]
> Az R-szkript végrehajtása modul másolatát tartalmazza ugyanazt a parancsprogramot, mint az eredeti modul. Másolja és illessze be egy modult a vásznon, a Másolás megőrzi az eredeti összes tulajdonságát.  
> 
> 

A kísérletet most alábbihoz hasonlóan jelenik meg:

![Split modul és R-szkriptek hozzáadása][4]

A kísérletek R-parancsfájlokkal további információkért lásd: [kísérlet kiterjesztése az R](extend-your-experiment-with-r.md).

**A következő: [Train a modellek és kiértékelése](walkthrough-4-train-and-evaluate-models.md)**

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
