---
title: Számítási feladatok biztonsági mentése az Azure Backup Server használatával
description: Ebből a cikkből megtudhatja, hogyan készítheti elő a környezetet a számítási feladatok védelmére és biztonsági mentésére a Microsoft Azure Backup Server (MABS) használatával.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: dd506668f9d75523ff7494bccb2979bf0785990d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273409"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Az Azure Backup Server telepítése és frissítése

> [!div class="op_single_selector"]
>
> * [Azure biztonsági másolat kiszolgálója](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> A következőre vonatkozik: MABS v3. (A MABS v2 már nem támogatott. Ha a MABS v3-nál korábbi verziót használ, frissítsen a legújabb verzióra.)

Ez a cikk bemutatja, hogyan készítheti elő a környezetet a számítási feladatok microsoft Azure Backup Server (MABS) használatával történő biztonsági mentésére. Az Azure Backup Server segítségével egyetlen konzolról védheti az alkalmazás-számítási feladatokat, például a Hyper-V virtuális gépeket, a Microsoft SQL Servert, a SharePoint Servert, a Microsoft Exchange-et és a Windows-ügyfeleket.

> [!NOTE]
> Az Azure Backup Server mostantól képes megvédeni a VMware virtuális gépeket, és továbbfejlesztett biztonsági lehetőségeket biztosít. Telepítse a terméket az alábbi szakaszokban és a legújabb Azure backup-ügynökben leírtak szerint. Ha többet szeretne tudni a VMware-kiszolgálók Azure Backup Server rel történő biztonsági mentéséről, olvassa el a [VMware-kiszolgálók biztonsági mentését](backup-azure-backup-server-vmware.md)című cikket. A biztonsági funkciókról az [Azure biztonsági mentési biztonsági szolgáltatásainak dokumentációjában olvashat.](backup-azure-security-feature.md)
>
>

Az Azure virtuális gépben üzembe helyezett MABS biztonsági másolatot készíthet az Azure-beli virtuális gépekről, de a biztonsági mentési művelet engedélyezéséhez ugyanabban a tartományban kell lennie. Az Azure virtuális gép biztonsági mentése ugyanaz marad, mint a helyszíni virtuális gépek biztonsági mentése, azonban a MABS üzembe helyezése az Azure-ban bizonyos korlátozásokkal rendelkezik. A korlátozásról további információt a [DPM azure-beli virtuális gépként című](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) témakörben talál.

> [!NOTE]
> Az Azure két üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelőés a klasszikus.](../azure-resource-manager/management/deployment-models.md) Ez a cikk az Erőforrás-kezelő modell használatával telepített virtuális gépek visszaállításának információit és eljárásait tartalmazza.
>
>

Az Azure Backup Server örökli a számítási feladatok biztonsági mentési funkcióinak nagy részét az Adatvédelmi kezelőtől (DPM). Ez a cikk a DPM dokumentációjára hivatkozik, amely ismerteti a megosztott funkciók egy részét. Bár az Azure Backup Server a DPM-mel megegyező funkciók nagy részét megosztja, az Azure Backup Server nem készít biztonsági másolatot szalagra, és nem integrálható a System Centerrel.

## <a name="choose-an-installation-platform"></a>Válasszon telepítési platformot

Az első lépés az Azure Backup Server működéséhez, hogy hozzon létre egy Windows Server. A kiszolgáló lehet az Azure-ban vagy a helyszíni.

### <a name="using-a-server-in-azure"></a>Kiszolgáló használata az Azure-ban

Amikor kiszolgálót választ az Azure Backup Server futtatásához, javasoljuk, hogy a Windows Server 2016 Datacenter vagy a Windows Server 2019 Datacenter galériaképével kezdje. A cikk, [az első Windows virtuális gép létrehozása az Azure Portalon](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), oktatóanyagot biztosít az Azure-ban ajánlott virtuális gép használatba lépéséhez, még akkor is, ha még soha nem használta az Azure-t. A kiszolgáló virtuális gép (VM) ajánlott minimális követelményeinek a következőknek kell lenniük: Standard_A4_v2 négy maggal és 8 GB RAM-mal.

A számítási feladatok védelme az Azure Backup Server használatával számos árnyalatot. A cikk, [a DPM telepítése Azure virtuális gépként,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))segít megmagyarázni ezeket az árnyalatokat. A gép üzembe helyezése előtt olvassa el teljesen ezt a cikket.

### <a name="using-an-on-premises-server"></a>Helyszíni kiszolgáló használata

Ha nem szeretné futtatni az alapkiszolgálót az Azure-ban, futtathatja a kiszolgálót egy Hyper-V VM, egy VMware virtuális gép vagy egy fizikai állomás. A kiszolgáló hardverének ajánlott minimális követelményei két mag és 8 GB RAM. A támogatott operációs rendszerek az alábbi táblázatban találhatók:

| Operációs rendszer | Platform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2016 és legújabb sp-k |64 bit |Standard, Datacenter, Essentials  |

A DPM-tároló deduplikációja a Windows Server deduplikációjával. További információ aRról, hogyan működik együtt a [DPM és a deduplikáció](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) a Hyper-V virtuális gépeken való üzembe helyezéskor.

> [!NOTE]
> Az Azure Backup Server egy dedikált, egycélú kiszolgálón való futtatásra van tervezve. Az Azure Backup Server nem telepíthető:
>
> * Tartományvezérlőként futó számítógépre
> * Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> * Olyan számítógépre, amely System Center Operations Manager felügyeleti kiszolgálóként működik
> * Olyan számítógépre, amelyen az Exchange Server fut
> * Olyan számítógépre, amely fürtcsomópontként működik
>
> Az Azure Backup Server telepítése nem támogatott Windows Server Core vagy Microsoft Hyper-V Server rendszerben.

Mindig csatlakozzon az Azure Backup Server egy tartományhoz. Ha azt tervezi, hogy áthelyezi a kiszolgálót egy másik tartományba, telepítse először az Azure Backup Servert, majd csatlakozzon a kiszolgálóhoz az új tartományhoz. A telepítés után nem *támogatott*egy meglévő Azure Backup Server-gép áthelyezése egy új tartományba.

Akár biztonsági mentési adatokat küld az Azure-ba, akár helyileg tartja azadatokat, az Azure Backup Servert regisztrálni kell a Recovery Services-tárolóban.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a Recovery Services-tárolók georedundáns tárolást használnak. Ha ez a tároló az elsődleges tároló, hagyja a tárolási beállítást georedundáns tárolóra. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra. A **Beállítások csoportban** kattintson a **Tulajdonságok gombra.**
2. A **Tulajdonságok csoportBan**a **Biztonsági másolat beállítása csoportban**kattintson a **Frissítés gombra.**

3. Jelölje ki a tárolóreplikáció típusát, majd kattintson a **Mentés gombra.**

     ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Szoftvercsomag

### <a name="downloading-the-software-package"></a>A szoftvercsomag letöltése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Ha már meg van nyitva egy Helyreállítási szolgáltatások tárolója, folytassa a 3. Ha nincs megnyitva a Recovery Services-tároló, de az Azure Portalon van, kattintson a főmenü **Böngészés parancsára.**

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

     ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     A Recovery Services-tárolók listája megjelenik.
   * A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.

     ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. A **Beállítások** panel alapértelmezés szerint megnyílik. Ha zárva van, kattintson a **Beállítások** gombra a beállítások panel megnyitásához.

    ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kattintson a **Biztonsági mentés** gombra az Első lépések varázsló megnyitásához.

    ![Biztonsági mentés első lépések](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Az **Első lépések a biztonsági mentés panelen,** amely megnyílik, **a Biztonsági mentési célok** automatikusan kiválasztásra kerülnek.

    ![Backup-goals-default-megnyitva](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. A **Biztonsági másolat kiválasztása** panelen a **Hol fut a számítási feladatok menüjében** válassza **a Helyszíni**lehetőséget.

    ![helyszíni és számítási feladatok céljaként](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    A **Mit szeretne biztonsági másolatot készíteni?** legördülő menüből válassza ki a védeni kívánt számítási feladatokat az Azure Backup Server használatával, majd kattintson az **OK**gombra.

    Az **Első lépések a biztonsági mentésvarázsló** átvált az Infrastruktúra **előkészítése** beállítással a számítási feladatok Azure-ba való biztonsági mentéséhez.

   > [!NOTE]
   > Ha csak fájlokról és mappákról szeretne biztonsági másolatot készíteni, javasoljuk, hogy használja az Azure Backup ügynököt, és kövesse az Első pillantás: Fájlok és mappák biztonsági mentése című cikkben található [útmutatást.](backup-try-azure-backup-in-10-mins.md) Ha a fájloknál és mappáknál többet szeretne védeni, vagy a jövőben ki szeretné bővíteni a védelmi igényeket, válassza ki ezeket a számítási feladatokat.
   >
   >

    ![Az első lépések varázsló módosítása](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. A **Előkészíti** az infrastruktúra panelen, amely megnyitja, kattintson a **Letöltés** i Telepítse az Azure Backup Server és a Letöltés i trezor hitelesítő adatait. Használja a tároló hitelesítő adatait az Azure Backup Server regisztrációja során a helyreállítási szolgáltatások tárolójában. A hivatkozások a letöltőközpontba mutatnak, ahol a szoftvercsomag letölthető.

    ![Infrastruktúra előkészítése az Azure Backup Server kiszolgálóhoz](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Jelölje ki az összes fájlt, és kattintson a **Tovább**gombra. Töltse le a Microsoft Azure Biztonsági másolat letöltési lapjáról érkező összes fájlt, és helyezze az összes fájlt ugyanabban a mappában.

    ![Letöltési központ 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Mivel a letöltés imérete az összes fájl együtt > 3G, egy 10 Mbps letöltési linket is eltarthat akár 60 perc ig a letöltés befejezéséhez.

### <a name="extracting-the-software-package"></a>A szoftvercsomag kibontása

Miután letöltötte az összes fájlt, kattintson a **MicrosoftAzureBackupInstaller.exe fájlra.** Ezzel elindítja a **Microsoft Azure biztonsági másolat telepítővarázslóját,** hogy a telepítőfájlokat az Ön által megadott helyre bontsa ki. Folytassa a varázsló, és kattintson a **Kivonás** gombra, hogy megkezdje a kivonási folyamat.

> [!WARNING]
> A telepítőfájlok kibontásához legalább 4 GB szabad terület szükséges.
>
>

![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Miután a kibontási folyamat befejeződött, jelölje be a jelölőnégyzetet, hogy indítsa el a frissen kibontott *setup.exe* a Microsoft Azure Backup Server telepítésének megkezdéséhez, és kattintson a **Befejezés** gombra.

### <a name="installing-the-software-package"></a>A szoftvercsomag telepítése

1. A beállítási varázsló elindításához kattintson a **Microsoft Azure Biztonsági másolat** elemre.

    ![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Az üdvözlőképernyőn kattintson a **Tovább** gombra. Ezzel az *Előfeltétel-ellenőrzések* szakaszhoz vezet. Ezen a képernyőn kattintson az **Ellenőrzés** gombra, ha meg szeretné állapítani, hogy az Azure Backup Server hardver- és szoftverelőfeltételei teljesültek-e. Ha minden előfeltétel sikeresen teljesül, megjelenik egy üzenet, amely jelzi, hogy a gép megfelel a követelményeknek. Kattintson a **Tovább** gombra.

    ![Azure Backup Server – Üdvözlő és előfeltételek ellenőrzése](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Az Azure Backup Server telepítőcsomag tartalmazza a megfelelő SQL Server bináris szükséges. Új Azure Backup Server-telepítés indításakor válassza az **SQL Server új példányának telepítése ezzel** a telepítővel lehetőséget, és kattintson a Check and **Install** gombra. Az előfeltételek sikeres telepítése után kattintson a **Tovább**gombra.

    >[!NOTE]
    >Ha saját SQL-kiszolgálót szeretne használni, a támogatott SQL Server-verziók az SQL Server 2014 SP1 vagy újabb, 2016-os és 2017-es verziói.  Az SQL Server összes verziójának Normál vagy Vállalati 64 bitesnek kell lennie.
    >Az Azure Backup Server nem működik távoli SQL Server-példányokkal. Az Azure Backup Server által használt példánynak helyinek kell lennie. Ha egy meglévő SQL-kiszolgálót használ a MABS-hez, a MABS telepítőcsak az SQL-kiszolgáló *elnevezett példányainak* használatát támogatja.

    ![Azure Backup Server – SQL-ellenőrzés](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Ha hiba történik a számítógép újraindítására vonatkozó javaslattal, tegye meg, és kattintson az **Újra gombra.** Ha SQL-konfigurációs problémák merülnek fel, konfigurálja újra az SQL-t az SQL irányelvei szerint, és próbálja meg újra telepíteni/frissíteni az MABS-t az SQL meglévő példányának használatával.

   **Manuális konfigurálás**

   Ha a saját SQL-példányát használja, győződjön meg arról, hogy beépített\Rendszergazdák hozzáadása a rendszergazdai szerepkörhöz a fő adatbázishoz.

    **SSRS-konfiguráció az SQL 2017-tel**

    Ha az SQL 2017 saját példányát használja, manuálisan kell konfigurálnia az SSRS-t. Az SSRS-konfiguráció után győződjön meg arról, hogy az SSRS *IsInitialized* tulajdonsága *True*értékre van állítva. Ha ez igaz értékre van állítva, a MABS feltételezi, hogy az SSRS már konfigurálva van, és kihagyja az SSRS-konfigurációt.

    Használja a következő értékeket az SSRS konfigurációhoz:
    * Szolgáltatásfiók: A "Beépített fiók használata" a hálózati szolgáltatás kell, hogy legyen
    * Webszolgáltatás URL-címe: a "Virtuális könyvtárat" ReportServer_ KELL lennie az\<SQLInstanceName>
    * Adatbázis: DatabaseName kell ReportServer$\<SQLInstanceName>
    * Webportál URL-címe: a "Virtuális könyvtárat" az SQLInstanceName> Reports_\<kell

    [További információ](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) az SSRS-konfigurációról.

    > [!NOTE]
    > A MABS adatbázisaként használt SQL Server licencelését a [Microsoft Online Services feltételei](https://www.microsoft.com/licensing/product-licensing/products) (OST) szabályozzák. Az OST szerint a MABS-hez mellékelt SQL Server csak mabs-adatbázisként használható.

4. Adja meg a Microsoft Azure Backup kiszolgálófájlok telepítésének helyét, és kattintson a **Tovább**gombra.

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    A kaparós sorsjegy az Azure-ra való biztonsági másolatot. Győződjön meg arról, hogy a karcolás helye legalább 5%-a tervezett adatok nak a felhőbe való biztonsági mentésére. A lemezvédelemhez a telepítés befejezése után külön lemezeket kell konfigurálni. A tárolókészletekkel kapcsolatos további információkért [lásd: Tárolókészletek és lemeztároló konfigurálása.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))
5. Adjon meg erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **Tovább**gombra.

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Adja meg, hogy a *Microsoft Update szolgáltatással* szeretné-e keresni a frissítéseket, majd kattintson a **Tovább**gombra.

   > [!NOTE]
   > Azt javasoljuk, hogy a Windows Update-et irányítsa át a Microsoft Update szolgáltatásra, amely biztonsági és fontos frissítéseket kínál a Windows és más termékek, például a Microsoft Azure Backup Server számára.
   >
   >

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Tekintse át a *Beállítások összegzését,* és kattintson a **Telepítés gombra.**

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A telepítés fázisokban történik. Az első fázisban a Microsoft Azure Recovery Services Agent telepítve van a kiszolgálón. A varázsló az internetkapcsolatot is ellenőrzi. Ha rendelkezésre áll az internetkapcsolat, folytathatja a telepítést, ha nem, meg kell adnia a proxy adatait az internethez való csatlakozáshoz.

    A következő lépés a Microsoft Azure Recovery Services Agent konfigurálása. A konfiguráció részeként meg kell adnia a tároló hitelesítő adatait a gép regisztrálásához a helyreállítási szolgáltatások tárolójában. Az Azure és a telephelye között küldött adatok titkosításához/visszafejtéséhez egy jelszót is biztosít. Automatikusan létrehozhat egy jelszót, vagy megadhat saját minimális 16 karakteres jelszót. Folytassa a varázslóval, amíg az ügynök nincs konfigurálva.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. A Microsoft Azure Backup kiszolgáló sikeres regisztrációja után a teljes telepítővarázsló folytatja az SQL Server és az Azure Backup Server összetevőinek telepítését és konfigurálását. Miután az SQL Server összetevő telepítése befejeződött, az Azure Backup Server összetevői települnek.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Amikor a telepítési lépés befejeződött, a termék asztali ikonjai is létrejönnek. Csak kattintson duplán az ikonra a termék elindításához.

### <a name="add-backup-storage"></a>Biztonsági másolat tárolásának hozzáadása

Az első biztonsági másolat az Azure Backup Server-géphez csatlakoztatott tárolón marad. A lemezek hozzáadásáról a [Tárolókészletek és lemeztároló konfigurálása](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)című témakörben talál további információt.

> [!NOTE]
> Akkor is hozzá kell adnia a biztonsági mentési tárhelyet, ha adatokat szeretne küldeni az Azure-ba. Az Azure Backup Server jelenlegi architektúrájában az Azure Backup-tároló tartalmazza az adatok *második* példányát, míg a helyi tároló az első (és kötelező) biztonsági másolat.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Az Adatvédelmi menedzser védelmi ügynökének telepítése és frissítése

A MABS a System Center Data Protection Manager védelmi ügynököt használja. [Az alábbi lépésekkel](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) telepítheti a védelmi ügynököt a védelmi kiszolgálókra.

Az alábbi szakaszok ismertetik, hogyan frissíthetők az ügyfélszámítógépek védelmi ügynökei.

1. A Biztonságimásolat-kiszolgáló felügyeleti konzolján válassza a **Felügyeleti** > **ügynökök**lehetőséget.

2. A megjelenítési ablaktáblán jelölje ki azokat az ügyfélszámítógépeket, amelyekről frissíteni szeretné a védelmi ügynököt.

   > [!NOTE]
   > Az **Ügynökfrissítések** oszlop jelzi, hogy a védelmi ügynök frissítése mikor érhető el minden védett számítógéphez. A **Műveletek** ablaktáblán a **Frissítés** művelet csak akkor érhető el, ha egy védett számítógép ki van jelölve, és a frissítések elérhetők.
   >
   >

3. Ha frissített védelmi ügynököket szeretne telepíteni a kijelölt számítógépekre, a **Műveletek** ablaktáblán válassza a **Frissítés lehetőséget.**

4. A hálózathoz nem csatlakozó ügyfélszámítógépek esetében a számítógép hálózathoz való csatlakoztatásáig az **Ügynök állapota** oszlop a Függőben lévő frissítés állapotát jeleníti **meg.**

   Miután egy ügyfélszámítógép csatlakozott a hálózathoz, az ügyfélszámítógép **Ügynökfrissítések** oszlopa a Frissítés állapotát jeleníti **meg.**

## <a name="move-mabs-to-a-new-server"></a>MABS áthelyezése új kiszolgálóra

Az alábbi lépéseket kell a MABS új kiszolgálóra való áthelyezésére, a tárhely megtartása mellett. Ez csak akkor végezhető el, ha az összes adat a Modern Backup Storage-on található.

  > [!IMPORTANT]
  >
  > * Az új kiszolgálónévnek meg kell egyeznie az eredeti Azure Backup Server-példány nevével. Az új Azure Backup Server-példány nevét nem módosíthatja, ha az előző tárolókészletet és az MABS-adatbázist (DPMDB) szeretné használni a helyreállítási pontok megőrzéséhez.
  > * Rendelkeznie kell a MABS-adatbázis (DPMDB) biztonsági másolatáról. Vissza kell állítania az adatbázist.

1. A megjelenítési ablaktáblán jelölje ki azokat az ügyfélszámítógépeket, amelyekről frissíteni szeretné a védelmi ügynököt.
2. Állítsa le az eredeti Azure biztonsági mentési kiszolgálót, vagy vegye le a vezetékről.
3. Állítsa alaphelyzetbe a számítógépfiókot az active directoryban.
4. Telepítse a Server 2016-ot az új számítógépre, és nevezze el az eredeti Azure Backup kiszolgálóval megegyező gépnévnek.
5. Csatlakozás a tartományhoz
6. Telepítse az Azure Backup Server V3-as vagy újabb kiszolgálót (helyezze át a MABS storage készletlemezeit a régi kiszolgálóról, és importálja)
7. Az 1.
8. Csatlakoztassa a tárolót az eredeti biztonsági másolat kiszolgálójáról az új kiszolgálóhoz.
9. Az SQL-visszaállításból a DPMDB
10. Az új kiszolgáló cd admin parancssorából a Microsoft Azure Backup telepítési helyére és tárolómappájába

    Példa: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Az Azure biztonsági mentésére futtassa a DPMSYNC -SYNC parancsot

    Ha a régiek áthelyezése helyett új lemezeket adott hozzá a DPM-tárolókészlethez, akkor futtassa a DPMSYNC -Reallocatereplica

## <a name="network-connectivity"></a>Hálózati kapcsolat

Az Azure Backup Server a termék sikeres működéséhez kapcsolatot igényel az Azure Backup szolgáltatással. Annak ellenőrzéséhez, hogy a gép rendelkezik-e az Azure-kapcsolattal, használja a ```Get-DPMCloudConnection``` parancsmagát az Azure Backup Server PowerShell konzolon. Ha a parancsmag kimenete IGAZ, akkor létezik kapcsolat, különben nincs kapcsolat.

Ugyanakkor az Azure-előfizetés kifogástalan állapotban kell lennie. Az előfizetés állapotának és kezelésének megtudásához jelentkezzen be az [előfizetési portálra.](https://account.windowsazure.com/Subscriptions)

Miután ismeri az Azure-kapcsolat és az Azure-előfizetés állapotát, az alábbi táblázat segítségével megtudhatja, hogy milyen hatással van a felajánlott biztonsági mentési/visszaállítási funkció.

| Kapcsolódási állapot | Azure-előfizetés | Biztonsági mentés az Azure platformra | Biztonsági másolatot készíteni a lemezre | Visszaállítás az Azure-ból | Visszaállítás lemezről |
| --- | --- | --- | --- | --- | --- |
| Csatlakozva |Aktív |Engedélyezve |Engedélyezve |Engedélyezve |Engedélyezve |
| Csatlakozva |Lejárt |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Csatlakozva |Megszüntetve |Leállítva |Leállítva |Leállítva és az Azure helyreállítási pontjai törölve |Leállítva |
| Elveszett kapcsolat 15 nap > |Aktív |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Elveszett kapcsolat 15 nap > |Lejárt |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Elveszett kapcsolat 15 nap > |Megszüntetve |Leállítva |Leállítva |Leállítva és az Azure helyreállítási pontjai törölve |Leállítva |

### <a name="recovering-from-loss-of-connectivity"></a>Felépülés a kapcsolat elvesztéséből

Ha tűzfallal vagy olyan proxyval rendelkezik, amely megakadályozza az Azure-hoz való hozzáférést, engedélyeznie kell a következő tartománycímeket a tűzfal/proxyprofilban:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

ExpressRoute Microsoft társviszony-létesítés esetén válassza ki a következő szolgáltatásokat/régiókat:

* Azure Active Directory (12076:5060)
* Microsoft Azure-régió (a Recovery Services-tároló helye szerint)
* Azure Storage (a Recovery Services-tároló helye szerint)

További részletekért látogasson el [az ExpressRoute útválasztási követelményeire.](https://docs.microsoft.com/azure/expressroute/expressroute-routing)

Miután az Azure-hoz való kapcsolódás helyreállt az Azure Backup Server-gépre, a végrehajtható műveleteket az Azure-előfizetés állapota határozza meg. A fenti táblázat a gép "Csatlakoztatása" után engedélyezett műveletek részleteit tartalmazza.

### <a name="handling-subscription-states"></a>Előfizetési állapotok kezelése

Lehetőség van egy Azure-előfizetés egy *lejárt* vagy *megszüntetett* állapotból az *aktív* állapotba. Ez azonban hatással van a termék viselkedésére, miközben az állapot nem *aktív:*

* A *megszüntetett* előfizetés elveszíti a funkcionalitást a megszüntetett időszakra vonatkozóan. Az *Aktív*bekapcsoláskor a biztonsági mentés/visszaállítás termékfunkciói újjáélednek. A biztonsági mentési adatok a helyi lemezen is lehívható, ha elég nagy megőrzési időszakkal tartották őket. Azonban a biztonsági mentési adatok az Azure-ban helyrehozhatatlanul elvesznek, ha az előfizetés belép a *deprovisioned* állapotba.
* Egy *lejárt* előfizetés csak addig veszíti el a funkcióit, amíg újra *aktívvá* nem vált. Az előfizetés *lejárt* időszakára ütemezett biztonsági mentések nem fognak futni.

## <a name="upgrade-mabs"></a>Frissítés MABS

Az alábbi eljárásokkal frissítheti a MABS-t.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Frissítés MABS V2-ről V3-ra

> [!NOTE]
>
> A MABS V2 nem előfeltétele a MABS V3 telepítésének. A MABS V3-ra azonban csak mabs v2-ről frissíthet.

A MABS frissítéséhez kövesse az alábbi lépéseket:

1. A MABS V2-ről a MABS V3-ra való frissítéshez szükség esetén frissítse az operációs rendszert Windows Server 2016-ra vagy Windows Server 2019-re.

2. Frissítse a kiszolgálót. A lépések hasonlóak a [telepítéshez.](#install-and-upgrade-azure-backup-server) Az SQL-beállítások esetében azonban lehetősége van az SQL-példány SQL 2017-re való frissítésére, vagy az SQL Server 2017 saját példányának használatára.

   > [!NOTE]
   >
   > Ne lépjen ki az SQL-példány frissítése közben, a kilépés eltávolítja az SQL jelentési példányt, és így a MABS újrafrissítésére tett kísérlet sikertelen lesz.

   > [!IMPORTANT]
   >
   >  Az SQL 2017-es frissítés részeként biztonsági másolatot készítünk az SQL titkosítási kulcsokról, és eltávolítjuk a jelentéskészítési szolgáltatásokat. Az SQL-kiszolgáló frissítése után a(z) 14.0.6827.4788) jelentéskészítő szolgáltatás telepítve van, & titkosítási kulcsok visszaállítása.
   >
   > Az SQL 2017 manuális konfigurálásakor tekintse meg az *SSRS-konfigurációt az SQL 2017-es fájllal* című szakaszban a Telepítési utasítások csoportban.

3. Frissítse a védett kiszolgálókon lévő védelmi ügynököket.
4. A biztonsági mentések az éles kiszolgálók újraindítása nélkül is folytatódnak.
5. Most már megkezdheti az adatok védelmét. Ha modern biztonsági mentési tárolóra frissít, miközben védelmet nyújt, kiválaszthatja a biztonsági mentések tárolásához kívánt köteteket is, és ellenőrizheti a kiépített terület alatt. [További információ](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Microsoft Azure Backup kiszolgáló a telepítési fázis (vagy biztonsági mentés vagy visszaállítás) során hibákkal sikertelen, további információkért tekintse meg ezt a [hibakód-dokumentumot.](https://support.microsoft.com/kb/3041338)
Az [Azure Backup tal kapcsolatos gyakori kérdéseket](backup-azure-backup-faq.md) is megkaphatja

## <a name="next-steps"></a>További lépések

Részletes információkat itt olvashat [a környezet DPM-re való előkészítéséről.](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019) Emellett információkat tartalmaz a támogatott konfigurációk, amelyeken az Azure Backup Server telepíthető és használható. [A PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) különböző műveletek végrehajtásához használhatja.

Ezekkel a cikkekkel mélyebben megismerheti a számítási feladatok védelmét a Microsoft Azure Backup kiszolgáló használatával.

* [SQL Server biztonsági másolatkészítése](backup-azure-backup-sql.md)
* [SharePoint-kiszolgáló biztonsági mentése](backup-azure-backup-sharepoint.md)
* [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
