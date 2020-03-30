---
title: Adatok importálása/exportálása webszolgáltatásokban
titleSuffix: ML Studio (classic) - Azure
description: Megtudhatja, hogy az Adatok importálása és az Adatok exportálása modul segítségével hogyan küldhet és fogadhat adatokat egy webszolgáltatásból.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204058"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Az Adatok importálása és az adatexportálási modulokat használó, klasszikus Azure Machine Learning Studio-beli (klasszikus) webszolgáltatások üzembe helyezése

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Amikor prediktív kísérletet hoz létre, általában hozzá egy webszolgáltatás bemeneti és kimeneti. A kísérlet üzembe helyezésekor a fogyasztók adatokat küldhetnek és fogadhatnak a webszolgáltatásból a bemeneteken és kimeneteken keresztül. Egyes alkalmazások esetében előfordulhat, hogy a fogyasztó adatai elérhetők egy adatcsatornából, vagy már egy külső adatforrásban, például az Azure Blob storage-ban vannak. Ezekben az esetekben nem kell olvasni és írni az adatokat webszolgáltatás bemenetek és kimenetek használatával. Ehelyett a Batch Execution Service (BES) segítségével adatokat olvashatnak az adatforrásból egy Import Data modul használatával, és a pontozási eredményeket egy másik adathelyre írhatják egy Adat exportálási modul használatával.

Az adatok importálása és az adatok exportálása modulok, olvashat, és írja a különböző adathelyek, például egy webes URL-t HTTP-n keresztül, egy Hive-lekérdezés, egy Azure SQL-adatbázis, Az Azure Table storage, Az Azure Blob storage, a Data Feed nyújt, vagy egy helyszíni SQL-adatbázis.

Ez a témakör a "Minta 5: Betanítás, teszt, bináris besorolás kiértékelése: felnőtt adatkészlet" mintát használ, és feltételezi, hogy az adatkészlet már be van töltve egy Népszámlálási adatok nevű Azure SQL-táblába.

## <a name="create-the-training-experiment"></a>A betanítási kísérlet létrehozása
Amikor megnyitja a "Minta 5: Train, Test, Kiértékeli a bináris besorolás: felnőtt adatkészlet" minta használja a minta Felnőtt census income binary classification adatkészlet. És a kísérlet a vásznon hasonló lesz a következő képhez:

![A kísérlet kezdeti konfigurációja.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Az adatok olvasása az Azure SQL-táblából:

1. Törölje az adatkészletmodult.
2. Az összetevők keresőmezőjébe írja be az importálást.
3. Az eredmények listájából adjon hozzá egy *Import Data* modult a kísérletvászonhoz.
4. Csatlakoztassa az *Adatok importálása* modul kimenetét a Tiszta hiányzó adatok modul *bemenetével.*
5. A tulajdonságok ablaktáblán válassza az **Azure SQL Database** az **adatforrás** legördülő menüben.
6. Az **Adatbázis-kiszolgáló neve**, **Adatbázis név**, **Felhasználónév**és **Jelszó** mezőbe írja be az adatbázishoz tartozó megfelelő adatokat.
7. Az Adatbázis lekérdezés mezőbe írja be a következő lekérdezést.

     válassza ki a [kor],

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
     a dbo.censusdata-tól;
8. A kísérleti vászon alján kattintson a **Futtatás**gombra.

## <a name="create-the-predictive-experiment"></a>A prediktív kísérlet létrehozása
Ezután állítsa be azt a prediktív kísérletet, amelyből a webszolgáltatást üzembe helyezi.

1. A kísérleti vászon alján kattintson a **Webszolgáltatás beállítása** elemre, és válassza **a Prediktív webszolgáltatás [Ajánlott]** lehetőséget.
2. Távolítsa el a *webszolgáltatás bemeneti* és *webszolgáltatás kimeneti moduljait* a prediktív kísérletből.
3. Az összetevők keresőmezőjébe írja be az exportálás kifejezést.
4. Az eredmények listájából adjon hozzá egy *Exportálási adatok* modult a kísérletvászonhoz.
5. Csatlakoztassa a *Score Model* modul kimenetét az Export Data modul *bemenetével.*
6. A tulajdonságok ablaktáblán válassza az **Azure SQL Database** az adatok cél legördülő menüben.
7. Az **Adatbázis-kiszolgáló neve**, **Adatbázisnév**, **Kiszolgálói felhasználói fiók neve**és A kiszolgáló felhasználói fiók **jelszavának** mezőben adja meg az adatbázisnak megfelelő adatokat.
8. A **vesszővel elválasztott oszlopok listájába** írja be a Pontozott címkék parancsot.
9. A **Data tábla név mezőjébe**írja be a dbo parancsot. ScoredLabels. Ha a tábla nem létezik, akkor jön létre, amikor a kísérlet fut, vagy a webszolgáltatás neve.
10. Az **adatható oszlopok vesszővel elválasztott listájába** írja be a ScoredLabels parancsot.

Amikor olyan alkalmazást ír, amely meghívja a végső webszolgáltatást, előfordulhat, hogy futásidőben másik bemeneti lekérdezést vagy céltáblát szeretne megadni. A bemenetek és kimenetek konfigurálásához a Webszolgáltatás paraméterei szolgáltatással állítsa be az Adatok modul *adatforrásimportálása* és az Adatmódú adatok célának *Data source* *exportálása* tulajdonságot.  A webszolgáltatás paramétereiről az [Azure Machine Learning Studio webszolgáltatás paraméterei a](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) Cortana Intelligence and Machine Learning blogon található további információ.

Az importálási lekérdezés és a céltábla webszolgáltatás-paramétereinek konfigurálása:

1. Az *Adatok importálása* modul tulajdonságablakában kattintson az **Adatbázis lekérdezési** mező jobb felső részén lévő ikonra, és válassza a **Beállítás webszolgáltatás-paraméterként lehetőséget**.
2. Az *Adatok exportálása* modul tulajdonságablakában kattintson az **Adattábla név** mezőjének jobb felső részén lévő ikonra, és válassza a **Beállítás webszolgáltatás-paraméterként lehetőséget.**
3. Az Adatok modul tulajdonságainak *exportálása* ablaktábla, a **Webszolgáltatás paraméterei** szakaszalján kattintson az Adatbázis-lekérdezés elemre, és nevezze át a Lekérdezésre.
4. Kattintson **az Adattábla neve gombra,** és nevezze át **a táblázatot.**

Ha végzett, a kísérletnek az alábbi képhez hasonlóan kell kinéznie:

![A kísérlet utolsó pillantása.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Most már telepítheti a kísérletet webszolgáltatásként.

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
Telepítheti a klasszikus vagy új webszolgáltatás.

### <a name="deploy-a-classic-web-service"></a>Klasszikus webszolgáltatás telepítése
Klasszikus webszolgáltatásként való üzembe helyezés és alkalmazás létrehozása a felhasználáshoz:

1. A kísérleti vászon alján kattintson a Futtatás gombra.
2. A futtatás befejezése után kattintson a **Webszolgáltatás telepítése gombra,** és válassza **a [Klasszikus] webszolgáltatás telepítése lehetőséget.**
3. A webszolgáltatás irányítópultján keresse meg az API-kulcsot. Másolja és mentse későbbi használatra.
4. Az **Alapértelmezett végpont** táblában kattintson a **Kötegelt végrehajtás** hivatkozásra az API súgólapjának megnyitásához.
5. A Visual Studio alkalmazásban hozzon létre egy C# konzolalkalmazást: **Új** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework)** alkalmazást.
6. Az API súgólapján keresse meg a **mintakód** szakaszt a lap alján.
7. Másolja és illessze be a C# mintakódot a Program.cs fájlba, és távolítsa el a blob storage összes hivatkozást.
8. Frissítse az *apiKey* változó értékét a korábban mentett API-kulccsal.
9. Keresse meg a kérésdeklarációt, és frissítse az *Adatok importálása* és az *Adatok exportálása* moduloknak átadott webszolgáltatás-paraméterek értékeit. Ebben az esetben az eredeti lekérdezést használja, de új táblanevet határoz meg.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Futtassa az alkalmazást.

A futtatás befejezésekor egy új tábla kerül az adatbázisba, amely tartalmazza a pontozási eredményeket.

### <a name="deploy-a-new-web-service"></a>Új webszolgáltatás telepítése

> [!NOTE]
> Új webszolgáltatás üzembe helyezéséhez megfelelő engedélyekkel kell rendelkeznie abban az előfizetésben, amelyre a webszolgáltatást telepítette. További információt a [Webszolgáltatás kezelése az Azure Machine Learning WebServices portálon című témakörben talál.](manage-new-webservice.md)

Új webszolgáltatásként való üzembe helyezés és alkalmazás létrehozása a felhasználáshoz:

1. A kísérleti vászon alján kattintson a **Futtatás**gombra.
2. A futtatás befejezése után kattintson a **Webszolgáltatás telepítése gombra,** és válassza **a Webszolgáltatás telepítése [Új] lehetőséget.**
3. A Kísérlet telepítése lapon adja meg a webszolgáltatás nevét, és válasszon egy díjszabási csomagot, majd kattintson **a Telepítés gombra.**
4. A **Rövid útmutató** lapon kattintson a **Felhasználás gombra.**
5. A **Mintakód** csoportban kattintson a **Köteg gombra.**
6. A Visual Studio alkalmazásban hozzon létre egy C# konzolalkalmazást: **Új** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework)** alkalmazást.
7. Másolja és illessze be a C# mintakódot a Program.cs fájlba.
8. Frissítse az *apiKey* változó értékét az **Alapszintű felhasználási adatok** szakaszban található elsődleges **kulccsal.**
9. Keresse meg a *scoreRequest* deklarációt, és frissítse az *adatok importálása* és az *adatok exportálása* moduloknak átadott webszolgáltatás-paraméterek értékeit. Ebben az esetben az eredeti lekérdezést használja, de új táblanevet határoz meg.

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

