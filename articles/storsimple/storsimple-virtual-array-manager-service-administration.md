---
title: Microsoft Azure StorSimple Manager virtuális tömb felügyelete | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti a StorSimple helyszíni virtuális tömb segítségével a StorSimple Eszközkezelő szolgáltatás az Azure Portalon.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62123805"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple virtuális tömb felügyeletéhez
![beállítási folyamat](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a StorSimple Device Manager szolgáltatás felületét, beleértve a csatlakozás módját és a rendelkezésre álló különböző lehetőségeket, és hivatkozásokat tartalmaz az adott munkafolyamatokra, amelyek ezen a felhasználói felületen keresztül hajthatók végre.

Elolvasása után ezt a cikket, akkor tudni fogja, hogyan kell:

* Csatlakozás a StorSimple Eszközkezelő szolgáltatáshoz
* Navigálás a StorSimple Eszközkezelő felhasználói felületén
* A StorSimple virtuális tömb felügyelete a StorSimple Eszközkezelő szolgáltatáson keresztül

> [!NOTE]
> A StorSimple 8000 sorozatú eszközhöz elérhető felügyeleti lehetőségek megtekintéséhez válassza [a StorSimple-eszköz felügyelete a StorSimple-kezelő szolgáltatás használata című](storsimple-manager-service-administration.md)lehetőséget.
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Csatlakozás a StorSimple Eszközkezelő szolgáltatáshoz
A StorSimple Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple virtuális tömbhöz csatlakozik. Az eszközök kezeléséhez egy böngészőben futó központi Microsoft Azure-portált használ. A StorSimple Eszközkezelő szolgáltatáshoz való csatlakozáshoz tegye a következőket.

#### <a name="to-connect-to-the-service"></a>Csatlakozás a szolgáltatáshoz
1. Nyissa [https://ms.portal.azure.com](https://ms.portal.azure.com)meg a.
2. Microsoft-fiók hitelesítő adatainak használatával jelentkezzen be a Microsoft Azure portálra (amely az ablaktábla jobb felső részén található).
3. A Tallózás --> "Szűrő" lehetőséget a StorSimple eszközkezelőknél az adott előfizetés összes eszközkezelőjének megtekintéséhez.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Felügyeleti feladatok végrehajtása a StorSimple Eszközkezelő szolgáltatás sal
Az alábbi táblázat a StorSimple Eszközkezelő szolgáltatás összegzése panelen elvégezhető összes gyakori felügyeleti feladat és összetett munkafolyamat összegzését mutatja be. Ezek a feladatok a kések alapján vannak rendszerezve, amelyeken elindulnak.

Az egyes munkafolyamatokkal kapcsolatos további információkért kattintson a táblázatban a megfelelő eljárásra.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Eszközkezelő munkafolyamatai
| Ha ezt akarod csinálni... | Az eljárás alkalmazása |
| --- | --- |
| Szolgáltatás létrehozása</br>Szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs lekérése</br>A szolgáltatás regisztrációs kulcsának újragenerálása |[A StorSimple Eszközkezelő szolgáltatás telepítése](storsimple-virtual-array-manage-service.md) |
| A tevékenységnaplók megtekintése |[A StorSimple szolgáltatás összegzésének használata](storsimple-virtual-array-service-summary.md) |
| Virtuális tömb inaktiválása</br>Virtuális tömb törlése |[Virtuális tömb inaktiválása vagy törlése](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Vészhelyreállítás és eszközfeladat-átvétel</br>Feladatátvételi előfeltételek</br>Üzletmenet-folytonossági vészhelyreállítás (BCDR)</br>Hibák a vészhelyreállítás során |[Vészhelyreállítás és eszközfeladat-átvétel a StorSimple virtuális tömbhöz](storsimple-virtual-array-failover-dr.md) |
| Részvények és mennyiségek biztonsági és biztonsági tartaléka</br>Kézi biztonsági mentés készítése</br>A biztonsági mentés ütemezésének módosítása</br>Meglévő biztonsági mentések megtekintése |[A StorSimple virtuális tömb biztonsági elége](storsimple-virtual-array-backup.md) |
| Megosztások klónozása biztonságimásolat-készletből</br>Kötetek klónozása biztonságimásolat-készletből</br>Elemszintű helyreállítás (csak fájlkiszolgáló) |[Klónozás a StorSimple virtuális tömb biztonsági másolatából](storsimple-virtual-array-clone.md) |
| A tárfiókok –</br>Tárfiók hozzáadása</br>Tárfiók szerkesztése</br>Tárfiók törlése |[A StorSimple virtuális tömb tárfiókjainak kezelése](storsimple-virtual-array-manage-storage-accounts.md) |
| Hozzáférés-vezérlési rekordok – kapcsolat</br>Hozzáférés-vezérlési rekord hozzáadása vagy módosítása </br>Hozzáférés-vezérlési rekord törlése |[Hozzáférés-vezérlési rekordok kezelése a StorSimple virtuális tömbhöz](storsimple-virtual-array-manage-acrs.md) |
| Feladatok részleteinek megjelenítése |[StorSimple virtuális tömbfeladatok kezelése](storsimple-virtual-array-manage-jobs.md) |
| Riasztási beállítások konfigurálása</br>Riasztási értesítések fogadása</br>Riasztások kezelése</br>Figyelmeztetések áttekintése |[A StorSimple virtuális tömb riasztásainak megtekintése és kezelése](storsimple-virtual-array-manage-alerts.md) |
| Az eszközrendszergazda jelszavának módosítása |[A StorSimple Virtual Array eszköz rendszergazdai jelszavának módosítása](storsimple-virtual-array-change-device-admin-password.md) |
| Szoftverfrissítések telepítése |[A virtuális tömb frissítése](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> A helyi [webes felhasználói felületet](storsimple-ova-web-ui-admin.md) a következő feladatokhoz kell használnia:
> 
> * [A szolgáltatásadat-titkosítási kulcs beolvasása](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Támogatási csomag létrehozása](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Virtuális tömb leállítása és újraindítása](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>További lépések
A webes felhasználói felületről és használatáról a [StorSimple webes felhasználói felület használata a StorSimple virtuális tömb felügyeletéhez](storsimple-ova-web-ui-admin.md)című lapban talál további információt.

