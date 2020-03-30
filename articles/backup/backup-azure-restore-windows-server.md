---
title: Adatok visszaállítása az Azure-ban Windows-kiszolgálóra
description: Ebből a cikkből megtudhatja, hogyan állíthatja vissza az Azure-ban tárolt adatokat windowsos kiszolgálóra vagy Windows-számítógépre a Microsoft Azure Recovery Services (MARS) ügynökkel.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 25ca8eecaeb615f071340188a23fae7978ddb75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409813"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Fájlok visszaállítása a Windows rendszerbe az Azure Resource Manager telepítési modelljének használatával

Ez a cikk bemutatja, hogyan lehet visszaállítani az adatokat egy biztonsági mentési tárolóból. Az adatok visszaállításához használja az Adatok helyreállítása varázslót a Microsoft Azure Recovery Services (MARS) ügynökben. A következőket teheti:

* Adatok visszaállítása ugyanarra a gépre, amelyről a biztonsági mentések készültek.
* Adatok visszaállítása egy másik gépre.

Az Azonnali visszaállítás funkcióval egy írható helyreállítási pont pillanatképét csatlakoztathatja helyreállítási kötetként. Ezután megismerheti a helyreállítási kötetet, és fájlokat másolhat a helyi számítógépre, így szelektíven visszaállíthatja a fájlokat.

> [!NOTE]
> [2017. januári Azure Backup-frissítés](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) re van szükség, ha az azonnali visszaállítással szeretné visszaállítani az adatokat. Emellett a biztonsági mentési adatokat a támogatási cikkben felsorolt területi beállításokban lévő tárolókban is védeni kell. Az azonnali visszaállítást támogató területi beállítások at a [2017 januári Azure Backup frissítésében](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) tekintheti meg.
>

Használja az azonnali visszaállítást a Recovery Services-tárolókkal az Azure Portalon. Ha a biztonsági másolat tárolóiban tároltadatokat a rendszer helyreállítási szolgáltatások tárolóivá konvertálta őket. Ha az Azonnali visszaállítást szeretné használni, töltse le a MARS frissítést, és kövesse az Azonnali visszaállítást megemlítő eljárásokat.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Adatok visszaállítása ugyanarra a gépre az Azonnali visszaállítás funkcióval

Ha véletlenül törölt egy fájlt, és vissza szeretné állítani ugyanarra a számítógépre (ahonnan a biztonsági mentés készült), a következő lépések segítenek az adatok helyreállításában.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, hol volt telepítve a beépülő modul, keressen a számítógépen vagy a **kiszolgálón**a Microsoft Azure Backup .

    Az asztali alkalmazásnak meg kell jelennie a keresési eredmények között.

2. A varázsló elindításához válassza az **Adatok helyreállítása** lehetőséget.

    ![Képernyőkép az Azure Backup biztonsági mentéséről, kiemelve az Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

3. Az **Első lépések** lapon az adatok ugyanarra a kiszolgálóra vagy számítógépre való visszaállításához válassza a Ez a **kiszolgáló (`<server name>`)** > **Tovább**lehetőséget.

    ![Képernyőkép: Az Adatok helyreállítása varázsló első lépései lap](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. A **Helyreállítási mód kiválasztása** lapon válassza **az Egyéni fájlok és mappák** > **A Tovább lehetőséget.**

    ![Képernyőkép: Az Adatok helyreállítása varázsló helyreállítási mód kiválasztása lapja](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Az egyes fájlok és mappák visszaállításához a .NET Framework 4.5.2-es vagy újabb verziószükséges. Ha nem látja az **Egyedi fájlok és mappák** lehetőséget, frissítenie kell a .NET Framework rendszert a 4.5.2-es vagy újabb verzióra, majd próbálkozzon újra.
 
   > [!TIP]
   > Az **Egyes fájlok és mappák** beállítás lehetővé teszi a helyreállítási pont adatainak gyors elérését. Alkalmas az egyes fájlok helyreállítására, amelyek mérete nem haladja meg a 80 GB-ot, és akár 6 Mb/s átviteli vagy másolási sebességet kínál a helyreállítás során. A **Kötet** beállítás helyreállítja az összes biztonsági másolatot egy adott köteten. Ez a beállítás gyorsabb adatátviteli sebességet biztosít (akár 60 Mb/s), amely ideális nagy méretű adatok vagy teljes kötetek helyreállításához.

5. A **Kötet és dátum kiválasztása** lapon jelölje ki a visszaállítani kívánt fájlokat és mappákat tartalmazó kötetet.

    A naptárban jelöljön ki egy helyreállítási pontot. A **félkövérrel szedett** dátumok legalább egy helyreállítási pont elérhetőségét jelzik. Ha egy adott napon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **Idő** legördülő menüből.

    ![Képernyőkép: Az Adatok helyreállítása varázsló Kötet és dátum kiválasztása lap](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, válassza a **Mount**lehetőséget.

    Az Azure Backup csatlakoztatja a helyi helyreállítási pontot, és helyreállítási kötetként használja.

7. A **Tallózás és a Fájlok helyreállítása** lapon válassza a **Tallózás** gombot a Windows Intéző megnyitásához, és keresse meg a kívánt fájlokat és mappákat.

    ![Képernyőkép: Az Adatok helyreállítása varázsló tallózása és helyreállása lap](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. A Windows Intézőben másolja a visszaállítani kívánt fájlokat és mappákat, és illessze be őket a kiszolgáló vagy a számítógép helyi helyére. A fájlokat közvetlenül a helyreállítási kötetről nyithatja meg vagy streamelheti, és ellenőrizheti, hogy a megfelelő verziókat javítja-e.

    ![Képernyőkép a Windows Intézőről, kiemelt Másolás](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Ha végzett, a **Tallózás és helyreállító fájlok** lapon válassza a **Leválasztás**lehetőséget. Ezután válassza az **Igen** lehetőséget a kötet leválasztásának megerősítéséhez.

    ![Képernyőkép: Az Adatok helyreállítása varázsló tallózása és helyreállása lap](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem választja a **Leválasztás**lehetőséget, a helyreállítási kötet a csatlakoztatástól kezdve 6 órán át csatlakoztatva marad. Folyamatban lévő fájlmásolás esetén azonban a csatlakoztatási idő legfeljebb 24 órára hosszabbodik meg. A kötet csatlakoztatása közben nem futnak biztonsági mentési műveletek. A kötet csatlakoztatása korként ütemezett biztonsági mentési műveletek a helyreállítási kötet leválasztása után futnak.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Adatok visszaállítása alternatív gépre az Azonnali visszaállítás funkcióval

Ha a teljes kiszolgáló elvész, továbbra is helyreállíthatja az adatokat az Azure Backup egy másik gépre. A következő lépések a munkafolyamatot szemléltetik.

Ezek a lépések a következő terminológiát tartalmazzák:

* *Forrásgép* – Az eredeti gép, amelyről a biztonsági mentés készült, és amely jelenleg nem érhető el.
* *Célgép* – Az a gép, amelyre az adatok helyreállnak.
* *Mintatároló* – A Recovery Services-tároló, amelyhez a forrásgép és a célgép regisztrálva van.

> [!NOTE]
> A biztonsági mentések nem állíthatók vissza olyan célszámítógépre, amely az operációs rendszer egy korábbi verzióját futtatja. Például egy Windows 7 rendszerű számítógépről készített biztonsági másolat visszaállítható windows 7 (vagy újabb) számítógépen. A Windows 8 rendszerű számítógépekről készített biztonsági másolat nem állítható vissza Windows 7 rendszerű számítógépre.
>
>

1. Nyissa meg a **Microsoft Azure biztonsági mentés** beépülő modult a célgépen.

2. Győződjön meg arról, hogy a célgép és a forrásgép ugyanahhoz a Recovery Services-tárolóhoz van regisztrálva.

3. Az **Adatok helyreállítása** varázsló megnyitásához válassza az Adatok **helyreállítása varázsló**megnyitásához.

    ![Képernyőkép az Azure Backup biztonsági mentéséről, kiemelve az Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

4. Az **Első lépések** lapon válassza a **Másik kiszolgáló**lehetőséget.

    ![Képernyőkép: Az Adatok helyreállítása varázsló első lépései lap](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Adja meg a tároló hitelesítő fájlját, amely megfelel a mintatárolónak, és válassza a **Tovább**gombot.

    Ha a tároló hitelesítő fájl érvénytelen (vagy lejárt), töltse le az új tároló hitelesítő fájl az Azure Portalon a mintatárolóból. Miután megadott egy érvényes tároló hitelesítő adatokat, megjelenik a megfelelő biztonsági mentési tároló neve.

6. A **Tartalékkiszolgáló kiválasztása** lapon válassza ki a forrásgépet a megjelenített gépek listájából, és adja meg a jelszót. Ezután válassza a **Tovább**gombot.

    ![Képernyőkép: Az Adatok helyreállítása varázsló Tartalékkiszolgáló kiválasztása lapja](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. A **Helyreállítási mód kiválasztása** lapon válassza **az Egyes fájlok és mappák** > **A Tovább lehetőséget.**

    ![Képernyőkép: Az Adatok helyreállítása varázsló helyreállítási mód kiválasztása lapja](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. A **Kötet és dátum kiválasztása** lapon jelölje ki a visszaállítani kívánt fájlokat és mappákat tartalmazó kötetet.

    A naptárban jelöljön ki egy helyreállítási pontot. A **félkövérrel szedett** dátumok legalább egy helyreállítási pont elérhetőségét jelzik. Ha egy adott napon belül több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **Idő** legördülő menüből.

    ![Képernyőkép: Az Adatok helyreállítása varázsló Kötet és dátum kiválasztása lap](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Válassza a **Csatlakoztatás** lehetőséget a helyreállítási pont helyi csatlakoztatásához helyreállítási kötetként a célgépen.

10. A **Tallózás és a Fájlok helyreállítása** lapon válassza a **Tallózás** gombot a Windows Intéző megnyitásához, és keresse meg a kívánt fájlokat és mappákat.

    ![Képernyőkép: Az Adatok helyreállítása varázsló tallózása és helyreállása lap](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. A Windows Intézőben másolja a fájlokat és mappákat a helyreállítási kötetről, és illessze be őket a célszámítógép helyére. A fájlokat közvetlenül a helyreállítási kötetről nyithatja meg vagy streamelheti, és ellenőrizheti, hogy a megfelelő verziók helyreállnak-e.

    ![Képernyőkép a Windows Intézőről, kiemelt Másolás](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Ha végzett, a **Tallózás és helyreállító fájlok** lapon válassza a **Leválasztás**lehetőséget. Ezután válassza az **Igen** lehetőséget a kötet leválasztásának megerősítéséhez.

    ![Képernyőkép: Az Adatok helyreállítása varázsló tallózása és helyreállása lap](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem választja a **Leválasztás**lehetőséget, a helyreállítási kötet a csatlakoztatástól kezdve 6 órán át csatlakoztatva marad. Folyamatban lévő fájlmásolás esetén azonban a csatlakoztatási idő legfeljebb 24 órára hosszabbodik meg. A kötet csatlakoztatása közben nem futnak biztonsági mentési műveletek. A kötet csatlakoztatása korként ütemezett biztonsági mentési műveletek a helyreállítási kötet leválasztása után futnak.
    >

## <a name="next-steps"></a>További lépések

* Most, hogy helyreállt a fájlok és mappák, kezelheti [a biztonsági másolatokat.](backup-azure-manage-windows-server.md)

* [Gyakori kérdések a fájlok és mappák biztonsági mentésével kapcsolatban.](backup-azure-file-folder-backup-faq.md)
