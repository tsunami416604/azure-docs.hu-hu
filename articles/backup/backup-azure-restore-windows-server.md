---
title: Helyreállíthatja az adatokat az Azure-ban a Windows Server vagy a Windows-számítógép
description: Ismerje meg, hogyan állíthatja vissza egy Windows Server vagy Windows-számítógép az Azure-ban tárolt adatokkal.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: 20d2f289f4d40d773fde9f6b770dc49b87c34804
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297247"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Fájlok visszaállítása a Windows-kiszolgálóra vagy -ügyfélre a Resource Manager-alapú üzemi modell használatával

Ez a cikk bemutatja, hogyan lehet egy biztonsági mentési tárolóból az adatok helyreállítását. Az adatok helyreállítását, használja az adatok helyreállítása varázsló a Microsoft Azure Recovery Services-(MARS) ügynök. Ha az adatok visszaállításához, lehetőség:

* Helyreállíthatja az adatokat ugyanarra a számítógépre, amelyről a biztonsági mentések származnak.
* Adatok visszaállítása egy másik számítógépre.

A Microsoft a 2017 január, amely egy előzetes verziójának frissítése a MARS-ügynök. Hibajavításokat tartalmaz, valamint a frissítés lehetővé teszi, hogy azonnali visszaállítani, amely lehetővé teszi, hogy egy írható helyreállítási pont pillanatképének mint helyreállítási kötet csatlakoztatási. A helyreállítási kötetet, és átmásolja a fájlokat a helyi számítógépre, és ezáltal visszaállítása fájlok majd feltárhatja.

> [!NOTE]
> A [2017 január Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) szükség, ha azonnali visszaállítás segítségével visszaállíthatja adatokat szeretne. A biztonsági mentési adatok is a területi beállítások támogatási cikk szerepel a tárolók kell levédeni. Tekintse át a [2017 január Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) területi beállításokat, amelyek támogatják az azonnali visszaállítása legújabb listáját. Az azonnali helyreállítás **nem** jelenleg az összes területi beállítás érhető el.
>

Használja az azonnali Restore with Recovery Services-tárolók az Azure Portalon. Backup-tárolók adatok tárolva, ha azok konvertált Recovery Services-tárolók. Ha azt szeretné, azonnali-visszaállítás, töltse le a MARS-frissítést, és hajtsa végre, amelyek említik, azonnali visszaállítása.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Azonnali visszaállítás segítségével helyreállíthatja azokat ugyanarra a gépre

Ha véletlenül törölte a fájlt, és vissza szeretné állítani, (, amelyről a biztonsági mentés készül) ugyanarra a számítógépre, a következő lépések segítséget nyújt az adatok helyreállításához.

1. Nyissa meg a **a Microsoft Azure Backup** számértékekhez igazodnak a. Ha nem tudja, ahol a beépülő modul telepítve van, keresse a számítógép vagy kiszolgáló **a Microsoft Azure Backup**.

    A keresési eredmények között meg kell jelennie az asztali alkalmazás.

2. Kattintson a **adatok helyreállítása** varázsló elindításához.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

3. Az a **bevezetés** ugyanazon a kiszolgálón vagy számítógépen, hogy az adatok helyreállítását ablaktáblában válassza **ezen a kiszolgálón (`<server name>`)** kattintson **tovább**.

    ![Állítsa vissza az adatokat ugyanarra a gépre, a kiszolgáló lehetőség kiválasztása](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. A a **helyreállítási mód kiválasztása** panelen válassza a **egyes fájlok és mappák** majd **tovább**.

    ![Fájlok tallózása](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
> [!IMPORTANT]
> Helyreállítható *egyes fájlok és mappák* szükséges a .NET-keretrendszer 4.5.2-es vagy újabb verziója. Ha nem látja a *egyes fájlok és mappák* lehetőséget, frissítenie kell .NET-keretrendszer 4.5.2-es vagy újabb, és próbálkozzon újra.

> [!TIP]
> A *egyes fájlok és mappák* beállítás lehetővé teszi a helyreállítási pont adataiban gyors eléréséhez. Megfelelő az egyes fájlok helyreállítása összesen mérete nem lehet hosszabb 80 GB méretű, és ajánlatok átviteli másolatot legfeljebb 6 MB/s lerövidíti a helyreállítás során. A *kötet* beállítást egy adott kötet összes biztonsági másolat adatait állítja helyre. Ez a beállítás nagyobb átviteli sebességre képes itt (legfeljebb 60 MB/s), amely ideális a számára helyreállítás nagy méretű adatok vagy a teljes kötet.

5. Az a **kötet és dátum kiválasztása** ablaktáblán válassza ki a fájlokat és/vagy a visszaállítani kívánt mappákat tartalmazó kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. Időben bármelyik helyreállítási pontra visszaállíthatja. A dátumok **félkövér** azt jelzik, legalább egy helyreállítási pont rendelkezésre állását. Ha dátum, akkor válassza, ha több helyreállítási pont érhető el, válassza az adott helyreállítási pontot a **idő** legördülő menüből.

    ![Kötet és dátum](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Miután kiválasztotta a helyreállítási pontot a visszaállításhoz, kattintson a **csatlakoztatási**.

    Az Azure Backup a helyi helyreállítási pontot csatlakoztatja, és használja azt, mint a helyreállítási kötet.

7. Az a **tallózása és a fájlok helyreállítása** ablaktáblán kattintson a **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről.

    ![Helyreállítási beállítások](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. A Windows Explorerben másolja a fájlokat és/vagy mappák visszaállítása, és illessze be őket a helyi kiszolgáló vagy számítógép bármely helyére. Nyissa meg vagy adatfolyam a fájlok közvetlenül a helyreállítási kötetet, és győződjön meg arról, hogy a megfelelő verzióra állít helyre.

    ![Másolja és illessze be a fájlok és mappák csatlakoztatott kötet helyi helyre](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Ha elkészült a fájlok és/vagy mappák visszaállítása az a **fájlok tallózása és visszaállítása** ablaktáblán kattintson a **leválasztás**. Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e választani a kötetet.

    ![A kötet leválasztása és a megerősítése](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem kattint a leválasztás, a helyreállítási kötet csatlakoztatva maradnak 6 órát is várhatnak a mikor lett csatlakoztatva. A csatlakoztatási ideje azonban kiterjesztett legfeljebb egy legfeljebb 24 órán át egy folyamatban lévő fájlmásolás esetén. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva van. Bármely biztonsági mentési művelet ütemezve az időre, ha a kötet csatlakoztatva van, a helyreállítási kötetet leválasztani után fog futni.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Azonnali visszaállítás segítségével visszaállíthatja az adatokat, egy másik számítógépre
Ha a teljes kiszolgáló elveszett, továbbra is helyreállíthatja az adatokat az Azure Backup egy másik gépre. A következő lépések bemutatják a munkafolyamatot.


Ezeket a lépéseket a használt terminológiával tartalmazza:

* *Forrásgép* – az eredeti gépet, amely a biztonsági mentéshez, és amelyet jelenleg nem érhető el.
* *Célszámítógép* – a gép, amelyre az adatok helyreállítása folyik.
* *Minta tároló* – a Recovery Services-tároló, amelyhez a *forrásgép* és *célgépen* van regisztrálva. <br/>

> [!NOTE]
> Biztonsági mentések nem lehet visszaállítani a célszámítógépen az operációs rendszer egy korábbi verzióját. Például egy olyan számítógép lehet Windows 7-ről készült biztonsági másolatok visszaállítása a Windows 8-as és újabb számítógép. Egy biztonsági egy Windows 8 rendszerű számítógép nem állítható vissza egy Windows 7 rendszerű számítógépen.
>
>

1. Nyissa meg a **a Microsoft Azure Backup** számértékekhez igazodnak a a a *célgépen*.

2. Győződjön meg, hogy a *célgépen* és a *forrásgép* ugyanabban a helyreállítási tárban van regisztrálva.

3. Kattintson a **adatok helyreállítása** megnyitásához a **adatok helyreállítása varázsló**.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

4. A a **bevezetés** ablaktáblán válassza előbb **egy másik kiszolgálóra**

    ![Egy másik kiszolgálóra](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Adja meg a tároló hitelesítő adatait tartalmazó fájlt, amely megfelel a *minta tároló*, és kattintson a **tovább**.

    Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), töltse le az új tárolói hitelesítő adatok fájlját a *minta tároló* az Azure Portalon. Miután megadta a egy érvényes tároló hitelesítő adatait, a megfelelő biztonsági mentési tároló nevére jelenik meg.


6. Az a **válassza ki a Backup Server** ablaktáblán válassza ki a *forrásgép* megjelenített gépet a listából, és adja meg a jelszót. Ezután kattintson a **Next** (Tovább) gombra.

    ![Számítógépek listája](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Az a **helyreállítási mód kiválasztása** ablaktáblán válassza ki az **egyes fájlok és mappák** kattintson **tovább**.

    ![Keresés](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Az a **kötet és dátum kiválasztása** ablaktáblán válassza ki a fájlokat és/vagy a visszaállítani kívánt mappákat tartalmazó kötetet.

    A naptárban válasszon ki egy helyreállítási pontot. Időben bármelyik helyreállítási pontra visszaállíthatja. A dátumok **félkövér** azt jelzik, legalább egy helyreállítási pont rendelkezésre állását. Ha dátum, akkor válassza, ha több helyreállítási pont érhető el, válassza az adott helyreállítási pontot a **idő** legördülő menüből.

    ![Elemek keresése](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Kattintson a **csatlakoztatási** helyileg csatlakoztatni a helyreállítási pont, a helyreállítási kötet a *célgépen*.

10. Az a **tallózása és a fájlok helyreállítása** ablaktáblán kattintson a **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről.

    ![Titkosítás](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. A Windows Intézőben, másolja a fájlokat és/vagy mappákat a helyreállítási kötetet, és illessze be őket a *célgépen* helyét. Nyissa meg a vagy adatfolyam a fájlok közvetlenül a helyreállítási kötetet, és ellenőrizze a megfelelő verziójával állíthatók helyre.

    ![Titkosítás](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Ha elkészült a fájlok és/vagy mappák visszaállítása az a **fájlok tallózása és visszaállítása** ablaktáblán kattintson a **leválasztás**. Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e választani a kötetet.

    ![Titkosítás](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Ha nem kattint a leválasztás, a helyreállítási kötet csatlakoztatva maradnak 6 órát is várhatnak a mikor lett csatlakoztatva. A csatlakoztatási ideje azonban kiterjesztett legfeljebb egy legfeljebb 24 órán át egy folyamatban lévő fájlmásolás esetén. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva van. Bármely biztonsági mentési művelet ütemezve az időre, ha a kötet csatlakoztatva van, a helyreállítási kötetet leválasztani után fog futni.
    >

## <a name="next-steps"></a>További lépések
* Most, hogy a fájlok és mappák helyreállítható már [a biztonsági másolatok kezelése a](backup-azure-manage-windows-server.md).
