---
title: "A StorSimple Manager szolgáltatás felügyeleti |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a StorSimple eszközt a StorSimple Manager szolgáltatás a klasszikus Azure portál használatával."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2586582e-d85c-42e1-afb3-be734c1c0461
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: dcca578b2993c025e62f1eca7ecec0e62a092479
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>A StorSimple eszköz felügyelete a StorSimple Manager szolgáltatás segítségével
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-8000-manager-service-administration.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a StorSimple Manager szolgáltatási felületén, beleértve azt, a különböző lehetőségekről és az adott munkafolyamatok, amelyek a felhasználói felületen keresztül végezhető mutató csatlakoztatása. Ez az útmutató kell alkalmazni. a StorSimple fizikai és a virtuális eszköz.

A cikk elolvasása után megtudhatja, hogy:

* Csatlakozás a StorSimple Manager szolgáltatáshoz
* Keresse meg a StorSimple Manager felhasználói felületén
* A StorSimple Manager szolgáltatással a StorSimple eszköz felügyelete

## <a name="connect-to-storsimple-manager-service"></a>Csatlakozás a StorSimple Manager szolgáltatáshoz
A StorSimple Manager szolgáltatás fut a Microsoft Azure-ban, és több StorSimple eszközt csatlakozik. Ezek az eszközök kezelését a böngészőjében futó központi Microsoft Azure klasszikus portál használatával. A StorSimple Manager szolgáltatáshoz csatlakozhat, tegye a következőket.

#### <a name="to-connect-to-the-service"></a>Sikerült csatlakozni a szolgáltatáshoz
1. Navigáljon a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. A Microsoft-fiók hitelesítő adatait használja, jelentkezzen be a Microsoft Azure klasszikus portálra (a jobb felső részén a panelen található).
3. Görgessen le a bal oldali navigációs panelen, a StorSimple Manager szolgáltatás eléréséhez.

## <a name="navigate-storsimple-manager-service-ui"></a>Keresse meg a StorSimple Manager szolgáltatás felhasználói felülete
A navigációs hierarchia a StorSimple Manager szolgáltatás felhasználói felülete az alábbi táblázatban látható.

* **A StorSimple Manager** kezdőlapja megnyitná a felhasználói felület szolgáltatásiszint-lapokat a szolgáltatásokon belüli összes eszközökre vonatkozik.
* **Eszközök** lap tart az eszköz – szint felhasználói felület oldalak alkalmazható egy adott eszközhöz.
* **Kötettárolók** lap megnyitná a kötet oldal, amely az eszközök összes kötet jeleníti meg.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>A StorSimple Manager szolgáltatás navigációs hierarchia
| Kezdőlapja | Szolgáltatásiszint-lapok | Eszközszintű lap | Eszközszintű lap |
| --- | --- | --- | --- |
| StorSimple Manager szolgáltatás |Szolgáltatási irányítópult |Eszköz irányítópult | |
| Eszközök → |Figyelés | | |
| Biztonságimásolat-katalógus |Kötet containers→ |Kötetek | |
| (Szolgáltatás) konfigurálása |Biztonsági mentési házirendek | | |
| Feladatok |(Eszköz) konfigurálása | | |
| Riasztások |Karbantartás | | |

![Videó elérhető](./media/storsimple-manager-service-administration/Video_icon.png)**Videó elérhető**

Kattintson a videót, amely végigvezeti a StorSimple Manager szolgáltatás felhasználói felület, [Itt](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>A StorSimple Manager szolgáltatással a StorSimple eszköz felügyelete
A következő táblázat az általános kezelési feladatok és a bonyolult munkafolyamatok a StorSimple Manager szolgáltatás felhasználói felületi belül végrehajtható összegzését jeleníti meg. Ezeket a feladatokat a felhasználói felület lapokat, amelyen kezdeményezett alapján vannak rendezve.

Minden munkafolyamat kapcsolatos további információkért kattintson a táblázat megfelelő eljárását.

#### <a name="storsimple-manager-workflows"></a>A StorSimple Manager munkafolyamatok
| Ha azt szeretné, hogy ehhez... | Ugrás a felhasználói felület lapon... | Ez az eljárás használható. |
| --- | --- | --- |
| Szolgáltatás létrehozása</br>A szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs lekérése</br>Szolgáltatásregisztrációs kulcs újragenerálása |StorSimple Manager szolgáltatás |[A StorSimple Manager szolgáltatás telepítése](storsimple-manage-service.md) |
| A szolgáltatásadat-titkosítási kulcs módosítása</br>A műveletnaplók megtekintése |A StorSimple Manager szolgáltatás → irányítópult |[A StorSimple Manager szolgáltatás irányítópult](storsimple-service-dashboard.md) |
| Eszköz inaktiválása</br>Eszköz törlése |A StorSimple Manager szolgáltatás → eszközök |[Inaktiválja vagy törölje az eszköz](storsimple-deactivate-and-delete-device.md) |
| Vész-helyreállítási és eszköz-feladatátvétellel kapcsolatos további tudnivalók</br>Feladatátvétel a fizikai eszköz</br>A virtuális eszköz feladatátvétel</br>Üzleti folytonosság vészhelyreállítási (BCDR) |A StorSimple Manager szolgáltatás → eszközök |[A StorSimple eszköz feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-device-failover-disaster-recovery.md) |
| A kötet biztonsági mentéseit lista</br>Válassza ki a biztonságimásolat-készletből</br>A biztonságimásolat-készlet törlése |A StorSimple Manager szolgáltatás → biztonságimásolat-katalógus |[Biztonsági másolatok kezelése](storsimple-manage-backup-catalog.md) |
| Kötet klónozása |A StorSimple Manager szolgáltatás → biztonságimásolat-katalógus |[Kötet klónozása](storsimple-clone-volume.md) |
| Állítsa vissza a biztonságimásolat-készletből |A StorSimple Manager szolgáltatás → biztonságimásolat-katalógus |[Állítsa vissza a biztonságimásolat-készletből](storsimple-restore-from-backup-set.md) |
| Tárolási fiókokról</br>Tárfiók hozzáadása</br>A storage-fiók szerkesztése</br>Tárfiók törlése</br>Kulcs elforgatási szögét storage-fiókok |Konfigurálja a StorSimple Manager szolgáltatás → |[Tárfiókok kezelése](storsimple-manage-storage-accounts.md) |
| Sávszélesség-sablonok</br>Sávszélességsablon hozzáadása</br>A sávszélesség-sablon szerkesztése</br>Sávszélesség sablon törlése</br>Sávszélesség alapértelmezett sablon használata</br>Egy megadott időpontban elinduló napos sávszélesség sablon létrehozása |Konfigurálja a StorSimple Manager szolgáltatás → |[Sávszélességsablonok kezelése](storsimple-manage-bandwidth-templates.md) |
| Hozzáférés-vezérlési rekordokat kapcsolatos</br>Hozzon létre egy hozzáférés-vezérlési rekordot</br>Egy hozzáférés-vezérlési rekordot szerkesztése</br>Egy hozzáférés-vezérlési rekordot törlése |Konfigurálja a StorSimple Manager szolgáltatás → |[Hozzáférés-vezérlési rekordokat kezelése](storsimple-manage-acrs.md) |
| Feladatok részleteinek megjelenítése</br>Feladatok megszakítása |A StorSimple Manager szolgáltatás → feladatok |[Feladatok kezelése](storsimple-manage-jobs.md) |
| Riasztási értesítések fogadása</br>Riasztások kezelése</br>Riasztások áttekintése |A StorSimple Manager Szolgáltatásriasztások → |[StorSimple-riasztások megtekintése és kezelése](storsimple-manage-alerts.md) |
| Csatlakoztatott kezdeményezők megtekintése</br>Az eszköz sorozatszámát keresése</br>A cél IQN keresése |A StorSimple Manager szolgáltatás → eszközök → irányítópult |[A StorSimple eszköz irányítópult](storsimple-device-dashboard.md) |
| Figyelési diagramok létrehozása |A StorSimple Manager szolgáltatás → eszközök figyelése → |[A StorSimple eszköz figyelése](storsimple-monitor-device.md) |
| A kötettároló hozzáadása</br>A kötettároló módosítása</br>A kötettároló törlése |Eszközök → Kötettárolók StorSimple Manager szolgáltatás → |[Kötettárolók kezelése](storsimple-manage-volume-containers.md) |
| Kötet hozzáadása</br>A kötet módosítása</br>A kötet offline állapotba helyezése</br>Kötet törlése</br>A kötet figyelése |A StorSimple Manager szolgáltatás → eszközök → Kötettárolók → kötetek |[Kötetek kezelése](storsimple-manage-volumes.md) |
| Eszköz beállításainak módosítása</br>Idő beállításainak módosítása</br>DNS.md beállításainak módosítása</br>Hálózati adapterek konfigurálása |A StorSimple Manager szolgáltatás → eszközök → konfigurálása |[A StorSimple eszköz eszköz konfigurációjának módosítása](storsimple-modify-device-config.md) |
| Webalkalmazás-proxy beállítások megjelenítése |A StorSimple Manager szolgáltatás → eszközök → konfigurálása |[Az eszköz webalkalmazás-proxy konfigurálása](storsimple-configure-web-proxy.md) |
| Eszköz rendszergazdai jelszavának módosítása</br>StorSimple Snapshot Manager jelszavának módosítása |A StorSimple Manager szolgáltatás → eszközök → konfigurálása |[StorSimple-jelszavak módosítása](storsimple-change-passwords.md) |
| A távfelügyelet konfigurálása |A StorSimple Manager szolgáltatás → eszközök → konfigurálása |[Távoli csatlakozás a StorSimple eszköz](storsimple-remote-connect.md) |
| A riasztási beállítások konfigurálása |A StorSimple Manager szolgáltatás → eszközök → konfigurálása |[StorSimple-riasztások megtekintése és kezelése](storsimple-manage-alerts.md) |
| A CHAP konfigurálása a StorSimple eszköz |A StorSimple Manager szolgáltatás → eszközök → konfigurálása |[A CHAP konfigurálása a StorSimple eszköz](storsimple-configure-chap.md) |
| Biztonsági mentési szabályzat hozzáadása</br>Adja hozzá vagy ütemezésének módosítása</br>A biztonsági mentési házirend törlése</br>Manuális biztonsági mentés készítése</br>Hozzon létre egy egyéni biztonsági mentési házirend több kötetek és ütemezése |A StorSimple Manager szolgáltatás → eszközök → biztonsági mentési házirendek |[Biztonsági mentési szabályzatok kezelése](storsimple-manage-backup-policies.md) |
| Állítsa le a eszközvezérlők</br>Indítsa újra a eszközvezérlők</br>Eszközvezérlők leállítása</br>Alaphelyzetbe állíthatja az eszközt a gyári beállítások</br>(Csak a helyi eszköz újabb rendszer) |A StorSimple Manager szolgáltatás → eszközök → karbantartás |[A StorSimple eszköz vezérlő kezelése](storsimple-manage-device-controller.md) |
| További tudnivalók a StorSimple hardverösszetevők</br>Figyelje meg a hardver állapotát</br>(Csak a helyi eszköz újabb rendszer) |A StorSimple Manager szolgáltatás → eszközök → karbantartás |[A figyelő hardverösszetevők](storsimple-monitor-hardware-status.md) |
| Hozzon létre egy támogatási csomag |A StorSimple Manager szolgáltatás → eszközök → karbantartás |[Létrehozását és kezelését egy támogatási csomag](storsimple-create-manage-support-package.md) |
| Telepítse a szoftverfrissítéseket |A StorSimple Manager szolgáltatás → eszközök → karbantartás |[Az eszköz frissítése](storsimple-update-device.md) |

## <a name="next-steps"></a>Következő lépések
Ha probléma merül fel a napi szintű üzemeltetése a StorSimple eszköz vagy az összes hardver összetevőit, tekintse meg:

* [Az operatív eszköz hibáinak elhárítása](storsimple-troubleshoot-operational-device.md)
* [Kijelző LED figyelési StorSimple használata](storsimple-monitoring-indicators.md)

Ha mégsem sikerül megoldani a problémákat, és a szolgáltatási kérelem létrehozásához szükséges, tekintse meg a [forduljon a Microsoft ügyfélszolgálatához](storsimple-contact-microsoft-support.md).

