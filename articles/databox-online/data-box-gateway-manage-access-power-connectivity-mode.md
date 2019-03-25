---
title: A Microsoft Azure Data Box Gateway eszközhöz való hozzáféréshez, a teljesítmény és a csatlakozási mód |} A Microsoft Docs
description: Ismerteti, hogyan kezelheti a hozzáférést, a teljesítmény és a kapcsolódási módja az, hogy a segítségével adatátvitel Azure-bA az Azure Data Box Gateway eszköz
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d46d74544181a6b9cbfd049b2f5461b20b928483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401432"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Az Azure Data Box Gateway kezelheti a hozzáférést, a teljesítmény és a csatlakozási mód

Ez a cikk ismerteti a hozzáférést, a teljesítmény és a csatlakozási mód kezelése az Azure Data Box Gateway számára. Ezeket a műveleteket a helyi webes felhasználói felületen vagy az Azure Portalon keresztül történik.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszköz-hozzáférés kezelése
> * Csatlakozási mód kezelése
> * Energiagazdálkodás

## <a name="manage-device-access"></a>Eszköz-hozzáférés kezelése

A Data Box átjáróeszköz eléréséhez a jelszó használatát vezérlik majd. Módosíthatja a jelszót a helyi webes felhasználói felületen. Az eszköz jelszavát az Azure Portalon is alaphelyzetbe állíthatja.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Kövesse az alábbi lépéseket a helyi felhasználói felületen, az eszköz jelszavának módosítása.

1. A helyi webes felhasználói felületen váltson **karbantartási > jelszó módosítása**.
2. Adja meg a jelenlegi jelszavát, majd az új jelszót. A megadott jelszónak 8 – 16 karakter hosszúságúnak kell lennie. A következő karakterek közül 3 a jelszóban: nagybetű, kisbetű, számjegyeket és speciális karaktereket. Az új jelszót.

    ![Jelszó megváltoztatása](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kattintson a **jelszó módosítása**.
 
### <a name="reset-device-password"></a>Eszköz jelszavának alaphelyzetbe állítása

A visszaállítási munkafolyamat nem szükséges majd előkeresnie a régi jelszót, és akkor hasznos, ha a jelszó elvész. Ez a munkafolyamat az Azure Portalon történik.

1. Az Azure Portalon lépjen a **áttekintés > rendszergazdai jelszó alaphelyzetbe állítása**.

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Adja meg az új jelszót, és erősítse azt. A megadott jelszónak 8 – 16 karakter hosszúságúnak kell lennie. A következő karakterek közül 3 a jelszóban: nagybetű, kisbetű, számjegyeket és speciális karaktereket. Kattintson a **alaphelyzetbe**.

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Csatlakozási mód kezelése

Az alapértelmezett Normál mód szereplőkkel eszközét is futtathatja részlegesen leválasztott vagy kapcsolat nélküli üzemmódban. Az egyes módokban, az alábbiakban ismertetjük:

- **Részlegesen csatlakoztatva** – ebben a módban az eszközt nem tölthetők fel bármely azonban az adatokat a megosztásokat az Azure-portálon keresztül kezelhetők.

    Ebben a módban általában szolgál a hálózati forgalmi díjas műholdas, és a cél, hogy minimalizálja a hálózati sávszélesség-használatot. Minimális hálózatleterheltség figyelési műveletekhez eszköz így is előfordulhatnak.

- **Leválasztott** – ebben a módban az eszköz teljes mértékben megszakad a felhő és mind a felhőbeli feltöltések, letöltések le vannak tiltva. Az eszköz csak a helyi webes felhasználói felületen felügyelhető.

    Ebben a módban általában használnak, az eszköz offline állapotúra szeretné állítani.

Az eszköz üzemmódjának módosítása, kövesse az alábbi lépéseket:

1. A helyi webes felhasználói felületen, az eszköz, lépjen a **Configuration > Felhőbeállítások**.
2. Tiltsa le a **felhőalapú feltöltési és letöltési**.
3. Az eszköz részlegesen leválasztott módban fut, engedélyezze az **az Azure portal felügyeleti**.

    ![Csatlakozási mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Az eszköz leválasztott módban fusson, tiltsa le a **az Azure portal felügyeleti**. Most már az eszköz csak kezelhetők a helyi webes felhasználói felületen.

    ![Csatlakozási mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Energiagazdálkodás

Állítsa le, vagy indítsa újra a fizikai és virtuális eszköz helyi webes felhasználói felület használatával. Az újraindítás előtt ajánlott offline állapotba állítani a gazdagépen található megosztásokat, majd magát az eszközt is. Ez a művelet minimálisra csökkenti a adatsérülés lehetőségét.

1. A helyi webes felhasználói felületen váltson **karbantartási > energiaellátási beállítások**.
2. Kattintson a **leállítási** vagy **indítsa újra a** függően, hogy mit kíván tenni.

    ![Energiagazdálkodási beállítások](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Amikor a rendszer megerősítést kér, kattintson a **Igen** a folytatáshoz.

> [!NOTE]
> Ha a virtuális eszköz leállítása, szüksége lesz a indítsa el az eszközt a hipervizor-management szolgáltatáson keresztül.
