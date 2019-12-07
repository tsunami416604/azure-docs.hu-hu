---
title: Az adatátalakítás a Databricks használatával
description: Megtudhatja, hogyan alakíthat át egy megoldást sablon használatával az adatátalakításhoz a Azure Data Factory Databricks-jegyzetfüzettel.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 5b39e354d503910d20141ce19c625eb79b4a7353
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890997"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Az adatátalakítás a Databricks használatával Azure Data Factory

Ebben az oktatóanyagban egy végpontok közötti folyamatot hoz létre, amely tartalmazza a Data Factoryban található, a **Keresés**, a **Másolás**és a **Databricks notebook** -tevékenységeket.

-   A **keresési** vagy GetMetaData tevékenységgel biztosítható, hogy a forrás adatkészlete készen álljon az alsóbb rétegbeli felhasználásra, mielőtt aktiválja a másolási és elemzési feladatot.

-   A **másolási tevékenység** a forrás fájlt/adatkészletet a fogadó tárolóba másolja. A fogadó tároló a Databricks jegyzetfüzetben DBFS van csatlakoztatva, hogy az adatkészletet közvetlenül a Spark tudja használni.

-   A **Databricks notebook tevékenység** elindítja a Databricks jegyzetfüzetet, amely átalakítja az adatkészletet, és hozzáadja azt egy feldolgozott mappához/SQL DW-hez.

A sablon egyszerű megtartásához a sablon nem hoz létre ütemezett triggert. Szükség esetén hozzáadhatja azt.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Előfeltételek

1.  Hozzon létre egy **blob Storage-fiókot** és egy `sinkdata` nevű tárolót **, amelyet**fogadóként kíván használni. Jegyezze fel a Storage- **fiók nevét**, a **tároló nevét**és a **hozzáférési kulcsot**, mivel azok a sablonban később rájuk vannak hivatkozva.

2.  Győződjön meg arról, hogy rendelkezik **Azure Databricks munkaterülettel** , vagy hozzon létre egy újat.

1.  **Importálja a jegyzetfüzetet ETL-hez**. Importálja az alábbi átalakító jegyzetfüzetet a Databricks-munkaterületre. (Nem kell ugyanazon a helyen lennie, mint az alábbi, de ne feledje, hogy a későbbiekben kiválasztott elérési utat kell megadnia.) Importálja a jegyzetfüzetet a következő URL-címről úgy, hogy beírja az URL-címet az URL mezőbe: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Válassza az **Importálás** lehetőséget.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Most frissítse az **átalakítási** jegyzetfüzetet a **Storage kapcsolati adataival** (név és hozzáférési kulcs). Lépjen az **5. parancsra** az importált jegyzetfüzetben, és cserélje le az alábbi kódrészletre a Kiemelt értékek cseréje után. Győződjön meg arról, hogy ez a fiók azonos a korábban létrehozott Storage-fiókkal, és tartalmazza a `sinkdata` tárolót.

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

1.  **Databricks hozzáférési token** létrehozása Data Factory számára a Databricks eléréséhez. **Mentse a hozzáférési jogkivonatot** későbbi használatra a Databricks társított szolgáltatás létrehozásához, amely a következőhöz hasonló: "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Társított szolgáltatások és adatkészletek létrehozása

1.  Hozzon létre új **társított szolgáltatásokat** Data Factory felhasználói felületen a *társított szolgáltatások + új kapcsolattal*

    1.  **Forrás** – a forrásadatok eléréséhez. Használhatja a minta forrásfájljait tartalmazó nyilvános blob Storage-tárolót.

        Válassza a **blob Storage**lehetőséget, majd az alábbi **sas URI** -t használva kapcsolódjon a forrás tárolóhoz (csak olvasási hozzáférés).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  Fogadó **– adatok** másolásához a alkalmazásba.

        Válassza ki az 1. előfeltételben létrehozott tárolót a fogadó társított szolgáltatásban.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – a Databricks-fürthöz való csatlakozáshoz

        Hozzon létre egy Databricks társított szolgáltatást a 2. c előfeltételben létrehozott hozzáférési kulccsal. Ha *interaktív fürttel*rendelkezik, ezt kiválaszthatja. (Ez a példa az *új feladatütemezés* lehetőséget használja.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  **Adatkészletek** létrehozása

    1.  **"SourceAvailability_Dataset"** létrehozása a forrásadatok elérhetőségének vizsgálatához

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Forrás adatkészlet –** a forrásadatok másolásához (bináris másolat használatával)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  Fogadó **adatkészlet** – a fogadó/célhely helyre történő másoláshoz

        1.  Társított szolgáltatás – válassza a "sinkBlob_LS" elemet a következőben: 1. b

        2.  Fájl elérési útja – "sinkdata/staged_sink"

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Tevékenységek létrehozása

1.  Hozzon létre egy "**rendelkezésre állási jelző**" keresési tevékenységet a forrás rendelkezésre állásának vizsgálatához (keresési vagy GetMetaData használható). Válassza a 2. a-ben létrehozott "sourceAvailability_Dataset" lehetőséget.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Hozzon létre egy másolási tevékenységet a**fájl és a blob**között az adatkészlet forrásról fogadóba való másolásához. Ebben az esetben az érték bináris fájl. A másolási tevékenységben a forrás és a fogadó konfigurációjának alábbi képernyőképei szerepelnek.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  **Folyamat paramétereinek** megadása

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Databricks- **tevékenység** létrehozása

    Válassza ki az előző lépésben létrehozott társított szolgáltatást.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Konfigurálja a **beállításokat**. Hozzon létre **alapszintű paramétereket** a képernyőképen látható módon, és hozzon létre paramétereket a Databricks-jegyzetfüzetnek a Data Factoryból való átadásához. Tallózással keresse meg és **válassza ki** a **2. előfeltételben**feltöltött **megfelelő notebook elérési utat** .

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Futtassa a**folyamatot. A Databricks-naplókra mutató hivatkozással részletesebb Spark-naplók találhatók.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Az adatfájlt a Storage Explorer használatával is ellenőrizheti. (Az Data Factory-folyamat futtatásával való korrelációhoz ez a példa hozzáfűzi a folyamat futtatási AZONOSÍTÓját az adatok gyárból a kimeneti mappába. Így nyomon követheti az egyes futtatásokon keresztül generált fájlokat.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Factory bemutatása](introduction.md)
