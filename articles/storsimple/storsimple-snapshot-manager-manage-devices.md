---
title: Eszközkezelés a StorSimple Snapshot Manager alkalmazással |} A Microsoft Docs
description: Ismerteti a StorSimple Snapshot Manager MMC beépülő modul használatával csatlakozhat, és a StorSimple-eszközök kezelése.
services: storsimple
documentationcenter: ''
author: SharS
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 51632b8b68640814fc113a94925b6d6deaca4c5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482566"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>A StorSimple Snapshot Manager használatával csatlakozhat, és a StorSimple-eszközök kezelése
## <a name="overview"></a>Áttekintés
Csomópontok használhatók a StorSimple Snapshot Managerben **hatókör** a StorSimple-eszköz importált adatokat, és frissítse a csatlakoztatott tárolóeszközök ablaktáblán. Ezenkívül amikor rákattint a **eszközök** csomópontra, a csatlakoztatott eszközök és a megfelelő állapot adatai listáját láthatja a **eredmények** ablaktáblán.

![Csatlakoztatott eszközök](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**1. ábra: Csatlakoztatott eszköz StorSimple Snapshot Managerrel** 

Attól függően, a **nézet** beállításokat is, a **eredmények** panel megjeleníti az egyes eszközök a következő információkat. (Nézet konfigurálásával kapcsolatos további információkért lépjen [Nézet menü](storsimple-use-snapshot-manager.md#view-menu).

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Name (Név) |A klasszikus Azure portálon konfigurált eszköz nevét |
| Modell |Az eszköz modellszáma |
| Version |Az eszközön telepített szoftver verziója |
| status |Az eszköz-e elérhető |
| A legutóbbi szinkronizálásra |Dátum és idő, amikor az eszköz legutóbb szinkronizálva |
| Sorozatszám |Az eszköz sorozatszámát |

Ha a jobb gombbal a **eszközök** csomópontja a **hatókör** ablaktáblán választhat a következő műveleteket:

* Adja hozzá, vagy cserélje le az eszközt
* Eszköz csatlakoztatása, és ellenőrizze az import
* Csatlakoztatott eszközök frissítése

Kattintva a **eszközök** csomópontot, és kattintson rá jobb gombbal egy eszköz neve az a **eredmények** ablaktáblán választhat a következő műveleteket:

* Egy eszköz hitelesítéséhez
* Eszközadatok megtekintése
* Eszköz frissítése
* Egy eszköz konfigurációjának törlése
* Egy eszköz jelszavának módosítása

> [!NOTE]
> Ezek a műveletek mindegyike is rendelkezésre áll a **műveletek** ablaktáblán.


Ez az oktatóanyag azt ismerteti, hogyan használata a StorSimple Snapshot Managerben való csatlakozás és eszközök kezelése és a következő feladatokat:

* Adja hozzá, vagy cserélje le az eszközt
* Eszköz csatlakoztatása, és ellenőrizze az import
* Csatlakoztatott eszközök frissítése
* Egy eszköz hitelesítéséhez
* Eszközadatok megtekintése
* Egy-egy eszköz frissítése
* Egy eszköz konfigurációjának törlése
* Egy lejárt eszköz jelszavának módosítása
* Cserélje le a sikertelen eszközök

> [!NOTE]
> A StorSimple Snapshot Manager felületéről használatával kapcsolatos általános információkért nyissa meg [StorSimple Snapshot Manager felhasználói felületén](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adja hozzá, vagy cserélje le az eszközt
A következő eljárás használatával adja hozzá, vagy cserélje le a StorSimple eszköz.

#### <a name="to-add-or-replace-a-device"></a>Vagy lecserélve az eszköz
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **eszközök** csomópontban, és kattintson **eszközök konfigurálása**. A **eszközök konfigurálása** párbeszédpanel jelenik meg.
   
    ![A StorSimple-eszköz konfigurálása](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Az a **eszköz** legördülő mezőben válassza ki az eszköz vagy a virtuális eszköz IP-címét. 
4. Az a **jelszó** szöveg írja be a StorSimple Snapshot Manager jelszavát, amelyet az eszköz a klasszikus Azure portálon létrehozott. Kattintson az **OK** gombra. A StorSimple Snapshot Manager keresi meg az eszközt, hogy meghatározta. 
   
   * Ha az eszköz érhető el, a StorSimple Snapshot Manager hozzáadja a kapcsolatot.
   * Ha az eszköz valamilyen okból elérhetetlenné válik, a StorSimple Snapshot Manager hibaüzenetet ad vissza. Kattintson a **OK** zárja be a hibaüzenetet, és kattintson a **Mégse** gombra kattintva zárja be a **eszközök konfigurálása** párbeszédpanel bezárásához.

## <a name="connect-a-device-and-verify-imports"></a>Eszköz csatlakoztatása, és ellenőrizze az import
A következő eljárással a StorSimple eszköz csatlakozhat, és győződjön meg arról, hogy minden olyan meglévő kötet csoportokhoz társított biztonsági másolatok importálva vannak.

#### <a name="to-connect-a-device-and-verify-imports"></a>Eszköz csatlakoztatása, és importálja ellenőrzése
1. Csatlakoztasson egy eszközt a StorSimple Snapshot Managerben, kövesse a Hozzáadás, vagy cserélje le az eszközt. Amikor egy eszköz csatlakozik, a StorSimple Snapshot Manager válaszol a következő:
   
   * Ha az eszköz valamilyen okból elérhetetlenné válik, a StorSimple Snapshot Manager hibaüzenetet ad vissza. 
   
   * Ha az eszköz érhető el, a StorSimple Snapshot Manager hozzáadja a kapcsolatot. Amikor az eszköz választja, megjelenik a **eredmények** panelje, és az állapot azt jelzi, hogy az eszköz **elérhető**. A StorSimple Snapshot Manager konfigurálva az eszköz, kötet-csoportnak sem importálja, feltéve, hogy a kötet csoportokhoz társított biztonsági másolatok. Biztonsági mentési házirendek nem lesznek importálva. Kötet csoportok, amelyek nem rendelkeznek társított biztonsági másolatok nem lesznek importálva.
2. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
3. Kattintson a jobb gombbal a legfelső szintű csomópontját a **hatókör** ablaktáblán, és kattintson **váltógomb import megjelenített**.
   
    ![Válassza ki-/ bekapcsolása import megjelenítése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. A **váltógomb import megjelenített** párbeszédpanel megjelenik, és az importált kötet csoportok és a biztonsági mentések állapotát. Kattintson az **OK** gombra.

Után a kötet csoportokat és a biztonsági mentések importálása sikeresen befejeződött, használhatja a StorSimple Snapshot Manager őket, ugyanúgy, mint a kötet és biztonsági mentések, létrehozása és konfigurálása a StorSimple Snapshot Manager alkalmazással kezelik lenne. 

## <a name="refresh-connected-devices"></a>Csatlakoztatott eszközök frissítése
Az alábbi eljárás segítségével csatlakoztatott StorSimple eszközt a StorSimple Snapshot Managerrel szinkronizálása.

#### <a name="to-refresh-connected-devices"></a>Csatlakoztatott eszközök frissítése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **eszközök**, és kattintson a **frissítési eszközök**. Ez szinkronizálja a csatlakoztatott eszközök a StorSimple Snapshot Managerrel, hogy a kötet csoportokat és a biztonsági másolatok, többek között a valamely legutóbbi kiegészítése is megtekintheti. 
   
    ![A StorSimple eszköz frissítése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

A **frissítési eszközök** művelet bármely új kötet csoportokat, és minden kapcsolódó biztonsági lekérdezi a csatlakoztatott eszközökről. Ellentétben a **ismételt vizsgálat kötetek** művelet elérhető a **kötetek** csomópont, **frissítési eszközök** nem állítja vissza a beállításjegyzék biztonsági mentése.

## <a name="authenticate-a-device"></a>Egy eszköz hitelesítéséhez
Az alábbi eljárás segítségével hitelesíteni a StorSimple eszköz StorSimple Snapshot Manager alkalmazással.

#### <a name="to-authenticate-a-device"></a>Egy eszköz hitelesítéséhez
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**.
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, és kattintson **hitelesítés**.
4. A **hitelesítés** párbeszédpanel jelenik meg. Írja be a jelszót, és kattintson a **OK**.
   
    ![Hitelesítés párbeszédpanel](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Eszközadatok megtekintése
Az alábbi eljárás segítségével megtekintheti a StorSimple eszköz részleteit, és szükség esetén szinkronizálja újra az eszközt a StorSimple Snapshot Managerrel.

#### <a name="to-view-and-resynchronize-device-details"></a>A megtekintéshez és az újraszinkronizáláshoz. eszköz részletei
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**.
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, és kattintson **részletek**.

4 a **eszközadatok** párbeszédpanel jelenik meg. Ebben a mezőben jeleníti meg a nevét, modell, verzió, sorozatszám, állapotát, cél iSCSI minősített nevét (IQN), és a legutóbbi szinkronizálás dátuma és időpontja.

* Kattintson a **újraszinkronizálása** szinkronizálni az eszközt.
* Kattintson a **OK** vagy **Mégse** a párbeszédpanel bezárásához.
  
  ![Eszköz adatai](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Egy-egy eszköz frissítése
A következő eljárás használatával szinkronizálja újra egy-egy StorSimple eszköz StorSimple Snapshot Manager alkalmazással.

#### <a name="to-refresh-a-device"></a>Eszköz frissítése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához. 
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**. 
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, és kattintson **frissítése eszköz**. Ez a StorSimple Snapshot Managerrel szinkronizálja az eszközt.

## <a name="delete-a-device-configuration"></a>Egy eszköz konfigurációjának törlése
Az alábbi eljárás segítségével a StorSimple Snapshot Manager-egyes StorSimple-eszköz konfigurációjának törlése.

#### <a name="to-delete-a-device-configuration"></a>Egy eszköz konfigurációjának törlése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**. 
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, és kattintson **törlése**. 
4. A következő üzenet jelenik meg. Kattintson a **Igen** törölni a konfigurációt, vagy kattintson a **nem** a törlésének visszavonásához.
   
    ![Eszköz konfigurációjának törlése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Egy lejárt eszköz jelszavának módosítása
Meg kell adnia egy jelszót, hitelesíteni a StorSimple eszköz StorSimple Snapshot Manager alkalmazással. A Windows PowerShell felületén állíthatja be az eszköz használatakor konfigurálnia ezt a jelszót. Azonban a jelszó is lejár. Ha ez történik, a klasszikus Azure portál használatával módosítsa a jelszót. Ezt követően az eszközt a StorSimple Snapshot Managerben lett konfigurálva, a jelszó lejárta előtt, mert újra hitelesítenie kell az eszközt a StorSimple Snapshot Managerben.

#### <a name="to-change-the-expired-password"></a>A lejárt jelszó módosítása
1. A klasszikus Azure portálon indítsa el a StorSimple Manager szolgáltatásban.
2. Kattintson a **eszközök** > **konfigurálása** az eszközhöz.
3. Görgessen le a StorSimple Snapshot Manager szakaszban. Adjon meg egy jelszót, amely 14 – 15 karakter hosszú lehet. Győződjön meg arról, hogy a jelszó nagybetűket, kisbetűket, számjegyeket és speciális karaktereket vegyesen tartalmazza.
4. Adja meg újra a jelszót a megerősítéshez.
5. Kattintson a lap alján található **Mentés** gombra.

#### <a name="to-re-authenticate-the-device"></a>Újra hitelesíteni kell az eszközt
1. Start StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **eszközök**. A konfigurált eszközök listája jelenik meg a **eredmények** ablaktáblán.
3. Válassza ki az eszközt, kattintson a jobb gombbal, majd kattintson **hitelesítés**.
4. Az a **hitelesítés** ablakban adja meg az új jelszót.
5. Válassza ki az eszközt, kattintson a jobb gombbal, és válassza **frissítési eszköz**. Ez a StorSimple Snapshot Managerrel szinkronizálja az eszközt.

## <a name="replace-a-failed-device"></a>Cserélje le a sikertelen eszközök
Ha a StorSimple eszköz meghiúsul, és helyébe a készenléti (feladatátvétel) eszközt, használja az alábbi lépéseket az új eszköz csatlakozhat, és a társított biztonsági másolatok megtekintéséhez.

#### <a name="to-connect-to-a-new-device-after-failover"></a>A feladatátvételt követően csatlakozni egy új eszköz
1. Konfigurálja újra az új eszköz iSCSI kapcsolat. Útmutatásért lépjen a "7. lépés: Csatlakoztatása, inicializálása és formázza a kötetet"a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Ha az új StorSimple-eszköz a régi kiszolgálóéval megegyező IP-címre, lehet csatlakozni a régi konfigurációt.


1. A Microsoft StorSimple felügyeleti szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   3. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
2. A konfigurációs adatokat, a régi eszköz eltávolítása:
   
   1. A Fájlkezelőben keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Törölje a BACatalog mappában található fájlokat.
3. A Microsoft StorSimple felügyeleti szolgáltatás újraindítása:
   
   1. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   2. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   3. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**.
4. Start StorSimple Snapshot Manager.
5. Az új StorSimple-eszköz konfigurálásához hajtsa végre a lépéseket, a 2. lépés: A StorSimple eszköz csatlakoztatása [üzembe helyezése a StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Kattintson a jobb gombbal a legfelső szintű csomópontja a **hatókör** panelen (a példában a StorSimple Snapshot Managerrel), és kattintson **váltógomb import megjelenített**. 
7. Egy üzenet jelenik meg, ha az importált kötet csoportok és a biztonsági mentések látható a StorSimple Snapshot Managerben. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [megtekintése és kezelése a kötetek a StorSimple Snapshot Manager használata](storsimple-snapshot-manager-manage-volumes.md).

