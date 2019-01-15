---
Cím: "2. lépés: Adatok feltöltése egy Machine Learning Studio-kísérletet "titleSuffix: Azure Machine Learning Studio description: "A Develop egy prediktív elemzési megoldást az útmutató 2. lépés: Feltöltés nyilvános adatok tárolása az Azure Machine Learning studióba. "
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.component: studio ms.topic: cikk

author: garyericson ms.author: garye ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro ms.date: 03/23/2017
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-studio-experiment"></a>Az útmutató 2. lépés: Meglévő adatok feltöltése egy Azure Machine Learning Studio-kísérlet
Ez az útmutató második lépése a [az Azure Machine Learning prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. **Meglévő adatok feltöltése**
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
A hitelkockázat kiszámításához prediktív modellek fejlesztése, betanítására és tesztelésére a modell segítségével adatokat kell. Ebben a bemutatóban a "UCI Statlog (német adatokból) Data Set" fogjuk használni a UC Irvine Machine Learning-adattárból. Található itt:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

A fájlt használjuk **german.data**. Töltse le a fájlt a merevlemez-meghajtóról.  

A **german.data** adatkészlet a 1000 elmúlt kérelmezők számára ajánlatbeli kreditek 20 változók sort tartalmaz. Ezeket a változókat 20 jelölik az adatkészlet bizonyos funkcióit (a *funkció vektor*), amely lehetővé teszi azonosító jellemzőinek minden kredit kérelmezőnek. Minden egyes sorban egy további oszlopot a kérelmező számított hitelkockázat, a 700 kérelmezők azonosította az eseményt alacsony hitelkockázat és 300 másodperc, a magas kockázatú jelöli.

A szolgáltatás vektor ezen adatok jellemzőit ismerteti, a UCI webhelyén. Ez magában foglalja a pénzügyi adatokat, kreditelőzmények, állapota és személyes adatokat. Az egyes kérelmező bináris minősítést lett adott, amely azt jelzi, hogy azok egy alacsony és magas kockázati kreditet. 

Betanítunk egy prediktív elemzési modellt használjuk ezeket az adatokat. Ha végzett, a modell fogadja el a szolgáltatás vektor egy új egyedi, és hogy ő-e a kis vagy nagy hitelkockázat előrejelzése képesnek kell lennie.  

Íme egy érdekes twist. A UCI webhelyen az adatkészlet leírása említi, amit Ha azt egy személy hitelkockázatáról misclassify költségei.
Ha a modell magas hitelkockázat előrejelzi a személy ténylegesen alacsony hitelkockázat, a modell által végrehajtott egy téves besorolás.
A fordított téves besorolás ötször költségesebb, a pénzügyi intézmény, de: Ha a modell egy alacsony hitelkockázat előrejelzi a személy ténylegesen magas hitelkockázat.

Ezért szeretnénk a modell betanítását, úgy, hogy a költség, ez utóbbi típusú téves besorolás ötször magasabb, mint a más módon misclassifying.
A legegyszerűbb megoldás ehhez a kísérletben a modell tanítása esetén (ötször) tételekhez valaki nagy hitelkockázat-képviselő másolásával. Ezután ha a modell misclassifies valaki alacsony hitelkockázat, ha ténylegesen magas kockázatú, a modell elvégzi, hogy ugyanazon téves besorolás ötször, egyszer minden ismétlődő. Ez növeli a hiba a betanítási eredmények költsége.


## <a name="convert-the-dataset-format"></a>Az adatkészlet formátum konvertálása
Az eredeti adathalmazból egy üres tagolt formátumot használja. A Machine Learning Studio jobban működik egy vesszővel tagolt (CSV) fájl, így azt fogjuk konvertálja az adatkészlet és cserélje le a tárolóhelyek vesszővel válassza el egymástól.  

Számos módon konvertálni az adatokat. Egyik módja a következő Windows PowerShell-parancs használatával:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Egy másik módja a Unix csökkentésének parancs használatával:  

    sed 's/ /,/g' german.data > german.csv  

Mindkét esetben hoztunk létre egy nevű fájlban lévő adatokat egy vesszővel tagolt verziója **german.csv** , használhatjuk a kísérletben.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Töltse fel az adatkészlet Machine Learning Studióban
Miután az adatok CSV formátumban lett konvertálva, töltse fel a Machine Learning studióba kell. 

1. Nyissa meg a Machine Learning Studio kezdőlapját ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Kattintson a menü ![menü][1] az ablak bal felső sarkában kattintson **Azure Machine Learning**, jelölje be **Studio**, és jelentkezzen be.

3. Kattintson a **+ új** az ablak alján.

4. Válassza ki **ADATKÉSZLET**.

5. Válassza ki **helyi FÁJLBÓL**.

    ![Adatkészlet hozzáadása egy helyi fájlból][2]

6. Az a **töltse fel egy új adatkészlet** párbeszédpanelen kattintson a **Tallózás** , és keresse meg a **german.csv** létrehozott fájlt.

7. Adja meg az adatkészlet nevét. Ebben a bemutatóban meghívására "UCI német hitelkártya adatok".

8. Adattípus kiválasztása **fejléc nélküli általános CSV-fájl (. nh.csv)**.

9. Ha szeretné, adjon meg egy leírást.

10. Kattintson a **OK** pipa jelre.  

    ![Töltse fel az adatkészlet][3]

Ez feltölti az adatokat az adatkészlet a modul, amely használhatók, egy kísérlet során.

Adatkészleteket, amelyek az Ön által feltöltött Studio kattintva kezelheti a **ADATKÉSZLETEK** fülre, és a Studio ablak bal oldalán.

![Adatkészletek kezelése][4]

Más típusú adatok importálása egy kísérlet kapcsolatos további információkért lásd: [a betanítási adatok importálása az Azure Machine Learning studióba](import-data.md).

**Következő: [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png
