---
title: Adatok másolása az Azure Data Factoryból az Azure Data Explorerbe
description: Ebben a cikkben megtudhatja, hogyan töltheti be (töltheti be) az adatokat az Azure Data Data Explorerbe az Azure Data Factory másolási eszköz használatával.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 8e17a004ff866f3915000fb72b6770757062cf83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422919"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Adatok másolása az Azure Data Explorerbe az Azure Data Factory használatával 

Az Azure Data Explorer egy gyors, teljes körűen felügyelt adatelemzési szolgáltatás. Valós idejű elemzést kínál nagy mennyiségű adatról, amelyek számos forrásból, például alkalmazásokból, webhelyekről és IoT-eszközökből származnak. Az Azure Data Explorer segítségével iteratív módon felderítheti az adatokat, és azonosíthatja a mintákat és anomáliákat a termékek javítása, az ügyfelek élményének javítása, az eszközök figyelése és a műveletek kiemelése érdekében. Segít felfedezni az új kérdéseket, és percek alatt választ kapni. 

Az Azure Data Factory egy teljes körűen felügyelt, felhőalapú adatintegrációs szolgáltatás. Segítségével feltöltheti az Azure Data Explorer-adatbázist a meglévő rendszerből származó adatokkal. Ez segít időt takaríthat meg, amikor elemzési megoldásokat épít.

Amikor adatokat tölt be az Azure Data Explorerbe, a Data Factory a következő előnyöket biztosítja:

* **Egyszerű beállítás:** Szerezzen be egy intuitív, ötlépéses varázslót, amely nem szükséges parancsfájlok futtatására.
* **Bővített adattár-támogatás:** Beépített támogatást kaphat a helyszíni és felhőalapú adattárak gazdag készletéhez. A részletes listát a [Támogatott adattárak](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)táblázatában található.
* **Biztonságos és megfelelő:** Az adatok átvitele HTTPS-en vagy Azure ExpressRoute-on keresztül történik. A globális szolgáltatásjelenlét biztosítja, hogy az adatok soha nem hagyja el a földrajzi határt.
* **Nagy teljesítmény:** Az adatok betöltési sebessége akár 1 gigabájt másodpercenként (GBps) az Azure Data Explorer. További információt a [Tevékenység teljesítményének másolása](/azure/data-factory/copy-activity-performance)című témakörben talál.

Ebben a cikkben a Data Factory Copy Data eszközzel tölthet be adatokat az Amazon Simple Storage Service (S3) az Azure Data Explorer be. Hasonló eljárást követhet más adattárakból, például a következőkből:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL adattárház](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Fájlrendszer](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Egy Azure Data Explorer-fürt és -adatbázis.](create-cluster-database-portal.md)
* Adatforrás.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://ms.portal.azure.com)

1. A bal oldali ablaktáblában válassza az **Erőforrás-elemzési** > **Analytics** > **adatgyár**létrehozása lehetőséget.

   ![Adatgyár létrehozása az Azure Portalon](media/data-factory-load-data/create-adf.png)

1. Az **Új adat-előállító** ablaktáblában adja meg az alábbi táblázat mezőinek értékeit:

   ![Az "Új adatgyár" ablaktábla](media/data-factory-load-data/my-new-data-factory.png)  

   | Beállítás  | Beírandó érték  |
   |---|---|
   | **Név** | A mezőbe írja be az adatgyár globálisan egyedi nevét. Ha hibaüzenetet kap, *a \"Data factory\" name LoadADXDemo nem érhető el,* adjon meg egy másik nevet az adat-előállítónak. A Data Factory-összetevők elnevezésére vonatkozó szabályokat a Data Factory elnevezési szabályai című témakörben [tésszet.](/azure/data-factory/naming-rules)|
   | **Előfizetés** | A legördülő listában válassza ki azt az Azure-előfizetést, amelyben létre szeretné hozni az adat-előállítót. |
   | **Erőforráscsoport** | Válassza **az Új létrehozása**lehetőséget, majd írja be egy új erőforráscsoport nevét. Ha már rendelkezik erőforráscsoporttal, válassza **a Meglévő használata**lehetőséget. |
   | **Verzió** | A legördülő listában válassza a **V2**lehetőséget. |    
   | **Helyen** | A legördülő listában válassza ki az adat-előállító helyét. A listában csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak létezhetnek más helyeken vagy régiókban. |

1. Kattintson a **Létrehozás** gombra.

1. A létrehozási folyamat figyeléséhez válassza az **Értesítések** lehetőséget az eszköztáron. Miután létrehozta az adat-előállítót, jelölje ki.
   
   Megnyílik **a Data Factory** ablaktábla.

   ![Az Adatgyár ablaktábla](media/data-factory-load-data/data-factory-home-page.png)

1. Ha az alkalmazást külön ablaktáblában szeretné megnyitni, jelölje be a **Author & Monitor** csempét.

## <a name="load-data-into-azure-data-explorer"></a>Adatok betöltése az Azure Data Explorerbe

Az Azure Data Explorerbe sokféle [adattárból](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) tölthet be adatokat. Ez a cikk bemutatja, hogyan tölthet be adatokat az Amazon S3-ból.

Az adatokat az alábbi módokon töltheti be:

* Az Azure Data Factory felhasználói felületén a bal oldali ablaktáblában válassza a **Szerző** ikont. Ez az [Azure Data Factory felhasználói felületének használatával az adatgyár létrehozása](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)című szakasz "Adatgyár létrehozása" című részében látható.
* Az Azure Data Factory Copy Data eszközben, [ahogy az Adatok másolása eszköz](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)ben látható az adatok másolása.

### <a name="copy-data-from-amazon-s3-source"></a>Adatok másolása az Amazon S3-ról (forrás)

1. Az **Első lépések** ablaktáblán nyissa meg az Adatok másolása eszközt az **Adatok másolása**lehetőség kiválasztásával.

   ![Az Adatok másolása eszköz gombja](media/data-factory-load-data/copy-data-tool-tile.png)

1. A **Tulajdonságok** ablaktábla **Tevékenység neve** mezőjébe írjon be egy nevet, majd válassza a **Tovább**gombot.

    ![Az Adatok másolása tulajdonságainak ablaktábla](media/data-factory-load-data/copy-from-source.png)

1. A **Forrás adattár** ablaktáblán válassza **az Új kapcsolat létrehozása lehetőséget.**

    ![Az adatok másolása "Forrásadattár" ablaktábla](media/data-factory-load-data/source-create-connection.png)

1. Válassza **az Amazon S3**lehetőséget, majd a **Folytatás**lehetőséget.

    ![Az új csatolt szolgáltatás ablaktábla](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Az **Új csatolt szolgáltatás (Amazon S3)** ablaktáblán tegye a következőket:

    ![Amazon S3 csatolt szolgáltatás megadása](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. A **Név** mezőbe írja be az új csatolt szolgáltatás nevét.

    b. A **Connect via integrációs futásidejű** legördülő listában válassza ki az értéket.

    c. Az **Access Key ID** mezőbe írja be az értéket.
    
    > [!NOTE]
    > Az Amazon S3-ban a hozzáférési kulcs megkereséséhez válassza ki az Amazon-felhasználónevét a navigációs sávon, majd válassza a **Saját biztonsági hitelesítő adatok lehetőséget**.
    
    d. A **Titkos elérési kulcs** mezőbe írjon be egy értéket.

    e. A létrehozott csatolt szolgáltatáskapcsolat teszteléséhez válassza a **Kapcsolat tesztelése**lehetőséget.

    f. Válassza a **Finish** (Befejezés) elemet.
    
      A **Forrás adattár** ablaktábla megjeleníti az új AmazonS31-kapcsolatot. 

1. Válassza a **Tovább lehetőséget.**

   ![A forrásadattár létrehozott kapcsolata](media/data-factory-load-data/source-data-store-created-connection.png)

1. A **Beviteli fájl vagy mappa kiválasztása** ablaktáblában tegye a következő lépéseket:

    a. Tallózással keresse meg a másolni kívánt fájlt vagy mappát, majd jelölje ki.

    b. Válassza ki a kívánt másolási viselkedést. Győződjön meg arról, hogy a **Bináris példány** jelölőnégyzet nincs bejelölve.

    c. Válassza a **Tovább lehetőséget.**

    ![Bemeneti fájl vagy mappa kiválasztása](media/data-factory-load-data/source-choose-input-file.png)

1. A **Fájlformátum beállításai** ablaktáblán adja meg a fájlhoz tartozó beállításokat. majd válassza a **Tovább**gombot.

   ![A "Fájlformátum beállításai" ablaktábla](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Adatok másolása az Azure Data Explorerbe (célhely)

Az új Azure Data Explorer kapcsolt szolgáltatás jön létre az adatok másolása az Azure Data Explorer céltábla (fogadó), amely ebben a szakaszban meghatározott.

> [!NOTE]
> Az Azure Data Factory parancstevékenység használatával futtathatja az [Azure Data Explorer vezérlőparancsait,](data-factory-command-activity.md) és használhatja a [lekérdezési parancsokból beadott adatok](/azure/kusto/management/data-ingestion/ingest-from-query)bármelyikét, például `.set-or-replace`a.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Az Azure Data Explorer csatolt szolgáltatásának létrehozása

Az Azure Data Explorer kapcsolt szolgáltatásának létrehozásához tegye a következő lépéseket:

1. Meglévő adattár-kapcsolat használatához vagy új adattár megadásához a **Cél adattár** ablaktábláján válassza az **Új kapcsolat létrehozása lehetőséget.**

    ![Céladattár ablaktábla](media/data-factory-load-data/destination-create-connection.png)

1. Az **Új csatolt szolgáltatás** ablaktáblán válassza az **Azure Data Explorer**lehetőséget, majd kattintson a Folytatás **gombra.**

    ![Az Új csatolt szolgáltatás ablaktábla](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Az **Új csatolt szolgáltatás (Azure Data Explorer)** ablaktáblán tegye a következő lépéseket:

    ![Az Azure Data Explorer új csatolt szolgáltatás ablaktábla](media/data-factory-load-data/adx-new-linked-service.png)

   a. A **Név** mezőbe írja be az Azure Data Explorer kapcsolt szolgáltatás nevét.

   b. A **Fiókkiválasztása csoportban**válasszon az alábbi lehetőségek közül: 

    * Válassza **az Azure-előfizetésből** lehetőséget, majd a legördülő listákban válassza ki az **Azure-előfizetést** és a **fürtöt.** 

        > [!NOTE]
        > A **Fürt** legördülő vezérlő csak az előfizetéshez társított fürtöket sorolja fel.

    * Válassza **a Manuális bevitel**lehetőséget, majd adja meg a **végpontot.**

   c. A **Bérlő** mezőbe írja be a bérlő nevét.

   d. Az **egyszerű szolgáltatásazonosító** mezőbe írja be az egyszerű szolgáltatásazonosítót.

   e. Válassza **az Egyszerű szolgáltatáskulcsot,** majd a **Szolgáltatásegyszerű kulcs** mezőben adja meg a kulcs értékét.

   f. Az **Adatbázis** legördülő listában válassza ki az adatbázis nevét. Másik lehetőségként jelölje be a **Szerkesztés jelölőnégyzetet,** majd írja be az adatbázis nevét.

   g. A létrehozott csatolt szolgáltatáskapcsolat teszteléséhez válassza a **Kapcsolat tesztelése**lehetőséget. Ha tud csatlakozni a csatolt szolgáltatáshoz, az ablaktáblán zöld pipa és sikeres kapcsolat üzenet **jelenik meg.**

   h. A csatolt szolgáltatás létrehozásának befejezéséhez válassza a **Befejezés** lehetőséget.

    > [!NOTE]
    > Az egyszerű szolgáltatás az Azure Data Factory által az Azure Data Explorer szolgáltatás eléréséhez. Egyszerű szolgáltatás létrehozásához nyissa [meg az Azure Active Directory (Azure AD) egyszerű szolgáltatásának létrehozását.](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Ne használja az Azure Key Vault metódust.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Az Azure Data Explorer adatkapcsolatának konfigurálása

A csatolt szolgáltatáskapcsolat létrehozása után megnyílik a **Cél adattár** ablaktábla, és a létrehozott kapcsolat használható. A kapcsolat konfigurálásához tegye a következő lépéseket:

1. Válassza a **Tovább lehetőséget.**

    ![Az Azure Data Explorer "Céladattár" ablaktábla](media/data-factory-load-data/destination-data-store.png)

1. A **Táblaleképezés** ablaktáblában állítsa be a céltábla nevét, majd kattintson a **Tovább gombra.**

    ![A céladatkészlet "Táblaleképezés" ablaktáblája](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Az **Oszlopleképezés** ablaktáblán a következő leképezések történnek:

    a. Az első leképezést az Azure Data Factory hajtja végre az [Azure Data Factory sémaleképezése](/azure/data-factory/copy-activity-schema-and-type-mapping)szerint. Tegye a következőket:

    * Állítsa be az **Oszlop-leképezéseket** az Azure Data Factory céltáblához. Az alapértelmezett leképezés jelenik meg a forrástól az Azure Data Factory céltábla.

    * Szakítsa meg azon oszlopok kijelölését, amelyeknek nem kell meghatároznia az oszlopleképezést.

    b. A második leképezés akkor történik, amikor ez a táblázatos adatok az Azure Data Explorer. A leképezés a [CSV leképezési szabályai](/azure/kusto/management/mappings#csv-mapping)szerint történik. Még akkor is, ha a forrásadatok nem CSV formátumúak, az Azure Data Factory táblázatos formátumba konvertálja az adatokat. Ezért a CSV-leképezés az egyetlen releváns leképezés ebben a szakaszban. Tegye a következőket:

    * (Nem kötelező) Az **Azure Data Explorer (Kusto) sink tulajdonságai,** adja hozzá a megfelelő **betöltési leképezési nevet,** hogy az oszlop leképezés használható.

    * Ha **a betöltési leképezés neve** nincs megadva, a rendszer az Oszlopleképezések szakaszban meghatározott név szerint **leképezési sorrendet** használja. *by-name* Ha *a név szerint* leképezés sikertelen, az Azure Data Explorer megpróbálja bedolgozni az adatokat egy *by-oszlop pozíció* sorrendben (azaz, hogy leképezi a pozíció alapértelmezett).

    * Válassza a **Tovább lehetőséget.**

    ![A céladatkészlet "Oszlopleképezés" ablaktáblája](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. A **Beállítások** ablaktáblán tegye a következő lépéseket:

    a. A **Hibatűrési beállítások csoportban**adja meg a megfelelő beállításokat.

    b. A **Teljesítménybeállítások**csoportban az **Átmeneti állapot engedélyezése** nem alkalmazható, és a Speciális **beállítások** költségszempontokat is tartalmaznak. Ha nincsenek konkrét követelményei, hagyja ezeket a beállításokat a megfelelő ben.

    c. Válassza a **Tovább lehetőséget.**

    ![A másolási adatok "Beállítások" ablaktábla](media/data-factory-load-data/copy-data-settings.png)

1. Az **Összegzés** ablaktáblán tekintse át a beállításokat, majd kattintson a **Tovább gombra.**

    ![Az "Összegzés" adatmásolási ablaktábla](media/data-factory-load-data/copy-data-summary.png)

1. A **Telepítés befejeződése** ablaktáblán hajtsa végre az alábbi műveletet:

    a. Ha át szeretne váltani a **Monitor** lapra, és meg szeretné tekinteni a folyamat állapotát (azaz a folyamatot, a hibákat és az adatfolyamot), válassza a **Monitor**lehetőséget.

    b. Csatolt szolgáltatások, adatkészletek és folyamatok szerkesztéséhez válassza a **Folyamat szerkesztése**lehetőséget.

    c. Az adatmásolási feladat végrehajtásához válassza a **Befejezés** gombot.

    ![A "Telepítés befejeződött" ablaktábla](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg az [Azure Data Explorer összekötő](/azure/data-factory/connector-azure-data-explorer) az Azure Data Factory.
* További információ a csatolt szolgáltatások, adatkészletek és folyamatok szerkesztéséről a [Data Factory felhasználói felületén.](/azure/data-factory/quickstart-create-data-factory-portal)
* Ismerje meg az [Azure Data Explorer adatlekérdezési lekérdezések.](/azure/data-explorer/web-query-data)
