---
title: Adatok betöltése az Office 365-ből az Azure Data Factory használatával |} A Microsoft Docs
description: Adatok másolása az Office 365-ből az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445778"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Adatok betöltése az Office 365-ből az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használható a Data Factory _adatok betöltése az Office 365-ből az Azure Blob storage_. Adatok másolása az Azure Data Lake Gen1 és Gen2 hasonló lépéseket követheti. Tekintse meg [Office 365-összekötő cikk](connector-office-365.md) a általában az adatok másolása az Office 365.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **erőforrás létrehozása** > **adatok + analitika** > **adat-előállító**: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az a **új adat-előállító** lap, adja meg a mezőket az alábbi képen látható:
      
   ![Új adat-előállító lap](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Név**: Adja meg az Azure data factory egy globálisan egyedi nevet. Ha a hibaüzenetet kapja "adat-előállító nevét \"LoadFromOffice365Demo\" nem érhető el" adja meg a data Factory egy másik nevet. Például használhatja a név  _**sajátneve**_**LoadFromOffice365Demo**. Próbálja meg újra létrehozni az adat-előállító. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetés, amelyben az adat-előállító létrehozásához. 
    * **Erőforráscsoport**: A legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza ki **V2**.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak más helyeken / régiókban is lehetnek. Ezek adattárak közé tartozik az Azure Data Lake Store, az Azure Storage, Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. Létrehozás befejezése után nyissa meg az adat-előállítóhoz. Megjelenik a **adat-előállító** kezdőlapja a következő képen látható módon:
   
   ![Data factory kezdőlap](./media/load-office-365-data/data-factory-home-page.png)

5. Válassza ki a **létrehozás és Monitorozás** csempére kattintva indítsa el az adatintegrációs alkalmazás külön lapon.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Válassza az "Első lépések" oldalon **folyamat létrehozása**.
 
    ![Folyamat létrehozása](./media/load-office-365-data/create-pipeline-entry.png)

2. Az a **az Általános lapon** a folyamat, adja meg "CopyPipeline" **neve** a folyamat.

3. A tevékenységek eszköz mezőbe > áthelyezése és átalakítása kategória > húzza át a **másolási tevékenység** az eszközkészletből a folyamat tervezőfelületére. Adja meg a "CopyFromOffice365ToBlob" tevékenység neve.

### <a name="configure-source"></a>Forrás konfigurálása

1. Nyissa meg a folyamat > **(forrás) lapot**, kattintson a **+ új** forrásadatkészlet létrehozásához. 

2. Jelölje ki az új adatkészlet ablakban **Office 365**, majd válassza ki **Befejezés**.

    ![Új Office 365-adatkészlet](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Megjelenik egy új lap nyílik meg az Office 365-adatkészlethez. Az a **az Általános lapon** a Tulajdonságok ablak alján, írja be a "SourceOffice365Dataset" nevet.

    ![Általános konfiguráció az Office 365-adatkészlet](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Nyissa meg a **kapcsolat lap** a Tulajdonságok ablakának. A társított szolgáltatás beviteli mező mellett kattintson **+ új**.
 
    ![Konfiguráció az Office 365 adatkészlet kapcsolata](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Az új társított szolgáltatás ablak neveként adja meg a "Office365LinkedService", adja meg a szolgáltatásnév-Azonosítót és a szolgáltatásnév kulcsát, majd válassza a Mentés a társított szolgáltatás üzembe helyezéséhez.

    ![Új Office 365-beli társított szolgáltatás](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. A társított szolgáltatás létrehozása után a rendszer visszalépteti az adatkészlet beállításaihoz. Melletti "Table" válassza a lefelé mutató nyílra kattintva bontsa ki a lista az elérhető Office 365-adatkészletek, és válassza a "BasicDataSet_v0. Contact_v0 "a legördülő listából:

    ![Konfiguráció az Office 365 adatkészlet tábla](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Nyissa meg a **séma lapján** a Tulajdonságok ablakban, majd válassza a **séma importálása**.  Figyelje meg, hogy megjelenik-e a séma- és minta értékek, forduljon az adatkészlethez.

    ![Konfiguráció az Office 365 adathalmaz-séma](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Most lépjen vissza a folyamat > forrás lap, győződjön meg arról, hogy SourceBlobDataset van kiválasztva.
 
### <a name="configure-sink"></a>Fogadó konfigurálása

1. Nyissa meg a folyamat > **fogadó lap**, és válassza ki **+ új** egy fogadó-adatkészlet létrehozásához.
 
2. Az új adatkészlet ablakban figyelje meg, hogy csak a támogatott cél példatípust az Office 365-ből van kiválasztva. Válassza ki **Azure Blob Storage**, majd válassza ki **Befejezés**.  Ebben az oktatóanyagban az Office 365-adatokat egy Azure Blob Storage-ba másolja.

    ![Új Blob-adatkészlet](./media/load-office-365-data/new-blob-dataset.png)

4. Az a **az Általános lapon** a Tulajdonságok ablakban, a nevét, adja meg a "OutputBlobDataset".

5. Nyissa meg a **kapcsolat lap** a Tulajdonságok ablakának. Válassza ki a társított szolgáltatás szövegmező melletti **+ új**.

    ![Konfiguráció Blob-adatkészlet kapcsolata](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. Az új társított szolgáltatás ablak neveként adja meg a "AzureStorageLinkedService", "szolgáltatásnév kiválasztása" lehetőséget a legördülő listából hitelesítési módszerek, töltse ki a szolgáltatásvégpont, bérlői szolgáltatásnév-Azonosítót, és szolgáltatás a szolgáltatásnév kulcsát, majd válassza a Mentés a társított szolgáltatás üzembe helyezéséhez.  Tekintse meg [Itt](connector-azure-blob-storage.md#service-principal-authentication) az egyszerű szolgáltatásnév hitelesítése az Azure Blob Storage-beállítása.

    ![Új társított Blob szolgáltatás](./media/load-office-365-data/new-blob-linked-service.png)

7. A társított szolgáltatás létrehozása után a rendszer visszalépteti az adatkészlet beállításaihoz. Fájl elérési útja, mellett válassza ki **Tallózás** választani a kimeneti mappát, ahol az Office 365-adatok bontja ki.  "Fájlformátum beállításai", melletti formátumú, területen válassza a "**JSON formátumban**", mellett Fájlmintát, válassza a "**objektumok közül**".

    ![Konfiguráció Blob-adatkészlet elérési útja és a formátum](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Lépjen vissza a folyamat > fogadó lap, győződjön meg arról, hogy OutputBlobDataset van kiválasztva.

## <a name="validate-the-pipeline"></a>A folyamat érvényesítése

A folyamat érvényesítéséhez válassza az **Érvényesítés** elemet az eszköztáron.

Megtekintheti a folyamat társított kód kattintson a jobb felső sarokban a JSON-kód is.

## <a name="publish-the-pipeline"></a>A folyamat közzététele

A felső eszköztáron válassza **összes közzététele**. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.

![Módosítások mentése](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása

Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget. A Folyamatfuttatás lapon válassza ki a **Befejezés**. 

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

Nyissa meg a **figyelés lapon** a bal oldalon. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A hivatkozások a **műveletek oszlopban** megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

![Folyamat figyelése](./media/load-office-365-data/pipeline-monitoring.png) 

A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** a műveletek oszlopban található hivatkozásra. Ebben a példában csak egy tevékenység található, ezért csak egy bejegyzést lát a listán. A másolási művelet részleteinek kiválasztása a **részletek hivatkozását (szemüveg ikon)** a műveletek oszlopban található.

![Tevékenység figyelése](./media/load-office-365-data/activity-monitoring.png) 

Ha első alkalommal a kért adatokat az ebben a környezetben (kombinációjával, hogy mely tábla folyamatban van a hozzáférés, mely cél fiók be az adatokat, és mely felhasználói identitás, hogy így az adatok hozzáférési kérés), megjelenik a másolási tevékenység állapot szerint "**folyamatban**", és csak akkor, ha a "Details" hivatkozást a műveletek megoldásra kattint, megjelenik az állapot szerint "**RequesetingConsent**".  Az adatelérési jóváhagyó csoportja tagjának kell jóváhagyhatja a kérést a Privileged Access Management, mielőtt konfigurálhatná az adatok kinyerése.

_Kérelmező hozzájárul állapota:_
![a tevékenység végrehajtásának részletei – jóváhagyási kérelem](./media/load-office-365-data/activity-details-request-consent.png) 

_Adatok kinyerése, állapot:_

![A tevékenység végrehajtásának részletei – adatok kinyeréséhez.](./media/load-office-365-data/activity-details-extract-data.png) 

Miután az hozzájárulásra van megadott adatkinyerés továbbra is, és némi várakozás után, a folyamat futásához befejezettként jelennek meg.

![Folyamat figyelése – sikeres](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Most nyissa meg a cél Azure Blob Storage, és győződjön meg arról, hogy az Office 365-adatok lehívása JSON formátumban.

## <a name="next-steps"></a>További lépések

Lépjen az Azure SQL Data Warehouse-támogatással kapcsolatos további információt a következő cikket: 

> [!div class="nextstepaction"]
>[Office 365-összekötő](connector-office-365.md)