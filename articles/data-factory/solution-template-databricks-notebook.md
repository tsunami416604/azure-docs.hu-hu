---
title: Átalakítás Azure Databricks
description: Megtudhatja, hogyan alakíthat át egy megoldást sablon használatával az adatátalakításhoz a Azure Data Factory Databricks-jegyzetfüzettel.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227514"
---
# <a name="transformation-with-azure-databricks"></a>Átalakítás Azure Databricks

Ebben az oktatóanyagban egy végpontok közötti folyamatot hoz létre, amely az **érvényesítési**, a **másolási**és a **Jegyzetfüzet** -tevékenységeket tartalmazza Data Factoryban.

-   Az **érvényesítési** tevékenység segítségével biztosítható, hogy a forrás-adatkészlet a másolási és elemzési feladatok elindítása előtt készen álljon az alsóbb rétegbeli felhasználásra.

-   A **másolási** tevékenység a forrás fájlt/adatkészletet a fogadó tárolóba másolja. A fogadó tároló a Databricks jegyzetfüzetben DBFS van csatlakoztatva, hogy az adatkészletet közvetlenül a Spark tudja használni.

-   A **Databricks notebook** tevékenység elindítja a Databricks jegyzetfüzetet, amely átalakítja az adatkészletet, és hozzáadja azt egy feldolgozott mappához/SQL DW-hez.

A sablon egyszerű megtartásához a sablon nem hoz létre ütemezett triggert. Szükség esetén hozzáadhatja azt.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Előfeltételek

1. Hozzon létre egy **blob Storage-fiókot** és egy `sinkdata` nevű tárolót **, amelyet**fogadóként kíván használni. Jegyezze fel a Storage- **fiók nevét**, a **tároló nevét**és a **hozzáférési kulcsot**, mivel azok a sablonban később rájuk vannak hivatkozva.

2. Győződjön meg arról, hogy rendelkezik **Azure Databricks munkaterülettel** , vagy hozzon létre egy újat.

3. **Importálja a jegyzetfüzetet az átalakításhoz**. 
    1. A Azure Databricks a következő képernyőképeket az **átalakítási** jegyzetfüzet Databricks-munkaterületre történő importálásához. Nem kell ugyanazon a helyen lennie, mint az alábbi, de ne feledje, hogy a későbbiekben kiválasztott elérési utat kell megadnia.
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. Válassza az "Importálás innen: **URL**" lehetőséget, és írja be a következő URL-címet a szövegmezőbe:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. Most frissítse az **átalakítási** jegyzetfüzetet a Storage-kapcsolatok adataival. Nyissa meg az **5. parancsot** (az alábbi kódrészletben látható módon) az importált jegyzetfüzetben, és cserélje le `<storage name>`és `<access key>` a saját Storage-kapcsolódási adataival. Győződjön meg arról, hogy ez a fiók azonos a korábban létrehozott Storage-fiókkal, és tartalmazza a `sinkdata` tárolót.

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

5.  **Databricks hozzáférési token** létrehozása Data Factory számára a Databricks eléréséhez. **Mentse a hozzáférési jogkivonatot** későbbi használatra a Databricks társított szolgáltatás létrehozásához, amely a következőhöz hasonló: "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>A sablon használata

1.  Nyissa meg az **átalakítást Azure Databricks** sablonnal. Hozzon létre új társított szolgáltatásokat a következő kapcsolatokhoz. 
    
    ![Kapcsolatok beállítása](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Forrás blob-kapcsolatok** – a forrásadatok eléréséhez. 
        
        Használhatja a minta forrásfájljait tartalmazó nyilvános blob Storage-tárolót. Az alábbi képernyőkép a konfiguráláshoz. Az alábbi **sas URL-cím** használata a forrás tárterülethez való kapcsolódáshoz (csak olvasási hozzáférés): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Cél blob-kapcsolatok** – adatok másolásához a alkalmazásba. 
        
        A fogadó társított szolgáltatásban válassza ki az 1. **Előfeltételben** létrehozott tárolót.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** – a Databricks-fürthöz való csatlakozáshoz.

        Hozzon létre egy Databricks társított szolgáltatást a 2. c **Előfeltételben** létrehozott hozzáférési kulccsal. Ha *interaktív fürttel*rendelkezik, ezt kiválaszthatja. (Ez a példa az *új feladatütemezés* lehetőséget használja.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. Válassza a **sablon használata**lehetőséget, és egy, az alább látható módon létrehozott folyamat jelenik meg:
    
    ![Folyamat létrehozása](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>A folyamat bevezetése és konfigurálása

Az új folyamat létrehozásakor a legtöbb beállítás automatikusan konfigurálva van az alapértelmezett értékekkel. Tekintse át a konfigurációkat, és szükség esetén frissítse a beállításokat a saját beállításainak megfelelően. Részletekért tekintse meg az alábbi utasításokat és képernyőképeket a hivatkozáshoz.

1.  Egy érvényesítési tevékenység **rendelkezésre állási jelzője** jön létre a forrás rendelkezésre állásának ellenőrzéséhez. Az előző lépésben létrehozott *SourceAvailabilityDataset* adatkészletként van kiválasztva.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  A másolási tevékenység **fájl – blob** , amely az adatkészlet forrásról fogadóba való másolását hozza létre. A másolási tevékenységben a forrás és a fogadó konfigurációjának alábbi képernyőképei szerepelnek.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Létrejön egy jegyzetfüzet-tevékenység **átalakítása** , és az előző lépésben létrehozott társított szolgáltatás van kiválasztva.
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. Válassza a **Beállítások** fület. A *Jegyzetfüzet elérési útja*esetén a sablon alapértelmezés szerint definiál egy útvonalat. Előfordulhat, hogy a 2. **Előfeltételben** feltöltött megfelelő notebook-elérési utat kell megkeresnie és kiválasztania. 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. Tekintse meg a képernyőképen látható *Alap paramétereket* . Ezeket át kell adni a Databricks notebookjának Data Factory. 

         ![Alapparaméterek](media/solution-template-Databricks-notebook/base-parameters.png)

1.  A **folyamat paramétereinek** meghatározása az alábbi módon történik.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. Az adatkészletek beállítása.
    1.  A rendszer létrehozta a **SourceAvailabilityDataset** , hogy ellenőrizze, hogy rendelkezésre állnak-e a forrásadatok.

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** – a forrásadatok másolásához.

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** – a fogadó/célhely helyre történő másoláshoz.

        1.  Társított szolgáltatás – *sinkBlob_LS* az előző lépésben létrehozva.

        2.  Fájl elérési útja – *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  Válassza a **hibakeresés** lehetőséget a folyamat futtatásához. A Databricks-naplókra mutató hivatkozással részletesebb Spark-naplók találhatók.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Az adatfájlt a Storage Explorer használatával is ellenőrizheti. (Az Data Factory-folyamat futtatásával való korrelációhoz ez a példa hozzáfűzi a folyamat futtatási AZONOSÍTÓját az adatok gyárból a kimeneti mappába. Így nyomon követheti az egyes futtatásokon keresztül generált fájlokat.)

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Factory bemutatása](introduction.md)
