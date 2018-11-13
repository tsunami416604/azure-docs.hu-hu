---
title: Használja az Azure Backup Server számítási feladatainak biztonsági mentéséhez az Azure-bA
description: Az Azure Backup Server használatával védeni, vagy biztonsági másolatot az Azure Portalon.
services: backup
author: PVRK
manager: shivamg
keywords: az Azure backup server; számítási feladatok; védelme számítási feladatainak biztonsági mentése
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: adigan; kasinh
ms.openlocfilehash: 602b7b2a81ec727c9acaf86165867daa20370947
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578722"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Telepítése és az Azure Backup Server frissítése
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Ez a cikk bemutatja, hogyan készítse elő a környezetét a Microsoft Azure Backup Server (MABS) használó munkaterhelések biztonsági mentéséhez. Az Azure Backup Server alkalmazások és szolgáltatások, például a Hyper-V virtuális gépek, a Microsoft SQL Server, SharePoint Server, a Microsoft Exchange és a Windows ügyfelek védheti egyetlen konzolról.

> [!NOTE]
> Az Azure Backup Server most is a VMware virtuális gépek védelméhez és továbbfejlesztett biztonsági képességeket biztosít. A termék telepítésére, az alábbi szakaszokban és a legújabb Azure Backup szolgáltatás ügynökének leírtak szerint. VMware-kiszolgálók az Azure Backup Server biztonsági mentésével kapcsolatos további tudnivalókért tekintse meg ezt a cikket [a VMware-kiszolgáló biztonsági mentése az Azure Backup Server](backup-azure-backup-server-vmware.md). Biztonsági képességeivel kapcsolatos további információkért tekintse meg [az Azure backup biztonsági funkciók dokumentációja](backup-azure-security-feature.md).
>
>

Infrastruktúra-szolgáltatás (IaaS) számítási feladatok, például az Azure-beli virtuális gépek, is védheti.

> [!NOTE]
> Az Azure az erőforrások létrehozásához és használatához két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). A cikk a Resource Manager modellel üzembe helyezett virtuális gépek visszaállításához információkat és eljárásokat ismerteti.
>
>

Az Azure Backup Server a Data Protection Manager (DPM) örökli a számítási feladatok biztonsági funkciók. Ez a cikk a DPM dokumentációjának ismertetnek néhányat a megosztott funkciót hivatkozásokat tartalmaz. Bár az Azure Backup Server oszt meg ugyanazokat a funkciókat, mint a DPM számos, az Azure Backup Server nem létezik-e a szalagra történő biztonsági mentése, és nem integrálható a System Centerrel.

## <a name="choose-an-installation-platform"></a>Válasszon egy telepítési platform
Az Azure Backup Server és az első lépést, hogy állítsa be a Windows Server. A kiszolgáló Azure-ban vagy a helyszínen is lehet.

### <a name="using-a-server-in-azure"></a>Az Azure-kiszolgáló használatával
Ha a kiszolgáló Azure Backup Server futtatásához, ajánlott a katalógus-lemezkép a Windows Server 2012 R2 Datacenter, Windows Server 2016 Datacenter vagy Windows Server 2019 Datacenter megkezdése. A cikk [az első Windows virtuális gép létrehozása az Azure Portalon](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), nyújt segítséget a javasolt virtuális gép az Azure-ban – első lépések akkor is, ha soha nem használta az Azure-t. A kiszolgáló virtuális gép (VM) vonatkozó ajánlott minimális követelményeknek kell: két maggal és 3,5 GB RAM-MAL rendelkező Standard A2.

Az Azure Backup Server számítási feladatok védelmét már sok apró sajátosságaival. A cikk [DPM telepítése Azure virtuális gépként](https://technet.microsoft.com/library/jj852163.aspx), segít ismertetik ezeket a vállalatánál. A gép üzembe helyezése előtt olvassa el ebben a cikkben teljesen.

### <a name="using-an-on-premises-server"></a>Egy helyszíni kiszolgáló használatával
Ha nem szeretné, hogy a kiszolgáló futtatása az Azure-ban, a Hyper-V virtuális gépek, VMware virtuális gép vagy egy fizikai gazdagép futtathatja a kiszolgálón. A kiszolgáló hardveres ajánlott minimális követelményei, kétmagos processzor és 4 GB RAM-MAL. Az alábbi táblázatban felsorolt támogatott operációs rendszerek:

| Operációs rendszer | Platform | SKU |
|:--- | --- |:--- |
| A Windows Server 2019 |64 bit |Standard, Datacenter, Essentials (MABS v3-as és újabb verziók) |
| A Windows Server 2016 és a legújabb szervizcsomagok |64 bit |Standard, Datacenter, Essentials (MABS V2 és újabb verziók) |
| Windows Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 és a legújabb szervizcsomagok |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |

A DPM-tároló használata a Windows Server Adatdeduplikáció deduplikálhatja. Tudjon meg többet [DPM és deduplikáció](https://technet.microsoft.com/library/dn891438.aspx) együttműködése a Hyper-V virtuális gépek üzembe helyezésekor.

> [!NOTE]
> Az Azure Backup Server dedikált, meghatározott célú kiszolgálókén történő futtatásra tervezték. Az Azure Backup Server nem telepíthető:
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amely System Center Operations Manager felügyeleti kiszolgálóként működik
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógépre, amely fürtcsomópontként működik

Az Azure Backup Server mindig csatlakoztatása a tartományhoz. Ha azt tervezi, a kiszolgáló áthelyezése egy másik tartományba, először telepítse az Azure Backup Server, majd csatlakoztassa a kiszolgálót az új tartományhoz. Egy Azure Backup Server meglévő gépre áthelyezése másik tartományba, üzembe helyezés után *nem támogatott*.

Biztonsági mentési adatokat küldjön az Azure-ba, vagy helyileg tárolja, hogy az Azure Backup Server regisztrálva kell lennie egy Recovery Services-tárolót.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a Recovery Services-tárolók használata a georedundáns tárolás. Ha ebben a tárban a elsődleges tároló, hagyja a beállítást georedundáns tárolás beállítása. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tárolót, a tároló irányítópultját és a beállítások menü megnyitásához. Ha a **beállítások** menü nem nyílik meg, kattintson a **minden beállítás** a tároló irányítópultján.
2. Az a **beállítások** menüben kattintson a **biztonsági mentési infrastruktúra** > **biztonsági mentés konfigurációja** megnyitásához a **biztonsági mentés konfigurációja**panelen. Az a **biztonsági mentés konfigurációja** menüben válassza ki a tárolóreplikációs beállítást a tároló számára.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Miután kiválasztotta a tárolási beállítást a tároló számára, készen áll, hogy hozzárendelje a virtuális gépet a tárolóhoz. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

## <a name="software-package"></a>Szoftverfrissítési csomag
### <a name="downloading-the-software-package"></a>A csomag letöltése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Ha már rendelkezik egy Recovery Services-tároló nyílt, folytassa a 3. lépés. Ha nem rendelkezik egy Recovery Services-tároló nyílt, de az Azure Portal, a főmenü **Tallózás**.

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

     ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     A Recovery Services-tárolók listája megjelenik.
   * A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.

     ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. A **beállítások** panel alapértelmezés szerint megnyílik. Ha be van zárva, kattintson a **beállítások** a beállítások panel megnyitásához.

    ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kattintson a **Backup** az első lépések varázsló megnyitásához.

    ![Biztonsági mentés első lépések](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Az a **Ismerkedés a biztonsági mentés** a megnyíló panelen **mentési cél használatát** lesz automatikusan kiválasztva.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Az a **biztonsági mentés célja** panelen, a **a számítási feladat futtató** menüben válassza **helyszíni**.

    ![a helyszíni és a célokat, számítási feladatok](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Az a **miről szeretne biztonsági másolatot készíteni?** legördülő menüben válassza ki az Azure Backup Server használatával védeni kívánt számítási feladatokat, és kattintson **OK**.

    A **Ismerkedés a biztonsági mentés** varázsló kapcsolók a **infrastruktúra előkészítése** beállítás számítási feladatainak biztonsági mentéséhez az Azure-bA.

   > [!NOTE]
   > Ha azt szeretné, hogy csak a fájlok és mappák biztonsági mentése, javasoljuk, hogy az Azure Backup ügynök használatával, és az-cikkben szereplő útmutatást [áttekintés: fájlok és mappák biztonsági mentése](backup-try-azure-backup-in-10-mins.md). Ha több, mint a fájlokat és mappákat védeni kívánja, vagy bontsa ki az adatvédelmi igényeket a jövőben tervez, válassza ki azokat a munkaterheléseket.
   >
   >

    ![Első lépések varázsló módosítása](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Az a **infrastruktúra előkészítése** panelen kattintson a **letöltése** mutató hivatkozásokat az Azure Backup Server telepítése és a letöltés a tároló hitelesítő adatai. A tároló hitelesítő adatait használja az Azure Backup Server a helyreállítási tárban való regisztrálása során. A hivatkozásokra kattintva a letöltőközpontból, amelyben a szoftvercsomag tölthető le.

    ![Azure Backup Server infrastruktúra előkészítése](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Válassza ki az összes fájlt, és kattintson a **tovább**. Töltse le a Microsoft Azure Backup letöltési oldalról érkező összes fájlt, és helyezze el az összes fájlt ugyanabba a mappába.

    ![1. letöltőközpontból](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Minden a fájlok letöltési mérete nem haladhatja meg a > 3G, mivel a 10 MB/s letöltése hivatkozásra a letöltés befejezéséhez legfeljebb 60 percig is tarthat.

### <a name="extracting-the-software-package"></a>A szoftverfrissítési csomag kicsomagolásához használt
Minden fájl letöltése után kattintson a **MicrosoftAzureBackupInstaller.exe**. Ekkor elindul a **a Microsoft Azure Backup telepítővarázsló** kibontani a telepítőfájlokat egy Ön által megadott helyre. Kövesse a varázsló lépéseit, majd kattintson a a **kinyerése** gomb a kinyerési folyamat megkezdéséhez.

> [!WARNING]
> Bontsa ki a telepítési fájlok legalább 4GB szabad lemezterület szükséges.
>
>

![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Miután a kinyerési folyamat befejezése után, jelölje be a jelölőnégyzetet, elindíthatja a frissen kinyert *setup.exe* megkezdése a Microsoft Azure Backup Server telepítése, és kattintson a a **Befejezés** gombra.

### <a name="installing-the-software-package"></a>A szoftverfrissítési csomag telepítése
1. Kattintson a **a Microsoft Azure Backup** a telepítővarázsló elindításához.

    ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Az üdvözlőképernyőn kattintson a **tovább** gombra. Ezzel továbblép a *előfeltétel-ellenőrzési* szakaszban. Ezen a képernyőn kattintson a **ellenőrizze** meghatározni, ha a hardver- és az Azure Backup Server teljesült. Ha minden előfeltétel a sikeresen teljesül, látni fogja a egy üzenet jelzi, hogy a gép megfelel-e a követelményeknek. Kattintson a **tovább** gombra.

    ![Az Azure Backup Server – Üdvözöljük és az Előfeltételek ellenőrzése](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. A Microsoft Azure Backup Server szükséges SQL Server Enterprise. Az Azure Backup Server-telepítési csomag emellett tartalmaz csomagolt a megfelelő SQL Server bináris fájlokat, akkor szükséges, ha nem szeretné használni a saját SQL. -Es verziótól kezdve egy új Azure Backup Server telepítése során ki kell választania a beállítás **ezzel a beállítással új SQL Server-példány telepítése** , és kattintson a **ellenőrzés és telepítés** gombra. Ha az előfeltételek telepítése sikeresen megtörtént, kattintson **tovább**.

    ![Az Azure Backup Server – SQL ellenőrzése](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Ha hiba történik, a javaslatra kattintva indítsa újra a gépet, ehhez, és kattintson a **újbóli ellenőrzés**. Bármely SQL-konfigurációs problémák esetén az SQL-irányelvek szerint az SQL újrakonfigurálása, és ismételje meg telepíteni vagy frissíteni a MABS a meglévő SQL-példány használatával.

   > [!NOTE]
   > Az Azure Backup Server nem működik a távoli SQL Server-példányt. Az Azure Backup Server által használt példány kell lennie a helyi. Abban az esetben, ha egy meglévő SQL server a MABS használ, a MABS-telepítő csak támogatja-e a használatát *elnevezett példányokkal* az SQL server.

   **Kézi konfigurálás**

    > [!IMPORTANT]

    > Konfigurálás után a MABS, manuálisan konfigurálásakor ügyeljen arra, hogy *IsInitialized* SSRS tulajdonsága *igaz*. Ha ez igaz értékre van beállítva, MABS feltételezi, hogy az SSRS már konfigurálva van, és kihagyja az SSRS konfigurációja.

    > Az SSRS konfigurációja a következő értékeket használja:

      >- Szolgáltatásfiók: A beépített fiók használata"hálózati szolgáltatás kell lennie
    >- Web Service URL-címe: 'A virtuális könyvtár' ReportServer_MSDPMINSTANCE kell lennie
    > - Adatbázis: DatabaseName ReportServer$ MSDPMINSTANCE kell lennie
    > - Webes portál URL-címe: 'A virtuális könyvtár' Reports_MSDPMINSTANCE kell lennie

    > [További](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) SSRS konfigurálásával kapcsolatban.

4. A Microsoft Azure Backup server-fájlok a telepítés helyét adja meg, és kattintson a **tovább**.

    ![A Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Az ideiglenes hely esetében történő biztonsági mentés Azure követelmény. Győződjön meg arról, az ideiglenes hely az adatok biztonsági mentése a felhőre tervezett legalább 5 %-át. A lemezvédelem különböző lemezen a telepítés befejezése után konfigurálni kell. Tárolókészletek kapcsolatos további információkért lásd: [konfigurálhatók a tárolókészletek és a lemezes tárolás](https://technet.microsoft.com/library/hh758075.aspx).
5. Adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **tovább**.

    ![A Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Válassza ki, hogy a használni kívánt *Microsoft Update* keressen frissítéseket, és kattintson a **tovább**.

   > [!NOTE]
   > Javasoljuk, hogy a Windows Update átirányítása a Microsoft Update szolgáltatásban, amely a biztonsági és fontos frissítéseket kínál a Windows- és egyéb termékek, mint a Microsoft Azure Backup Server.
   >
   >

    ![A Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Tekintse át a *beállítások összefoglalása* kattintson **telepítése**.

    ![A Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A telepítés ciklusokban történik. Az első szakaszban a Microsoft Azure Recovery Services Agent telepítve van a kiszolgálón. A varázsló internetkapcsolattal is keres. Ha érhető el internetkapcsolat folytathatja a telepítést, ha nem, meg kell adnia a proxy adatait az internethez való kapcsolódáshoz.

    A következő lépés, hogy a Microsoft Azure Recovery Services Agent konfigurálása. A konfiguráció részeként akkor a tároló hitelesítő adatokat a gépet a helyreállítási tárban való regisztrálásához. Meg fog adni egy jelszót az adatok Azure-ban és a helyszíni eredetű között küldött titkosítási/visszafejtési. Automatikusan hozzon létre egy hozzáférési kódot, vagy adjon meg a saját legalább 16 karakterből álló jelszót. A varázsló folytatásához, amíg az ügynök úgy van beállítva.

    ![Az Azure biztonsági mentési Serer PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Miután a Microsoft Azure Backup-kiszolgáló regisztrálása sikeresen befejeződött, a varázsló általános abból telepítését és konfigurációját az SQL Server és az Azure Backup Server összetevőket. Miután az SQL Server-összetevő telepítése befejeződött, az Azure Backup Server ezeket az összetevőket.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

A telepítési lépés befejezése után a termék asztali ikonok létrejön is. Csak az ikonra duplán kattintva indítsa el a termék.

### <a name="add-backup-storage"></a>Backup storage hozzáadása
Az első biztonsági másolat az Azure Backup Server géphez csatolt storage másolatok. Lemezek hozzáadásával kapcsolatos további információkért lásd: [konfigurálhatók a tárolókészletek és a lemezes tárolás](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Adja hozzá a biztonsági mentési tár, még akkor is, ha azt tervezi, hogy adatokat küldjön az Azure-bA kell. Az Azure Backup Server aktuális architektúra, az Azure Backup-tároló tárolja a *második* közben a helyi tárhely az adatok másolatát tárolja az első (és kötelező) a biztonsági másolat.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Telepítése és a Data Protection Manager védelmi ügynök frissítése

MABS használja a System Center Data Protection Manager védelmi ügynököt. [A következő lépéseket](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) a védelmi ügynök telepítése a védelem kiszolgálókon.

A következő szakaszok ismertetik az ügyfélszámítógépek védelmi ügynökök frissítése.

1. A Backup Server felügyeleti konzolon válassza ki a **felügyeleti** > **ügynökök**.

2. A kijelzőpanelen válassza ki az ügyfélszámítógépek, amelynek meg szeretné frissíteni a védelmi ügynök.

  > [!NOTE]
  > A **Ügynökfrissítések** oszlopban látható, ha a védelmi ügynök frissítése elérhető minden védett számítógépen. Az a **műveletek** panelen a **frissítés** művelet érhető el, csak ha egy védett számítógép van kiválasztva, és frissítések érhetők el.
  >
  >

3. A kiválasztott számítógépeken a frissített védelmi ügynök telepítése a **műveletek** ablaktáblán válassza előbb **frissítés**.

4. Az ügyfélszámítógép, amely nem csatlakozik a hálózathoz, amíg a számítógép csatlakozik a hálózathoz a **ügynök állapota** az oszlopban látható egy állapotát **frissítés függőben**.

  Miután egy ügyfélszámítógép csatlakozik a hálózathoz, a **Ügynökfrissítések** az ügyfélszámítógép az oszlopban látható egy állapotát **Updating**.

## <a name="move-mabs-to-a-new-server"></a>Helyezze át a MABS egy új kiszolgálóra

Ha át kell helyeznie a MABS egy új kiszolgálóra a tároló megőrzése az alábbiakban a lépéseket. Ez csak akkor, ha minden adat van a Modern Backup Storage elvégezhető.


  > [!IMPORTANT]
  > - Az új kiszolgáló nevét a neve megegyezik az eredeti Azure Backup Server-példányra kell lennie. Az Azure Backup Server új példányának neve nem módosítható, ha azt szeretné használni a korábbi tárolókészlet és a Data Protection Manager-adatbázis helyreállítási pontok megőrzése.
  > - A Data Protection Manager-adatbázis biztonsági másolatának kell rendelkeznie. Állítsa vissza az adatbázist kell.

1. A kijelzőpanelen válassza ki az ügyfélszámítógépek, amelynek meg szeretné frissíteni a védelmi ügynök.
2. Leállítása az eredeti Azure backup server, vagy ki az átviteli állapotúra.
3. Állítsa vissza a számítógépfiókot az active Directoryban.
4. Telepítse a Server 2016 új gépen, és nevezze el a gép neve megegyezik az eredeti Azure Backup server.
5. A tartományhoz
6. Telepítse az Azure Backup server V2 vagy újabb (helyezze át a DPM tárolókészlet-lemezeit a régi kiszolgáló és az importálás)
7. Állítsa vissza a dpmdb-t 1. lépésében foglalt.
8. A tároló csatlakoztatása az eredeti biztonsági mentési kiszolgálóról az új kiszolgálóra.
9. Az SQL állítsa vissza a dpmdb-t
10. A rendszergazdai parancssorból az új kiszolgáló cd, a Microsoft Azure Backup telepítése a hely és a bin mappa

Elérési út példa: C:\windows\system32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
az Azure backup futtassa a DPMSYNC-SYNC

10) Futtassa a DPMSYNC-SYNC Megjegyzés felvett új lemezeket a DPM tárolókészlethez áthelyezése a régiek helyett majd futtassa a DPMSYNC - reallocatereplica parancsot

## <a name="network-connectivity"></a>Hálózati kapcsolat
Az Azure Backup Server az Azure Backup szolgáltatás a termék sikeres működéséhez kapcsolat szükséges. Ellenőrizze, hogy a gépe képes az Azure-ba, használja a ```Get-DPMCloudConnection``` parancsmagot az Azure Backup Server PowerShell-konzolon. Ha a parancsmag kimenete értéke igaz, akkor a kapcsolat létezik, ellenkező esetben nincs kapcsolat.

Egy időben az Azure-előfizetést kell lennie a kifogástalan állapotban. Ismerje meg, az előfizetés állapotát, és a felügyelet, jelentkezzen be a [előfizetési portálra](https://account.windowsazure.com/Subscriptions).

Ha már tudja, hogy az állapot, az Azure-kapcsolat és az Azure-előfizetést, az alábbi táblázat segítségével ismerje meg, hogy a művelet hatása a biztonsági mentési és visszaállítási funkció érhető el.

| Kapcsolati állapota | Azure-előfizetés | Azure biztonsági mentés | Biztonsági mentés lemezre | Állítsa vissza az Azure-ból | Lemez visszaállítása |
| --- | --- | --- | --- | --- | --- |
| Csatlakozva |Aktív |Engedélyezve |Engedélyezve |Engedélyezve |Engedélyezve |
| Csatlakozva |Elévült |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Csatlakozva |– Figyelmeztetés megszüntetésről |Leállítva |Leállítva |A leállított és az Azure helyreállítási pontok törlése |Leállítva |
| Kapcsolat > 15 napon keresztül |Aktív |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Kapcsolat > 15 napon keresztül |Elévült |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Kapcsolat > 15 napon keresztül |– Figyelmeztetés megszüntetésről |Leállítva |Leállítva |A leállított és az Azure helyreállítási pontok törlése |Leállítva |

### <a name="recovering-from-loss-of-connectivity"></a>A kapcsolat megszakadása helyreállítása
Ha van egy tűzfal vagy proxy, amely megakadályozza, hogy hozzáférést az Azure-ba, szüksége, amelyeket engedélyezni kell a következő tartomány címeket, a tűzfal vagy proxy profil:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Miután a kapcsolat az Azure-bA az Azure Backup Server gép vissza lett állítva, a végrehajtható műveletek az Azure-előfizetés állapota határozza meg. A fenti táblázatban rendelkezik engedélyezett, ha a gép "csatlakoztatva" műveletek részleteit.

### <a name="handling-subscription-states"></a>– Előfizetési állapotok kezelése
Az Azure-előfizetéssel is meg lehet egy *lejárt* vagy *megszüntetett* állapotát a *Active* állapota. Azonban ez rendelkezik néhány következmények a termék működését, amíg az állapota nem *aktív*:

* A *megszüntetett* előfizetés elveszti a funkciót, a rendszer eltávolította a következő időszakra vonatkozóan. Bekapcsolása folyamatban lévő *aktív*, a termék biztonsági mentési és visszaállítási funkciójának újjáélesztett van. A biztonsági mentés a helyi lemezen lévő adatokat is lekérhetők Ha elég nagy megőrzési idővel rendelkező tartották. Azonban a biztonsági mentési adatok az Azure-ban végérvényesen elvesznek az előfizetés kerül, ha a *megszüntetett* állapota.
* Egy *lejárt* előfizetés csak elveszíti a funkciót, amíg nem lett végrehajtva *aktív* újra. Bármely, az időszak, amely az előfizetés volt ütemezett biztonsági mentések *lejárt* nem fog futni.

## <a name="upgrade-mabs"></a>MABS frissítése
Az alábbi eljárásokat követve frissíthet a MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Frissítés a MABS V2 a V3

> [!NOTE]

> MABS V2 nem áll a MABS V3 telepítésének előfeltétele. Azonban frissítheti a MABS V3 csak a MABS V2.

A következő lépések segítségével frissítse a MABS:

1. Frissítse MABS V2 MABS v3-as, frissítse az operációs rendszer Windows Server 2016 vagy Windows Server 2019 szükség esetén.

2.  Frissítse a kiszolgálót. A lépések hasonlóak [telepítési](#install-and-upgrade-azure-backup-server). Az SQL-beállítások, kap egy beállítást, az SQL-példány frissítése SQL 2017-ben a, vagy használja a saját példányát az SQL server 2017.

  > [!NOTE]

  > Ne lépjen ki az SQL-példány frissítés alatt áll, kilép fogja eltávolítani az SQL-jelentéskészítési példány, és ezért újból frissíteni a MABS tett kísérlet sikertelen lesz.

  Nagyon fontos szempont, vegye figyelembe:

  > [!IMPORTANT]

  >  SQL 2017-verziófrissítés részeként a Microsoft SQL titkosítási kulcsok biztonsági mentése, és távolítsa el a jelentéskészítési szolgáltatási. Az SQL server frissítése után jelentéskészítési service(14.0.6827.4788) telepítve van, és a titkosítási kulcsok vissza vannak állítva.

3. Frissítse a védelmi ügynököket a védett kiszolgálókon.
4. Biztonsági mentések továbbra is az üzemi kiszolgálók újraindítása nélkül.
5. Megkezdheti az adatok védelmének most. Ha frissít a Modern Backup Storage védelmét, miközben azt is beállíthatja a köteteken lévő biztonsági másolatok tárolására, és keressen kiosztott terület alatt szeretné. [További információk](backup-mabs-add-storage.md).

> [!NOTE]

> Ha frissít a MABS V1, V2, győződjön meg arról, hogy az operációs rendszer Windows Server 2016 vagy Windows Server 2012 R2. Kihasználhatja az új funkciók, például a System Center 2016 Data Protection Manager Modern biztonsági mentési tár, Backup Server V2 telepíteni kell a Windows Server 2016-ban. Előtt, frissítsen, vagy telepítse a Backup Server V2, olvassa el a [telepítésének előfeltételei](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) a MABS vonatkozik.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a Microsoft Azure Backup server hibákkal meghiúsul, a telepítési fázis (vagy biztonsági mentése és visszaállítása) során, tekintse meg a [hiba kódok dokumentum](https://support.microsoft.com/kb/3041338) további információt.
Emellett olvassa el [Azure Backup kapcsolatos gyakori kérdések](backup-azure-backup-faq.md)

## <a name="next-steps"></a>További lépések
Részletes információkat szerezhet a [a környezet előkészítése a DPM](https://technet.microsoft.com/library/hh758176.aspx) a Microsoft TechNet webhelyen. Támogatott konfigurációk, amelyen az Azure Backup Server telepíthető és használható kapcsolatos információkat is tartalmaz. Használhat egy sorozatát [PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) különféle műveletek végrehajtásához.

Ezek a cikkek segítségével mélyebb betekintést nyerni a munkaterhelések védelme a Microsoft Azure Backup server használatával.

* [Az SQL Server biztonsági másolat](backup-azure-backup-sql.md)
* [A SharePoint server biztonsági másolat](backup-azure-backup-sharepoint.md)
* [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
