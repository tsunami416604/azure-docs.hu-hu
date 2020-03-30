---
title: Rövid útmutató – Stream Analytics-feladat létrehozása az Azure Portal használatával
description: Ez az útmutató a Stream Analytics-feladatok létrehozásának első lépéseit mutatja be, és segítségével megtanulhatja beállítani a bemeneteket és kimeneteket, és létrehozni egy lekérdezést.
author: mamccrea
ms.author: mamccrea
ms.date: 06/21/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 4abf5078a005f9d928397d9666e7f2bc55d65f19
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75431556"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával

Az útmutató azt mutatja be, hogy hogyan kezdhet hozzá egy Stream Analytics-feladat létrehozásához. Ebben a rövid útmutatóban definiálegy Stream Analytics-feladatot, amely valós idejű streamelési adatokat olvas be, és 27-nél nagyobb hőmérsékletű üzeneteket szűr. A Stream Analytics-feladat adatokat olvas fel az IoT Hubból, átalakítja az adatokat, és visszaírja az adatokat egy tárolóba a blob storage-ban. A rövid útmutatóban használt bemeneti adatokat egy Raspberry Pi online szimulátor hozza létre. 

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics feladat meghatározása előtt elő kell készítenie a bemeneti adatokat. A valós idejű érzékelő adatok az IoT Hub, amely később konfigurált feladat bemeneti. A feladat által igényelt bemeneti adatok előkészítéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza > **az** >  **Erőforrás-internet**létrehozása**IoT Hub**lehetőséget.

3. Az **IoT Hub** ablaktáblán adja meg a következő adatokat:
   
   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Előfizetés  | \<Az Ön előfizetése\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Régió  |  \<Válassza ki a felhasználóihoz legközelebb eső régiót\> | Válassza ki azt a földrajzi helyet, ahol az IoT Hub üzemeltetheti. Használja a felhasználókhoz legközelebb eső helyet. |
   |IoT hub neve  | MyASAIoTHub  |   Válassza ki az IoT Hub nevét.   |

   ![IoT Hub létrehozása](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Válassza a **Tovább lehetőséget: Méret és méretezés beállítása**lehetőséget.

5. Válasszon **tarifacsomag és méretet**. Ehhez a rövid útmutatóhoz válassza az **F1 – Ingyenes** szintet, ha az még elérhető az előfizetésén. További információ: [IoT Hub díjszabás.](https://azure.microsoft.com/pricing/details/iot-hub/)

   ![Az IoT Hub méretezése és méretezése](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át az IoT Hub adatait, és kattintson **a Létrehozás gombra.** Az IoT Hub létrehozása eltarthat néhány percet. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

7. Az IoT Hub navigációs menüjében kattintson **a Hozzáadás** gombra az **IoT-eszközök**csoportban. Adja hozzá **az eszközazonosítót,** és kattintson a **Mentés gombra.**

   ![Eszköz hozzáadása az IoT Hubhoz](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszközt az **IoT-eszközök** listájából. Másolja a **Kapcsolat karakterláncot – elsődleges kulcsot,** és mentse egy jegyzettömbbe, hogy később használhassa.

   ![IoT Hub-eszköz kapcsolati karakterláncának másolása](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Blob-tárterület létrehozása

1. Az Azure Portal bal felső sarkában válassza az Erőforrás > **tárterület-fiók****Storage** >  **létrehozása lehetőséget.**

2. A **Tárfiók létrehozása** ablaktáblán adja meg a tárfiók nevét, helyét és erőforráscsoportját. Válassza ki ugyanazt a helyet és erőforráscsoportot, mint a létrehozott IoT Hub. Ezután kattintson **a Véleményezés + létrehozás** gombra a fiók létrehozásához.

   ![Storage-fiók létrehozása](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. A tárfiók létrehozása után válassza ki a **Blobok csempét** az **Áttekintő** ablaktáblán.

   ![Tárfiókok áttekintése](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. A **Blob Service** lapon válassza a **Tároló** lehetőséget, és adja meg a tároló nevét, például *a container1.* Hagyja a **nyilvános hozzáférési szintet** **privátként (nincs névtelen hozzáférés),** és válassza **az OK**gombot.

   ![Blobtároló létrehozása](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Jelentkezzen be az Azure portálra.

2. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.  

3. Válassza az **Analytics** > **Stream Analytics-feladat lehetőséget** az eredménylistából.  

4. Töltse ki a Stream Analytics-feladat paneljét a következő információkkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Feladat neve   |  MyASAJob között   |   Adjon meg egy nevet a Stream Analytics-feladat azonosításához. A Stream Analytics-feladat neve csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és legalább 3, de legfeljebb 63 karakter hosszúságú lehet. |
   |Előfizetés  | \<Az Ön előfizetése\> |  Válassza ki a feladathoz használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza ki ugyanazt az erőforráscsoportot, mint az IoT Hub. |
   |Hely  |  \<Válassza ki a felhasználóihoz legközelebb eső régiót\> | Válassza ki azt a földrajzi helyet, ahol a Stream Analytics-feladatot üzemeltetni szeretné. A nagyobb teljesítmény és az adatátviteli díjak csökkentése érdekében válassza a felhasználóihoz legközelebb eső helyet. |
   |Streamelési egységek  | 1  |   A Streamelési egységek azoknak a számítási erőforrásoknak felelnek meg, amelyek a feladat futtatásához szükségesek. Ez az érték alapértelmezés szerint 1. A streamelési egységek skálázásával kapcsolatos további tudnivalókért olvassa el a [Skálázás streamelési egységekkel](stream-analytics-streaming-unit-consumption.md) című cikket.   |
   |Üzemeltetési környezet  |  Felhő  |   A Stream Analytics-feladatok a felhőben vagy a peremhálózaton is üzembe helyezhetők. A Cloud lehetővé teszi az Azure Cloud üzembe helyezését, az Edge pedig lehetővé teszi az IoT Edge-eszközre való üzembe helyezést. |

   ![Feladat létrehozása](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Ha a feladatot egy irányítópulton szeretné elhelyezni, válassza ki a **Rögzítés az irányítópulton** jelölőnégyzetet, ezután pedig válassza a **Létrehozás** lehetőséget.  

6. Meg kell jelennie egy *folyamatban lévő központi telepítésnek...* értesítés jelenik meg a böngészőablak jobb felső részén. 

## <a name="configure-job-input"></a>Feladatbemenet konfigurálása

Ebben a szakaszban konfigurálja az IoT Hub-eszköz bemeneta a Stream Analytics-feladathoz. Használja a rövid útmutató előző szakaszában létrehozott IoT Hub.

1. Keresse meg a Stream Analytics-feladatot.  

2. Válassza **a Bemenetek** > hozzáadása az**IoT Hub****bemenetének** > hozzáadása lehetőséget.  

3. Töltse ki az **IoT Hub** lapot a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias  |  IoTHubInput   |  Adja meg a feladat bemenetének azonosító nevét.   |
   |Előfizetés   |  \<Az Ön előfizetése\> |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |IoT Hub  |  MyASAIoTHub |  Adja meg az előző szakaszban létrehozott IoT Hub nevét. |

4. Az egyéb beállításokat hagyja alapértelmezett értéken, és válassza a**Mentés** lehetőséget a beállítások mentéséhez.  

   ![A bemeneti adatok konfigurálása](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Feladatkimenet konfigurálása

1. Keresse meg a korábban létrehozott Stream Analytics-feladatot.  

2. Válassza **a Kimenetek** > blob**hozzáadása** > **tároló lehetőséget.**  

3. A **Blobtároló** oldalon adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Kimeneti alias |   BlobOutput   |   Adja meg a feladat kimenetének azonosító nevét. |
   |Előfizetés  |  \<Az Ön előfizetése\>  |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |Tárfiók |  asaquickstartstorage |   Válassza ki vagy adja meg a tárfiók nevét. Ha a tárfiókok ugyanahhoz az előfizetéshez tartoznak, a rendszer automatikusan észleli a nevüket.       |
   |Tároló |   container1  |  Válassza ki a tárfiókjában létrehozott meglévő tárolót.   |

4. Az egyéb beállításokat hagyja alapértelmezett értéken, és válassza a**Mentés** lehetőséget a beállítások mentéséhez.  

   ![Kimenet konfigurálása](./media/stream-analytics-quick-create-portal/configure-asa-output.png)
 
## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

1. Keresse meg a korábban létrehozott Stream Analytics-feladatot.  

2. Válassza ki a **Lekérdezés** lehetőséget, és frissítse a lekérdezés adatait a következő módon:  

   ```sql
   SELECT *
   INTO BlobOutput
   FROM IoTHubInput
   HAVING Temperature > 27
   ```

3. Ebben a példában a lekérdezés beolvassa az adatokat az IoT Hubból, és másolja azokat egy új fájlba a blobban. Kattintson a **Mentés** gombra.  

   ![A transzformáció konfigurálása](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatása

1. Nyissa meg a [Raspberry Pi Azure IoT Online Simulator t.](https://azure-samples.github.io/raspberry-pi-web-simulator/)

2. Cserélje le a helyőrzőt a 15- ös sorban az Azure IoT Hub-eszköz kapcsolati karakterláncára, amelyet egy előző szakaszban mentett.

3. Kattintson a **Futtatás gombra.** A kimenetnek meg kell jelennie az Érzékelő adatait és az IoT Hubnak küldött üzeneteket.

   ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. Térjen vissza a feladat áttekintési oldalára, és válassza az **Indítás** lehetőséget.

2. A **Feladat indítása**csoportban válassza a **Most**lehetőséget a **Projekt kimeneti kezdési időpontja** mezőben. Ezután válassza a **Start** gombot a feladat elindításához.

3. Néhány perc elteltével keresse meg a portálon azt a tárfiókot és tárolót, amelyet a feladat kimeneteként beállított. Mostanra a kimeneti fájl megjelent a tárolóban. Az első indítás után pár percbe telhet, amíg a feladat elindul, de ezt követően mindig futni fog, amint adatok érkeznek.  

   ![A transzformált adatok](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje az erőforráscsoportot, a Stream Analytics-feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe az Azure Portallal. Stream Analytics-feladatokat a [PowerShell,](stream-analytics-quick-create-powershell.md)a [Visual Studio](stream-analytics-quick-create-vs.md)és a Visual [Studio-kód](quick-create-vs-code.md)használatával is telepíthet.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)
