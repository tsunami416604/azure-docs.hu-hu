---
title: Az Azure verem VMs fájlok biztonsági mentése
description: Azure Backup használatával biztonsági mentése és helyreállítása Azure verem fájlokat és alkalmazásokat az Azure-verem környezethez.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 2fb3bad56de781dd81d4c5f82b734c9420c75dee
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751704"
---
# <a name="back-up-files-on-azure-stack"></a>Azure-veremben fájlok biztonsági mentése
Azure Backup segítségével védeni (vagy készítsen biztonsági másolatot) fájlok és alkalmazások Azure veremben. Biztonsági másolatot készíteni a fájlokhoz és alkalmazásokhoz, telepítse a Microsoft Azure Backup Server Azure verem futó virtuális gépként. Az azonos virtuális hálózatban lévő bármely Azure verem kiszolgálón lévő fájlok védelme biztosítható. Egyszer telepítette az Azure Backup Server, adja hozzá az Azure-lemezeket a helyi tárterület érhető el, a rövid távú biztonsági másolatok növelése érdekében. Az Azure Backup Server hosszú távú megőrzési Azure tárolást használ.

> [!NOTE]
> Bár az Azure Backup Server és System Center Data Protection Manager (DPM) hasonló a DPM nem támogatott Azure verem való használatra.
>

Ez a cikk nem foglalkozik az Azure Backup Server telepítése a verem Azure környezetben. Azure Backup Server telepítése Azure veremben, tekintse meg a cikket, [Azure Backup Server telepítése](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Fájlok és mappák biztonsági mentése az Azure verem virtuális gépeken az Azure-bA

Az Azure Backup Server számára a fájlok védelméhez az Azure-verem virtuális gépeken futó virtuális gépek megadásához nyissa meg az Azure Backup Server konzol. A konzol fogja használni, a védelmi csoportok konfigurálásához és a virtuális gépek az adatok védelme érdekében.

1. Kattintson az Azure Backup Server konzolon **védelmi** az eszköztáron kattintson **új** megnyitásához a **új védelmi csoport létrehozása** varázsló.

   ![Konfigurálja a védelmet az Azure Backup Server konzol](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Nyissa meg a varázsló néhány másodpercet vehet igénybe. Amikor megnyílik a varázsló, kattintson a **következő** ahhoz, hogy értékről a **védelmi csoport típusának kiválasztása** képernyő.

   ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Az a **védelmi csoport típusának kiválasztása** képernyőn, válassza a **kiszolgálók** kattintson **következő**.

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    A **csoporttagok kiválasztása** képernyőn megnyílik. 

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Az a **csoporttagok kiválasztása** kattintson **+** bontsa ki a lista is. Az összes, amelyet védeni kíván jelölje be a jelölőnégyzetet. Minden elem kijelölése után kattintson **következő**.

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft azt javasolja, és minden adat, amely a védelmi házirend fogja osztani egy védelmi csoportba. Hamarosan tervezésével és telepítésével a védelmi csoportok, tekintse meg a System Center DPM cikk tájékoztatást [telepíteni a védelmi csoportok](https://docs.microsoft.com/en-us/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Az a **adatvédelmi módszer kiválasztása** írja be a védelmi csoport nevét. Jelölje be a **szeretném, hogy rövid távú védelem használata:** és **online védelmet szeretnék**. Kattintson a **Tovább** gombra.

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Válassza ki a **online védelmet szeretnék**, először ki kell választania **szeretném, hogy rövid távú védelem használata:** lemez. Az Azure Backup Server nem szalag alapú védelmet biztosítani, így lemezre rövid távú védelemhez csak choice.

5. Az a **rövid távú célok megadása** képernyőn, válassza ki, mennyi ideig szeretné megőrizni a helyreállítási pontok mentve lemezre, és ha a növekményes biztonsági mentés. Kattintson a **Tovább** gombra.

    > [!IMPORTANT]
    > Meg kell **nem** az Azure Backup Server-csatolású lemezeket (biztonsági mentés) műveleti helyreállítási adatok megőrzéséhez a több mint öt napig.
    >

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Helyett időközeinek megadása növekményes biztonsági mentések, futtatását egy expressz teljes biztonsági mentés előtt minden egyes ütemezett helyreállítási pontok, kattintson a **csak helyreállítási pont létrehozása előtt**. Ha alkalmazások munkaterhelését védi, az Azure Backup Server hoz helyreállítási pontok a szinkronizálás gyakorisága (feltéve, hogy az alkalmazás támogatja a növekményes biztonsági mentések). Ha az alkalmazás nem támogatja a növekményes biztonsági mentések, Azure Backup Server fut-e egy expressz teljes biztonsági mentés.

    A **helyreállítási pontok fájl**, adja meg, mikor kell létrehozni a helyreállítási pontok. Kattintson a **módosítás** időpontjait és napjait a héten helyreállítási pontok létrehozásának beállításához.

6. Az a **tekintse át a lemezkiosztást** képernyőn, tekintse át a tárolási tárolókészletben lefoglalt lemezterületet a védelmi csoporthoz.

    **Teljes adatok mérete** van készítsen biztonsági másolatot kívánt adatok méretétől és **létesítendő lemezterület** Azure biztonsági mentési kiszolgálón van az ajánlott hely a védelmi csoportot. Az Azure Backup Server úgy dönt, hogy az épp ezért tökéletes választás a biztonsági mentési köteten, a beállítások alapján. Azonban módosíthatja a biztonsági mentési kötet lehetőségei a lemez foglalás részletei. A munkaterhelések esetén válassza ki a kívánt tárolási legördülő menüből. A módosítások módosítsa a teljes tárterület és a szabad tárhely a rendelkezésre álló lemezterület ablaktáblán. Underprovisioned-e hely tárolókapacitást Azure Backup Server javasol ad hozzá a kötetet a biztonsági mentések zökkenőmentesen jövőbeni folytatásához.

7. A **replika-létrehozási módszer kiválasztása**, válassza ki, hogyan szeretné kezelni a teljes kezdeti adatreplikálás. Ha úgy dönt, hogy a hálózaton keresztül replikálja, Azure azt javasolja, hogy úgy dönt, hogy a csúcsidőn kívüli időpontot. Nagy mennyiségű adatok vagy kevésbé optimális hálózati állapotok esetén érdemes megfontolni az adatok cserélhető adathordozó segítségével replikálni.

8. A **konzisztencia-ellenőrzési beállítások kiválasztása**, válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzést. Engedélyezze a konzisztencia-ellenőrzések csak akkor, ha a adatok replikáció inkonzisztenssé válik, vagy ütemezés szerint futtatni. Ha nem szeretné konfigurálni az automatikus konzisztencia-ellenőrzését, az bármikor lefuttathat egy manuális ellenőrzést:
    * Az a **védelmi** területén az Azure biztonsági mentés konzolját, kattintson a jobb gombbal a védelmi csoport, és válasszon **konzisztencia-ellenőrzés**.

9. Ha úgy dönt, hogy a biztonsági Azure, a **online védelem adatainak megadása** lapon ellenőrizze, hogy a munkaterhelések biztonsági mentése az Azure-bA szeretne van kiválasztva.

10. A **online biztonsági mentés ütemezésének megadása**, adja meg, amikor megtörténik a növekményes biztonsági mentést az Azure-bA. 

    Biztonsági mentések minden nap vagy hét/hónap vagy év és az időpontot vagy dátumot, amelynél futtatni kell futtatását is ütemezheti. Biztonsági mentés akkor fordulhat elő, legfeljebb naponta kétszer. Minden alkalommal, amikor egy biztonsági mentési feladat futhasson, adatok helyreállítási pont készült Azure-ban az Azure Backup Server lemezen tárolt adatok biztonsági másolatai példányát.

11. A **online megőrzési szabály megadása**, adja meg, hogyan megőrzi a helyreállítási pontok a napi vagy heti vagy havi vagy éves biztonsági mentést készített az Azure-ban.

12. A **válassza ki az online replikációs**, adja meg az adatok teljes kezdeti replikációjához módját. 

13. A **összegzés**, tekintse át a beállításokat. Amikor rákattint **csoport létrehozása**, a kezdeti adatreplikálás következik be. Ha az adatok replikáció befejezése után a a **állapot** lapon, a védelmi csoport állapota látható **OK**. A kezdeti biztonsági mentési feladat védelmicsoport a csoport beállításait.

Kérdések megválaszolásával igénylő: hogyan akkor bontsa lemezes tárolás az Azure-verem rövid távú lemezes tárhelyre. Mik azok a útmutatást ki, amely ismerteti, hogy a rövid távú lemezes tárhelyre van szükség?

## <a name="recover-file-data"></a>Fájladatok helyreállítása

Azure Backup Server konzollal helyreállítani az adatokat a virtuális géphez.

1. Az Azure Backup Server konzolon a navigációs sávon kattintson **helyreállítási** , és keresse meg a helyreállítani kívánt adatokat. Az eredmények ablaktáblájában jelölje ki az adatokat.

2. A helyreállítási pontokat tartalmazó naptárban látható, félkövérrel szedett dátumokra jelző helyreállítási pont nem érhető el. Válassza ki a helyreállítani egy helyreállítási pont dátumát.

3. Az a **helyreállítható elem** ablaktáblán válassza ki a helyreállítani kívánt elemet.

4. Az a **műveletek** ablaktáblán kattintson a **helyreállítása** a helyreállítási varázsló megnyitásához.

5. Helyreállíthatók az adatok az alábbiak szerint:

    * **Visszaállítás az eredeti helyre** – Ha az ügyfélszámítógép VPN-kapcsolaton keresztül csatlakozik, ez a beállítás nem működik. Ehelyett használja egy másik helyre, és másolja adatok arról a helyről.
    * **Visszaállítás másik helyre**

6. Adja meg a helyreállítási beállításokat:

    * A **létező verzió helyreállítása esetén**, jelölje be **készítsen másolatot**, **kihagyása**, vagy **felülírása**. Felülírása csak akkor használható helyreállítása az eredeti helyre.
    * A **biztonság visszaállítása**, válassza a **beállításainak a célszámítógépen alkalmazása** vagy **a helyreállítási ponthoz tartozó verzió biztonsági beállításainak alkalmazása**.
    * A **sávszélesség-szabályozás**, kattintson a **módosítás** sávszélesség-szabályozás engedélyezése.
    * **Értesítési** kattintson **e-mail küldése a helyreállítás befejezéséről**, és adja meg a címzetteket, akiknek a értesítést fog kapni. Az e-mail címeket vesszővel való elválasztása.
    * Kattintson a kiválasztás után **tovább**

7. Tekintse át a helyreállítási beállításokat, majd kattintson **helyreállítása**. 

    > [!Note] 
    > Amíg a helyreállítási feladat van folyamatban, a kijelölt helyreállítási elemeket az összes szinkronizálási feladatot törölve lesznek.
    >

Modern biztonsági mentési tároló (MB) használata, a fájlkiszolgáló a végfelhasználói helyreállítás (EUR) nem támogatott. Fájl Server EUR rendelkezik függőség a kötet árnyékmásolata szolgáltatás (VSS), amely nem használja a Modern Backup-tárhelyre. Ha engedélyezve van a EUR, tegye a következőket adatok helyreállításához:

1. Keresse meg a védett fájlokat, és kattintson a jobb gombbal a fájl nevét, és válassza ki **tulajdonságok**.

2. Az a **tulajdonságok** menüben kattintson a **korábbi verziók** , és válassza ki a helyreállítani kívánt verziót.

## <a name="view-azure-backup-server-with-a-vault"></a>Nézet Azure biztonsági mentés kiszolgálót a tárolóban
Azure portál Azure Backup Server entitások megtekintéséhez kövesse az alábbi lépéseket:
1. Nyissa meg a Recovery Services-tároló.
2. Kattintson a biztonsági mentési infrastruktúra.
3. Biztonságimásolat-felügyeleti kiszolgálók megtekintése.

## <a name="see-also"></a>Lásd még
Információ az Azure Backup Server használatával egyéb munkaterhelések védelme érdekében tekintse meg a következő cikkeket:
- [Készítsen biztonsági másolatot a SharePoint-farm.](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Készítsen biztonsági másolatot az SQL server](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sql-azure-stack)
