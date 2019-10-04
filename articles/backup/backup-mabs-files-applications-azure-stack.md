---
title: Azure Stack virtuális gépek fájljainak biztonsági mentése
description: A Azure Backup használatával biztonsági mentést készíthet, valamint helyreállíthatja Azure Stack fájljait és alkalmazásait a Azure Stack-környezetében.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: dacurwin
ms.openlocfilehash: d9e7aaca99e551e17e8b4be5ef6146a19e44355f
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210207"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Fájlok és alkalmazások biztonsági mentése az Azure Stackben

A Azure Backup a fájlok és az alkalmazások Azure Stackn való védelemmel (vagy biztonsági mentéssel) való ellátására is használható. Fájlok és alkalmazások biztonsági mentéséhez telepítse Microsoft Azure Backup-kiszolgálót Azure Stack rendszeren futó virtuális gépre. Az azonos virtuális hálózatban található Azure Stack-kiszolgálók fájljait is védelemmel láthatja el. A Azure Backup Server telepítése után hozzáadhat Azure-lemezeket a rövid távú biztonsági mentési adatmennyiséghez elérhető helyi tárterület növeléséhez. A Azure Backup Server hosszú távú adatmegőrzéshez az Azure Storage-t használja.

> [!NOTE]
> Bár a Azure Backup Server és a System Center Data Protection Manager (DPM) hasonlóak, a DPM nem támogatott a Azure Stack használatával.
>

Ez a cikk nem tárgyalja a Azure Backup Server telepítését a Azure Stack környezetben. A Azure Stack Azure Backup Server telepítéséhez tekintse meg a [Azure Backup Server telepítését](backup-mabs-install-azure-stack.md)ismertető cikket.

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Azure Stack virtuális gépek fájljainak és mappáinak biztonsági mentése az Azure-ba

A Azure Stack virtuális gépek fájljainak védelemmel Azure Backup Server konfigurálásához nyissa meg a Azure Backup Server-konzolt. A konzol használatával konfigurálhatja a védelmi csoportokat, és gondoskodhat a virtuális gépek adatainak védelméről.

1. A Azure Backup Server-konzolon kattintson a **védelem** elemre, majd az eszköztáron kattintson az **új** elemre az **új védelmi csoport létrehozása** varázsló megnyitásához.

   ![A védelem konfigurálása Azure Backup Server-konzolon](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    A varázsló megnyitása eltarthat néhány másodpercig. A varázsló megnyitása után kattintson a **tovább** gombra a **védelmi csoport típusának kiválasztása** képernyőre való továbblépés előtt.

   ![Új védelmi csoport varázsló nyílik meg](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. A **védelmi csoport típusának kiválasztása** képernyőn válassza a **kiszolgálók** elemet, majd kattintson a **tovább**gombra.

    ![Új védelmi csoport varázsló nyílik meg](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Megnyílik a **csoporttagok kiválasztása** képernyő.

    ![Új védelmi csoport varázsló nyílik meg](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. A **csoporttagok kiválasztása** képernyőn kattintson **+** az alelemek listájának kibontásához. A védelemmel ellátni kívánt összes elemnél jelölje be a jelölőnégyzetet. Ha minden elem ki van választva, kattintson a **tovább**gombra.

    ![Új védelmi csoport varázsló nyílik meg](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft azt javasolja, hogy minden olyan, a védelmi szabályzattal megosztva tárolt adattal egy védelmi csoportba helyezze. A védelmi csoportok megtervezésével és telepítésével kapcsolatos teljes információkért tekintse meg a System Center DPM című cikket, amely a [védelmi csoportok telepítését](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801)ismerteti.

4. Az **adatvédelmi módszer kiválasztása** képernyőn adja meg a védelmi csoport nevét. Jelölje be a **rövid távú védelmet szeretnék** a következő használatával jelölőnégyzetet: és **szeretnék online védelmet**használni. Kattintson a **Tovább** gombra.

    ![Új védelmi csoport varázsló nyílik meg](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Az **online védelem szeretnék**kiválasztásához először válassza a **rövid távú védelmet szeretnék a következő használatával:** Lemez. Azure Backup Server nem véd a szalagra, ezért a rövid távú védelem érdekében a lemez az egyetlen választás.

5. A **rövid távú célok megadása** képernyőn válassza ki, hogy mennyi ideig szeretné megőrizni a lemezre mentett helyreállítási pontokat, és hogy mikor mentse a növekményes biztonsági mentéseket. Kattintson a **Tovább** gombra.

    > [!IMPORTANT]
    > A Azure Backup Server csatolt lemezeken több mint öt napig **ne** őrizze meg az operatív helyreállítási (biztonsági mentési) adatait.
    >

    ![Új védelmi csoport varázsló nyílik meg](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    A növekményes biztonsági mentések intervallumának kiválasztása helyett az expressz teljes biztonsági mentést csak az egyes ütemezett helyreállítási pontok előtt válassza **közvetlenül a helyreállítási pont előtt**. Ha az alkalmazások munkaterheléseit védi, Azure Backup Server helyreállítási pontokat hoz létre a szinkronizálás gyakoriságának ütemezésekor (ha az alkalmazás támogatja a növekményes biztonsági mentéseket). Ha az alkalmazás nem támogatja a növekményes biztonsági mentéseket, Azure Backup Server expressz teljes biztonsági mentést futtat.

    A **Fájl-helyreállítási pontok**esetében a helyreállítási pontok létrehozásának idejét kell megadni. Kattintson a **módosítás** gombra a helyreállítási pontok létrehozásakor a hét időpontjának és napjainak megadásához.

6. A **lemez kiosztásának áttekintése** képernyőn tekintse át a védelmi csoport számára lefoglalt tárterület lemezterületét.

    A védelmi csoport számára ajánlott terület a **teljes adatméret** a biztonsági mentésre és a lemezterület kiszámítására szolgáló Azure Backup Server. Azure Backup Server kiválasztja az ideális biztonsági mentési kötetet a beállítások alapján. A lemez-foglalás részleteiben azonban szerkesztheti a kötetek biztonsági mentésének lehetőségeit. A munkaterhelések esetében válassza ki az előnyben részesített tárolót a legördülő menüben. A módosítások a rendelkezésre álló Disk Storage ablaktáblán a teljes tárterület és a szabad tárterület értékeit módosítják. A kiépített terület az a tárterület, Azure Backup Server a kötethez való hozzáadását javasolja, hogy a biztonsági mentések zökkenőmentesen folytatódnak a jövőben.

7. A **replika-létrehozási módszer**kiválasztása lapon válassza ki, hogyan szeretné kezelni a kezdeti teljes adatreplikációt. Ha úgy dönt, hogy a hálózaton keresztül replikál, az Azure azt javasolja, hogy válasszon ki egy off-Peak időpontot. Nagy mennyiségű vagy az optimális hálózati feltételeknél kisebb méretű adatforgalom esetén érdemes lehet a cserélhető adathordozó használatával replikálni az adatreplikálást.

8. A **konzisztencia-ellenőrzési beállítások kiválasztása**területen válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Engedélyezze, hogy a konzisztencia-ellenőrzés csak akkor fusson, ha az adatreplikálás inkonzisztens lesz, vagy egy ütemterv szerint. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, bármikor lefuttathat egy manuális ellenőrzést:
    * A Azure Backup Server-konzol **védelem** területén kattintson a jobb gombbal a védelmi csoportra, és válassza a **konzisztencia-ellenőrzés végrehajtása**lehetőséget.

9. Ha úgy dönt, hogy biztonsági másolatot készít az Azure-ra, az **online védelmi adatok megadása** oldalon ellenőrizze, hogy ki van-e választva az Azure-ba biztonsági mentéshez használni kívánt munkaterhelések.

10. Az **online biztonsági mentés ütemezésének megadása**területen határozza meg, hogy mikor történjen növekményes biztonsági mentés az Azure-ba.

    A biztonsági másolatok napi, heti, havi és éves futtatására, valamint a futtatásának időpontjára és dátumára is ütemezhetők. A biztonsági mentések naponta akár kétszer is előfordulhatnak. Minden alkalommal, amikor egy biztonsági mentési feladatot futtat, egy adathelyreállítási pont jön létre az Azure-ban a Azure Backup Server lemezen tárolt biztonsági másolati adatok másolatával.

11. Az **online adatmegőrzési szabály meghatározása**területen határozza meg, hogyan maradnak meg a napi/heti/havi/éves biztonsági másolatokből létrehozott helyreállítási pontok az Azure-ban.

12. Az **online replikáció kiválasztása**lapon adja meg, hogyan történjen az adatkezdeti teljes replikálás.

13. Az **Összefoglalás**lapon tekintse át a beállításokat. Amikor a **csoport létrehozása**elemre kattint, a kezdeti adatreplikálás történik. Amikor az adatreplikálás befejeződik, az **állapot** lapon a védelmi csoport állapota **OK**értékként jelenik meg. A kezdeti biztonsági mentési művelet a védelmi csoport beállításaival összhangban zajlik.

## <a name="recover-file-data"></a>Fájlok adatainak helyreállítása

A Azure Backup Server-konzol használatával állítsa helyre az adatokat a virtuális gépre.

1. A Azure Backup Server konzol navigációs sávján kattintson a **helyreállítás** elemre, és keresse meg a helyreállítani kívánt adatokat. Az eredmények ablaktábláján válassza ki az adatmennyiséget.

2. A helyreállítási pontok szakaszban lévő naptárban a félkövérrel szedett dátumok jelzik a helyreállítási pontokat. Válassza ki a helyreállítani kívánt dátumot.

3. A helyreállítható **elem** ablaktáblán válassza ki a helyreállítani kívánt elemet.

4. A **műveletek** ablaktáblán kattintson a **helyreállítás** elemre a helyreállítási varázsló megnyitásához.

5. Az adatok a következőképpen állíthatók helyre:

    * **Helyreállítás az eredeti helyre** – ha az ügyfélszámítógép VPN-kapcsolaton keresztül csatlakozik, ez a beállítás nem működik. Ehelyett használjon másik helyet, majd másolja az adott helyről.
    * **Helyreállítás másik helyre**

6. A helyreállítási beállítások megadása:

    * A **meglévő verzió-helyreállítási viselkedéshez**válassza a **másolat létrehozása**, a **kihagyás**vagy a **felülírás**lehetőséget. A felülírás csak az eredeti helyre történő helyreállításkor érhető el.
    * A **biztonsági mentéshez**válassza **a célszámítógép beállításainak alkalmazása** vagy a **helyreállítási pont biztonsági beállításainak alkalmazása**lehetőséget.
    * A **hálózati**sávszélesség-használat szabályozásához kattintson a **módosítás** gombra a hálózati sávszélesség-használat szabályozásának engedélyezéséhez.
    * **Értesítés** Kattintson az **E-mail küldése a helyreállítás befejezéséről**elemre, és adja meg a címzetteket, akik megkapják az értesítést. Az e-mail címeket vesszővel válassza el egymástól.
    * A kijelölések után kattintson a **tovább** gombra.

7. Tekintse át a helyreállítási beállításokat, és kattintson a **helyreállítás**gombra.

    >[!Note]
    >Amíg a helyreállítási feladat folyamatban van, a kijelölt helyreállítási elemek összes szinkronizációs feladata meg lett szakítva.

Modern biztonsági másolati tárhely (MBS) használata esetén a fájlkiszolgáló végfelhasználói helyreállítása (EUR) nem támogatott. A fájlkiszolgáló EUR Kötet árnyékmásolata szolgáltatás (VSS) függőséggel rendelkezik, amelyet a modern biztonsági másolati tárhely nem használ. Ha az EUR engedélyezve van, a következő lépésekkel állíthatja helyre az adatokat:

1. Navigáljon a védett fájlokhoz, és kattintson a jobb gombbal a fájl nevére, és válassza a **Tulajdonságok**menüpontot.

2. A **Tulajdonságok** menüben kattintson az **előző verziók** lehetőségre, és válassza ki a helyreállítani kívánt verziót.

## <a name="view-azure-backup-server-with-a-vault"></a>Azure Backup Server megtekintése tárolóval

A Azure Portal Azure Backup Server entitások megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg Recovery Services-tárolót.
2. Kattintson a biztonsági mentési infrastruktúra elemre.
3. A biztonságimásolat-felügyeleti kiszolgálók megtekintése.

## <a name="see-also"></a>Lásd még

További információ a Azure Backup Server használatáról más számítási feladatok ellátásához: a következő cikkek egyike:

* [A SharePoint-farm biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [SQL Server biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
