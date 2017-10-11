---
title: "A visszaállítás egy Windows Server vagy a Windows-ügyfél a klasszikus telepítési modell segítségével az Azure-ból |} Microsoft Docs"
description: "Megtudhatja, hogyan visszaállítása a Windows Server vagy a Windows-ügyfél."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 300b2b17b44e21ed446fd63d572a2461e2fc1343
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Fájlok visszaállítása a Windows-kiszolgálóra vagy -ügyfélre a klasszikus üzemi modell használatával
> [!div class="op_single_selector"]
> * [Klasszikus portál](backup-azure-restore-windows-server-classic.md)
> * [Azure Portal](backup-azure-restore-windows-server.md)
>
>

Ez a cikk azt ismerteti, hogyan adatokat helyreállítani egy biztonsági mentési tárolóból, és annak visszaállítására egy kiszolgáló vagy a számítógépen. -Től kezdődően. március 2017, már nem készíthetők mentési tárolók a klasszikus portálon.

> [!IMPORTANT]
> A biztonsági mentési tárolókról mostantól lehetőség van Recovery Services-tárolókra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> **2017. október 15-től** a PowerShell a továbbiakban nem használható Backup-tárolók létrehozására. <br/> **2017. november 1-től kezdődően**:
>- A rendszer automatikusan elvégzi valamennyi megmaradó biztonsági mentési tároló átváltását Recovery Services-tárolókra.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>

Adatok visszaállítása, használja az adatok helyreállítása varázsló a Microsoft Azure Recovery Services (MARS) ügynök. Adatok helyreállításakor lehetősége:

* Állítsa vissza az adatokat ugyanarra a számítógépre, amelyről a biztonsági mentések vették.
* A visszaállítás egy másik gépen.

2017. január, a Microsoft kiadott a MARS Agent előzetes frissítés. Hibajavításokat tartalmaz, valamint a frissítés lehetővé teszi, hogy azonnali visszaállítása, amely lehetővé teszi egy helyreállítási kötet írható helyreállítási pont pillanatkép csatlakoztatni. A helyi számítógépre, ezáltal visszaállítása fájlok majd ismerje meg a helyreállítási kötet, és másolja a fájlokat.

> [!NOTE]
> A [2017. január Azure Backup frissítését](https://support.microsoft.com/en-us/help/3216528?preview) szükség, ha az adatok helyreállítását a azonnali visszaállításához használni kívánt. A biztonsági mentési adatok is a tárolók az területi beállításokat, a támogatási cikkben szereplő kell védeni. Tekintse át a [2017. január Azure Backup frissítését](https://support.microsoft.com/en-us/help/3216528?preview) területi beállításokat, amelyek támogatják a azonnali állítsa vissza a legfrissebb listáját. Azonnali visszaállítás **nem** elérhetők az összes területi beállításokat.
>

Azonnali visszaállítási nem áll rendelkezésre a Recovery Services-tárolók használható az Azure portál és a biztonsági mentési tárolók a klasszikus portálon. Ha szeretné használni a azonnali visszaállítása, töltse le a MARS frissítést, és hajtsa végre, amelyek említik azonnali visszaállítása.


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Ugyanaz a gép adatok helyreállíthatók a azonnali visszaállítás segítségével

Ha véletlenül törölte a fájlt, és ugyanarra a számítógépre (ahol a biztonsági mentés lesz végrehajtva) visszaállítására szeretnének, a következő lépésekkel állítsa helyre az adatokat.

1. Nyissa meg a **a Microsoft Azure Backup szolgáltatás** az illesztési. Ha nem tudja, ahová a beépülő modul telepítve van, a számítógép vagy a kiszolgáló keresése **a Microsoft Azure Backup szolgáltatás**.

    Egy asztali alkalmazás meg kell jelennie a keresési eredmények között.

2. Kattintson a **adatok helyreállítása** varázsló elindításához.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

3. Az a **bevezetés** ablaktáblán, az adatok helyreállítását a kiszolgálóhoz vagy a számítógépen, válassza ki **ehhez a kiszolgálóhoz (`<server name>`)** kattintson **következő**.

    ![Állítsa vissza az adatokat ugyanarra a számítógépre a kiszolgáló lehetőség kiválasztása](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. A a **válassza ki a helyreállítási mód** ablaktáblában válassza **egyes fájlok és mappák** , majd **következő**.

    ![Fájlok tallózása](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. A a **mennyiség kiválasztása és a dátum** ablaktáblán válassza ki a kötetet, amely tartalmazza a fájlok és/vagy a visszaállítani kívánt mappákat.

    A naptárban válasszon ki egy helyreállítási pontot. Bármelyik helyreállítási pontra állíthatja vissza időben. A dátumok **félkövér** jelző legalább egy helyreállítási pont rendelkezésre állását. Miután egy dátumot, ha több helyreállítási pont nem érhető el, válassza ki az adott helyreállítási pontot a **idő** legördülő menü.

    ![Kötet és a dátum](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, kattintson a **csatlakoztatási**.

    Azure biztonsági mentés, a helyi helyreállítási pontot csatlakoztatja, és egy helyreállítási kötet használja.

7. Az a **tallózással keresse meg és a fájlok helyreállítása** ablaktáblán kattintson a **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről.

    ![Helyreállítási beállítások](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. A Windows Intézőben másolja a fájlokat és/vagy mappákat szeretné visszaállítani, majd illessze be a helyi a kiszolgálóhoz vagy a számítógép bármely helyére. Nyissa meg vagy adatfolyamként küldje el a fájlok közvetlenül a helyreállítási kötet, és ellenőrizze, hogy a megfelelő verzióra módon legyenek helyreállíthatók.

    ![Másolja és illessze be a fájlok és mappák csatlakoztatott kötet helyi helyre](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Ha elkészült a fájlok és/vagy mappák visszaállítása a a **tallózással keresse meg és a helyreállítási fájlokat** ablaktáblán kattintson a **leválasztási**. Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e a kötet leválasztása.

    ![A kötet leválasztása és megerősítése](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem kattint az leválasztási, a helyreállítási kötet marad csatlakoztatott hat órán át a óta, amikor csatlakoztatva lett. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva. Bármely az időszakban, ha a kötet csatlakoztatva, ütemezett biztonsági mentési művelet után a helyreállítási kötet nem fog futni.
    >


## <a name="recover-data-to-the-same-machine"></a>Ugyanarra a számítógépre adatok helyreállítása
Ha véletlenül törölte a fájlt, és ugyanarra a számítógépre (ahol a biztonsági mentés lesz végrehajtva) visszaállítására szeretnének, a következő lépésekkel állítsa helyre az adatokat.

1. Nyissa meg a **a Microsoft Azure Backup szolgáltatás** az illesztési.
2. Kattintson a **adatok helyreállítása** a munkafolyamat elindítására.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Válassza ki a  **ehhez a kiszolgálóhoz (*yourmachinename*) ** fájl ugyanazon a számítógépen a biztonsági másolat visszaállítása a beállítást.

    ![Ugyanaz a gép](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. Válassza ki a **fájlok tallózása** vagy **fájlok keresése**.

    Hagyja bejelölve az alapértelmezett beállítás, ha azt tervezi, hogy az elérési úton található egy vagy több fájlok helyreállítása. Ha nem tudja biztosan, hogy a mappaszerkezetet, de szeretné megkeresni a fájlt, válassza ki a **fájlok keresése** lehetőséget. Ez a szakasz céljából azt folytatja az alapértelmezett beállítás.

    ![Fájlok tallózása](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Válassza ki a kötetet, amelyen állítsa vissza a fájlt kívánja.

    Minden olyan pont állíthatja vissza időben. Megjelenő dátumok **félkövér** a havinaptár-vezérlőben meg, a helyreállítási pont rendelkezésre állását. A kijelölt dátum a biztonsági mentési ütemezést (és a biztonsági mentés sikeres) alapján választhat egy pont időpontját a **idő** legördülő listán.

    ![Kötet és a dátum](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Válassza ki a helyreállítandó elemek. Beállíthatja a többszörös kiválasztási mappák és fájlok kívánja visszaállítani.

    ![Fájlok kiválasztása](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Adja meg a helyreállítási paramétereket.

    ![Helyreállítási beállítások](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * Lehetősége van az eredeti helyre (amelyben a fájl vagy mappa volna felülírja), vagy egyazon számítógépen egy másik helyre állítja vissza.
   * A visszaállítani kívánt fájl vagy mappa a célhelyen létezésének (ugyanazon fájl két verziója)-másolatok létrehozása, felülírja a fájlokat a célhelyen, vagy a fájlok a cél létező helyreállításának kihagyása.
   * Erősen ajánlott, hogy hagyja bejelölve az alapértelmezett beállítás állítja vissza az ACL-ek a helyreállítás alatt álló fájlokat.
8. Amikor a bemeneti adatok érhetők el, kattintson a **következő**. A helyreállítási munkafolyamat, amely a fájlok ezen a számítógépen, megkezdődik.

## <a name="recover-to-an-alternate-machine"></a>Helyreállítás egy másik gépen
A teljes kiszolgáló nem vesztek el, ha akkor is továbbra is állíthat helyre adatokat az Azure Backup másik gépre. A következő lépések bemutatják a munkafolyamat.  

Ezeket a lépéseket a használt terminológiával alábbiakat tartalmazza:

* *Forrásgép* – a biztonsági mentés készült, és ez jelenleg nem érhető el az eredeti gép.
* *Célszámítógép* – a gép, amelyre az adatok helyreállítása.
* *Minta tároló* – a mentési tárolóba, amelyhez a *forrásgép* és *célgépen* van regisztrálva. <br/>

> [!NOTE]
> A gépről készített biztonsági másolatok nem állítható vissza egy számítógépen, amelyen az operációs rendszer korábbi verzióját. Például ha biztonsági mentést készít a Windows 7 gépről, akkor visszaállítása végezhető el a Windows 8 vagy újabb gép. Azonban a viszont nem rendelkezik igaz.
>
>

1. Nyissa meg a **a Microsoft Azure Backup szolgáltatás** a az illesztési a *célgépen*.
2. Győződjön meg arról, hogy a *célgépen* és a *forrásgép* ugyanazt a biztonsági mentési tárolóhoz regisztrált.
3. Kattintson a **adatok helyreállítása** a munkafolyamat elindítására.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Válassza ki **egy másik kiszolgáló**

    ![Egy másik kiszolgáló](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Adja meg a tároló hitelesítő adatait tartalmazó fájlt, amely megfelel a *minta tároló*. Ha a tárolói hitelesítő adatok fájlját érvénytelen (vagy lejárt) töltse le az új tárolói hitelesítő adatok fájlját az *minta tároló* a klasszikus Azure portálon. Miután a tárolói hitelesítő adatok fájlját valósul meg, szemben a tároló hitelesítő adatait tartalmazó fájlt a mentési tároló jelenik meg.
6. Válassza ki a *forrásgép* megjelenített gépet a listából.

    ![Számítógépek listája](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Válassza a **fájlok keresése** vagy **fájlok tallózása** lehetőséget. Ez a szakasz céljából használjuk a **fájlok keresése** lehetőséget.

    ![Keresés](./media/backup-azure-restore-windows-server-classic/search.png)
8. Válassza ki a kötet és a dátum a következő képernyőn. Keresse meg a visszaállítani kívánt fájl/mappa nevét.

    ![Keresési elemek](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Válassza ki a helyet, ahol a fájlokat kell állítani.

    ![Hely visszaállítása](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Adja meg a titkosítási jelszót, amely során *forrás gép* regisztrálási *minta tároló*.

    ![Titkosítás](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. Amikor a bemeneti valósul meg, kattintson a **helyreállítása**, amely elindítja a megadott célt a biztonsági mentésben szereplő fájlok helyreállítását.

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Állítsa vissza az adatokat egy másik számítógépre a azonnali visszaállítás segítségével
A teljes kiszolgáló nem vesztek el, ha akkor is továbbra is állíthat helyre adatokat az Azure Backup másik gépre. A következő lépések bemutatják a munkafolyamat.

Ezeket a lépéseket a használt terminológiával alábbiakat tartalmazza:

* *Forrásgép* – a biztonsági mentés készült, és ez jelenleg nem érhető el az eredeti gép.
* *Célszámítógép* – a gép, amelyre az adatok helyreállítása.
* *Minta tároló* – a Recovery Services-tároló, amelyhez a *forrásgép* és *célgépen* van regisztrálva. <br/>

> [!NOTE]
> Biztonsági mentés nem állítható vissza egy célszámítógépen az operációs rendszer korábbi verzióját futtató. Például egy készült biztonsági másolatok a Windows 7 számítógép lehet vissza a Windows 8-as vagy újabb verzióját, számítógép. Nem állítható vissza egy készült biztonsági másolatok a Windows 8 számítógépről a Windows 7 rendszerű számítógépen.
>
>

1. Nyissa meg a **a Microsoft Azure Backup szolgáltatás** a az illesztési a *célgépen*.

2. Győződjön meg arról a *célgépen* és a *forrásgép* ugyanazt a Recovery Services-tároló van regisztrálva.

3. Kattintson a **adatok helyreállítása** megnyitásához a **adatok helyreállítása varázsló**.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

4. Az a **bevezetés** ablaktáblán válassza előbb **egy másik kiszolgáló**

    ![Egy másik kiszolgáló](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Adja meg a tároló hitelesítő adatait tartalmazó fájlt, amely megfelel a *minta tároló*, és kattintson a **következő**.

    Ha a tárolói hitelesítő adatok fájlját érvénytelen (vagy lejárt), töltse le az új tárolói hitelesítő adatok fájlját az *minta tároló* az Azure portálon. Miután megadta a egy érvényes tároló hitelesítő adatait, a megfelelő mentési tároló neve jelenik meg.

6. Az a **biztonsági mentés kiszolgáló kiválasztása** ablaktáblán válassza ki a *forrásgép* megjelenített gépet a listából, és adja meg a jelszót. Ezután kattintson a **Next** (Tovább) gombra.

    ![Számítógépek listája](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Az a **válassza ki a helyreállítási mód** ablaktáblán válassza ki az **egyes fájlok és mappák** kattintson **tovább**.

    ![Keresés](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. A a **mennyiség kiválasztása és a dátum** ablaktáblán válassza ki a kötetet, amely tartalmazza a fájlok és/vagy a visszaállítani kívánt mappákat.

    A naptárban válasszon ki egy helyreállítási pontot. Bármelyik helyreállítási pontra állíthatja vissza időben. A dátumok **félkövér** jelző legalább egy helyreállítási pont rendelkezésre állását. Miután egy dátumot, ha több helyreállítási pont nem érhető el, válassza ki az adott helyreállítási pontot a **idő** legördülő menü.

    ![Keresési elemek](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Kattintson a **csatlakoztatási** helyileg csatlakoztatása a helyreállítás kötetként a helyreállítási pont a *célgépen*.

10. Az a **tallózással keresse meg és a fájlok helyreállítása** ablaktáblán kattintson a **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről.

    ![Titkosítás](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. A Windows Intézőben, másolja a fájlokat és/vagy mappák a helyreállítási kötet be őket a *célgépen* helyét. Nyissa meg vagy adatfolyamként küldje el a fájlok közvetlenül a helyreállítási kötet, és ellenőrizze, hogy a megfelelő verzióra módon legyenek helyreállíthatók.

    ![Titkosítás](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Ha elkészült a fájlok és/vagy mappák visszaállítása a a **tallózással keresse meg és a helyreállítási fájlokat** ablaktáblán kattintson a **leválasztási**. Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e a kötet leválasztása.

    ![Titkosítás](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem kattint az leválasztási, a helyreállítási kötet marad csatlakoztatott hat órán át a óta, amikor csatlakoztatva lett. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva. Bármely az időszakban, ha a kötet csatlakoztatva, ütemezett biztonsági mentési művelet után a helyreállítási kötet nem fog futni.
    >


## <a name="next-steps"></a>Következő lépések
* [Az Azure biztonsági mentési – gyakori kérdések](backup-azure-backup-faq.md)
* Látogasson el a [Azure biztonsági mentési fórum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>Részletek
* [Az Azure biztonsági mentés áttekintése](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Az Azure virtuális gépek biztonsági mentése](backup-azure-vms-introduction.md)
* [Fel Microsoft-munkaterhelések biztonsági mentése](backup-azure-dpm-introduction.md)
