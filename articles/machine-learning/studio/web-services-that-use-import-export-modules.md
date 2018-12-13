---
title: Importálási/exportálási adatok webszolgáltatások – az Azure Machine Learning Studióban |} A Microsoft Docs
description: Ismerje meg, hogy az adatok importálása és exportálása az modulok használata, küldhet és fogadhat adatokat egy webszolgáltatásból.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 84bc151e8d7acf8d320b5883e8637c321762fb32
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090346"
---
# <a name="deploy-azure-machine-learning-studio-web-services-that-use-data-import-and-data-export-modules"></a>Adatok importálása és az adatok exportálása modult használó Azure Machine Learning Studio-webszolgáltatások üzembe helyezése

Amikor létrehoz egy prediktív kísérletet, általában hozzáadhat egy webes szolgáltatás bemenet és kimenet. Központi telepítésekor a kísérletet, a fogyasztók küldhet és fogadhat adatokat keresztül bemeneteit és kimeneteit a webszolgáltatásból. Egyes alkalmazások esetében a egy fogyasztó data adatcsatornáról érhető el, vagy már található egy külső adatforrást, például az Azure Blob storage. Ezekben az esetekben nincs szükségük használata a web service bemeneti és kimeneti adatok olvasását és írását. Akkor is, ehelyett a Batch Execution Service (BES) segítségével adatokat olvasni az adatforrás-adatok importálása modullal és írási a pontozási eredményeinek-adatok exportálása modult használó más helyre.

Az adatok importálása és exportálása adatok modulok, olvasni és írni a helyeket, például a HTTP, a Hive-lekérdezés, egy Azure SQL database, az Azure Table storage, Azure Blob storage-adatcsatorna használatával egy webes URL-címet adjon meg különböző adatokat, vagy egy helyszíni SQL-adatbázis.

Ez a témakör használja az "5 minta: Train, tesztelési, kiértékelése a bináris osztályozás: felnőtt adatkészlet" mintát, és feltételezi, hogy az adatkészlet már be van töltve censusdata nevű Azure SQL-táblába.

## <a name="create-the-training-experiment"></a>A betanítási kísérlet létrehozása
Amikor megnyitja a "minta 5: Train, tesztelési, Evaluate bináris osztályozási: felnőtt adatkészlet" mintát, használja a bináris osztályozási felnőtt népszámlálási jövedelem mintaadatkészlettel. És a vásznon a kísérlet a következő képhez hasonlóan néz ki:

![A kísérlet kezdeti konfigurálása.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Az adatokat olvasni az Azure SQL-táblát:

1. Az adatkészlet-modul törlése.
2. Az összetevők a keresőmezőbe írja be a importálása.
3. Az eredmények listájában, adjon hozzá egy *adatok importálása* modult a kísérletvászonra.
4. Csatlakozzon a kimenetét a *adatok importálása* modul bemeneti, a *Clean Missing Data* modul.
5. A tulajdonságait tartalmazó ablaktáblán jelölje ki **Azure SQL Database** a a **adatforrás** legördülő listából.
6. Az a **adatbázis-kiszolgáló neve**, **adatbázisnév**, **felhasználónév**, és **jelszó** mezőknél adja meg a megfelelő adatokat a az adatbázis.
7. A Database lekérdezési mezőjében adja meg a következő lekérdezést.
   
     Válassza ki a [Kor],
   
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     a dbo.censusdata;
8. A kísérlet vászon alján kattintson **futtatása**.

## <a name="create-the-predictive-experiment"></a>A prediktív kísérlet létrehozása
A következő, állítsa be a prediktív kísérletet, amelyről a web Service szolgáltatásának telepítése.

1. A kísérlet vászon alján kattintson **webszolgáltatás beállítása** válassza **prediktív webszolgáltatás [ajánlott]**.
2. Távolítsa el a *webes bemenet* és *webes szolgáltatás kimeneti modulok* a prediktív kísérletből. 
3. Az összetevők a keresőmezőbe írja be a exportálási.
4. Az eredmények listájában, adjon hozzá egy *adatok exportálása* modult a kísérletvászonra.
5. Csatlakozzon a kimenetét a *Score Model* modul bemeneti, a *adatok exportálása* modul. 
6. A tulajdonságait tartalmazó ablaktáblán jelölje ki **Azure SQL Database** adatokat a rendeltetési legördülő.
7. Az a **adatbázis-kiszolgáló neve**, **adatbázisnév**, **kiszolgáló felhasználói fiók nevét**, és **kiszolgáló felhasználói fiók jelszava** mezőknél adja meg a az adatbázis megfelelő adatait.
8. Az a **vesszővel tagolt oszlopok menteni** mezőbe írja be a pontozott címkék.
9. Az a **táblázat neve adatmező**, írja be a dbo. ScoredLabels. Ha a tábla nem létezik, létrejön a kísérlet futtatásakor vagy a web service nevezzük.
10. Az a **vesszővel tagolt listája datatable oszlopok** mezőbe írja be a ScoredLabels.

Ha egy alkalmazás, amely meghívja a végső webszolgáltatás ír, érdemes adjon meg egy másik bemeneti lekérdezés vagy a céltábla futási időben. A bemenetek és kimenetek konfigurálása, a webszolgáltatás-paraméterek szolgáltatás segítségével állítsa be a *adatok importálása* modul *adatforrás* tulajdonság és a *adatok exportálása* mód adatait cél tulajdonság.  A webszolgáltatás-paraméterek további információkért lásd: a [AzureML webszolgáltatás-paraméterek bejegyzés](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) meg a Cortana Intelligence and Machine Learning blogon.

A webszolgáltatás-paraméterek a lekérdezés importálása és a céltábla konfigurálása:

1. A Tulajdonságok panelen az a *adatok importálása* modult, kattintson az ikonra a felső, jobb a **adatbázis-lekérdezés** mezőbe, majd válasszon **webszolgáltatási paraméter beállítása**.
2. A Tulajdonságok panelen az a *adatok exportálása* modult, kattintson az ikonra a felső, jobb a **adatok táblanév** mezőbe, majd válasszon **webszolgáltatási paraméter beállítása**.
3. Alsó részén a *adatok exportálása* modul tulajdonságait tartalmazó ablaktáblán, a a **webszolgáltatás-paraméterek** részen kattintson az adatbázis-lekérdezés, és nevezze át az lekérdezés.
4. Kattintson a **adatok táblanév** , és nevezze át **tábla**.

Amikor elkészült, is futtathatja a kísérletet a következő képhez hasonlóan kell kinéznie:

![Utolsó kísérlet megjelenését.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Most már telepítheti a kísérlet webszolgáltatásként.

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
A klasszikus vagy egy új webszolgáltatás telepítheti.

### <a name="deploy-a-classic-web-service"></a>A klasszikus webszolgáltatások üzembe helyezése
Klasszikus webszolgáltatásként üzembe helyezéséhez, és létrehozhat egy alkalmazást, ezért használja fel:

1. A kísérlet vászon alján kattintson a Futtatás.
2. A Futtatás befejeződésekor kattintson **webszolgáltatás üzembe helyezése** válassza **Web Service telepítése [klasszikus]**.
3. A webszolgáltatás irányítópultján keresse meg az API-kulcsot. Másolja és mentse későbbi használatra.
4. Az a **alapértelmezett végpont** tábla mértékrácsán kattintson a **kötegelt végrehajtási** nyissa meg az API-súgóoldalon mutató hivatkozást.
5. A Visual Studióban hozzon létre egy C# Konzolalkalmazás: **új** > **projekt** > **Visual C#**   >   **Windows klasszikus Asztalialkalmazás** > **Console App (.NET Framework)**.
6. Az API-súgóoldalon, keresse meg a **mintakód** szakasz az oldal alján.
7. Másolja és illessze be a C# mintakód a Program.cs fájlba, és távolítson el minden hivatkozást a blob Storage.
8. Frissítse az értéket, a *apikey tulajdonsággal végzett tesztelése* változó a korábban mentett API-kulccsal.
9. Keresse meg a kérelem nyilatkozat, és frissítse a webszolgáltatás-paraméterek, amelyeket a rendszer átad a *adatok importálása* és *adatok exportálása* modulok. Ebben az esetben, az eredeti lekérdezéssel, de egy új tábla nevét adja meg.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Futtassa az alkalmazást. 

A Futtatás végeztével egy új tábla hozzáadódik a pontozási eredményeinek tartalmazó adatbázis.

### <a name="deploy-a-new-web-service"></a>Egy új webszolgáltatás üzembe helyezése

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információkért lásd: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md). 

Új webszolgáltatásként üzembe helyezéséhez, és létrehozhat egy alkalmazást, ezért használja fel:

1. A kísérlet vászon alján kattintson **futtatása**.
2. A Futtatás befejeződésekor kattintson **webszolgáltatás üzembe helyezése** válassza **[Új] Web Service telepítése**.
3. Kísérlet üzembe helyezése lapon adjon meg egy nevet, a webes szolgáltatáshoz, és a egy tarifacsomagot választani, majd kattintson **telepítés**.
4. Az a **rövid** kattintson **felhasználás**.
5. Az a **mintakód** területén kattintson **Batch**.
6. A Visual Studióban hozzon létre egy C# Konzolalkalmazás: **új** > **projekt** > **Visual C#**   >   **Windows klasszikus Asztalialkalmazás** > **Console App (.NET Framework)**.
7. Másolja és illessze be a C# mintakód a Program.cs fájlba.
8. Frissítse az értéket, a *apikey tulajdonsággal végzett tesztelése* változót a **elsődleges kulcs** található a **alapvető fogyasztási adatai** szakaszban.
9. Keresse meg a *scoreRequest* nyilatkozat, és frissítse a webszolgáltatás-paraméterek, amelyeket a rendszer átad a *adatok importálása* és *adatok exportálása* modulok. Ebben az esetben, az eredeti lekérdezéssel, de egy új tábla nevét adja meg.
   
        var scoreRequest = new
        {       
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Futtassa az alkalmazást. 

