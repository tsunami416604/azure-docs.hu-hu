---
title: A Microsoft Azure Data Box Gateway eszközhöz való hozzáféréshez, a teljesítmény és a csatlakozási mód |} A Microsoft Docs
description: Ismerteti, hogyan kezelheti a hozzáférést, a teljesítmény és a kapcsolódási módja az, hogy a segítségével adatátvitel Azure-bA az Azure Data Box Gateway eszköz
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/09/2018
ms.author: alkohli
ms.openlocfilehash: 8f9172418f15b129a71242038efd4cdb7683bbf7
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516226"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway-preview"></a>Kezelheti a hozzáférést, a teljesítmény és a csatlakozási mód az Azure Data Box Gateway (előzetes verzió)

Ez a cikk ismerteti a hozzáférést, a teljesítmény és a csatlakozási mód kezelése az Azure Data Box Gateway számára. Ezeket a műveleteket a helyi webes felhasználói felületen vagy az Azure Portalon keresztül történik.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszköz-hozzáférés kezelése
> * Csatlakozási mód kezelése
> * Energiagazdálkodás

> [!IMPORTANT]
> A Data Box Gateway előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="manage-device-access"></a>Eszköz-hozzáférés kezelése

A Data Box átjáróeszköz eléréséhez a rendszergazdai jelszó használatát vezérlik majd. Módosíthatja a rendszergazdai jelszót a helyi webes felhasználói felületen. Az eszköz rendszergazdai jelszava az Azure Portalon is alaphelyzetbe állíthatja.

### <a name="change-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása

Ha elfelejtette a jelszavát, majd módosíthatja a jelszavát. Kövesse az alábbi lépéseket a helyi felhasználói felületen, az eszköz rendszergazdai jelszavának módosítása.

1. A helyi webes felhasználói felületen váltson **karbantartási > jelszó módosítása**.
2. Adja meg a jelenlegi jelszavát, majd az új jelszót. A megadott jelszónak 8 – 16 karakter hosszúságúnak kell lennie. A következő karakterek közül 3 a jelszóban: nagybetű, kisbetű, számjegyeket és speciális karaktereket. Az új jelszót.

    ![Jelszó megváltoztatása](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kattintson a **jelszó módosítása**.
 
### <a name="reset-device-administrator-password"></a>Eszköz rendszergazdai jelszavának alaphelyzetbe állítása

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

    ![Energiaellátási beállítások](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Amikor a rendszer megerősítést kér, kattintson a **Igen** a folytatáshoz.

> [!NOTE]
> Ha a virtuális eszköz leállítása, szüksége lesz a indítsa el az eszközt a hipervizor-management szolgáltatáson keresztül.