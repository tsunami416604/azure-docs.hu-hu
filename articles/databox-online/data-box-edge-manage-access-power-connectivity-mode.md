---
title: A Microsoft Azure Data Box Edge eszközhöz való hozzáféréshez, a teljesítmény és a csatlakozási mód |} A Microsoft Docs
description: Ismerteti, hogyan kezelheti a hozzáférést, a teljesítmény és a kapcsolódási módja az, hogy a segítségével adatátvitel Azure-bA az Azure Data Box Edge-eszköz
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 813563b500b9365289285a89536f2724fb87acad
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417802"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Az Azure Data Box Edge kezelheti a hozzáférést, a teljesítmény és a csatlakozási mód

Ez a cikk ismerteti, hogyan kezelheti a hozzáférést, a teljesítmény és a csatlakozási mód az Azure Data Box Edge. Ezeket a műveleteket a helyi webes felhasználói felületen vagy az Azure Portalon keresztül történik.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszköz-hozzáférés kezelése
> * Csatlakozási mód kezelése
> * Energiagazdálkodás


## <a name="manage-device-access"></a>Eszköz-hozzáférés kezelése

A Data Box Edge-eszközön a hozzáférést az eszköz jelszó használatát vezérlik majd. Módosíthatja a jelszót a helyi webes felhasználói felületen. Az eszköz jelszavát az Azure Portalon is alaphelyzetbe állíthatja.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Kövesse az alábbi lépéseket a helyi felhasználói felületen, az eszköz jelszavának módosítása.

1. A helyi webes felhasználói felületen váltson **karbantartási > jelszó módosítása**.
2. Adja meg a jelenlegi jelszavát, majd az új jelszót. A megadott jelszónak 8 – 16 karakter hosszúságúnak kell lennie. A következő karakterek közül 3 a jelszóban: nagybetű, kisbetű, számjegyeket és speciális karaktereket. Az új jelszót.

    ![Jelszó megváltoztatása](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Válassza ki **jelszó módosítása**.
 
### <a name="reset-device-password"></a>Eszköz jelszavának alaphelyzetbe állítása

A visszaállítási munkafolyamat nem szükséges majd előkeresnie a régi jelszót, és akkor hasznos, ha a jelszó elvész. Ez a munkafolyamat az Azure Portalon történik.

1. Az Azure Portalon lépjen a **áttekintés > rendszergazdai jelszó alaphelyzetbe állítása**.

    ![Új jelszó létrehozása](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Adja meg az új jelszót, és erősítse azt. A megadott jelszónak 8 – 16 karakter hosszúságúnak kell lennie. A következő karakterek közül 3 a jelszóban: nagybetű, kisbetű, számjegyeket és speciális karaktereket. Válassza ki **alaphelyzetbe**.

    ![Új jelszó létrehozása](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Csatlakozási mód kezelése

Az alapértelmezett teljes csatlakoztatott mód szereplőkkel eszközét is futtathatja részlegesen csatlakoztatva, vagy teljesen leválasztott módban. Az egyes módokban, az alábbiakban ismertetjük:

- **Teljes körűen csatlakoztatott** – Ez a szokásos alapértelmezett módban, amely az eszköz működik. A felhő feltöltési és a letöltési adatok engedélyezve ebben a módban van. Az Azure portal vagy a helyi webes felhasználói felületének használatával felügyeli az eszközt.

- **Részlegesen csatlakoztatva** – ebben a módban az eszközt nem tölthet fel és töltse le a minden megosztás azonban adatokat az Azure-portálon keresztül kezelhetők.

    Ebben a módban általában szolgál a hálózati forgalmi díjas műholdas, és a cél, hogy minimalizálja a hálózati sávszélesség-használatot. Minimális hálózatleterheltség figyelési műveletekhez eszköz így is előfordulhatnak.

- **Leválasztott** – ebben a módban az eszköz teljes mértékben megszakad a felhő és mind a felhőbeli feltöltések, letöltések le vannak tiltva. Az eszköz csak a helyi webes felhasználói felületen felügyelhető.

    Ebben a módban általában használnak, az eszköz offline állapotúra szeretné állítani.

Az eszköz üzemmódjának módosítása, kövesse az alábbi lépéseket:

1. A helyi webes felhasználói felületen, az eszköz, lépjen a **Configuration > Felhőbeállítások**.
2. A legördülő listából válassza ki a mód, amely az eszköz az működtetni kívánt. Közül választhat **teljes csatlakoztatott**, **részben csatlakoztatva**, és **teljes leválasztott**. Az eszköz részlegesen leválasztott módban fut, engedélyezze az **az Azure portal felügyeleti**.

    ![Csatlakozási mód](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Energiagazdálkodás

Állítsa le, vagy indítsa újra a fizikai eszköz helyi webes felhasználói felület használatával. Azt javasoljuk, hogy az újraindítás előtt igénybe vehet a megosztások kapcsolat nélküli módban az kiszolgáló és az eszköz. Ez a művelet minimálisra csökkenti a adatsérülés lehetőségét.

1. A helyi webes felhasználói felületen váltson **karbantartási > energiaellátási beállítások**.
2. Válassza ki **leállítási** vagy **indítsa újra a** függően, hogy mit kíván tenni.

    ![Energiagazdálkodási beállítások](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Amikor a rendszer megerősítést kér, válassza ki a **Igen** a folytatáshoz.

> [!NOTE]
> Ha leállítja a fizikai eszközt, szüksége lesz a leküldéses főkapcsoló kapcsolja be az eszközön.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [megosztások kezelése](data-box-edge-manage-shares.md).