---
title: Stream Analytics-feladat létrehozása az Azure Portal használatával | Microsoft Docs
description: Ez az útmutató a Stream Analytics-feladatok létrehozásának első lépéseit mutatja be, és segítségével megtanulhatja beállítani a bemeneteket és kimeneteket, és létrehozni egy lekérdezést.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 11/21/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 7762a48fd34973872fe4d0b00906a03a18d52867
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311925"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával

Az útmutató azt mutatja be, hogy hogyan kezdhet hozzá egy Stream Analytics-feladat létrehozásához. Ebben a rövid útmutatóban a Stream Analytics-feladat, amely beolvassa a valós idejű streamelési adatok és a egy nagyobb, mint 27 hőmérséklet-szűrők üzenetek határozhatók meg. A Stream Analytics-feladat adatokat olvasni az IoT Hub device, átalakítja az adatokat, és írhat vissza az adatokat blob Storage-tárolóba. A rövid útmutatóban használt bemeneti adatok egy Raspberry Pi online szimulátor hozza létre. 

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

Mielőtt meghatározná a Stream Analytics-feladat, készítse elő az adatokat, amelyeket később a feladat bemeneti van konfigurálva. A feladat bemeneti adatainak előkészítéséhez, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.

3. Az a **az IoT Hub** panelen adja meg a következőket:
   
   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Előfizetés  | \<Az Ön előfizetése\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Régió  |  \<Válassza ki a felhasználóihoz legközelebb eső régiót\> | Válasszon egy földrajzi hely, ahol az IoT hubot üzemeltetni. A felhasználókhoz legközelebb eső helyet használja. |
   |Iot Hub-példány neve  | MyASAIoTHub  |   Válassza ki az IoT Hub nevét.   |

   ![IoT Hub létrehozása](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Válassza ki **tovább: állítsa be, mérete és méretezése**.

5. Válasszon **tarifacsomag és méretet**. Ez a rövid útmutatóhoz válassza a **F1 – ingyenes** réteg, ha továbbra is elérhető az előfizetésén. További információkért lásd: [IoT Hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Méretezés és skálázhatja az IoT hubhoz](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Az IoT Hub adatait, és kattintson a **létrehozás**. Az IoT Hub létrehozása néhány percet is igénybe vehet. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

7. Az IoT Hub navigációs menüben kattintson a **Hozzáadás** alatt **IoT-eszközök**. Adjon hozzá egy **Eszközazonosító** kattintson **mentése**.

   ![Hozzáad egy eszközt az IoT hubhoz](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszköz regisztrációját az **IoT-eszközök** listája. Másolás a **kapcsolati karakterlánc – elsődleges kulcs** , és mentse a Jegyzettömbben későbbi használat céljából.

   ![Az IoT Hub eszköz kapcsolati karakterlánc másolása](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Blob-tároló létrehozása

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Storage** > **Tárfiók** lehetőséget.

2. Az a **storage-fiók létrehozása** panelen adjon meg egy fiók nevét, helyen és erőforráscsoportban csoportjához. Az IoT Hub létrehozott, válassza az ugyanazon a helyen és erőforráscsoportban. Kattintson a **felülvizsgálat + létrehozása** a fiók létrehozásához.

   ![Storage-fiók létrehozása](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. A tárfiók létrehozása után válassza ki a **Blobok** csempét a **áttekintése** ablaktáblán.

   ![Tárfiókok áttekintése](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Az a **Blob Service** lapon jelölje be **tároló** és adja meg például a tároló nevét *container1*. Hagyja a **nyilvános hozzáférés szintje** , **privát (nincs névtelen hozzáférés)** válassza **OK**.

   ![Blobtároló létrehozása](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Jelentkezzen be az Azure portálra.

2. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.  

3. A megjelenő listában válassza az **Adatok + analitika** > **Stream Analytics-feladat** lehetőséget.  

4. Töltse ki a Stream Analytics-feladat paneljét a következő információkkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Feladat neve   |  MyASAJob   |   Adjon meg egy nevet a Stream Analytics-feladat azonosításához. A Stream Analytics-feladat neve csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és legalább 3, de legfeljebb 63 karakter hosszúságú lehet. |
   |Előfizetés  | \<Az Ön előfizetése\> |  Válassza ki a feladathoz használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza ki ugyanazt az erőforráscsoportot, az IoT hubhoz. |
   |Hely  |  \<Válassza ki a felhasználóihoz legközelebb eső régiót\> | Válassza ki azt a földrajzi helyet, ahol a Stream Analytics-feladatot üzemeltetni szeretné. A nagyobb teljesítmény és az adatátviteli díjak csökkentése érdekében válassza a felhasználóihoz legközelebb eső helyet. |
   |Streamelési egységek  | 1  |   A streamelési egységek a feladatok végrehajtásához felhasznált számítási erőforrásokat jelölik. Ez az érték alapértelmezés szerint 1. A streamelési egységek skálázásával kapcsolatos további tudnivalókért olvassa el a [Skálázás streamelési egységekkel](stream-analytics-streaming-unit-consumption.md) című cikket.   |
   |Üzemeltetési környezet  |  Felhő  |   A Stream Analytics-feladatok a felhőben vagy a peremhálózaton is üzembe helyezhetők. Felhőbe történő telepítéskor az Azure Cloudba telepítheti a feladatot, Edge esetén pedig egy IoT Edge-eszközre. |

   ![Feladat létrehozása](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Ha a feladatot egy irányítópulton szeretné elhelyezni, válassza ki a **Rögzítés az irányítópulton** jelölőnégyzetet, ezután pedig válassza a **Létrehozás** lehetőséget.  

6. Megjelenik egy *üzembe helyezés folyamatban...*  tetején megjelenő értesítés, a böngészőablak jobb. 

## <a name="configure-job-input"></a>Feladatbemenet konfigurálása

Ebben a szakaszban konfigurálhatja a Stream Analytics-feladat bemenete az IoT Hub eszköz. A rövid útmutató az előző szakaszban létrehozott IoT Hub használata.

1. Keresse meg a Stream Analytics-feladatot.  

2. Válassza ki **bemenetek** > **Stream bemenet hozzáadása** > **az IoT Hub**.  

3. Töltse ki a **az IoT Hub** lap a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias  |  IoTHubInput   |  Adja meg a feladat bemenetének azonosító nevét.   |
   |Előfizetés   |  \<Az Ön előfizetése\> |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |IoT Hub  |  MyASAIoTHub |  Adja meg az előző szakaszban létrehozott IoT hub nevét. |

4. Az egyéb beállításokat hagyja alapértelmezett értéken, és válassza a**Mentés** lehetőséget a beállítások mentéséhez.  

   ![A bemeneti adatok konfigurálása](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Feladatkimenet konfigurálása

1. Keresse meg a korábban létrehozott Stream Analytics-feladatot.  

2. Válassza ki **kimenetek** > **Hozzáadás** > **a Blob storage-**.  

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

3. Ebben a példában a lekérdezés olvassa be az adatokat az IoT hubról, és átmásolja egy új fájlt a blobba. Kattintson a **Mentés** gombra.  

   ![A transzformáció konfigurálása](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatásához

1. Nyissa meg a [Raspberry Pi az Azure IoT Online szimulátor](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. A sor 15 a helyőrzőt cserélje le az Azure IoT Hub eszköz kapcsolati karakterláncát az egyik előző szakaszban mentett.

3. Kattintson a **Run** (Futtatás) parancsra. A kimenet az érzékelő adatokat és az IoT hubnak küldött üzenetek kell megjelennie.

   ![Raspberry Pi az Azure IoT Online szimulátor](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. Térjen vissza a feladat áttekintési oldalára, és válassza az **Indítás** lehetőséget.

2. Alatt **indítási feladat**válassza **most**, az a **feladatkimenet kezdési idő** mező. Ezután válassza ki **Start** elindítani a feladatot.

3. Néhány perc elteltével keresse meg a portálon azt a tárfiókot és tárolót, amelyet a feladat kimeneteként beállított. Mostanra a kimeneti fájl megjelent a tárolóban. Az első indítás után pár percbe telhet, amíg a feladat elindul, de ezt követően mindig futni fog, amint adatok érkeznek.  

   ![A transzformált adatok](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe az Azure Portallal. A Stream Analytics-feladatokat a [PowerShell](stream-analytics-quick-create-powershell.md) és a [Visual Studio](stream-analytics-quick-create-vs.md) használatával is üzembe helyezheti.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)

