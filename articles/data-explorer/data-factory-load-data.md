---
title: Adatok másolása az Azure Data Factory az Azure Data Explorer
description: Ebben a témakörben elsajátíthatja (betöltés) adatok betöltését az adatkezelőt az Azure-bA az Azure Data Factory másolás eszköz használatával
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c3c5484d865c73a6f478ffc9ad4c3fc86c2c8170
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59619106"
---
# <a name="copy-data-from-azure-data-factory-to-azure-data-explorer"></a>Adatok másolása az Azure Data Factory az Azure Data Explorer

Az Azure Data Explorer egy gyors, teljes körűen felügyelt elemzési szolgáltatás adatstreamelési számos más forrásból, például alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű valós idejű elemzés céljából. Iteratív elemezheti adatait, és azonosítja a mintákat és rendellenességeket termékek javítása, növelje a vásárlói élményt, eszközök figyelését, és növeli a műveleteket. Új kérdéseket tehet fel, amelyekre percek alatt választ kaphat. Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás az. A szolgáltatás segítségével az Azure Data Explorer adatbázis adatok feltöltéséhez a meglévő rendszerről és időt takaríthat meg az elemzési megoldások készítése során.

Az Azure Data Factory az adatok betöltését az Azure az adatkezelőt az alábbi előnyöket biztosítja:

* **Könnyű beállítás**: Egy intuitív öt lépés varázsló nem parancsfájl-szükséges.
* **Számos adattároló támogatási**: A helyszíni és felhőalapú adattárak számos beépített támogatása. Részletes listáját, tekintse meg a fenti táblázat [támogatott adattárak](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute adatokat továbbítja. A szolgáltatás globális jelenlét biztosítja, hogy az adatok a földrajzi határ sosem hagyja el.
* **Nagy teljesítményű**: Legfeljebb 1 GB/s adatok betöltési sebesség az adatkezelőt az Azure-bA. További információkért lásd: [másolási tevékenység](/azure/data-factory/copy-activity-performance).

Ez a cikk bemutatja, hogyan adatok betöltése az Amazon S3 az Adatkezelőben az Azure Data Factory az adatok másolása eszköz használatával. Például adatok másolása más adattárakban hasonló lépéseket követnie [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), és [fájlrendszer](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Az Azure Data Explorer fürt és adatbázis](create-cluster-database-portal.md)
* Amazon S3.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Válassza ki a **erőforrás létrehozása** (+) gombot a portál bal felső sarkában > **Analytics** > **adat-előállító**.

   ![Új adat-előállító létrehozása](media/data-factory-load-data/create-adf.png)

1. Az a **új adat-előállító** lap, adja meg az alábbi mezők értékeit, majd **létrehozás**.

    ![Új adat-előállító lap](media/data-factory-load-data/my-new-data-factory.png)

    **Beállítás**  | **Mező leírása**
    |---|---|
    | **Name (Név)** | Adja meg a data Factory egy globálisan egyedi nevet. Ha a hibaüzenet *"adat-előállító nevét \"LoadADXDemo\" nem érhető el"*, adja meg a data Factory egy másik nevet. A Data Factory-összetevők elnevezési szabályait, lásd: [Data Factory elnevezési szabályait](/azure/data-factory/naming-rules).|
    | **Előfizetés** | Válassza ki az Azure-előfizetés, amelyben az adat-előállító létrehozásához. |
    | **Erőforráscsoport** | Válassza ki **új létrehozása** , és adja meg egy új erőforráscsoport nevét. Válassza ki **meglévő**, ha rendelkezik egy meglévő erőforráscsoportot. |
    | **Verzió** | Válassza ki **V2** |
    | **Hely** | Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak lehetnek más helyével vagy régiójával. |
    | | |

1. Válassza ki az értesítéseket az eszköztáron a létrehozási folyamat megfigyeléséhez. Létrehozás befejezése után nyissa meg a létrehozott adat-előállítót. A **adat-előállító** kezdőlapjának megnyitása.

   ![Data factory kezdőlap](media/data-factory-load-data/data-factory-home-page.png)

1. Válassza ki a **létrehozás és Monitorozás** csempére kattintva indítsa el az alkalmazás külön lapon.

## <a name="load-data-into-azure-data-explorer"></a>Adatok betöltése az Azure Data Explorer

Lehet adatokat betölteni a számos különböző típusú [adattárak](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) az adatkezelőt az Azure-bA. Ez a témakör ismerteti az adatok betöltésekor az Amazon S3-ból.

Az adatok betöltése az Azure Data Factory használatával az Azure Data Explorer két módja van:

* Az Azure Data Factory felhasználói felületének - [ **Szerző** lap](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Az Azure Data Factory **adatok másolása** eszköz](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) a cikk ezt használja.

### <a name="copy-data-from-amazon-s3-source"></a>Adatok másolása az Amazon S3 (forrás)

1. Az a **első lépések** lapon válassza ki a **adatok másolása** csempére, hogy az adatok másolása eszköz elindításához.

   ![Adatok másolása eszköz csempéje](media/data-factory-load-data/copy-data-tool-tile.png)

1. Az a **tulajdonságok** adja meg azokat **feladat neve** válassza **tovább**.

    ![Másolja ki a forrás tulajdonságai](media/data-factory-load-data/copy-from-source.png)

1. Az a **forrásadattár** kattintson **+ új kapcsolat létrehozása**.

    ![Forrásadatok kapcsolat létrehozása](media/data-factory-load-data/source-create-connection.png)

1. Válassza ki **Amazon S3**, majd válassza ki **Folytatás**

    ![Új társított szolgáltatás](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Az a **új társított szolgáltatás (Amazon S3)** lapon, tegye a következőket:

    ![Adja meg az Amazon S3-beli társított szolgáltatás](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Adja meg **neve** az új társított szolgáltatás.
    * Válassza ki **csatlakozás integrációs modulon keresztül** értéket a legördülő listából.
    * Adja meg a **elérési kulcs Azonosítóját** értéket.
    * Adja meg a **titkos elérési kulcsát** értéket.
    * Válassza ki **kapcsolat tesztelése** létrehozta a társított szolgáltatás kapcsolatának teszteléséhez.
    * Válassza a **Finish** (Befejezés) elemet.

1. Az a **forrásadattár** lapon látni fogja az új AmazonS31 kapcsolat. Kattintson a **Tovább** gombra.

   ![Forrásadatok tárolja a kapcsolat létrehozása](media/data-factory-load-data/source-data-store-created-connection.png)

1. Az a **a bemeneti fájl vagy mappa kiválasztása** oldalon:

    1. Keresse meg a másolni kívánt mappát vagy fájlt. Válassza ki a mappát vagy fájlt.
    1. Válassza ki a másolási viselkedés szükség szerint. Tartsa **bináris másolat** nincs bejelölve.
    1. Kattintson a **Tovább** gombra.

    ![Bemeneti fájl vagy mappa kiválasztása](media/data-factory-load-data/source-choose-input-file.png)

1. Az a **fájlformátum beállításai** oldalon válassza ki a fájlt, majd kattintson a megfelelő beállításainak **tovább**.

   ![Bemeneti fájl vagy mappa kiválasztása](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Adatok másolása az Azure Data Explorer (cél)

Az Azure Data Explorer új társított szolgáltatás másolja az adatokat az Azure Data Explorer céltáblázatba történő (tárolóba fogadó), az alább megadott kell létrehozni.

1. Az a **célként megadott adattárba** , válassza egy meglévő data kapcsolat tárolására, vagy adjon meg egy új adattároló kattintva **+ új kapcsolat létrehozása**.

    ![Céladattár lap](media/data-factory-load-data/destination-create-connection.png)

1. Az a **új társított szolgáltatás** lapon jelölje be **Azure adatkezelő**, majd válassza ki **Folytatás**

    ![Válassza ki az Azure Data Explorer – új társított szolgáltatás](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Az a **új társított szolgáltatás (Azure Data Explorer)** lapon, tegye a következőket:

    ![Új társított szolgáltatás ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Válassza ki **neve** az Adatkezelőben az Azure-beli társított szolgáltatást.
   * A **kijelöléséről fiók**: 
        * Válassza ki a **az Azure-előfizetés** választógombot, és válassza ki a **Azure-előfizetés** fiókot. Ezután válassza ki a **fürt**. Megjegyzés: a listában csak fürtök listázása a felhasználóhoz tartozó lesz.
        * Jelölje ki **adja meg manuálisan** választógombot, és adja meg a **végpont**.
    * Adja meg a **bérlői**.
    * Adja meg **résztvevő-azonosító szolgáltatás**.
    * Válassza ki **szolgáltatásnév kulcsát** gombra, és adja meg **szolgáltatásnév kulcsát**.
    * Válassza ki a **adatbázis** a legördülő menüből. Jelölje ki **szerkesztése** jelölőnégyzetet, és adja meg az adatbázis nevét.
    * Válassza ki **kapcsolat tesztelése** létrehozta a társított szolgáltatás kapcsolatának teszteléséhez. Ha a telepítés, egy zöld pipa csatlakozhat **sikeres csatlakozás** jelenik meg.
    * Válassza ki **Befejezés** társított szolgáltatás létrehozásához.

    > [!NOTE]
    > Egyszerű szolgáltatás Azure Data Factory használják az Azure Data Explorer szolgáltatás eléréséhez. Az egyszerű szolgáltatásnév [egyszerű szolgáltatás létrehozása az Azure Active Directory (Azure AD)](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad). Ne használja a **Azure Key Vault** metódust.

1. A **célként megadott adattárba** nyílik meg. Az Azure Data Explorer létrehozott kapcsolat érhető el használatra. Válassza ki **tovább** a kapcsolat konfigurálásához.

    ![ADX célként megadott adattárba](media/data-factory-load-data/destination-data-store.png)

1. A **a kimeneti fájl vagy mappa kiválasztása**, adja meg a kimeneti mappa nevét, határozza meg a beállításokat, és válassza **tovább**.

    ![Adja meg a kimeneti mappa](media/data-factory-load-data/specify-path.png)

1. A **tábla hozzárendelése**, állítsa be a cél tábla nevét, és válassza ki **tovább**.

    ![Cél adatkészlet tábla hozzárendelése](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Az a **oszlopleképezés** oldalon:
    * Az első leképezés ADF a következők szerint végzi [ADF séma hozzárendelése](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Állítsa be a **oszlop-hozzárendelések** az Azure Data Factory céloldali tábla. Az alapértelmezett leképezést forrásból jelenik meg az ADF céltáblában.
        * Törölje az oszlopokat, amelyeket nem kell az oszlop-hozzárendelés meghatározása.
    * A második leképezés következik be, amikor a táblázatos adatok Azure Data Explorer be lett töltve. Leképezés a következők szerint történik [CSV-leképezési szabályok](/azure/kusto/management/mappings#csv-mapping). Vegye figyelembe, hogy akkor is, ha a forrásadatok nem CSV formátumban, ADF rendelkezik konvertálni az adatokat egy táblázatos formátumra, leképezés CSV ezért ezen a ponton csak megfelelő leképezés.
        * A **Azure Data Explorer (Kusto) fogadó tulajdonságok** adja hozzá a megfelelő **Adatbetöltési megfeleltetés neve** (nem kötelező) úgy, hogy az oszlop-hozzárendelés is használható.
        * Ha **Adatbetöltési megfeleltetés neve** nincs megadva, meghatározott "by-name" leképezési rendelés **oszlop-hozzárendelések** szakaszban történik. Ha "by-name" leképezés nem sikerül, Azure adatkezelő megpróbálja betöltheti az adatokat (maps által-pozíciója az alapértelmezett) "oszlop alapján pozíciója" sorrendben.
    * Válassza ki **tovább**

    ![Cél adatkészlet oszlop-hozzárendelés](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. A **Beállítások** lapon:
    * Állítsa be a megfelelő **tűrését tartalék**.
    * **Teljesítménybeállítások**: Előkészítés engedélyezése nem alkalmazható. **Speciális beállítások** költség szempontok közé tartozik. Hagyja üresen, ha nem adott meg kell.
    * Kattintson a **Tovább** gombra.

    ![Adatok beállítások másolása](media/data-factory-load-data/copy-data-settings.png)

1. A **összefoglalás**, tekintse át a beállításokat, és válassza ki **tovább**.

    ![Összefoglaló adatok másolása](media/data-factory-load-data/copy-data-summary.png)

1. Az a **üzembe helyezés lap**:
    * Válassza ki **figyelő** váltson át a **figyelő** lapra, és megnézheti az állapotukat, a folyamat (folyamatban, a hibák és az adatok folyamat).
    * Válassza ki **folyamat szerkesztése** társított szolgáltatások, adatkészletek és folyamatok szerkesztéséhez.
    * Válassza ki **Befejezés** teljes másolatának adatait tevékenységhez

    ![Üzembe helyezés lap](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>További lépések

* További tudnivalók a társított szolgáltatások, adatkészletek és folyamatok a szerkesztését a [Data Factory felhasználói felülete](/azure/data-factory/quickstart-create-data-factory-portal)

* Ismerje meg [Azure adatkezelő lekérdezések](/azure/data-explorer/web-query-data) adatok lekérdezéséhez.
