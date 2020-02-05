---
title: Windows rendszerű gépek biztonsági mentése a MARS-ügynökkel
description: A Windows rendszerű gépek biztonsági mentéséhez használja a Azure Backup Microsoft Recovery Services (MARS) ügynököt.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025537"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Windowsos gépek biztonsági mentése az Azure Backup MARS-ügynök használatával

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni a Windows rendszerű gépekről a [Azure Backup](backup-overview.md) szolgáltatás és a Microsoft Azure Recovery Services (MARS) ügynök, más néven Azure Backup ügynök használatával.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Ellenőrizze az előfeltételeket, és hozzon létre egy Recovery Services-tárolót.
> * A MARS-ügynök letöltése és beállítása
> * Hozzon létre egy biztonsági mentési szabályzatot és az ütemtervet.
> * Végezzen el egy igény szerinti biztonsági mentést.

## <a name="about-the-mars-agent"></a>Tudnivalók a MARS-ügynökről

A MARS-ügynököt a Azure Backup a fájlok, mappák és rendszerállapotok biztonsági mentéséhez használja a helyszíni gépekről és az Azure-beli virtuális gépekről az Azure Backup Recovery Services-tárolóba. Az ügynököt a következőképpen futtathatja:

* Az ügynököt közvetlenül a helyszíni Windows rendszerű gépeken futtassa, hogy közvetlenül biztonsági mentést készítsenek az Azure Backup Recovery Services-tárolójába.
* Futtassa az ügynököt a Windows rendszerű Azure-beli virtuális gépeken (az Azure VM Backup bővítménnyel párhuzamosan) a virtuális gépen található adott fájlok és mappák biztonsági mentéséhez.
* Futtassa az ügynököt egy Microsoft Azure Backup-kiszolgálón (MABS) vagy egy System Center Protection-Manager-(DPM-) kiszolgálón. Ebben a forgatókönyvben a gépek és a munkaterhelések biztonsági mentést végeznek a MABS/DPM, majd a MABS/DPM a MARS-ügynök használatával biztonsági mentést készít az Azure-beli tárolóba.
A biztonsági mentés attól függ, hogy hol van telepítve az ügynök.

> [!NOTE]
> Az Azure-beli virtuális gépek biztonsági mentésének elsődleges módszere egy Azure Backup-bővítmény használata a virtuális gépen. Ezzel biztonsági másolatot készít a teljes virtuális gépről. Érdemes lehet telepíteni és használni a MARS-ügynököt a bővítmény mellett, ha a virtuális gépen található meghatározott fájlokról és mappákról szeretne biztonsági másolatot készíteni. [További információk](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Biztonsági mentési folyamat lépései](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Előkészületek

* [Útmutató](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup Windows rendszerű gépek biztonsági mentését a MARS-ügynökkel.
* [Ismerje meg](backup-architecture.md#architecture-back-up-to-dpmmabs) a Mars-ügynököt futtató biztonsági mentési architektúrát egy másodlagos MABS vagy DPM-kiszolgálón.
* [Tekintse át](backup-support-matrix-mars-agent.md) a támogatott és a Mars-ügynökkel készíthető biztonsági mentést.
* Ellenőrizze az internet-hozzáférést azon gépeken, amelyekről biztonsági másolatot szeretne készíteni.
* Egy kiszolgáló vagy ügyfél Azure-ba történő biztonsági mentéséhez Azure-fiókra van szükség. Ha még nem rendelkezik ilyennel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

### <a name="verify-internet-access"></a>Internet-hozzáférés ellenőrzése

Ha a számítógépén korlátozott az Internet-hozzáférés, győződjön meg arról, hogy a számítógépen vagy a proxyn a tűzfalbeállítások lehetővé teszik az alábbi URL-címek és IP-címek használatát:

#### <a name="urls"></a>URLs

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP-címek

* 20.190.128.0/18
* 40.126.0.0/18

A fentiekben felsorolt URL-címek és IP-címek elérése a HTTPS protokollt használja a 443-es porton.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló tárolja az idő múlásával létrehozott biztonsági mentéseket és helyreállítási pontokat, és tartalmazza a biztonsági mentési szabályzatot, amely a biztonsági mentést végző gépekre vonatkozik. Hozzon létre egy tárolót az alábbiak szerint:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/) Azure-előfizetése használatával.

2. Keresse meg és válassza ki **Recovery Services**tárolókat.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. A **Recovery Services** -tárolók menüben kattintson a **+ Hozzáadás**elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához.

   * A névnek egyedinek kell lennie az Azure-előfizetéshez.
   * 2 – 50 karakterből állhat.
   * Betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.

5. Válassza ki azt az Azure-előfizetést, erőforráscsoportot és földrajzi régiót, amelyben létre kívánja hozni a tárolót. A biztonsági másolatok a tárolóba kerülnek. Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Recovery Services-tároló létrehozása – 3. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * A tár létrehozása eltarthat egy ideig.
   * Figyelje az állapotjelző értesítéseket a portál jobb felső részén. Ha néhány perc elteltével nem látja a tárolót, kattintson a **frissítés**gombra.

     ![Kattintson a Frissítés gombra](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Tárolási redundancia beállítása

A Azure Backup automatikusan kezeli a tároló tárterületét. Meg kell adnia a tárterület replikálásának módját.

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra. A **Beállítások** szakaszban kattintson a **Tulajdonságok**elemre.
2. A **Tulajdonságok**alatt a **biztonsági mentés konfigurálása**területen kattintson a **frissítés**elemre.

3. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés**gombra.

      ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Javasoljuk, hogy ha az Azure-t elsődleges biztonsági mentési tárolási végpontként használja, folytassa az alapértelmezett **geo-redundáns** beállítás használatát.
* Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit.
* További információ a [földrajzi](../storage/common/storage-redundancy-grs.md) és [helyi](../storage/common/storage-redundancy-lrs.md) redundanciáról.

## <a name="download-the-mars-agent"></a>A MARS-ügynök letöltése

Töltse le a MARS-ügynököt a telepítendő számítógépekre, amelyekről biztonsági másolatot szeretne készíteni.

* Ha már telepítette az ügynököt valamelyik gépen, ellenőrizze, hogy a legújabb verziót futtatja-e.
* A legújabb verzió a portálon érhető el, vagy [közvetlen letöltést](https://aka.ms/azurebackup_agent) használ

1. A tárolóban **első lépések**alatt kattintson a **biztonsági mentés**elemre.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. A **hol fut a számítási feladatok?** területen válassza **a**helyszíni lehetőséget. Akkor is ezt a beállítást kell választania, ha a MARS-ügynököt egy Azure-beli virtuális gépen szeretné telepíteni.
3. A **Miről szeretne biztonsági másolatot készíteni?** területen válassza a **fájlok és mappák** és/vagy a **rendszerállapot**lehetőséget. Számos egyéb lehetőség is rendelkezésre áll, de ezek csak akkor támogatottak, ha másodlagos biztonsági mentési kiszolgálót futtat. Kattintson az **infrastruktúra előkészítése**elemre.

      ![Fájlok és mappák konfigurálása](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Az **infrastruktúra előkészítése**területen a **Recovery Services ügynök telepítése**alatt töltse le a Mars-ügynököt.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. A letöltési előugró menüben kattintson a **Mentés** gombra. Alapértelmezés szerint az **MARSagentinstaller.exe** fájlt a rendszer a Downloads mappába menti.

6. Most jelölje be **a már letöltött vagy a legújabb Recovery Services ügynök használata**lehetőséget, majd töltse le a tároló hitelesítő adatait.

    ![a tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Kattintson a **Mentés** gombra. A rendszer letölti a fájlt a letöltési mappájába. A tár hitelesítő adatait tartalmazó fájl nem nyitható meg.

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Futtassa a **MARSagentinstaller. exe** fájlt azon gépeken, amelyekről biztonsági másolatot szeretne készíteni.
2. A MARS-ügynök telepítővarázslója > **telepítési beállítások**területen határozza meg, hogy hová szeretné telepíteni az ügynököt, valamint a gyorsítótárhoz használni kívánt helyet. Ezután kattintson a **Next** (Tovább) gombra.
   * Azure Backup a gyorsítótár használatával tárolja az adatpillanatképeket, mielőtt elküldené őket az Azure-ba.
   * A gyorsítótár helyének legalább 5%-ának kell lennie a biztonsági mentéshez használt adatmennyiség méretének legalább 5%-ával.

    ![MARS varázsló telepítési beállításai](./media/backup-configure-vault/mars1.png)

3. A **proxy konfigurálása**területen határozza meg, hogy a Windows-gépen futó ügynök hogyan kapcsolódjon az internethez. Ezután kattintson a **Next** (Tovább) gombra.

   * Ha egyéni proxyt használ, a proxybeállításokat és szükség esetén a hitelesítő adatokat kell megadnia.
   * Ne feledje, hogy az ügynöknek hozzá kell férnie az [URL-címekhez](#verify-internet-access).

     ![MARS varázsló – internet-hozzáférés](./media/backup-configure-vault/mars2.png)

4. A **telepítés** lapon tekintse át az előfeltételek ellenőrzését, majd kattintson a **telepítés**gombra.
5. Az ügynök telepítése után kattintson a **Folytatás a regisztrációhoz**lehetőségre.
6. A **kiszolgáló regisztrálása varázslóban** > tár **azonosítóját**, tallózással keresse meg és válassza ki a letöltött hitelesítő adatokat tartalmazó fájlt. Ezután kattintson a **Next** (Tovább) gombra.

    ![A tároló hitelesítő adatainak regisztrálása](./media/backup-configure-vault/register1.png)

7. A **titkosítás beállítása**lapon meg kell adni egy jelszót, amelyet a rendszer a számítógép biztonsági másolatainak titkosítására és visszafejtésére fog használni.

    * Mentse a titkosítási jelszót egy biztonságos helyen.
    * Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem segít helyreállítani a biztonsági mentési adatokat. Mentse a fájlt egy biztonságos helyen. Szüksége lesz rá a biztonsági másolat visszaállításához.

8. Kattintson a **Befejezés** gombra. Az ügynök most telepítve van, és a gépe regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza, hogy mikor kell pillanatképeket készíteni az adatról a helyreállítási pontok létrehozásához és a helyreállítási pontok megőrzéséhez.

* A biztonsági mentési szabályzatot a MARS-ügynök használatával konfigurálhatja.
* A Azure Backup nem veszi automatikusan figyelembe a nyári időmegtakarítást (DST). Ez némi eltérést okozhat a tényleges idő és az ütemezett biztonsági mentés ideje között.

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
    - Példaként tekintse meg a következő képernyőképet:

        -   A napi biztonsági másolatok éjfélkor és 6:00 ÓRAKOR tartanak hét napig.
        -   A szombaton éjfélkor és 6:00 ÓRAKOR készített biztonsági másolatok négy hétig tartanak.
        -   A hónap utolsó hetében szombaton, éjfélkor és 6:00 ÓRAKOR készített biztonsági másolatok 12 hónapig tartanak.
        -   A március utolsó hetében szombatonként készített biztonsági másolatok 10 évig tartanak.

        ![Adatmegőrzési példa](./media/backup-configure-vault/retention-example.png)


11. A **kezdeti biztonsági mentés típusának kiválasztása beállításnál** döntse el, hogy szeretné-e a kezdeti biztonsági mentést a hálózaton keresztül vagy offline biztonsági mentést használni (az offline biztonsági mentésről további információt ebben a [cikkben](offline-backup-azure-data-box.md)talál). Ha a kezdeti biztonsági mentést a hálózaton keresztül szeretné végrehajtani, válassza az **automatikusan a hálózaton keresztül** lehetőséget, és kattintson a **tovább**gombra.

    ![kezdeti biztonsági mentés típusa](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. A **megerősítés**lapon tekintse át az információkat, majd kattintson a **Befejezés**gombra.

    ![Biztonsági másolat típusának megerősítése](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

    ![Biztonsági mentési folyamat módosításának megerősítése](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Létre kell hoznia egy szabályzatot minden olyan gépen, amelyen az ügynök telepítve van.

### <a name="perform-the-initial-backup-offline"></a>A kezdeti biztonsági mentés offline állapotba helyezése

A kezdeti biztonsági mentést automatikusan a hálózaton keresztül, vagy offline is futtathatja. A kezdeti biztonsági mentés offline kitöltése akkor hasznos, ha olyan nagy mennyiségű adattal rendelkezik, amely sok hálózati sávszélességet igényel az átvitelhez. Az offline átvitelt a következőképpen végezheti el:

1. A biztonsági mentési adatkészletet egy átmeneti helyre kell írnia.
2. A AzureOfflineBackupDiskPrep eszközzel másolja az adatok az átmeneti helyről egy vagy több SATA-lemezre.
3. Az eszköz létrehoz egy Azure importálási feladatot. [További](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) információ az Azure importálásáról és exportálásáról.
4. A SATA-lemezeket egy Azure-adatközpontba küldi.
5. Az adatközpontban a lemezre vonatkozó Adatmásolás egy Azure Storage-fiókba történik.
6. Azure Backup átmásolja az adatokat a Storage-fiókból a tárolóba, és a növekményes biztonsági mentések ütemezve lesznek.

[További](offline-backup-azure-data-box.md) információ az offline előkészítésről.

### <a name="enable-network-throttling"></a>Hálózati sávszélesség-szabályozás engedélyezése

A hálózati sávszélesség-szabályozás engedélyezésével szabályozhatja, hogy a MARS-ügynök hogyan használja a hálózati sávszélességet. A szabályozás akkor lehet hasznos, ha a munkaidőn belül biztonsági mentést szeretne készíteni, de szeretné szabályozni, hogy mekkora sávszélesség legyen használatban a biztonsági mentési és visszaállítási tevékenységhez.

* Azure Backup hálózati szabályozás a szolgáltatásminőség [(QoS) szolgáltatást](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) használja a helyi operációs rendszeren.
* A hálózati sávszélesség-szabályozás a Windows Server 2012 és újabb rendszereken érhető el. Az operációs rendszernek a legújabb szervizcsomagokat kell futtatnia.

A hálózati sávszélesség-szabályozást a következőképpen engedélyezheti:

1. A MARS-ügynökben kattintson a **Tulajdonságok módosítása**elemre.
2. A **szabályozás** lapon jelölje be a **biztonsági mentési műveletek internetes sávszélesség-szabályozásának engedélyezése**jelölőnégyzetet.

    ![Hálózati sávszélesség-szabályozás](./media/backup-configure-vault/throttling-dialog.png)
3. A munkahelyi és a munkaidőn kívüli munkaidőben is megadhatja az engedélyezett sávszélességet. A sávszélesség értéke 512 kbps, és akár 1 023 MBps is lehet. Ezután kattintson az **OK** gombra.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

1. A MARS-ügynökben kattintson a **biztonsági mentés most**lehetőségre.

    ![Windows Server biztonsági másolat készítése](./media/backup-configure-vault/backup-now.png)

2. Ha a MARS-ügynök verziója 2.0.9169.0 vagy újabb, egyéni adatmegőrzés állítható be. A **biztonsági mentés eddig** szakaszban válassza ki a dátumot a megjelenített naptárból:

   ![Biztonsági mentési naptár megőrzése](./media/backup-configure-vault/mars-ondemand.png)

3. A **megerősítés**lapon tekintse át a beállításokat, majd kattintson a **biztonsági mentés**gombra.
4. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha ezt a biztonsági mentés befejeződése előtt végzi el, a varázsló továbbra is fut a háttérben.
5. A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

## <a name="on-demand-backup-policy-retention-behavior"></a>Igény szerinti biztonsági mentési szabályzat adatmegőrzési viselkedése

>[!NOTE]
>Csak a MARS-ügynök 2.0.9169.0 régebbi verzióira érvényes
>

* További információkért tekintse meg [a biztonsági mentési szabályzat létrehozása](backup-configure-vault.md#create-a-backup-policy) című 8. lépését.

| Biztonsági mentési ütemterv beállítása | Mennyi ideig őrzi meg a rendszer a biztonsági másolatok adatai megőrzését?
| -- | --
| Biztonsági mentés készítése: * nap | **Alapértelmezett megőrzés**: a napi biztonsági mentések megőrzési ideje (nap) <br/><br/> **Kivétel**: Ha a hosszú távú adatmegőrzéshez napi ütemezett biztonságimásolat-készlet (hét, hónap, év) meghiúsul, akkor a sikertelen ütemezett biztonsági mentés után azonnal elindítható egy igény szerinti biztonsági mentés, amely hosszú távú adatmegőrzést eredményez. Ellenkező esetben a következő ütemezett biztonsági mentés a hosszú távú adatmegőrzést veszi figyelembe.<br/><br/> **Példa**: Ha (mondjuk) az ütemezett biztonsági mentés a 8:00-es csütörtökön történik, és ugyanezt a biztonsági mentést figyelembe kell venni a heti/havi/évenkénti megőrzéshez, akkor az első igény szerinti biztonsági mentés a következő ütemezett biztonsági mentés előtt aktiválódik (mondjuk 8:00 8:00).
| Biztonsági mentés készítése minden: * hetente | **Alapértelmezett megőrzés**: 1 nap <br/> A heti biztonsági mentési házirenddel rendelkező adatforrások igény szerinti biztonsági mentését a következő napon törli a rendszer, még akkor is, ha az adatforrás legújabb biztonsági mentései vannak. <br/><br/> **Kivétel**: Ha egy heti ütemezett biztonságimásolat-készlet hosszú távú megőrzésre van beállítva (hét, hónap, év), akkor a sikertelen ütemezett biztonsági mentés után azonnal elindítható egy igény szerinti biztonsági mentés a hosszú távú megőrzés érdekében. Ellenkező esetben a következő ütemezett biztonsági mentés a hosszú távú adatmegőrzést veszi figyelembe. <br/><br/> **Példa**: Ha (mondjuk) az ütemezett biztonsági mentés a 8:00-es csütörtökön történik, és ugyanezt a biztonsági mentést kell figyelembe venni a havi/éves megőrzési időszakra vonatkozóan, akkor a következő ütemezett biztonsági mentés előtt elindított első igény szerinti biztonsági mentés (say 8:00 8:00)

## <a name="next-steps"></a>Következő lépések

[További információ a fájlok visszaállításáról](backup-azure-restore-windows-server.md).
