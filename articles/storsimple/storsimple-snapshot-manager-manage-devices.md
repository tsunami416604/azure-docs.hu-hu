---
title: Eszközök kezelése a StorSimple Snapshot Manager segítségével | Microsoft dokumentumok
description: A StorSimple Snapshot Manager MMC beépülő modul használatával csatlakozhat és kezelhető a StorSimple-eszközök.
services: storsimple
documentationcenter: ''
author: twooley
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: a037ddc5a43b288349b17dc7d954991a2c634aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254689"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>StorSimple-eszközök csatlakoztatása és kezelése a StorSimple Snapshot Manager használatával
## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager **hatóköre** ablaktáblában a csomópontok segítségével ellenőrizheti az importált StorSimple-eszközadatokat, és frissítheti a csatlakoztatott tárolóeszközöket. Ezenkívül ha az **Eszközök** csomópontra kattint, az **Eredmények** ablaktáblán megtekintheti a csatlakoztatott eszközök listáját és a megfelelő állapotinformációkat.

![Csatlakoztatott eszközök](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**1. ábra: StorSimple Snapshot Manager csatlakoztatott eszköz** 

A **Nézet-beállításoktól** függően az **Eredmények** ablaktábla az egyes eszközökre vonatkozó alábbi információkat jeleníti meg. (A nézet konfigurálásáról a [Nézet menüben](storsimple-use-snapshot-manager.md#view-menu)talál további információt.

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Név |Az eszköz neve az Azure klasszikus portálon konfigurálva |
| Modell |A készülék típusszáma |
| Verzió |Az eszközre telepített szoftver verziója |
| status |Azt jelzi, hogy az eszköz elérhető-e |
| Utolsó szinkronizált |Az eszköz utolsó szinkronizálásának dátuma és időpontja |
| Sorozatszám |A készülék sorozatszáma |

Ha a jobb gombbal az **Eszközök** csomópontra kattint a **Hatókör** ablaktáblában, a következő műveletek közül választhat:

* Eszköz hozzáadása vagy cseréje
* Eszköz csatlakoztatása és az importálás ellenőrzése
* Csatlakoztatott eszközök frissítése

Ha az **Eszközök** csomópontra kattint, majd a jobb gombbal az eszköz nevére kattint az **Eredmények** ablaktáblán, a következő műveletek közül választhat:

* Eszköz hitelesítése
* Eszközadatok megtekintése
* Eszköz frissítése
* Eszközkonfiguráció törlése
* Eszközjelszó módosítása

> [!NOTE]
> Mindezek a műveletek a **Műveletek** ablaktáblán is elérhetők.


Ez az oktatóanyag bemutatja, hogyan lehet a StorSimple Snapshot Manager használatával eszközöket csatlakoztatni és kezelni, és végrehajtani a következő feladatokat:

* Eszköz hozzáadása vagy cseréje
* Eszköz csatlakoztatása és az importálás ellenőrzése
* Csatlakoztatott eszközök frissítése
* Eszköz hitelesítése
* Eszközadatok megtekintése
* Egyedi eszköz frissítése
* Eszközkonfiguráció törlése
* Lejárt eszközjelszó módosítása
* Meghibásodott eszköz cseréje

> [!NOTE]
> A StorSimple Snapshot Manager felület használatával kapcsolatos általános tudnivalókért látogasson el a [StorSimple Snapshot Manager felhasználói felületére.](storsimple-use-snapshot-manager.md)


## <a name="add-or-replace-a-device"></a>Eszköz hozzáadása vagy cseréje
StorSimple-eszköz hozzáadásához vagy cseréjéhez kövesse az alábbi eljárást.

#### <a name="to-add-or-replace-a-device"></a>Eszköz hozzáadása vagy cseréje
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal az **Eszközök** csomópontra, majd kattintson **az Eszköz konfigurálása parancsra.** Megjelenik **az Eszköz konfigurálása** párbeszédpanel.
   
    ![StorSimple eszköz konfigurálása](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Az **Eszköz** legördülő lista válassza ki az eszköz vagy a virtuális eszköz IP-címét. 
4. A **Jelszó** mezőbe írja be a StorSimple Snapshot Manager jelszót, amelyet az eszközhöz az Azure klasszikus portálon hozott létre. Kattintson az **OK** gombra. A StorSimple Snapshot Manager megkeresi az azonosított eszközt. 
   
   * Ha az eszköz elérhető, a StorSimple Snapshot Manager hozzáad egy kapcsolatot.
   * Ha az eszköz bármilyen okból nem érhető el, a StorSimple Snapshot Manager hibaüzenetet ad vissza. A hibaüzenet bezárásához kattintson az **OK** gombra, majd a **Mégse gombra** az **Eszköz konfigurálása** párbeszédpanel bezárásához.

## <a name="connect-a-device-and-verify-imports"></a>Eszköz csatlakoztatása és az importálás ellenőrzése
Az alábbi eljárással csatlakoztasson egy StorSimple-eszközt, és ellenőrizze, hogy a társított biztonsági másolatokkal rendelkező meglévő kötetcsoportok importálása megtörtént-e.

#### <a name="to-connect-a-device-and-verify-imports"></a>Eszköz csatlakoztatása és az importálás ellenőrzése
1. Ha egy eszközt a StorSimple Snapshot Managerhez szeretne csatlakoztatni, kövesse az eszköz hozzáadása vagy cseréje című részben található utasításokat. Amikor egy eszközhöz csatlakozik, a StorSimple Snapshot Manager a következőképpen válaszol:
   
   * Ha az eszköz bármilyen okból nem érhető el, a StorSimple Snapshot Manager hibaüzenetet ad vissza. 
   
   * Ha az eszköz elérhető, a StorSimple Snapshot Manager hozzáad egy kapcsolatot. Amikor kiválasztja az eszközt, az megjelenik az **Eredmények** ablaktáblán, és az állapotmező azt jelzi, hogy az eszköz **elérhető.** A StorSimple Snapshot Manager importálja az eszközhöz konfigurált kötetcsoportokat, feltéve, hogy a kötetcsoportokhoz biztonsági másolatok tartoznak. A biztonsági mentési házirendek importálása nem történik meg. A társított biztonsági másolattal nem rendelkező kötetcsoportok importálása nem történik meg.
2. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
3. Kattintson a jobb gombbal a felső csomópontra a **Hatókör** ablaktáblában, majd kattintson az **Importálás megjelenítése parancs váltása parancsra.**
   
    ![Import megjelenítése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Megjelenik az **Importálások megjelenítése** párbeszédpanel, amely az importált kötetcsoportok és biztonsági mentések állapotát mutatja. Kattintson az **OK** gombra.

A kötetcsoportok és biztonsági mentések importálása után a StorSimple Snapshot Manager segítségével kezelheti őket, ugyanúgy, mint a StorSimple Snapshot Managerrel létrehozott és konfigurált kötetcsoportok és biztonsági mentések kezelése. 

## <a name="refresh-connected-devices"></a>Csatlakoztatott eszközök frissítése
Az alábbi eljárással szinkronizálhatja a csatlakoztatott StorSimple eszközöket a StorSimple Snapshot Managerrel.

#### <a name="to-refresh-connected-devices"></a>Csatlakoztatott eszközök frissítése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal **az Eszközök**elemre, majd kattintson az Eszközök **frissítése parancsra.** Ez szinkronizálja a csatlakoztatott eszközöket a StorSimple Snapshot Managerrel, így megtekintheti a kötetcsoportokat és a biztonsági másolatokat, beleértve a legutóbbi kiegészítéseket is. 
   
    ![A StorSimple eszközök frissítése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Az **Eszközök frissítése** művelet lekéri az új kötetcsoportokat és a kapcsolódó biztonsági másolatokat a csatlakoztatott eszközökről. A Kötetek csomóponthoz elérhető **Rescan volumes** művelettel **ellentétben** az **Eszközök frissítése** nem állítja vissza a biztonsági másolat beállításjegyzékét.

## <a name="authenticate-a-device"></a>Eszköz hitelesítése
Az alábbi eljárással hitelesítheti a StorSimple-eszközt a StorSimple Snapshot Manager rel.

#### <a name="to-authenticate-a-device"></a>Eszköz hitelesítése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson az **Eszközök gombra.**
3. Az **Eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, majd kattintson a **Hitelesítés parancsra.**
4. Megjelenik **a Hitelesítés** párbeszédpanel. Írja be az eszköz jelszavát, majd kattintson **az OK gombra.**
   
    ![Hitelesítés párbeszédpanel](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Eszközadatok megtekintése
Az alábbi eljárással megtekintheti a StorSimple-eszköz részleteit, és szükség esetén újraszinkronizálhatja az eszközt a StorSimple Snapshot Managerrel.

#### <a name="to-view-and-resynchronize-device-details"></a>Az eszköz részleteinek megtekintése és újraszinkronizálása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson az **Eszközök gombra.**
3. Az **Eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, majd kattintson a **Részletek parancsra.**

4.Megjelenik **az Eszköz részletei** párbeszédpanel. Ebben a mezőben a név, a modell, a verziószám, a sorozatszám, az állapot, a cél iSCSI minősített név (IQN) és az utolsó szinkronizálás dátuma és időpontja látható.

* Az eszköz szinkronizálásához kattintson az **Újraszinkronizálás gombra.**
* A párbeszédpanel bezárásához kattintson az **OK** vagy a **Mégse** gombra.
  
  ![Eszközadatok](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Egyedi eszköz frissítése
Az alábbi eljárással szinkronizálhatja újra az egyes StorSimple-eszközöket a StorSimple Snapshot Managerrel.

#### <a name="to-refresh-a-device"></a>Eszköz frissítése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához. 
2. A **Hatókör** ablaktáblán kattintson az **Eszközök gombra.** 
3. Az **Eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, majd kattintson az **Eszköz frissítése parancsra.** Ez szinkronizálja az eszközt a StorSimple Snapshot Manager rel.

## <a name="delete-a-device-configuration"></a>Eszközkonfiguráció törlése
Az alábbi eljárással törölheti az egyes StorSimple-eszközkonfigurációt a StorSimple Snapshot Managerből.

#### <a name="to-delete-a-device-configuration"></a>Eszközkonfiguráció törlése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson az **Eszközök gombra.** 
3. Az **Eredmények** ablaktáblán kattintson a jobb gombbal az eszköz nevére, majd kattintson a **Törlés parancsra.** 
4. A következő üzenet jelenik meg. Kattintson az **Igen** gombra a konfiguráció törléséhez, vagy kattintson a **Nem** gombra a törlés megszakításához.
   
    ![Eszköz konfigurációjának törlése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Lejárt eszközjelszó módosítása
Meg kell adnia egy jelszót a StorSimple-eszköz storSimple Snapshot Manager hitelesítéséhez. Ezt a jelszót akkor konfigurálja, amikor a Windows PowerShell-felületen állítja be az eszközt. A jelszó azonban lejár. Ha ez történik, használhatja az Azure klasszikus portálon a jelszó módosításához. Ezután, mivel az eszköz a Jelszó lejárta előtt konfigurálva volt a StorSimple Snapshot Managerben, újra kell hitelesítenie az eszközt a StorSimple Snapshot Managerben.

#### <a name="to-change-the-expired-password"></a>A lejárt jelszó módosítása
1. A klasszikus Azure-portálon indítsa el a StorSimple Manager szolgáltatást.
2. Kattintson az **Eszköz** > **konfigurálása** elemre.
3. Görgessen le a StorSimple Snapshot Manager szakaszhoz. Adjon meg egy 14-15 karakterből álló jelszót. Győződjön meg arról, hogy a jelszó nagybetűs, kis- és numerikus és speciális karaktereket tartalmaz.
4. A megerősítéshez adja meg újra a jelszót.
5. Kattintson a lap alján található **Mentés** gombra.

#### <a name="to-re-authenticate-the-device"></a>Az eszköz újbóli hitelesítése
1. Indítsa el a StorSimple Snapshot Managert.
2. A **Hatókör** ablaktáblán kattintson az **Eszközök gombra.** A konfigurált eszközök listája megjelenik az **Eredmények** ablaktáblán.
3. Jelölje ki az eszközt, kattintson a jobb gombbal, majd kattintson a **Hitelesítés parancsra.**
4. A **Hitelesítés** ablakban adja meg az új jelszót.
5. Jelölje ki az eszközt, kattintson a jobb gombbal, és válassza **az Eszköz frissítése parancsot.** Ez szinkronizálja az eszközt a StorSimple Snapshot Manager rel.

## <a name="replace-a-failed-device"></a>Meghibásodott eszköz cseréje
Ha egy StorSimple-eszköz meghibásodik, és egy készenléti (feladatátvételi) eszköz váltja fel, az alábbi lépésekkel csatlakozhat az új eszközhöz, és megtekintheti a kapcsolódó biztonsági másolatokat.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Csatlakozás új eszközhöz feladatátvétel után
1. Konfigurálja újra az iSCSI-kapcsolatot az új eszközhöz. Az utasításokért válassza a ["7.](storsimple-8000-deployment-walkthrough-u2.md)

> [!NOTE]
> Ha az új StorSimple-eszköz ugyanazt az IP-címet, mint a régi, előfordulhat, hogy képes csatlakozni a régi konfigurációt.


1. A Microsoft StorSimple felügyeleti szolgáltatás leállítása:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultjának **Eszközök** menüjében válassza a **Szolgáltatások parancslehetőséget.**
   3. A **Szolgáltatások** ablakban válassza a **Microsoft StorSimple Management Service lehetőséget.**
   4. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás leállítása**elemre.
2. Távolítsa el a régi eszközhöz kapcsolódó konfigurációs információkat:
   
   1. A Fájlkezelőben keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog mappát.
   2. Törölje a BACatalog mappában lévő fájlokat.
3. Indítsa újra a Microsoft StorSimple felügyeleti szolgáltatást:
   
   1. A Kiszolgálókezelő irányítópultjának **Eszközök** menüjében válassza a **Szolgáltatások parancslehetőséget.**
   2. A **Szolgáltatások** ablakban válassza a **Microsoft StorSimple Management Service lehetőséget.**
   3. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás újraindítása**elemre.
4. Indítsa el a StorSimple Snapshot Managert.
5. Az új StorSimple-eszköz konfigurálásához hajtsa végre a [2.](storsimple-snapshot-manager-deployment.md)
6. Kattintson a jobb gombbal a felső szintű csomópontra a **Hatókör** ablaktáblában (a példában a StorSimple Snapshot Manager), majd kattintson az **Importálásmegjelenítése váltása parancsra.** 
7. Egy üzenet jelenik meg, ha az importált kötetcsoportok és biztonsági mentések láthatók a StorSimple Snapshot Manager ben. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezelheti a StorSimple snapshot kezelőt a StorSimple megoldás felügyeletéhez.](storsimple-snapshot-manager-admin.md)
* Ismerje meg, hogyan tekintheti meg [és kezelheti a köteteket a StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-volumes.md)segítségével.

