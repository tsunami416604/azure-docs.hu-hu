---
title: StorSimple-Eszközkezelő szolgáltatás felügyeleti |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás használatával.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723306"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>A StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple-Eszközkezelő szolgáltatás felületén, beleértve a kapcsolódás, a különböző rendelkezésre álló lehetőségeket, valamint az ezen a felhasználói felületen keresztül is elvégezhető az adott munkafolyamatok mutató hivatkozásokat tartalmaz. Ez az útmutató akkor alkalmazható, mindkét; a StorSimple fizikai eszköz és a felhőalapú berendezés.

Ez a cikk elolvasása után megtanulhatja, hogy:

* Kapcsolódni a StorSimple-Eszközkezelő szolgáltatáshoz
* A StorSimple-Eszközkezelő szolgáltatáson keresztül a StorSimple eszköz felügyelete

## <a name="connect-to-storsimple-device-manager-service"></a>Kapcsolódni a StorSimple-Eszközkezelő szolgáltatáshoz

A StorSimple-Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple-eszközhöz csatlakozik. Ezek az eszközök kezeléséhez használhatja a böngészőben futó központi Microsoft Azure-portálon. Szeretne csatlakozni a StorSimple-Eszközkezelő szolgáltatás, tegye a következőket.

#### <a name="to-connect-to-the-service"></a>A szolgáltatáshoz való csatlakozáshoz
1. Navigáljon a [ https://portal.azure.com/ ](https://portal.azure.com/).
2. A Microsoft-fiók hitelesítő adatait használja, jelentkezzen be a Microsoft Azure Portalon (a jobb felső sarkában a panelen található).
3. Görgessen le a bal oldali navigációs panelen, a StorSimple-Eszközkezelő szolgáltatás eléréséhez.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple-Eszközkezelő szolgáltatás StorSimple-eszközök felügyelete

Az alábbi táblázat a gyakori felügyeleti feladatok és a StorSimple-Eszközkezelő szolgáltatásban felhasználói felület végrehajtható komplex munkafolyamatok összegzését jeleníti meg. Ezeket a feladatokat a felhasználói felület paneleket, amelyen kezdeményezett alapján vannak rendezve.

Minden egyes munkafolyamat kapcsolatos további információkért kattintson a megfelelő eljárás a táblában.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple-Eszközkezelő munkafolyamatok

| Ha azt szeretné, ehhez... | Ez az eljárás használható. |
| --- | --- |
| Szolgáltatás létrehozása</br>A szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs lekérése</br>Szolgáltatás regisztrációs kulcsának újragenerálása |[A StorSimple-Eszközkezelő szolgáltatás üzembe helyezése](storsimple-8000-manage-service.md) |
| A Tevékenységnaplók megtekintése |[Használja a StorSimple-Eszközkezelő szolgáltatás összegzése](storsimple-8000-service-dashboard.md) |
| A szolgáltatásadat-titkosítási kulcs módosítása</br>A műveletnaplók megtekintése |[A StorSimple-Eszközkezelő szolgáltatás irányítópultjának használata](storsimple-8000-service-dashboard.md) |
| Eszköz inaktiválása</br>Eszköz törlése |[Inaktiválja vagy törölje az eszköz](storsimple-8000-deactivate-and-delete-device.md) |
| További információ a katasztrófa utáni helyreállítás és az eszköz feladatátvételi</br>Feladatátvétel fizikai eszközre</br>A feladatátvevő virtuális eszközre</br>Üzleti folytonosság – vészhelyreállítás (BCDR) |[Feladatátvétel és vészhelyreállítás a StorSimple-eszköz helyreállítása](storsimple-8000-device-failover-disaster-recovery.md) |
| Egy kötet biztonsági mentését listája</br>Válassza ki a biztonságimásolat-készletből</br>A biztonságimásolat-készlet törlése |[Biztonsági másolatok kezelése](storsimple-8000-manage-backup-catalog.md) |
| Kötet klónozása |[Kötet klónozása](storsimple-8000-clone-volume-u2.md) |
| Állítsa vissza a biztonságimásolat-készletből |[Állítsa vissza a biztonságimásolat-készletből](storsimple-8000-restore-from-backup-set-u2.md) |
| Tudnivalók a storage-fiókok</br>Tárfiók hozzáadása</br>Storage-fiók szerkesztése</br>Tárfiók törlése</br>A tárfiókok kulcsrotálás |[Tárfiókok kezelése](storsimple-8000-manage-storage-accounts.md) |
| A sávszélesség-sablonok</br>Sávszélességsablon hozzáadása</br>Sávszélességsablon szerkesztése</br>Sávszélességsablon törlése</br>A sávszélesség alapértelmezett sablon használata</br>Hozzon létre egy egész napos sávszélességsablont, amely egy megadott időpontban kezdődik |[Sávszélességsablonok kezelése](storsimple-8000-manage-bandwidth-templates.md) |
| Tudnivalók a hozzáférés-vezérlési rekordok</br>Egy hozzáférés-vezérlési rekord létrehozása</br>Egy hozzáférés-vezérlési rekord szerkesztése</br>Egy hozzáférés-vezérlési rekord törlése |[Hozzáférés-vezérlési rekordok kezelése](storsimple-8000-manage-acrs.md) |
| Feladatok részleteinek megjelenítése</br>Feladatok megszakítása |[Feladatok kezelése](storsimple-8000-manage-jobs-u2.md) |
| Riasztási értesítések fogadása</br>Riasztások kezelése</br>Riasztások áttekintése |[A StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| Figyelési diagramok létrehozása |[A StorSimple-eszköz figyelése](storsimple-monitor-device.md) |
| Kötettároló hozzáadása</br>A kötettároló módosítása</br>A kötettároló törlése |[Kötettárolók kezelése](storsimple-8000-manage-volume-containers.md) |
| Kötet hozzáadása</br>Kötet módosítása</br>A kötet offline állapotba helyezése</br>Kötet törlése</br>Megfigyelési |[Kötetek kezelése](storsimple-8000-manage-volumes-u2.md) |
| Eszköz beállításainak módosítása</br>Idő beállításainak módosítása</br>DNS.md beállításainak módosítása</br>Hálózati adapterek konfigurálása |[A StorSimple eszköz az eszköz konfigurációjának módosítása](storsimple-8000-modify-device-config.md) |
| Nézet webproxy beállításai |[Az eszköz webproxy konfigurálása](storsimple-8000-configure-web-proxy.md) |
| Eszköz rendszergazdai jelszavának módosítása</br>A StorSimple Snapshot Manager jelszavának módosítása |[A StorSimple-jelszavaikat](storsimple-8000-change-passwords.md) |
| Távfelügyelet konfigurálása |[Távoli csatlakozás a StorSimple-eszköz](storsimple-8000-remote-connect.md) |
| A riasztási beállítások konfigurálása |[A StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| A CHAP konfigurálása a StorSimple eszköz |[A CHAP konfigurálása a StorSimple eszköz](storsimple-configure-chap.md) |
| Biztonsági mentési szabályzat hozzáadása</br>Adjon hozzá vagy ütemezésének módosítása</br>A biztonsági mentési szabályzat törlése</br>Manuális biztonsági mentés készítése</br>Hozzon létre egy egyéni biztonsági mentési házirendet több kötetek és ütemezések |[Biztonsági mentési szabályzatok kezelése](storsimple-8000-manage-backup-policies-u2.md) |
| Eszközvezérlők leállítása</br>Indítsa újra a eszközvezérlők</br>Állítsa le a eszközvezérlők</br>Alaphelyzetbe állíthatja az eszközt a gyári beállításokat</br>(Csak a helyi eszközön újabb rendszer) |[A StorSimple az eszközvezérlő kezelése](storsimple-8000-manage-device-controller.md) |
| További információ a StorSimple hardverkonfiguráción összetevők</br>Hardver állapotának figyelése</br>(Csak a helyi eszközön újabb rendszer) |[A figyelő hardverösszetevők](storsimple-8000-monitor-hardware-status.md) |
| Hozzon létre egy támogatási csomagot |[Hozzon létre és kezelheti a támogatási csomagot](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Szoftverfrissítések telepítése |[Az eszköz frissítése](storsimple-update-device.md) |

## <a name="next-steps"></a>További lépések

A StorSimple-eszköz napi működésének vagy azokkal a hardverösszetevők problémákat tapasztal, tekintse meg:

* [Hibaelhárítás a diagnosztikai eszköz használatával](storsimple-8000-diagnostics.md)
* [StorSimple figyelési jelző LED-ek használata](storsimple-monitoring-indicators.md)

Ha mégsem sikerül megoldani a problémákat, és a egy szolgáltatási kérelem létrehozásához szükséges, tekintse meg [forduljon a Microsoft támogatási](storsimple-8000-contact-microsoft-support.md).

