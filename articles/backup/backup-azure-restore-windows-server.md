---
title: Helyreállíthatja az adatokat az Azure-ban a Windows server vagy a Windows-számítógép
description: Ismerje meg, hogyan állíthatja vissza egy Windows server vagy Windows-számítógép az Azure-ban tárolt adatok.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: 1e8e9365567c19400b86dc60d966eb965b83591d
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281751"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Fájlok visszaállítása Windows az Azure Resource Manager-alapú üzemi modell használatával

Ez a cikk bemutatja, hogyan lehet egy biztonsági mentési tárolóból az adatok helyreállítását. Az adatok helyreállítását, használja az adatok helyreállítása varázsló a Microsoft Azure Recovery Services-(MARS) ügynök. A következőket teheti:

* Helyreállíthatja az adatokat ugyanarra a számítógépre, amelyről a biztonsági mentések származnak.
* Adatok visszaállítása egy másik gépre.

Az azonnali visszaállítása szolgáltatás használatával csatlakoztassa egy írható helyreállítási pont pillanatképének helyreállítási kötetként. Ezután megismerheti a helyreállítási kötetet, és átmásolja a fájlokat egy helyi számítógépre, ezáltal visszaállítása fájlok.

> [!NOTE]
> A [2017 január Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) szükség, ha azonnali visszaállítás segítségével visszaállíthatja adatokat szeretne. A biztonsági mentési adatok is, a tárolók a területi beállítások a támogatási cikkben felsorolt kell levédeni. Tekintse át a [2017 január Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) területi beállításokat, amelyek támogatják az azonnali visszaállítása legújabb listáját.
>

Használja az azonnali Restore with Recovery Services-tárolók az Azure Portalon. Backup-tárolók adatok tárolva, ha azok konvertált Recovery Services-tárolók. Ha azt szeretné, azonnali-visszaállítás, töltse le a MARS-frissítést, és hajtsa végre, amelyek említik, azonnali visszaállítása.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Azonnali visszaállítás segítségével helyreállíthatja azokat ugyanarra a gépre

Ha véletlenül törölte a fájlt, és visszaállítja azt (, amelyről a biztonsági mentés készül) ugyanarra a gépre, a következő lépések segítséget nyújt az adatok helyreállításához.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, ahol a beépülő modul telepítve van, keresse a számítógép vagy kiszolgáló **a Microsoft Azure Backup**.

    A keresési eredmények között meg kell jelennie az asztali alkalmazás.

2. Válassza ki **adatok helyreállítása** varázsló elindításához.

    ![Képernyőkép az Azure Backup szolgáltatással, az adatok helyreállítása kiemelésével](./media/backup-azure-restore-windows-server/recover.png)

3. Az a **bevezetés** adatok ugyanazon a kiszolgálón vagy számítógépen való visszaállításához jelölje be **ezen a kiszolgálón (`<server name>`)** > **tovább**.

    ![Képernyőkép a helyreállítás adatok varázsló első lépések lap](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Az a **helyreállítási mód kiválasztása** lapon a **egyes fájlok és mappák** > **tovább**.

    ![Képernyőkép a helyreállítás adatok varázsló helyreállítási mód kiválasztása lap](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
  > [!IMPORTANT]
  > Az egyes fájlok és mappák visszaállítása beállítást igényel a .NET-keretrendszer 4.5.2-es vagy újabb. Ha nem látja a **egyes fájlok és mappák** lehetőséget, frissítenie kell .NET-keretrendszer 4.5.2-es vagy újabb, illetve, és próbálkozzon újra.

  > [!TIP]
  > A **egyes fájlok és mappák** beállítás lehetővé teszi a helyreállítási pont adataiban gyors eléréséhez. Ideális választás az egyes fájlok helyreállításához összesen nem lehet hosszabb 80 GB méretű, és átvitel kínál, vagy a példány legfeljebb 6 MB/s lerövidíti a helyreállítás során. A **kötet** beállítást egy adott kötet összes biztonsági másolatok állítja helyre. Ezt a beállítást biztosít gyorsabb átviteli sebessége (akár 60 MB/s), amely ideális a nagy méretű adatok vagy a teljes kötet visszaállítása.

5. Az a **kötet és dátum kiválasztása** lapra, jelölje be a fájlok és a visszaállítani kívánt mappákat tartalmazó kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A dátumok **félkövér** azt jelzik, legalább egy helyreállítási pont rendelkezésre állását. Ha több helyreállítási pont érhető el belül csak egy időpontot, válassza ki az adott helyreállítási pontot a **idő** legördülő menüből.

    ![Képernyőkép a helyreállítás adatok varázsló kötet és dátum kiválasztása lap](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Miután kiválasztotta a helyreállítási pontot a visszaállításhoz, válassza ki a **csatlakoztatási**.

    Az Azure Backup a helyi helyreállítási pontot csatlakoztatja, és használja azt, mint a helyreállítási kötet.

7. Az a **tallózása és a fájlok helyreállítása** lapon jelölje be **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről.

    ![Képernyőkép a helyreállítás adatok varázsló keresse meg és a fájlok helyreállítása](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. A Windows Explorerben másolja a fájlokat és mappákat szeretné visszaállítani, és illessze be őket a helyi kiszolgáló vagy számítógép bármely helyére. Nyissa meg a vagy adatfolyam a fájlok közvetlenül a helyreállítási kötetet, és győződjön meg arról, hogy a megfelelő verzióra állít helyre.

    ![Képernyőkép a Windows Explorerben másolással kiemelésével](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Ha elkészült, az a **tallózása és a fájlok helyreállítása** lapon jelölje be **leválasztás**. Válassza ki **Igen** annak ellenőrzéséhez, hogy szeretné-e választani a kötetet.

    ![Képernyőkép a helyreállítás adatok varázsló keresse meg és a fájlok helyreállítása](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem jelöli be **leválasztás**, 6 órán át kezdve ha csatlakoztatva volt a helyreállítási kötet csatlakoztatva maradnak. A csatlakozási idő azonban ki van bővítve, legfeljebb 24 órás egy folyamatban lévő fájlmásolás esetén. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva van. Az idő, amikor a kötet csatlakoztatva van-e ütemezve bármely biztonsági mentési művelet után a helyreállítási kötetet leválasztani fog futni.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Azonnali visszaállítás segítségével visszaállíthatja az adatokat, egy másik számítógépre
Ha a teljes kiszolgáló elveszett, továbbra is helyreállíthatja az adatokat az Azure Backup egy másik gépre. A következő lépések bemutatják a munkafolyamatot.


Ezeket a lépéseket a következő kifejezésekkel tartalmazza:

* *Forrásgép* – az eredeti gépet, amely a biztonsági mentéshez, és amely jelenleg nem érhető el.
* *Célszámítógép* – a gép, amelyre az adatok helyreállítása folyik.
* *Minta tároló* – a Recovery Services-tároló, amely a forrásgép és a célként megadott gép regisztrálva vannak. <br/>

> [!NOTE]
> Biztonsági mentések nem lehet visszaállítani a célszámítógépen az operációs rendszer korábbi verzióját futtató. Például egy biztonsági egy Windows 7 rendszerű számítógépen a Windows 8 (vagy újabb) állíthatók vissza. Egy biztonsági egy Windows 8 rendszerű számítógép nem állítható vissza egy Windows 7 rendszerű számítógépen.
>
>

1. Nyissa meg a **a Microsoft Azure Backup** beépülő modult a célszámítógépen.

2. Győződjön meg arról, hogy a célszámítógép és a forrásgép ugyanabban a helyreállítási tárban regisztrált.

3. Válassza ki **adatok helyreállítása** megnyitásához a **adat-helyreállító varázslóban**.

    ![Képernyőkép az Azure Backup szolgáltatással, az adatok helyreállítása kiemelésével](./media/backup-azure-restore-windows-server/recover.png)

4. Az a **bevezetés** lapon jelölje be **egy másik kiszolgálóra**.

    ![Képernyőkép a helyreállítás adatok varázsló első lépések lap](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Adja meg a tároló hitelesítő adatait tartalmazó fájlt, amely megfelel a minta-tárba, és válassza ki **tovább**.

    Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), töltse le a tároló új hitelesítőadat-fájlja a minta tárolóból az Azure Portalon. Miután megadta a egy érvényes tároló hitelesítő adatait, a megfelelő biztonsági mentési tár neve jelenik meg.


6. Az a **válassza ki a Backup Server** lapon válassza ki a forrásgép megjelenített gépet a listából, és adja meg a jelszót. Ezután kattintson a **Tovább** gombra.

    ![Képernyőképe az helyreállítása adatok varázsló Backup-kiszolgáló kiválasztása lap](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Az a **helyreállítási mód kiválasztása** lapra, jelölje be **egyes fájlok és mappák** > **tovább**.

    ![Képernyőkép a helyreállítás adatok varázsló helyreállítási mód kiválasztása lap](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Az a **kötet és dátum kiválasztása** lapra, jelölje be a fájlok és a visszaállítani kívánt mappákat tartalmazó kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. A dátumok **félkövér** azt jelzik, legalább egy helyreállítási pont rendelkezésre állását. Ha több helyreállítási pont érhető el belül csak egy időpontot, válassza ki az adott helyreállítási pontot a **idő** legördülő menüből.

    ![Képernyőkép a helyreállítás adatok varázsló kötet és dátum kiválasztása lap](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Válassza ki **csatlakoztatási** helyileg csatlakoztatni a helyreállítási pont a helyreállítási kötetet, a célgépen.

10. Az a **fájlok tallózása és a helyreállítás** lapon jelölje be **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről.

    ![Képernyőkép az helyreállítása adatok varázsló keresse meg és helyreállítása fájlok lap](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. A Windows Explorerben másolja a fájlokat és mappákat a helyreállítási kötetet, és illessze be őket a célként megadott gép helye. Nyissa meg a vagy adatfolyam a fájlok közvetlenül a helyreállítási kötetet, és győződjön meg arról, hogy a rendszer visszaállítja az megfelelő verziójával.

    ![Képernyőkép a Windows Explorerben másolással kiemelésével](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Ha elkészült, az a **tallózása és a fájlok helyreállítása** lapon jelölje be **leválasztás**. Válassza ki **Igen** annak ellenőrzéséhez, hogy szeretné-e választani a kötetet.

    ![Képernyőkép az helyreállítása adatok varázsló keresse meg és helyreállítása fájlok lap](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem jelöli be **leválasztás**, 6 órán át kezdve ha csatlakoztatva volt a helyreállítási kötet csatlakoztatva maradnak. A csatlakozási idő azonban ki van bővítve, legfeljebb 24 órás egy folyamatban lévő fájlmásolás esetén. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva van. Az idő, amikor a kötet csatlakoztatva van-e ütemezve bármely biztonsági mentési művelet után a helyreállítási kötetet leválasztani fog futni.
    >

## <a name="next-steps"></a>További lépések
Most, hogy a fájlok és mappák helyreállítható már [a biztonsági másolatok kezelése a](backup-azure-manage-windows-server.md).
