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
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9a123ed45b5857aa40fc9853a95c528833ba8aa9
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523188"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Adatmásolás az SAP Business warehouse-hoz az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan lehet Azure Data Factory segítségével az SAP Business Warehouse (BW) megnyitott központon keresztül adatok másolása az Azure Data Lake Storage Gen2-re. Adatok másolása másik hasonló folyamata használhatja [támogatott fogadó adattárakba](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Általános információk az adatok másolása az SAP BW-hez, az SAP BW Open Hub integráció és a különbözeti a kinyerési folyamat, például: [SAP Business Warehouse nyílt központon keresztül az Azure Data Factory használatával adatokat másol](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Előfeltételek

- **Az Azure Data Factory**: Ha még nincs fiókja, kövesse a lépéseket [adat-előállító létrehozása](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **Az SAP BW Open Hub cél (OHD) a cél típusa "Adatbázistábla"**: Hozzon létre egy OHD, vagy ellenőrizze, hogy a OHD megfelelően van konfigurálva a Data Factory-integráció, tekintse meg a [SAP BW Open Hub cél konfigurációk](#sap-bw-open-hub-destination-configurations) című szakaszát.

- **Az SAP BW-felhasználó a következő engedélyeket kell**:

  - Távoli függvényhívások (RFC) engedélyezését és az SAP BW.
  - Az "Execute" tevékenységét engedélyekkel a **S_SDSAUTH** engedélyezési objektum.

- **A [saját üzemeltetésű integrációs modul (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) SAP .NET-összekötővel 3.0**. Kövesse a beállítási lépések:

  1. Telepíti és regisztrálja a saját üzemeltetésű integrációs modult, 3.13 vagy újabb verziója. (A cikk későbbi részében ezt a folyamatot ismerteti.)

  2. Töltse le a [64 bites SAP-összekötő a Microsoft .NET-hez 3.0](https://support.sap.com/en/product/connectors/msnet.html) SAP webhelyről, és telepítse a számítógépre, a saját üzemeltetésű A telepítés során győződjön meg arról, hogy kiválasztotta **GAC szerelvények telepítése** a a **opcionális beállítási lépéseket** párbeszédpanelen, az alábbi képen látható módon:

     ![Állítsa be a .NET-összekötő az SAP párbeszédpanel](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Hajtsa végre a teljes másolási SAP BW Open hubról

Az Azure Portalon nyissa meg az adat-előállítóhoz. Válassza ki **létrehozás és Monitorozás** , nyissa meg a Data Factory felhasználói felületének külön lapon.

1. Az a **első lépések** lapon jelölje be **adatok másolása** az adatok másolása eszköz megnyitásához.

2. Az a **tulajdonságok** adja meg azokat a **feladat neve**, majd válassza ki **tovább**.

3. Az a **forrásadattár** lapon jelölje be **+ új kapcsolat létrehozása**. Válassza ki **SAP BW Open Hub** az összekötő-katalógusban, és válassza ki a **Folytatás**. Szűrés az összekötők, beírhatja **SAP** kifejezést a keresőmezőbe.

4. Az a **adja meg az SAP BW Open Eseményközpont-kapcsolat** lapon, a következő lépésekkel hozzon létre egy új kapcsolatot.

   ![Az SAP BW Open Hub társított szolgáltatása lap létrehozása](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Az a **csatlakozás integrációs modulon keresztül** válasszon ki egy meglévő saját üzemeltetésű Vagy, hozzon létre egyet, ha még nincs ilyen.

      Egy új saját üzemeltetésű integrációs modul létrehozásához válassza **+ új**, majd válassza ki **saját üzemeltetésű**. Adjon meg egy **neve**, majd válassza ki **tovább**. Válassza ki **Expressz telepítés** az adott számítógépen telepíteni, vagy kövesse a **manuális telepítés** biztosított lépéseket.

      Az említett [Előfeltételek](#prerequisites), győződjön meg arról, hogy rendelkezik az SAP-összekötő a Microsoft .NET-hez a saját üzemeltetésű integrációs modul futtató azonos számítógépre telepített 3.0.

   2. Adja meg az SAP BW **kiszolgálónév**, **rendszer száma**, **ügyfél-Azonosítót** **nyelvi** (kivéve ha **EN**) , **Felhasználónév**, és **jelszó**.

   3. Válassza ki **kapcsolat tesztelése** ellenőrizze a beállításokat, és válassza ki a **Befejezés**.

   4. Új kapcsolat jön létre. Kattintson a **Tovább** gombra.

5. Az a **nyílt Hub célok kiválasztása** érhetők el az SAP BW Open Hub célok lapon, jelölje ki. Válassza ki az adatokat másolni, és válassza ki a OHD **tovább**.

   ![SAP BW Open Hub céloldali tábla kiválasztása](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Adjon meg egy szűrőt, ha szükség van egy. Ha a OHD csak egy egyetlen adatátvitel (DTP) folyamat-végrehajtás egyetlen kérelem azonosítója adatokat tartalmaz, vagy ha biztos benne, hogy a DTP befejeződött, és szeretné másolni az adatokat, törölje a **utolsó kérelem kizárása** jelölőnégyzetet.

   További tudnivalók a ezeket a beállításokat a a [SAP BW Open Hub cél konfigurációk](#sap-bw-open-hub-destination-configurations) című szakaszát. Válassza ki **ellenőrzése** ellenőrizze, hogy milyen adatok visszaadásához fog. Ezután kattintson a **Tovább** gombra.

   ![Az SAP BW Open Hub szűrő beállítása](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Az a **célként megadott adattárba** lapon jelölje be **+ új kapcsolat létrehozása** > **Azure Data Lake Storage Gen2**  >   **Továbbra is**.

8. Az a **adja meg az Azure Data Lake Storage kapcsolati** lapon, a következő lépésekkel hozzon létre egy kapcsolatot.

   ![Egy ADLS Gen2 kapcsolódószolgáltatás-lap létrehozása](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Válassza ki a Data Lake Storage Gen2-kompatibilis fiókját a **neve** legördülő listából.
   2. Válassza ki **Befejezés** a kapcsolat létrehozásához. Ezután kattintson a **Tovább** gombra.

9. Az a **a kimeneti fájl vagy mappa kiválasztása** lap, adja meg **copyfromopenhub** a kimeneti mappa neveként. Ezután kattintson a **Tovább** gombra.

   ![Válassza ki a kimeneti mappa lap](media/load-sap-bw-data/choose-output-folder.png)

10. A a **beállítás fájlformátum** lapon jelölje be **tovább** , az alapértelmezett beállításokat használja.

    ![Adja meg a fogadó formátum lapja](media/load-sap-bw-data/specify-sink-format.png)

11. Az a **beállítások** lapon, bontsa ki a **teljesítménybeállításokat**. Adjon meg egy értéket a **másolási foka** például 5 betölteni az SAP BW párhuzamosan. Ezután kattintson a **Tovább** gombra.

    ![Beállítások konfigurálása](media/load-sap-bw-data/configure-copy-settings.png)

12. Az a **összefoglalás** lapon, tekintse át a beállításokat. Ezután kattintson a **Tovább** gombra.

13. Az a **üzembe helyezési** lapon jelölje be **figyelő** folyamat monitorozásához.

    ![Üzembe helyezés lap](media/load-sap-bw-data/deployment.png)

14. Figyelje meg, hogy a **figyelő** fülre az oldal bal oldalán automatikusan ki van jelölve. A **műveletek** oszlop tevékenység futtatási részleteinek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozásokat tartalmaz.

    ![A folyamat figyelési nézet](media/load-sap-bw-data/pipeline-monitoring.png)

15. Válassza ki, a folyamat futásához társított tevékenységfuttatások megtekintéséhez **Tevékenységfuttatások megtekintése** a a **műveletek** oszlop. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. A – a folyamatfuttatások nézetre váltáshoz válassza ki a **folyamatok** a fenti hivatkozásra. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Képernyő figyelése](media/load-sap-bw-data/activity-monitoring.png)

16. Figyelheti a minden egyes másolási tevékenység-végrehajtási részleteit, válassza ki a **részletei** hivatkozás, amely az alábbi szemüveg ikon **műveletek** a tevékenység-figyelés nézetben. Rendelkezésre álló szerepel a fogadó, adatátvitelt, végrehajtási lépések és időtartama a forráskiszolgálóról másolt adatokat tartalmazó kötetre, és konfigurációkat használják.

    ![Tevékenység részletei figyelése](media/load-sap-bw-data/activity-monitoring-details.png)

17. Megtekintéséhez a **maximális Kérelemazonosító**, lépjen vissza a tevékenység-figyelési nézet, és válasszon **kimeneti** alatt **műveletek**.

    ![Tevékenység kimeneti képernyő](media/load-sap-bw-data/activity-output.png)

    ![Tevékenység kimeneti részleteinek megtekintése](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Hajtsa végre a növekményes SAP BW Open hubról

> [!TIP]
> Lásd: [SAP BW Open Hub összekötő különbözeti kinyerési folyamat](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) megtudhatja, hogyan adat-előállítóban az SAP BW Open Hub összekötő növekményes adatokat másol az SAP BW. Ez a cikk is segíthet alapszintű összekötő-konfiguráció ismertetése.

Most folytassa az alábbi lépésekkel konfigurálhatja az SAP BW Open hubról növekményes másolat.

A növekményes másolási alapján "felső küszöbbel rendelkező" mechanizmust alkalmaz a **kérelemazonosító**. Ez automatikusan a DTP által generált az SAP BW Open Hub cél. A következő diagramon láthatók a munkafolyamat:

![A növekményes másolási munkafolyamat folyamatábra](media/load-sap-bw-data/incremental-copy-workflow.png)

Az adat-előállító **első lépések** lapon jelölje be **folyamat létrehozása sablonból** használhatja a beépített sablont.

1. Keresse meg **SAP BW** , és jelölje ki a **növekményes másolása az SAP BW Azure Data Lake Storage Gen2** sablont. Ez a sablon az Azure Data Lake Storage Gen2 másolja az adatokat. Egy hasonló munkafolyamat használatával másolja a többi fogadó.

2. A sablon fő lapján, a vagy a következő három kapcsolatok létrehozása, majd válassza ki és **ezzel a sablonnal** az ablak jobb alsó sarkában.

   - **Az Azure Blob storage**: Ez az útmutató a magas vízjel, amely tárolására használjuk az Azure Blob storage a *max másolt Kérelemazonosító*.
   - **SAP BW Open Hub**: Ez az adatokat másolni a forrás. Tekintse meg a korábbi másolási teljes forgatókönyv részletes konfiguráció.
   - **Az Azure Data Lake Storage Gen2**: Ez az adatokat másolni a fogadó. Tekintse meg a korábbi másolási teljes forgatókönyv részletes konfiguráció.

   ![A növekményes másolási SAP BW-sablonból](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ez a sablon a következő három tevékenységeket tartalmazó folyamatot hoz létre, és megkönnyíti a kapcsolt siker: *Keresési*, *adatmásolás*, és *webes*.

   Nyissa meg a folyamat **paraméterek** fülre. Láthatja, a konfigurációk, meg kell adnia.

   ![SAP BW-konfigurációból növekményes másolat](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Adja meg a nyitott Hub táblanév adatokat másolni.

   - **ADLSGen2SinkPath**: Adja meg az adatok másolása az Azure Data Lake Storage Gen2 elérési utat. Ha az elérési út nem létezik, a Data Factory másolási tevékenység végrehajtása során hoz létre egy elérési utat.

   - **HighWatermarkBlobPath**: Adja meg például a magas vízjelének tárolási helyét `container/path`.

   - **HighWatermarkBlobName**: Adja meg a blob nevét, a felső küszöbértékek tárolására például `requestIdCache.txt`. A Blob storage-ban nyissa meg a megfelelő elérési útját HighWatermarkBlobPath + HighWatermarkBlobName, mint például *container/path/requestIdCache.txt*. Hozzon létre egy blob tartalmát 0.

      ![Blob tartalma](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: Ez a sablon használatával WebActivity hívja az Azure Logic Apps felső küszöbbel rendelkező érték beállításához a Blob storage-ban. Másik lehetőségként használhatja az Azure SQL Database tárolja azt. A tárolt eljárási tevékenység használatával frissítse az értéket.

      Az alábbi képen látható módon először létre kell hoznia egy logikai alkalmazást. Ezt követően illessze be a **HTTP POST URL-címe**.

      ![Logikai alkalmazások konfigurálása](media/load-sap-bw-data/logic-app-config.png)

      1. Nyissa meg az Azure Portalt. Adjon meg új **Logic Apps** szolgáltatás. Válassza ki **+ üres logikai alkalmazás** go to **Logic Apps Designerben**.

      2. Az eseményindító létrehozása **amikor egy HTTP-kérelem érkezett**. Adja meg a HTTP-kérés törzse a következők szerint:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. Adjon hozzá egy **blob létrehozása** művelet. A **mappa elérési útja** és **blobnév**, használhatja ugyanazokat az értékeket a korábban konfigurált **HighWatermarkBlobPath** és **HighWatermarkBlobName**.

      4. Kattintson a **Mentés** gombra. Ezután másolja a értékét **HTTP POST URL-címe** használata a Data Factory-folyamatot.

4. Miután megadta a Data Factory-folyamat paramétereit, válassza ki a **Debug** > **Befejezés** a konfiguráció érvényesítéséhez egy Futtatás elindításához. Vagy válassza **összes közzététele** a módosítások közzététele, és válassza ki a **eseményindító** futtató végrehajtásához.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub Destination configurations

Ez a szakasz az SAP BW Open Eseményközpont-összekötő használata a Data Factory adatokat másolni az SAP BW ügyféloldali konfigurációját mutatja be.

### <a name="configure-delta-extraction-in-sap-bw"></a>Az SAP BW különbözeti kinyerési konfigurálása

Ha korábbi másolási és a növekményes másolási vagy csak a növekményes másolási van szüksége, adja meg a különbözeti kinyerési SAP BW.

1. A megnyitott Hub cél létrehozása. A OHD SAP tranzakció RSA1, ami automatikusan létrehozza a szükséges Adatátalakítási és adatátviteli folyamatot hozhat létre. Használja a következő beállításokat:

   - **ObjectType**: Minden objektumtípus is használhatja. Itt használjuk **InfoCube** példaként.
   - **Cél típusa**: Válassza ki **adatbázistábla**.
   - **A tábla kulcs**: Válassza ki **műszaki kulcs**.
   - **Kibontási**: Válassza ki **tartani az adatok és rekordok Insert táblába**.

   ![Az SAP BW OHD különbözeti kinyerési párbeszédpanel létrehozása](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Az SAP BW OHD delta2 kinyerési párbeszédpanel létrehozása](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Előfordulhat, hogy növeli a párhuzamosan futó SAP munkahelyi folyamatok esetében a DTP számát:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. A folyamat láncban DTP ütemezése.

   Egy adatkocka DTP különbözeti csak a szükséges sorok tömörített még nem működik. Győződjön arról, hogy BW-kocka tömörítés nem a DTP előtt nyissa meg a Hub táblához. Ennek legegyszerűbb módja, hogy a DTP integrálható a meglévő folyamat minősített főtanúsítványhoz kapcsolódik. A következő példában a DTP (így a OHD) szúr be a folyamat lánc között a *igazítás* (összesített) és *összecsukása* (cube tömörítés) lépéseit.

   ![Hozzon létre az SAP BW folyamat lánc folyamatábra](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Az SAP BW teljes kinyerési konfigurálása

Mellett a különbözeti kinyerési érdemes lehet az ugyanaz az SAP BW InfoProvider teljes kivonása. Ez általában akkor érvényes, ha azt szeretné, a teljes másolási, de nem növekményes vagy szeretne [szinkronizálja újra a különbözeti kinyerési](#resync-delta-extraction).

Nem lehet egynél több DTP számára az azonos OHD. Ezért létre kell hoznia egy további OHD különbözeti kivonása előtt.

![Az SAP BW OHD teljes létrehozása](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Válassza ki a teljes terhelés OHD különböző lehetőségei, mint a különbözeti kivonása:

- A OHD: Állítsa be a **kinyerési** beállítást **adatok törlése és -rekordok beszúrása**. Ellenkező esetben az adatok kinyerése történik esetén ismételje meg a folyamat BW láncban DTP több alkalommal.

- Az a DTP: Állítsa be **kinyerési mód** való **teljes**. Módosítania kell a az automatikusan létrehozott DTP **különbözeti** való **teljes** azonnal a OHD létrehozása után a képen látható módon:

   ![Az SAP BW OHD létrehozása "Teljes" kinyerési konfigurált párbeszédpanel](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- A Data Factory BW Open Eseményközpont-összekötő: Kapcsolja ki a **kizárási utolsó kérelem**. Ellenkező esetben nem fogja kinyerni.

Jellemzően futtatnia a teljes DTP manuálisan. Vagy a teljes DTP hozhat létre egy folyamatot láncot. Fontos általában egy külön láncot, amely a meglévő folyamat láncok független. Mindkét esetben *győződjön meg arról, hogy a DTP befejeződött-e a Kibontás a Data Factory másolás megkezdése előtt*. Ellenkező esetben csak részleges adatot másolni.

### <a name="run-delta-extraction-the-first-time"></a>Futtassa a különbözeti kivonása az első alkalommal

Az első különbözeti kivonás értelemben az egy *kinyerési teljes*. Alapértelmezés szerint az SAP BW Open Eseményközpont-összekötő a legutóbbi kérés kizárhat, amikor adatokat másolja. Az első különbözeti a kivonási nincsenek adatok ki kell olvasni a Data Factory másolási tevékenység által mindaddig, amíg egy későbbi DTP különbözeti adatokat állít elő egy külön kérelmet az azonosítóval a tábla Ennek elkerülése érdekében két módja van:

- Kapcsolja ki a **kizárási utolsó kérelem** első különbözeti kivonására lehetőséget. Győződjön meg arról, hogy az első különbözeti DTP befejeződött-e, mielőtt elkezdené a változások kivonás először.
-  Használja az eljárást a különbözeti kinyerés újraszinkronizálása a következő szakaszban leírtak szerint.

### <a name="resync-delta-extraction"></a>Szinkronizálja újra a különbözeti kivonása

A következő esetekben módosítja az adatokat a SAP BW-kocka, de azokat nem veszi figyelembe a különbözeti DTP:

- SAP BW szelektív Törlés (a sorok szűrése feltétel használatával)
- SAP BW kérelem törlése (a hibás kérelmek)

Az SAP nyílt Hub cél nem egy adatok adatközpont-szabályozású ügyfélkezelési adatok cél (az összes SAP BW támogatási csomag 2015 óta esetén). Tehát törölheti adatokat egy adatkockából a OHD adatainak módosítása nélkül. Ezután szinkronizálja újra az adatok Data factoryval adatkocka:

1. Futtassa a teljes kinyerése a Data Factory (az SAP teljes DTP használatával).
2. Törölje a különbözeti DTP a nyitott Hub tábla összes sorát.
3. A különbözeti DTP állapotának beállítása a **beolvasása kész**.

Ezt követően az összes későbbi különbözeti DTPs és adat-előállító különbözeti információkinyerés a várt módon működik.

A különbözeti DTP állapotának beállítása a **beolvasása kész**, az alábbi lehetőségek a különbözeti DTP manuálisan futtatására használhat:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>További lépések

Ismerje meg az SAP BW Open Hub connector támogatása:

> [!div class="nextstepaction"]
>[SAP Business Warehouse nyílt Hub-összekötő](connector-sap-business-warehouse-open-hub.md)
