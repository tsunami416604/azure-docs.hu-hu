---
title: A Microsoft Azure StorSimple Manager Virtual Array felügyeleti |} A Microsoft Docs
description: Ismerje meg, hogy a StorSimple Virtual Array helyszíni kezelése a StorSimple-Eszközkezelő szolgáltatással az Azure Portalon.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091868"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>A StorSimple Virtual Array felügyelete a StorSimple-Eszközkezelő szolgáltatás használatával
![a telepítő folyamat](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a StorSimple-Eszközkezelő szolgáltatás felületén, beleértve a hogyan csatlakozhat, és a különböző lehetőségekről, valamint hivatkozásokat az adott munkafolyamatok, amelyek a felhasználói felületen keresztül is elvégezhető.

Ez a cikk elolvasása után, tudni fogja, hogyan lehet:

* Csatlakozás a StorSimple-Eszközkezelő szolgáltatás
* Keresse meg a StorSimple Device Manager felhasználói felületén
* A StorSimple Virtual Array keresztül a StorSimple-Eszközkezelő szolgáltatás felügyeletéhez

> [!NOTE]
> A StorSimple 8000 sorozatú eszköz esetében elérhető felügyeleti beállítások megtekintéséhez lépjen a [a StorSimple Manager szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Csatlakozás a StorSimple-Eszközkezelő szolgáltatás
A StorSimple-Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple Virtual Arrayt csatlakozik. Ezek az eszközök kezeléséhez használhatja a böngészőben futó központi Microsoft Azure-portálon. Szeretne csatlakozni a StorSimple-Eszközkezelő szolgáltatás, tegye a következőket.

#### <a name="to-connect-to-the-service"></a>A szolgáltatáshoz való csatlakozáshoz
1. Nyissa meg a következőt: [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. A Microsoft-fiók hitelesítő adatait használja, jelentkezzen be a Microsoft Azure Portalon (a jobb felső sarkában a panelen található).
3. Keresse meg tallózással keresse meg "Szűrheti"--> a StorSimple-Eszközkezelők a eszközkezelők megtekintéséhez egy adott előfizetésben.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>A StorSimple-Eszközkezelő szolgáltatás segítségével a felügyeleti feladatok végrehajtása
Az alábbi táblázat a gyakori felügyeleti feladatok és a StorSimple-Eszközkezelő szolgáltatás összefoglalás panelén belül végrehajtható komplex munkafolyamatok összegzését jeleníti meg. Ezeket a feladatokat a paneleket, amelyen kezdeményezett alapján vannak rendezve.

Minden egyes munkafolyamat kapcsolatos további információkért kattintson a megfelelő eljárás a táblában.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple-Eszközkezelő munkafolyamatok
| Ha azt szeretné, ehhez... | Ezzel az eljárással |
| --- | --- |
| Szolgáltatás létrehozása</br>A szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs lekérése</br>A szolgáltatás regisztrációs kulcsának újragenerálása |[A StorSimple-Eszközkezelő szolgáltatás üzembe helyezése](storsimple-virtual-array-manage-service.md) |
| A Tevékenységnaplók megtekintése |[Használja a StorSimple szolgáltatás összegzése](storsimple-virtual-array-service-summary.md) |
| A virtuális tömb inaktiválása</br>Törölje a virtuális tömb |[Inaktiválja vagy törölje a virtuális tömb](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Katasztrófa utáni helyreállítás és az eszköz feladatátvételi</br>Feladatátvételi Előfeltételek</br>Üzleti folytonosság – vészhelyreállítás (BCDR)</br>A vészhelyreállítás során hibák |[A StorSimple Virtual Array vész helyreállítási és az eszköz feladatátvétele](storsimple-virtual-array-failover-dr.md) |
| Megosztások és kötetek biztonsági mentése</br>Manuális biztonsági mentés készítése</br>A biztonsági mentési ütemezés módosítása</br>Meglévő biztonsági másolatok megtekintéséhez |[Készítsen biztonsági másolatot a StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Klónozott megosztás biztonságimásolat-készlet</br>Klónozott kötet a biztonságimásolat-készlet</br>Elemszintű helyreállítás (csak a fájlkiszolgáló) |[Klónozza a StorSimple virtuális tömb egy biztonsági másolatból](storsimple-virtual-array-clone.md) |
| Tudnivalók a storage-fiókok</br>Tárfiók hozzáadása</br>Storage-fiók szerkesztése</br>Tárfiók törlése |[Storage-fiókok kezelése a StorSimple Virtual Array](storsimple-virtual-array-manage-storage-accounts.md) |
| Tudnivalók a hozzáférés-vezérlési rekordok</br>Hozzáadása vagy módosítása egy hozzáférés-vezérlési rekord </br>Egy hozzáférés-vezérlési rekord törlése |[Hozzáférés-vezérlési rekordok kezelése a StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Feladatok részleteinek megjelenítése |[A StorSimple Virtual Array feladatok kezelése](storsimple-virtual-array-manage-jobs.md) |
| A riasztási beállítások konfigurálása</br>Riasztási értesítések fogadása</br>Riasztások kezelése</br>Riasztások áttekintése |[Riasztások megtekintése és kezelése a StorSimple virtuális tömb](storsimple-virtual-array-manage-alerts.md) |
| Az eszköz rendszergazdai jelszavának módosítása |[A StorSimple Virtual Array eszköz rendszergazdai jelszavának módosítása](storsimple-virtual-array-change-device-admin-password.md) |
| Szoftverfrissítések telepítése |[Frissítse a virtuális tömb](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Kell használnia a [helyi webes felületén](storsimple-ova-web-ui-admin.md) a következő feladatokhoz:
> 
> * [A szolgáltatásadat-titkosítási kulcs lekérése](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Hozzon létre egy támogatási csomagot](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Állítsa le és indítsa újra a virtuális tömb](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>További lépések
További információ a webes felhasználói felületen, és hogyan kell használni, [a StorSimple webes felhasználói felület segítségével felügyelheti a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

