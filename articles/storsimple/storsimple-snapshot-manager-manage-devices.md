---
title: "Eszközkezelés a StorSimple Snapshot Manager |} Microsoft Docs"
description: "Ismerteti a StorSimple Snapshot Manager MMC beépülő modul segítségével csatlakozhat, és kezelheti a StorSimple eszközökhöz."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>StorSimple Snapshot Manager segítségével csatlakozhat, és kezelheti a StorSimple eszközökhöz
## <a name="overview"></a>Áttekintés
Használhatja csomópontok a StorSimple Snapshot Manager **hatókör** importált StorSimple eszköz adatok ellenőrzése és frissítése csatlakoztatott tárolóeszközök ablaktáblán. Továbbá, amikor rákattint az **eszközök** csomópont, a csatlakoztatott eszközök és a megfelelő állapot adatai listáját megtekintheti a **eredmények** ablaktáblán.

![Csatlakoztatott eszközök](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**1. ábra: A StorSimple Snapshot Manager csatlakoztatott eszközön** 

Attól függően a **nézet** beállításokat, a **eredmények** ablaktábla megjeleníti azokat az egyes eszközök a következő információkat. (Nézet konfigurálásával kapcsolatos további információkért látogasson el [Nézet menü](storsimple-use-snapshot-manager.md#view-menu).

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Név |A klasszikus Azure portálon az eszköz neve |
| Modell |A modell száma az eszköz |
| Verzió |A szoftver az eszközre telepített verziója |
| status |Hogy az eszköz rendelkezésre áll-e |
| Az utolsó szinkronizálás |Dátum és idő, amikor az eszköz volt utoljára szinkronizálva |
| Sorozatszám |Az eszköz sorozatszámát |

Ha a jobb gombbal a **eszközök** csomópontja a **hatókör** ablaktáblán választhat az alábbi műveleteket:

* Adja hozzá, vagy cserélje le az eszköz
* Csatlakoztasson egy eszközt, és ellenőrizze a importálása
* Frissítse a csatlakoztatott eszközök

Ha a **eszközök** csomópontot, és kattintson rá jobb gombbal egy eszköz neve az a **eredmények** ablaktáblán választhat az alábbi műveleteket:

* Egy eszköz hitelesítéséhez
* Eszköz részleteinek megtekintése
* Egy eszköz frissítése
* Egy eszköz konfigurálásának törlése
* A jelszó módosítása

> [!NOTE]
> Ezek a műveletek mindegyike is elérhető a **műveletek** ablaktáblán.


Ez az oktatóanyag ismerteti, hogyan StorSimple Snapshot Manager használatával csatlakozzon és eszközök kezeléséhez, és hajtsa végre a következő feladatokat:

* Adja hozzá, vagy cserélje le az eszköz
* Csatlakoztasson egy eszközt, és ellenőrizze a importálása
* Frissítse a csatlakoztatott eszközök
* Egy eszköz hitelesítéséhez
* Eszköz részleteinek megtekintése
* Egy adott eszköz frissítése
* Egy eszköz konfigurálásának törlése
* Egy lejárt jelszó módosítása
* Cserélje le a hibás eszköz

> [!NOTE]
> A StorSimple Snapshot Manager felület használatával kapcsolatos általános információkért látogasson el [StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adja hozzá, vagy cserélje le az eszköz
A következő eljárással lehet hozzáadni, vagy cserélje le a StorSimple eszköz.

#### <a name="to-add-or-replace-a-device"></a>Adja hozzá, vagy cserélje le az eszköz
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **eszközök** csomópontra, majd **eszköz konfigurálása**. A **eszköz konfigurálása** párbeszédpanel jelenik meg.
   
    ![A StorSimple eszköz konfigurálása](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Az a **eszköz** legördülő mezőben válassza ki az IP-címét vagy a virtuális eszköz. 
4. Az a **jelszó** szöveg mezőbe írja be a StorSimple Snapshot Manager jelszavát, amelyet az eszköz a klasszikus Azure portálon létrehozott. Kattintson az **OK** gombra. StorSimple Snapshot Manager megkeresi az azonosított eszközre. 
   
   * Ha az eszköz nem érhető el, a StorSimple Snapshot Manager hozzáadja a kapcsolat.
   * Ha az eszköz a bármilyen okból nem érhető el, a StorSimple Snapshot Manager hibaüzenetet ad vissza. Kattintson a **OK** zárja be a hibaüzenetet, és kattintson **Mégse** bezárásához a **eszköz konfigurálása** párbeszédpanel megnyitásához.

## <a name="connect-a-device-and-verify-imports"></a>Csatlakoztasson egy eszközt, és ellenőrizze a importálása
A következő eljárás segítségével csatlakoztassa a StorSimple eszközt, és győződjön meg arról, hogy minden meglévő kötet csoportokhoz társított biztonsági másolatok lettek importálva.

#### <a name="to-connect-a-device-and-verify-imports"></a>Csatlakoztasson egy eszközt, és győződjön meg arról importálja
1. Csatlakoztasson egy eszközt a StorSimple Snapshot Manager, kövesse az utasításokat a Hozzáadás, vagy cserélje ki egy eszközt. Amikor az eszköz csatlakozik, az alábbiak szerint válaszol a StorSimple Snapshot Manager:
   
   * Ha az eszköz a bármilyen okból nem érhető el, a StorSimple Snapshot Manager hibaüzenetet ad vissza. 
   
   * Ha az eszköz nem érhető el, a StorSimple Snapshot Manager hozzáadja a kapcsolat. Ha az eszköz választja, megjelenik a **eredmények** ablaktáblán, és az állapot mező jelzi, hogy az eszköz **elérhető**. StorSimple Snapshot Manager importálja az eszköz minden kötet-csoportokhoz, feltéve, hogy a kötet csoportokhoz társított biztonsági másolatok. Biztonsági mentési házirendek nincsenek importálva. Kötet csoportok, amelyek nem rendelkeznek társított biztonsági másolatok nincsenek importálva.
2. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
3. Kattintson a jobb gombbal a legfelső csomópontra a **hatókör** ablaktáblán, és kattintson **váltása importálja megjelenítési**.
   
    ![Válassza ki-/ bekapcsolása importálja megjelenítése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. A **váltása importálja megjelenítési** párbeszédpanel jelenik meg, az importált kötet csoportok és a biztonsági mentések állapotát megjeleníti. Kattintson az **OK** gombra.

Követően a mennyiségi csoportok és a biztonsági mentések sikeresen importálva, használhatja a StorSimple Snapshot Manager kezelhetők, ugyanúgy, mint a kötet csoportok és a biztonsági mentések, amelyek létrehozása és konfigurálása a StorSimple Snapshot Manager ugyanúgy felügyelheti. 

## <a name="refresh-connected-devices"></a>Frissítse a csatlakoztatott eszközök
A következő eljárással a csatlakoztatott StorSimple eszközök szinkronizálása a StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Csatlakoztatott eszközök frissítéséhez
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **eszközök**, és kattintson a **frissítési eszközök**. Ez szinkronizálja a csatlakoztatott eszközök a StorSimple Snapshot Manager, hogy a kötet csoportok és a biztonsági mentések, többek között a legutóbbi kiegészítéseket megtekintéséhez. 
   
    ![A StorSimple eszköz frissítése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

A **frissítési eszközök** művelet lekérdezi a csatlakoztatott eszközökről bármely új kötet csoportok és a társított biztonsági másolatok. Ellentétben a **ismételt vizsgálat kötetek** művelet érhető el a **kötetek** csomópont, **frissítési eszközök** nem állítja vissza a beállításjegyzék biztonsági mentése.

## <a name="authenticate-a-device"></a>Egy eszköz hitelesítéséhez
A következő eljárással a StorSimple eszköz StorSimple Snapshot Manager hitelesítéséhez.

#### <a name="to-authenticate-a-device"></a>Egy eszköz hitelesítéséhez
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**.
3. Az a **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevét, majd kattintson a **hitelesítés**.
4. A **hitelesítés** párbeszédpanel jelenik meg. Írja be a jelszót, és kattintson **OK**.
   
    ![Hitelesítés párbeszédpanel](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Eszköz részleteinek megtekintése
A következő eljárás segítségével a StorSimple eszköz részletes adatainak megtekintéséhez, és ha szükséges, az eszköz StorSimple Snapshot Manager újraszinkronizálása.

#### <a name="to-view-and-resynchronize-device-details"></a>A megtekintése, és az újraszinkronizáláshoz. eszköz részletei
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**.
3. Az a **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevét, majd kattintson a **részletek**.

4 a **eszközadatok** párbeszédpanel jelenik meg. Ebben a mezőben jeleníti meg a nevét, modell, verzió, sorozatszámát, állapotát, cél iSCSI minősített nevét (IQN), és utolsó szinkronizálás dátuma és időpontja.

* Kattintson a **újraszinkronizálásra** az eszköz szinkronizálása.
* Kattintson a **OK** vagy **Mégse** a párbeszédpanel bezárásához.
  
  ![Eszköz részletei](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Egy adott eszköz frissítése
A következő eljárással egy önálló StorSimple eszközt a StorSimple Snapshot Manager újraszinkronizálása.

#### <a name="to-refresh-a-device"></a>Egy eszköz frissítése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager. 
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**. 
3. Az a **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevét, majd kattintson a **frissítése eszköz**. Ez a StorSimple Snapshot Manager szinkronizálja az eszközt.

## <a name="delete-a-device-configuration"></a>Egy eszköz konfigurálásának törlése
Az alábbi eljárás segítségével törölheti az egyes StorSimple eszköz konfigurációs a StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Egy eszköz konfigurálásának törlése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**. 
3. Az a **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevét, majd kattintson a **törlése**. 
4. A következő üzenet jelenik meg. Kattintson a **Igen** törölje a konfigurációt, vagy kattintson a **nem** a törlés visszavonására.
   
    ![Eszköz törlése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Egy lejárt jelszó módosítása
Meg kell adnia egy jelszót a StorSimple eszköz StorSimple Snapshot Manager hitelesítéséhez. Ha a Windows PowerShell-felületet segítségével állítsa be az eszközt konfigurálnia ezt a jelszót. Azonban a jelszó is lejár. Ez akkor fordul elő, ha a klasszikus Azure portál segítségével módosítsa a jelszót. Ezt követően az eszköz StorSimple Snapshot Manager konfigurálása előtt a jelszó lejárt, mert az eszköz StorSimple Snapshot Manager újra hitelesítenie kell.

#### <a name="to-change-the-expired-password"></a>A lejárt jelszó módosítása
1. A klasszikus Azure portálon a StorSimple Manager szolgáltatás elindítása.
2. Kattintson a **eszközök** > **konfigurálása** az eszközön.
3. Görgessen le a StorSimple Snapshot Manager szakaszban. Adjon meg egy 14 – 15 karakter hosszú jelszót. Győződjön meg arról, hogy a jelszó nagybetű, nagybetűk, numerikus és speciális karakterek vegyesen tartalmazza.
4. Adja meg újra a jelszót a megerősítéshez.
5. Kattintson a lap alján található **Mentés** gombra.

#### <a name="to-re-authenticate-the-device"></a>Újra hitelesíteni az eszközt
1. Indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**. Konfigurált eszközök listája jelenik meg a **eredmények** ablaktáblán.
3. Válassza ki az eszközt, kattintson a jobb gombbal, majd **hitelesítés**.
4. Az a **hitelesítés** ablak, írja be az új jelszót.
5. Válassza ki az eszközt, kattintson a jobb gombbal, majd válassza **frissítési eszköz**. Ez a StorSimple Snapshot Manager szinkronizálja az eszközt.

## <a name="replace-a-failed-device"></a>Cserélje le a hibás eszköz
Ha a StorSimple eszköz sikertelen, és lecseréli a készenléti (feladatátvétel) eszközön, a következő lépésekkel csatlakozzon az új eszközhöz, és a társított biztonsági másolatok megtekintése.

#### <a name="to-connect-to-a-new-device-after-failover"></a>A feladatátvételt követően kapcsolódni egy új eszköz
1. Konfigurálja újra az új eszköz iSCSI csatlakozni. Útmutatásért nyissa meg a "7. lépés: csatlakoztatási, inicializálása és egy kötet formátum" a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Ha az új StorSimple eszköz a régi kiszolgálóéval azonos IP-címét, akkor előfordulhat, hogy tudnak kapcsolódni a régi konfigurációs.


1. A Microsoft a StorSimple szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   3. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
2. Távolítsa el a régi eszköz konfigurációs adatokat:
   
   1. A Fájlkezelőben keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Törölje a BACatalog mappában található fájlokat.
3. Indítsa újra a Microsoft a StorSimple szolgáltatás:
   
   1. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   2. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   3. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**.
4. Indítsa el a StorSimple Snapshot Manager.
5. Az új StorSimple eszköz konfigurálásához végrehajtásához a 2. lépés: Csatlakozás a StorSimple eszköz [StorSimple Snapshot Manager telepítése](storsimple-snapshot-manager-deployment.md).
6. Kattintson a jobb gombbal a legfelső szintű csomópontja a **hatókör** ablaktáblán (a példában a StorSimple Snapshot Manager), és kattintson **váltása importálja megjelenítési**. 
7. Egy üzenet jelenik meg, ha az importált kötet csoportok és a biztonsági mentések a StorSimple Snapshot Manager látható. Kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével megtekintheti, és a kötetek kezelése](storsimple-snapshot-manager-manage-volumes.md).

