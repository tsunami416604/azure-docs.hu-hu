---
title: VMware virtuális gépek visszaállítása az Azure Backup Server használatával
description: Az Azure Backup Server (MABS) használatával visszaállíthatja a VMware vCenter/ESXi kiszolgálón futó VMware virtuális gépeket.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212357"
---
# <a name="restore-vmware-virtual-machines"></a>VMware virtuális gépek visszaállítása

Ez a cikk bemutatja, hogyan használhatja a Microsoft Azure Backup Server (MABS) segítségével a VMware VM helyreállítási pontok visszaállítását. Az adatok helyreállításának mabs-ének használatáról a Védett adatok helyreállítása című [témakörben olvashat.](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server) A MABS felügyeleti konzolon kétféleképpen kereshethelyre állítható adatokat – keresés vagy tallózás. Az adatok helyreállításakor lehet, hogy nem szeretné visszaállítani az adatokat vagy a virtuális gép ugyanazon a helyen. Emiatt a MABS három helyreállítási lehetőséget támogat a VMware VM biztonsági mentések esetében:

* **Eredeti hely helyreállítása (OLR)** – Az OLR használatával visszaállíthatja a védett virtuális gép eredeti helyére. A virtuális gép csak akkor állítható vissza az eredeti helyére, ha a biztonsági mentés történt óta nem adtak hozzá vagy töröltek lemezeket. Ha lemezeket adtak hozzá vagy töröltek, alternatív hely-helyreállítást kell használnia.

* **Alternatív hely helyreállítása (ALR)** – Ha az eredeti virtuális gép hiányzik, vagy nem szeretné megzavarni az eredeti virtuális gép, helyreállítani a virtuális gép egy másik helyre. A virtuális gép egy másik helyre történő helyreállításához meg kell adnia egy ESXi-állomás, erőforráskészlet, mappa, valamint a tárolási adattár és elérési út helyét. A visszaállított virtuális gép és az eredeti virtuális gép megkülönböztetése érdekében az MABS hozzáfűzi a "-Recovered" parancsot a virtuális gép nevéhez.

* **Egyéni fájlhely-helyreállítás (ILR)** – Ha a védett virtuális gép Egy Windows Server virtuális gép, a virtuális gépen belüli egyes fájlok/mappák helyreállíthatók az MABS ILR-képességével. Az egyes fájlok helyreállításához olvassa el a cikk későbbi részében található eljárást.

## <a name="restore-a-recovery-point"></a>Helyreállítási pont visszaállítása

1. A MABS felügyeleti konzolon kattintson a Helyreállítási nézet elemre.

2. A Tallózás ablaktáblán tallózással vagy szűréssel keresse meg a helyreállítani kívánt virtuális gépét. Miután kiválasztotta a virtuális gép vagy mappa, a helyreállítási pontok ablaktábla megjeleníti a rendelkezésre álló helyreállítási pontokat.

    ![Elérhető helyreállítási pontok](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. A **Helyreállítási pontok mezőben** a naptár és a legördülő menüsegítségével válassza ki a helyreállítási pont elhívásának dátumát. A félkövérrel szedett naptárdátumok rendelkezésre állnak helyreállítási pontokkal.

4. Az eszközmenüszalagon kattintson a **Helyreállítás** gombra a **Helyreállítási varázsló**megnyitásához.

    ![Helyreállítási varázsló, A helyreállítás kijelölésének áttekintése](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Kattintson a **Tovább** gombra a **Helyreállítási beállítások megadása** képernyőre való ugráshoz.

6. A **Helyreállítási beállítások megadása** képernyőn, ha engedélyezni szeretné a hálózati sávszélesség szabályozását, kattintson **a Módosítás gombra.** Ha le szeretné tiltani a hálózati szabályozást, kattintson a **Tovább**gombra. A varázsló képernyőjén nincs más lehetőség a VMware virtuális gépekhez. Ha úgy dönt, hogy módosítja a hálózati sávszélesség-szabályozást, a Szabályozás párbeszédpanelen jelölje be a **Hálózati sávszélesség-használat szabályozásának engedélyezését.** Ha engedélyezve van, konfigurálja a **Beállítások** és **a Munkaütemezés**beállítást.

7. A **Helyreállítási típus kiválasztása** képernyőn válassza ki, hogy az eredeti példányra vagy egy új helyre szeretne-e helyreállítani, majd kattintson a **Tovább**gombra.

     * Ha a **Helyreállítás az eredeti példányra**lehetőséget választja, nem kell több döntést hoznia a varázslóban. A rendszer az eredeti példány adatait használja.

     * Ha **a Helyreállítás virtuális gépként**lehetőséget választja bármely állomáson , majd a Cél **megadása** képernyőn adja meg az **ESXi állomás, az erőforráskészlet, a mappa** és az elérési út **adatait.**

      ![Helyreállítási típus kiválasztása](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Az **Összegzés** képernyőn tekintse át a beállításokat, és kattintson a **Helyreállítás** gombra a helyreállítási folyamat elindításához. A **helyreállítási állapot** képernyő a helyreállítási művelet előrehaladását mutatja.

## <a name="restore-an-individual-file-from-a-vm"></a>Egyéni fájl visszaállítása virtuális gépről

Az egyes fájlok at egy védett virtuális gép helyreállítási pont. Ez a szolgáltatás csak Windows Server virtuális gépekesetén érhető el. Az egyes fájlok visszaállítása hasonló a teljes virtuális gép visszaállításához, kivéve, ha a helyreállítási folyamat megkezdése előtt a VMDK-ban böngészik, és megtalálja a kívánt fájlt.Restoreing individual files is similar to restoreing the entire VMm, except you browse into the VMDK and find the file(s) want, before starting the recovery process. Adott fájl helyreállítása vagy fájlok kijelölése Windows Server virtuális gépről:

>[!NOTE]
>Az egyes fájlok virtuális gépről történő visszaállítása csak Windows virtuális gépek és lemezhelyreállítási pontok esetén érhető el.

1. A MABS felügyeleti konzolon kattintson a **Helyreállítási** nézet elemre.

2. A **Tallózás** ablaktáblán tallózással vagy szűréssel keresse meg a helyreállítani kívánt virtuális gépét. Miután kiválasztotta a virtuális gép vagy mappa, a helyreállítási pontok ablaktábla megjeleníti a rendelkezésre álló helyreállítási pontokat.

    ![Elérhető helyreállítási pontok](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. A **Helyreállítási pontok a:** ablaktáblán a naptár segítségével válassza ki a kívánt helyreállítási pontot(oka)t tartalmazó dátumot. A biztonsági mentési házirend konfigurálásától függően a dátumok nak több helyreállítási pontis lehet. Miután kiválasztotta a helyreállítási pont eltöltésének napját, győződjön meg arról, hogy a megfelelő **helyreállítási időt**választotta. Ha a kijelölt dátumnak több helyreállítási pontja van, válassza ki a helyreállítási pontot a Helyreállítási idő legördülő menüben. Miután kiválasztotta a helyreállítási pontot, a helyreállítható elemek listája megjelenik a **Path:** ablaktáblában.

4. A helyreállítani kívánt fájlok megkereséséhez a **Görbe** ablaktáblán kattintson duplán az elemre a **Helyreállítható elem** oszlopban a megnyitáshoz. Jelölje ki a helyreállítani kívánt fájlt, fájlokat vagy mappákat. Több elem kijelöléséhez nyomja le a **Ctrl billentyűt** az egyes elemek kijelölése közben. A **Görbe** ablaktáblán kereshet a **Helyreállítható elem** oszlopban megjelenő fájlok vagy mappák listájában. **Az alábbi keresési lista** nem keres almappákat. Az almappákban való kereséshez kattintson duplán a mappára. A **Fel** gombbal mozoghat egy gyermekmappából a szülőmappába. Több elemet (fájlt és mappát) is kijelölhet, de azoknak ugyanabban a szülőmappában kell lenniük. Ugyanazon helyreállítási feladattöbb mappájából nem lehet visszaállítani az elemeket.

    ![A helyreállítási beállítások áttekintése](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Miután kijelölte az elem(eke)t a helyreállításhoz, a Felügyeleti konzol eszközmenüszalagján kattintson a **Helyreállítás** gombra a **Helyreállítási varázsló**megnyitásához . A Helyreállítás varázslóban a **Helyreállítási vizsgálat áttekintése képernyőn** láthatók a helyreállítandó kijelölt elemek.

6. A **Helyreállítási beállítások megadása** képernyőn, ha engedélyezni szeretné a hálózati sávszélesség szabályozását, kattintson **a Módosítás gombra.** Ha le szeretné tiltani a hálózati szabályozást, kattintson a **Tovább**gombra. A varázsló képernyőjén nincs más lehetőség a VMware virtuális gépekhez. Ha úgy dönt, hogy módosítja a hálózati sávszélesség-szabályozást, a Szabályozás párbeszédpanelen jelölje be a **Hálózati sávszélesség-használat szabályozásának engedélyezését.** Ha engedélyezve van, konfigurálja a **Beállítások** és **a Munkaütemezés**beállítást.
7. A **Helyreállítási típus kiválasztása** képernyőn kattintson a **Tovább**gombra. A fájl(oka)t vagy mappákat csak hálózati mappába állíthatja helyre.
8. A **Cél megadása** képernyőn kattintson a **Tallózás** gombra a fájlok vagy mappák hálózati helyének megkereséséhez. A MABS létrehoz egy mappát, amelyben az összes helyreállított elem másolásra kerül. A mappanév előtaggal rendelkezik, MABS_day hónapos évben. Amikor kiválasztja a helyreállított fájlok vagy mappák helyét, a rendszer megadhatja az adott hely részleteit (Cél, Cél elérési út és szabad terület).

    ![Fájlok helyreállításához szükséges hely megadása](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. A **Helyreállítási beállítások megadása** képernyőn válassza ki az alkalmazni kívánt biztonsági beállítást. Választhatja a hálózati sávszélesség-használat szabályozásának módosítását, de a szabályozás alapértelmezés szerint le van tiltva. Emellett a **SAN-helyreállítás** és **az értesítés** nincs engedélyezve.
10. Az **Összegzés** képernyőn tekintse át a beállításokat, és kattintson a **Helyreállítás** gombra a helyreállítási folyamat elindításához. A **helyreállítási állapot** képernyő a helyreállítási művelet előrehaladását mutatja.

## <a name="next-steps"></a>További lépések

Az Azure Backup Server használatával kapcsolatos hibaelhárítási problémákról az [Azure Backup Server hibaelhárítási útmutatójában](./backup-azure-mabs-troubleshoot.md)talál.
