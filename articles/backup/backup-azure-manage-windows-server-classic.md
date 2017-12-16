---
title: "Azure mentési tárolókban, és a kiszolgálók Azure-ban a klasszikus üzembe helyezési modellel kezelése |} Microsoft Docs"
description: "Ez az oktatóanyag segítségével megtudhatja, hogyan Azure mentési tárolókban és kiszolgálók kezelésére."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: cwatson
ms.openlocfilehash: 3a54abe5b86aab41544e61d311ecfab19b577aa7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Azure Backup-tárolók és -kiszolgálók konfigurálása a klasszikus üzemi modellel
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-manage-windows-server.md)
> * [Klasszikus](backup-azure-manage-windows-server-classic.md)
>
>

Ebben a cikkben a biztonságimásolat-felügyeleti feladatokat a klasszikus Azure portál és a Microsoft Azure Backup szolgáltatás ügynöke egy áttekintést talál.

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

> [!IMPORTANT]
> A biztonsági mentési tárolókról mostantól lehetőség van Recovery Services-tárolókra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> Utáni novemberi 30, 2017 csak akkor tud biztonsági mentési tárolók létrehozása a PowerShell használatával. **2017. November 30 által**:
>- Minden fennmaradó Backup-tároló automatikusan Recovery Services-tárolóra frissül.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>

## <a name="management-portal-tasks"></a>Kezelési portál feladatok
1. Jelentkezzen be a [kezelési portál](https://manage.windowsazure.com).
2. Kattintson a **Recovery Services**, majd kattintson a gyors kezdés lapon megtekintheti a mentési tároló nevére.

    ![Helyreállítási szolgáltatások](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Az első lépések lap tetején beállítások kiválasztását, megtekintheti az elérhető felügyeleti feladatait.

![Lapok kezelése](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Irányítópult
Válassza ki **irányítópult** a használati áttekintésben a kiszolgálóhoz. A **használatának áttekintése** tartalmazza:

* A felhőbe regisztrált Windows-kiszolgálók száma
* A védett felhőben található Azure virtuális gépek száma
* A teljes Azure-ban felhasznált tárterület
* A legutóbbi feladatok állapota

Az irányítópult alján a következő feladatokat végezheti el:

* **A tanúsítványnak a** – Ha a tanúsítvány használatával regisztrálja a kiszolgálót, majd használja ezt a tanúsítványt frissíteni. Ha a tárolói hitelesítő adatokat használ, ne használjon **szükséges tanúsítvány kezelése**.
* **Törlés** -törli a jelenlegi biztonsági mentési tárolóba. Ha a mentési tárolóban már használatban van, úgy, hogy szabadítson fel helyet a tárolási törölheti. **Törlés** csak akkor engedélyezett, miután az összes regisztrált kiszolgálókat, hogy törölték a tárolóból.

![Biztonsági mentési irányítópult feladatok](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Regisztrált cikkek
Válassza ki **regisztrált elemek** ehhez a tárolóhoz regisztrált kiszolgáló nevének megtekintéséhez.

![Regisztrált cikkek](./media/backup-azure-manage-windows-server-classic/registered-items.png)

A **típus** szűrés alapértelmezett Azure virtuális gépen. Válassza ki, ha ehhez a tárolóhoz regisztrált kiszolgálók nevei, **Windows server** a legördülő menüből.

Itt a következő feladatokat végezheti el:

* **Az Újraregisztrálás engedélyezése** – Ha ezt a beállítást a kiszolgáló is használhatja a **regisztrációs varázsló** a helyszíni Microsoft Azure Backup-ügynök regisztrálni a kiszolgálót a biztonsági mentési tároló még egyszer. Akkor lehet, hogy újra kell regisztrálnia a tanúsítványt a bekövetkezett hiba miatt, vagy ha egy kiszolgáló építhető újra kellett.
* **Törlés** -kiszolgáló törlése a biztonsági mentési tárolóból. A tárolt adatok a kiszolgálóhoz tartozó összes azonnal törli.

    ![Regisztrált konfigurációelemekkel kapcsolatos feladatokhoz](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Védett elemek
Válassza ki **védett elemek** mentett a kiszolgálók elemek megtekintéséhez.

![Védett elemek](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Konfigurálás
Az a **konfigurálása** lapon kiválaszthatja a megfelelő tárolási redundancia beállítást. Válassza ki a tárolási redundancia beállítást érdemes megfelelő, egy tároló létrehozása után, és mielőtt a számítógépek van regisztrálva.

> [!WARNING]
> Amint egy elem van regisztrálva a tárolóba, a redundancia tárolómegoldást zárolva van, ezért nem módosítható.
>
>

![Konfigurálás](./media/backup-azure-manage-windows-server-classic/configure.png)

További információ a jelen cikk tudnivalók [adattároló redundanciája, amely](../storage/common/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>A Microsoft Azure Backup szolgáltatás ügynöke feladatok
### <a name="console"></a>Konzol
Nyissa meg a **Microsoft Azure Backup szolgáltatás ügynökének** (a gép kereséssel megtalálja *a Microsoft Azure Backup szolgáltatás*).

![Biztonságimásolat-készítő ügynökkel](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Az a **műveletek** érhető el a biztonságimásolat-készítő ügynök konzol jobb végezheti el az alábbi felügyeleti feladatokat:

* Kiszolgáló regisztrálása
* Biztonsági mentés ütemezése
* Biztonsági másolat készítése
* Tulajdonságainak módosítása

![Ügynök konzol műveletek](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> A **adatok helyreállítása**, lásd: [fájlokat állíthatja vissza a Windows server vagy a Windows-ügyfélszámítógép](backup-azure-restore-windows-server.md).
>
>

### <a name="modify-an-existing-backup"></a>Módosítsa a meglévő biztonsági mentésből
1. Kattintson a Microsoft Azure Backup szolgáltatás ügynökének **biztonsági mentés ütemezése**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. Az a **ütemezett biztonsági mentés varázsló** hagyja a **biztonsági másolati elemei vagy időpontok szerepelnek módosítása** jelölőnégyzetet és kattintson **következő**.

    ![Ütemezett biztonsági mentés módosítása](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. Ha szeretné-e hozzáadása vagy módosítása elemek, a a **elemek kijelölése biztonsági mentéshez** képernyőn kattintson **elemek hozzáadása**.

    Azt is beállíthatja **kizárások beállításai** ezen a lapon, a varázslóban. Ha ki szeretné zárni a fájlok vagy fájltípusok olvassa el, hogyan adhat hozzá [kizárások beállításai](#exclusion-settings).
4. Válassza ki a fájlok és mappák biztonsági mentése, és kattintson a kívánt **gépházban**.

    ![Elemek hozzáadása](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. Adja meg a **biztonsági mentés ütemezése** kattintson **következő**.

    (A 3-szor naponkénti maximum) napi vagy heti biztonsági mentései is ütemezheti.

    ![Adja meg a biztonsági mentés ütemezését](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Adja meg a biztonsági mentés ütemezése esetén, tekintse meg a részletes [cikk](backup-azure-backup-cloud-as-tape.md).
   >
   >
6. Válassza ki a **adatmegőrzési** a biztonsági másolatot, majd kattintson a **következő**.

    ![Válassza ki az adatmegőrzési házirend](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. Az a **megerősítő** képernyőn tekintse át az adatokat, és kattintson a **Befejezés**.
8. Miután a varázsló befejezi a **biztonsági mentés ütemezése**, kattintson a **Bezárás**.

    Védelmi módosítása, után ellenőrizheti, hogy biztonsági mentést a megfelelő váltanak a **feladatok** lapra, és erősítse meg, hogy a módosítások megjelennek a biztonsági mentési feladatok.

### <a name="enable-network-throttling"></a>Hálózati sávszélesség-szabályozás engedélyezése
Az Azure Backup szolgáltatás ügynökének biztosítja a sávszélesség-szabályozási fülre, amely lehetővé teszi, hogy vezérlését a hálózati sávszélesség használatának adatátvitel során. Ez a vezérlő akkor lehet hasznos, ha biztonsági kell során az adatokat munkaidő, de nem szeretné a biztonsági mentési folyamat zavarja a más internetes forgalmat. Átviteli biztonsági mentése és visszaállítása a tevékenységek adatok szabályozás vonatkozik.  

Elemre a szabályozás engedélyezéséhez:

1. Az a **Backup szolgáltatás ügynökének**, kattintson a **tulajdonságainak módosítása**.
2. Válassza ki a **engedélyezi az internetes sávszélesség szabályozásának a biztonsági mentési műveleteknél** jelölőnégyzetet.

    ![Hálózati sávszélesség-szabályozás](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. Miután engedélyezte a sávszélesség-szabályozás, adja meg a megengedett sávszélesség vonatkozó biztonsági mentési adatátvitel során **időpontokat a munkaidőhöz** és **munkaidőn kívüli**.

    A sávszélesség értékek kezdődjenek 512 kilobájt / másodperc (Kbps), és folytathatja a legfeljebb 1023 megabájt / másodperc (Mbps). Is kijelölni a kezdő és a Befejezés **időpontokat a munkaidőhöz**, és a hét melyik napjain minősülnek munkahelyi nap. A megadott munkaidőn kívül idő tekinthető munkaidőn kívüli időszakra.
4. Kattintson az **OK** gombra.

## <a name="exclusion-settings"></a>Kizárások beállításai
1. Nyissa meg a **Microsoft Azure Backup szolgáltatás ügynökének** (a gép kereséssel megtalálja *a Microsoft Azure Backup szolgáltatás*).

    ![Nyissa meg a biztonságimásolat-készítő ügynökkel](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. Kattintson a Microsoft Azure Backup szolgáltatás ügynökének **biztonsági mentés ütemezése**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. A biztonsági mentés ütemezése varázsló hagyja a a **biztonsági másolati elemei vagy időpontok szerepelnek módosítása** jelölőnégyzetet és kattintson **következő**.

    ![Ütemezésének módosítása](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. Kattintson a **kizárások beállításai**.

    ![Jelölje ki az elemet kizárása](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. Kattintson a **adja hozzá a kizárási**.

    ![Kizárások hozzáadása](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. Válassza ki azt a helyet, és kattintson a **OK**.

    ![Válassza ki a kizárási helyét](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. Adja hozzá a fájl kiterjesztése a **Fájltípus** mező.

    ![Fájltípus kizárása](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    .Mp3 bővítmény felvétele

    ![Példa a fájl típusa](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Egy másik bővítmény hozzáadásához kattintson **Kizárás hozzáadása** , és adja meg egy másik típus fájlnévkiterjesztés (.jpeg-bővítmény hozzáadása).

    ![Egy másik példában a fájl típusa](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. Ha a bővítményeket adott hozzá, kattintson **OK**.
9. A biztonsági mentés ütemezése varázsló segítségével kattintva folytatni **következő** mindaddig, amíg a **visszaigazolási lapja**, kattintson a **Befejezés**.

    ![Kizárási megerősítése](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Következő lépések
* [Windows Server vagy a Windows ügyfél visszaállítása az Azure-ból](backup-azure-restore-windows-server.md)
* Azure biztonsági mentés kapcsolatos további információkért lásd: [Azure biztonsági mentés áttekintése](backup-introduction-to-azure-backup.md)
* Látogasson el a [Azure biztonsági mentési fórum](http://go.microsoft.com/fwlink/p/?LinkId=290933)
