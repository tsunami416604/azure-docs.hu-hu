---
title: Adatok betöltése az Office 365-ből az Azure Data Factory használatával
description: Adatok másolása az Office 365-ből az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 3422176ed89b7f575c11cc40e5be8420da0018b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415799"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Adatok betöltése az Office 365-ből az Azure Data Factory használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk bemutatja, hogyan használhatja a Data Factory _betöltési adatait az Office 365-ből az Azure Blob storage-ba._ Hasonló lépéseket követhet az Azure Data Lake Gen1 vagy Gen2 adatok másolásához. Az [Office 365-összekötő általános](connector-office-365.md) adatok másolásáról szóló cikkben olvashat.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **Erőforrás-elemzési** > **Analytics** > **adatgyár**létrehozása lehetőséget: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lapon adja meg az alábbi képen látható mezők értékeit:
      
   ![Új adat-előállító lap](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Név**: Adjon meg egy globálisan egyedi nevet az Azure-adat-előállító. Ha a "Data factory name *LoadFromOffice365Demo* is not available" hibaüzenet jelenik meg, adjon meg egy másik nevet az adatgyárnak. Használhatja például a**LoadFromOffice365Demo** _**nevet.**_ Próbálja meg újra létrehozni az adat-előállítót. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetést, amelyben létre szeretné hozni az adat-előállító. 
    * **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2**lehetőséget.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak más helyeken és régiókban is lehetnek. Ezek az adattárak közé tartozik az Azure Data Lake Store, az Azure Storage, az Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatgyárat. A **Data Factory** kezdőlapja az alábbi képen látható módon jelenik meg:
   
   ![Data factory kezdőlap](./media/load-office-365-data/data-factory-home-page.png)

5. Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. A "Kezdjük" lapon válassza a **Folyamat létrehozása lehetőséget.**
 
    ![Folyamat létrehozása](./media/load-office-365-data/create-pipeline-entry.png)

2. A folyamat **Általános lapján** adja meg a "CopyPipeline" értéket a folyamat **neve** mezőbe.

3. A Tevékenységek eszközmezőben > Áthelyezés & átalakítás kategóriában, > húzza a **Másolás tevékenységet** az eszközmezőből a folyamattervező felületére. Adja meg a "CopyFromOffice365ToBlob" értéket tevékenységnévként.

### <a name="configure-source"></a>Forrás konfigurálása

1. Lépjen a forrásadatkészlet létrehozásához > **folyamatforrás >** gombra, kattintson **a + Új** gombra. 

2. Az Új adatkészlet ablakban válassza az **Office 365**lehetőséget, majd kattintson a **Folytatás gombra.**
 
3. Most már a másolási tevékenység konfigurációja lapon van. Kattintson az Office 365-adatkészlet melletti **Szerkesztés** gombra az adatkonfiguráció folytatásához.

    ![Az Office 365 általános adatkészletének konfigurációja](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Megjelenik egy új lap az Office 365-adatkészlethez. A Tulajdonságok ablak alján található **Általános lapmezőbe** írja be a "SourceOffice365Dataset" kifejezést a Név mezőbe.
 
5. Nyissa meg a Tulajdonságok ablak **Kapcsolat lapját.** A Csatolt szolgáltatás szövegmezője mellett kattintson a **+ Új**gombra.

6. Az Új csatolt szolgáltatás ablakban adja meg az "Office365LinkedService" nevet, írja be az egyszerű szolgáltatásazonosítót és a szolgáltatásegyszerű kulcsot, majd tesztelje a kapcsolatot, és válassza a **Létrehozás** lehetőséget a csatolt szolgáltatás központi telepítéséhez.

    ![Új Office 365 csatolt szolgáltatás](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. A társított szolgáltatás létrehozása után a rendszer visszalépteti az adatkészlet beállításaihoz. A **Táblázat**csoportban válassza a lefelé mutató nyilat az elérhető Office 365-adatkészletek listájának kibontásához, majd válassza a "BasicDataSet_v0. Message_v0" a legördülő listából:

    ![Konfigurációs Office 365-adatkészlettábla](./media/load-office-365-data/edit-dataset.png)

8. Most lépjen vissza a **folyamat** > **forrása lapra,** és folytassa az Office 365-adatok kinyeréséhez szükséges további tulajdonságok konfigurálását.  A felhasználói hatókör és a felhasználói hatókör szűrő nem kötelező predikátumok, amelyek korlátozhatják az Office 365-ből kinyerni kívánt adatokat. A beállítások konfigurálásáról az [Office 365-ös adatkészlettulajdonságai](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) című szakaszban olvashat.

9. Ki kell választania az egyik dátumszűrőt, és meg kell adnia a kezdési és befejezési időértékeket.

10. Kattintson a **Séma importálása** fülre az üzenet adatkészletséma importálásához.

    ![Az Office 365-ös adatkészletséma config](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Fogadó konfigurálása

1. Nyissa meg a folyamat> **a Fogadó lapot**, és válassza a + **Új** lehetőséget a fogadó adatkészlet létrehozásához.
 
2. Az Új adatkészlet ablakban figyelje meg, hogy csak a támogatott célhelyek vannak kijelölve az Office 365-ből történő másoláskor. Válassza az Azure Blob Storage ( **Azure Blob Storage**, bináris formátum , majd a **Folytatás**) lehetőséget.  Ebben az oktatóanyagban az Office 365-adatokat egy Azure Blob Storage-ba másolja.

3. Kattintson az Azure Blob Storage-adatkészlet melletti **Szerkesztés** gombra az adatkonfiguráció folytatásához.

4. A Tulajdonságok ablak **Általános lapján** a Név mezőbe írja be a "OutputBlobDataset" értéket.

5. Nyissa meg a Tulajdonságok ablak **Kapcsolat lapját.** A Társított szolgáltatások szövegbeviteli mező mellett válassza az **+ Új** lehetőséget.

6. Az Új csatolt szolgáltatás ablakban adja meg az "AzureStorageLinkedService" nevet, válassza ki a "Szolgáltatásegyszerű" lehetőséget a hitelesítési módszerek legördülő listájából, töltse ki a Szolgáltatásvégpontot, a Bérlőt, az egyszerű szolgáltatásazonosítót és az egyszerű szolgáltatáskulcsot, majd válassza a Mentés lehetőséget a csatolt szolgáltatás üzembe helyezéséhez.  Tekintse meg [itt,](connector-azure-blob-storage.md#service-principal-authentication) hogyan állíthatja be az egyszerű szolgáltatás hitelesítése az Azure Blob Storage.

    ![Új Blob-csatolt szolgáltatás](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>A folyamat érvényesítése

A folyamat érvényesítéséhez válassza az **Érvényesítés** elemet az eszköztáron.

A folyamathoz társított JSON-kódot a jobb felső sarokban lévő Kód gombra kattintva is megtekintheti.

## <a name="publish-the-pipeline"></a>A folyamat közzététele

A felső eszköztáron válassza az **Összes közzététele**lehetőséget. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.

![Változások közzétevése](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása

Az eszköztáron válassza az **Eseményindító hozzáadása** lehetőséget, majd az **Eseményindító most**lehetőséget. A Folyamatfuttatás lapon válassza a **Befejezés** elemet. 

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

Lépjen a bal oldali **Figyelés** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **Műveletek** oszlopban lévő hivatkozások segítségével megtekintheti a tevékenység részleteit, és újra futtathatja a folyamatot.

![Folyamat figyelése](./media/load-office-365-data/pipeline-status.png) 

A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a Műveletek oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység található, ezért csak egy bejegyzést lát a listán. A másolási művelet részleteinek megtekintéséhez válassza a Műveletek oszlop **Részletek** hivatkozását (szemüveg ikon).

![Tevékenység figyelése](./media/load-office-365-data/activity-status.png) 

Ha ez az első alkalom, hogy adatokat kér ehhez a környezethez (amelynek kombinációja az adattábla elérése, melyik célfiókba töltődik be, és melyik felhasználói identitás teszi az adatelérési kérelmet), akkor a másolási tevékenység állapota **Folyamatban**állapotú lesz, és csak akkor jelenik meg, ha a Műveletek csoport "Részletek" hivatkozására **kattint.**  Az adatelérés-jóváhagyó csoport egyik tagjának jóvá kell hagynia a kérelmet a kiemelt hozzáférés-kezelésben, mielőtt az adatok kinyerése folytatódhatna.

_Állapot, mint a kért hozzájárulás:_
![Tevékenység végrehajtási adatok - hozzájárulás kérése](./media/load-office-365-data/activity-details-request-consent.png) 

_Adatok kinyeréseként állapot:_

![Tevékenység-végrehajtási részletek - adatok kinyerése](./media/load-office-365-data/activity-details-extract-data.png) 

A hozzájárulás után az adatok kinyerése folytatódik, és egy idő után a folyamat futtatása sikeresként jelenik meg.

![Figyelési folyamat - sikerült](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Most nyissa meg a célhoz az Azure Blob Storage-ot, és ellenőrizze, hogy az Office 365-adatok bináris formátumban lettek-e kinyerve.

## <a name="next-steps"></a>További lépések

Az Azure SQL Data Warehouse támogatásáról az alábbi cikkhez továbbkell lépnie: 

> [!div class="nextstepaction"]
>[Office 365-összekötő](connector-office-365.md)