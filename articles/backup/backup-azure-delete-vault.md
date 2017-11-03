---
title: " Az Azure Recovery Services-tároló törlése |} Microsoft Docs "
description: "Tudnivalók az Azure biztonsági mentési és a Recovery Services-tároló törlése. Egy mentési tárolót egy Azure-felhőbe tárolóban, vagy az Azure recovery-tároló hívható. Problémák elhárítása a klasszikus portálon vagy az Azure-portálon a mentési tároló nem törölhető."
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: ae4a73d12898c62fe2c5cf3683bc7c1c8c845fdf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése
Az Azure Backup szolgáltatás két típusú tárolóval rendelkezik – a Backup-tárolóval és a Recovery Services-tárolóval. A Backup-tároló vált elsőként elérhetővé. Utána következett a Recovery Services-tároló a kibővített, Resource Managerrel történő üzembe helyezések támogatása érdekében. Bővített képességeit, és az információk függőségek, amely a tárolóban kell tárolni, mert egy biztonsági mentési vagy a Recovery Services-tároló törlése zavaró lehet. Ez a cikk ismerteti a tárolók a klasszikus portál és az Azure-portálon az törlése.  

| **Központi telepítési típus** | **Portál** | **Tároló neve** |
| --- | --- | --- |
| Klasszikus |Klasszikus |Mentési tároló |
| Resource Manager |Azure |Recovery Services-tároló |

> [!NOTE]
> A Backup-tárolók nem tudják megvédeni a Resource Manager által üzembe helyezett megoldásokat. Recovery Services-tároló segítségével azonban classically telepített kiszolgálók és virtuális gépek védelmére.  
>

> [!IMPORTANT]
> A biztonsági mentési tárolókról mostantól lehetőség van Recovery Services-tárolókra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> **2017. október 15-től** a PowerShell a továbbiakban nem használható Backup-tárolók létrehozására. <br/> **2017. november 1-től kezdődően**:
>- A rendszer automatikusan elvégzi valamennyi megmaradó biztonsági mentési tároló átváltását Recovery Services-tárolókra.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>

Ebben a cikkben a kifejezés használjuk tárolóban, az általános űrlapon a mentési tárolóhoz vagy a Recovery Services-tároló hivatkozik. Ha szükséges, a tárolók megkülönböztetni a formális nevét, a Backup-tárolóban és a Recovery Services-tároló használjuk.

## <a name="deleting-a-recovery-services-vault"></a>Recovery Services-tároló törlése
Recovery Services-tároló törlése adatbázisunk - *a tároló nem tartalmaz semmilyen erőforráshoz megadott*. Recovery Services-tároló törlése előtt távolítsa el, vagy törli az összes erőforrást a tárolóban lévő állapottal. Ha törli a tárolót erőforrásokat tartalmazó, például az alábbi képen hibaüzenetet kap:

![Tároló törlése hiba](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Kattintson az erőforrások a tárolóból törölve lett, amíg **újra** ugyanezt a hibaüzenetet eredményez. Ha most rögzített, a hibaüzenet, kattintson a **Mégse** és az alábbi lépésekkel törli az erőforrást a tárolóban lévő állapottal.

### <a name="removing-the-items-from-a-vault-protecting-a-vm"></a>Az elemek eltávolítása a tárolóból, a virtuális gépek védelméhez
Ha már rendelkezik nyissa meg a Recovery Services-tároló, ugorjon a második lépésre.

1. Nyissa meg az Azure-portálon, és az irányítópultról nyissa meg a törölni kívánt tárolót.

   Ha még nem rendelkezik a Recovery Services-tároló rögzítve az irányítópulton, a központ menüben kattintson a **több szolgáltatások** írja be az erőforrások listájához, **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tárolók**.

   ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   A Recovery Services-tárolók listája jelenik meg. A listában jelölje ki a törölni kívánt tárolót.

   ![tároló válasszon a listából](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. A tároló nézetben, tekintse meg a **Essentials** ablaktáblán. Törli a tárolót, a védett elemek nem lehet. Ha megjelenik egy száma nem nulla, vagy a **biztonsági mentés elemek** vagy **biztonsági mentése a felügyeleti kiszolgálók**, a tároló törlése előtt el kell távolítania azokat az elemeket.

    ![Nézze meg védett elemek Essentials ablaktábla](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Virtuális gépek és a fájlok és mappák biztonsági mentés elemek minősülnek, és jelennek meg a **biztonsági mentés elemek** az Essentials ablaktábla területére. A DPM-kiszolgáló szerepel a **Management Server biztonsági másolat** az Essentials ablaktábla területére. **Replikált elemek** vonatkoznak az Azure Site Recovery szolgáltatásban.
3. A védett elemek eltávolítása a tárolóból megkezdéséhez azon elemeinek megkeresése a tárolóban lévő állapottal. Tároló irányítópultján kattintson **beállítások**, és kattintson a **biztonsági mentési elemek** , hogy a panel megnyitásához.

    ![tároló válasszon a listából](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    A **biztonsági mentés elemek** panel rendelkezik külön listák alapján elemtípus: Azure virtuális gépek vagy a fájl-mappák (lásd a kép). Elemtípus megjelenik az Azure virtuális gépeken. Válassza ki, ha a fájl-mappák elemek listáját a tárolóban, **-mappákban** a legördülő menüből.
4. Egy elem törlése a tárolóból, a virtuális gépek védelméhez, előtt állítsa le az elemhez tartozó biztonsági mentési feladat, és törölni a helyreállítási pont adatait. Minden elemhez a tárolóban kövesse az alábbi lépéseket:

    a. Az a **biztonsági másolati elemei** panelen kattintson a jobb gombbal az elemet, és válassza ki a helyi menüből **Stop biztonsági mentés**.

    ![a biztonsági mentési feladat leállítása](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    A biztonsági mentés leállítása panel nyílik meg.

    b. A a **biztonsági mentés leállítása** panelen a a **válasszon egy lehetőséget** menüjében válassza **biztonsági mentési adatok törlése** > írja be a elem neve > kattintson **Stop biztonsági mentés**.

    Írja be a következő elem ellenőrzése a törölni kívánt nevét. A **állítsa le a biztonsági mentés** gomb akkor aktiválódik, miután ellenőrizte, hogy a cikk. Ha nem látja a párbeszédpanelen írja be a biztonsági mentési elem neve, úgy döntött, hogy a **biztonsági mentési adatok megőrzése** lehetőséget.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Igény szerint is adja meg a okát, miért törölni az adatokat, majd adja meg megjegyzéseit. Miután rákattintott **állítsa le a biztonsági mentés**, törli a tárolót előtt fejezze be a törlési feladat engedélyezése. Győződjön meg arról, hogy a feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). <br/>
    A feladat végrehajtása után kap egy üzenetet, amely meghatározza, hogy a biztonsági mentési folyamat le lett állítva, a biztonsági mentési adatok számára, hogy az elem törölve.

    c. A lista egy elemére a törlése után a **biztonsági mentés elemek** menüben kattintson **frissítése** a fennmaradó elemeket a tárolóban lévő állapottal.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

      Ha nincsenek elemek a listában, görgessen a **Essentials** ablaktábla a biztonsági mentési tároló paneljén. Nincs nem lehet bármely **elem biztonsági mentését**, **biztonsági mentése a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel a listában. Ha elemek még mindig a tárolóban, három lépés lépjen vissza, és válasszon egy másik elem típusa listát.  
5. Ha nincsenek további elemek tároló eszköztárán, kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-vault.png)
6. Győződjön meg arról, hogy szeretné-e a tárolót, kattintson a **Igen**.

    A tároló törlődik, és visszaadja a portál a **új** szolgáltatás menü.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Mi történik, ha a biztonsági mentési folyamat leállt, de megőrzi az adatokat?
Ha azonban véletlenül leállítja a biztonsági mentési folyamat *maradnak* az adatokat, törölnie kell a biztonsági mentési adatok a tároló törlése előtt. A biztonsági mentési adatok törlése:

1. Az a **biztonsági másolati elemei** panelen kattintson a jobb gombbal az elemet, és a helyi menüben kattintson a **biztonsági mentési adatok törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    A **biztonságimásolat-adatok törlése** panel nyílik meg.
2. Az a **biztonságimásolat-adatok törlése** panelen adja meg az elem nevét, és kattintson a **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Ha az adatok törlését, 4c lépésnél lépjen vissza, és folytathatja a műveletet.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>A DPM-kiszolgáló védelmére használt tároló törlése
Egy tárolót a DPM-kiszolgáló védelmére használt törlése előtt törölje a létrehozott helyreállítási pontot, és az majd szüntesse meg a kiszolgáló a tárolóból.

Törölni kívánja a védelmi csoporthoz tartozó adatokat:

1. A DPM felügyeleti konzolján kattintson **védelmi** > Válasszon ki egy védelmi csoportot > Válassza ki a védelmi csoport > és az eszközök menüszalagján kattintson **eltávolítása**.

  Válassza ki a védelmi csoport aktiválja a **eltávolítása** gombra az eszközök menüszalagján. A példában a tag van **dummyvm9**. Válasszon ki több tagot a védelmi csoport, tagjaira kattintás közben tartsa lenyomva a Ctrl billentyűt.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    A **védelem kikapcsolása** párbeszédpanel nyílik meg.
2. Az a **védelem kikapcsolása** párbeszédablakban válassza **védett adatok törlése**, és kattintson a **védelem kikapcsolása**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Törli a tárolót, törölje a jelet, vagy a tároló védett adatok törlése. Attól függően, hogy a védelmi csoport adatainak és a helyreállítási pontok száma szükség lehet bárhol néhány másodperc több percig törli az adatokat. A **védelem kikapcsolása** párbeszédpanel állapotát jeleníti meg, ha a feladat befejeződött.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. A folyamat folytatásához az összes védelmi csoport összes tagjához.

    Távolítsa el az összes védett adatok és a védelmi csoportban.
4. Ha töröl minden tag a védelmi csoportból, váltson az Azure-portálon. Nyissa meg a tároló-irányítópultot, és győződjön meg arról, hogy nincsenek nem **biztonsági mentés elemek**, **biztonsági mentése a felügyeleti kiszolgálók**, vagy **replikált elemek**. A tároló eszköztáron kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-vault.png)

    Ha a tárolóhoz társított biztonságimásolat-felügyeleti kiszolgálók, akkor is, ha nincs elérhető adat a tárolóban a tároló nem törölhető. Ha a biztonságimásolat-felügyeleti kiszolgálók, a tárolóhoz rendelt törölt, de a felsorolt kiszolgálók a **Essentials** ablaktáblában tekintse meg [található a biztonsági mentés felügyeleti kiszolgálókat a tárolóhoz társított](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Győződjön meg arról, hogy szeretné-e a tárolót, kattintson a **Igen**.

    A tároló törlődik, és visszaadja a portál a **új** szolgáltatás menü.

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Egy üzemi kiszolgáló védelmére használt tároló törlése
A tároló egy üzemi kiszolgáló védelmére használt törlése előtt törölje, vagy szüntesse meg a kiszolgáló a tárolóból.

Az üzemi kiszolgáló a tárolóhoz rendelt törlése:

1. Az Azure portálon, a tároló irányítópult megnyitásához, és kattintson a **beállítások** > **biztonsági infrastruktúra** > **az üzemi kiszolgálók**.

    ![Nyissa meg az üzemi kiszolgálók panel](./media/backup-azure-delete-vault/delete-production-server.png)

    A **az üzemi kiszolgálók** panel nyílik meg, és megjeleníti a tárolóban lévő összes éles kiszolgálók.

    ![az üzemi kiszolgálók listája](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Az a **az üzemi kiszolgálók** panelen kattintson a jobb gombbal a kiszolgálón, és kattintson a **törlése**.

    ![az üzemi kiszolgáló törlése ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    A **törlése** panel nyílik meg.

    ![az üzemi kiszolgáló törlése ](./media/backup-azure-delete-vault/delete-blade.png)
3. Az a **törlése** panelen ellenőrizze a kiszolgáló nevét, és kattintson a **törlése**. Adjon megfelelő nevet a kiszolgáló aktiválása a **törlése** gombra.

    A tároló törlése után kap egy üzenetet, amely meghatározza, hogy a tároló törölve lett. Törlését követően a tárolóban található összes kiszolgáló ugorjunk vissza a Essentials ablaktáblán, a tároló irányítópultjának.
4. A tároló irányítópult, győződjön meg arról, nincsenek nem **biztonsági mentés elemek**, **biztonsági mentése a felügyeleti kiszolgálók**, vagy **replikált elemek**. A tároló eszköztáron kattintson **törlése**.
5. Győződjön meg arról, hogy szeretné-e a tárolót, kattintson a **Igen**.

    A tároló törlődik, és visszaadja a portál a **új** szolgáltatás menü.

## <a name="delete-a-backup-vault-in-classic-portal"></a>A klasszikus portálon biztonsági mentési tároló törlése
Az alábbi utasítások alapján vannak a biztonsági másolatok tárolóját a klasszikus portálon törléséhez. A mentési tároló törlése előtt törölnie kell a helyreállítási pontok, vagy a biztonsági másolatba mentett elemek, majd távolítsa el a regisztrált kiszolgálókat. A regisztrált kiszolgálókat a következők: a Windows Server, a munkaállomás vagy a virtuális gépek lett regisztrálva.

1. Nyissa meg a [klasszikus portál](https://manage.windowsazure.com).

2. Mentési tárolók listában jelölje ki a törölni kívánt tárolót.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)

    A tároló irányítópult megnyitása. Tekintse meg a tárolóhoz rendelt Windows-kiszolgálók és/vagy Azure virtuális gépek száma. Emellett tekintse meg a teljes Azure-ban felhasznált tárterület. Állítsa le az összes biztonsági mentési feladat, és a tároló törlése előtt törölje annak összes adatot.

3. Kattintson a **védett elemek** fülre, majd **védelem kikapcsolása**

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)

    A **állítsa le a védelmet a tároló"** párbeszédpanel jelenik meg.
4. Az a **állítsa le a védelmet a tároló"** párbeszédpanelen a jelölőnégyzet **Delete tartozó biztonsági mentési adatok** kattintson ![pipa](./media/backup-azure-delete-vault/checkmark.png). <br/>
    Szükség esetén válassza ki a védelem leállítása okát, és adja meg a megjegyzéseit.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)

    Ha a tárolóban lévő elemeket töröl, a tároló üres lesz.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. Lévő lapot, kattintson a **regisztrált elemek**. A **típus** legördülő menüben válassza ki a tárolóhoz társított kiszolgáló típusú teszi. A típus a Windows Server vagy Azure virtuális gép lehet. A következő példában válassza ki a virtuális gépet a tárolóhoz társított, és kattintson a **Unregister**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/classic-portal-unregister.png)

  Törli a regisztrációt a Windows Server rendszerben szeretné a **típus** legördülő menüben válassza **Windows Server**, kattintson ![pipa](./media/backup-azure-delete-vault/checkmark.png) frissíti a képernyőt, majd Kattintson a **törlése**. <br/>

  ![Válassza ki a Windows Server](./media/backup-azure-delete-vault/select-windows-server.png)

6. Lévő lapot, kattintson a **irányítópult** lap megnyitásához. Ellenőrizze, hogy nincsenek regisztrált kiszolgálókat vagy a felhőben lévő védett Azure virtuális gépeken. Azt is ellenőrizze, nincs megőrzés adat. Kattintson a **törlése** törli a tárolót.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)

    Megnyitja a biztonsági mentés törlése tároló visszaigazoló képernyő. Jelöljön ki egy lehetőséget miért törölni a tárolót, és kattintson ![pipa](./media/backup-azure-delete-vault/checkmark.png). <br/>

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)

    A tároló törlődik, és Ön visszatér a klasszikus portál irányítópultjára.

### <a name="find-the-backup-management-servers-registered-to-the-vault"></a>A biztonsági mentés felügyeleti kiszolgálókat a tárolóhoz társított keresése
Ha több kiszolgálót a tárolóban regisztrált, számukra megjegyezhető nehézkes lehet. Tekintse meg a tárolóhoz társított kiszolgálók, és törölje őket:

1. A tároló irányítópult megnyitásához.
2. Az a **Essentials** ablaktáblán kattintson a **beállítások** , hogy a panel megnyitásához.

    ![Nyissa meg a beállítások panel](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Az a **beállítások panel**, kattintson a **biztonsági infrastruktúra**.
4. Az a **biztonsági infrastruktúra** panelen kattintson a **biztonságimásolat-felügyeleti kiszolgálók**. Ekkor megnyílik a biztonságimásolat-felügyeleti kiszolgálók panel.

    ![biztonságimásolat-felügyeleti kiszolgálók listája](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Kiszolgáló törlése a listából, kattintson a jobb gombbal a kiszolgáló nevét, és kattintson a **törlése**.
    A **törlése** panel nyílik meg.
6. Az a **törlése** panelen adja meg annak a kiszolgálónak a nevét. Ha hosszú a neve, másolja, és illessze be a biztonságimásolat-felügyeleti kiszolgálók listája. Kattintson a **törlése**.  
