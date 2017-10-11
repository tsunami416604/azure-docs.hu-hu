---
title: "Azure Backup Server használatával-munkaterhelések biztonsági mentése a klasszikus Azure portálon |} Microsoft Docs"
description: "Győződjön meg arról, hogy a környezet megfelelő előkészítése az Azure Backup Server használatával-munkaterhelések biztonsági mentése"
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
keywords: "az Azure biztonsági mentési kiszolgáló; mentési tároló"
ms.assetid: d86450e8-da63-4283-8fd7-2ee629fa14ab
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.openlocfilehash: ffef289e154986e4b08a072d3a95f77818fb9c35
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Számítási feladatok biztonsági mentésének előkészítése az Azure Backup Serverrel
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Az Azure Backup Server (klasszikus)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (klasszikus)](backup-azure-dpm-introduction-classic.md)
>
>

Ez a cikk tárgya az Azure Backup Server használatával-munkaterhelések biztonsági mentése a környezet előkészítése. Az Azure Backup Server a Hyper-V virtuális gépek, a Microsoft SQL Server, a SharePoint Server, a Microsoft Exchange és a Windows-ügyfelek például alkalmazások és szolgáltatások védelmet biztosíthat egyetlen konzolról.

> [!WARNING]
> Az Azure Backup Server örökli a munkaterhelések biztonsági mentéséhez a Data Protection Manager (DPM) funkcióit. Ezek a képességek egy részénél a DPM dokumentációjának mutató hivatkozások találhatók. Azonban Azure Backup Server nem adja meg a védelem szalagon vagy integrálása a System Center.
>
>

## <a name="1-windows-server-machine"></a>1. Windows Server-számítógépen
![1. lépés](./media/backup-azure-microsoft-azure-backup/step1.png)

Az első lépés az Azure Backup Server használatba felé, hogy a Windows Server-számítógépen.

| Hely | Minimumkövetelmények | További utasítások |
| --- | --- | --- |
| Azure |Az Azure infrastruktúra-szolgáltatási virtuális gép<br><br>A2 méretű Standard: 2 mag, 3.5-ös GB RAM |Egy egyszerű gyűjteménye a Windows Server 2012 R2 Datacenter képe indítható. [Azure Backup Server (DPM) használatával IaaS munkaterhelések védelme](https://technet.microsoft.com/library/jj852163.aspx) sok apró rendelkezik. Győződjön meg arról, hogy olvassa el a cikk teljesen a gép üzembe helyezése előtt. |
| Helyszíni követelmények |A Hyper-V virtuális gépek<br> VMWare virtuális gép<br> vagy a fizikai gazdagép<br><br>2 processzor és 4GB RAM |A DPM-tároló, a Windows Server deduplikálásával is deduplikálása. További tudnivalók [DPM és deduplikáció](https://technet.microsoft.com/library/dn891438.aspx) együttműködése a Hyper-V virtuális gépek telepítésekor. |

> [!NOTE]
> Javasoljuk, hogy az Azure Backup Server telepíthető-e a virtuális gép Windows Server 2012 R2 Datacenter. Az Előfeltételek számos automatikusan tartoznak a Windows operációs rendszerének legújabb verziójával.
>
>

Ha azt tervezi, Azure Backup Server csatlakoztatása a tartományhoz, javasoljuk, hogy csatlakozik a fizikai kiszolgáló vagy a virtuális gép tartományhoz az Azure Backup Server szoftver telepítése előtt. Egy Azure Backup Server megköveteli az új tartományba, a telepítést követően *nem támogatott*.

## <a name="2-backup-vault"></a>2. Mentési tároló
![2. lépés](./media/backup-azure-microsoft-azure-backup/step2.png)

Biztonsági mentési adatok küldése az Azure-ba, vagy helyileg megakadályozza, hogy az Azure Backup Server szerepelnie kell egy tárolóba. Ha egy új Azure Backup-felhasználó, és szeretné használni az Azure Backup Server, tekintse meg a az Azure portál-verzió az ennek a cikknek - [Felkészülés az Azure Backup Server használatával-munkaterhelések biztonsági mentése](backup-azure-microsoft-azure-backup.md).

> [!IMPORTANT]
> 2017 márciusától már nem hozhat létre Backup-tárolókat a klasszikus portálon.
> A biztonsági mentési tárakról mostantól lehetőség van helyreállítási tárakra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> 2017. október 15-től a PowerShell nem használható Backup-tárolók létrehozására. **2017. november 1-től**:
>- Minden fennmaradó Backup-tároló automatikusan Recovery Services-tárolóra frissül.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>



## <a name="3-software-package"></a>3. Szoftvercsomag
![3. lépés](./media/backup-azure-microsoft-azure-backup/step3.png)

### <a name="downloading-the-software-package"></a>A csomag letöltése
Hasonló tároló hitelesítő adatait, letöltheti a Microsoft Azure Backup szolgáltatás számára az alkalmazások és szolgáltatások a **gyors kezdés lapon** mentési tároló.

1. Kattintson a **az alkalmazások és szolgáltatások (lemezről lemezre történő a felhőbe)**. Ekkor megjelenik a letöltőközpontból oldalra, ahol a csomag letölthető a.

    ![A Microsoft Azure biztonsági mentési üdvözlőképernyője](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)
2. Kattintson a **Letöltés** gombra.

    ![A letöltőközpontból 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)
3. Válassza ki a fájlokat, és kattintson a **következő**. Töltse le a Microsoft Azure Backup letöltési oldala érkező összes fájlt, és a fájlokat helyezze ugyanabba a mappába.
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
2. Az üdvözlőképernyőn kattintson a **következő** gombra. Ehhez szükséges, hogy a *előfeltételek ellenőrzésének* szakasz. Ezen a képernyőn kattintson a a **ellenőrizze** gombra kattintva határozza meg, ha az Azure Backup Server hardver- és előfeltételek teljesülnek. Ha az előfeltételek mindegyike teljesül sikeres, megjelenik egy üzent arról, hogy a gép megfelel-e a követelményeknek. Kattintson a **következő** gombra.

    ![Az Azure Backup Server - üdvözlő és az Előfeltételek ellenőrzése](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server szükséges SQL Server Standard, és az Azure Backup Server telepítési csomag részeként elérhető csomagolt szükséges megfelelő SQL Server bináris fájljait. Egy új Azure Backup Server telepítésének indításakor ki kell választania a beállítás **ezzel a beállítással új SQL Server-példány telepítése** , és kattintson a **ellenőrzés és telepítés** gombra. Ha az előfeltételek telepítése sikeresen megtörtént, kattintson **következő**.

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

    A következő lépés, hogy a Microsoft Azure Recovery Services Agent konfigurálása. A konfigurálás részeként meg kell adnia az Ön regisztrálni a gépet a mentési tárolóban tárolói hitelesítő adatokat. Az Azure és a helyszínen között küldött adatok titkosításához/visszafejtéséhez egy hozzáférési kódot is biztosítja. Automatikusan egy hozzáférési kódot létrehozni, vagy adja meg a saját legalább 16 karakterből álló jelszót. A varázsló folytatásához, amíg az ügynök nincs konfigurálva.

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

## <a name="4-network-connectivity"></a>4. Hálózati kapcsolat
![step4](./media/backup-azure-microsoft-azure-backup/step4.png)

Az Azure Backup Server esetén az Azure Backup szolgáltatás a termék sikeres működéséhez kapcsolat szükséges. Hogy a számítógép rendelkezik-e az Azure-bA kapcsolattal érvényesítéséhez használja a ```Get-DPMCloudConnection``` parancsmag az Azure Backup Server PowerShell-konzolban. Ha a parancsmag kimenete értéke igaz, akkor van kapcsolat, ellenkező esetben nincs kapcsolat.

Egy időben az Azure-előfizetést kell lennie állapota kifogástalan. Az előfizetés állapotának megállapítása és a kezeléséhez jelentkezzen be a [előfizetés portal](https://account.windowsazure.com/Subscriptions).

Miután eldöntötte, hogy az Azure-előfizetés és Azure kapcsolat állapotát, az alábbi táblázat segítségével megtudhatja, milyen hatással a biztonsági mentés/visszaállítás funkciói.

| Kapcsolati állapota | Azure-előfizetés | Az Azure biztonsági mentés | Lemezes biztonsági mentés | Állítsa vissza az Azure-ból | Állítsa vissza a lemezről |
| --- | --- | --- | --- | --- | --- |
| Csatlakoztatva |Aktív |Engedélyezett |Engedélyezett |Engedélyezett |Engedélyezett |
| Csatlakoztatva |Lejárt |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Csatlakoztatva |Platformelőfizetés |Leállítva |Leállítva |Leállított és az Azure helyreállítási pontjainak törlése |Leállítva |
| Elveszett kapcsolat > 15 nap |Aktív |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Elveszett kapcsolat > 15 nap |Lejárt |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
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

## <a name="next-steps"></a>Következő lépések
Részletes információt kaphat a [a környezet előkészítése a DPM](https://technet.microsoft.com/library/hh758176.aspx) a Microsoft TechNet webhelyen. Támogatott konfigurációk, amelyen Azure Backup Server telepítése és használt adatait is tartalmazza.

Ezek a cikkek segítségével egy mélyrehatóbb ismereteket szerezhet a munkaterhelések védelme a Microsoft Azure Backup server.

* [SQL Server biztonsági másolat](backup-azure-backup-sql.md)
* [A SharePoint server biztonsági másolat](backup-azure-backup-sharepoint.md)
* [Alternatív kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
