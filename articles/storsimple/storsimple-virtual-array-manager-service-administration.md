---
title: A Microsoft Azure StorSimple Manager virtuális tömb felügyeleti |} Microsoft Docs
description: 'Útmutató: a StorSimple a helyszíni virtuális tömb kezelése az Azure-portálon a StorSimple Device Manager szolgáltatás használatával.'
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
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875843"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>A StorSimple Device Manager szolgáltatás segítségével felügyelheti a StorSimple virtuális tömb
![a telepítő folyamatábra](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a StorSimple Device Manager szolgáltatási felületén, beleértve a kapcsolódás, és a különböző lehetőségeket, és hivatkozásokat ad a megadott munkafolyamatok, amelyek a felhasználói felületen keresztül hajtható végre.

A cikk elolvasása után fog tudni hogyan:

* A StorSimple Device Manager szolgáltatásához
* Keresse meg a StorSimple eszköz kezelő felhasználói felület
* A StorSimple virtuális tömb a StorSimple Device Manager szolgáltatásával felügyelete

> [!NOTE]
> A StorSimple 8000 series eszköz felügyeleti lehetőségekről megtekintéséhez keresse fel [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>A StorSimple Device Manager szolgáltatásához
A StorSimple Device Manager szolgáltatás a Microsoft Azure-ban és több StorSimple virtuális tömbök csatlakozik. Ezek az eszközök kezelését a böngészőjében futó központi Microsoft Azure portál használatával. A StorSimple eszköz Manager szolgáltatáshoz csatlakozhat, tegye a következőket.

#### <a name="to-connect-to-the-service"></a>Sikerült csatlakozni a szolgáltatáshoz
1. Ugrás a [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. A Microsoft-fiók hitelesítő adatait használja, jelentkezzen be a Microsoft Azure-portálra (a jobb felső részén a panelen található).
3. Keresse meg tallózással keresse meg a StorSimple eszköz kezelők az eszköz vezetők megadott előfizetés megtekintéséhez a "Szűrése"-->.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>A StorSimple Device Manager szolgáltatás használata a kezelési feladatok
A következő táblázat az általános kezelési feladatok és a bonyolult munkafolyamatok a StorSimple Device Manager szolgáltatás összefoglaló panel belül végrehajtható összegzését jeleníti meg. Ezeket a feladatokat a panelt, amelyen kezdeményezett alapján vannak rendezve.

Minden munkafolyamat kapcsolatos további információkért kattintson a táblázat megfelelő eljárását.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager munkafolyamatok
| Ha azt szeretné, hogy ehhez... | Ezzel az eljárással |
| --- | --- | --- |
| Szolgáltatás létrehozása</br>A szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs lekérése</br>A szolgáltatás regisztrációs kulcsának újragenerálása |[A StorSimple Device Manager szolgáltatás telepítése](storsimple-virtual-array-manage-service.md) |
| A tevékenység naplók megtekintése |[Használja a StorSimple szolgáltatás összegzése](storsimple-virtual-array-service-summary.md) |
| Egy virtuális tömb inaktiválása</br>Egy virtuális tömb törlése |[Inaktiválja vagy törölje a virtuális tömb](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Vész-helyreállítási és eszköz feladatátvevő</br>Feladatátvételi Előfeltételek</br>Üzleti folytonosság vészhelyreállítási (BCDR)</br>A vészhelyreállítás során hibák |[A StorSimple virtuális tömb vész helyreállítási és eszköz feladatátvétele](storsimple-virtual-array-failover-dr.md) |
| Megosztások és a kötetek biztonsági mentése</br>Manuális biztonsági mentés készítése</br>A biztonsági mentési ütemezés módosítása</br>Meglévő biztonsági másolatok megtekintése |[Készítsen biztonsági másolatot a StorSimple virtuális tömb](storsimple-virtual-array-backup.md) |
| Klónozás megosztások biztonságimásolat-készletből</br>A biztonságimásolat-készletből kötetek klónozása</br>Elemszintű helyreállítás (csak a fájlkiszolgáló) |[A StorSimple virtuális tömb biztonsági másolatából klónozása](storsimple-virtual-array-clone.md) |
| Tárolási fiókokról</br>Tárfiók hozzáadása</br>A storage-fiók szerkesztése</br>Tárfiók törlése |[A StorSimple virtuális tömb storage-fiókok kezelése](storsimple-virtual-array-manage-storage-accounts.md) |
| Hozzáférés-vezérlési rekordokat kapcsolatos</br>Hozzáadása vagy módosítása egy hozzáférés-vezérlési rekordot </br>Egy hozzáférés-vezérlési rekordot törlése |[A StorSimple virtuális tömb a hozzáférés-vezérlési rekordokat kezelése](storsimple-virtual-array-manage-acrs.md) |
| Feladatok részleteinek megjelenítése |[A StorSimple virtuális tömb feladatok kezelése](storsimple-virtual-array-manage-jobs.md) |
| A riasztási beállítások konfigurálása</br>Riasztási értesítések fogadása</br>Riasztások kezelése</br>Riasztások áttekintése |[Riasztások megtekintése és kezelése a StorSimple virtuális tömb](storsimple-virtual-array-manage-alerts.md) |
| Az eszköz rendszergazdai jelszavának módosítása |[Módosítsa a StorSimple virtuális tömb eszköz rendszergazdai jelszava](storsimple-virtual-array-change-device-admin-password.md) |
| Telepítse a szoftverfrissítéseket |[A virtuális tömb frissítése](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Kell használnia a [helyi webes felhasználói felület](storsimple-ova-web-ui-admin.md) a következő feladatokhoz:
> 
> * [A szolgáltatásadat-titkosítási kulcs beolvasása](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Hozzon létre egy támogatási csomag](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Állítsa le és indítsa újra a virtuális tömb](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Következő lépések
További információ a webes felhasználói felület, és hogy miképpen lehet vele, [a StorSimple webes felhasználói felület segítségével felügyelheti a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

