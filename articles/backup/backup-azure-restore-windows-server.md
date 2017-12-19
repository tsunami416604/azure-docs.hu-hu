---
title: "A visszaállítás az Azure-ban a Windows Server vagy a Windows-számítógép |} Microsoft Docs"
description: "Útmutató a Windows Server vagy a Windows-számítógép Azure-ban tárolt adatok helyreállítását."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/16/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 7b259009a8d96e81a141e7718c0d54d40d7b67d1
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Fájlok visszaállítása a Windows-kiszolgálóra vagy -ügyfélre a Resource Manager-alapú üzemi modell használatával

Ez a cikk azt ismerteti, hogyan adatok helyreállítását a biztonsági mentési tárolóból. Adatok visszaállítása, használja az adatok helyreállítása varázsló a Microsoft Azure Recovery Services (MARS) ügynök. Adatok helyreállításakor lehetősége:

* Állítsa vissza az adatokat ugyanarra a számítógépre, amelyről a biztonsági mentések vették.
* A visszaállítás egy másik gépen.

2017. január, a Microsoft kiadott a MARS Agent előzetes frissítés. Hibajavításokat tartalmaz, valamint a frissítés lehetővé teszi, hogy azonnali visszaállítása, amely lehetővé teszi egy helyreállítási kötet írható helyreállítási pont pillanatkép csatlakoztatni. A helyi számítógépre, ezáltal visszaállítása fájlok majd ismerje meg a helyreállítási kötet, és másolja a fájlokat.

> [!NOTE]
> A [2017. január Azure Backup frissítését](https://support.microsoft.com/en-us/help/3216528?preview) szükség, ha az adatok helyreállítását a azonnali visszaállításához használni kívánt. A biztonsági mentési adatok is a tárolók az területi beállításokat, a támogatási cikkben szereplő kell védeni. Tekintse át a [2017. január Azure Backup frissítését](https://support.microsoft.com/en-us/help/3216528?preview) területi beállításokat, amelyek támogatják a azonnali állítsa vissza a legfrissebb listáját. Azonnali visszaállítás **nem** elérhetők az összes területi beállításokat.
>

Azonnali visszaállítási nem áll rendelkezésre a Recovery Services-tárolók használható az Azure portál és a biztonsági mentési tárolók a klasszikus portálon. Ha szeretné használni a azonnali visszaállítása, töltse le a MARS frissítést, és hajtsa végre, amelyek említik azonnali visszaállítása.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

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
    > Ha nem kattint az leválasztási, a helyreállítási kötet marad csatlakoztatott hat órán át a óta, amikor csatlakoztatva lett. A csatlakoztatási ideje azonban kiterjesztett legfeljebb 24 óra egy folyamatban lévő fájlmásolás esetén. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva. Bármely az időszakban, ha a kötet csatlakoztatva, ütemezett biztonsági mentési művelet után a helyreállítási kötet nem fog futni.
    >


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
    > Ha nem kattint az leválasztási, a helyreállítási kötet marad csatlakoztatott hat órán át a óta, amikor csatlakoztatva lett. A csatlakoztatási ideje azonban kiterjesztett legfeljebb 24 óra egy folyamatban lévő fájlmásolás esetén. Nincs biztonsági mentési műveletek fog futni, amíg a kötet csatlakoztatva. Bármely az időszakban, ha a kötet csatlakoztatva, ütemezett biztonsági mentési művelet után a helyreállítási kötet nem fog futni.
    >

## <a name="troubleshooting"></a>Hibaelhárítás
Ha az Azure Backup szolgáltatás sikeresen csatlakoztatható a helyreállítási kötet több percig való kattintás után is **csatlakoztatási** vagy sikertelen egy vagy több hiba, a helyreállítási kötet csatlakoztatása az alábbi lépésekkel helyreállítása általában megkezdéséhez.

1.  Szakítsa meg a folyamatban lévő csatlakozási folyamat, abban az esetben, ha több percig futott.

2.  Győződjön meg arról, hogy az Azure Backup szolgáltatás ügynökének legújabb verzióját a rendszer. Szeretne tudni az Azure Backup szolgáltatás ügynökének fájlverzió-információkat, kattintson a **kapcsolatban a Microsoft Azure Recovery Services Agent** a a **műveletek** ablaktábla a Microsoft Azure Backup szolgáltatás konzolon, és győződjön meg arról, hogy a **verzió** szám egyenlő vagy nagyobb, mint az említett verzió [Ez a cikk](https://go.microsoft.com/fwlink/?linkid=229525). Letöltheti a legújabb verziót [Itt](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Ugrás a **Eszközkezelő** -> **tárolóvezérlők** győződjön meg arról, keresse meg és **Microsoft iSCSI-kezdeményező**. Ha meg tudja találni, közvetlenül ugorjon a 7 alatt. 

4.  Ha a Microsoft iSCSI-kezdeményező szolgáltatás nem találja, ahogy azt korábban említettük, a 3. lépésben, ellenőrizze, hogy ha egy bejegyzés alatt található **Eszközkezelő** -> **tárolóvezérlők** nevű **ismeretlen eszköz** hardver azonosítójú **ROOT\ISCSIPRT**.

5.  Kattintson a jobb gombbal **ismeretlen eszköz** válassza **illesztőprogram frissítése**.

6.  A beállítás kiválasztásával az illesztőprogram frissítéséhez **automatikusan frissített illesztőprogram keresése**. A frissítés megvalósításának meg kell változtatni **ismeretlen eszköz** való **Microsoft iSCSI-kezdeményező** alább látható módon. 

    ![Titkosítás](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Ugrás a **Feladatkezelő** -> **szolgáltatások (helyi)** -> **Microsoft iSCSI-kezdeményező szolgáltatás**. 

    ![Titkosítás](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatás a szolgáltatásban, ehhez kattintson a jobb gombbal kattintva **leállítása** és a jobb gombbal kattint rá újra, és kattintson a Tovább **Start**.

9.  Ismételje meg a helyreállítás azonnali visszaállítás segítségével. 

Ha továbbra sem sikerül a helyreállítás, indítsa újra a kiszolgálót vagy Windows-ügyfélen. A számítógép újraindítása nem kívánatos, vagy a helyreállítás továbbra is sikertelen, a kiszolgáló újraindítása után is, ha végezze el egy másik gép, és lépjen kapcsolatba az Azure támogatja a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) és a támogatási kérelem elküldése.

## <a name="next-steps"></a>Következő lépések
* Most, hogy a fájlok és mappák már helyreállítva, akkor [kezelheti a biztonsági másolatok](backup-azure-manage-windows-server.md).
