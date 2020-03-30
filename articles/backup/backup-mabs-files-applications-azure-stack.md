---
title: Fájlok biztonsági és biztonsági másolatot az Azure Stack virtuális gépek
description: Az Azure Backup segítségével biztonsági másolatot készíthet az Azure Stack-fájlokról és -alkalmazásokról az Azure Stack-környezetbe.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: 2bcdf7f720708db1487d7d5cdaee41dc93c05728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172323"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Fájlok és alkalmazások biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és

Az Azure Backup segítségével megvédheti (vagy biztonsági másolatot készíthet) az Azure Stackben lévő fájlok és alkalmazások védelmére (vagy biztonsági mentéséhez). Fájlok és alkalmazások biztonsági mentéséhez telepítse a Microsoft Azure Backup Server-t az Azure Stacken futó virtuális gépként. A fájlokat az azure stack-kiszolgálókon ugyanazon a virtuális hálózaton. Miután telepítette az Azure Backup Servert, adja hozzá az Azure-lemezeket a rövid távú biztonsági mentési adatok számára rendelkezésre álló helyi tárterület növeléséhez. Az Azure Backup Server az Azure storage-t használja a hosszú távú megőrzéshez.

> [!NOTE]
> Bár az Azure Backup Server és a System Center Data Protection Manager (DPM) hasonló, A DPM nem támogatott az Azure Stack használata.
>

Ez a cikk nem vonatkozik az Azure Backup Server azure stack környezetben történő telepítésére. Az Azure Backup Server azure stackre történő telepítéséhez olvassa el az Azure Backup Server telepítése című [cikket.](backup-mabs-install-azure-stack.md)

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Fájlok és mappák biztonsági és biztonsági és biztonsági másolatot az Azure-beli virtuális gépeken

Ha az Azure Backup Servert az Azure Stack virtuális gépeiben lévő fájlok védelmére szeretné konfigurálni, nyissa meg az Azure Backup Server konzolt. A konzol segítségével konfigurálhatja a védelmi csoportokat, és megvédheti a virtuális gépeken lévő adatokat.

1. Az Azure Backup Server konzolon kattintson a **Védelem** elemre, majd az eszköztáron kattintson az **Új** gombra az **Új védelmi csoport létrehozása** varázsló megnyitásához.

   ![Védelem konfigurálása az Azure Backup Server konzolon](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    A varázsló megnyitása eltarthat néhány másodpercig. A varázsló megnyitása után kattintson a **Tovább** gombra a **Védelem csoporttípus kiválasztása** képernyőre való ugráshoz.

   ![Megnyílik az Új védelem csoport varázslója](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. A **Védelem csoporttípus kiválasztása** képernyőn válassza a **Kiszolgálók** lehetőséget, és kattintson a **Tovább**gombra.

    ![Megnyílik az Új védelem csoport varázslója](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Megnyílik **a Csoporttagok kiválasztása** képernyő.

    ![Megnyílik az Új védelem csoport varázslója](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. A **Csoporttagok kiválasztása** képernyőn **+** kattintson ide az alelemek listájának kibontásához. Jelölje be a jelölőnégyzetet. Miután az összes elemet kiválasztotta, kattintson a **Tovább**gombra.

    ![Megnyílik az Új védelem csoport varázslója](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft azt javasolja, hogy a védelmi szabályzatot megosztó összes adatot helyezze egyetlen védelmi csoportba. A védelmi csoportok tervezésével és telepítésével kapcsolatos teljes körű információkért tekintse meg a System Center DPM [Telepítési védelmi csoportok című cikkében.](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801)

4. Az **Adatvédelmi módszer kiválasztása** képernyőn adja meg a védelmi csoport nevét. Jelölje be a **rövid távú védelmet a következővel:** és **online védelmet szeretnék.** Kattintson a **Tovább** gombra.

    ![Megnyílik az Új védelem csoport varázslója](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    A **"Szeretnék" online védelmet**választva először a Rövid távú védelmet a következő vel kell **kiválasztania:** Lemez. Az Azure Backup Server nem védi a szalagot, így a lemez az egyetlen választás a rövid távú védelem.

5. A **Rövid távú célok megadása** képernyőn adja meg, hogy mennyi ideig őrizze meg a lemezre mentett helyreállítási pontokat, és mikor mentse a növekményes biztonsági másolatokat. Kattintson a **Tovább** gombra.

    > [!IMPORTANT]
    > Az Azure Backup Server hez csatlakoztatott lemezeken öt napnál tovább **nem** őrizheti meg a működési helyreállítási (biztonsági mentési) adatokat.
    >

    ![Megnyílik az Új védelem csoport varázslója](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Ahelyett, hogy a növekményes biztonsági mentések időközét választaná, ha az egyes ütemezett helyreállítási pont előtt egy expressz teljes biztonsági mentést szeretne futtatni, kattintson **a Közvetlenül a helyreállítási pont előtt**elemre. Ha az alkalmazás-munkaterhelések védelme, az Azure Backup Server helyreállítási pontokat hoz létre a szinkronizálásgyakoriságütemezés szerint (feltéve, hogy az alkalmazás támogatja a növekményes biztonsági mentéseket). Ha az alkalmazás nem támogatja a növekményes biztonsági mentéseket, az Azure Backup Server egy expressz teljes biztonsági mentést futtat.

    **Fájl helyreállítási pontok**esetén adja meg, hogy mikor hozzon létre helyreállítási pontokat. Kattintson a **Módosítás gombra** a hét helyreállítási pontok létrehozásának időpontjának és napjának beállításához.

6. A **Lemezfoglalás áttekintése** képernyőn tekintse át a védelmi csoport számára lefoglalt tárterület-területet.

    **A teljes adatméret** a biztonsági másolatot készíteni kívánt adatok mérete, és az Azure Backup Server en **kiépítendő lemezterület** az ajánlott hely a védelmi csoport számára. Az Azure Backup Server a beállítások alapján kiválasztja az ideális biztonsági mentési kötetet. Lehetőség van azonban arra, hogy a Disk allocation details (Lemezfoglalás részletei) panelen módosítsa a biztonsági mentési kötetek választási lehetőségeit. A munkaterhelésekhez válassza ki a legördülő menüből az előnyben részesített tárhelyet. A módosítások megváltoztatják az Összes tárhely és a Szabad tárterület értékeit az Elérhető lemezterület ablaktáblán. Alulkiosztott terület az Azure Backup Server által a kötethez adandó tárterület mennyisége, amely a jövőben zökkenőmentesen folytatja a biztonsági mentéseket.

7. A **Replikalétrehozási módszer kiválasztása csoportban**válassza ki, hogyan szeretné kezelni a kezdeti teljes adatreplikációt. Ha úgy dönt, hogy replikálja a hálózaton keresztül, az Azure azt javasolja, hogy válasszon egy csúcsidőn kívüli idő. Nagy mennyiségű adat vagy az optimálisnál kisebb hálózati feltételek esetén fontolja meg az adatok cserélhető adathordozóhasználatával történő replikálását.

8. A **Konzisztencia-ellenőrzési beállítások** lépésben válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Engedélyezze, hogy a konzisztencia-ellenőrzések csak akkor fussanak, ha az adatreplikáció inkonzisztenssé válik, vagy ütemezés szerint. Ha nem szeretné konfigurálni az automatikus konzisztencia-ellenőrzést, bármikor futtasson manuális ellenőrzést:
    * Az Azure Backup Server konzol **Védelmi** területén kattintson a jobb gombbal a védelmi csoportra, és válassza a **Konzisztencia-ellenőrzés végrehajtása parancsot.**

9. Ha úgy dönt, hogy biztonsági másolatot készít az Azure-ba, az **Online védelmi adatok megadása** lapon győződjön meg arról, hogy az Azure-ra készíteni kívánt számítási feladatok ki vannak jelölve.

10. Az **online biztonsági mentés ütemezésének megadása**területen adja meg, hogy mikor történjen az Azure-ba történő növekményes biztonsági mentések.

    Napi, heti, havi és éves rendszerességet is beállíthat, és megadhatja a futtatás kívánt dátumát és időpontját is. Naponta legfeljebb kétszer történhet biztonsági mentés. Minden alkalommal, amikor egy biztonsági mentési feladat fut, egy adat-helyreállítási pont jön létre az Azure-ban az Azure Backup Server lemezen tárolt biztonsági mentési adatok másolata.

11. Az **online adatmegőrzési szabályzat megadása**területen adja meg, hogy a napi/heti/havi/éves biztonsági mentések ből létrehozott helyreállítási pontok hogyan őrződnek meg az Azure-ban.

12. Az **Online replikáció kiválasztása csoportban**adja meg, hogyan történjen az adatok kezdeti teljes replikációja.

13. Az **Összegzés lapon**tekintse át a beállításokat. Ha a **Csoport létrehozása gombra**kattint, a kezdeti adatreplikáció történik. Amikor az adatreplikáció befejeződik, az **Állapot** lapon a védelmi csoport állapota **rendben jelenik meg.** A kezdeti biztonsági mentési feladat a védelmi csoport beállításaival összhangban történik.

## <a name="recover-file-data"></a>Fájladatok helyreállítása

Az Azure Backup Server konzol segítségével helyreállíthatja az adatokat a virtuális gépen.

1. Az Azure Backup Server konzolon a navigációs sávon kattintson a **Helyreállítás** gombra, és keresse meg a helyreállítani kívánt adatokat. Az eredménypanelen jelölje ki az adatokat.

2. A helyreállítási pontok szakasznaptárában a félkövérrel szedett dátumok jelzik, hogy a helyreállítási pontok rendelkezésre állnak. Válassza ki a helyreállítandó dátumot.

3. A **Helyreállítható elem** ablaktáblában jelölje ki a helyreállítani kívánt elemet.

4. Kattintson a **Műveletek** panel **Helyreállítás** elemére a helyreállítási varázsló megnyitásához.

5. Az adatokat a következő módon állíthatja helyre:

    * **Helyreállítás az eredeti helyre** – Ha az ügyfélszámítógép VPN-en keresztül csatlakozik, ez a beállítás nem működik. Ehelyett használjon egy másik helyet, majd másolja az adatokat arról a helyről.
    * **Helyreállítás másik helyre**

6. Adja meg a helyreállítási beállításokat:

    * **A Meglévő verzió-helyreállítási viselkedés**esetén válassza a Másolat **létrehozása**, **Kihagyás**vagy **Felülírás**lehetőséget. A felülírás csak akkor érhető el, ha az eredeti helyre való helyreállítást.
    * A **Biztonság visszaállítása párbeszédpanelen**válassza **a Célszámítógép beállításainak alkalmazása** vagy a helyreállítási pont **verzióbiztonsági beállításainak alkalmazása lehetőséget.**
    * A **hálózati sávszélesség-használat szabályozásához**kattintson a **Módosítás gombra** a hálózati sávszélesség-használat szabályozásának engedélyezéséhez.
    * **Értesítés** Kattintson **az E-mail küldése a helyreállítás befejezésekor gombra,** és adja meg az értesítést fogadó címzetteket. Az e-mail-címeket vesszővel válassza el egymástól.
    * A kijelölés után kattintson a **Tovább** gombra.

7. Nézze át a helyreállítási beállításokat, és kattintson a **Helyreállítás** gombra.

    >[!Note]
    >Amíg a helyreállítási feladat folyamatban van, a kijelölt helyreállítási elemek összes szinkronizálási feladata megszakad.

Modern biztonsági mentési tároló (MBS) használata esetén a Fájlkiszolgáló végfelhasználói helyreállítása (EUR) nem támogatott. A File Server EUR függőséget biztosít a Kötet árnyékmásolata szolgáltatástól (VSS), amelyet a Modern Backup Storage nem használ. Ha az EUR engedélyezve van, az adatok helyreállításához kövesse az alábbi lépéseket:

1. Keresse meg a védett fájlokat, és kattintson a jobb gombbal a fájl nevére, és válassza **a Tulajdonságok parancsot.**

2. Kattintson a **Tulajdonságok** menü **Előző verziók parancsára,** és válassza ki a helyreállítani kívánt verziót.

## <a name="view-azure-backup-server-with-a-vault"></a>Az Azure Backup Server megtekintése tárolóval

Az Azure Backup Server entitásainak megtekintéséhez az Azure Portalon az alábbi lépéseket hajthatja végre:

1. Nyissa meg a helyreállítási szolgáltatások tárolót.
2. Kattintson a Biztonsági másolat infrastruktúra elemre.
3. A biztonságimásolat-kezelő kiszolgálók megtekintése.

## <a name="next-steps"></a>További lépések

Az Azure Backup Server más számítási feladatok védelmére való használatáról az alábbi cikkekben talál további információt:

* [SharePoint-farm biztonsági és biztonsági másolatot](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [SQL Server-kiszolgálók biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
