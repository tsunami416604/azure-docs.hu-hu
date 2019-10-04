---
title: Adatok másolása Azure Data Factoryból az Azure-ba Adatkezelő
description: Ebből a cikkből megtudhatja, hogyan végezheti el az adatterhelést az Azure Adatkezelőba a Azure Data Factory másolási eszköz használatával.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300599"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Adatmásolás az Azure Adatkezelőba Azure Data Factory használatával 

Az Azure Adatkezelő egy gyors, teljes körűen felügyelt, adatelemzési szolgáltatás. Valós idejű elemzést biztosít nagy mennyiségű, több forrásból, például alkalmazásokból, webhelyekről és IoT származó adatokból. Az Azure Adatkezelő segítségével iteratív az adatok feltárására és a minták és rendellenességek azonosítására a termékek fejlesztése, a felhasználói élmény fokozása, az eszközök figyelése és a műveletek fokozása érdekében. Segít az új kérdések megismerésében és a válaszok percek alatt történő megszerzésében. 

A Azure Data Factory egy teljes körűen felügyelt, felhőalapú, adatintegrációs szolgáltatás. A segítségével feltöltheti az Azure Adatkezelő-adatbázist a meglévő rendszerből származó adatokkal. Hasznos időt takaríthat meg az elemzési megoldások létrehozásakor.

Az Azure Adatkezelőba való betöltéskor a Data Factory a következő előnyöket biztosítja:

* **Egyszerű telepítés**: Egy olyan intuitív, öt lépésből álló varázslót kap, amely nem igényel parancsfájlt.
* **Rich adattár-támogatás**: Beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez. Részletes listát a [támogatott adattárakkal](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)foglalkozó táblázatban talál.
* **Biztonságos és megfelelő**: Az adatátvitel HTTPS-vagy Azure-ExpressRoute keresztül történik. A globális szolgáltatás jelenléte biztosítja, hogy az adatai soha nem hagyják el a földrajzi határt.
* **Nagy teljesítmény**: Az adatfeltöltési sebesség akár 1 gigabájt/másodperc (GBps) az Azure Adatkezelő. További információ: [másolási tevékenység teljesítménye](/azure/data-factory/copy-activity-performance).

Ebben a cikkben a Data Factory Adatok másolása eszköz használatával tölti be az Amazon Simple Storage Service (S3) adatait az Azure Adatkezelőba. Az adatok más adattárakból történő másolásához hasonló eljárást követheti, például:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Fájlrendszer](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Azure adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md).
* Adatforrás.

## <a name="create-a-data-factory"></a>data factory létrehozása

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com).

1. A bal oldali ablaktáblán válassza az **erőforrás** > létrehozása**elemzési** > **Data Factory**elemet.

   ![Hozzon létre egy adatgyárat a Azure Portal](media/data-factory-load-data/create-adf.png)

1. Az **új adat-előállító** ablaktáblán adja meg az alábbi táblázat mezőinek értékeit:

   ![Az "új adatbázis-előállító" panel](media/data-factory-load-data/my-new-data-factory.png)  

   | Beállítás  | ENTER érték  |
   |---|---|
   | **Name** | A mezőbe írja be az adatelőállító globálisan egyedi nevét. Ha hibaüzenetet kap, a *LoadADXDemo \"\" név nem érhető el*, adjon meg egy másik nevet az adatelőállítónak. A Data Factory összetevők elnevezésével kapcsolatos szabályokért lásd: [Data Factory elnevezési szabályok](/azure/data-factory/naming-rules).|
   | **Előfizetés** | A legördülő listában válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. |
   | **Erőforráscsoport** | Válassza az **új létrehozása**lehetőséget, majd adja meg az új erőforráscsoport nevét. Ha már rendelkezik erőforráscsoporthoz, válassza a **meglévő használata**lehetőséget. |
   | **Verzió** | A legördülő listában válassza a **v2**elemet. |  
   | **Location** | A legördülő listában válassza ki az adatelőállító helyét. A listában csak a támogatott helyszínek jelennek meg. Az adatfeldolgozó által használt adattárak más helyszíneken vagy régiókban is létezhetnek. |

1. Kattintson a **Létrehozás** gombra.

1. A létrehozási folyamat figyeléséhez kattintson az **értesítések** elemre az eszköztáron. Miután létrehozta az adatelőállítót, jelölje ki.
   
   Megnyílik a **Data Factory** panel.

   ![A Data Factory panel](media/data-factory-load-data/data-factory-home-page.png)

1. Ha az alkalmazást külön ablaktáblában szeretné megnyitni, válassza a **szerző & figyelés** csempét.

## <a name="load-data-into-azure-data-explorer"></a>Betöltés az Azure Adatkezelőba

Az Azure Adatkezelőba számos különböző típusú [adattárolóból](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) tölthet be adatmennyiséget. Ez a cikk azt ismerteti, hogyan tölthetők be az Amazon S3-ból származó adatok.

Az adatait az alábbi módokon töltheti be:

* A Azure Data Factory felhasználói felületen, a bal oldali ablaktáblán válassza a **Szerző** ikont, ahogyan az az [adatelőállító létrehozása a Azure Data Factory felhasználói felület használatával](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)című szakaszban látható.
* Az Azure Data Factory Adatok másolása eszközben, ahogy az a [adatok másolása eszköz használata az adatmásoláshoz](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)című ábrán látható.

### <a name="copy-data-from-amazon-s3-source"></a>Adatok másolása az Amazon S3-ból (forrás)

1. Az **első lépések** ablaktáblán nyissa meg a adatok másolása eszközt a **adatok másolása**lehetőség kiválasztásával.

   ![A Adatok másolása eszköz gomb](media/data-factory-load-data/copy-data-tool-tile.png)

1. A **Tulajdonságok** ablaktábla **feladat neve** mezőjében adjon meg egy nevet, majd kattintson a **tovább**gombra.

    ![A Adatok másolása Tulajdonságok panel](media/data-factory-load-data/copy-from-source.png)

1. A **forrás adattár** ablaktáblán válassza az **új kapcsolatok létrehozása**lehetőséget.

    ![A "forrás adattár" panel Adatok másolása](media/data-factory-load-data/source-create-connection.png)

1. Válassza az **Amazon S3**elemet, majd kattintson a **Folytatás**gombra.

    ![Az új társított szolgáltatás ablaktábla](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Az **új társított szolgáltatás (Amazon S3)** panelen tegye a következőket:

    ![Az Amazon S3 társított szolgáltatásának meghatározása](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. A **név** mezőbe írja be az új társított szolgáltatás nevét.

    b. A **Csatlakozás Integration Runtime** legördülő listából válassza ki az értéket.

    c. A **hozzáférési kulcs azonosítója** mezőben adja meg az értéket.
    
    > [!NOTE]
    > Az Amazon S3-ban keresse meg az Ön hozzáférési kulcsát, válassza ki az Amazon-felhasználónevet a navigációs sávon, majd válassza a **saját biztonsági hitelesítő adatok**lehetőséget.
    
    d. A **titkos elérési kulcs** mezőbe írjon be egy értéket.

    e. A létrehozott társított szolgáltatás kapcsolatának teszteléséhez válassza a **kapcsolat tesztelése**elemet.

    f. Válassza a **Finish** (Befejezés) elemet.
    
      A **forrás adattár** ablaktáblán az új AmazonS31-kapcsolatok láthatók. 

1. Kattintson a **Tovább** gombra.

   ![Forrás adattár létrehozott kapcsolatai](media/data-factory-load-data/source-data-store-created-connection.png)

1. A **bemeneti fájl vagy mappa kiválasztása** panelen tegye a következőket:

    a. Keresse meg a másolni kívánt fájlt vagy mappát, majd jelölje ki.

    b. Válassza ki a kívánt másolási viselkedést. Győződjön meg arról, hogy a **bináris másolás** jelölőnégyzet be van jelölve.

    c. Kattintson a **Tovább** gombra.

    ![Bemeneti fájl vagy mappa kiválasztása](media/data-factory-load-data/source-choose-input-file.png)

1. A fájlformátum **beállításai** ablaktáblán válassza ki a megfelelő beállításokat a fájlhoz. majd válassza a **tovább**lehetőséget.

   ![A "fájlformátum beállításai" panel](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Adatmásolás az Azure Adatkezelőba (cél)

Az új Azure Adatkezelő társított szolgáltatás azért jön létre, hogy az ebben a szakaszban megadott Azure Adatkezelő-célhelybe (fogadóba) másolja az adatmásolt fájlokat.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Az Azure Adatkezelő társított szolgáltatás létrehozása

Az Azure Adatkezelő társított szolgáltatás létrehozásához tegye a következőket:

1. Meglévő adattár-kapcsolatok használatához vagy új adattár megadásához válassza az **új kapcsolatok létrehozása**lehetőséget a **cél adattár** ablaktáblán.

    ![Célhely adattárának ablaktáblája](media/data-factory-load-data/destination-create-connection.png)

1. Az **új társított szolgáltatás** ablaktáblán válassza az **Azure adatkezelő**lehetőséget, majd kattintson a **Folytatás**gombra.

    ![Az új társított szolgáltatás ablaktábla](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Az **új társított szolgáltatás (Azure adatkezelő)** panelen tegye a következőket:

    ![Az Azure Adatkezelő új társított szolgáltatás panelje](media/data-factory-load-data/adx-new-linked-service.png)

   a. A **név** mezőbe írja be az Azure adatkezelő társított szolgáltatás nevét.

   b. A **fiók kiválasztása módszernél**hajtsa végre az alábbi műveletek egyikét: 

    * Válassza ki az **Azure-előfizetést** , majd a legördülő listában válassza ki az **Azure-előfizetését** és a **fürtjét**. 

        > [!NOTE]
        > A **fürt** legördülő lista csak azokat a fürtöket sorolja fel, amelyek az előfizetéséhez vannak társítva.

    * Válassza az **ENTER manuálisan**lehetőséget, majd adja meg a **végpontot**.

   c. A **bérlő** mezőbe írja be a bérlő nevét.

   d. Az **egyszerű szolgáltatásnév azonosítója** mezőben adja meg az egyszerű szolgáltatásnév azonosítóját.

   e. Válassza ki az **egyszerű szolgáltatásnév kulcsot** , majd a **szolgáltatásnév kulcs** mezőjébe írja be a kulcs értékét.

   f. Az **adatbázis** legördülő listában válassza ki az adatbázis nevét. Másik lehetőségként jelölje be a **Szerkesztés** jelölőnégyzetet, majd adja meg az adatbázis nevét.

   g. A létrehozott társított szolgáltatás kapcsolatának teszteléséhez válassza a **kapcsolat tesztelése**elemet. Ha kapcsolódhat a társított szolgáltatáshoz, akkor az ablaktábla zöld pipa jelenik meg, és a **sikeres kapcsolat** üzenet jelenik meg.

   h. A társított szolgáltatás létrehozásának befejezéséhez válassza a **Befejezés** lehetőséget.

    > [!NOTE]
    > Az egyszerű szolgáltatásnevet a Azure Data Factory használja az Azure Adatkezelő szolgáltatás eléréséhez. Egyszerű szolgáltatásnév létrehozásához nyissa meg az [Azure Active Directory (Azure ad) egyszerű szolgáltatásnév létrehozását](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Ne használja a Azure Key Vault metódust.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Az Azure Adatkezelő adatkapcsolatok konfigurálása

A társított szolgáltatás kapcsolatának létrehozása után megnyílik a **célhely adattár** ablaktáblája, és a létrehozott kapcsolat használható. A kapcsolódás konfigurálásához tegye a következőket:

1. Kattintson a **Tovább** gombra.

    ![Az Azure Adatkezelő "rendeltetési adattár" panel](media/data-factory-load-data/destination-data-store.png)

1. A **tábla leképezése** ablaktáblán állítsa be a céltábla nevét, majd kattintson a **tovább**gombra.

    ![A cél adatkészlet "tábla leképezése" panelje](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Az **oszlop-hozzárendelés** ablaktáblán a következő leképezések kerülnek érvénybe:

    a. Az első leképezést Azure Data Factory a [Azure Data Factory séma leképezésének](/azure/data-factory/copy-activity-schema-and-type-mapping)megfelelően hajtja végre. Tegye a következőket:

    * Adja meg az Azure Data Factory céltábla **oszlop-hozzárendeléseit** . Az alapértelmezett leképezés megjelenik a forrástól a Azure Data Factory cél tábláig.

    * Szakítsa meg azon oszlopok kijelölését, amelyekre nincs szüksége az oszlop leképezésének definiálásához.

    b. A második leképezés akkor fordul elő, ha a táblázatos adatokat betöltik az Azure Adatkezelőba. A leképezés a [CSV-megfeleltetési szabályoknak](/azure/kusto/management/mappings#csv-mapping)megfelelően lett elvégezve. Ha a forrásadatok nem CSV formátumúak, akkor a Azure Data Factory táblázatos formátumúvá alakítja át az adatokat. Ezért a CSV-megfeleltetés az egyetlen releváns leképezés ebben a fázisban. Tegye a következőket:

    * Választható Az **Azure adatkezelő (Kusto)** -fogadó tulajdonságai területen adja hozzá a megfelelő betöltési **leképezés nevét** , hogy az oszlop leképezése használható legyen.

    * Ha nincs megadva a *betöltési* **leképezés neve** , a rendszer az **oszlop-hozzárendelések** szakaszban megadott név szerinti megfeleltetési sorrendet fogja használni. Ha a *név szerinti* leképezés nem sikerül, az Azure adatkezelő megkísérli bevenni az adatot egy *oszlop szerinti pozícióban* (azaz az alapértelmezés szerint a következőt adja meg).

    * Kattintson a **Tovább** gombra.

    ![A cél adatkészlet "oszlop-hozzárendelés" panelje](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. A **Beállítások** panelen tegye a következőket:

    a. A **hibatűrési beállítások**területen adja meg a megfelelő beállításokat.

    b. A **teljesítmény beállításai**területen **engedélyezze az átmeneti tárolást** , és a **Speciális beállítások** között a Cost szempontok is érvényesek. Ha nem rendelkezik konkrét követelményekkel, hagyja meg ezeket a beállításokat.

    c. Kattintson a **Tovább** gombra.

    ![A "beállítások másolása" panel](media/data-factory-load-data/copy-data-settings.png)

1. Az **Összefoglalás** ablaktáblán tekintse át a beállításokat, majd kattintson a **tovább**gombra.

    ![Az "összefoglalás" ablaktábla másolási adatai](media/data-factory-load-data/copy-data-summary.png)

1. Az **üzembe helyezés befejezése** panelen tegye a következőket:

    a. Ha váltani szeretne a **figyelő** lapra, és megtekinti a folyamat állapotát (azaz az előrehaladást, a hibákat és az adatfolyamot), válassza a **figyelő**lehetőséget.

    b. A társított szolgáltatások, adatkészletek és folyamatok szerkesztéséhez válassza a **folyamat szerkesztése**lehetőséget.

    c. Válassza a **Befejezés** lehetőséget az adatok másolása feladat befejezéséhez.

    ![Az "üzembe helyezés befejezése" panel](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a Azure Data Factory [Azure adatkezelő-összekötővel](/azure/data-factory/connector-azure-data-explorer) .
* További információ a társított szolgáltatások, adatkészletek és folyamatok szerkesztéséről a [Data Factory felhasználói felületen](/azure/data-factory/quickstart-create-data-factory-portal).
* Ismerje meg az Azure Adatkezelő Adatlekérdezési [lekérdezéseit](/azure/data-explorer/web-query-data) .
