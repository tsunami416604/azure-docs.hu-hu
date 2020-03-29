---
title: StorSimple Eszközkezelő szolgáltatás felügyelete | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti a StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatás használatával az Azure Portalon.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723306"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple Eszközkezelő szolgáltatás felületét, beleértve a csatlakozás módját, a rendelkezésre álló különböző lehetőségeket, és az adott, ezen a felhasználói felületen keresztül elvégezhető munkafolyamatokra mutató hivatkozásokat. Ez az iránymutatás mindkettőre vonatkozik; a StorSimple fizikai eszköz és a felhőberendezés.

Elolvasása után ezt a cikket, akkor megtanulják, hogy:

* Csatlakozás a StorSimple Eszközkezelő szolgáltatáshoz
* A StorSimple eszköz felügyelete a StorSimple Eszközkezelő szolgáltatáson keresztül

## <a name="connect-to-storsimple-device-manager-service"></a>Csatlakozás a StorSimple Eszközkezelő szolgáltatáshoz

A StorSimple Eszközkezelő szolgáltatás fut a Microsoft Azure-ban, és több StorSimple-eszközhöz csatlakozik. Az eszközök kezeléséhez egy böngészőben futó központi Microsoft Azure-portált használ. A StorSimple Eszközkezelő szolgáltatáshoz való csatlakozáshoz tegye a következőket.

#### <a name="to-connect-to-the-service"></a>Csatlakozás a szolgáltatáshoz
1. Keresse [https://portal.azure.com/](https://portal.azure.com/)meg a it.
2. Microsoft-fiók hitelesítő adatainak használatával jelentkezzen be a Microsoft Azure portálra (amely az ablaktábla jobb felső részén található).
3. Görgessen le a bal oldali navigációs ablaktáblán a StorSimple Eszközkezelő szolgáltatás eléréséhez.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple-eszköz felügyelete a StorSimple Eszközkezelő szolgáltatás használatával

Az alábbi táblázat a StorSimple Eszközkezelő szolgáltatás felhasználói felületén elvégezhető összes gyakori felügyeleti feladat és összetett munkafolyamat összegzését mutatja be. Ezek a feladatok a felhasználói felület paneljei alapján vannak rendszerezve, amelyeken elindulnak.

Az egyes munkafolyamatokkal kapcsolatos további információkért kattintson a táblázatban a megfelelő eljárásra.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Eszközkezelő munkafolyamatai

| Ha ezt akarod csinálni... | Használja ezt az eljárást. |
| --- | --- |
| Szolgáltatás létrehozása</br>Szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs beszerezése</br>Szolgáltatásregisztrációs kulcs újragenerálása |[StorSimple Eszközkezelő szolgáltatás telepítése](storsimple-8000-manage-service.md) |
| A tevékenységnaplók megtekintése |[A StorSimple Eszközkezelő szolgáltatás összegzésének használata](storsimple-8000-service-dashboard.md) |
| A szolgáltatásadat-titkosítási kulcs módosítása</br>A műveleti naplók megtekintése |[A StorSimple Eszközkezelő szolgáltatás irányítópultjának használata](storsimple-8000-service-dashboard.md) |
| Eszköz inaktiválása</br>Eszköz törlése |[Eszköz inaktiválása vagy törlése](storsimple-8000-deactivate-and-delete-device.md) |
| Tudnivalók a vészhelyreállításról és az eszközfeladat-átvételről</br>Feladatátvétel fizikai eszközre</br>Feladatátvétel virtuális eszközre</br>Üzletmenet-folytonossági vészhelyreállítás (BCDR) |[Feladatátvétel és vészhelyreállítás a StorSimple-eszközhöz](storsimple-8000-device-failover-disaster-recovery.md) |
| Kötet biztonsági másolatainak listázása</br>Biztonságimásolat-készlet kiválasztása</br>Biztonságimásolat-készlet törlése |[Biztonsági másolatok kezelése](storsimple-8000-manage-backup-catalog.md) |
| Kötet klónozása |[Kötet klónozása](storsimple-8000-clone-volume-u2.md) |
| Biztonságimásolat-készlet visszaállítása |[Biztonságimásolat-készlet visszaállítása](storsimple-8000-restore-from-backup-set-u2.md) |
| A tárfiókok –</br>Tárfiók hozzáadása</br>Tárfiók szerkesztése</br>Tárfiók törlése</br>A tárfiókok kulcsos elforgatása |[Tárfiókok kezelése](storsimple-8000-manage-storage-accounts.md) |
| Sávszélesség-sablonok –</br>Sávszélesség-sablon hozzáadása</br>Sávszélesség-sablon szerkesztése</br>Sávszélesség-sablon törlése</br>Alapértelmezett sávszélesség-sablon használata</br>Egész napos sávszélességsablon létrehozása, amely egy megadott időpontban kezdődik |[Sávszélességsablonok kezelése](storsimple-8000-manage-bandwidth-templates.md) |
| Hozzáférés-vezérlési rekordok – kapcsolat</br>Hozzáférés-vezérlési rekord létrehozása</br>Hozzáférés-vezérlőrekord szerkesztése</br>Hozzáférés-vezérlési rekord törlése |[Hozzáférés-vezérlési rekordok kezelése](storsimple-8000-manage-acrs.md) |
| Feladatok részleteinek megjelenítése</br>Feladat visszavonása |[Feladatok kezelése](storsimple-8000-manage-jobs-u2.md) |
| Riasztási értesítések fogadása</br>Riasztások kezelése</br>Figyelmeztetések áttekintése |[StorSimple riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| Figyelési diagramok létrehozása |[A StorSimple eszköz figyelése](storsimple-monitor-device.md) |
| Kötettároló hozzáadása</br>Kötettároló módosítása</br>Kötettároló törlése |[Kötettárolók kezelése](storsimple-8000-manage-volume-containers.md) |
| Kötet hozzáadása</br>Kötet módosítása</br>Kötet kapcsolat nélküli üzemmódba váltása</br>Kötet törlése</br>Kötet figyelése |[Kötetek kezelése](storsimple-8000-manage-volumes-u2.md) |
| Eszközbeállítások módosítása</br>Időbeállítások módosítása</br>DNS.md beállításainak módosítása</br>Hálózati összeköttetések konfigurálása |[A StorSimple eszköz eszközkonfigurációjának módosítása](storsimple-8000-modify-device-config.md) |
| Webproxy-beállítások megtekintése |[Webproxy konfigurálása az eszközhöz](storsimple-8000-configure-web-proxy.md) |
| Eszközrendszergazdai jelszó módosítása</br>StorSimple Snapshot Manager jelszó módosítása |[StorSimple jelszavak módosítása](storsimple-8000-change-passwords.md) |
| Távfelügyelet konfigurálása |[Csatlakozás távolról a StorSimple eszközhöz](storsimple-8000-remote-connect.md) |
| Riasztási beállítások konfigurálása |[StorSimple riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| Chap konfigurálása a StorSimple eszközhöz |[Chap konfigurálása a StorSimple eszközhöz](storsimple-configure-chap.md) |
| Biztonsági mentési szabályzat hozzáadása</br>Ütemezés hozzáadása vagy módosítása</br>Biztonságimentési házirend törlése</br>Kézi biztonsági mentés készítése</br>Egyéni biztonsági mentési házirend létrehozása több kötettel és ütemezéssel |[Biztonsági mentési szabályzatok kezelése](storsimple-8000-manage-backup-policies-u2.md) |
| Eszközvezérlők leállítása</br>Eszközvezérlők újraindítása</br>Eszközvezérlők leállítása</br>A készülék visszaállítása gyári alapértékekre</br>(Fent csak helyszíni eszközökre van kivetve) |[StorSimple eszközvezérlő kezelése](storsimple-8000-manage-device-controller.md) |
| További információ a StorSimple hardverösszetevőkről</br>Hardverállapotának figyelése</br>(Fent csak helyszíni eszközökre van kivetve) |[Hardverösszetevők figyelése](storsimple-8000-monitor-hardware-status.md) |
| Támogatási csomag létrehozása |[Támogatási csomag létrehozása és kezelése](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Szoftverfrissítések telepítése |[Az eszköz frissítése](storsimple-update-device.md) |

## <a name="next-steps"></a>További lépések

Ha bármilyen problémát tapasztal a StorSimple eszköz napi működésével vagy annak bármely hardverösszetevőjével kapcsolatban, olvassa el a következőket:

* [Hibaelhárítás a Diagnosztika eszköz használatával](storsimple-8000-diagnostics.md)
* [StorSimple figyelési jelző LED-ek használata](storsimple-monitoring-indicators.md)

Ha nem tudja megoldani a problémákat, és szolgáltatási kérelmet kell létrehoznia, olvassa el a Kapcsolatfelvétel a [Microsoft támogatási szolgálatával.](storsimple-8000-contact-microsoft-support.md)

