---
title: A MARS-ügynök biztonsági másolatainak kezelése és figyelése
description: Ismerje meg, hogyan kezelheti és figyelheti a Microsoft Azure Recovery Services (MARS) ügynökbiztonsági mentéseket az Azure Backup szolgáltatás használatával.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a88ec4dc9283114e06eed424172dbb958850c2e9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025101"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>A Microsoft Azure Recovery Services (MARS) ügynökbiztonsági mentések kezelése az Azure Backup szolgáltatás használatával

Ez a cikk a Microsoft Azure Recovery Services Agent szolgáltatással biztonsági mentést tartalmazó fájlok és mappák kezelését ismerteti.

## <a name="modify-a-backup-policy"></a>Biztonsági mentési házirend módosítása

A biztonsági mentési házirend módosításakor új elemeket vehet fel, eltávolíthatja a meglévő elemeket a biztonsági mentésből, vagy kizárhatja a fájlok biztonsági mentését a Kizárási beállítások használatával.

- **Az Elemek hozzáadása beállítás** csak új elemek hozzáadására használható biztonsági másolatot. Meglévő elemek eltávolításához használja az **Elemek eltávolítása** vagy **a Kizárási beállítások lehetőséget.**  
- **Az Elemek eltávolítása** ezzel a beállítással távolíthatja el az elemek biztonsági származtatását.
  - Az **Elemek eltávolítása**helyett a **Kizárási beállítások segítségével** távolíthatja el a köteten belüli összes elemet.
  - Ha törli az összes kijelölést egy köteten, akkor az elemek régi biztonsági másolatai az utolsó biztonsági mentés időpontjában megőrzési beállításokként maradnak meg, módosítási lehetőség nélkül.
  - Ezeknek az elemeknek az újbóli kijelölése az első teljes biztonsági mentéshez vezet, és az új házirend-módosítások nem lesznek alkalmazva a régi biztonsági mentésekre.
  - A teljes kötet kijelölésének megszüntetése megőrzi a korábbi biztonsági mentést anélkül, hogy módosítani adná az adatmegőrzési házirendet.
- **A Kizárási beállítások ezzel** a beállítással zárhat ki bizonyos elemeket a biztonsági másolatokból.

### <a name="add-new-items-to-existing-policy"></a>Új elemek hozzáadása a meglévő házirendhez

1. A **Műveletek ben**kattintson a Biztonsági másolat **ütemezése gombra.**

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

2. A **Házirendelem kiválasztása** lapon válassza **a Fájlok és mappák biztonsági másolatütemezésének módosítása** lehetőséget, majd kattintson a **Tovább**gombra.

    ![Házirendelemek kiválasztása](./media/backup-azure-manage-mars/select-policy-items.png)

3. A **Biztonsági másolat módosítása vagy leállítása** lapon válassza a **Biztonságimásolat-elemek vagy -időpontok módosítása** lehetőséget, majd kattintson a **Tovább**gombra.

    ![Biztonsági mentés módosítása vagy ütemezése](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. A **Biztonsági másolat hoz lehetőséget tartalmazó elemek kiválasztása** lapon kattintson az Elemek **hozzáadása** gombra a biztonsági másolatot készíteni kívánt elemek hozzáadásához.

    ![Elemek biztonsági másolatának módosítása vagy ütemezése](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Az **Elemek kiválasztása** ablakban jelölje ki a hozzáadni kívánt legyeket vagy mappákat, majd kattintson az **OK**gombra.

    ![Az elemek kijelölése](./media/backup-azure-manage-mars/select-item.png)

6. Végezze el a következő lépéseket, és kattintson a **Befejezés** gombra a művelet befejezéséhez.

### <a name="add-exclusion-rules-to-existing-policy"></a>Kizárási szabályok hozzáadása a meglévő házirendhez

Kizárási szabályok hozzáadásával kihagyhatja azokat a fájlokat és mappákat, amelyekről nem szeretne biztonsági másolatot tartani. Ezt új házirend definiálásakor vagy meglévő házirend módosításakor teheti meg.

1. A Műveletek ablaktáblán kattintson a **Biztonsági másolat ütemezése gombra.** Nyissa meg a **Biztonsági másolathoz elemet kijelölő elemek et,** és kattintson **a Kizárási beállítások gombra.**

    ![Az elemek kijelölése](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. A **Kizárási beállítások**csoportban kattintson **a Kizárás hozzáadása gombra.**

    ![Az elemek kijelölése](./media/backup-azure-manage-mars/add-exclusion.png)

3. Az **Elemek kijelölése elemet listában**tallózással keresse meg a fájlokat és mappákat, jelölje ki a kizárni kívánt elemeket, majd kattintson az **OK**gombra.

    ![Az elemek kijelölése](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Alapértelmezés szerint a kijelölt mappákban lévő összes **almappa** ki van zárva. Ezt az **Igen** vagy a **Nem**lehetőséget választva módosíthatja. A kizárandó fájltípusokat az alábbi módon szerkesztheti és megadhatja:

    ![Az elemek kijelölése](./media/backup-azure-manage-mars/subfolders-type.png)

5. Végezze el a következő lépéseket, és kattintson a **Befejezés** gombra a művelet befejezéséhez.

### <a name="remove-items-from-existing-policy"></a>Elemek eltávolítása a meglévő házirendből

1. A Műveletek ablaktáblán kattintson a **Biztonsági másolat ütemezése gombra.** Nyissa meg a **Biztonsági másolathoz szükséges elemek kiválasztása lehetőséget.** A listából jelölje ki azokat a fájlokat és mappákat, amelyeket el szeretne távolítani a biztonsági mentésütemezésből, és kattintson **az Elemek eltávolítása gombra.**

    ![Az elemek kijelölése](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Óvatosan járjon el, ha teljesen eltávolít egy kötetet a házirendből.  Ha újra hozzá kell adnia, akkor a lesz kezelve, mint egy új kötetet. A következő ütemezett biztonsági mentés a növekményes biztonsági mentés helyett kezdeti biztonsági mentést (teljes biztonsági mentést) hajt végre. Ha később ideiglenesen el kell távolítania és elemeket kell hozzáadnia, akkor az **Elemek eltávolítása** helyett ajánlott **a Kizárási beállítások használata** a teljes biztonsági mentés helyett a növekményes biztonsági mentés biztosításához.

2. Végezze el a következő lépéseket, és kattintson a **Befejezés** gombra a művelet befejezéséhez.

## <a name="stop-protecting-files-and-folder-backup"></a>A fájlok és mappák biztonsági másolatának védelmének leállítása

A Fájlok és mappák biztonsági mentésének védelmét kétféleképpen szüntetheti meg:

- **A védelem leállítása és a biztonsági mentési adatok megőrzése**.
  - Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladatok védelme.
  - Az Azure Backup szolgáltatás továbbra is megtartja az összes meglévő helyreállítási pontokat.  
  - A le nem járt helyreállítási pontok biztonsági mentési adatait visszaállíthatja.
  - Ha úgy dönt, hogy folytatja a védelmet, akkor használhatja a *Biztonsági mentés ütemezésének újbóli engedélyezése* lehetőséget. Ezt követően az adatok az új adatmegőrzési szabály alapján maradnak meg.
- **A védelem leállítása és a biztonsági mentési adatok törlése**.
  - Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladatot az adatok védelmében, és törli az összes helyreállítási pontot.
  - Kapni fog egy törlés Biztonsági mentési adatok riasztási e-mailt egy üzenettel *Az adatok ehhez a biztonsági másolat elem törölték. Ezek az adatok ideiglenesen 14 napig lesznek elérhetők, majd véglegesen törlődnek,* és ajánlott művelet *Az adatok helyreállításához 14 napon belül újra védi a biztonsági mentési elemet.*
  - A védelem folytatásához a törlési művelettől számított 14 napon belül újra védje meg újra.

### <a name="stop-protection-and-retain-backup-data"></a>A védelem leállítása és a biztonsági mentési adatok megőrzése

1. Nyissa meg a MARS felügyeleti konzolt, nyissa meg a **Műveletek ablaktáblát,** és **válassza a Biztonsági másolat ütemezése lehetőséget.**

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/mars-actions.png)
1. A **Házirendelem kiválasztása** lapon válassza **a Fájlok és mappák biztonsági ütemezésének módosítása** lehetőséget, majd kattintson a **Tovább**gombra.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Az **Ütemezett biztonsági másolat módosítása vagy leállítása** lapon válassza a Biztonsági másolat **használatának leállítása lehetőséget, de a tárolt biztonsági másolatok megtartása az ütemezés ismételt aktiválásáig.** Ezután válassza a **Tovább gombot.**

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Az **Ütemezett biztonsági másolat szüneteltetése csoportban** tekintse át az adatokat, és kattintson a Befejezés **gombra.**

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. A **Biztonsági mentési folyamat módosítása lapon** ellenőrizze, hogy az ütemezés biztonsági mentésének szüneteltetése sikeres állapotban van-e, és kattintson a **befejezéshez.**

### <a name="stop-protection-and-delete-backup-data"></a>A védelem leállítása és a biztonsági mentési adatok törlése

1. Nyissa meg a MARS felügyeleti konzolt, nyissa meg a **Műveletek** ablaktáblát, és válassza **a Biztonsági másolat ütemezése**lehetőséget.
2. Az **Ütemezett biztonsági másolat módosítása vagy leállítása** lapon válassza a Biztonsági másolat **használatának leállítása lehetőséget, és törölje az összes tárolt biztonsági másolatot**. Ezután válassza a **Tovább gombot.**

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az **Ütemezett biztonsági másolat leállítása** lapon válassza a **Befejezés gombot.**

    ![Állítsa le az ütemezett biztonsági mentést.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer kéri, hogy adjon meg egy biztonsági PIN-kódot (személyes azonosítószámot), amelyet manuálisan kell létrehoznia. Ehhez először jelentkezzen be az Azure Portalon.
5. Nyissa meg a Helyreállítási szolgáltatások > **tárolóbeállításainak** > **tulajdonságait.** **Recovery Services vault**
6. A **Biztonsági PIN csoportban**válassza a **Létrehozás lehetőséget.** Másolja a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson az **OK gombra.**

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A **Biztonsági másolat módosítása folyamata** lapon a következő üzenet jelenik meg: *A törölt biztonsági mentési adatok 14 napig megmaradnak. Ezt követően a biztonsági mentési adatok véglegesen törlődnek.*  

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlése után kövesse a portál következő lépéseit.

## <a name="re-enable-protection"></a>Védelem újbóli engedélyezése

Ha leállította a védelmet az adatok megőrzése közben, és úgy döntött, hogy folytatja a védelmet, akkor újra engedélyezheti a biztonsági mentésütemezést a biztonsági mentési házirend módosítása használatával.

1. A **Műveletek oldalon** válassza a Biztonsági mentés **ütemezése**lehetőséget.
1. Válassza **a Biztonsági mentés ütemezésének újraengedélyezése lehetőséget. Módosíthatja a biztonsági másolat elemeit vagy az időpontokat is,** majd kattintson a **Tovább**gombra.<br>

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. A **Biztonsági másolatban a Biztonsági másolatban**kattintson a **Tovább**gombra.

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-manage-mars/re-enable-next.png)
1. A **Biztonsági másolat ütemezésének megadása**területen adja meg a biztonsági mentés ütemezését, majd kattintson a **Tovább**gombra.
1. Az **Adatmegőrzési szabály kiválasztása csoportban**adja meg az adatmegőrzési időtartamot, majd kattintson a **Tovább**gombra.
1. Végül a **Megerősítés** képernyőn tekintse át a szabályzat részleteit, és kattintson a **Befejezés gombra.**

## <a name="re-generate-passphrase"></a>Jelszó újbóli generálása

A jelszó az adatok titkosítására és visszafejtésére szolgál, miközben biztonsági másolatot készít a helyszíni vagy helyi gépről a MARS-ügynök használatával az Azure-ba vagy az Azure-ból. Ha elvesztette vagy elfelejtette a jelszót, akkor regenerálhatja a jelszót (feltéve, hogy a gép még mindig regisztrálva van a Recovery Services Vaultban, és a biztonsági mentés konfigurálva van) az alábbi lépésekkel:

- A MARS-ügynök konzolon nyissa meg a Műveletek > **panelmódosítási tulajdonságok at** >. **Actions Pane** Ezután lépjen a **Titkosítás lapra.**<br>
- Jelölje be **a Jelszó módosítása** jelölőnégyzetet.<br>
- Írjon be egy új jelszót, vagy kattintson **a Jelszó generálása gombra.**
- Az új jelszó mentéséhez kattintson a **Tallózás** gombra.

    ![Jelszó létrehozása.](./media/backup-azure-manage-mars/passphrase.png)
- A módosítások alkalmazásához kattintson az **OK** gombra.  Ha a [biztonsági funkció](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) engedélyezve van az Azure Portalon a Recovery Services Vault, majd meg kell adnia a biztonsági PIN-kódot. A PIN-kód fogadásához kövesse a [cikkben](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)leírt lépéseket.<br>
- Illessze be a biztonsági PIN-kódot a portálról, és kattintson az **OK** gombra a módosítások alkalmazásához.<br>

    ![Jelszó létrehozása.](./media/backup-azure-manage-mars/passphrase2.png)
- Győződjön meg arról, hogy a jelszó biztonságosan menti egy másik helyen (kivéve a forrásgép), lehetőleg az Azure Key Vault. Kövesse nyomon az összes jelszót, ha több gépről is biztonsági másolatot készül a MARS-ügynökökkel.

## <a name="next-steps"></a>További lépések

- A támogatott forgatókönyvekről és korlátozásokról a [MARS-ügynök támogatási mátrixában](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)talál.
- További információ [az igény szerinti biztonsági mentési házirendek megtartásának viselkedéséről.](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)
