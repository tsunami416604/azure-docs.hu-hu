---
title: Adatok másolása Azure Data Factoryból az Azure-ba Adatkezelő
description: Ebben a témakörben bemutatjuk, hogyan tölthetők be (betöltési) adatai az Azure Adatkezelőba Azure Data Factory másolási eszköz használatával
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b3bd9b800da4f096639d02c78b718216441621a9
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803980"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Adatmásolás az Azure Adatkezelőba Azure Data Factory használatával 

Az Azure Adatkezelő egy gyors, teljes körűen felügyelt adatelemzési szolgáltatás, amellyel a nagy mennyiségű adatfolyamot, többek között az alkalmazásokat, webhelyeket és IoT-eszközöket is valós időben elemezheti. Iteratív az adatok feltárására és a minták és rendellenességek azonosítására a termékek fejlesztése, a felhasználói élmény fokozása, az eszközök monitorozása és a műveletek fokozása érdekében. Új kérdéseket tehet fel, amelyekre percek alatt választ kaphat. A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatással feltöltheti Azure Adatkezelő adatbázisát a meglévő rendszerből származó adatokkal, és időt takaríthat meg az elemzési megoldások kiépítésekor.

Azure Data Factory a következő előnyöket nyújtja az Azure Adatkezelőba való betöltéshez:

* **Egyszerűen beállítható**: Egy olyan intuitív, öt lépésből álló varázsló, amely nem igényel parancsfájlt.
* **Rich adattár-támogatás**: Beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez. Részletes listát a [támogatott adattárakkal](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)foglalkozó táblázatban talál.
* **Biztonságos és megfelelő**: Az adatátvitel HTTPS-vagy ExpressRoute keresztül történik. A globális szolgáltatás jelenléte biztosítja, hogy az adatai soha nem hagyják el a földrajzi határt.
* **Nagy teljesítmény**: Akár 1 GB/s betöltési sebesség az Azure Adatkezelőba. Részletekért lásd: [másolási tevékenység teljesítménye](/azure/data-factory/copy-activity-performance).

Ez a cikk bemutatja, hogyan tölthetők be az Amazon S3-ből származó adatok az Data Factory Adatok másolása eszközzel az Azure Adatkezelőba. Az adatok más adattárakból (például [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle)és [fájlrendszerből](/azure/data-factory/connector-file-system)) történő másolásához hasonló lépéseket is követheti.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Azure Adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md)
* Adatforrás.

## <a name="create-a-data-factory"></a>data factory létrehozása

1. Válassza az **erőforrás létrehozása** gombot (+) a portál bal felső sarkában > **Analytics** > **Data Factory**.

   ![Új adat-előállító létrehozása](media/data-factory-load-data/create-adf.png)

1. Az **új adat-előállító** lapon adja meg a következő mezők értékeit, majd válassza a **Létrehozás**lehetőséget.

    ![Új adat-előállító lap](media/data-factory-load-data/my-new-data-factory.png)

    **Beállítás**  | **Mező leírása**
    |---|---|
    | **Name** | Adja meg az adatelőállító globálisan egyedi nevét. Ha *" \"a LoadADXDemo\" nem érhető el"* hibaüzenet jelenik meg, adjon meg egy másik nevet az adatelőállítónak. Data Factory összetevők elnevezési szabályait lásd: [Data Factory elnevezési szabályok](/azure/data-factory/naming-rules).|
    | **Előfizetés** | Válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. |
    | **Erőforráscsoport** | Válassza az **új létrehozása** lehetőséget, és adja meg egy új erőforráscsoport nevét. Ha rendelkezik meglévő erőforráscsoporthoz, válassza a **meglévő használata**lehetőséget. |
    | **Verzió** | **V2** kiválasztása |
    | **Location** | Válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adatfeldolgozó által használt adattárak más helyszíneken vagy régiókban is lehetnek. |
    | | |

1. A létrehozási folyamat figyeléséhez válassza az értesítések lehetőséget az eszköztáron. A létrehozás befejezése után nyissa meg a létrehozott adatelőállítót. Megnyílik a **Data Factory** kezdőlapja.

   ![Data factory kezdőlap](media/data-factory-load-data/data-factory-home-page.png)

1. Válassza a **szerző & figyelés** csempét az alkalmazás külön lapon történő elindításához.

## <a name="load-data-into-azure-data-explorer"></a>Betöltés az Azure Adatkezelőba

Az adatok számos különböző típusú [adattárból](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) tölthetők be az Azure Adatkezelőba. Ez a témakör az Amazon S3-ból származó adatok betöltését ismerteti.

Az Azure-Adatkezelő kétféleképpen tölthetők be a Azure Data Factory használatával:

* Azure Data Factory felhasználói felület – [ **Szerző** lap](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* A cikkben használt [Azure Data Factory **adatok másolása** eszköz](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) .

### <a name="copy-data-from-amazon-s3-source"></a>Adatok másolása az Amazon S3-ból (forrás)

1. Az **első lépések** oldalon válassza a **adatok másolása** csempét a adatok másolása eszköz elindításához.

   ![Adatmásolási eszköz csempe](media/data-factory-load-data/copy-data-tool-tile.png)

1. A **Tulajdonságok** lapon adja meg a **feladat nevét** , majd kattintson a **Tovább gombra**.

    ![Másolás forrás tulajdonságaiból](media/data-factory-load-data/copy-from-source.png)

1. A **forrás adattár** lapon kattintson az **+ új kapcsolatok létrehozása**lehetőségre.

    ![Adatforrás-létrehozási kapcsolatok](media/data-factory-load-data/source-create-connection.png)

1. Válassza ki az **Amazon S3**elemet, majd válassza a **Folytatás** lehetőséget.

    ![Új társított szolgáltatás](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Az **új társított szolgáltatás (Amazon S3)** oldalon hajtsa végre a következő lépéseket:

    ![Az Amazon S3 társított szolgáltatásának meghatározása](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Adja meg az új társított szolgáltatás **nevét** .
    * A legördülő listából válassza a **Csatlakozás az Integration Runtime** értékével lehetőséget.
    * Itt adhatja meg a **hozzáférési kulcs azonosítójának** értékét.
    * A **titkos hozzáférési kulcs** értékének megadása.
    * Válassza a **kapcsolat tesztelése** lehetőséget a létrehozott társított szolgáltatás kapcsolatának teszteléséhez.
    * Válassza a **Finish** (Befejezés) elemet.

1. A **forrás adattár** lapon láthatja az új AmazonS31-kapcsolatokat. Kattintson a **Tovább** gombra.

   ![Forrás adattár létrehozott kapcsolatai](media/data-factory-load-data/source-data-store-created-connection.png)

1. A **bemeneti fájl vagy mappa kiválasztása** oldalon:

    1. Keresse meg a másolni kívánt mappát vagy fájlt. Válassza ki a mappát vagy a fájlt.
    1. Szükség szerint válassza ki a másolási viselkedést. A **bináris másolat** megtartása nincs bejelölve.
    1. Kattintson a **Tovább** gombra.

    ![Bemeneti fájl vagy mappa kiválasztása](media/data-factory-load-data/source-choose-input-file.png)

1. A **fájlformátumok beállításai** lapon válassza ki a fájlhoz tartozó beállításokat, és kattintson a **tovább**gombra.

   ![Bemeneti fájl vagy mappa kiválasztása](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Adatmásolás az Azure Adatkezelőba (cél)

Az Azure Adatkezelő új társított szolgáltatás jön létre, hogy átmásolja az adatfájlokat az alább megadott Azure Adatkezelő céltáblaba (fogadóba).

#### <a name="create-the-azure-data-explorer-linked-service"></a>Az Azure Adatkezelő társított szolgáltatás létrehozása

1. A **cél adattár** lapon meglévő adattár-kapcsolatokat használhat, vagy megadhat egy új adattárat az **+ új kapcsolatok létrehozása**lehetőségre kattintva.

    ![Céladattár lap](media/data-factory-load-data/destination-create-connection.png)

1. Az **új társított szolgáltatás** lapon válassza az **Azure adatkezelő**lehetőséget, majd kattintson a **Folytatás** gombra.

    ![Válassza az Azure Adatkezelő – új társított szolgáltatás elemet](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Az **új társított szolgáltatás (Azure adatkezelő)** lapon hajtsa végre a következő lépéseket:

    ![ADX új társított szolgáltatás](media/data-factory-load-data/adx-new-linked-service.png)

   * Válassza ki az Azure Adatkezelő társított szolgáltatás **nevét** .
   * A **fiók kiválasztási módszere**: 
        * Válassza az **Azure-előfizetések** választógombot, és válassza ki az **Azure-előfizetési** fiókját. Ezután válassza ki a **fürtöt**. Vegye figyelembe, hogy a legördülő lista csak a felhasználóhoz tartozó fürtöket sorolja fel.
        * Választhatja azt is, hogy a **manuálisan** választógombot adja meg, és adja meg a **végpontot**.
    * A **bérlő**meghatározása.
    * Adja meg az **egyszerű szolgáltatásnév azonosítóját**.
    * Válassza ki az **egyszerű szolgáltatásnév** gombot, és adja meg az **egyszerű szolgáltatásnév kulcsát**.
    * Válassza ki az **adatbázist** a legördülő menüből. Másik lehetőségként válassza a **Szerkesztés** jelölőnégyzetet, és adja meg az adatbázis nevét.
    * Válassza a **kapcsolat tesztelése** lehetőséget a létrehozott társított szolgáltatás kapcsolatának teszteléséhez. Ha csatlakozni tud a telepítőhöz, akkor a zöld pipa- **kapcsolat sikeresen** megjelenik.
    * Válassza a **Befejezés** lehetőséget a társított szolgáltatás létrehozásának befejezéséhez.

    > [!NOTE]
    > Az egyszerű szolgáltatásnevet a Azure Data Factory használja az Azure Adatkezelő szolgáltatás eléréséhez. Az egyszerű szolgáltatásnév esetében [hozzon létre egy Azure Active Directory (Azure ad) egyszerű szolgáltatást](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Ne használja a **Azure Key Vault** metódust.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Az Azure Adatkezelő adatkapcsolatok konfigurálása

1. Megnyílik a **célhely adattára** . A létrehozott Azure Adatkezelő-adatcsatlakozás elérhető. A kapcsolódás konfigurálásához kattintson a **tovább** gombra.

    ![ADX célhelyének adattára](media/data-factory-load-data/destination-data-store.png)

1. A **táblázat-hozzárendelés**területen állítsa be a céltábla nevét, és kattintson a **Tovább gombra**.

    ![Cél adatkészlet tábla leképezése](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Az **oszlop-hozzárendelés** oldalon:
    * Az első leképezést az ADF az [ADF-séma leképezése](/azure/data-factory/copy-activity-schema-and-type-mapping) alapján végzi el
        * Adja meg az Azure Data Factory céltábla **oszlop-hozzárendeléseit** . Az alapértelmezett leképezés a forrástól az ADF-cél tábláig jelenik meg.
        * Törölje azokat az oszlopokat, amelyek nem szükségesek az oszlopok leképezésének definiálásához.
    * A második leképezés akkor fordul elő, ha a táblázatos adatokat betöltik az Azure Adatkezelőba. A leképezés a [CSV-megfeleltetési szabályoknak](/azure/kusto/management/mappings#csv-mapping)megfelelően lett elvégezve. Vegye figyelembe, hogy még akkor is, ha a forrásadatok nem CSV formátumúak, az ADF táblázatos formátumba alakította át az adatokat, ezért a CSV-megfeleltetés az egyetlen releváns leképezés ebben a fázisban.
        * Az **Azure adatkezelő (Kusto)** alatt a fogadó tulajdonságainál adja meg a megfelelő betöltési **leképezés nevét** (nem kötelező), hogy az oszlopok leképezése használható legyen.
        * Ha nincs megadva a betöltési **leképezés neve** , az **oszlop-hozzárendelések** szakaszban megadott "Name" leképezési sorrend fog történni. Ha a "név szerinti" leképezés nem sikerül, az Azure Adatkezelő megpróbálja bevenni az adatot az "oszlop szerinti pozíció" sorrendben (az alapértelmezett leképezések szerint).
    * Kattintson a **Tovább** gombra.

    ![Cél adatkészlet oszlopának leképezése](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. A **Beállítások** lapon:
    * Adja meg a megfelelő **hibatűrési beállításokat**.
    * **Teljesítmény beállításai**: Az előkészítés engedélyezése nem alkalmazható. A **Speciális beállítások** közé tartoznak a díjszabási szempontok. Ha nem rendelkezik konkrét igényekkel, hagyja ki.
    * Kattintson a **Tovább** gombra.

    ![Adatbeállítások másolása](media/data-factory-load-data/copy-data-settings.png)

1. Az **Összefoglalás**lapon tekintse át a beállításokat, majd kattintson a **Tovább gombra**.

    ![Adatmásolás összefoglalása](media/data-factory-load-data/copy-data-summary.png)

1. A **központi telepítés lapon**:
    * Válassza a **figyelő** lehetőséget a **figyelő** lapra váltáshoz, és tekintse meg a folyamat állapotát (folyamat, hibák és adatfolyamok).
    * Válassza a **folyamat szerkesztése** lehetőséget a társított szolgáltatások, adatkészletek és folyamatok szerkesztéséhez.
    * Válassza a **Befejezés** lehetőséget az adatok másolása feladat befejezéséhez

    ![Üzembe helyezés lap](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a Azure Data Factory [Azure adatkezelő-összekötővel](/azure/data-factory/connector-azure-data-explorer) .

* További információ a társított szolgáltatások, adatkészletek és folyamatok szerkesztéséről a [Data Factory felhasználói felületen](/azure/data-factory/quickstart-create-data-factory-portal).

* Ismerje meg az Azure Adatkezelő Adatlekérdezési [lekérdezéseit](/azure/data-explorer/web-query-data) .
