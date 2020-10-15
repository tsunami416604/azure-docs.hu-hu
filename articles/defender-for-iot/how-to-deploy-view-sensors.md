---
title: Az Azure Defender IoT-beli beépített érzékelők megtekintése és kezelése
description: Ez a cikk azt ismerteti, hogyan lehet megtekinteni és törölni a beérkező érzékelőket, valamint új aktiválási fájlokat letölteni az Azure Defender IoT-beli beépített érzékelőkhöz.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094407"
---
# <a name="view-and-manage-onboarded-sensors"></a>Beépített érzékelők megtekintése és kezelése

Ez a cikk ismerteti, hogyan lehet megtekinteni és törölni a beérkező érzékelőket, valamint új aktiválási fájlokat letölteni a beépített érzékelőkhöz.

## <a name="update-sensor-management-mode"></a>Az érzékelő felügyeleti módjának frissítése

Előfordulhat, hogy frissíteni szeretné az érzékelő által felügyelt módot. Ha ezt teszi, el kell távolítania az aktuális érzékelőt az érzékelő kezelése lapról, és fel kell töltenie egy új aktiválási fájlt.

- Helyileg felügyelt mód **helyett a felhőalapú felügyelt mód használata**: frissítse a helyileg felügyelt érzékelőhöz tartozó aktiválási fájlt egy felhőalapú felügyelt érzékelőhöz tartozó aktiválási fájllal. Az újraaktiválást követően a rendszer az érzékelő és az Azure Defender for IoT portálon is megjeleníti az érzékelők észleléseit. Az újraaktiválási fájl sikeres feltöltése után az újonnan észlelt riasztási adatokat az Azure-ba küldi a rendszer.

- **A felhőalapú felügyelt mód helyett helyileg felügyelt módban dolgozhat**: frissítse egy felhőalapú felügyelt érzékelő aktiválási fájlját egy helyileg felügyelt érzékelőhöz tartozó aktiválási fájllal. Az újraaktiválást követően az érzékelő észlelési adatai csak az érzékelőben jelennek meg.

- **Az érzékelő hozzárendelése egy új IoT hubhoz**: Előfordulhat, hogy az érzékelőt egy új IoT hub szeretné hozzárendelni. Ehhez újra regisztrálnia kell az érzékelőt, és fel kell töltenie egy új aktiválási fájlt.

**Az érzékelő újraaktiválása:**

1. Navigáljon az Azure Defender for IoT, **érzékelő kezelése** lapra.

2. Válassza ki azt az érzékelőt, amelyhez új aktiválási fájlt szeretne feltölteni.

3. Törölje.

4. Az érzékelőt újra be kell készíteni a bevezetési **oldalról az új** mód vagy egy új IoT hub.

5. Töltse le az aktiválási fájlt az **aktiválási fájl letöltése** lapról.

6. Jelentkezzen be az Azure Defender for IoT-érzékelő konzolra.

7. Az érzékelő konzolon válassza a **Rendszerbeállítások** , majd az **újraaktiválás**lehetőséget.

   ![Az újraaktiválási nézet képernyőképe](media/updates/image14.png)

8. Válassza a **feltöltés** lehetőséget, és válassza ki a mentett fájlt.

9. Válassza az **aktiválás**lehetőséget.
 
## <a name="delete-sensors"></a>Érzékelők törlése

Ha töröl egy felhőalapú felügyelt érzékelőt, az adatokat a rendszer nem küldi el a IoT Hub.

Törölje a helyileg felügyelt érzékelőket, ha már nem dolgozik velük.

**Érzékelők törlése:**

1. Navigáljon az Azure Defender for IoT **Sensor Management** oldalára.

2. Válassza ki a törölni kívánt érzékelőket.

3. Válassza az **érzékelő törlése**lehetőséget.

## <a name="next-steps"></a>Következő lépések

A konfigurációs beállításokkal kapcsolatos további információkért folytassa a modul konfigurálásának útmutatójában.
> [!div class="nextstepaction"]
> [Útmutató a modul konfigurálásához](./how-to-agent-configuration.md)
