---
title: "Az IoT-megoldás létrehozása a Stream Analytics segítségével |} Microsoft Docs"
description: "A Stream Analytics IoT-megoldás az őrbódét forgatókönyv-bevezető oktatóanyag"
keywords: "IOT-megoldás, ablakban funkciók"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: a93693ef7d40025fa96846594a8eb525a50b6885
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Az IoT-megoldás létrehozása a Stream Analytics segítségével
## <a name="introduction"></a>Bevezetés
Ebben az oktatóanyagban megtudhatja hogyan Azure Stream Analytics segítségével valós idejű elemzések lekérése az adataiból. A fejlesztők könnyedén kombinálhatja adatstreamek, például kattintás-adatfolyamok, a naplókat és az eszköz által létrehozott események előzményrekordjaira vagy üzleti elemzéseket kapcsolattípusokból referenciaadatok. Teljes körűen felügyelt, valós idejű stream számítási szolgáltatás, amely a Microsoft Azure-ban Azure Stream Analytics biztosít, beépített hibatűrési, alacsony késéssel és méretezhetőség terén annak érdekében, hogy másolatot és futtató percben.

Ez az oktatóanyag befejezése után fogja tudni:

* Ismerkedjen meg az Azure Stream Analytics-portálon.
* Konfigurálhatja és telepítheti a folyamatos átviteli feladatnak.
* Fogalmazza meg valós problémákat és azok megoldási a Stream Analytics lekérdezési nyelv használatával.
* Hogyan megoldások streaming az ügyfeleknek a Stream Analytics az vetett bizalmat.
* A figyelés és naplózás a felhasználói élmény használja a probléma megoldásához.

## <a name="prerequisites"></a>Előfeltételek
Szüksége lesz az oktatóanyag elvégzéséhez a következő előfeltételek teljesülését:

* A legújabb [Azure PowerShell](/powershell/azure/overview)
* A Visual Studio 2017, 2015, vagy az ingyenes [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* Egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)
* Rendszergazdai jogosultságokkal azon a számítógépen
* Töltse le a [TollApp.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) a Microsoft letöltőközpontból
* Választható lehetőség: Forráskód a TollApp esemény generátor a [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>A forgatókönyv bevezető: "Hello, téren!"
Téren állomás egy közös jelenség. Tapasztal őket több gyorsforgalmi, hidak és alagutakon keresztül történik. Minden téren állomás van több téren fülkéit foglalja magában. A manuális fülkéit foglalja magában le kell fizetnie a téren egy kísérő. Automatikus fülkéit foglalja magában: felett minden kiállítási érzékelő megvizsgálja egy RFID kártya, amely, ha a téren kiállítási, a vehicle szélvédőkeret rögzítettek. Akkor is könnyen egy esemény adatfolyamként, amelyben érdekes művelet végrehajtható ezen a téren állomások keresztül járművekről gyűjtött áthaladását megjelenítéséhez.

![Kép autók téren fülkéit foglalja magában:](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Bejövő adatforgalom
Ez az oktatóanyag két adatstreamek működik. Be- és a téren állomások telepített érzékelők az első adatfolyam eredményez. A második adatfolyama statikus keresési adatkészletre mutató vehicle regisztrációs adatokat.

### <a name="entry-data-stream"></a>Bejegyzés adatfolyam
A bejegyzés adatfolyam autók információkat tartalmaz, téren állomások kapunál.

| TollID | EntryTime | LicensePlate | Állapot | Ellenőrizze | Modell | VehicleType | VehicleWeight | Téren | Címke |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |7001 JNB |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |1001 YXZ |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |1004 ABC |KI |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |KI |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |1007 BNJ |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |1007 CDE |NJ |Toyota |4 x 4 |1 |0 |6 |321987654 |

Az oszlopok rövid leírása itt található:

| Oszlop | Leírás |
| --- | --- |
| TollID |A téren kiállítási azonosítója, amely egyedileg azonosít egy téren kiállítási |
| EntryTime |A dátum és idő a téren kiállítási UTC a vehicle-bejegyzés |
| LicensePlate |A vehicle licenc lemez számát |
| Állapot |Az Amerikai Egyesült Államokban állapotának |
| Ellenőrizze |A automobile gyártója |
| Modell |A automobile modell száma |
| VehicleType |Utas járművekről gyűjtött vagy a kereskedelmi járművekről gyűjtött 2 vagy 1 |
| WeightType |Vehicle súly tonna; az utasok járművekről gyűjtött 0 |
| Téren |USD téren értéke |
| Címke |Az e-címke a autó, amely automatizálja a fizetési; üres, ha a fizetési manuálisan végezhető el |

### <a name="exit-data-stream"></a>Kilépés adatfolyam
A kilépési adatfolyam a téren állomás elhagyása autók információkat tartalmaz.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |7001 JNB |
| 1 |2014-09-10T12:03:00.0000000Z |1001 YXZ |
| 3 |2014-09-10T12:04:00.0000000Z |1004 ABC |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |1007 BNJ |
| 2 |2014-09-10T12:07:00.0000000Z |1007 CDE |

Az oszlopok rövid leírása itt található:

| Oszlop | Leírás |
| --- | --- |
| TollID |A téren kiállítási azonosítója, amely egyedileg azonosít egy téren kiállítási |
| ExitTime |A dátum és idő, a vehicle elhagyásakor téren kiállítási UTC szerint |
| LicensePlate |A vehicle licenc lemez számát |

### <a name="commercial-vehicle-registration-data"></a>Kereskedelmi vehicle regisztrációs adatok
Az oktatóprogram egy kereskedelmi vehicle adatbázist statikus pillanatképet.

| LicensePlate | RegistrationId | Lejárt |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| 1005 BIZTONSÁGI |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Az oszlopok rövid leírása itt található:

| Oszlop | Leírás |
| --- | --- |
| LicensePlate |A vehicle licenc lemez számát |
| RegistrationId |A vehicle regisztrációs azonosítója |
| Lejárt |A regisztrációs állapotát a vehicle: 0, ha a vehicle regisztrációs aktív, ha lejárt a regisztrációs 1 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Az Azure Stream Analytics a környezet beállítása
Az oktatóanyag teljesítéséhez szüksége van a Microsoft Azure-előfizetés. A Microsoft biztosít az ingyenes Microsoft Azure-szolgáltatásokhoz.

Ha még nem rendelkezik Azure-fiókot, akkor [ingyenes próbaverzióját kérelem](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Regisztráljon egy ingyenes próbaverziót, mobileszközök, amelyek fogadhat szöveges üzeneteinek és érvényes hitelkártyát kell.
> 
> 

Győződjön meg arról, ez a cikk végén a "Az Azure-fiók törlése" című szakaszának lépéseit követni, így biztosíthatja, hogy az Azure-kreditjeinek a lehető legjobb felhasználását.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Az oktatóanyag szükséges Azure-erőforrások kiépítése
Ez az oktatóanyag két az event hubs fogadásához szükséges *bejegyzés* és *kilépéshez* adatfolyamokat. Az Azure SQL Database kiírathatja a eredményeket a Stream Analytics-feladatok. Az Azure Storage a vehicle regisztrációjával kapcsolatos hivatkozási adatokat tárolja.

A Setup.ps1 parancsfájl a TollApp mappában a Githubon hozhat létre az összes szükséges erőforrásokat. Idő érdekében azt javasoljuk, hogy azt. Ha azt szeretné, további információt ezen erőforrások konfigurálása az Azure portálon, tekintse meg a "Oktatóanyag erőforrások konfigurálása az Azure-portálon a" függelék.

Töltse le és mentse a támogató [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) mappa és a fájlokat.

Nyissa meg a **Microsoft Azure PowerShell** ablak *rendszergazdaként*. Ha még nem rendelkezik Azure PowerShell, kövesse az utasításokat [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview) a telepítéshez.

A Windows automatikusan blokkolja .ps1 .dll vagy .exe fájlok, mert a végrehajtási házirend beállítása, a parancsfájl futtatása előtt kell. Győződjön meg arról, hogy az Azure PowerShell ablakban fut *rendszergazdaként*. Futtatás **Set-ExecutionPolicy unrestricted**. Amikor a rendszer kéri, írja be a **Y**.

![Képernyőkép a "Set-ExecutionPolicy unrestricted" Azure PowerShell ablakban fut](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Futtatás **Get-ExecutionPolicy** győződjön meg arról, hogy működőképes-e a parancsot.

![Képernyőkép a "Get-ExecutionPolicy" Azure PowerShell ablakban fut](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Nyissa meg azt a könyvtárat, amely rendelkezik a parancsfájlok és készítő alkalmazás.

![Képernyőkép a "cd .\TollApp\TollApp" az Azure PowerShell ablakban fut](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Típus **.\\ Setup.ps1** beállítása az Azure-fiókjával, hozzon létre és konfigurálja az összes szükséges erőforrásokat, és a start események generálásához. A parancsfájl véletlenszerűen szerzi be a terület az erőforrások létrehozásához. Kifejezetten megad egy régiót, továbbítja a **-hely** paraméter a következő példában látható módon:

**. \\Setup.ps1-helyre "USA középső RÉGIÓJA"**

![Az Azure bejelentkezési oldalát bemutató képernyőkép](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

A parancsfájl megnyitása a **bejelentkezés** Microsoft Azure oldalán. Adja meg a fiók hitelesítő adatait.

> [!NOTE]
> A fiókjának van hozzáférési joga több előfizetéssel, ha a rendszer kéri, írja be az oktatóanyag használni kívánt előfizetés nevét.
> 
> 

A parancsfájl futtatásához több percig is eltarthat. Miután befejeződött, a kimeneti az alábbi képernyőfelvételen kell hasonlítania.

![A parancsfájl az Azure PowerShell-ablakban képernyőképe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Ezenkívül megjelenik egy másik ablakban az alábbi képernyőfelvételhez hasonló. Az alkalmazás események küldése az Azure Event Hubs, amely az oktatóanyag futtatásához szükséges. Igen ne állítsa le az alkalmazást vagy az ablak bezárása, amíg be nem fejeződik az oktatóanyag.

![Képernyőkép a "Küldő event hub adatok"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Most már megtekintheti az erőforrások az Azure portálon kell lennie. Ugrás a <https://portal.azure.com>, és jelentkezzen be a fiók hitelesítő adataival. Vegye figyelembe, hogy jelenleg bizonyos funkciók használja a klasszikus portálon. Ezeket a lépéseket egyértelműen jelzik.

### <a name="azure-event-hubs"></a>Azure Event Hubs
Az Azure portálon kattintson **további szolgáltatások** a bal oldali felügyeleti ablaktábla alján. Típus **az Event hubs** a mezőben megadott, és kattintson a **az Event hubs**. Megnyílik egy új böngészőablakot megjelenítéséhez a **SERVICE BUS** terület a **klasszikus portál**. Itt látható az Event Hubs hozta létre a Setup.ps1 parancsfájl.

![Service Bus](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Kattintson a kezdetű *tolldata*. Kattintson a **EVENT HUBS** fülre. Látni fogja a két az event hubs nevű *bejegyzés* és *kilépéshez* létre ebben a névtérben.

![Event Hubs lapon a klasszikus portálon](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

### <a name="azure-storage-container"></a>Az Azure Storage-tároló áttekintése
1. Lépjen vissza a lap a böngészőben nyissa meg az Azure-portálhoz. Kattintson a **tárolási** az oktatóanyagban használt Azure tároló megtekintéséhez az Azure portál bal oldalán.
   
    ![Tárolási menüpont](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)
2. Kattintson a kezdődő *tolldata*. Kattintson a **TÁROLÓK** fülre, és tekintse meg a létrehozott tároló.
   
    ![Az Azure portálon tárolók lap](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)
3. Kattintson a **tolldata** tároló, a feltöltött JSON-fájl, amely rendelkezik a vehicle regisztrációs adatainak megjelenítéséhez.
   
    ![Képernyőfelvétel a registration.json fájl a tárolóban](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

### <a name="azure-sql-database"></a>Azure SQL Database
1. Az első lapon nyitotta meg a böngészőben lépjen vissza az Azure-portálon. Kattintson a **SQL-ADATBÁZISOK** az Azure portálon, hogy az SQL-adatbázis, amely az oktatóprogram használható, és kattintson a bal oldalán **tolldatadb**.
   
    ![Képernyőfelvétel a kívánt SQL-adatbázis létrehozása](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. Másolja a kiszolgáló neve nélkül a portszám (*kiszolgálónév*. database.windows.net, például).
    ![A létrehozott SQL-adatbázis adatbázis képernyőképe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Kapcsolódjon az adatbázishoz a Visual Studio
Visual Studio lekérdezés eredményeit a kimeneti adatbázis eléréséhez használja.

Kapcsolódás az SQL-adatbázis (cél), a Visual Studio eszközből:

1. Nyissa meg a Visual Studio, és kattintson a **eszközök** > **Kapcsolódás adatbázis**.
2. Kattintson az **Microsoft SQL Server** adatforrásként.
   
    ![Változás adatforrás párbeszédpanel](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. Az a **kiszolgálónév** mezőbe illessze be a másolt az előző szakaszban az Azure portálról nevét (Ez azt jelenti, hogy *kiszolgálónév*. database.windows.net).
4. Kattintson a **SQL Server-hitelesítés használható**.
5. Adja meg **tolladmin** a a **felhasználónév** mező és **123toll!** az a **jelszó** mező.
6. Kattintson a **válasszon vagy adjon meg egy adatbázisnevet**, és válassza ki **TollDataDB** -adatbázisként.
   
    ![Kapcsolat párbeszédpanel hozzáadása](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Kattintson az **OK** gombra.
8. Nyissa meg a Server Explorer.
   
    ![Server Explorer (Kiszolgálókezelő)](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. Tekintse meg a TollDataDB adatbázis négy tábláit.
   
    ![A TollDataDB adatbázis](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Esemény generátor: TollApp mintaprojektet
A PowerShell parancsfájl automatikusan elindul az események küldése az TollApp minta alkalmazás használatával. Nem kell további lépések végrehajtására.

Azonban ha érdekli megvalósítás részletei, megtalálhatja az alkalmazás forráskódját, a TollApp a Githubon [minták/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Képernyőkép a jelenik meg a Visual Studio mintakód](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
1. Az Azure portálon kattintson a zöld plusz jel új Stream Analytics-feladat létrehozása a lap bal felső sarkában. Válassza ki **Eszközintelligencia + analitika** majd **Stream Analytics-feladat**.
   
    ![Új gomb](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Adja meg a feladat nevét, az előfizetés ellenőrzése javítsa ki, és hozzon létre egy új erőforráscsoportot a Event hub tárolóként ugyanabban a régióban (alapértelmezett érték déli középső Régiójában a parancsfájl).
3. Kattintson a **rögzítés az irányítópulton** , majd **létrehozása** az oldal alján.
   
    ![A Stream Analytics-feladat beállítás létrehozása](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Adja meg a bemeneti források
1. A feladat készít, és nyissa meg a feladatok lapot. Vagy kattintson a portál irányítópultján létrehozott analytics-feladathoz.

2. Kattintson a **BEMENETEK** lapon meghatározhatja a forrásadatokban.
   
    ![A bemeneti adatok lap](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. Kattintson a **vegye fel a bemeneti**.
   
    ![A bemeneti beállítás hozzáadása](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. Adja meg **EntryStream** , **bemeneti ÁLJEL**.
5. Forrástípus **adatfolyam**
6. Forrás **eseményközpont**.
7. **Service bus-névtér** le egy a TollData az vetett a kell lennie.
8. **Eseményközpont neveként** meg **bejegyzés**.
9. **Eseményközpont házirend neve*van **RootManageSharedAccessKey** (az alapértelmezett érték).
10. Válassza ki **JSON** a **esemény SZERIALIZÁLÁSI formátum** és **UTF8** a **KÓDOLÁS**.
   
    A beállítások hasonlóan fog kinézni:
   
    ![Eseményközpont-beállítások](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. Kattintson a **létrehozása** a varázsló befejezéséhez a lap alján.
    
    Most, hogy létrehozta a bejegyzést adatfolyam, akkor lesz kövesse a lépéseket a kilépési adatfolyam létrehozásához. Ne adja meg, értékeket az alábbi képernyőfelvételen a.
    
    ![A kilépési adatfolyam beállításai](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Két bemeneti adatfolyamok definiált:
    
    ![Az Azure portálon definiált bemeneti adatfolyamok](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    A következő adhat a car regisztrációs adatokat tartalmazó blob elérési referenciaadat-bemenetek.
11. Kattintson a **hozzáadása**, majd kövesse az adatfolyam bemenetek, de válassza **REFERENCIAADATOK** helyett **adatfolyam** és a **bemeneti Alias** van **regisztrációs**.

12. tárfiók kezdetű **tolldata**. A tároló neve legyen **tolldata**, és a **elérési út mintája** kell **registration.json**. Ezt a fájlnevet és nagybetűk különbözőnek kell lennie **kis**.
    
    ![Blog tárolási beállításai](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. Kattintson a **létrehozása** a varázsló befejezéséhez.

Most már az összes bemenet vannak definiálva.

## <a name="define-output"></a>Adja meg a kimeneti
1. A Stream Analytics-áttekintés ablaktábláján válassza **KIMENETEK**.
   
    ![A kimenet lapon és a "Kimenetnek hozzáadása" lehetőséget](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. Kattintson az **Add** (Hozzáadás) parancsra.
3. Állítsa be a **kimeneti alias** "kimeneti", majd **gyűjtése** való **SQL-adatbázis**.
3. Válassza ki a kiszolgáló nevét, amely a cikk a "Kapcsolódás az adatbázis a Visual Studio" szakaszában szerepel. Az adatbázisnév **TollDataDB**.
4. Adja meg **tolladmin** a a **felhasználónév** mezőben **123toll!** az a **jelszó** mező, és **TollDataRefJoin** a a **tábla** mező.
   
    ![SQL-adatbázis beállításai](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. Kattintson a **Create** (Létrehozás) gombra.

## <a name="azure-stream-analytics-query"></a>Az Azure Stream analytics-lekérdezés
A **lekérdezés** lap tartalmaz, amely átalakítja a bejövő adatok SQL-lekérdezést.

![A lekérdezés a lekérdezés lap hozzá](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

Ez az oktatóanyag kísérli meg több, adatokat és a megoldások, amelyek segítségével az Azure Stream Analytics adjon meg egy megfelelő választ a Stream Analytics lekérdezések toll kapcsolódó üzleti kérdések megválaszolása.

Az első Stream Analytics-feladat indítása előtt most megismerkedhet néhány olyan forgatókönyvet, és a lekérdezés szintaxisa.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Bevezetés az Azure Stream Analytics lekérdezési nyelv
- - -
Tegyük fel, hogy szeretné-e, adjon meg egy téren kiállítási gépjárműveket száma. Mivel ez egy állandó folyama miatt események, meg kell adnia egy "időn belül." Módosítsa a kérdés, hogy "hogyan különböző adja meg egy téren kiállítási három percenként?". Ezt gyakran nevezik az átfedésmentes száma.

Nézzük meg az Azure Stream Analytics-lekérdezés, amely a kérdésre választ:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Ahogy látja, az Azure Stream Analytics használ lekérdezésnyelvet, amely például az SQL, és adja meg a lekérdezés idővel kapcsolatos szempontok néhány bővítmények hozzáadása.

További részletekért olvassa el [Időkezelést](https://msdn.microsoft.com/library/azure/mt582045.aspx) és [Ablakozó](https://msdn.microsoft.com/library/azure/dn835019.aspx) MSDN a lekérdezésben használt szerkezeteket tartalmaz.

## <a name="testing-azure-stream-analytics-queries"></a>Tesztelés Azure Stream Analytics-lekérdezések
Most, hogy az első Azure Stream Analytics lekérdezési írt, tesztelheti, akkor a mintaadatfájlok a TollApp mappában található a következő elérési út használatával:

**..\\TollApp\\TollApp\\adatok**

Ez a mappa a következő fájlokat tartalmazza:

* Entry.JSON
* Exit.JSON
* Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>1. kérdés: Sűrítéses egy téren kiállítási száma
1. Nyissa meg az Azure-portálon, és nyissa meg a létrehozott Azure Stream Analytics-feladathoz. Kattintson a **lekérdezés** lapra, és illessze be a fenti szakaszban leírt lekérdezést.

2. A mintaadatok lekérdezése ellenőrzéséhez feltölteni az adatokat a EntryStream bemeneti be kattintva a... szimbólum, és kiválasztja **tölthet fel fájlból adatot**.

    ![Képernyőfelvétel a Entry.json fájl](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. A megjelenő panelen válassza ki a fájlt (Entry.json) a helyi számítógépen, és kattintson a **OK**. A **teszt** ikon fog most megvilágítására és kattintható lehet.
   
    ![Képernyőfelvétel a Entry.json fájl](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. Ellenőrizze, hogy a lekérdezés eredménye a várt módon:
   
    ![A vizsgálati eredmények](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>2. kérdés: Jelentés fordított időt az áthaladás, a téren kiállítási minden autó
Rendelkezik egy autó az áthaladás, a téren szükséges átlagos idő segít a folyamat, és a felhasználói élmény hatékonyságának ellenőrzéséhez.

A teljes időnek megkereséséhez kell csatlakoztatni a EntryTime adatfolyam a ExitTime adatfolyam. Az adatfolyamok TollId és LicencePlate oszlopokon csatlakozni fog. A **csatlakozás** operátor szükséges, amely leírja a csatlakoztatott események elfogadható időeltérése historikus eltérést megadását. Használandó **DATEDIFF** működnek, adja meg, hogy legyen-e az események egymástól legfeljebb 15 perc. Akkor is érvényesek a **DATEDIFF** való kilépéshez és az bejegyzés alkalommal kiszámítására, a tényleges idő egy autó a téren állomás futtatásával töltött. Vegye figyelembe a különbség az, felhasználásának **DATEDIFF** használat esetén a egy **válasszon** utasítás helyett egy **csatlakozás** feltétel.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Ez a lekérdezés teszteléséhez frissíteni a lekérdezést a **lekérdezés** a feladathoz. Adja hozzá a vizsgálat a **ExitStream** ugyanúgy, mint az **EntryStream** fent lett megadva.
   
2. Kattintson a **teszt**.

3. Jelölje be a lekérdezés teszteléséhez és a kimeneti megtekintése:
   
    ![A vizsgálat eredményének](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>3. kérdés: Jelentés minden, kereskedelmi járművekről gyűjtött lejárt regisztrálása
Az Azure Stream Analytics statikus pillanatképeket készíteni segítségével csatlakozik a historikus adatfolyamokat. Ez a funkció bemutatásához, használja a következő minta kérdést.

Ha egy kereskedelmi vehicle regisztrálva van a téren vállalatot, azt is haladnak át a téren kiállítási nélkül lett a vizsgálathoz. Kereskedelmi Vehicle regisztrációs keresési tábla használandó azonosítsa az összes kereskedelmi járművekről gyűjtött regisztráció lejárt.

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

Referenciaadatok a lekérdezés teszteléséhez ját definiálja a hivatkozási adatait, amely már elvégezte a bemeneti forrása.

Ez a lekérdezés teszteléséhez illessze be a lekérdezést a **lekérdezés** lapra, majd **tesztelése**, és adja meg a két bemeneti forrás és a regisztráció az adatokat, és kattintson a **tesztelése**.  
   
![A vizsgálat eredményének](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladat indítása
Most azt az idő, a konfiguráció befejezését, majd indítsa el a feladatot. Mentse a kérdés 3 lekérdezést, amely eredményez, amely megfelel a sémája kimeneti a **TollDataRefJoin** kimeneti táblához.

Ugrás a feladat **IRÁNYÍTÓPULT**, és kattintson a **START**.

![A Start gombra, a feladat irányítópultjának képernyőképe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

A megnyíló párbeszédpanelen módosítsa a **START kimeneti** beosztásba **egyéni idő**. Az aktuális időpont előtt egy óra módosítani. Ez a módosítás biztosítja, hogy az eseményközpontból összes esemény dolgoznak fel az eseményeket generálni az oktatóanyag elején indítása óta. Most kattintson a **Start** gombra kattintva indítsa el a feladatot.

![Egyéni idő kiválasztása](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

A feladat kezdési néhány percet is igénybe vehet. A legfelső szintű oldalon az állapot látható a Stream Analytics.

![A feladat állapotának képernyőképe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>A Visual Studio ellenőrzésének az eredménye
1. Nyissa meg a Visual Studio Server Explorer, és kattintson a jobb gombbal a **TollDataRefJoin** tábla.
2. Kattintson a **tábla adatok megjelenítése** a feladat eredményének megtekintéséhez.
   
    !["Tábla adatok megjelenítése" a Server Explorer kiválasztása](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Horizontális felskálázás az Azure Stream Analytics feladatok
Az Azure Stream Analytics célja, hogy rugalmasan méretezési, hogy a nagy mennyiségű adat is képes kezelni. Az Azure Stream Analytics lekérdezési használhatja egy **PARTITION BY** záradékot, hogy a rendszer, hogy ez a lépés ki lesz skálázva. **PartitionId** különleges oszlop, amely a rendszer hozzáadja a partícióazonosító: a bemeneti (eseményközpont) megfelelően.

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Állítsa le a jelenlegi feladatot, frissítse a lekérdezést a **lekérdezés** lapot, és nyissa meg a **beállítások** áttételi a projekt irányítópultján. Kattintson a **méretezési**.
   
    **ADATFOLYAM-egységek** határozzák meg a számítási teljesítményt, amely a feladatok fogadására.
2. Módosítsa az vetett 6 1.
   
    ![Képernyőkép a 6 adatfolyam-továbbítási egység kiválasztása](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. Lépjen a **KIMENETEK** lapon, és módosítsa az SQL-tábla neve **TollDataTumblingCountPartitioned**.

Ha a feladat indítása, Azure Stream Analytics munkahelyi szét több számítási erőforrással is javítható az átviteli teljesítmény. Vegye figyelembe, hogy a TollApp alkalmazás is küld-e események által TollId particionálva.

## <a name="monitor"></a>Figyelés
A **FIGYELŐ** terület tartalmazza a futó feladat statisztikája. Konfigurációra nincs szükség a tárolók használatához először fiók ugyanabban a régióban (name téren Ez a dokumentum többi hasonlóan).   

![A figyelő képernyőképe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Van-e hozzáférési **tevékenységi naplóit** a projekt irányítópultján **beállítások** terület is.


## <a name="conclusion"></a>Összegzés
Ez az oktatóanyag az Azure Stream Analytics szolgáltatás bevezetett. Azt mutatja be- és kimenetekkel a Stream Analytics-feladat konfigurálása. Az oktatóanyag ismertetése a téren forgatókönyvet használja, a problémák merülhetnek fel az adatok az Azure Stream Analytics egyszerű SQL-szerű lekérdezéseket mozgásérzékelési, és hogyan azok megoldhatók a területen található általános típusú. Az oktatóanyag ismerteti a SQL bővítmény szerkezetek az ideiglenes adatai. Bemutatta, hogyan csatlakoztathatók a adatfolyamokat, az adatfolyamot a statikus referenciaadatok kiegészítése, és nagyobb átviteli sebesség eléréséhez lekérdezés horizontális.

Ez az oktatóanyag egy jó bevezetést tartalmaz, bár nincs kész bármilyen módon. A SAQL nyelvet további lekérdezési mintáinak található [gyakori Stream Analytics használati minták példák lekérdezése](stream-analytics-stream-analytics-query-patterns.md).
Tekintse meg a [online dokumentáció](https://azure.microsoft.com/documentation/services/stream-analytics/) Azure Stream Analytics tájékozódhat.

## <a name="clean-up-your-azure-account"></a>Az Azure-fiók törlése
1. Állítsa le a Stream Analytics-feladat az Azure portálon.
   
    A Setup.ps1 parancsfájl létrehoz két eseményközpontokhoz és SQL-adatbázis. A következő útmutatók segítenek karbantartása erőforrások az oktatóanyag végén.
2. A PowerShell ablakban írja be a **.\\ Cleanup.ps1** elindítani a parancsfájlt, amely törli az oktatóanyagban használt erőforrások.
   
   > [!NOTE]
   > Erőforrások neve azonosítja. Győződjön meg arról, hogy az egyes elemek eltávolításának megerősítése előtt gondosan ellenőrizze.
   > 
   > 


