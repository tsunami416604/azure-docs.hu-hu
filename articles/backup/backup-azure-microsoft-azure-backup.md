---
title: -Munkaterhelések biztonsági mentése az Azure-bA az Azure Backup Server használatával |} Microsoft Docs
description: Azure Backup Server használatával védeni, vagy a munkaterhelések biztonsági mentése az Azure portálon.
services: backup
documentationcenter: ''
author: PVRK
manager: shivamg
editor: ''
keywords: az Azure biztonsági mentési kiszolgáló; munkaterhelések; védelme -munkaterhelések biztonsági mentése
ms.assetid: e7fb1907-9dc1-4ca1-8c61-50423d86540c
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/14/2018
ms.author: masaran;trinadhk;pullabhk;markgal;adigan
ms.openlocfilehash: ef6be97144d05f18362ef707ef255b93c8cf21d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Számítási feladatok biztonsági mentésének előkészítése az Azure Backup Serverrel
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Ez a cikk azt ismerteti, hogyan készítse fel a környezetet az Azure Backup Server használatával-munkaterhelések biztonsági mentése. Az Azure Backup Server a Hyper-V virtuális gépek, a Microsoft SQL Server, a SharePoint Server, a Microsoft Exchange és a Windows-ügyfelek például alkalmazások és szolgáltatások védelmet biztosíthat egyetlen konzolról.

> [!NOTE]
> Az Azure Backup Server most megvédheti a VMware virtuális gépeket, és magasabb szintű biztonságra képességeket biztosít. A termék telepítését, a lentebbi; 1. frissítés és a legújabb Azure Backup szolgáltatás ügynökének vonatkoznak. Az Azure Backup Server VMware-kiszolgálók biztonsági mentésével kapcsolatos további tudnivalókért tekintse meg a cikket, [használata Azure biztonsági mentés kiszolgáló biztonsági mentése a VMware server](backup-azure-backup-server-vmware.md). Biztonsági képességeivel kapcsolatos további tudnivalókért tekintse meg [Azure biztonsági mentési biztonsági jellemzőkkel dokumentáció](backup-azure-security-feature.md).
>
>

Infrastruktúra, a szolgáltató (IaaS) munkaterhelések, például az Azure virtuális gépeken is védheti.

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, a telepített, a Resource Manager modellt használó virtuális gépek visszaállítására.
>
>

Az Azure Backup Server örökli nagy részét a munkaterhelés biztonsági mentési funkció a Data Protection Manager (DPM). Ez a cikk a DPM dokumentációjának ismertetnek néhányat az megosztott funkciókat mutató hivatkozásokat tartalmaz. Azure Backup Server, ha nagy része megegyezik a DPM funkcióval megosztja. Az Azure Backup-kiszolgáló nem a szalagra történő biztonsági mentése, és nem integrálható a System Center.

## <a name="choose-an-installation-platform"></a>Válasszon egy telepítési platform
Az első lépés az Azure Backup Server használatba felé, hogy állítson be egy Windows Server. A kiszolgáló Azure vagy a helyszíni lehet.

### <a name="using-a-server-in-azure"></a>Az Azure-kiszolgáló használatával
Egy Azure Backup Servert futtató kiszolgáló kiválasztásakor ajánlott a kiindulási pont egy Windows Server 2012 R2 Datacenter vagy a Windows Server 2016 Datacenter gyűjtemény képe. A cikk [az első Windows rendszerű virtuális gép létrehozása az Azure portálon](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), nyújt segítséget Ismerkedés az Azure, az ajánlott virtuális gép még akkor is, ha soha nem használta az Azure-t. A virtuális gép (VM) ajánlott minimális követelményei kell lennie: A2 szabványosnak kétmagos processzor és 3.5-ös GB RAM-MAL.

Sok apró munkaterhelések védelme a Azure Backup Server rendelkezik. A cikk [DPM telepítése Azure virtuális gépként](https://technet.microsoft.com/library/jj852163.aspx), segítséget nyújt a apró ismertetik. A gép történő telepítése előtt olvassa el ebben a cikkben teljesen.

### <a name="using-an-on-premises-server"></a>A helyszíni kiszolgáló használata
Ha nem szeretné, hogy az Azure-ban az alap server futtatásához, a kiszolgáló futtathatja a Hyper-V virtuális gépek, VMware virtuális gép vagy egy fizikai gazdagéphez. A Kiszolgálóhardver ajánlott minimális követelményei, kétmagos processzor és 4 GB RAM-MAL. A támogatott operációs rendszerek a következő táblázatban láthatók:

| Operációs rendszer | Platform | SKU |
|:--- | --- |:--- |
| Windows Server 2016 és a legújabb szervizcsomag-verzió |64 bit |Standard, Datacenter, Essentials (MABS v2 onwards) |
| Windows Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 és a legújabb szervizcsomagok |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |

A DPM-tároló, a Windows Server deduplikálásával is deduplikálása. További tudnivalók [DPM és deduplikáció](https://technet.microsoft.com/library/dn891438.aspx) együttműködése a Hyper-V virtuális gépek telepítésekor.

> [!NOTE]
> Az Azure Backup Server futtatásra tervezték, egy dedikált, meghatározott célú kiszolgálón. Az Azure Backup Server nem telepíthető:
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amely System Center Operations Manager felügyeleti kiszolgálóként működik
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógépre, amely fürtcsomópontként működik

Azure Backup Server mindig csatlakoztatása a tartományhoz. Ha a kiszolgáló egy másik tartományba helyezi át, először telepítse az Azure Backup Server, majd csatlakoztassa a kiszolgálót az új tartományhoz. Egy meglévő Azure Backup Server gépet áthelyezése egy új tartományba, telepítés *nem támogatott*.

Biztonsági mentési adatok küldése az Azure-ba, vagy helyileg legyen, hogy a helykiszolgáló biztonsági mentése Azure szerepelnie kell a Recovery Services-tároló.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a Recovery Services-tárolók georedundáns tárolás használata. Ha ebben a tárolóban a elsődleges tároló, hagyja a tárolási beállítást, a georedundáns tárolást. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tároló, a tároló irányítópult és a beállítások menü megnyitásához. Ha a **beállítások** menü nem nyitható meg **összes beállítás** a tároló irányítópultjának.
2. A a **beállítások** menüben kattintson a **biztonsági infrastruktúra** > **biztonsági mentési konfigurációhoz** megnyitásához a **biztonsági mentési konfigurációhoz**panelen. Az a **biztonsági mentési konfigurációhoz** menüben válassza ki a tárolási replikációs beállítás a tároló számára.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Miután kiválasztotta a tárolási beállítást a tároló számára, készen áll, hogy hozzárendelje a virtuális gépet a tárolóhoz. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

## <a name="software-package"></a>Szoftvercsomag
### <a name="downloading-the-software-package"></a>A csomag letöltése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Ha már rendelkezik egy Recovery Services-tároló nyitva, folytassa a 3. Ha nem rendelkezik a Recovery Services-tároló nyílt, de az Azure portálon, a főmenü kattintson **Tallózás**.

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

     ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     A Recovery Services-tárolók listája megjelenik.
   * A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.

     ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. A **beállítások** alapértelmezés szerint megnyílik panelen. Ha be van zárva, kattintson a **beállítások** beállítások panel megnyitásához.

    ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kattintson a **biztonsági mentés** az első lépések varázsló megnyitásához.

    ![Biztonsági mentés első lépések](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Az a **Ismerkedés a biztonsági mentés** panelt megnyitó, **biztonsági mentési célok** lesz automatikusan kiválasztva.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Az a **biztonsági mentési cél** panelen a a **a számítási feladatok futtató** menüjében válassza **helyszíni**.

    ![a helyszíni és a munkaterhelések célok](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Az a **miről szeretne biztonsági másolatot készíteni?** legördülő menüben válassza ki az Azure Backup Server használatával védeni kívánt munkaterhelések, és kattintson **OK**.

    A **Ismerkedés a biztonsági mentés** varázsló kapcsolók a **infrastruktúra előkészítése** beállítással-munkaterhelések biztonsági mentése az Azure-bA.

   > [!NOTE]
   > Ha szeretné fájlok és mappák biztonsági mentése, ajánlott az Azure Backup-ügynök használatával, és a cikk útmutatása [először: fájlok és mappák biztonsági mentését](backup-try-azure-backup-in-10-mins.md). Ha több, mint a fájlokat és mappákat védeni kívánja, vagy a jövőben bontsa ki a védelmi igényeinek szeretne, válassza ki azokat a munkaterheléseket.
   >
   >

    ![Első lépések varázsló módosítása](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Az a **infrastruktúra előkészítése** panelen kattintson a **letöltése** Azure Backup Server telepítése és a letöltési tárolói hitelesítő adatokat. A recovery services-tároló az Azure Backup Server regisztráció során a tárolói hitelesítő adatokat használja. A hivatkozások a letöltőközpontból, amelyben a szoftvercsomag tölthető le.

    ![Az Azure Backup Server infrastruktúra előkészítése](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Válassza ki a fájlokat, és kattintson a **következő**. Töltse le a Microsoft Azure Backup letöltési oldala érkező összes fájlt, és a fájlokat helyezze ugyanabba a mappába.

    ![A letöltőközpontból 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Mivel a fájlok letöltési mérete nem haladhatja meg > 3G, a 10 MB/s le hivatkozást a letöltés befejezéséhez legfeljebb 60 percig is eltarthat.

### <a name="extracting-the-software-package"></a>A csomag kibontása
A fájlok letöltése után kattintson **MicrosoftAzureBackupInstaller.exe**. Ekkor elindul a **Microsoft Azure biztonsági mentés telepítővarázsló** kibontásához a telepítési fájlokat, Ön által meghatározott helyre. Kövesse a varázsló lépéseit, majd kattintson a a **kibontása** gombra a kinyerési folyamat megkezdéséhez.

> [!WARNING]
> Bontsa ki a telepítési fájlok legalább 4GB szabad lemezterület szükséges.
>
>

![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Ha a kinyerési folyamat befejeződött, a jelölőnégyzet bejelölésével indítsa el a kibontott frissen *setup.exe* elindítja a Microsoft Azure Backup Server telepítése, és kattintson a a **Befejezés** gombra.

### <a name="installing-the-software-package"></a>A csomag telepítése
1. Kattintson a **a Microsoft Azure Backup szolgáltatás** a telepítő varázsló elindításához.

    ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Az üdvözlőképernyőn kattintson a **következő** gombra. Ehhez szükséges, hogy a *előfeltételek ellenőrzésének* szakasz. Ezen a képernyőn kattintson a **ellenőrizze** meghatározni, ha az Azure Backup Server hardver- és előfeltételek teljesülnek. Ha minden előfeltétele sikeresen, látni fogja egy üzenet, amely azt jelzi, hogy a gép megfelel-e a követelményeknek. Kattintson a **következő** gombra.

    ![Az Azure Backup Server - üdvözlő és az Előfeltételek ellenőrzése](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server szükséges SQL Server Standard. További az Azure Backup Server telepítési csomag részeként elérhető csomagolt a megfelelő SQL Server bináris fájljait, akkor szükséges, ha nem szeretné használni a saját SQL. Egy új Azure Backup Server telepítésének indításakor ki kell választania a beállítás **ezzel a beállítással új SQL Server-példány telepítése** , és kattintson a **ellenőrzés és telepítés** gombra. Ha az előfeltételek telepítése sikeresen megtörtént, kattintson **következő**.

    ![Az Azure Backup Server - SQL ellenőrzése](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Ha hiba lép fel, indítsa újra a gépet ajánlás, ehhez, és kattintson az **ellenőrizze újra**.

   > [!NOTE]
   > Az Azure Backup Server egy távoli SQL Server-példány nem fog működni. Az Azure Backup Server által használt példány kell elhelyezkednie.
   >
   >
4. Adjon meg egy helyet, a Microsoft Azure Backup server fájlok telepítéséhez, majd kattintson **következő**.

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Az ideiglenes helyen történő biztonsági mentés Azure feltétele. Győződjön meg arról, az ideiglenes helye az adatok biztonsági mentése a felhőbe tervezett legalább 5 %-át. A lemezvédelem különálló lemezek be kell állítani a telepítés befejeződése után. Tárolókészletek kapcsolatos további információkért lásd: [tárolókészletek konfigurálása és a lemezes tárolás](https://technet.microsoft.com/library/hh758075.aspx).
5. Adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **következő**.

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Adja meg, hogy a használni kívánt *Microsoft Update* frissítések keresését, és kattintson a **következő**.

   > [!NOTE]
   > Javasoljuk, a Windows Update, Microsoft Update szolgáltatásban, ami biztonsági és fontos frissítéseket kínál a Windowshoz és más termékek, például a Microsoft Azure Backup Server átirányítása.
   >
   >

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Tekintse át a *összegzése a beállítások* kattintson **telepítése**.

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A telepítési fázisban történik. Az első fázisban a Microsoft Azure Recovery Services Agent telepítve van a kiszolgálón. A varázsló is ellenőrzi az internetkapcsolat. Ha internetkapcsolat érhető el nyugodtan folytathatja a telepítést, ha nem, meg kell adnia a proxy adatait, az internethez való kapcsolódáshoz.

    A következő lépés, hogy a Microsoft Azure Recovery Services Agent konfigurálása. A konfigurálás részeként meg kell adnia a tárolói hitelesítő adatokat regisztrálni a gépet, hogy a recovery services-tároló. Az Azure és a helyszínen között küldött adatok titkosításához/visszafejtéséhez egy hozzáférési kódot is biztosítja. Automatikusan egy hozzáférési kódot létrehozni, vagy adja meg a saját legalább 16 karakterből álló jelszót. A varázsló folytatásához, amíg az ügynök nincs konfigurálva.

    ![Az Azure biztonsági mentési Serer PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Miután a Microsoft Azure Backup-kiszolgáló regisztrálása sikeresen befejeződött, a teljes telepítővarázsló onnantól telepítésével és konfigurálásával, az SQL Server és az Azure Backup Server-összetevőt. Az SQL Server-összetevő telepítése után az Azure biztonsági mentés kiszolgáló-összetevők telepítve vannak.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

A telepítési lépés befejezése után a termék asztali ikonok lesz létrehozva is. Csak az ikonra duplán kattintva indítsa el a terméket.

### <a name="add-backup-storage"></a>Biztonsági mentési tároló hozzáadása
Az első biztonsági másolat tárhely az Azure Backup Server géphez csatolt tárhelyen tárolnia. Lemezek hozzáadásával kapcsolatos további információkért lásd: [tárolókészletek konfigurálása és a lemezes tárolás](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Kell hozzáadnia a biztonságimásolat-tároláshoz, akkor is, ha azt tervezi, hogy az adatok küldése az Azure-bA. Az Azure Backup Server aktuális architektúra, az Azure mentési tároló tartalmazza a *második* a helyi tároló közben az adatok másolatát tartalmazza az első (és kötelező) biztonsági másolatot.
>
>

## <a name="network-connectivity"></a>Hálózati kapcsolat
Az Azure Backup Server esetén az Azure Backup szolgáltatás a termék sikeres működéséhez kapcsolat szükséges. Hogy a számítógép rendelkezik-e az Azure-bA kapcsolattal érvényesítéséhez használja a ```Get-DPMCloudConnection``` parancsmag az Azure Backup Server PowerShell-konzolban. Ha a parancsmag kimenete értéke igaz, akkor van kapcsolat, ellenkező esetben nincs kapcsolat.

Egy időben az Azure-előfizetést kell lennie állapota kifogástalan. Az előfizetés állapotának megállapítása és a kezeléséhez jelentkezzen be a [előfizetés portal](https://account.windowsazure.com/Subscriptions).

Miután eldöntötte, hogy az Azure-előfizetés és Azure kapcsolat állapotát, az alábbi táblázat segítségével megtudhatja, milyen hatással a biztonsági mentés/visszaállítás funkciói.

| Kapcsolati állapota | Azure-előfizetés | Azure biztonsági mentés | Biztonsági mentés lemezre | Állítsa vissza az Azure-ból | Állítsa vissza a lemezről |
| --- | --- | --- | --- | --- | --- |
| Csatlakozva |Aktív |Engedélyezve |Engedélyezve |Engedélyezve |Engedélyezve |
| Csatlakozva |Elévült |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Csatlakozva |Platformelőfizetés |Leállítva |Leállítva |Leállított és az Azure helyreállítási pontjainak törlése |Leállítva |
| Elveszett kapcsolat > 15 nap |Aktív |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Elveszett kapcsolat > 15 nap |Elévült |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Elveszett kapcsolat > 15 nap |Platformelőfizetés |Leállítva |Leállítva |Leállított és az Azure helyreállítási pontjainak törlése |Leállítva |

### <a name="recovering-from-loss-of-connectivity"></a>Végezze el a kapcsolat megszakadása
Ha egy tűzfal vagy egy proxy, amely megakadályozza hozzáférés az Azure-ba, kell engedélyezett a következő tartomány címek a tűzfal /-proxy profil:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Amennyiben a kapcsolat az Azure-bA az Azure Backup Server géphez visszaállítása sikeresen megtörtént, a végrehajtható műveletek az Azure-előfizetés állapota határozza meg. A fenti táblázatban engedélyezett, ha a számítógép "kapcsolódó" műveleteire vonatkozó részleteket tartalmaz.

### <a name="handling-subscription-states"></a>– Előfizetési állapotok kezelése
Lehetséges az Azure-előfizetés érvénybe egy *lejárt* vagy *Deprovisioned* állapotát a *aktív* állapotát. Azonban ez rendelkezik néhány hatással vannak a termék működését az állapot nem *aktív*:

* A *Deprovisioned* előfizetés elveszti a funkció akkor van platformelőfizetés időszakra vonatkozóan. Forduljon a *aktív*, a szolgáltatások a biztonsági mentés/visszaállítás újjáélesztett van. A biztonsági mentési adatok a helyi lemezen is esetén lekérhetők tartották megfelelően nagy megőrzési időtartam. Azonban a biztonsági mentési adatok az Azure-ban végérvényesen elvesznek az előfizetés kerül, ha a *Deprovisioned* állapotát.
* Egy *lejárt* előfizetés csak elveszíti funkcióját, amíg nem lett végrehajtva *aktív* újra. Bármely az időtartamot, ameddig az előfizetés az ütemezett biztonsági mentések *lejárt* nem fog futni.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a Microsoft Azure Backup server hibákkal megszakad, miközben a telepítési fázis (vagy biztonsági mentési vagy visszaállítási), tekintse meg a [hiba kódok dokumentum](https://support.microsoft.com/kb/3041338) további információt.
Azt is jelentheti [Azure biztonsági mentés kapcsolatos gyakori kérdések](backup-azure-backup-faq.md)

## <a name="next-steps"></a>További lépések
Részletes információt kaphat a [a környezet előkészítése a DPM](https://technet.microsoft.com/library/hh758176.aspx) a Microsoft TechNet webhelyen. Támogatott konfigurációk, amelyen Azure Backup Server telepítése és használt adatait is tartalmazza.

Ezek a cikkek segítségével egy mélyrehatóbb ismereteket szerezhet a munkaterhelések védelme a Microsoft Azure Backup server.

* [SQL Server biztonsági másolat](backup-azure-backup-sql.md)
* [A SharePoint server biztonsági másolat](backup-azure-backup-sharepoint.md)
* [Alternatív kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
