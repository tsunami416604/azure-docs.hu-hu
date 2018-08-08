---
title: Az Azure Stack virtuális gépeiről fájlok biztonsági mentése
description: Az Azure Backup használatával biztonsági mentése és helyreállítása az Azure Stack-fájlok és alkalmazások számára az Azure Stack környezettel.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: a9eca3c3aeaa8d9d0d3d7728f8aebd63543cb604
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617115"
---
# <a name="back-up-files-on-azure-stack"></a>Az Azure Stacken fájlok biztonsági mentése
Az Azure Backup segítségével védelme (vagy felhatolásra) fájlok és alkalmazások az Azure Stack. Fájlok és alkalmazások biztonsági mentése, telepítse a Microsoft Azure Backup Server az Azure Stacken futó virtuális gépként. Bármely Azure Stack-kiszolgálón az azonos virtuális hálózatba a fájlok védelmét. Miután telepítette az Azure Backup Server, a rövid távú biztonsági mentési adatok érhető el helyi tárhely bővítése érdekében az Azure lemezeket ad hozzá. Az Azure Backup Server az Azure storage hosszú távú megőrzésének használ.

> [!NOTE]
> Bár az Azure Backup Server és System Center Data Protection Manager (DPM) hasonló a DPM nem támogatott az Azure Stackkel való használathoz.
>

Ez a cikk nem tárgyalja az Azure Stack-környezet az Azure Backup Server telepítése. Az Azure Backup Server telepítése az Azure Stacken: a cikk [az Azure Backup Server telepítése](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Fájlok és mappák biztonsági mentése az Azure Stack-beli virtuális gépeken az Azure-bA

Az Azure Backup Server számára a fájlok védelméhez az Azure Stack virtuális gépek konfigurálásához nyissa meg az Azure Backup Server konzolon. A konzol fogja használni, a védelmi csoportok konfigurálásához és a virtuális gépek az adatok védelme érdekében.

1. Az Azure Backup Server konzolon kattintson a **védelmi** az eszköztáron kattintson **új** megnyitásához a **új védelmi csoport létrehozása** varázsló.

   ![Az Azure Backup Server konzolon a védelem konfigurálása](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Igénybe vehet néhány másodpercet, amíg a varázsló megnyitásához. Amikor megnyílik a varázsló, kattintson a **tovább** azt mutatja be, hogy a **védelmi csoport típusának kiválasztása** képernyő.

   ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Az a **védelmi csoport típusának kiválasztása** képernyő, válassza a **kiszolgálók** kattintson **tovább**.

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    A **csoporttagok kiválasztása** képernyőn megnyílik. 

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Az a **csoporttagok kiválasztása** kattintson **+** bontsa ki a listát is. A védeni kívánt összes elemet jelölje be a jelölőnégyzetet. Összes elem kiválasztása után kattintson a **tovább**.

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft azt javasolja, és a egy alkalmazásvédelmi szabályzatot, fog osztani egy védelmi csoport összes adatot. Hamarosan tervezésével és telepítésével a védelmi csoportok, tekintse meg a System Center DPM cikket, teljes körű tájékoztatásért [védelmi csoportok üzembe helyezése](https://docs.microsoft.com/en-us/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Az a **adatvédelmi módszer kiválasztása** írja be a védelmi csoport nevét. Jelölje be a **használatával rövid távú védelmet szeretnék:** és **online védelmet szeretnék**. Kattintson a **Tovább** gombra.

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Válassza ki a **online védelmet szeretnék**, először ki kell választania **használatával rövid távú védelmet szeretnék:** lemez. Az Azure Backup Server nem a szalagra történő védelme, hogy a lemez rövid távú védelem esetén az egyetlen lehetősége.

5. Az a **rövid távú célok megadása** képernyőn, mennyi ideig kívánja őrizni a helyreállítási pontokat, lemez és a növekményes biztonsági mentés a mentett. Kattintson a **Tovább** gombra.

    > [!IMPORTANT]
    > Érdemes **nem** megőrzése üzemképességet (biztonsági mentés) az Azure Backup Server-csatolású lemezeket öt napnál hosszabb.
    >

    ![Új védelmi csoport varázsló megnyitása](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Helyett időközeinek megadása a növekményes biztonsági másolat, az expressz teljes biztonsági mentés előtt minden egyes ütemezett helyreállítási pontot, kattintson a **közvetlenül egy helyreállítási pont előtt**. Ha alkalmazások munkaterhelését védi, az Azure Backup Server a (feltéve, hogy az alkalmazás támogatja a növekményes biztonsági mentést) a szinkronizálási gyakoriság ütemezés szerint helyreállítási pontokat hoz létre. Ha az alkalmazás nem támogatja a növekményes biztonsági másolatok, az Azure Backup Server fut-e egy expressz teljes biztonsági mentés.

    A **fájlt a helyreállítási pontok**, adja meg a helyreállítási pontok létrehozásának. Kattintson a **módosítás** időpontok és mikor jöjjenek létre helyreállítási pontokat a hét napjait.

6. Az a **tekintse át a lemezkiosztást** lapon tekintse át a storage tárolókészletben lefoglalt lemezterületet a védelmi csoport számára.

    **Adatok mérete összesen** van a készíteni kívánt adatok méretétől és **kiépítendő terület lemez** az Azure Backup Server nem az ajánlott hely a védelmi csoport számára. Az Azure Backup Server beállítások alapján ideális biztonsági mentési kötetet választja ki. Azonban a biztonsági mentési kötetek választási lehetőségeit a lemez foglalás részletei szerkesztheti. A munkaterhelésekhez válassza ki a legördülő menüből az előnyben részesített tárhelyet. A módosítások megváltoztatják az értékeket a teljes tárolási és szabad tárterület a rendelkezésre álló lemezterület ablaktáblán. Underprovisioned hely az Azure Backup Server biztonsági mentések problémamentesen későbbi folytatásához a kötethez hozzáadni javasol a tárhely.

7. A **replika-létrehozási módszer kiválasztásának**válassza ki, hogyan szeretné kezelni a kezdeti teljes adatreplikációt. Ha úgy dönt, hogy a hálózaton keresztül replikálja, az Azure javasolja úgy dönt, hogy egy csúcsidőn kívüli időpont. Nagy mennyiségű adat- vagy kevésbé optimális hálózati állapotok fontolja meg a cserélhető adathordozó használatával az adatok replikálásához.

8. A **konzisztencia-ellenőrzési beállítások kiválasztása**válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Engedélyezze a konzisztencia-ellenőrzések csak akkor, amikor adatokat replikáció inkonzisztenssé válik, vagy egy ütemezés szerint futtatni. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, az bármikor futtathat manuális ellenőrzést:
    * Az a **védelmi** terület az Azure Backup Server-konzolon kattintson a jobb gombbal a védelmi csoport, és válasszon **konzisztencia-ellenőrzés végrehajtása**.

9. Ha úgy dönt, hogy biztonsági mentést az Azure-bA a **online védelem adatainak megadása** lapon ellenőrizze, hogy a számítási feladatok biztonsági mentése az Azure-bA szeretne ki van jelölve.

10. A **online biztonsági mentés ütemezésének megadása**, adja meg, amikor megtörténik a növekményes biztonsági mentést az Azure-bA. 

    Minden nap heti/havi és éves és az időpontot vagy dátumot, amikor futtatni kell futtatni a biztonsági mentéseket ütemezhet. Naponta legfeljebb kétszer történhet biztonsági mentés. Minden alkalommal, amikor egy biztonsági mentési feladat fut, egy data helyreállítási pont létrehozása az Azure-ban az Azure Backup Server lemezen tárolt biztonsági másolat adatok másolatából.

11. A **online megőrzési szabály megadása**, adja meg, hogy a a napi/heti/havi/évi biztonsági mentésekből létrehozott helyreállítási pontokat meddig őrizze meg az Azure-ban.

12. A **online replikáció kiválasztása**, adja meg, hogyan történik a adatok teljes kezdeti replikálását. 

13. A **összefoglalás**, tekintse át a beállításokat. Amikor rákattint **csoport létrehozása**, megtörténik a kezdeti adatok replikálása. Amikor az adatok replikációját befejezését követően a a **állapota** lapon az védelmi csoport állapota **OK**. A kezdeti biztonsági mentési feladat szerint lezajlik a védelmi csoport beállításait.

## <a name="recover-file-data"></a>Fájladatok helyreállítása

Az Azure Backup Server konzol használatával helyreállíthatja azokat a virtuális gép.

1. Az Azure Backup Server konzol navigációs sávján kattintson **helyreállítási** , és keresse meg a helyreállítani kívánt adatokat. Az eredménypanelen jelölje ki az adatokat.

2. A naptár, a helyreállítási pontok szakaszban a félkövérrel szedett dátumok azt jelzik, a helyreállítási pont érhető el. Válassza ki az helyreállításához.

3. Az a **helyreállítható elem** ablaktáblán válassza ki a helyreállítani kívánt elemet.

4. Az a **műveletek** ablaktáblán kattintson a **helyreállítása** a helyreállítási varázsló megnyitásához.

5. Helyreállíthatja az adatokat a következő:

    * **Visszaállítás az eredeti helyre** – Ha az ügyfélszámítógép VPN-kapcsolaton keresztül csatlakozik, ez a beállítás nem működik. Ehelyett használjon egy másik helyre, és másolja adatait erről a helyről.
    * **Visszaállítás másik helyre**

6. Adja meg a helyreállítási beállításokat:

    * A **létező verzió helyreállítása esetén**válassza **másolatot**, **kihagyása**, vagy **felülírás**. Írja felül csak akkor, ha az eredeti helyre történő helyreállítás érhető el.
    * A **biztonság visszaállítása**, válassza a **a célként megadott számítógép-beállítások alkalmazásához** vagy **a helyreállítási ponthoz tartozó verzió biztonsági beállításainak alkalmazása**.
    * A **sávszélesség-szabályozás**, kattintson a **módosítás** sávszélesség-szabályozás engedélyezése.
    * **Értesítési** kattintson **e-mail küldése a helyreállítás befejezéséről**, és adja meg a címzetteket, akik megkapják az értesítést. Külön az e-mail címeket vesszővel válassza el egymástól.
    * Kattintson a kiválasztás után **tovább**

7. Tekintse át a helyreállítási beállításokat, és kattintson a **helyreállítása**. 

    > [!Note] 
    > Amíg folyamatban van a helyreállítási feladat, a kijelölt helyreállítási elemeket az összes szinkronizálási feladatot megszakították.
    >

A Modern Backup Storage (MBP) használata, a fájlkiszolgáló végfelhasználói helyreállítása (EUR) nem támogatott. Fájlkiszolgáló végfelhasználói helyreállítása a kötet árnyékmásolata szolgáltatás (VSS), ami a Modern Backup Storage nem használ maga. Ha EUR engedélyezve van, használja az alábbi lépéseket helyreállítani az adatokat:

1. Keresse meg a védett fájlokat, és kattintson a jobb gombbal a fájl nevét, és válassza ki **tulajdonságok**.

2. Az a **tulajdonságok** menüben kattintson a **korábbi verziók** , és válassza ki a helyreállítani kívánt verziót.

## <a name="view-azure-backup-server-with-a-vault"></a>Megtekintése az Azure Backup Server-tárolóval
Az Azure Portalon az Azure Backup Server entitások megtekintéséhez kövesse az alábbi lépéseket:
1. Nyissa meg a Recovery Services-tárolót.
2. Kattintson a biztonsági mentési infrastruktúra.
3. Biztonsági másolatokat kezelő kiszolgálók megtekintése.

## <a name="see-also"></a>Lásd még
Egyéb munkaterhelések védelme érdekében az Azure Backup Server használatával kapcsolatos információkért lásd: a következő cikkeket:
- [A SharePoint-farm biztonsági mentése](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sharepoint-azure-stack)
- [SQL server biztonsági mentése](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sql-azure-stack)
