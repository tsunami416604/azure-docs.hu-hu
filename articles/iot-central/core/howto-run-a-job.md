---
title: Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central-feladatok lehetővé teszik a csoportos eszközök felügyeleti funkcióit, például a tulajdonságok frissítését vagy a parancsok végrehajtását.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80157755"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban

A csatlakoztatott eszközök méretezéséhez a feladatok használatával Microsoft Azure IoT Central is használhatja. A feladatok lehetővé teszik a tömeges frissítését az eszköz tulajdonságaira és a parancsok futtatására. Ez a cikk bemutatja, hogyan kezdheti el a feladatok használatát a saját alkalmazásban.

## <a name="create-and-run-a-job"></a>Feladatok létrehozása és futtatása

Ebből a szakaszból megtudhatja, hogyan hozhat létre és futtathat feladatokat. Bemutatja, hogyan állíthatja be a fény küszöbértékét a logisztikai átjáró eszközeinek csoportjához.

1. Navigáljon a **feladatok** elemre a bal oldali ablaktáblán.

2. Új feladatok létrehozásához válassza az **+ új** elemet:

    ![Új feladatok létrehozása](./media/howto-run-a-job/createnewjob.png)

3. Adja meg a létrehozandó feladatot azonosító nevet és leírást.

4. Válassza ki azt a céleszköz-csoportot, amelyre alkalmazni szeretné a feladatot. Megtekintheti, hogy hány eszközre vonatkozik a feladatok konfigurációja az **Összefoglalás** szakaszban.

5. Ezután válassza a **felhő tulajdonság**, **tulajdonság** vagy **parancs** lehetőséget a konfigurálandó feladattípusként. A **Tulajdonságok** konfigurációjának beállításához válasszon ki egy tulajdonságot, és állítsa be az új értékét. **Parancs**beállításához válassza ki a futtatandó parancsot. A tulajdonság-feladatok több tulajdonságot is beállíthat:

    ![Feladatok konfigurálása](./media/howto-run-a-job/configurejob.png)

6. A feladatot a létrehozás után válassza a **Futtatás** vagy a **Mentés**lehetőséget. A feladat most megjelenik a fő **feladatok** oldalon. Ezen az oldalon megtekintheti az aktuálisan futó feladatot és a korábban futtatott vagy mentett feladatok előzményeit. A mentett feladatok bármikor megnyithatók a Szerkesztés folytatásához vagy a futtatásához:

    ![Feladatok megtekintése](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > A korábban futtatott feladatok esetében 30 nappal korábbi előzményeket tekinthet meg.

7. A feladatok áttekintéséhez válassza ki a listából a megtekinteni kívánt feladatot. Ez az Áttekintés a feladatok részleteit, az eszközöket és az Eszközállapot-értékeket tartalmazza. Ebből az áttekintésből kiválaszthatja a feladatok **részleteinek letöltése** lehetőséget is, ha le szeretné tölteni a feladatok részleteit tartalmazó CSV-fájlt, beleértve az eszközöket és az állapotukat is. Ezek az információk a hibaelhárításhoz hasznosak lehetnek:

    ![Eszközállapot megtekintése](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Feladatok kezelése

Az egyik futó feladat leállításához nyissa meg, majd válassza a **Leállítás**lehetőséget. A feladatok állapota úgy változik, hogy a rendszer leállítja a feladatot. Az **Összefoglalás** szakaszban látható, hogy mely eszközök lettek végrehajtva, sikertelenek, vagy még függőben vannak.

A jelenleg leállított feladatok futtatásához jelölje ki azt, majd válassza a **Futtatás**lehetőséget. A feladatok állapota úgy változik, hogy az tükrözze a feladatot. Az **Összefoglalás** szakasz a legújabb folyamattal folytatja a frissítést.

![Feladatok kezelése](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Feladatok másolása

Az egyik meglévő feladat másolásához jelölje ki azt a **feladatok** lapon, majd válassza a **Másolás**lehetőséget. Ekkor megnyílik a feladatok konfigurációjának egy másolata, amelyet **a rendszer a** feladatok nevéhez hozzáfűz. Az új feladatot az alábbiak szerint mentheti vagy futtathatja:

![Másolási feladatok](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>A feladatok állapotának megtekintése

A feladatok létrehozása után az **állapot** oszlop a feladatokhoz tartozó legújabb állapotüzenetek szerint frissül. A következő táblázat felsorolja a lehetséges állapotinformációkat:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejezve            | Ezt a feladatot minden eszközön végrehajtotta a rendszer.              |
| Sikertelen               | A feladatot nem sikerült végrehajtani, és nem hajtották végre teljesen az eszközökön.  |
| Függőben              | Ez a feladattípus még nem kezdte meg az eszközökön való végrehajtást.         |
| Fut              | Ez a feladatok jelenleg az eszközökön vannak végrehajtva.             |
| Leállítva              | Ezt a feladatot a felhasználó manuálisan leállította.           |

Az állapotjelző üzenetet a feladatokban található eszközök áttekintése követi. A következő táblázat felsorolja a lehetséges Eszközállapot-értékeket:

| Állapotüzenet       | Állapot jelentése                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sikeres            | Azon eszközök száma, amelyeken a feladatot sikeresen végrehajtották.       |
| Sikertelen               | Azon eszközök száma, amelyeken nem sikerült végrehajtani a feladatot.       |

### <a name="view-the-device-status"></a>Az eszköz állapotának megtekintése

A feladatok és az összes érintett eszköz állapotának megtekintéséhez nyissa meg a feladatot. Ha le szeretne tölteni egy CSV-fájlt, amely tartalmazza a feladatok részleteit, beleértve az eszközök listáját és az állapotukat, válassza a **feladatok adatainak letöltése**lehetőséget. Az egyes eszközök neve mellett a következő állapotüzenetek egyike jelenik meg:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejezve            | A feladatot végrehajtotta ezen az eszközön.                                     |
| Sikertelen               | A feladatot nem sikerült végrehajtani az eszközön. A hibaüzenet további információkat jelenít meg.  |
| Függőben              | A feladatot még nem hajtották végre ezen az eszközön.                                   |

> [!NOTE]
> Ha egy eszköz törölve lett, nem választhatja ki az eszközt. Az eszköz AZONOSÍTÓjának törlésével jelenik meg.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre feladatokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Eszközök kezelése](howto-manage-devices.md)
- [Az eszköz sablonjának verziója](howto-version-device-template.md)
