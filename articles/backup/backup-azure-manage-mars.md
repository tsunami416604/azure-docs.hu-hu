---
title: A MARS-ügynök biztonsági másolatainak kezelése és figyelése
description: Megtudhatja, hogyan kezelheti és figyelheti Microsoft Azure Recovery Services (MARS) ügynök biztonsági másolatait a Azure Backup szolgáltatás használatával.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: edb672f97f467378176bf6cdf04fe8e22cc5e51b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173068"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Microsoft Azure Recovery Services-(MARS-) ügynök biztonsági másolatainak kezelése a Azure Backup szolgáltatás használatával

Ez a cikk azt ismerteti, hogyan kezelhetők a Microsoft Azure Recovery Services ügynökkel biztonsági mentés alatt álló fájlok és mappák.

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza, hogy mikor kell pillanatképeket készíteni az adatról a helyreállítási pontok létrehozásához és a helyreállítási pontok megőrzéséhez. A biztonsági mentési szabályzatot a MARS-ügynök használatával konfigurálhatja.

Hozzon létre egy szabályzatot az alábbiak szerint:

1. A MARS-ügynök letöltése és regisztrálása után indítsa el az ügynök konzolját. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.  
2. A **műveletek**területen kattintson a **biztonsági mentés időzítése**elemre.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)
3. A biztonsági mentés ütemezett létrehozása varázslóban > az **első lépéseket**, majd kattintson a **tovább**gombra.
4. Az **elemek kijelölése biztonsági mentéshez lapon**kattintson az **elemek hozzáadása**gombra.

    ![Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. Az **elemek kiválasztása**területen válassza ki, hogy mit szeretne biztonsági másolatot készíteni, majd kattintson **az OK gombra**.

    ![Biztonsági mentésre kijelölt elemek](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Az **elemek kijelölése biztonsági mentéshez** lapon kattintson a **tovább**gombra.
7. Az **Ütemezés biztonsági** mentése lapon megadhatja, hogy napi vagy heti biztonsági mentést szeretne készíteni. Ezután kattintson a **Next** (Tovább) gombra.

    - A biztonsági másolat létrehozásakor létrejön egy helyreállítási pont.
    - A környezetében létrehozott helyreállítási pontok száma a biztonsági mentési ütemtervtől függ.

8. Napi biztonsági mentéseket naponta akár háromszor is ütemezhet. A képernyőképen például két napi biztonsági mentés látható, egy éjfélkor, egy pedig 6:00 ÓRAKOR.

    ![Napi ütemterv](./media/backup-configure-vault/day-schedule.png)

9. A heti biztonsági mentéseket is futtathatja. A képernyőképen látható például, hogy a biztonsági másolatok minden második vasárnap & szerdán, 9:30-kor és 1:00-kor.

    ![Heti ütemterv](./media/backup-configure-vault/week-schedule.png)

10. Az **adatmegőrzési szabály kiválasztása** lapon adja meg, hogyan tárolja az adatok korábbi másolatait. Ezután kattintson a **Next** (Tovább) gombra.

    - Az adatmegőrzési beállítások határozzák meg, hogy mely helyreállítási pontokat kell tárolni, és hogy mennyi ideig kell tárolni őket.
    - Ha például a napi adatmegőrzési beállítást állítja be, akkor azt jelzi, hogy a napi megőrzéshez megadott idő elteltével a rendszer a megadott számú napig megőrzi a legutóbbi helyreállítási pontot. Másik példaként egy havi adatmegőrzési szabályzatot is megadhat, amely azt jelzi, hogy minden hónap 30-án létrehozott helyreállítási pontot 12 hónapig kell tárolni.
    - A napi és heti helyreállítási pontok megőrzése általában egybeesik a biztonsági mentési ütemtervtel. Azt jelenti, hogy amikor a biztonsági mentést az ütemterv szerint indítja el, a biztonsági mentés által létrehozott helyreállítási pont a napi vagy heti adatmegőrzési szabályzatban jelzett időtartamra van tárolva.
    - A következő képernyőképen látható: – a napi biztonsági másolatok éjfélkor és 6:00 ÓRAKOR tartanak hét napig.
            – A szombaton éjfélkor és 6:00 ÓRAKOR készített biztonsági másolatok négy hétig tartanak.
            – A hónap utolsó hetében szombaton, éjfélkor és 6:00 ÓRAKOR készített biztonsági másolatok 12 hónapig tartanak.
            – A március utolsó hetében szombaton készített biztonsági másolatok 10 évig tartanak.

    ![Adatmegőrzési példa](./media/backup-configure-vault/retention-example.png)

11. A **kezdeti biztonsági mentés típusának kiválasztása beállításnál** döntse el, hogy szeretné-e a kezdeti biztonsági mentést a hálózaton keresztül vagy offline biztonsági mentést használni (az offline biztonsági mentésről további információt ebben a [cikkben](backup-azure-backup-import-export.md)talál). Ha a kezdeti biztonsági mentést a hálózaton keresztül szeretné végrehajtani, válassza az **automatikusan a hálózaton keresztül** lehetőséget, és kattintson a **tovább**gombra.

    ![kezdeti biztonsági mentés típusa](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. A **megerősítés**lapon tekintse át az információkat, majd kattintson a **Befejezés**gombra.
    ![biztonsági másolat típusának megerősítése](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.
  ![a biztonsági mentési folyamat módosításának megerősítése](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Létre kell hoznia egy szabályzatot minden olyan gépen, amelyen az ügynök telepítve van.

## <a name="modify-a-backup-policy"></a>Biztonsági mentési szabályzat módosítása

A biztonsági mentési szabályzat módosításakor hozzáadhat új elemeket, eltávolíthatja a meglévő elemeket a biztonsági másolatból, vagy kizárhatja a fájlok biztonsági mentését a kizárási beállítások használatával.

- **Elemek hozzáadása** ezt a lehetőséget csak új elemek biztonsági mentéshez való hozzáadásához használja. Meglévő elemek eltávolításához használja az **elemek eltávolítása** vagy a **kizárási beállítások** lehetőséget.  
- **Elemek eltávolítása** ezzel a beállítással távolíthatja el az elemeket a biztonsági mentésből.
  - A **kizárási beállítások** használatával **távolítsa el**a köteten belüli összes elemet az elemek eltávolítása helyett.
  - Egy kötet összes kijelölésének törlésével a rendszer a legutóbbi biztonsági mentés időpontjában megőrzési beállításokként megőrzi az elemek régi biztonsági másolatait, a módosítás hatóköre nélkül.
  - Ha újra kiválasztja ezeket az elemeket, a rendszer az első teljes biztonsági mentést és az új házirend-módosításokat nem alkalmazza a régi biztonsági másolatokra.
  - A teljes kötet kijelölésének megszüntetése megőrzi a korábbi biztonsági mentést az adatmegőrzési szabályzat módosításának hatóköre nélkül.
- **Kizárási beállítások** ezzel a beállítással kizárhat bizonyos elemeket a biztonsági mentésből.
  
### <a name="add-new-items-to-existing-policy"></a>Új elemek hozzáadása meglévő szabályzathoz

1. A **műveletek**területen kattintson a **biztonsági mentés időzítése**elemre.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

2. A **házirend-elem kiválasztása** lapon válassza **a fájlok és mappák biztonsági mentésének módosítása** lehetőséget, majd kattintson a **tovább**gombra.

    ![Házirend-elemek kiválasztása](./media/backup-azure-manage-mars/select-policy-items.png)

3. A **biztonsági mentés módosítása vagy leállítása** lapon jelölje be a **módosítások végrehajtása a biztonsági másolatok vagy időpontok számára** lehetőséget, és kattintson a **tovább**gombra.

    ![Biztonsági mentés módosítása vagy időzítése](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Az elemek **kiválasztása a biztonsági mentéshez** lapon kattintson az **elemek hozzáadása** lehetőségre azon elemek hozzáadásához, amelyekről biztonsági másolatot szeretne készíteni.

    ![Biztonsági másolat hozzáadási elemeinek módosítása vagy ütemezett létrehozása](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Az **elemek kijelölése** ablakban válassza ki a hozzáadni kívánt legyek vagy mappák elemet, majd kattintson **az OK**gombra.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-item.png)

6. Hajtsa végre a következő lépéseket, majd kattintson a **Befejezés** gombra a művelet befejezéséhez.

### <a name="add-exclusion-rules-to-existing-policy"></a>Kizárási szabályok hozzáadása a meglévő szabályzathoz

Kizárási szabályok hozzáadásával kihagyhatja azokat a fájlokat és mappákat, amelyeket nem szeretne biztonsági mentésre készíteni. Ezt az új szabályzat definiálásakor vagy egy meglévő szabályzat módosításakor teheti meg.

1. A Műveletek ablaktáblán kattintson az **ütemezett biztonsági mentés**elemre. A **biztonsági mentéshez válassza az elemek elemet** , majd kattintson a **kizárási beállítások**elemre.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. A **kizárási beállítások**területen kattintson a **kizárás hozzáadása**lehetőségre.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/add-exclusion.png)

3. A **kizárni kívánt elemek kiválasztásával**tallózzon a fájlokhoz és mappákhoz, és válassza ki azokat az elemeket, amelyeket ki szeretne zárni, majd kattintson **az OK**gombra.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Alapértelmezés szerint a kijelölt mappákban lévő összes **almappa** ki van zárva. Ezt megváltoztathatja az **Igen** vagy a **nem**lehetőség kiválasztásával. A kizárni kívánt fájltípusokat a lent látható módon szerkesztheti és részletezheti:

    ![Elemek kijelölése](./media/backup-azure-manage-mars/subfolders-type.png)

5. Hajtsa végre a következő lépéseket, majd kattintson a **Befejezés** gombra a művelet befejezéséhez.

### <a name="remove-items-from-existing-policy"></a>Elemek eltávolítása meglévő házirendből

1. A Műveletek ablaktáblán kattintson az **ütemezett biztonsági mentés**elemre. Lépjen a **biztonsági mentés elemre**. A listából válassza ki azokat a fájlokat és mappákat, amelyeket el szeretne távolítani a biztonsági mentési ütemtervből, majd kattintson az **elemek eltávolítása**elemre.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Körültekintően járjon el, ha teljesen eltávolítja a kötetet a szabályzatból.  Ha újra hozzá kell adnia, akkor a rendszer új kötetként kezeli. A következő ütemezett biztonsági mentés a növekményes biztonsági mentés helyett kezdeti biztonsági mentést (teljes biztonsági mentést) hajt végre. Ha a későbbiekben átmenetileg el kell távolítania és hozzá kell adnia az elemeket, akkor az **elemek eltávolítása** helyett a **kizárási beállítások** használata javasolt, hogy a teljes biztonsági mentés helyett a növekményes biztonsági mentést biztosítsa.

2. Hajtsa végre a következő lépéseket, majd kattintson a **Befejezés** gombra a művelet befejezéséhez.

## <a name="stop-protecting-files-and-folder-backup"></a>Fájlok és mappák biztonsági mentésének leállítása

A fájlok és mappák biztonsági mentése kétféleképpen állítható le:

- **A védelem leállítása és a biztonsági mentési adat megőrzése**.
  - Ezzel a beállítással leállíthatja az összes jövőbeli biztonsági mentési feladatot a védelemből.
  - Azure Backup szolgáltatás megőrzi azokat a helyreállítási pontokat, amelyek biztonsági mentése a megőrzési házirend alapján történt.
  - Visszaállíthatja a lejárt helyreállítási pontok biztonsági másolatait.
  - Ha úgy dönt, hogy folytatja a védelmet, használhatja a *biztonsági mentési ütemterv újbóli engedélyezése* lehetőséget. Ezt követően az adatok megmaradnak az új adatmegőrzési szabályzat alapján.
- **Állítsa le a védelmet, és törölje a biztonsági másolati fájlokat**.
  - Ezzel a beállítással leállíthatja az összes jövőbeli biztonsági mentési feladatot az adatok védelme és az összes helyreállítási pont törlése után.
  - A biztonsági mentési adatriasztások törléséről értesítő e-mailt fog kapni, amely üzenettel *törli a biztonsági mentési tétel adatait. Ezek az adatok 14 napig átmenetileg elérhetők lesznek, ami után véglegesen törlődik,* és a javasolt művelet *14 napon belül újra védetté teszi a biztonsági mentési tételt az adatok helyreállításához.*
  - A védelem folytatásához a törlési művelettől számított 14 napon belül meg kell oldania a védelmet.

### <a name="stop-protection-and-retain-backup-data"></a>A védelem leállítása és a biztonsági mentési adat megőrzése

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek ablaktáblára**, és **válassza a biztonsági mentés időzítése elemet**.
    ![az ütemezett biztonsági mentést.](./media/backup-azure-manage-mars/mars-actions.png)
1. A **házirend elemének kiválasztása** lapon válassza **a fájlok és mappák biztonsági mentési ütemtervének módosítása** lehetőséget, majd kattintson a **tovább**gombra.
    ![az ütemezett biztonsági mentést.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás ezzel a biztonsági mentési ütemezéssel lehetőséget, de a tárolt biztonsági mentéseket tartsa meg, amíg újra nem aktiválja az ütemezést**. Ezután válassza a **Tovább** lehetőséget.  
    ![az ütemezett biztonsági mentést.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Az **ütemezett biztonsági mentés szüneteltetése** lapon tekintse át az adatokat, majd kattintson a **Befejezés** gombra ![az ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. a **biztonsági mentési folyamat módosítása** alatt jelölje be az ütemezett biztonsági mentés szüneteltetésének sikerességét, és kattintson a **Bezárás** gombra a befejezéshez.

### <a name="stop-protection-and-delete-backup-data"></a>Védelem leállítása és biztonsági másolatok törlése

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek** ablaktáblára, és válassza a **biztonsági mentés időzítése**elemet.
2. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás a biztonsági mentési ütemezés használatával lehetőséget, és törölje az összes tárolt biztonsági**mentést. Ezután válassza a **Tovább** lehetőséget.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az **ütemezett biztonsági mentés leállítása** lapon válassza a **Befejezés**lehetőséget.

    ![Állítsa le az ütemezett biztonsági mentést.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer felszólítja, hogy adjon meg egy biztonsági PIN-kódot (személyes azonosító számot), amelyet manuálisan kell előkészítenie. Ehhez először jelentkezzen be a Azure Portalba.
5. Lépjen a **Recovery Services** tároló > **Beállítások** > **Tulajdonságok menüpontra**.
6. A **biztonsági PIN-kód**területen válassza a **készítés**elemet. Másolja ezt a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson **az OK gombra**.

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A **biztonsági mentési folyamat módosítása** lapon a következő üzenet jelenik meg: a *törölt biztonsági mentési adat 14 napig megmarad. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés.*  

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a portál következő lépéseit.

## <a name="re-enable-protection"></a>Védelem újbóli engedélyezése

Ha leállította a védelmet, miközben megtartja az adatvédelmet, és úgy döntött, hogy folytatja a védelmet, akkor a biztonsági mentési szabályzat módosításával újból engedélyezheti a biztonsági mentést.

1. A **műveletek** lapon válassza az **ütemezett biztonsági mentés**lehetőséget.
1. Válassza a **biztonsági mentési ütemterv újbóli engedélyezése lehetőséget. A biztonsági másolati elemeket vagy a fogakat is módosíthatja** , és kattintson a **tovább**gombra.
    ![törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. A **biztonsági mentés elemek kijelölése lapon**kattintson a **tovább**gombra.
    ![törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-manage-mars/re-enable-next.png)
1. A **biztonsági mentés**időpontjának megadása lapon válassza ki a biztonsági mentés ütemtervét, és kattintson a **tovább**gombra.
1. A **megőrzési szabály kiválasztása**területen adja meg a megőrzési időtartamot, majd kattintson a **tovább**gombra.
1. Végül a **megfelelőség** képernyőn tekintse át a szabályzat részleteit, és kattintson a **Befejezés**gombra.

## <a name="next-steps"></a>Következő lépések

- A támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos információkért tekintse meg a [Mars támogatási mátrixát](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- További információ az [igény szerinti biztonsági mentési szabályzat megőrzési viselkedéséről](backup-configure-vault.md#on-demand-backup-policy-retention-behavior).
