---
title: Az Azure Backup MARS-ügynökkel rendelkező Windows-gépek biztonsági mentése
description: Az Azure biztonsági mentés Microsoft Recovery Services-(MARS) ügynök használatával Windows-gépek biztonsági mentése.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: raynew
ms.openlocfilehash: 3700ffe0a2b0e0d3ec69bce3a11cdc36d28d9145
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569110"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Az Azure Backup MARS-ügynökkel rendelkező Windows-gépek biztonsági mentése

Ez a cikk azt ismerteti, hogyan használata Windows-gépek biztonsági mentéséhez a [Azure Backup](backup-overview.md) szolgáltatás és a Microsoft Azure Recovery Services-(MARS) ügynök, más néven az Azure Backup ügynököt.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Előfeltételek ellenőrzése és a egy Recovery Services-tároló létrehozása.
> * A MARS-ügynök letöltése és beállítása
> * Ütemezés és a biztonsági mentési szabályzat létrehozása.
> * Hajtsa végre az ad hoc biztonsági mentése.

## <a name="about-the-mars-agent"></a>Tudnivalók a MARS-ügynök

A MARS-ügynök szolgál az Azure Backup szolgáltatás biztonsági mentésére fájlok, mappák és rendszerállapot a helyszíni gépek és Azure virtuális gépek biztonsági mentési Recovery Services-tárolót az Azure-ban. Az ügynök módon futtathatja:

- Futtassa az ügynök közvetlenül a helyi Windows-számítógépek úgy, hogy azok biztonsági másolatot készíthet közvetlenül az Azure-beli biztonsági mentési Recovery Services-tárolóba.
- Futtassa az ügynök Azure virtuális gépek adott fájlok és mappák biztonsági mentése a virtuális gépen futó Windows (egymás mellett az Azure virtuális gép biztonsági mentési bővítményt a).
- Futtassa az ügynök a Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection - Manager (DPM) kiszolgálóhoz. Ebben a forgatókönyvben gépeket és számítási feladatok biztonsági mentése a MABS/dpm-hez, és ezután MABS/DPM biztonsági másolatot készít az Azure-bA a MARS-ügynök tárolójába.
Milyen biztonsági másolatot készíthet attól függ, ahová az agent telepítve van.

> [!NOTE]
> Azure virtuális gépek biztonsági mentésének elsődleges módszere van a virtuális gép Azure Backup bővítmény használatával. A teljes virtuális gép biztonsági mentését. Érdemes, telepítését és használatát a MARS-ügynök a kiterjesztéssel együtt, ha meghatározott fájlok és mappák biztonsági mentése a virtuális gépen szeretné. [További információk](backup-architecture.md#architecture-direct-backup-of-azure-vms).

![Biztonsági mentési folyamat lépései](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Előkészületek

- [Ismerje meg, hogyan](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) Azure Backup és a MARS-ügynök Windows-gépek biztonsági mentését.
- [Ismerje meg](backup-architecture.md#architecture-back-up-to-dpmmabs) a biztonsági mentési architektúra a MARS-ügynök fut egy másodlagos MABS vagy a DPM-kiszolgálón.
- [Felülvizsgálat](backup-support-matrix-mars-agent.md) mit támogat, és milyen biztonsági mentésre alkalmas, a MARS-ügynökkel.
- Ellenőrizze a készíteni kívánt gépek internet-hozzáféréssel.
- Biztonsági mentése a kiszolgáló vagy ügyfél az Azure-ba, szüksége van egy Azure-fiókra. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) mindössze néhány perc alatt.

### <a name="verify-internet-access"></a>Internet-hozzáférés ellenőrzése

Ha a gép korlátozott internet-hozzáféréssel, győződjön meg arról, hogy a gépen, vagy a proxy tűzfal beállításai engedélyezik ezek URL-címek:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Recovery Services-tároló tárolja a biztonsági mentéseket és helyreállítási pontokat hoz létre, idővel, és tartalmazza a biztonsági másolat gépeken alkalmazott biztonsági mentési házirendet. Hozzon létre egy tárolót a következőképpen:

1. Jelentkezzen be a [az Azure Portal](https://portal.azure.com/) Azure-előfizetése.
2. Írja be a kifejezést a keresőmezőbe, **Recovery Services** kattintson **Recovery Services-tárolók**.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. Az a **Recovery Services-tárolók** menüben kattintson a **+ Hozzáadás**.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy 2–50 karakter hosszúságú nevet. Ennek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.

5. Válassza ki az Azure-előfizetést, erőforráscsoportot és a földrajzi régióban, amelyben a tároló kell létrehozni. Biztonsági mentési adatok összmennyiségét zajlik. Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Recovery Services-tároló létrehozása – 3. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

A tároló létrehozása több percet is igénybe vehet. A portálon figyelje. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listája. Ha néhány perc múlva a tároló nem látja, kattintson a **frissítése**.

![Kattintson a Frissítés gombra](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Tárhely-redundancia beállítása

Az Azure Backup automatikusan kezeli a tárolóhoz tartozó tároló. Meg kell adnia, hogy a tároló replikálásának módját.

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra. Alatt a **beállítások** területén kattintson **tulajdonságok**.
2. A **tulajdonságok**alatt **biztonsági mentés konfigurációja**, kattintson a **frissítés**.

3. Válassza ki a tárolóreplikáció típusa, és kattintson a **mentése**.

      ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- Azt javasoljuk, hogy ha az elsődleges biztonsági mentési tároló végpontjaként, mint az Azure használata továbbra is használja az alapértelmezett **georedundáns** beállítás.
- Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit.
- Tudjon meg többet [földrajzi](../storage/common/storage-redundancy-grs.md) és [helyi](../storage/common/storage-redundancy-lrs.md) redundanciát.

## <a name="download-the-mars-agent"></a>A MARS-ügynök letöltése

Töltse le a MARS-ügynök telepítéséhez a kívánt gépekre történő biztonsági mentéséhez.

- Ha már telepítette az ügynököt minden olyan gépen, győződjön meg arról, a legújabb verziót futtat.
- A legújabb verziója elérhető a portálon, vagy a egy [közvetlen letöltésére](https://aka.ms/azurebackup_agent)

1. A tároló alatt **bevezetés**, kattintson a **biztonsági mentés**.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. A **a számítási feladat futtató?** válassza **helyszíni**. Érdemes ezt a beállítást akkor is, ha azt szeretné, a MARS-ügynök telepítése egy Azure-beli virtuális gépen.
3. A **miről szeretne biztonsági másolatot készíteni?** válassza **fájlok és mappák** és/vagy **rendszerállapot**. Nincsenek elérhető egyéb lehetőségek számos, de ha futtatja a másodlagos biztonsági mentési kiszolgálóra csak ezek használatát támogatja. Kattintson a **az infrastruktúra előkészítése**.

      ![Fájlok és mappák konfigurálása](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Az a **infrastruktúra előkészítése**alatt **telepítse a Recovery Services agent**, töltse le a MARS-ügynök.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. A letöltési előugró menüben kattintson a **Mentés** gombra. Alapértelmezés szerint az **MARSagentinstaller.exe** fájlt a rendszer a Downloads mappába menti.

6. Ellenőrizze **már letöltése vagy a Recovery Services Agent legújabb használatával**, és töltse le a tároló hitelesítő adatait.

    ![a tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Kattintson a **Save** (Mentés) gombra. A fájl letöltése a letöltési mappához. A tároló hitelesítőadat-fájlja nem nyitható meg.

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Futtassa a **MARSagentinstaller.exe** fájlt a kívánt gépekre történő biztonsági mentéséhez.
2. A MARS Agent varázslóban > **telepítési beállítások**, adja meg, amelyre telepíteni az ügynököt, és egy helyet a gyorsítótár használatára. Ezután kattintson a **Next** (Tovább) gombra.
    - Az Azure Backup adat-pillanatképek tárolására az Azure-ba való elküldés előtt használja a gyorsítótár.
    - A gyorsítótár helyét kell rendelkeznie az adatok biztonsági mentése a méretének legalább 5 %-ával egyenlő szabad terület.

    ![A MARS varázsló telepítési beállításai](./media/backup-configure-vault/mars1.png)

2. A **proxykonfiguráció**, adja meg, hogyan csatlakozzon az internethez az a Windows-gépen futó ügynök. Ezután kattintson a **Next** (Tovább) gombra.

    - Ha egyéni használ proxy adja meg a proxybeállításokat, és a hitelesítő adatokat, ha szükséges.
    - Ne feledje, hogy az ügynök hozzá kell férnie [ezen URL-címek](#verify-internet-access).

    ![A MARS varázsló internet-hozzáférés](./media/backup-configure-vault/mars2.png)

3. A **telepítési** tekintse át az Előfeltételek ellenőrzése, és kattintson a **telepítése**.
4. Az ügynök telepítése után kattintson a **továbblépni a regisztrációra**.
5. Az a **kiszolgáló regisztrálása varázsló** > **tároló azonosítása**, keresse meg és válassza ki a letöltött hitelesítő adatai fájlt. Ezután kattintson a **Next** (Tovább) gombra.

    ![Register - tároló hitelesítő adatai](./media/backup-configure-vault/register1.png)

6. A **titkosítási beállítás**, adjon meg egy jelszót, amely használható titkosításához és visszafejtéséhez a gép biztonsági mentését.

    - Mentse a titkosítási jelszót egy biztonságos helyen.
    - Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem tud segíteni a biztonsági mentési adatok helyreállítását. Mentse a fájlt egy biztonságos helyen. Szüksége lesz rá a biztonsági másolat visszaállításához.

7. Kattintson a **Befejezés**. Az ügynök most telepítve van, és a gépe regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="create-a-backup-policy"></a>A biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza, hogy hozzon létre helyreállítási pontokat, az adatok pillanatképeket készíthet, és hogy mennyi ideig kell megőrizni a helyreállítási pontok.

- A MARS-ügynök használatával biztonsági mentési szabályzatot konfigurálnia.
- Az Azure Backup nem automatikusan nyári időszámításra (cél) figyelembe kell venni. Emiatt előfordulhat néhány eltérés van a tényleges idő és a ütemezett biztonsági mentés időpontja között.

Hozzon létre egy házirendet a következőképpen:

1. Az összes nyissa meg a MARS-ügynök. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.
2. A **műveletek**, kattintson a **biztonsági mentés ütemezése**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

3. A biztonsági mentés ütemezése varázsló > **bevezetés**, kattintson a **tovább**.
4. A **elemek kijelölése biztonsági mentéshez**, kattintson a **elemek hozzáadása**.
5. A **elemek kijelölése**, válassza ki, mit szeretne készíteni. Ezután kattintson az **OK** gombra.
6. A **elemek kijelölése biztonsági mentéshez** kattintson **tovább**.
7. A **biztonsági mentési ütemezés megadása** csoportjában adja meg, ha szeretne napi vagy heti biztonsági másolatok készítése. Ezután kattintson a **Next** (Tovább) gombra.

    - Helyreállítási pont jön létre, amikor egy biztonsági mentés készül.
    - A környezetben létrehozott helyreállítási pontokat a száma, a biztonsági mentési ütemezést függ.

1. Napi biztonsági mentésekhez, akár naponta háromszor is ütemezhető. Ha például a képernyőképen látható, két napi biztonsági mentések éjfélkor egyet és este 6 órára.

    ![Napi ütemezés](./media/backup-configure-vault/day-schedule.png)

9. Heti biztonsági mentések túl is futtathatja. Ha például a képernyőképen látható mentésekre minden másodlagos vasárnap & szerda 9:30 -kor és 1:00 -kor.

    ![Heti ütemezés](./media/backup-configure-vault/week-schedule.png)

8. Az a **adatmegőrzési szabályzat kijelölése** csoportjában adja meg, hogyan tárolja az adatok korábbi másolatát. Ezután kattintson a **Next** (Tovább) gombra.

   - Megőrzési beállítások határozzák meg, melyik helyreállítási pontokat szeretné tárolni, és mennyi ideig azok kell tárolni a.
   - Például állíthat be egy napi megőrzési beállítás, akkor azt jelzik, hogy a napi megőrzési megadott időpontban, a legutóbbi helyreállítási pontot megőrzi a rendszer a megadott számú napig. Vagy egy másik példát, megadhatja annak jelzésére, hogy minden hónapban 30 létrehozott helyreállítási pontot kell tárolni 12 hónapon keresztül havi adatmegőrzési.
   - A napi és heti helyreállítási pont megőrzése általában megegyezik a biztonsági mentés ütemezése. Ami azt jelenti, hogy a biztonsági mentési ütemezés szerint aktiválódik, amikor a helyreállítási pont, a biztonsági mentés által létrehozott tárolt jelzett a napi vagy heti adatmegőrzési időtartama alatt.
   - Tegyük fel, a következő képernyőfelvételen látható:
     - Napi biztonsági mentések éjfél és 6-kor hét napig őrzi meg.
     - Biztonsági másolatokat egy szombat hajnalban 6 óra és éjfél őrzi meg a 4 héten keresztül.
     - 12 hónapig őrzi meg az elmúlt héten az éjfél és 6-kor hónap a szombat biztonsági másolatokat. – Biztonsági másolatokat egy március az elmúlt hét szombatján 10 évig tartanak.

   ![Adatmegőrzési-példa](./media/backup-configure-vault/retention-example.png)

11. A **mentés típusának** adja meg, hogy a kezdeti biztonsági mentési és érvénybe a hálózaton keresztüli vagy kapcsolat nélküli módban. Ezután kattintson a **Next** (Tovább) gombra.

10. A **megerősítő**, tekintse át az adatokat, és kattintson **Befejezés**.
11. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### <a name="perform-the-initial-backup-offline"></a>A kezdeti biztonsági mentés offline

Futtathatja egy kezdeti biztonsági mentés automatikusan a hálózaton keresztül, vagy offline állapotba. Egy kezdeti biztonsági mentés offline áttöltést akkor hasznos, ha nagy mennyiségű adat átvitelét a hálózati sávszélesség számos igényel. Egy kapcsolat nélküli átviteli tegye a következőket:

1. A biztonsági mentési adatokat írni egy előkészítési helyet.
2. A AzureOfflineBackupDiskPrep eszköz használatával az adatok másolása az átmeneti helyen egy vagy több SATA lemezeken.
3. Az eszköz létrehoz egy Azure Import-feladattal. [További](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Azure importálási és exportálási.
4. A SATA lemezek küldeni egy Azure-adatközpontban.
5. Az Adatközpont a lemez adatokat másolja egy Azure storage-fiókot.
6. Az Azure Backup az adatok a storage-fiókból-tárolóba másol, és a növekményes biztonsági mentés van ütemezve.

[További](backup-azure-backup-import-export.md) kapcsolatos offline áttöltést.

### <a name="enable-network-throttling"></a>Hálózati sávszélesség szabályozásának engedélyezése

Szabályozhatja a hálózati sávszélesség felhasználása a MARS-ügynök által a hálózati sávszélesség-szabályozás engedélyezése. Szabályozás akkor hasznos, ha a biztonsági másolatokat a munkaidő alatt, de vezérelhető, hogy mekkora sávszélességet használatos biztonsági mentése és visszaállítása tevékenység kell.

- Az Azure Backup hálózati sávszélesség-szabályozás használt [szolgáltatásminőség (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) a helyi operációs rendszer.
- Hálózati sávszélesség-szabályozás a biztonsági mentéshez és a Windows Server 2008 R2 és újabb verziók, Windows 7 és újabb verziók esetében érhető el. Operációs rendszert kell futtatnia a legújabb szervizcsomagokkal.

Engedélyezze a hálózati sávszélesség szabályozása a következő:

1. Kattintson a MARS-ügynök **tulajdonságainak módosítása**.
2. Az a **sávszélesség-szabályozási** lapon jelölje **szabályozása a biztonsági mentési műveletek internetes sávszélességének**.

    ![Hálózati sávszélesség-szabályozás](./media/backup-configure-vault/throttling-dialog.png)
3. Adja meg az engedélyezett sávszélesség, munkahelyi és a munkaidő kívül. Sávszélesség 512 KB/s-kor kezdődik, és nyissa meg a akár 1,023 MB/s. Ezután kattintson az **OK** gombra.

## <a name="run-an-ad-hoc-backup"></a>Egy ad-hoc biztonsági mentés futtatása

1. Kattintson a MARS-ügynök **azonnali biztonsági mentés**. Ez elindít a kezdeti replikálás a hálózaton keresztül.

    ![Windows Server biztonsági másolat készítése](./media/backup-configure-vault/backup-now.png)

2. A **megerősítő**, tekintse át a beállításokat, majd kattintson **biztonsági mentése**.
3. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha erre a biztonsági mentés befejezése előtt, akkor a varázsló továbbra is fut a háttérben.

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](backup-azure-restore-windows-server.md) fájlok visszaállítása.
