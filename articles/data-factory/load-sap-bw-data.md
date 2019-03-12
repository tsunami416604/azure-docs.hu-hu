---
title: Adatok betöltése az SAP Business warehouse-hoz az Azure Data Factory használatával |} A Microsoft Docs
description: Az Azure Data Factory használata adatok másolása az SAP Business Warehouse (BW)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 607c3ff128c82c7baa268cf8f068232428ec5331
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733216"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>SAP Business Warehouse (BW) az adatok betöltése az Azure Data Factory használatával

Ez a cikk bemutatja a forgatókönyv a Data Factory használatával _az SAP Business Warehouse (BW) megnyitott központon keresztül adatok betöltése az Azure Data Lake Storage Gen2_. Adatok másolása másik hasonló lépéseket követnie [támogatott fogadó adattárakba](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Tekintse meg [SAP BW Open Hub összekötő cikk](connector-sap-business-warehouse-open-hub.md) az adatok másolása az SAP BW általánosságban, beleértve az SAP BW Open Hub integráció és a különbözeti a kinyerési folyamat bemutatása.

## <a name="prerequisites"></a>Előfeltételek

- **Az Azure Data Factory (ADF):** Ha nem rendelkezik egy adat-előállítót, kövesse a "[adat-előállító létrehozása](quickstart-create-data-factory-portal.md#create-a-data-factory)" szakaszt hozhat létre egyet. 

- **Az SAP BW Open Hub cél (OHD) az "Adatbázis táblaként" cél típusának.** Hajtsa végre a [SAP BW Open Hub cél konfigurációk](#sap-bw-open-hub-destination-configurations) szakasz szeretne létrehozni egyet, vagy győződjön meg arról, ha a meglévő OHD megfelelően van konfigurálva az ADF használatával integrációját.

- **Használt SAP BW-felhasználónak kell rendelkeznie, a következő engedélyekkel:**

  - RFC és az SAP BW engedélyezését.
  - Az engedélyek a "**végrehajtása**"Tevékenység engedélyezési objektum"**S_SDSAUTH**".

- **[Önálló gazdagép az Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) SAP .NET-összekötővel 3.0 szükségesek**. Az alábbiakban a részletes előkészített, amely kell végrehajtani:

  1. Telepítse és regisztrálja a helyi integrációs modul verziójával > = 3.13 (kezelt a következő forgatókönyv). 

  2. Töltse le a [64 bites SAP .NET összekötő 3.0](https://support.sap.com/en/product/connectors/msnet.html) SAP webhelyről, és telepítse a helyi integrációs modul gépen.  Amikor telepíti, a "opcionális beállítási lépéseket" ablakban ellenőrizze, hogy kiválasztja a "**GAC szerelvények telepítése**" lehetőséget az alábbi képen látható módon.

     ![SAP .NET-összekötő beállítása](media\connector-sap-business-warehouse-open-hub\install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Az SAP BW Open Hub teljes másolását.

Az Azure Portalon, válassza a data factory -> válassza **létrehozás és Monitorozás** az ADF felhasználói felületének külön lapon történő elindításához. 

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon válassza az **Adatok másolása** csempét. 

2. Az a **tulajdonságok** csoportjában adja meg egy nevet a **feladat neve** mezőt, és válassza **tovább**.

3. Az a **forrásadattár** kattintson **+ új kapcsolat létrehozása** -> válassza **SAP BW Open Hub** az összekötő-katalógusból -> válassza **Folytatás**. Az összekötők a keresőmezőbe írja be "Az SAP".

4. Az a **adja meg az SAP BW Open Eseményközpont-kapcsolat** lapon 

   ![Az SAP BW Open Hub társított szolgáltatás létrehozása](media\load-sap-bw-data\create-sap-bw-open-hub-linked-service.png)

   1. Válassza ki a **csatlakozás integrációs modulon keresztül**: kattintson a legördülő listában válassza ki egy meglévő helyi integrációs modul, vagy létrehozhat egy újat, ha helyi integrációs modul még nem rendelkezik. 

      Új létrehozásához kattintson a **+ új** a legördülő -> select típusa **saját üzemeltetésű** -> adjon meg egy **neve** kattintson **tovább** -> válassza a **Expressz telepítés** az aktuális gépen telepíteni, vagy kövesse a **manuális telepítés** lépések van.

      A [Előfeltételek](#prerequisites), győződjön meg arról is, a **SAP .NET összekötő 3.0** ugyanarra a gépre, amelyen fut a helyi integrációs modul telepítése.

   2. Adja meg az SAP BW **kiszolgálónév**, **rendszerszám**, **ügyfél-Azonosítót** **nyelvi** (ha EN kívül), **felhasználónév**, és **jelszó**.

   3. Kattintson a **kapcsolat tesztelése** ellenőrzése a beállításokat, majd válassza ki **Befejezés**.

   4. Megjelenik egy új kapcsolat jön létre. Kattintson a **Tovább** gombra.

5. Az a **nyílt Hub kiválasztása destinations** lapon keresse meg az SAP BW elérhető nyílt Hub célok, és válassza ki a kívánt adatokat másolni, majd kattintson a **tovább**.

   ![Az SAP BW Open Hub tábla kiválasztása](media\load-sap-bw-data\select-sap-bw-open-hub-table.png)

6. Ha szükséges, adja meg a szűrő. Ha megnyitott Hub célhelyként csak egyetlen kérelemazonosítóval egyetlen Data Transfer folyamat (DTP) végrehajtási adatokat tartalmaz, vagy arról a DTP véget ért, és szeretné az összes adatot, törölje a jelet a **utolsó kérelem kizárása**. Több az ezen beállítások vonatkoznak az SAP BW-konfiguráció a további [SAP BW Open Hub cél konfigurációk](#sap-bw-open-hub-destination-configurations) szakaszban. Kattintson a **ellenőrzése** , gondosan ellenőrizze az adatokat ad vissza, majd válassza ki **tovább**.

   ![Az SAP BW Open Hub szűrő beállítása](media\load-sap-bw-data\configure-sap-bw-open-hub-filter.png)

7. Az a **célként megadott adattárba** lap, kattintson **+ új kapcsolat létrehozása**, majd válassza ki **Azure Data Lake Storage Gen2**, és válassza ki **Folytatás**.

8. Az a **adja meg az Azure Data Lake Storage kapcsolati** lapon 

   ![ADLS Gen2 társított szolgáltatás létrehozása](media\load-sap-bw-data\create-adls-gen2-linked-service.png)

   1. Válassza ki a Data Lake Storage Gen2 a "Tárfiók neve" képes a fiókot a legördülő listából.
   2. Válassza ki **Befejezés** a kapcsolat létrehozásához. Ezután kattintson a **Tovább** gombra.

9. Az a **a kimeneti fájl vagy mappa kiválasztása** lapon adja meg a "copyfromopenhub", a kimeneti mappa nevét, és válassza ki **tovább**.

   ![Kimeneti mappa kiválasztása](media\load-sap-bw-data\choose-output-folder.png)

10. Az a **beállítás fájlformátum** lapon jelölje be **tovább** , az alapértelmezett beállításokat használja.

   ![Adja meg a fogadó formátum](media\load-sap-bw-data\specify-sink-format.png)

11. Az a **beállítások** lapon, bontsa ki a **teljesítménybeállításokat**, és állítsa be **másolási foka** például 5 annak érdekében, hogy az SAP BW párhuzamos betöltése. Kattintson a **tovább**.

    ![Beállítások konfigurálása](media\load-sap-bw-data\configure-copy-settings.png)

12. Az a **összefoglalás** lapon tekintse át a beállításokat, és válassza ki **tovább**.

13. Az a **üzembe helyezési** lapon jelölje be **figyelő** folyamat monitorozásához.

    ![Üzembe helyezés lap](media\load-sap-bw-data\deployment.png)

14. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop tartalmazza a tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások:

    ![Folyamat figyelése](media\load-sap-bw-data\pipeline-monitoring.png)

15. A folyamat futásához társított tevékenységfuttatások megtekintéséhez jelölje ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Válassza ki, váltson vissza a folyamatfuttatások nézetre a **folyamatok** a fenti hivatkozásra. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenység figyelése](media\load-sap-bw-data\activity-monitoring.png)

16. Figyelheti a minden egyes másolási tevékenység-végrehajtási részleteit, válassza ki a **részletei** (szemüveg képet) kapcsolat alatt **műveletek** a figyelési nézet tevékenység. Adatait is figyelheti, például a fogadó, a fájlmegosztásra, a megfelelő időtartamot a végrehajtási lépések a forráskiszolgálóról másolt adatok mennyiségét, és a használt konfigurációk:

    ![Tevékenység részletei figyelése](media\load-sap-bw-data\activity-monitoring-details.png)

17. Tekintse át a **maximális Kérelemazonosító** másolt. Lépjen vissza a figyelési nézet tevékenység, kattintson a **kimeneti** alatt **műveletek**.

    ![Tevékenység kimenete](media\load-sap-bw-data\activity-output.png)

    ![Tevékenység kimeneti részletei](media\load-sap-bw-data\activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Az SAP BW Open Hub növekményes másolását.

> [!TIP]

> Tekintse meg [SAP BW Open Hub összekötő különbözeti kinyerési folyamat](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) további ADF másolási tevékenységgel az adatok növekményes másolása az SAP BW működéséről.

Most folytassa az alábbi lépésekkel konfigurálhatja az SAP BW Open hubról növekményes másolat. 

A növekményes másolási magas vízjel mechanizmus DTP által automatikusan generált azonosító, az SAP BW Open Hub cél kérés alapján használja. A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható:

![Növekményes másolás munkafolyamat](media\load-sap-bw-data\incremental-copy-workflow.png)

Az ADF UI felületen **első lépések** lapon jelölje be **folyamat létrehozása**. 

1. Húzza a három tevékenységek - **keresési, az adatok másolása és webes** – alakzatot a vásznon, és azokat a sikeresség láncolt győződjön. Ez az útmutató azt tárolására fog használni az Azure Blob a magas vízjel - maximális másolt kérés azonosítója. SQL-adatbázis segítségével tárolja, és tárolt eljárási tevékenység használata helyett a webes tevékenység frissíteni is.

   ![A növekményes másolási folyamat](media\load-sap-bw-data\incremental-copy-pipeline.png)

2. Keresési tevékenység konfigurálása:

   1. A keresési tevékenység **beállítások** lapon jelölje **csak az első sor** lehetőséget.

      ![Keresési beállítások](media\load-sap-bw-data\lookup-settings.png)

   2. Konfigurálása **forrásadatkészlet** Blob-adatkészletet, mint az a **fájl elérési útja**, a blob mutasson, ahol szeretné tárolni a maximális másolt Kérelemazonosító magas vízjel, és megtarthatja a formátumot a szöveges formátumban.

      ![A BLOB-adatkészlet beállításai](media\load-sap-bw-data\blob-dataset.png)

   3. A megfelelő blob elérési hozzon létre egy blob tartalmát 0.

      ![Blob tartalma](media\load-sap-bw-data\blob.png)

3. Adja meg a másolási tevékenység: 

   1. A **forrás** lapon **forrásadatkészlet** SAP BW Open Hub adatkészletként. 

   2. Szerkessze a **SAP BW Open Hub adatkészlet**:

      1. A **paraméterek** lapra, és állítsa a "kérelemazonosító" nevű paraméter
      2. A **kapcsolat** lap, adja meg, nyissa meg egy tábla központnevet, megtartása "Kizárása utolsó kérelem azonosítója" kiválasztva, és állítsa be az alap Kérelemazonosító kifejezés használata (a dinamikus tartalom hozzáadása) `@dataset().requestId`.

   3. Lépjen vissza a másolási tevékenység **forrás** lapra, konfigurálja a használt kifejezés "kérelemazonosító" értéket (a dinamikus tartalom hozzáadása) `@{activity('<look up activity name>').output.firstRow.Prop_0}`. Ennek megfelelően módosítsa a "keresse meg a tevékenység neve" Ebben a kifejezésben.

      ![Forrásbeállítások másolása](media\load-sap-bw-data\copy-source.png)

4. Webes tevékenység konfigurálása: a webes tevékenység hívni fogja a logikai alkalmazás blobba maximális másolt Kérelemazonosító tárolására.

   1. Válassza az Azure portal -> új egy **logikai alkalmazás** egy **HTTP-kérelem** és a egy **blob létrehozása** módon. Használja az ugyanazon konfigurálva a fenti keresési tevékenység forrás blob-fájlt. Másolja a **HTTP POST URL-címe** a webes tevékenység lesz használható.

      ![Logikai alkalmazás konfigurációja](media\load-sap-bw-data\logic-app-config.png)

   2. Lépjen vissza az ADF szerkesztése **webes tevékenység** az alábbi beállításokat. Állítsa be a szervezet kifejezés (dinamikus tartalom hozzáadása) `{"sapOpenHubMaxRequestId":"@{activity('CopyFromSap').output.sapOpenHubMaxRequestId}"}`.

      ![Webes tevékenység beállításai](media\load-sap-bw-data\web-activity-settings.png)

5. Ezt követően kattinthat **Debug** a konfiguráció érvényesítéséhez, vagy válasszon **összes közzététele** a módosítások közzététele, majd kattintson a **eseményindító** futtató végrehajtásához.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub Destination configurations

Ez a szakasz az SAP BW-oldalán a szükséges konfiguráció használatához az SAP BW Open Eseményközpont-összekötő az ADF-ben az adatok másolásához mutatja be.

### <a name="configure-delta-extraction-in-sap-bw"></a>Az SAP BW különbözeti kinyerési konfigurálása

Ha egyaránt korábbi példányát és a növekményes másolási, vagy csak a növekményes másolási van szüksége, adja meg a különbözeti kinyerési SAP BW.

1. A megnyitott Hub cél (OHD) létrehozása

   A OHD SAP tranzakció RSA1 hozhat létre. Ez automatikusan létrehozza a szükséges Adatátalakítási és az adatok átvitele folyamatban (DTP). Használja a következő beállításokat:

   - Objektum típusa bármilyen lehet. InfoCube itt példaként használjuk.
   - **Cél típusa:** *Adatbázis-táblában*
   - **Tábla kulcsa:** *Technikai kulcs*
   - **Kibontási:** *Adatok megőrzése és táblába Insert rekordok*

   ![Hozzon létre az SAP BW OHD különbözeti kivonása](media\load-sap-bw-data\create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media\load-sap-bw-data\create-sap-bw-ohd-delta2.png)

   Előfordulhat, hogy növeli a párhuzamosan futó SAP munkahelyi folyamatok esetében a DTP számát:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data\create-sap-bw-ohd-delta3.png)

2. A folyamat láncban DTP ütemezése

   Az adatkocka egy különbözeti DTP csak akkor működik, ha a szükséges sorok nem tömörített még. Ezért gondoskodnia kell arról, hogy BW-kocka tömörítés nem fut a DTP előtt nyissa meg a Hub táblához. Ennek legegyszerűbb módja a DTP integrálása a meglévő folyamat minősített főtanúsítványhoz kapcsolódik. Az alábbi példában a DTP (így a OHD) egészül között a lépés a folyamat lánc (aggregált összesítési) beállítása és összecsukása (Cube tömörítés).

   ![create-sap-bw-process-chain](media\load-sap-bw-data\create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Az SAP BW teljes kinyerési konfigurálása

A különbözeti kivonása mellett érdemes az azonos InfoProvider teljes kivonása rendelkezik. Ez általában akkor érvényes, ha a teljes másolási növekményes nélkül kíván, vagy szeretné [szinkronizálja újra a különbözeti kinyerési](#re-sync-delta-extraction).

Nem az azonos OHD az egynél több DTP kell rendelkeznie. Ezért szeretne létrehozni, mint a különbözeti kinyerése egy további OHD.

![create-sap-bw-ohd-full](media\load-sap-bw-data\create-sap-bw-ohd-full.png)

Válassza ki a teljes terhelés OHD különbözeti kinyerési, mint más lehetőségek:

- A OHD: állítsa be a "Kivonása" beállítás értéke "*adatok törlése és -rekordok beszúrása*". Ellenkező esetben adatok szeretne kinyerni sokszor Ha ismétlődő a DTP BW folyamat láncban.

- A DTP: "Kinyerési üzemmód" beállítás "*teljes*". Módosítania kell az automatikusan létrehozott DTP a különbözeti teljes, csak a OHD létrehozása után:

   ![create-sap-bw-ohd-full2](media\load-sap-bw-data\create-sap-bw-ohd-full2.png)

- Az ADF SAP BW Open Eseményközpont-összekötő: kapcsolja ki a "*kizárási utolsó kérelem*". Ellenkező esetben semmit nem szeretne kinyerni. 

Jellemzően futtatnia a teljes DTP manuálisan. Vagy is létrehozhat egy folyamatot láncot a teljes DTP – Ez általában akkor lehet egy külön folyamat láncot, a meglévő folyamat láncok használatától független. Mindkét esetben kell **ellenőrizze, hogy a DTP ADF másolással a kivonás előtt befejeződött**, ellenkező esetben adatok csak részlegesen lesznek másolva.

### <a name="run-delta-extraction-the-first-time"></a>Futtassa a különbözeti kivonása az első alkalommal

Az első különbözeti kivonás értelemben az egy **teljes kinyerési**. Megjegyzés alapértelmezett ADF SAP BW Open Eseményközpont-összekötő által a legutóbbi kérés nem tartalmazza, az adatok másolásakor. Először az ADF másolási activtiy, különbözeti kivonása esetén nincs az adatok kinyerése történik addig, amíg nincs a későbbi DTP különbözeti adatokat állít elő a táblázatban külön kérelmet az azonosítóval. Bár ennek elkerülése érdekében két lehetséges módja van:

1. Kapcsolja ki a "Kizárni utolsó kérelem" az első különbözeti kivonása az ebben az esetben győződjön meg arról, hogy az első különbözeti DTP befejeződött-e először a különbözeti kivonás előtt kell
2. A különbözeti kivonás újra szinkronizálásra, az alább ismertetett eljárással.

### <a name="re-sync-delta-extraction"></a>Szinkronizálja újra a különbözeti kivonása

Van néhány olyan forgatókönyvek, melyek az adatok az SAP BW-kocka módosítását, de azokat nem veszi figyelembe a különbözeti DTP:

- SAP BW szelektív Törlés (a sorok szűrése feltétel használatával)
- Az SAP BW kérelem törlése (hibás kérés)

Egy SAP nyílt Hub cél nem érhető adatok adatközpont-szabályozású ügyfélkezelési adatok cél (az összes SAP BW támogatási csomag 2015 évhez képest). Ezért is lehet adatokat törölhet egy adatkockát a OHD adatainak módosítása nélkül. Ebben az esetben, újra kell szinkronizálnia az adatokkal az ADF adatkocka az adatokat a következő lépések végrehajtásával:

1. Futtassa a teljes kivonása az ADF-ben (az SAP teljes DTP használatával)
2. A megnyitott Hub tábla összes sorát a különbözeti DTP törlése
3. A különbözeti DTP állapotának beállítása beolvasása kész

Ezt követően minden későbbi különbözeti DTPs és ADF különbözeti Információkinyerés pontosan a várt módon működik.

A különbözeti DTP állapotát a különbözeti DTP segítségével manuálisan a következő beállítást futtatásával beállíthatja beolvasása kész: "*Nincs adatátvitel; A különbözeti állapota a forrásban: Fetched*”.

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkben olvashat az SAP BW Open Hub connector támogatása: 

> [!div class="nextstepaction"]
>[SAP Business Warehouse nyílt Hub-összekötő](connector-sap-business-warehouse-open-hub.md)
