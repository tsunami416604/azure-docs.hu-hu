---
title: Az Azure Backup Server telepítése az Azure Stackben |} A Microsoft Docs
description: Az Azure Backup Server használatával védeni, vagy az Azure Stack-munkaterhelések biztonsági mentéséhez.
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
keywords: az Azure backup server; számítási feladatok; védelme számítási feladatainak biztonsági mentése
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 6/5/2018
ms.author: raynew
ms.openlocfilehash: 31052276f06c5afdf78bf094637b7f60be4acbec
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262727"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server telepítése az Azure Stacken

Ez a cikk ismerteti az Azure Backup Server telepítése az Azure Stacken. Az Azure Backup Server infrastruktúra-szolgáltatás (IaaS) számítási feladatok, például a virtuális gépek az Azure Stackben futnak, védheti. A munkaterhelések védelme érdekében az Azure Backup Server használatának előnye minden munkaterhelés-védelem egy egyetlen konzolról felügyelheti.

> [!NOTE]
> Biztonsági képességeivel kapcsolatos további információkért tekintse meg [Azure Backup biztonsági funkciók dokumentációja](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Az Azure Backup Server védelmi mátrixa
Az Azure Backup Server védi az Azure Stack következő virtuális gépek számítási feladataihoz.

| Védett adatforrás | Védelem és helyreállítás |
| --------------------- | ----------------------- |
| A Windows Server félig féléves csatorna – Datacenter és Enterprise/Standard | Kötetek, fájlok, mappák |
| A Windows Server 2016 – Datacenter és Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 - Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| SQL Server 2016 | Adatbázis |
| SQL Server 2014 | Adatbázis |
| SQL Server 2012 SP1 | Adatbázis |
| SharePoint 2016 | Farm, adatbázis, előtér, webkiszolgáló |
| SharePoint 2013 | Farm, adatbázis, előtér, webkiszolgáló |
| SharePoint 2010 | Farm, adatbázis, előtér, webkiszolgáló |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Az Azure Backup Server környezetre vonatkozó Előfeltételek

Fontolja meg a javaslatok ebben a szakaszban, az Azure Stack-környezet az Azure Backup Server telepítésekor. Az Azure Backup Server telepítő ellenőrzi, hogy a környezet rendelkezik a szükséges előfeltételeket, de meg időt takaríthat meg előkészítése telepítése előtt.

### <a name="determining-size-of-virtual-machine"></a>Virtuális gép méretének meghatározásához
Az Azure Stack virtuális gépen az Azure Backup Server futtatásához használja a méretét a2-es vagy nagyobb. Ha segítségre van szüksége egy virtuális gép méretének kiválasztásával, töltse le a [Azure Stack-beli Virtuálisgép-méret Számológép](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Az Azure Stack virtuális gépek virtuális hálózatok
Az Azure Stack számítási használt összes virtuális gép ugyanazon az Azure virtual network és Azure-előfizetéshez kell tartoznia.

### <a name="azure-backup-server-vm-performance"></a>Az Azure Backup Server rendszerű virtuális gép teljesítmény
Ha más virtuális gépekkel megosztva, a storage-fiók mérete és IOPS korlátok hatása az Azure Backup Server rendszerű virtuális gép teljesítményét. Ebből kifolyólag használjon külön tárfiókot az Azure Backup Server virtuális gép. Az Azure Backup ügynököt futtat az Azure Backup Server van szüksége az ideiglenes tároló:
- saját használatra (gyorsítótárhely),
- (helyi átmeneti terület) a felhőből helyreállított adatok

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Az Azure Backup ideiglenes lemezes tárolás konfigurálása
Ideiglenes lemezes tárolás, a felhasználó kötetként érhető el, amely tartalmaz minden egyes Azure Stack-virtuális gép `D:\`. Az Azure Backup által igényelt helyi átmeneti terület beállítható úgy, hogy található `D:\`, és a gyorsítótár helyét is helyezhető `C:\`. Ezzel a módszerrel nem kell faragottnak, erről az Azure Backup Server virtuális géphez csatolt adatlemezek kell.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Biztonsági mentési adatok tárolására, a helyi lemez és az Azure-ban
Az Azure Backup Server biztonsági mentési adatokat tárolja, az operatív helyreállítás a virtuális géphez csatolt Azure lemezeken történik. Miután a lemezeket és tárolóhelyet a virtuális gép csatlakozik, az Azure Backup Server kezeli storage. A biztonsági mentési adatok tárolási függ a számát és méretét, az egyes csatlakoztatott lemezekkel [Azure Stack virtuális gép](../azure-stack/user/azure-stack-storage-overview.md). Minden egyes Azure Stack virtuális gép mérete rendelkezik a virtuális géphez csatolt lemezek maximális száma. Ha például A2 négy lemezek. A3 nyolc lemezek. A4 16 lemez. Újra a méret és a lemezek számát határozza meg, a biztonsági mentési tárolókészlet méretét.

> [!IMPORTANT]
> Érdemes **nem** megőrzése üzemképességet (biztonsági mentés) az Azure Backup Server-csatolású lemezeket öt napnál hosszabb.
>

Biztonsági mentési adatok tárolására az Azure-ban csökkenti a biztonsági mentési infrastruktúra az Azure Stacken. Ha öt napnál régebbi adatok, akkor az Azure-ban kell tárolni.

Az Azure-beli biztonsági mentési adatok tárolására, hozzon létre, vagy használja a Recovery Services-tároló. Biztonsági mentése az Azure Backup Server számítási feladatok előkészítésekor meg [konfigurálása a Recovery Services-tároló](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). A beállítása után minden alkalommal, amikor egy biztonsági mentési feladat fut, a helyreállítási pont létrehozása a tárolóban. Minden egyes Recovery Services-tároló 9999 helyreállítási pontokat tárolja. Létrehozott helyreállítási pontokat, és hogy mennyi ideig szeretné megőrizni azokat számától függően a biztonsági másolatok adatainak megőrzése is sok éve. Például sikerült létrehoz havi helyreállítási pontokat, és megőrizheti azok öt év során.
 
### <a name="scaling-deployment"></a>Központi telepítés méretezése
Ha azt szeretné, a telepítés méretezésére az alábbi lehetőségek állnak rendelkezésére:
  - Vertikális felskálázás – az Azure Backup Server virtuális gépet eltávolít egy sorozat a D sorozat méretének növeléséhez, és a helyi tárhely bővítése [/ az Azure Stack-virtuális gép utasításokat](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Adatok kiszervezése – régebbi adatok küldése az Azure-ba, és csak a legújabb adatokat kell az Azure Backup Server csatolt tárhelyen megőrzése.
  - Horizontális felskálázás – adjon hozzá további Azure Backup kiszolgálót a munkaterhelések védelme érdekében.

### <a name="net-framework"></a>.NET-keretrendszer

.NET-keretrendszer 3.5 SP1 vagy újabb kell telepíteni a virtuális gépen.

### <a name="joining-a-domain"></a>Csatlakozás tartományhoz

Az Azure Backup Server virtuális gépet egy tartományhoz kell csatlakoznia. Rendszergazdai jogosultságokkal rendelkező tartományi felhasználó kell telepíteni a virtuális gép Azure Backup Server.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Az IaaS virtuális gépek használata az Azure Stackben

Amikor kiválasztja, a kiszolgáló Azure Backup Server, indítsa el a Windows Server 2012 R2 Datacenter vagy a Windows Server 2016 Datacenter katalógus-lemezkép. A cikk [az első Windows virtuális gép létrehozása az Azure Portalon](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), biztosít a virtuális gép javasolt – első lépések oktatóanyag. A kiszolgáló virtuális gép (VM) vonatkozó ajánlott minimális követelményeknek kell lennie: A2 méretű standard szintű két maggal és 3,5 GB RAM.

Az Azure Backup Server számítási feladatok védelmét már sok apró sajátosságaival. A cikk [DPM telepítése Azure virtuális gépként](https://technet.microsoft.com/library/jj852163.aspx), segít ismertetik ezeket a vállalatánál. A gép üzembe helyezése előtt olvassa el ebben a cikkben teljesen.

> [!NOTE]
> Az Azure Backup Server futtatásra tervezték, dedikált, meghatározott célú virtuális gépen. Az Azure Backup Server nem telepíthető:
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógépre, amely fürtcsomópontként működik

Az Azure Backup Server mindig csatlakoztatása a tartományhoz. Ha az Azure Backup Server áthelyezése egy másik tartományban van szüksége, először az Azure Backup Server telepítése, majd csatlakoztassa az új tartományhoz. Miután az Azure Backup Server telepít, nem helyezhető egy új tartományhoz.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A Recovery Services vault tárolóreplikációs beállítást georedundáns tárolás és helyileg redundáns tárolás között válasszon teszi lehetővé. Alapértelmezés szerint a Recovery Services-tárolók használata a georedundáns tárolás. Ha ebben a tárban a elsődleges tároló, hagyja a beállítást georedundáns tárolás beállítása. Ha azt szeretné, hogy egy olcsóbb, rövidebb tartós, válassza a helyileg redundáns tárolás. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tárolót, a tároló irányítópultját és a beállítások menü megnyitásához. Ha a **beállítások** menü nem nyílik meg, kattintson a **minden beállítás** a tároló irányítópultján.
2. Az a **beállítások** menüben kattintson a **biztonsági mentési infrastruktúra** > **biztonsági mentés konfigurációja** megnyitásához a **biztonsági mentés konfigurációja**menüben. Az a **biztonsági mentés konfigurációja** menüben válassza ki a tárolóreplikációs beállítást a tároló számára.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Töltse le az Azure Backup Server

Töltse le az Azure Backup Server kétféleképpen történhet. Az Azure Backup Server telepítőt a letöltheti a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Az Azure Backup Server telepítő, konfigurál egy Recovery Services-tárolót is letöltheti. A következő lépések végigvezetik a telepítő letöltése az Azure Portalról a Recovery Services-tároló konfigurálása közben.

1. Az Azure Stack virtuális gépről [jelentkezzen be az Azure-előfizetéshez az Azure Portalon](https://portal.azure.com/).
2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![Az összes szolgáltatás lehetőséget a főmenü](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Az a **minden szolgáltatás** párbeszédpanel, írja be *Recovery Services*. Ahogy elkezd gépelni, a bemeneti szűri az erőforrások listájában. Ha azt látja, válassza ki a **Recovery Services-tárolók**.

    ![Az összes szolgáltatások párbeszédpanelen írja be a Recovery Services](./media/backup-mabs-install-azure-stack/all-services.png)

    Az előfizetés Recovery Services-tárolók listája megjelenik.

4. Recovery Services-tárolók listájából válassza ki a tárolót az irányítópult megnyitásához.

    ![Az összes szolgáltatások párbeszédpanelen írja be a Recovery Services](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Első lépések a tároló menüjében kattintson **Backup** az első lépések varázsló megnyitásához.

    ![Biztonsági mentés első lépések](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Megnyílik a biztonsági mentés menü.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. A biztonsági mentés menü, a **a számítási feladat futtató** menüjében válassza **helyszíni**. Az a **miről szeretne biztonsági másolatot készíteni?** legördülő menüben válassza a kívánt munkaterhelések védelme az Azure Backup Server használatával. Ha nem tudja, mely számítási feladatok, jelölje be, válassza ki a **Hyper-V virtuális gépek** majd **infrastruktúra előkészítése**.

    ![a helyszíni és a célokat, számítási feladatok](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    A **infrastruktúra előkészítése** menü megnyitása.

7. Az a **infrastruktúra előkészítése** menüben kattintson a **letöltése** megnyitásához az Azure Backup Server telepítési fájlok letöltési weblapon.

    ![Első lépések varázsló módosítása](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    A Microsoft Azure Backup Server, a letölthető fájlokat üzemeltető nyílik meg.

8. A Microsoft Azure Backup Server letöltési oldalon válassza ki a nyelvet, és kattintson a **letöltése**.

    ![Töltse le a center megnyitása](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Az Azure Backup Server telepítő áll nyolc fájlok - telepítőt, és hét .bin fájlt. Ellenőrizze **Fájlnév** válassza ki az összes szükséges fájlokat, és kattintson a **tovább**. Töltse le az összes fájlt ugyanabba a mappába.

    ![1. letöltőközpontból](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Az összes telepítési fájlok letöltési mérete nagyobb, mint 3 GB-os. A 10 MB/s letöltési hivatkozásra, az összes telepítési fájlok letöltése előfordulhat, hogy legfeljebb 60 percet vesz igénybe. Töltse le a fájlokat a megadott letöltési helyét.

## <a name="extract-azure-backup-server-install-files"></a>Az Azure Backup Server telepítése fájlok kibontása

Miután az Azure Stack virtuális géphez letöltött összes fájlt, nyissa meg a letöltési helyre. Az első fázisa az Azure Backup Server telepítése, hogy a fájlok kibontásához.

![1. letöltőközpontból](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. A letöltött fájlok listáját a telepítés indításához kattintson **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Bontsa ki a telepítési fájlok legalább 4GB szabad lemezterület szükséges.
    >

2. Az Azure Backup Server varázslóban kattintson **tovább** folytatásához.

    ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Válassza ki az Azure Backup Server fájlok elérési útját, és kattintson a **tovább**.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ellenőrizze a kibontási helyét, és kattintson a **kinyerése**.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. A varázsló a fájlok kibontása és a telepítési folyamat készíthet.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. A kinyerési folyamat befejezése után kattintson a **Befejezés**. Alapértelmezés szerint **hajtsa végre a setup.exe** van kiválasztva. Amikor rákattint **Befejezés**, Setup.exe Microsoft Azure Backup Server telepítése a megadott helyre.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Telepítse a csomagot

Az előző lépésben kattintott **Befejezés** való kilépéshez a kibontási fázist, az Azure Backup Server telepítővarázslójának elindításához.

![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Az Azure Backup Server a Data Protection Managerrel oszt meg a kódot. Látni fogja az Azure Backup Server telepítő a Data Protection Manager és a DPM mutató hivatkozásokat. Bár az Azure Backup Server és a Data Protection Manager külön termékek, ezeket a termékeket szorosan kapcsolódnak egymáshoz.

1. Indítsa el a telepítővarázslót, kattintson a **Microsoft Azure Backup Server**.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Az a **üdvözlő** kattintson **tovább**.

    ![Az Azure Backup Server – Üdvözöljük és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Az a **előfeltétel-ellenőrzési** kattintson **ellenőrizze** meghatározni, ha a hardver- és az Azure Backup Server teljesült.

    ![Az Azure Backup Server – Üdvözöljük és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Ha a környezetben a szükséges előfeltételeket, látni fogja a egy üzenet jelzi, hogy a gép megfelel-e a követelményeknek. Kattintson a **tovább**.  

    ![Az Azure Backup Server – Előfeltételek ellenőrzésén](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Ha a környezet nem felel meg a szükséges előfeltételeket, a problémák kell megadni. Az előfeltételek nem teljesültek a DpmSetup.log is szerepelnek. Az előfeltétel-hibák megoldásához, és futtassa **újbóli ellenőrzés**. Telepítés nem folytatható, amíg minden előfeltétele teljesül.

    ![Az Azure Backup Server - telepítés előfeltételei nem teljesülnek](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. A Microsoft Azure Backup Server az SQL Server szükséges. Az Azure Backup Server-telepítési csomag áll rendelkezésre az a megfelelő SQL Server bináris fájlokat. Ha szeretné használni a saját SQL-telepítést, is. Azonban ajánlott el lehetővé teszik a telepítő, adjon hozzá egy SQL Server új példányát. A választott a környezete működésének ellenőrzésére kattintson **ellenőrzés és telepítés**.

   > [!NOTE]
   > Az Azure Backup Server nem működik a távoli SQL Server-példányt. Az Azure Backup Server által használt példány helyinek kell lennie.
   >

    ![Az Azure Backup Server – Üdvözöljük és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Miután ellenőrizte, ha a virtuális gép Azure Backup Server telepítéséhez szükséges előfeltételeket, kattintson a **tovább**.

    ![Az Azure Backup Server – Üdvözöljük és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Ha hiba történik, a javaslatra kattintva indítsa újra a gépet, majd indítsa újra a gépet. A számítógép újraindítása után indítsa újra a telepítőt, és amikor juthat el a **SQL-beállítások** kattintson **újbóli ellenőrzés**.

5. Az a **telepítési beállítások**, adja meg a Microsoft Azure Backup server-fájlok a telepítés helyét, és kattintson a **tovább**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Az ideiglenes hely szükséges biztonsági mentése az Azure-bA. Győződjön meg arról, az ideiglenes hely mérete az adatok biztonsági mentése az Azure-bA tervezett legalább 5 %-ával egyenlő. A lemezvédelem különböző lemezen a telepítés befejezése után konfigurálni kell. Tárolókészletek kapcsolatos további információkért lásd: [konfigurálhatók a tárolókészletek és a lemezes tárolás](https://technet.microsoft.com/library/hh758075.aspx).

6. Az a **biztonsági beállítások** képernyőn adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, kattintson a **tovább**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Az a **a Microsoft Update Opt-In** képernyőn, adja meg, hogy a használni kívánt *Microsoft Update* keressen frissítéseket, és kattintson a **tovább**.

   > [!NOTE]
   > Javasoljuk, hogy a Windows Update átirányítása a Microsoft Update szolgáltatásban, amely a biztonsági és fontos frissítéseket kínál a Windows- és egyéb termékek, mint a Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Tekintse át a *beállítások összefoglalása* kattintson **telepítése**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Befejeztével az Azure Backup Server telepítése a telepítő azonnal elindítja a Microsoft Azure Recovery Services ügynököt telepítő.

9. A Microsoft Azure Recovery Services Agent telepítőjének megnyílik, és internetkapcsolat ellenőrzése. Internetkapcsolat érhető el, ha a telepítés folytatásához. Ha nincs kapcsolat, adja meg a proxy adatait az internethez való kapcsolódáshoz. A proxybeállítások megadása után kattintson a **tovább**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. A Microsoft Azure Recovery Services Agent telepítéséhez kattintson **telepítése**.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    A Microsoft Azure Recovery Services ügynök, más néven az Azure Backup-ügynök konfigurálása az Azure Backup Server a Recovery Services-tárolóba. A beállítása után az Azure Backup Server mindig ugyanazt a Recovery Services-tároló-adatok biztonsági mentése.

11. Telepíti a Microsoft Azure Recovery Services agent befejezése után kattintson **tovább** elindításához a következő fázis: az Azure Backup Server regisztrálja a Recovery Services-tárolóval.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    A telepítő elindítja a **kiszolgáló regisztrálása varázsló**.

12. Váltás az Azure-előfizetés és a Recovery Services-tárolóba. Az a **infrastruktúra előkészítése** menüben kattintson a **letöltése** a tároló hitelesítő adatainak letöltése. Ha a **letöltése** a 2. lépésben gomb nincs aktív, jelölje be **már Letöltöttem vagy használom a legújabb Azure Backup Server telepítése** a gomb aktiválásához. A tároló hitelesítő adatainak letöltése a letöltéseket tároló helyet. Vegye figyelembe ezt a helyet, mert, szüksége lesz rá a következő lépéshez.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Az a **tároló azonosítása** menüben kattintson a **Tallózás** található a Recovery Services-tároló hitelesítő adatait.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Az a **válassza ki a tároló hitelesítő adatainak** párbeszédpanelen nyissa meg a letöltési helyre, válassza ki a tároló hitelesítő adatait, majd kattintson a **nyílt**.

    A hitelesítő adatok elérési útja a tárolót azonosító menü jelenik meg. Kattintson a **tovább** eljuthat a titkosítási beállítást.

14. Az a **titkosítási beállítás** párbeszédpanelen adjon meg egy jelszót, és a egy helyen tárolja a jelszót, majd kattintson a biztonsági mentés titkosítása **tovább**.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Adjon meg egy saját jelszót, vagy a jelszó-generátor használatával hozzon létre egyet. A hozzáférési kódot a vásárló dönti el, és a Microsoft nem mentése vagy kezelni ezt a jelszót. Felkészülés egy katasztrófa, mentse a megadott jelszó elérhető helyen.

    Miután rákattint **tovább**, az Azure Backup-kiszolgáló regisztrálva van a Recovery Services-tároló. A telepítő továbbra is fennáll, az SQL Server és az Azure Backup Server telepítése.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. A telepítő befejeződése után állapotát jeleníti meg, hogy az összes szoftver telepítése sikeresen megtörtént.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Amikor a telepítés befejeződött, az Azure Backup Server konzol és az Azure Backup Server PowerShell-lel ikonjai a kiszolgáló asztalán jönnek létre.

## <a name="add-backup-storage"></a>Backup storage hozzáadása

Az első biztonsági másolat az Azure Backup Server géphez csatolt storage másolatok. Lemezek hozzáadásával kapcsolatos további információkért lásd: [adja hozzá a Modern Backup storage](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Adja hozzá a biztonsági mentési tár, még akkor is, ha azt tervezi, hogy adatokat küldjön az Azure-bA kell. Az Azure Backup Server architektúra a Recovery Services-tároló tartalmaz a *második* közben a helyi tárhely az adatok másolatát tárolja az első (és kötelező) a biztonsági másolat.
>
>

## <a name="network-connectivity"></a>Hálózati kapcsolat

Az Azure Backup Server az Azure Backup szolgáltatás a termék sikeres működéséhez kapcsolat szükséges. Ellenőrizze, hogy a gépe képes az Azure-ba, használja a ```Get-DPMCloudConnection``` parancsmagot az Azure Backup Server PowerShell-konzolon. A parancsmag kimenete értéke igaz, akkor a kapcsolat létezik, ellenkező esetben nincs kapcsolat.

Egy időben az Azure-előfizetést kell lennie a kifogástalan állapotban. Ismerje meg, az előfizetés állapotát, és a felügyelet, jelentkezzen be a [előfizetési portálra](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Ha már tudja, hogy az állapot, az Azure-kapcsolat és az Azure-előfizetést, az alábbi táblázat segítségével ismerje meg, hogy a művelet hatása a biztonsági mentési és visszaállítási funkció érhető el.

| Kapcsolati állapota | Azure-előfizetés | Azure biztonsági mentés | Biztonsági mentés lemezre | Állítsa vissza az Azure-ból | Visszaállítás lemezről |
| --- | --- | --- | --- | --- | --- |
| Csatlakozva |Aktív |Engedélyezve |Engedélyezve |Engedélyezve |Engedélyezve |
| Csatlakozva |Elévült |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Csatlakozva |– Figyelmeztetés megszüntetésről |Leállítva |Leállítva |A leállított és az Azure helyreállítási pontok törlése |Leállítva |
| Kapcsolat > 15 napon keresztül |Aktív |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Kapcsolat > 15 napon keresztül |Elévült |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Kapcsolat > 15 napon keresztül |– Figyelmeztetés megszüntetésről |Leállítva |Leállítva |A leállított és az Azure helyreállítási pontok törlése |Leállítva |

### <a name="recovering-from-loss-of-connectivity"></a>A kapcsolat megszakadása helyreállítása

Ha tűzfal vagy proxy miatt nem hozzáférés az Azure-ba, a tűzfal vagy proxy profil engedélyezett a következő tartomány-címekre:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Miután visszaállította a kapcsolatot az Azure-bA az Azure Backup Server, az Azure-előfizetés állapota meghatározza, hogy a végrehajtható műveletek. Ha a kiszolgáló már **csatlakoztatva**, a táblázat [hálózati kapcsolat](backup-mabs-install-azure-stack.md#network-connectivity) a rendelkezésre álló műveletek megjelenítéséhez.

### <a name="handling-subscription-states"></a>– Előfizetési állapotok kezelése

Lehetséges egy Azure-előfizetést a *lejárt* vagy *megszüntetett* állapotra *aktív* állapota. Bár az előfizetés állapota nem *aktív*:

- Amíg az előfizetés *megszüntetett*, az elveszíti funkciót. Az előfizetés visszaállítása *aktív*, a biztonsági mentési és visszaállítási funkció revives. Ha a biztonsági mentési adatokat a helyi lemezen elég nagy megőrzési idővel rendelkező megmaradt, biztonsági mentési adatok lekérhetők. Viszont az Azure-beli biztonsági mentési adatokat, végérvényesen elvesznek az előfizetés kerül, ha a *megszüntetett* állapota.
- Amíg az előfizetés *lejárt*, az elveszíti funkciót. Ütemezett biztonsági mentések nem működnek, amíg az előfizetés *lejárt*.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Microsoft Azure Backup server hibákkal meghiúsul, a telepítési fázis (vagy biztonsági mentése és visszaállítása) során, tekintse meg a [hiba kódok dokumentum](https://support.microsoft.com/kb/3041338).
Emellett olvassa el [Azure Backup kapcsolatos gyakori kérdések](backup-azure-backup-faq.md)

## <a name="next-steps"></a>További lépések

A cikk [a környezet előkészítése a DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), támogatott az Azure Backup Server beállításokkal kapcsolatos információkat tartalmazza.

A következő cikkek segítségével mélyebb betekintést nyerni a munkaterhelések védelme a Microsoft Azure Backup Server használatával.

- [Az SQL Server biztonsági másolat](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [A SharePoint server biztonsági másolat](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
