---
title: Átalakítás az Azure Databricks szolgáltatással
description: Ismerje meg, hogyan használhat megoldássablont az adatok átalakításához egy Databricks-jegyzetfüzet használatával az Azure Data Factoryban.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 65b89a13637f5a4e1712995a6ac58d88b4421806
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414851"
---
# <a name="transformation-with-azure-databricks"></a>Átalakítás az Azure Databricks szolgáltatással

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban egy teljes körű folyamatot hoz létre, amely tartalmazza az **Érvényesítési**, **Adatok másolása**és **jegyzetfüzet-tevékenységek** et az Azure Data Factoryban.

- **Az ellenőrzés** biztosítja, hogy a forrásadatkészlet készen áll az alsóbb rétegbeli felhasználásra, mielőtt elindítana a másolási és elemzési feladatot.

- **Adatok másolása** duplikálja a forrás-adatkészletet a fogadó tárolóba, amely dbfs-ként van csatlakoztatva az Azure Databricks-jegyzetfüzetben. Ily módon az adatkészlet et közvetlenül felhasználhatja a Spark.

- **A Jegyzetfüzet** elindítja a Databricks-jegyzetfüzetet, amely átalakítja az adatkészletet. Az adatkészletet egy feldolgozott mappához vagy az Azure SQL Data Warehouse-hoz is hozzáadja.

Az egyszerűség kedvéért az oktatóanyagban szereplő sablon nem hoz létre ütemezett eseményindítót. Szükség esetén hozzáadhat egyet.

![A csővezeték diagramja](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Blob-tárfiók `sinkdata` egy tárolót hívott használni, mint egy fogadó.

  Jegyezze fel a tárfiók nevét, a tároló nevét és a hozzáférési kulcsot. Ezekre az értékekre később szüksége lesz a sablonban.

- Egy Azure Databricks munkaterület.

## <a name="import-a-notebook-for-transformation"></a>Jegyzetfüzet importálása átalakításhoz

**Átalakítási** jegyzetfüzet importálása a Databricks-munkaterületre:

1. Jelentkezzen be az Azure Databricks-munkaterületére, és válassza **az Importálás lehetőséget.**
       ![Menüparancs munkaterület importálásához](media/solution-template-Databricks-notebook/import-notebook.png) A munkaterület elérési útja eltérhet a megjelenítettelérési úttól, de később emlékezzen rá.
1. Válassza **az Importálás innen: URL**lehetőséget. A szövegmezőbe írja `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`be a mezőbe.

   ![Kijelölés jegyzetfüzet importálására](media/solution-template-Databricks-notebook/import-from-url.png)

1. Most frissítsük az **átalakítási** jegyzetfüzetet a tárolási kapcsolat adataival.

   Az importált jegyzetfüzetben lépjen az **5-ös parancsra** a következő kódrészletben látható módon.

   - Cserélje `<storage name>` `<access key>` le és írja be a saját tárolási kapcsolatadatait.
   - Használja a tárolót `sinkdata` tartalmazó tárfiókot.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Hozzon létre egy **Databricks hozzáférési jogkivonatot** a Data Factory számára a Databricks eléréséhez.
   1. A Databricks-munkaterületen válassza ki a felhasználói profil ikonját a jobb felső sarokban.
   1. Válassza a **Felhasználói beállítások lehetőséget**.
    ![Menüparancs a felhasználói beállításokhoz](media/solution-template-Databricks-notebook/user-setting.png)
   1. Válassza az **Új jogkivonat létrehozása lehetőséget** a Hozzáférési **jogkivonatok** lapon.
   1. Válassza **a Létrehozás**lehetőséget.

    !["Létrehozás" gomb](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Mentse a hozzáférési jogkivonatot* későbbi használatra egy Databricks csatolt szolgáltatás létrehozásához. A hozzáférési jogkivonat `dapi32db32cbb4w6eee18b7d87e45exxxxxx`valahogy így néz ki.

## <a name="how-to-use-this-template"></a>A sablon használata

1. Nyissa meg az Átalakítás az **Azure Databricks sablont,** és hozzon létre új csatolt szolgáltatásokat a következő kapcsolatokhoz.

   ![Kapcsolatok beállítása](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Forrásblob-kapcsolat** – a forrásadatok eléréséhez.

       Ebben a gyakorlatban használhatja a nyilvános blob tároló, amely tartalmazza a forrásfájlokat. Hivatkozzon a konfigurációkövetkező képernyőképére. A forrástárolóhoz való csatlakozáshoz (írásvédett hozzáférés) a következő **SAS-URL-cím** használatával csatlakozhat:

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![A hitelesítési módszer és a SAS URL-cím kiválasztása](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Cél Blob Connection** - a másolt adatok tárolására.

       Az **Új csatolt szolgáltatás** ablakban válassza ki a fogadó tárolási blob.

       ![Tárolási blob elősüllye új csatolt szolgáltatásként](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** – a Databricks-fürthöz való csatlakozáshoz.

        Hozzon létre egy Databricks-kapcsolt szolgáltatás segítségével a hozzáférési kulcsot, amely korábban létrehozott. Ha rendelkezik *ilyen,* választhat egy interaktív fürt kiválasztását. Ez a példa az **Új feladatfürt** beállítást használja.

        ![A fürthöz való csatlakozás kijelölése](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Válassza **a Sablon használata lehetőséget.** Látni fogja, hogy létrehozott egy folyamat.

    ![Folyamat létrehozása](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>A csővezeték bevezetése és konfigurálása

Az új folyamatban a legtöbb beállítás automatikusan az alapértelmezett értékekkel van konfigurálva. Tekintse át a folyamat konfigurációit, és hajtsa végre a szükséges módosításokat.

1. Az **Érvényesítési** tevékenység **rendelkezésre állása jelzőben** `SourceAvailabilityDataset` ellenőrizze, hogy a **forrásadatkészlet** értéke a korábban létrehozott értékre van-e beállítva.

   ![Forrásadatkészlet értéke](media/solution-template-Databricks-notebook/validation-settings.png)

1. Az **Adatok másolása** **fájl-blob,** ellenőrizze a **Forrás** és **a Fogadó** lapon. Szükség esetén módosítsa a beállításokat.

   - **Forrás** ![lap Forrása lap](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **A** ![Mosdó lap Elsüllyesztő lapja](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. A **Jegyzetfüzet-tevékenység** **átalakítása**párbeszédpanelen szükség szerint tekintse át és frissítse az elérési utakat és beállításokat.

   **A Databricks-alapú szolgáltatást** előre ki kell tölteni egy előző ![lépés értékével, ahogy az látható: A Databricks csatolt szolgáltatás feltöltési értéke](media/solution-template-Databricks-notebook/notebook-activity.png)

   A **Jegyzetfüzet** beállításainak ellenőrzése:
  
    1. Válassza a **Beállítások** lapot. A **Jegyzetfüzet elérési útja**esetén ellenőrizze, hogy az alapértelmezett elérési út helyes-e. Előfordulhat, hogy tallózással kell eljárnia, és ki kell választania a megfelelő jegyzetfüzetelérési utat.

       ![Jegyzetfüzet elérési útja](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Bontsa ki az **Alapparaméterek** választót, és ellenőrizze, hogy a paraméterek megegyeznek-e a következő képernyőképen láthatóval. Ezek a paraméterek a Databricks-jegyzetfüzeta Data Factory.

       ![Alapparaméterek](media/solution-template-Databricks-notebook/base-parameters.png)

1. Ellenőrizze, hogy a **folyamatparaméterei** megegyeznek-e a következő képernyőképen láthatóval: ![Folyamatparaméterei](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Csatlakozzon az adatkészletekhez.

   - **SourceAvailabilityDataset** - annak ellenőrzése, hogy a forrásadatok rendelkezésre állnak-e.

     ![A SourceAvailabilityDataset csatolt szolgáltatásának és fájlelérési útjának kijelölése](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - a forrásadatok eléréséhez.

       ![A SourceFilesDataset csatolt szolgáltatásának és fájlelérési útjának kijelölése](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - az adatok másolása a fogadó célhelyére. Használja a következő értékeket:

     - **Csatolt szolgáltatás** - `sinkBlob_LS`, amely et egy előző lépésben hoztak létre.

     - **A fájl elérési útja** - `sinkdata/staged_sink`.

       ![A DestinationFilesDataset csatolt szolgáltatásának és fájlelérési útjának kijelölése](media/solution-template-Databricks-notebook/destination-dataset.png)

1. A folyamat futtatásához válassza a **Debug** lehetőséget. A Databricks-naplókra mutató hivatkozást részletesebb Spark-naplókért találhatja meg.

    ![Hivatkozás a Databricks naplókhoz a kimenetből](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Az adatfájlt az Azure Storage Explorer használatával is ellenőrizheti.

    > [!NOTE]
    > A Data Factory folyamatfuttatásokkal való korrelációhoz ez a példa hozzáfűzi a folyamat futtatási azonosítóját az adat-előállítóból a kimeneti mappába. Ez segít nyomon követni az egyes futtatások által létrehozott fájlokat.
    > ![Hozzáfűzött folyamat futtatási azonosítója](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)
