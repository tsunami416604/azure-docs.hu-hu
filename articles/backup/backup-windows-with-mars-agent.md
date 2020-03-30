---
title: Windows-gépek biztonsági másolatot a MARS-ügynök használatával
description: A Windows-gépek biztonsági mentéséhez használja a Microsoft Azure Recovery Services (MARS) ügynököt.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408912"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Windows-gépek biztonsági mentése az Azure Backup MARS ügynök használatával

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot a Windows-gépekről az [Azure Backup](backup-overview.md) szolgáltatás és a Microsoft Azure Recovery Services (MARS) ügynök használatával. Mars is ismert, mint az Azure Backup ügynök.

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
>
> * Az előfeltételek ellenőrzése
> * Hozzon létre egy biztonsági mentési házirendet és ütemezést.
> * Igény szerinti biztonsági mentés végrehajtása.

## <a name="before-you-start"></a>Előkészületek

* Ismerje meg, hogy az Azure Backup hogyan [használja a MARS-ügynököt a Windows-gépek biztonsági mentéséhez.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Ismerje meg a MARS-ügynököt másodlagos MABS vagy Data Protection Manager kiszolgálón futó [biztonsági mentési architektúrát.](backup-architecture.md#architecture-back-up-to-dpmmabs)
* Tekintse [át, hogy mit támogat, és mit tud a](backup-support-matrix-mars-agent.md) MARS-ügynök.
* [Ellenőrizze az internet-hozzáférést](install-mars-agent.md#verify-internet-access) azokon a gépeken, amelyekről biztonsági másolatot szeretne tenni.
* Ha a MARS-ügynök nincs telepítve, [here](install-mars-agent.md)itt megtudhatja, hogyan telepítheti.

## <a name="create-a-backup-policy"></a>Biztonsági mentési házirend létrehozása

A biztonsági mentési szabályzat határozza meg, hogy mikor kell pillanatképeket készíteni az adatokról helyreállítási pontok létrehozásához. Azt is meghatározza, hogy mennyi ideig tartsa a helyreállítási pontokat. A MARS-ügynök segítségével konfigurálhatja a biztonsági mentési házirendet.

Az Azure Backup nem veszi figyelembe automatikusan a nyári időszámítást. Ez az alapértelmezett okozhat némi eltérést a tényleges idő és az ütemezett biztonsági mentési idő között.

Biztonsági mentési házirend létrehozása:

1. A MARS-ügynök letöltése és regisztrálása után nyissa meg az ügynökkonzolt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.  

1. A **Műveletek csoportban**válassza **a Biztonsági másolat ütemezése**lehetőséget.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)
1. A Biztonsági másolat ütemezése varázslóban válassza az Első lépések > **továbbadása lehetőséget.** **Getting started**
1. A **Biztonsági másolatot szeretne termékek kijelölése**csoportban válassza **az Elemek hozzáadása**lehetőséget.

    ![Biztonsági másolatot adó elemek hozzáadása](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Az **Elemek kijelölése** párbeszédpanelen jelölje ki a biztonsági másolatot, majd kattintson **az OK gombra.**

    ![A biztonsági másolatot adó elemek kijelölése](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. A **Biztonsági másolatot tartalmazó elemek kijelölése** lapon válassza a **Tovább**gombot.
1. A **Biztonsági másolat ütemezésének megadása** lapon adja meg, hogy mikor készítsen napi vagy heti biztonsági mentést. Ezután válassza a **Tovább**gombot.

    * A helyreállítási pont jön létre, amikor egy biztonsági mentést.
    * A környezetben létrehozott helyreállítási pontok száma a biztonsági mentés ütemezése függ.
    * Naponta legfeljebb három napi biztonsági mentést ütemezhet. A következő példában két napi biztonsági mentés történik, egy éjfélkor és egy 18:00-kor.

        ![Napi biztonsági mentésütemezés beállítása](./media/backup-configure-vault/day-schedule.png)

    * Heti biztonsági mentéseket is futtathat. A következő példában a biztonsági mentések minden egyes vasárnap és szerdán 9:30-kor és 1:00-kor készülnek.

        ![Heti biztonsági mentésütemezés beállítása](./media/backup-configure-vault/week-schedule.png)

1. Az **Adatmegőrzési házirend kiválasztása** lapon adja meg, hogyan tárolja az adatok előzménypéldányait. Ezután válassza a **Tovább**gombot.

    * Az adatmegőrzési beállítások határozzák meg, hogy mely helyreállítási pontokat kell tárolni, és mennyi ideig tárolja őket.
    * A napi megőrzési beállítás esetén azt jelzi, hogy a napi megőrzési idő megadott időpontban a legújabb helyreállítási pont a megadott számú napig megmarad. Vagy megadhat egy havi adatmegőrzési szabályzatot, amely jelzi, hogy a minden hónap 30-án létrehozott helyreállítási pontot 12 hónapig kell tárolni.
    * A napi és heti helyreállítási pontok megőrzése általában egybeesik a biztonsági mentésütemezéssel. Így amikor az ütemezés biztonsági mentést indít el, a biztonsági mentés által létrehozott helyreállítási pont a napi vagy heti adatmegőrzési házirend által megadott ideig tárolódik.
    * A következő példában:

        * A napi biztonsági mentéseket éjfélkor és 18:00-kor hét napig tároljuk.
        * A szombaton éjfélkor és 18:00-kor készített biztonsági mentéseket négy hétig őrzik.
        * A hónap utolsó szombatján, éjfélkor és 18:00-kor készített biztonsági mentéseket 12 hónapig őrzik.
        * A március utolsó szombatján készített biztonsági mentéseket 10 évig őrzik.

        ![Példa adatmegőrzési szabályra](./media/backup-configure-vault/retention-example.png)

1. A **Kezdeti biztonsági másolat típusának kiválasztása** lapon döntse el, hogy a kezdeti biztonsági mentést a hálózaton keresztül kívánja-e elkészíteni, vagy kapcsolat nélküli biztonsági mentést szeretne használni. A kezdeti biztonsági mentés hálózaton keresztüli mentéséhez válassza **az** > Automatikus hálózaton keresztül**Ezután lehetőséget.**

    Az offline biztonsági mentésről az [Azure Data Box használata offline biztonsági mentéshez](offline-backup-azure-data-box.md)című témakörben talál további információt.

    ![Válassza ki a biztonsági másolat kezdeti típusát](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. A **Megerősítés** lapon tekintse át az adatokat, majd kattintson a **Befejezés gombra.**

    ![A biztonsági másolat típusának megerősítése](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, válassza a **Bezárás**gombot.

    ![A biztonsági mentési ütemezés állapotának megtekintése](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Hozzon létre egy házirendet minden olyan gépen, ahol az ügynök telepítve van.

### <a name="do-the-initial-backup-offline"></a>A kezdeti biztonsági mentés offline módban

A kezdeti biztonsági mentést automatikusan futtathatja a hálózaton keresztül, vagy biztonsági másolatot készíthet kapcsolat nélküli módban. A kezdeti biztonsági mentés offline beállítása akkor hasznos, ha nagy mennyiségű adattal rendelkezik, amelyek átviteléhez nagy hálózati sávszélességre lesz szükség.

Kapcsolat nélküli átvitel:

1. Írja be a biztonsági mentési adatokat egy átmeneti helyre.
1. Az AzureOfflineBackupDiskPrep eszközzel másolja az adatokat az átmeneti helyről egy vagy több SATA lemezre.

    Az eszköz létrehoz egy Azure Import feladatot. További információ: [Mi az Azure importálási/exportálási szolgáltatás.](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)
1. Küldje el a SATA-lemezeket egy Azure-adatközpontba.

    Az adatközpontban a lemezadatok egy Azure-tárfiókba másolva. Az Azure Backup átmásolja az adatokat a tárfiókból a tárolóba, és a növekményes biztonsági mentések vannak ütemezve.

Az offline vetésről az [Azure Data Box használata offline biztonsági mentéshez](offline-backup-azure-data-box.md)című témakörben talál további információt.

### <a name="enable-network-throttling"></a>Hálózati szabályozás engedélyezése

A hálózati szabályozás engedélyezésével szabályozhatja, hogy a MARS-ügynök hogyan használja a hálózati sávszélességet. A szabályozás akkor hasznos, ha munkaidőben kell biztonsági másolatot készítenie az adatokról, de szabályozni szeretné, hogy a biztonsági mentési és visszaállítási tevékenység mekkora sávszélességet használjon.

Az Azure Backup hálózati szabályozása [a szolgáltatás minősége (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) a helyi operációs rendszeren.

A biztonsági mentések hálózati szabályozása Windows Server 2012 és újabb, valamint Windows 8 és újabb rendszeren érhető el. Az operációs rendszereknek a legújabb szervizcsomagokat kell futtatniuk.

A hálózati szabályozás engedélyezése:

1. A MARS-ügynökben válassza a **Tulajdonságok módosítása**lehetőséget.
1. A **Szabályozás** lapon válassza az **Internet sávszélesség-használat szabályozásának engedélyezése a biztonsági mentési műveletekhez**lehetőséget.

    ![Hálózati szabályozás beállítása biztonsági mentési műveletekhez](./media/backup-configure-vault/throttling-dialog.png)
1. Adja meg az engedélyezett sávszélességet munkaidőben és munkaszüneti órákban. A sávszélesség értéke 512 Kb/s sebességgel kezdődik, és akár 1023 Mb/s-ra is emelkedik. Ezután kattintson az **OK** gombra.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

1. A MARS-ügynökben válassza a **Biztonsági másolatot ( Nagy- és nagy- és nagy- és nagybaj) lehetőséget.**

    ![Biztonsági másolatok ról uk Windows Server rendszerben](./media/backup-configure-vault/backup-now.png)

1. Ha a MARS-ügynök verziója 2.0.9169.0 vagy újabb, akkor egyéni megőrzési dátumot állíthat be. A **Biztonsági másolat megtartása kassza** szakaszban válasszon egy dátumot a naptárból.

   ![Megőrzési dátum testreszabása a naptárral](./media/backup-configure-vault/mars-ondemand.png)

1. A **Megerősítést kérő** lapon tekintse át a beállításokat, és válassza a **Biztonsági másolatok kiválasztása**lehetőséget.
1. A varázsló bezárásához válassza a **Bezárás** gombot. Ha a biztonsági mentés befejezése előtt bezárja a varázslót, a varázsló továbbra is a háttérben fut.

A kezdeti biztonsági mentés befejezése után a **Feladat befejeződött** állapot megjelenik a Biztonsági másolat konzolon.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Igény szerinti biztonsági mentési házirend-megőrzési viselkedés beállítása

> [!NOTE]
> Ez az információ csak a 2.0.9169.0-nál régebbi MARS-ügynökverziókra vonatkozik.
>

| Biztonsági mentés ütemezése beállítás | Az adatmegőrzés időtartama
| -- | --
| Day | **Alapértelmezett megőrzés:** Egyenértékű a "napi biztonsági mentések napokban való megőrzésével". <br/><br/> **Kivétel:** Ha egy napi ütemezett biztonsági mentés, amely hosszú távú megőrzési (hetek, hónapok vagy évek) beállítása sikertelen, egy igény szerinti biztonsági mentés, amely közvetlenül a hiba után aktiválódik a hosszú távú megőrzési. Ellenkező esetben a következő ütemezett biztonsági mentés a hosszú távú megőrzési.<br/><br/> **Példa forgatókönyv:** Az ütemezett biztonsági mentés csütörtökön 8:00-kor nem sikerült. Ezt a biztonsági mentést heti, havi vagy éves megőrzési célokra kellett figyelembe venni. Így az első igény szerinti biztonsági mentés a következő tervezett biztonsági mentés előtt, pénteken 8:00-kor automatikusan címkézve lesz heti, havi vagy éves megőrzésre. Ez a biztonsági mentés helyettesíti a csütörtök 8:00 biztonsági mentést.
| Hét | **Alapértelmezett megőrzés:** Egy nap. A heti biztonsági mentési házirenddel rendelkező adatforrások igény szerinti biztonsági másolatai a következő napon törlődnek. A rendszer akkor is törlődik, ha ők az adatforrás legfrissebb biztonsági másolatai. <br/><br/> **Kivétel:** Ha egy heti ütemezett biztonsági mentés, amely hosszú távú megőrzési (hetek, hónapok vagy évek) beállítása sikertelen, egy igény szerinti biztonsági mentés, amely közvetlenül a hiba után aktiválódik a hosszú távú megőrzési. Ellenkező esetben a következő ütemezett biztonsági mentés a hosszú távú megőrzési. <br/><br/> **Példa forgatókönyv:** Az ütemezett biztonsági mentés csütörtökön 8:00-kor nem sikerült. Ezt a biztonsági mentést figyelembe kellett venni a havi vagy éves megőrzési. Így az első igény szerinti biztonsági mentés, amely a következő tervezett biztonsági mentés előtt, csütörtökön 8:00-kor aktiválódik, automatikusan címkézi a havi vagy éves megőrzési. Ez a biztonsági mentés helyettesíti a csütörtök 8:00 biztonsági mentést.

További információt a [Biztonsági másolat létrehozása című témakörben talál.](#create-a-backup-policy)

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [állíthatja vissza a fájlokat az Azure-ban.](backup-azure-restore-windows-server.md)
* Gyakori kérdések keresése [a fájlok és mappák biztonsági mentésével kapcsolatban](backup-azure-file-folder-backup-faq.md)

