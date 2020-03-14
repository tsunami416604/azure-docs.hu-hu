---
title: Adatimportálási/-exportálási szolgáltatás
titleSuffix: ML Studio (classic) - Azure
description: Ismerje meg, hogy az adatok importálása és exportálása az modulok használata, küldhet és fogadhat adatokat egy webszolgáltatásból.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 144a3bc0d9e0499a238e4033d37d5e4d3fa61e05
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204058"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Az adatimportálást és az adatexportálási modulokat használó Azure Machine Learning Studio (klasszikus) webszolgáltatások üzembe helyezése

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Amikor létrehoz egy prediktív kísérletet, általában hozzáadhat egy webes szolgáltatás bemenet és kimenet. Központi telepítésekor a kísérletet, a fogyasztók küldhet és fogadhat adatokat keresztül bemeneteit és kimeneteit a webszolgáltatásból. Egyes alkalmazások esetében a egy fogyasztó data adatcsatornáról érhető el, vagy már található egy külső adatforrást, például az Azure Blob storage. Ezekben az esetekben nincs szükségük használata a web service bemeneti és kimeneti adatok olvasását és írását. Akkor is, ehelyett a Batch Execution Service (BES) segítségével adatokat olvasni az adatforrás-adatok importálása modullal és írási a pontozási eredményeinek-adatok exportálása modult használó más helyre.

Az adatok importálása és exportálása adatok modulok, olvasni és írni a helyeket, például a HTTP, a Hive-lekérdezés, egy Azure SQL database, az Azure Table storage, Azure Blob storage-adatcsatorna használatával egy webes URL-címet adjon meg különböző adatokat, vagy egy helyszíni SQL-adatbázis.

Ez a témakör használja az "5 minta: Train, tesztelési, kiértékelése a bináris osztályozás: felnőtt adatkészlet" mintát, és feltételezi, hogy az adatkészlet már be van töltve censusdata nevű Azure SQL-táblába.

## <a name="create-the-training-experiment"></a>A betanítási kísérlet létrehozása
Amikor megnyitja a "minta 5: Train, tesztelési, Evaluate bináris osztályozási: felnőtt adatkészlet" mintát, használja a bináris osztályozási felnőtt népszámlálási jövedelem mintaadatkészlettel. És a vásznon a kísérlet a következő képhez hasonlóan néz ki:

![A kísérlet kezdeti konfigurálása.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Az adatokat olvasni az Azure SQL-táblát:

1. Az adatkészlet-modul törlése.
2. Az összetevők a keresőmezőbe írja be a importálása.
3. Az eredmények listájában adjon hozzá egy *adatimportálási* modult a kísérlet vászonhoz.
4. Az *adatok importálása* modul kimenetének összekötése a *tiszta hiányzó* adatmodul bemenetével.
5. A Tulajdonságok ablaktáblán válassza a **Azure SQL Database** lehetőséget az **adatforrás** legördülő menüben.
6. Az adatbázis- **kiszolgáló neve**, az **adatbázis neve**, a **Felhasználónév**és a **jelszó** mezőben adja meg az adatbázishoz szükséges adatokat.
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
8. A kísérlet vászon alján kattintson a **Futtatás**elemre.

## <a name="create-the-predictive-experiment"></a>A prediktív kísérlet létrehozása
A következő, állítsa be a prediktív kísérletet, amelyről a web Service szolgáltatásának telepítése.

1. A kísérlet vászon alján kattintson a **webszolgáltatás beállítása** elemre, és válassza a **prediktív webszolgáltatás elemet [ajánlott]** .
2. Távolítsa el a *webszolgáltatás bemeneti* és *webszolgáltatási kimeneti modulját* a prediktív kísérletből.
3. Az összetevők a keresőmezőbe írja be a exportálási.
4. Az eredmények listájában adjon hozzá egy *adatexportálási* modult a kísérlet vászonhoz.
5. A *score Model* modul kimenetének összekötése az *adatexportálási* modul bemenetével.
6. A Tulajdonságok ablaktáblán válassza a **Azure SQL Database** lehetőséget az adatcél legördülő menüben.
7. Az adatbázis- **kiszolgáló neve**, az **adatbázis neve**, a **kiszolgáló felhasználói fiókjának neve**és a **kiszolgáló felhasználói fiók jelszava** mezőkben adja meg az adatbázishoz szükséges adatokat.
8. A **menteni kívánt oszlopok vesszővel tagolt listájában** írja be a következőt: pontozásos címkék.
9. Az **adattábla neve mezőbe**írja be a következőt: dbo. ScoredLabels. Ha a tábla nem létezik, létrejön a kísérlet futtatásakor vagy a web service nevezzük.
10. A **DataTable oszlopok vesszővel tagolt listája** mezőjébe írja be a következőt: ScoredLabels.

Ha egy alkalmazás, amely meghívja a végső webszolgáltatás ír, érdemes adjon meg egy másik bemeneti lekérdezés vagy a céltábla futási időben. Ezen bemenetek és kimenetek konfigurálásához használja a webszolgáltatás paramétereit, és állítsa be az adatok *importálása* modul *adatforrás* tulajdonságát és az adatok *exportálása* a cél tulajdonságot.  A webszolgáltatási paraméterekkel kapcsolatos további információkért tekintse meg a Cortana Intelligence és Machine Learning blog [Azure Machine learning Studio webszolgáltatási paraméterek bejegyzését](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) .

A webszolgáltatás-paraméterek a lekérdezés importálása és a céltábla konfigurálása:

1. Az *adatimportálási* modul tulajdonságok paneljén kattintson az **adatbázis-lekérdezés** mező jobb felső sarkában található ikonra, és válassza a **beállítás webszolgáltatásként paramétert**.
2. Az *adatexportálási* modul tulajdonságok paneljén kattintson az **adattábla neve** mező jobb felső sarkában található ikonra, és válassza a **beállítás webszolgáltatásként paramétert**.
3. Az *adatok exportálása* modul tulajdonságai ablaktábla alján, a **webszolgáltatás paramétereinek** szakaszban kattintson az adatbázis-lekérdezés elemre, és nevezze át a lekérdezést.
4. Kattintson **az adattábla neve** elemre, és nevezze át a **táblát**.

Amikor elkészült, is futtathatja a kísérletet a következő képhez hasonlóan kell kinéznie:

![Utolsó kísérlet megjelenését.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Most már telepítheti a kísérlet webszolgáltatásként.

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
A klasszikus vagy egy új webszolgáltatás telepítheti.

### <a name="deploy-a-classic-web-service"></a>A klasszikus webszolgáltatások üzembe helyezése
Klasszikus webszolgáltatásként üzembe helyezéséhez, és létrehozhat egy alkalmazást, ezért használja fel:

1. A kísérlet vászon alján kattintson a Futtatás.
2. A Futtatás befejeződése után kattintson a **webszolgáltatás üzembe** helyezése elemre, és válassza a **webszolgáltatás telepítése [klasszikus]** lehetőséget.
3. A webszolgáltatás irányítópultján keresse meg az API-kulcsot. Másolja és mentse későbbi használatra.
4. Az **alapértelmezett végpont** táblában kattintson a Batch- **végrehajtás** hivatkozásra az API-Súgó oldal megnyitásához.
5. A Visual Studióban hozzon C# létre egy Console alkalmazást: **új** > **Project** > **Visual C#**  > **Windows klasszikus asztali** > **Console app (.NET-keretrendszer)** .
6. Az API-Súgó lapon keresse meg a kódlap alján található **mintakód** szakaszt.
7. Másolja és illessze be a C# mintakód a Program.cs fájlba, és távolítson el minden hivatkozást a blob Storage.
8. Frissítse a *apiKey* változó értékét a korábban mentett API-kulccsal.
9. Keresse meg a kérelem deklarációját, és frissítse az adatok *importálása* és *exportálása* modulba átadott webszolgáltatás-paraméterek értékeit. Ebben az esetben, az eredeti lekérdezéssel, de egy új tábla nevét adja meg.

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
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md).

Új webszolgáltatásként üzembe helyezéséhez, és létrehozhat egy alkalmazást, ezért használja fel:

1. A kísérlet vászon alján kattintson a **Futtatás**elemre.
2. Ha a Futtatás befejeződött, kattintson a **webszolgáltatás üzembe helyezése** elemre, és válassza a **webszolgáltatás üzembe helyezése [új]** lehetőséget.
3. A kísérlet telepítése lapon adja meg a webszolgáltatás nevét, és válasszon ki egy díjszabási csomagot, majd kattintson a **telepítés**elemre.
4. A rövid **útmutató lapon kattintson a felhasználás** elemre **.**
5. A **mintakód** szakaszban kattintson a **Batch**elemre.
6. A Visual Studióban hozzon C# létre egy Console alkalmazást: **új** > **Project** > **Visual C#**  > **Windows klasszikus asztali** > **Console app (.NET-keretrendszer)** .
7. Másolja és illessze be a C# mintakód a Program.cs fájlba.
8. Frissítse a *apiKey* változó értékét az **alapszintű felhasználás adatai** szakaszban található **elsődleges kulccsal** .
9. Keresse meg a *scoreRequest* -deklarációt, és frissítse az adatok *importálása* és *exportálása* modulba átadott webszolgáltatás-paraméterek értékeit. Ebben az esetben, az eredeti lekérdezéssel, de egy új tábla nevét adja meg.

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

