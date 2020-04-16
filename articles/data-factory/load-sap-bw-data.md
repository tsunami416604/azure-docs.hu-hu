---
title: Adatok betöltése az SAP Business Warehouse-ból
description: Adatok másolása az SAP Business Warehouse (BW) rendszerből az Azure Data Factory használatával
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 96b23696164514ad2f16de72f0f76aa237ffce2e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415841"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja az Azure Data Factory segítségével adatok másolása az SAP Business Warehouse (BW) open hub on keresztül az Azure Data Lake Storage Gen2. Hasonló folyamattal más támogatott fogadó [adattárakba](copy-activity-overview.md#supported-data-stores-and-formats)másolhat adatokat.

> [!TIP]
> Az SAP BW-ből történő adatok másolásáról, beleértve az SAP BW Open Hub integrációt és a különbözeti kinyerési folyamatot, az Adatok másolása az [SAP Business Warehouse-ból az Open Hub használatával az Azure Data Factory használatával](connector-sap-business-warehouse-open-hub.md)című témakörben talál általános információt.

## <a name="prerequisites"></a>Előfeltételek

- **Azure Data Factory:** Ha nem rendelkezik ilyen, kövesse a lépéseket, hogy [hozzon létre egy adat-előállító.](quickstart-create-data-factory-portal.md#create-a-data-factory)

- **SAP BW Open Hub Destination (OHD) "Adatbázistábla" céltípussal:** OhD létrehozása vagy annak ellenőrzéséhez, hogy az OHD megfelelően van-e konfigurálva a Data Factory-integrációhoz, tekintse meg a cikk [SAP BW Open Hub Destination konfigurációi](#sap-bw-open-hub-destination-configurations) című részét.

- **Az SAP BW felhasználónak a következő engedélyekre van szüksége:**

  - Távoli függvényhívások (RFC) és SAP BW engedélyezése.
  - A **S_SDSAUTH** engedélyezési objektum "Végrehajtás" tevékenységéhez szükséges engedélyek.

- **Saját [üzemeltetésű integrációs futásidő (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) az SAP .NET 3.0-s összekötővel.** Kövesse az alábbi telepítési lépéseket:

  1. Telepítse és regisztrálja az önkiszolgáló integrációs futásidejű, 3.13-as vagy újabb verziót. (Ezt a folyamatot a cikk későbbi részében ismertetjük.)

  2. Töltse le a [64 bites SAP Connector for Microsoft .NET 3.0-t](https://support.sap.com/en/product/connectors/msnet.html) az SAP webhelyéről, és telepítse ugyanarra a számítógépre, mint a saját üzemeltetésű infravörös kapcsolat. A telepítés során győződjön meg arról, hogy a **Választható beállítási lépések** párbeszédpanelen a **Szerelések telepítése a GAC-ra lehetőséget választja,** ahogy az alábbi képen látható:

     ![Az SAP .NET-összekötő párbeszédpanel beállítása](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Teljes másolatot készíteni az SAP BW Open Hub-ról

Az Azure Portalon lépjen az adat-előállítóra. Válassza **a Szerzői & figyelő** lehetőséget a Data Factory felhasználói felületének külön lapon való megnyitásához.

1. A **Let's get started** (Az adatok másolása) lapon válassza az **Adatok másolása** eszközt.

2. A **Tulajdonságok** lapon adja meg **a Tevékenység nevét**, majd kattintson a **Tovább**gombra.

3. A **Forrásadattár** lapon válassza az **+Új kapcsolat létrehozása**lehetőséget. Válassza az **SAP BW Open Hub** elemet az összekötőgyűjteményből, majd kattintson a Folytatás **gombra.** Az összekötők szűréséhez írja be az **SAP-t** a keresőmezőbe.

4. Az **SAP BW Open Hub kapcsolat megadása** lapon az alábbi lépésekkel hozhat létre új kapcsolatot.

   ![SAP BW Open Hub-hoz csatolt szolgáltatáslap létrehozása](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. A **Connect via integrációs futásidejű** lista, válasszon ki egy meglévő saját üzemeltetésű ir. Vagy válassza az egyik létrehozását, ha még nem rendelkezik ilyen.

      Új, saját üzemeltetésű infravörös kapcsolat létrehozásához válassza a **+Új**, majd az **Önkiszolgáló**lehetőséget. Írjon be egy **nevet**, majd válassza a **Tovább**gombot. Válassza az **Express telepítőt** az aktuális számítógépre való telepítéshez, vagy kövesse a **manuális beállítási** lépéseket.

      Az Előfeltételek című részben [említettek](#prerequisites)szerint győződjön meg arról, hogy az SAP Connector for Microsoft .NET 3.0 ugyanazon a számítógépen van telepítve, ahol a saját üzemeltetésű infravörös kapcsolat fut.

   2. Töltse ki az SAP BW **kiszolgáló nevét**, **a rendszerszámot,** **az ügyfélazonosítót,** **a nyelvet** (ha nem **az EN),** **a felhasználónevet**és **a jelszót.**

   3. A beállítások érvényesítéséhez válassza a **Kapcsolat tesztelése** lehetőséget, majd a **Befejezés**gombot.

   4. Új kapcsolat jön létre. Kattintson a **Tovább** gombra.

5. A **Hub megnyitása célok kiválasztása** lapon keresse meg az SAP BW-ben elérhető nyitott központi célokat. Jelölje ki az adatok másolásához kívánt OHD-t, majd kattintson a **Tovább**gombra.

   ![Válassza az SAP BW Open Hub Destination tábla](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Ha szüksége van rá, adjon meg egy szűrőt. Ha az OHD csak egyetlen adatátviteli folyamat (DTP) egyetlen kérelemazonosítóval történő végrehajtását tartalmazza, vagy biztos benne, hogy a DTP befejeződött, és másolni szeretné az adatokat, törölje az **utolsó kérelem kizárása** jelölőnégyzetet.

   Ezekről a beállításokról a cikk [SAP BW Open Hub Destination konfigurációi](#sap-bw-open-hub-destination-configurations) című részében olvashat bővebben. Az **Ellenőrzés** lehetőséget választva ellenőrizze, hogy milyen adatokat ad vissza. Ezután válassza a **Tovább**gombot.

   ![SAP BW Open Hub szűrő konfigurálása](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. A **Céladattár** lapon válassza az **+Új kapcsolat** > létrehozása**az Azure Data Lake Storage Gen2** > **Continue lehetőséget.**

8. Az **Azure Data Lake Storage-tároló megadása kapcsolat** lapon az alábbi lépésekkel hozhat létre kapcsolatot.

   ![ADLS Gen2 csatolt szolgáltatáslap létrehozása](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Válassza ki a Data Lake Storage Gen2-kompatibilis fiókját a **Név** legördülő listából.
   2. Kattintson a **Befejezés** gombra a kapcsolat létrehozásához. Ezután válassza a **Tovább**gombot.

9. A **Kimeneti fájl vagy mappa kiválasztása** lapon adja meg a **copyfromopenhub** parancsot kimeneti mappa névként. Ezután válassza a **Tovább**gombot.

   ![Kimeneti mappa lap kiválasztása](media/load-sap-bw-data/choose-output-folder.png)

10. A **Fájlformátum beállítási** lapján válassza a **Tovább** gombot az alapértelmezett beállítások használatához.

    ![A kezdőtárformátum lap megadása](media/load-sap-bw-data/specify-sink-format.png)

11. A **Beállítások** lapon bontsa ki a **Teljesítménybeállítások lehetőséget.** Adjon meg egy értéket **a másolási párhuzamosság foka,** például 5 az SAP BW-ből párhuzamosan történő betöltéshez. Ezután válassza a **Tovább**gombot.

    ![Másolási beállítások konfigurálása](media/load-sap-bw-data/configure-copy-settings.png)

12. Az **Összegzés** lapon tekintse át a beállításokat. Ezután válassza a **Tovább**gombot.

13. A **Központi telepítés** lapon válassza **a Figyelő** lehetőséget a folyamat figyeléséhez.

    ![Üzembe helyezés lap](media/load-sap-bw-data/deployment.png)

14. Figyelje meg, hogy a lap bal oldalán lévő **Monitor** fül automatikusan ki van jelölve. A **Műveletek** oszlop hivatkozásokat tartalmaz a tevékenység futtatásának részleteinek megtekintéséhez és a folyamat újbóli futtatásához.

    ![Csővezeték figyelési nézete](media/load-sap-bw-data/pipeline-monitoring.png)

15. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez válassza a **Műveletek** oszlop **tevékenységfuttatásainak megtekintése** lehetőséget. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamatfuttatások nézetre, válassza a **folyamatkapcsolatok** közötti kapcsolatot a tetején. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenységfigyelő képernyő](media/load-sap-bw-data/activity-monitoring.png)

16. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a **Részletek** hivatkozást, amely a tevékenységek figyelése nézetben a **Műveletek** csoport alatt található. A rendelkezésre álló részletek közé tartozik a forrásból a fogadóba másolt adatkötet, az adatátviteli, végrehajtási lépések és időtartam, valamint a használt konfigurációk.

    ![Tevékenységfigyelés részletei](media/load-sap-bw-data/activity-monitoring-details.png)

17. A **maximális kérelemazonosító**megtekintéséhez lépjen vissza a tevékenységfigyelési nézetbe, és válassza **a Kimenet lehetőséget** a Műveletek **csoportban.**

    ![Tevékenység kimeneti képernyője](media/load-sap-bw-data/activity-output.png)

    ![Tevékenység kimeneti részleteinek nézete](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Növekményes másolás az SAP BW Open Hub-ról

> [!TIP]
> Tekintse meg [az SAP BW Open Hub-összekötő különbözeti kibontási folyamatát,](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) amelyből megtudhatja, hogy az SAP BW Open Hub-összekötő a Data Factory-ban hogyan másolja át az SAP BW növekményes adatait. Ez a cikk is segít megérteni az alapvető összekötő konfigurációját.

Most továbbra is konfigurálja a növekményes másolást az SAP BW Open Hubról.

A növekményes példány "magas vízjel" mechanizmust használ, amely a **kérelemazonosítón**alapul. Ezt az azonosítót a DTP automatikusan létrehozza az SAP BW Open Hub Destination alkalmazásban. Az alábbi ábra ezt a munkafolyamatot mutatja:

![Növekményes másolási munkafolyamat-folyamatábra](media/load-sap-bw-data/incremental-copy-workflow.png)

Az adatgyárban **Az első lépések** lapon válassza a Folyamat létrehozása **sablonból** lehetőséget a beépített sablon használatához.

1. Keresse meg az **SAP BW-t** az **SAP BW növekményes példányának** az Azure Data Lake Storage Gen2 sablonjának megkereséséhez. Ez a sablon adatokat másol az Azure Data Lake Storage Gen2-be. Hasonló munkafolyamattal más fogadótípusokba másolhat.

2. A sablon főlapján jelölje ki vagy hozza létre a következő három kapcsolatot, majd válassza a **Sablon használata lehetőséget** az ablak jobb alsó sarkában.

   - **Azure Blob storage:** Ebben a forgatókönyvben az Azure Blob storage-ot használjuk a magas vízjel tárolására, amely a *maximálisan másolt kérelemazonosító.*
   - **SAP BW Open Hub**: Ez az a forrás, amelyből adatokat lehet másolni. A részletes konfigurációt az előző teljes másolási forgatókönyvben tájékozhatja.
   - **Azure Data Lake Storage Gen2:** Ez a fogadó adatok másolása. A részletes konfigurációt az előző teljes másolási forgatókönyvben tájékozhatja.

   ![Növekményes másolás az SAP BW sablonból](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ez a sablon a következő három tevékenységet kínáló folyamatot hoz létre, és sikeressé teszi őket: *Keres,* *Adatok másolása*és *web*.

   Nyissa meg a folyamat **paraméterei** lapot. Az összes szükséges konfigurációt láthatja.

   ![Növekményes másolás az SAP BW konfigurációjából](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Adja meg az Open Hub tábla nevét, amelyből adatokat szeretne másolni.

   - **Data_Destination_Container:** Adja meg a cél Azure Data Lake Storage Gen2 tároló adatok másolása. Ha a tároló nem létezik, a Data Factory másolási tevékenység létrehoz egyet a végrehajtás során.
  
   - **Data_Destination_Directory:** Adja meg a mappa elérési útját az Azure Data Lake Storage Gen2 tároló ban adatok másolásához. Ha az elérési út nem létezik, a Data Factory másolási tevékenység létrehoz egy elérési utat a végrehajtás során.
  
   - **HighWatermarkBlobContainer**: Adja meg a tárolót a magas vízjel értékének tárolásához.

   - **HighWatermarkBlobDirectory**: Adja meg a mappa elérési útját a tároló alatt a magas vízjel érték ének tárolásához.

   - **HighWatermarkBlobName**: Adja meg a blob nevét a `requestIdCache.txt`magas vízjel értékének tárolásához, például . A Blob storage-ban lépjen a HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName megfelelő elérési útjára, például *a container/path/requestIdCache.txt fájlra.* Hozzon létre egy blobot 0 tartalommal.

      ![Blob tartalma](media/load-sap-bw-data/blob.png)

   - **LogicAppURL:** Ebben a sablonban a WebActivity használatával hívjuk meg az Azure Logic Apps-t a blobstorage magas vízjelértékének beállításához. Vagy használhatja az Azure SQL Database-t a tárolásához. Az érték frissítéséhez használjon tárolt eljárási tevékenységet.

      Először létre kell hoznia egy logikai alkalmazást, ahogy az alábbi képen látható. Ezután illessze be a **HTTP POST URL-címét**.

      ![Logikai alkalmazás konfigurációja](media/load-sap-bw-data/logic-app-config.png)

      1. Nyissa meg az Azure Portalt. Válasszon ki egy új **Logic Apps** szolgáltatást. Válassza a **+Blank Logic App lehetőséget** a **Logic Apps Designer**elemre való ugráshoz.

      2. **Http-kérelem érkezésekori**eseményindító létrehozása. Adja meg a HTTP-kérelem törzsét az alábbiak szerint:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Blob **létrehozása** művelet hozzáadása. **A Mappa elérési útja** és a Blob **neve**esetén ugyanazokat az értékeket használja, mint amelyeket korábban a *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* és *a HighWatermarkBlobName mappában*konfigurált.

      4. Kattintson a **Mentés** gombra. Ezután másolja a **HTTP POST URL-cím** értékét a Data Factory-folyamatban való használatra.

4. Miután megadt a Data Factory folyamat paramétereit, válassza **a Debug** > **Befejezés** lehetőséget a futtatás meghívásához a konfiguráció érvényesítéséhez. Vagy válassza **a Közzététel** lehetőséget az összes módosítás közzétételéhez, majd a Futtatás **isztikai eseményindító hozzáadása** parancsra.

## <a name="sap-bw-open-hub-destination-configurations"></a>AZ SAP BW Open Hub Destination konfigurációi

Ez a szakasz bemutatja az SAP BW-oldal konfigurációját az SAP BW Open Hub-összekötő data factory használatával.

### <a name="configure-delta-extraction-in-sap-bw"></a>Különbözeti kinyerés konfigurálása SAP BW-ben

Ha szüksége van mind a korábbi másolásés növekményes másolás, vagy csak növekményes másolás, konfigurálja a különbözeti kinyerés az SAP BW.If you need both historical copy and incremental copy or only incremental copy, configure delta extraction in SAP BW.

1. Hozza létre az Open Hub Destination(Open Hub Destination) című állomást. Az OHD az SAP-tranzakció RSA1, amely automatikusan létrehozza a szükséges átalakítási és adatátviteli folyamat. Használja a következő beállításokat:

   - **ObjectType**: Bármilyen objektumtípust használhat. Itt az **InfoCube-ot** használjuk példaként.
   - **Céltípusa**: Válassza **az Adatbázistábla lehetőséget**.
   - **A táblázat kulcsa:** Válassza a **technical key**lehetőséget.
   - **Kibontás**: Válassza **az Adatok megtartása és rekordok beszúrása a táblába**lehetőséget.

   ![SAP BW OHD különbözeti kihúzásának létrehozása párbeszédpanel](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD delta2 kihúzásának létrehozása párbeszédpanel](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Növelheti a Párhuzamosan futó SAP-munkafolyamatok számát a DTP-hez:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Ütemezze a DTP-t folyamatláncokban.

   A kocka különbözeti DTP-je csak akkor működik, ha a szükséges sorok nincsenek tömörítve. Győződjön meg arról, hogy a BW-kocka tömörítése nem fut az Open Hub tábla DTP-je előtt. Ennek legegyszerűbb módja a DTP integrálása a meglévő folyamatláncokba. A következő példában a DTP (az OHD-hez) bekerül a folyamatláncba az *Igazítás* (összesítés) és az *Összecsukás* (kockatömörítés) lépések között.

   ![SAP BW folyamatábra létrehozása](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Teljes kinyerés konfigurálása SAP BW-ben

A különbözeti kinyerés mellett előfordulhat, hogy ugyanazt az SAP BW InfoProvider-t szeretné teljes mértékben kidolgozni. Ez általában akkor érvényes, ha teljes másolatot szeretne készíteni, de nem növekményes, vagy újra szinkronizálni szeretné a [különbözeti kibontást.](#resync-delta-extraction)

Nem lehet egynél több DTP ugyanazért az OHD-hez. Tehát létre kell hoznia egy további OHD-t a delta kinyerés előtt.

![Sap BW OHD teljes létrehozása](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

A teljes terhelés OHD, válasszon más lehetőségeket, mint a delta kitermelés:

- Az OHD: Állítsa be a **Kibontás** beállítást **az Adatok törlésére és a Rekordok beszúrására.** Ellenkező esetben az adatok kinyerése sokszor, amikor megismétli a DTP egy BW folyamatlánc.

- A DTP: Állítsa **kinyerési mód** **teljes**. Az automatikusan létrehozott DTP-t az OHD létrehozása után azonnal meg kell **változtatnia Deltáról** **teljesre,** ahogy ez a kép a következőket mutatja:

   ![Sap BW OHD párbeszédpanel létrehozása a "Teljes" kinyeréshez konfigurálva](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- A Data Factory BW Open Hub összekötőjében: Kapcsolja ki **az Utolsó kérelem kizárása .** Ellenkező esetben semmi sem lesz kivonva.

A teljes DTP-t általában manuálisan futtatja. Vagy létrehozhat egy folyamatláncot a teljes DTP-hez. Ez általában egy külön lánc, amely független a meglévő folyamatláncoktól. Mindkét esetben *győződjön meg arról, hogy a DTP befejeződött, mielőtt elkezdené a kibontást a Data Factory copy használatával.* Ellenkező esetben csak részleges adatok lesznek másolva.

### <a name="run-delta-extraction-the-first-time"></a>A különbözeti kivonás futtatása az első alkalommal

Az első delta extrakció technikailag *teljes extrakció.* Alapértelmezés szerint az SAP BW Open Hub összekötő kizárja az utolsó kérelmet, amikor adatokat másol. Az első különbözeti kinyeréshez a Data Factory másolási tevékenység nem nyer ki adatokat, amíg egy későbbi DTP nem hoz létre különbözeti adatokat a táblában egy külön kérelemazonosítóval. Ezt a forgatókönyvet kétféleképpen kerülheti el:

- Kapcsolja ki az **Utolsó kérelem kizárása** beállítást az első különbözeti kibontáshoz. Győződjön meg arról, hogy az első delta DTP befejeződött, mielőtt először elindítané a delta kinyerést.
-  Használja az eljárást a különbözeti kihúzás újraszinkronizálásához, a következő szakaszban leírtak szerint.

### <a name="resync-delta-extraction"></a>Különbözeti kihúzás újraszinkronizálása

A következő forgatókönyvek módosítják az SAP BW-kockák adatait, de a különbözeti DTP nem veszi figyelembe őket:

- SAP BW szelektív törlés (sorok bármilyen szűrőfeltétel használatával)
- SAP BW kérelem törlése (hibás kérések)

Az SAP Open Hub Destination nem egy data-mart által vezérelt adatcél (2015 óta az összes SAP BW támogatási csomagban). Így törölheti az adatokat egy kockából az OHD adatainak módosítása nélkül. Ezután újra kell szinkronizálnia a kocka adatait a Data Factory-val:

1. Teljes kinyerés futtatása a Data Factory ban (egy teljes DTP használatával az SAP-ban).
2. Törölje a különbözeti DTP Open Hub táblájának összes sorát.
3. Állítsa a különbözeti DTP állapotát **Lehívásra.**

Ezt követően az összes ezt követő különbözeti DTP és a Data Factory különbözeti kinyerések a várt módon működik.

A különbözeti DTP állapotának **lehívása**beállításához a következő beállítással futtathatja a különbözeti DTP-t manuálisan:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>További lépések

További információ az SAP BW Open Hub összekötő támogatásáról:

> [!div class="nextstepaction"]
>[SAP Business Warehouse Open Hub összekötő](connector-sap-business-warehouse-open-hub.md)
