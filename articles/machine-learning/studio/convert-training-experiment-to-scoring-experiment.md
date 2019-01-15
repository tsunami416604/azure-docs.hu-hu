---
Cím: Készítse elő a modell a központi telepítési titleSuffix: Azure Machine Learning Studio description: Hogyan készíti elő a betanított modellt webszolgáltatásként üzembe helyezés a Machine Learning Studio betanítási kísérlet átalakítása prediktív kísérletté.
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.component: studio ms.topic: cikk

author: ericlicoding ms.author: amlstudiodocs ms.date: 03/28/2017
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Hogyan készülhet fel a modell üzembe helyezése az Azure Machine Learning Studióban

Az Azure Machine Learning Studio prediktív elemzési modellek fejlesztése majd kifejlesztéséhez, és egy Azure-alapú webes szolgáltatás telepítésével szükséges eszközöket biztosít.

Ehhez a kísérlet létrehozásának - nevű használhatja Studio egy *betanítási kísérlet* –, amelyen a vonat, pontszám, és szerkesztheti a modell. Ha elégedett, Felkészülés a modell üzembe helyezéséhez a betanítási kísérlet átalakítása egy *prediktív kísérletté* pontszám felhasználói adatok megfelelően van konfigurálva.

Ez a folyamat egy példa látható [forgatókönyv: Az Azure Machine Learning hitelkockázat értékelésére szolgáló prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md).

Ez a cikk részletes hogyan alakulnak át betanítási kísérlet be egy prediktív kísérletet, és a prediktív kísérletté telepítési módját mélyreható vesz igénybe. Megismerésével ezeket az adatokat, ismerje meg az üzembe helyezett modell hatékonyabbá tétele konfigurálása.



## <a name="overview"></a>Áttekintés 

A betanítási kísérlet átalakítása prediktív kísérletté folyamat három lépésből áll:

1. Cserélje le a gépi tanulási algoritmus-modulok a betanított modell.
2. Trim csak a szükséges ahhoz, hogy pontozási modulok a kísérletet. Betanítási kísérlet számos, amelyek szükségesek a képzés, de nincs rájuk szükség, ha a modell tanítása.
3. Határozza meg, hogy a modell a webes felhasználói adatokat fogad, és milyen adatokat vissza kell.

> [!TIP]
> A betanítási kísérlet során már az érintett a tanítási és pontozási a modell használatával saját adatai. De üzembe helyezését követően felhasználók küld-e az új adatokat a modellbe, és előrejelzési eredményeket adja vissza. Úgy ahogy a betanítási kísérlet átalakítása egy prediktív kísérletet, hogy előkészítse a telepítéshez, tartsa szem előtt a modell mások általi felhasználási módjáról.
> 
> 

## <a name="set-up-web-service-button"></a>Webszolgáltatás beállítása gomb
A kísérlet futtatása után (kattintson **futtatása** a kísérlet vászon alján), kattintson a **webszolgáltatás beállítása** gomb (válassza ki a **prediktív webszolgáltatás** lehetőséget). **Webszolgáltatás beállítása** hajt végre, az a betanítási kísérlet átalakítása prediktív kísérletté három lépést:

1. A betanított modell menti a **betanított modellek** a modulpaletta (a kísérleti vászonra a bal oldalon) szakaszában. Felülírja a gépi tanulási algoritmus és [tanítási modell] [ train-model] a mentett betanított modell rendelkező modulok.
2. Az eszköz elemzi a kísérlethez, és eltávolítja a modult, amely egyértelműen használták csak képzés, és már nincs szükség.
3. Beilleszt _bemenet webes_ és _kimeneti_ modulok az alapértelmezett helyek a kísérlet során (ezeket a modulokat elfogadása és a felhasználói adatokat adja vissza).

Ha például az alábbi kísérlet betanítja népszámlálási mintaadatokkal kétosztályos gyorsított döntési fa modell:

![Betanítási kísérlet][figure1]

A kísérletben a modulok alapvetően négy különböző funkciók hajtsa végre:

![A modul funkciók][figure2]

A betanítási kísérlet átalakítása prediktív kísérletté, ha ezeket a modulokat némelyike már nincs rá szükség, vagy már eltérő célra szolgál:

* **Adatok** – ebben a minta adatkészletben lévő adatok nem használatos a kiértékelés során – a felhasználó a webszolgáltatás meg fogja adni a pontozandó adatokat. Ez az adatkészlet, adattípusok, például metaadataiból azonban a betanított modell használják. Így kell tartani az adatkészletet a prediktív kísérletté úgy, hogy ezeket a metaadatokat.

* **Előkészítő** – attól függően, hogy a rendszer elküldi a pontozási, előfordulhat, hogy ezeket a modulokat, vagy nem lehet feldolgozni a bejövő adatok szükségesek a felhasználói adatok. A **webszolgáltatás beállítása** gomb nem touch ezek – el kell döntenie, hogyan szeretné kezelni őket.
  
    Például az ebben a példában a minta adatkészlet tehát hiányzó előfordulhat, hogy rendelkezik egy [Clean Missing Data] [ clean-missing-data] modul megtalálható azokkal kapcsolatban. A minta adatkészlet is oszlopokat, amelyeket nem szükségesek a modell betanításához. Ezért egy [Select Columns in Dataset] [ select-columns] modul megtalálható az adatfolyam felesleges oszlopok kizárása. Ha tudja, hogy az adatokat, hogy a rendszer elküldi a webszolgáltatással pontozó nem fog a hiányzó értékeket, majd eltávolíthatja a [Clean Missing Data] [ clean-missing-data] modul. Mivel azonban a [Select Columns in Dataset] [ select-columns] modul segítségével határozza meg az oszlopokat, amelyek a betanított modellt vár, a modulnak kell maradnia.

* **A vonat** – ezek a modulok használják a modell betanításához. Amikor rákattint **webszolgáltatás beállítása**, ezeket a modulokat, betanított modellt tartalmazó egyetlen modul helyén. Ez a modul a rendszer menti a **betanított modellek** a modulpaletta szakaszában.

* **Pontszám** – ebben a példában a [Split Data] [ split] modul szolgál a streamből ossza Tesztadatok és a betanítási adatok. Az a prediktív kísérletet, hogy nem tanítása válik, így [Split Data] [ split] távolíthatja el. Hasonlóképpen, a második [Score Model] [ score-model] modul és a [Evaluate Model] [ evaluate-model] modul a Tesztadatok eredményeinek így összehasonlítására szolgálnak Ezek a modulok nem szükségesek az a prediktív kísérletet. A fennmaradó [Score Model] [ score-model] modult, azonban szükség van a webszolgáltatással pontszám eredményt adja vissza.

Példánkban kinézetét kattintás után a következő **webszolgáltatás beállítása**:

![A konvertált prediktív kísérletté][figure3]

Által végzett munka **webszolgáltatás beállítása** elegendő lehet, hogy készítse elő a kísérlethez webszolgáltatásként üzembe helyezni. Azonban érdemes néhány adott kísérletét, további munkát.

### <a name="adjust-input-and-output-modules"></a>Módosítsa a bemeneti és kimeneti modulok
A betanítási kísérlet során használt betanítási adatok egy készletét, és ezután volt a feldolgozást egy képernyő, amely a machine learning algoritmus szükséges az adatok beolvasásához. Ha az adatokat kaphat a webes szolgáltatáson keresztül nem kell ezt a feldolgozást, elkerülheti,: kimenete csatlakoztatása a **webszolgáltatás bemeneti modul** , egy másik modul a kísérletben. A felhasználó adatait most érkezik a modellben, ezen a helyen.

Például alapértelmezés szerint **webszolgáltatás beállítása** helyezi a **bemenet webes** modul felső részén az adatfolyama, a fenti ábrán látható módon. Azt manuálisan is elhelyezheti, de a **bemenet webes** az adatfeldolgozás modulok korábbi:

![A web service bemeneti áthelyezése][figure4]

A bemeneti adatokat a web service keresztül közvetlenül a Score Model-modul mostantól továbbítja a bármely előfeldolgozás nélkül.

Hasonlóképpen, alapértelmezés szerint **webszolgáltatás beállítása** a szolgáltatás kimeneti modul alján, az adatfolyam-be illeszti be. Ebben a példában a web service vissza fogja küldeni a felhasználónak a kimenetét a [Score Model] [ score-model] modult, amely tartalmazza a bemeneti adatok teljes vektor- és a pontozási eredményeinek.
Azonban, ha valami más vissza inkább, ezután adhatja csak hozzá további modulok előtt a **webes szolgáltatás kimeneti** modul. 

Például a csak a pontozási eredményeinek és a bemeneti adatok nem a teljes vektor visszaadása, adjon hozzá egy [Select Columns in Dataset] [ select-columns] modul összes oszlopot, kivéve a pontozási eredményeinek kizárása. Ezután lépjen a **webes szolgáltatás kimeneti** modul kimenete a [Select Columns in Dataset] [ select-columns] modul. A kísérlet így néz ki:

![A web service kimeneti áthelyezése][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Adja hozzá, vagy távolítsa el a további adatokat feldolgozó modulok
Ha a kísérlet során, hogy ismeri a kiértékelés során nem szükséges további modulok, ezek távolíthatja el. Például mert egekbe szökött a **bemenet webes** modul után az adatok feldolgozása modulok pontra, törölhetjük az [Clean Missing Data] [ clean-missing-data] modulnak a a prediktív kísérletet.

A prediktív kísérletet most néz ki:

![További modul eltávolítása][figure6]


### <a name="add-optional-web-service-parameters"></a>Nem kötelező webszolgáltatás-paraméterek hozzáadása
Bizonyos esetekben érdemes, hogy a felhasználó a webszolgáltatás-modulok működésének módosításához, ha hozzáfér a szolgáltatáshoz. *Webalkalmazás-paraméterek* lehetővé teszi ezt.

Ilyenek például állítsa be egy [adatok importálása] [ import-data] modul, így a felhasználó az üzembe helyezett webszolgáltatás egy másik adatforráshoz adhatja meg a webszolgáltatás eléréséhez. Vagy konfigurálása egy [adatok exportálása] [ export-data] modul úgy, hogy egy másik célhelyet lehet megadni.

Webszolgáltatás-paraméterek megadása, és rendelje azokat egy vagy több modulja paraméter, és megadhatja, hogy azok a szükséges és választható. A felhasználó a webszolgáltatás értékek a szolgáltatás érhető el, és a modul műveletek módosítani ezeket a paramétereket biztosít.

Webszolgáltatás-paraméterek vannak, és a használatukkal kapcsolatos további információkért lásd: [használata az Azure Machine Learning webszolgáltatás-paraméterek][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>A prediktív kísérletté üzembe webszolgáltatásként
Most, hogy a prediktív kísérletté megfelelően elő van készítve, telepíthet egy Azure-webszolgáltatásként. A webszolgáltatás segítségével felhasználók küldhetnek adatokat a modell és a modell az előrejelzéseket adja vissza.

A teljes telepítési folyamatról további információkért lásd: [egy Azure Machine Learning webszolgáltatás üzembe helyezése][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
