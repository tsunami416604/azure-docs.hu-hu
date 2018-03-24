---
title: Importálási/exportálási adatok használatának Azure Machine Learning webszolgáltatások |} Microsoft Docs
description: Ismerje meg, hogyan használható az adatok importálása és exportálása adatok modulok adatokat küldeni és fogadni webszolgáltatásból.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 61143302b21f93326f9f81885d845cd388203eb3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Az Adatok importálása és az Adatok exportálása modult használó Azure ML webszolgáltatások üzembe helyezése

Amikor létrehoz egy prediktív kísérletté, általában hozzáadhat egy webszolgáltatás bemenete és a kimeneti. A kísérlet telepítésekor fogyasztók küldhet és fogadhat adatokat a webszolgáltatás a be- és kimenetekkel keresztül. Egyes alkalmazások a fogyasztói adatoktól adatcsatornáról érhető el vagy már található egy külső adatforrásból, például az Azure Blob Storage tárolóban. Ebben az esetben nincs szükségük olvasása és írása az adatokat, és a webszolgáltatás bemenetei és kimenetei. Ugyanakkor, ehelyett a kötegelt végrehajtási szolgáltatás (BES) segítségével adatokat olvasni az adatforrást, és adatokat importálhat-modullal és a pontozási eredményeinek írni egy másik adatok helye az adatok exportálása modullal.

Az adatok importálása és exportálása adatok modulok, olvasni és írni a különböző adatokat, például egy webes URL-Címéhez HTTP, a Hive-lekérdezések, egy Azure SQL-adatbázis, Azure Table storage, Azure Blob Storage tárolóban, a adatcsatorna révén helyét adja meg, vagy egy helyi SQL-adatbázis.

Ez a témakör használja a "minta 5: vonat, tesztelési, Evaluate bináris osztályozási: felnőtt Dataset" sample, és azt feltételezi, hogy a dataset már be van töltve nevű censusdata Azure SQL-táblába.

## <a name="create-the-training-experiment"></a>A képzési kísérlet létrehozása
Amikor megnyitja a "minta 5: vonat, tesztelési, Evaluate bináris osztályozási: felnőtt Dataset" mintát használ a minta bináris osztályozási felnőtt nyilvántartásba bevétel adatkészlet. És a kísérlet vászonra az alábbi képen hasonlóan fog kinézni:

![A kísérlet a kezdeti konfigurálását.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Az adatokat olvasni az Azure SQL táblázat:

1. Az adatkészlet-modul törlése.
2. Összetevők a keresőmezőbe írja be a importálása.
3. Az eredmények listájában hozzá egy *és adatokat importálhat* modult a kísérletvászonra.
4. Csatlakozás kimenetét a *és adatokat importálhat* modul bemeneti, a *Clean Missing Data* modul.
5. A Tulajdonságok panelen, jelölje ki **Azure SQL Database** a a **adatforrás** legördülő menüből.
6. Az a **adatbázis-kiszolgáló neve**, **adatbázisnév**, **felhasználónév**, és **jelszó** mezőkben adja meg a megfelelő adatokat a az adatbázis.
7. Az adatbázis lekérdezés mezőben adja meg a következő lekérdezés.
   
     Válassza ki a [kora]
   
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
8. Kattintson a kísérletvászon alján **futtatása**.

## <a name="create-the-predictive-experiment"></a>A prediktív kísérlet létrehozása
Ezután a prediktív kísérletté, ahol a webszolgáltatás telepítése beállítása.

1. A kísérlet vászon alján kattintson **webes szolgáltatások beállítása** válassza **prediktív webszolgáltatás [ajánlott]**.
2. Távolítsa el a *webszolgáltatás bemenetét* és *webes szolgáltatás kimeneti modulok* a prediktív kísérlet. 
3. Összetevők keresőmezőbe írja be az exportálás.
4. Az eredmények listájában hozzá egy *adatok exportálása* modult a kísérletvászonra.
5. Csatlakozás kimenetét a *Score Model* modul bemeneti, a *adatok exportálása* modul. 
6. A Tulajdonságok panelen, jelölje ki **Azure SQL Database** meg az adatok cél legördülő listában.
7. Az a **adatbázis-kiszolgáló neve**, **adatbázisnév**, **kiszolgáló felhasználói fiók nevét**, és **kiszolgáló felhasználói fiók jelszavát** mezőkben adja meg a az adatbázis megfelelő adatokat.
8. Az a **vesszővel tagolt listája menteni oszlopok** mezőbe írja be a pontozott címkék.
9. Az a **tábla neve adatmező**, írja be a dbo. ScoredLabels. Ha a tábla nem létezik, létrejön a kísérlet fut, vagy a webes szolgáltatás neve.
10. Az a **vesszővel tagolt listája datatable oszlopok** mezőbe írja be a ScoredLabels.

Ha egy alkalmazás, amely behívja a végső webszolgáltatás ír, érdemes lehet adjon meg egy másik bemeneti lekérdezés vagy a célként megadott táblája futási időben. Konfigurálja ezeket bemenetekhez és kimenetekhez, a webszolgáltatás-paramétereket szolgáltatás használatával állítsa be a *és adatokat importálhat* modul *adatforrás* tulajdonság és a *adatok exportálása* mód adatait cél tulajdonság.  A webszolgáltatás-paramétereket további információkért lásd: a [AzureML webszolgáltatás-paramétereket bejegyzés](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) a Cortana Intelligence és a Machine Learning Blog.

A webszolgáltatás-paramétereket az importálási és a célként megadott táblája konfigurálása:

1. A Tulajdonságok panelen az a *és adatokat importálhat* modult, kattintson az ikonra a bal felső sarkában a **adatbázis-lekérdezés** mezőben, majd válassza ki **webszolgáltatási paraméter beállítása**.
2. A Tulajdonságok panelen az a *adatok exportálása* modult, kattintson az ikonra a bal felső sarkában a **adatok táblanév** mezőben, majd válassza ki **webszolgáltatási paraméter beállítása**.
3. Alján a *adatok exportálása* modul tulajdonságok panelen, a a **webszolgáltatás-paramétereket** szakaszt, kattintson az adatbázis-lekérdezés és lekérdezés nevezni.
4. Kattintson a **adatok táblanév** és adjon neki **tábla**.

Amikor elkészült, a kísérlet a következő kép hasonlóan kell kinéznie:

![Kísérlet végső megjelenését.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Most már telepítheti a kísérlet webszolgáltatásként.

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
A klasszikus vagy az új webszolgáltatás telepítése.

### <a name="deploy-a-classic-web-service"></a>Klasszikus webes szolgáltatás telepítése
Klasszikus webszolgáltatásként üzembe helyezését, és felhasználását, az alkalmazás létrehozása:

1. A kísérlet vászon alján kattintson a Futtatás gombra.
2. Kattintson a Futtatás befejeződésekor **webes szolgáltatás telepítése** válassza **webes szolgáltatás telepítése [klasszikus]**.
3. A webes szolgáltatás irányítópultján keresse meg az API-kulcs. Másolja ki és mentse későbbi használat céljából.
4. Az a **alapértelmezett végpont** table, kattintson a **kötegelt végrehajtási** nyissa meg az API-Súgóoldalát mutató hivatkozást.
5. A Visual Studio, hozzon létre egy C# konzolalkalmazást: **új** > **projekt** > **Visual C#** > **Windows Klasszikus asztal** > **Konzolalkalmazásból (.NET-keretrendszer)**.
6. Az API segítségével lapon található a **mintakód** szakasz az oldal alján.
7. Másolja és illessze be a C# mintakód a Program.cs fájlba, és távolítson el minden hivatkozást a blob Storage.
8. Frissítse az értéket a *apiKey* változó a korábban mentett API-kulccsal.
9. Keresse meg a kérelem nyilatkozat, és módosítsa a átadott webszolgáltatás-paramétereket a *és adatokat importálhat* és *adatok exportálása* modulok. Ebben az esetben akkor az eredeti lekérdezéssel, de egy új táblázat nevének meghatározása.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Futtassa az alkalmazást. 

A Futtatás befejezésekor új tábla hozzáadódik a pontozási eredményeinek tartalmazó adatbázis.

### <a name="deploy-a-new-web-service"></a>Egy új webszolgáltatás-bővítmény telepítése

> [!NOTE] 
> Egy új webszolgáltatás-bővítmény telepítése, megfelelő engedélyekkel kell rendelkeznie, amelyhez az előfizetést, a webszolgáltatás telepítése. További információkért lásd: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

Telepítsen egy új webszolgáltatás-bővítmény, és felhasználását, az alkalmazás létrehozása:

1. Kattintson a kísérletvászon alján **futtatása**.
2. Kattintson a Futtatás befejeződésekor **webes szolgáltatás telepítése** válassza **[Új] webes szolgáltatás telepítése**.
3. Telepítése kísérlet lapon adjon meg egy nevet, a webes szolgáltatás, és válassza ki a tarifacsomagot, majd kattintson **telepítés**.
4. Az a **gyors üzembe helyezés** kattintson **felhasználás**.
5. Az a **mintakód** kattintson **kötegelt**.
6. A Visual Studio, hozzon létre egy C# konzolalkalmazást: **új** > **projekt** > **Visual C#** > **Windows Klasszikus asztal** > **Konzolalkalmazásból (.NET-keretrendszer)**.
7. Másolja és illessze be a C# mintakód a Program.cs fájl.
8. Frissítse az értéket a *apiKey* változó, a **elsődleges kulcs** található, a **alapvető fogyasztási adatai** szakasz.
9. Keresse meg a *scoreRequest* nyilatkozat és módosítsa a átadott webszolgáltatás-paramétereket a *és adatokat importálhat* és *adatok exportálása* modulok. Ebben az esetben akkor az eredeti lekérdezéssel, de egy új táblázat nevének meghatározása.
   
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

