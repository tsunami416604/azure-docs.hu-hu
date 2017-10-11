---
title: "A StorSimple eszköz kezelő szolgáltatás felügyeleti |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a StorSimple eszközt a StorSimple Device Manager szolgáltatással az Azure portálon."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>A StorSimple eszköz felügyelete a StorSimple Device Manager szolgáltatás használata

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple eszköz Manager szolgáltatási felületén, figyeléséről, valamint azt, a különböző lehetőségekről és az adott munkafolyamatok, amelyek a felhasználói felületen keresztül végezhető mutató való kapcsolódáshoz. Ez az útmutató kell alkalmazni. a StorSimple fizikai eszköz és a felhő készülék.

A cikk elolvasása után megtudhatja, hogy:

* Csatlakozzon a StorSimple Device Manager szolgáltatáshoz
* A StorSimple eszközt a StorSimple Device Manager szolgáltatásával felügyelete

## <a name="connect-to-storsimple-device-manager-service"></a>Csatlakozzon a StorSimple Device Manager szolgáltatáshoz

A StorSimple Device Manager szolgáltatás a Microsoft Azure-ban és több StorSimple eszközt csatlakozik. Ezek az eszközök kezelését a böngészőjében futó központi Microsoft Azure portál használatával. A StorSimple eszköz Manager szolgáltatáshoz csatlakozhat, tegye a következőket.

#### <a name="to-connect-to-the-service"></a>Sikerült csatlakozni a szolgáltatáshoz
1. Navigáljon a [https://portal.azure.com/](https://portal.azure.com/).
2. A Microsoft-fiók hitelesítő adatait használja, jelentkezzen be a Microsoft Azure-portálra (a jobb felső részén a panelen található).
3. Görgessen le a bal oldali navigációs panelen, a StorSimple Device Manager szolgáltatás eléréséhez.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple Device Manager szolgáltatással a StorSimple eszköz felügyelete

A következő táblázat az általános kezelési feladatok és a bonyolult munkafolyamatok a StorSimple Device Manager szolgáltatás felhasználói felületi belül végrehajtható összegzését jeleníti meg. Ezeket a feladatokat a felhasználói felület panelt, amelyen kezdeményezett alapján vannak rendezve.

Minden munkafolyamat kapcsolatos további információkért kattintson a táblázat megfelelő eljárását.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager munkafolyamatok

| Ha azt szeretné, hogy ehhez... | Ez az eljárás használható. |
| --- | --- |
| Szolgáltatás létrehozása</br>A szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs lekérése</br>Szolgáltatásregisztrációs kulcs újragenerálása |[A StorSimple Device Manager szolgáltatás telepítése](storsimple-8000-manage-service.md) |
| A tevékenység naplók megtekintése |[Használja a StorSimple Device Manager szolgáltatás összegzése](storsimple-8000-service-dashboard.md) |
| A szolgáltatásadat-titkosítási kulcs módosítása</br>A műveletnaplók megtekintése |[A StorSimple Device Manager szolgáltatás irányítópult](storsimple-8000-service-dashboard.md) |
| Eszköz inaktiválása</br>Eszköz törlése |[Inaktiválja vagy törölje az eszköz](storsimple-8000-deactivate-and-delete-device.md) |
| Vész-helyreállítási és eszköz-feladatátvétellel kapcsolatos további tudnivalók</br>Feladatátvétel a fizikai eszköz</br>A virtuális eszköz feladatátvétel</br>Üzleti folytonosság vészhelyreállítási (BCDR) |[A StorSimple eszköz feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-8000-device-failover-disaster-recovery.md) |
| A kötet biztonsági mentéseit lista</br>Válassza ki a biztonságimásolat-készletből</br>A biztonságimásolat-készlet törlése |[Biztonsági másolatok kezelése](storsimple-8000-manage-backup-catalog.md) |
| Kötet klónozása |[Kötet klónozása](storsimple-8000-clone-volume-u2.md) |
| Állítsa vissza a biztonságimásolat-készletből |[Állítsa vissza a biztonságimásolat-készletből](storsimple-8000-restore-from-backup-set-u2.md) |
| Tárolási fiókokról</br>Tárfiók hozzáadása</br>A storage-fiók szerkesztése</br>Tárfiók törlése</br>Kulcs elforgatási szögét storage-fiókok |[Tárfiókok kezelése](storsimple-8000-manage-storage-accounts.md) |
| Sávszélesség-sablonok</br>Sávszélességsablon hozzáadása</br>A sávszélesség-sablon szerkesztése</br>Sávszélesség sablon törlése</br>Sávszélesség alapértelmezett sablon használata</br>Egy megadott időpontban elinduló napos sávszélesség sablon létrehozása |[Sávszélességsablonok kezelése](storsimple-8000-manage-bandwidth-templates.md) |
| Hozzáférés-vezérlési rekordokat kapcsolatos</br>Hozzon létre egy hozzáférés-vezérlési rekordot</br>Egy hozzáférés-vezérlési rekordot szerkesztése</br>Egy hozzáférés-vezérlési rekordot törlése |[Hozzáférés-vezérlési rekordokat kezelése](storsimple-8000-manage-acrs.md) |
| Feladat részleteinek megtekintése</br>Feladatok megszakítása |[Feladatok kezelése](storsimple-8000-manage-jobs-u2.md) |
| Riasztási értesítések fogadása</br>Riasztások kezelése</br>Riasztások áttekintése |[StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| Figyelési diagramok létrehozása |[A StorSimple eszköz figyelése](storsimple-monitor-device.md) |
| A kötettároló hozzáadása</br>A kötettároló módosítása</br>A kötettároló törlése |[Kötettárolók kezelése](storsimple-8000-manage-volume-containers.md) |
| Kötet hozzáadása</br>A kötet módosítása</br>A kötet offline állapotba helyezése</br>Kötet törlése</br>A kötet figyelése |[Kötetek kezelése](storsimple-8000-manage-volumes-u2.md) |
| Eszköz beállításainak módosítása</br>Idő beállításainak módosítása</br>DNS.md beállításainak módosítása</br>Hálózati adapterek konfigurálása |[A StorSimple eszköz eszköz konfigurációjának módosítása](storsimple-8000-modify-device-config.md) |
| Webalkalmazás-proxy beállítások megjelenítése |[Az eszköz webalkalmazás-proxy konfigurálása](storsimple-8000-configure-web-proxy.md) |
| Eszköz rendszergazdai jelszavának módosítása</br>StorSimple Snapshot Manager jelszavának módosítása |[StorSimple-jelszavak módosítása](storsimple-8000-change-passwords.md) |
| A távfelügyelet konfigurálása |[Távoli csatlakozás a StorSimple eszköz](storsimple-8000-remote-connect.md) |
| A riasztási beállítások konfigurálása |[StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| A CHAP konfigurálása a StorSimple eszköz |[A CHAP konfigurálása a StorSimple eszköz](storsimple-configure-chap.md) |
| Biztonsági mentési szabályzat hozzáadása</br>Adja hozzá vagy ütemezésének módosítása</br>A biztonsági mentési házirend törlése</br>Manuális biztonsági mentés készítése</br>Hozzon létre egy egyéni biztonsági mentési házirend több kötetek és ütemezése |[Biztonsági mentési szabályzatok kezelése](storsimple-8000-manage-backup-policies-u2.md) |
| Állítsa le a eszközvezérlők</br>Indítsa újra a eszközvezérlők</br>Eszközvezérlők leállítása</br>Alaphelyzetbe állíthatja az eszközt a gyári beállítások</br>(Csak a helyi eszköz újabb rendszer) |[A StorSimple eszköz vezérlő kezelése](storsimple-8000-manage-device-controller.md) |
| További tudnivalók a StorSimple hardverösszetevők</br>Figyelje meg a hardver állapotát</br>(Csak a helyi eszköz újabb rendszer) |[A figyelő hardverösszetevők](storsimple-8000-monitor-hardware-status.md) |
| Hozzon létre egy támogatási csomag |[Létrehozását és kezelését egy támogatási csomag](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Telepítse a szoftverfrissítéseket |[Az eszköz frissítése](storsimple-update-device.md) |

## <a name="next-steps"></a>Következő lépések

Ha probléma merül fel a napi szintű üzemeltetése a StorSimple eszköz vagy az összes hardver összetevőit, tekintse meg:

* [A diagnosztikai eszköz használata – hibaelhárítás](storsimple-8000-diagnostics.md)
* [Kijelző LED figyelési StorSimple használata](storsimple-monitoring-indicators.md)

Ha mégsem sikerül megoldani a problémákat, és a szolgáltatási kérelem létrehozásához szükséges, tekintse meg a [forduljon a Microsoft ügyfélszolgálatához](storsimple-8000-contact-microsoft-support.md).

