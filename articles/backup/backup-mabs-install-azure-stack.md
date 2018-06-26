---
title: Az Azure Backup Server telepítése Azure veremben |} Microsoft Docs
description: Azure Backup Server használatával védeni, vagy Azure verem-munkaterhelések biztonsági mentése.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: az Azure biztonsági mentési kiszolgáló; munkaterhelések; védelme -munkaterhelések biztonsági mentése
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/5/2018
ms.author: markgal
ms.openlocfilehash: c9dd6a1818b0afeb5e577724568a8254a70c8228
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753353"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server telepítése az Azure Stacken

Ez a cikk ismerteti az Azure Backup Server telepítése Azure veremben. Az Azure Backup Server látná el az infrastruktúra, a szolgáltató (IaaS) munkaterhelések, például az Azure-készletben futó virtuális gépek. Egy Azure Backup Server használatával a munkaterhelések védelme érdekében ez minden munkaterhelések védelmének egyetlen konzolról kezelheti.

> [!NOTE]
> Biztonsági képességeivel kapcsolatos további tudnivalókért tekintse meg [Azure Backup szolgáltatás biztonsági jellemzőkkel dokumentáció](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Az Azure Backup Server védelmi mátrixa
Az Azure Backup Server a következő Azure verem virtuálisgép-munkaterhelését védi.

| Védett adatforrás | Védelem és helyreállítás |
| --------------------- | ----------------------- |
| Windows Server félig éves csatorna - Datacenter vagy Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 – Datacenter/Entprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2008 R2 – Datacenter vagy Enterprise/Standard | Kötetek, fájlok, mappák |
| SQL Server 2016 | Adatbázis |
| SQL Server 2014 | Adatbázis |
| SQL Server 2012 SP1 | Adatbázis |
| A SharePoint 2016 | Farm, adatbázis, előtér, webalkalmazás-kiszolgáló |
| SharePoint 2013 | Farm, adatbázis, előtér, webalkalmazás-kiszolgáló |
| A SharePoint 2010 | Farm, adatbázis, előtér, webalkalmazás-kiszolgáló |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Az Azure Backup Server környezetet előfeltételei

Vegye figyelembe az ebben a szakaszban szereplő ajánlásokhoz, amikor az Azure Backup Server telepítése a verem Azure környezetben. Az Azure biztonsági mentés telepítője ellenőrzi, hogy a környezetében rendelkezik a szükséges előfeltételeket, de időt takaríthat lesz előkészítése telepítése előtt.

### <a name="determining-size-of-virtual-machine"></a>Virtuális gép mérete meghatározása
Egy Azure verem virtuális gépen futó Azure Backup Server, használja a A2 méret vagy nagyobb. Ha segítségre van szüksége a virtuálisgép-méret kiválasztása, töltse le a [Azure verem virtuális gép mérete Számológép](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuális hálózatok verem Azure virtuális gépeken
Egy Azure verem munkaterhelés használt összes virtuális gépet a azonos Azure virtuális hálózat és az Azure-előfizetéshez kell tartoznia.

### <a name="azure-backup-server-vm-performance"></a>Az Azure Backup Server VM teljesítmény
Ha más virtuális gépekkel megosztva használ, a tárolási fiók méretét és IOPS-korlátok gyakorolt hatás Azure Backup Server virtuális gép teljesítményét. Ezért egy külön tárfiókot kell használnia, az Azure Backup Server virtuális gép. Az Azure biztonsági mentési kiszolgálón futó Azure Backup ügynöknek szüksége van, a ideiglenes tárhelyre:
- saját használatra (gyorsítótárhely),
- (helyi átmeneti terület) a felhőből helyreállított adatok

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure biztonsági mentési mennyiségű ideiglenes lemezes tárolás konfigurálása
Minden Azure verem virtuális gép rendelkezik mennyiségű ideiglenes lemezes tárhellyel, a felhasználó kötetként rendelkezésre álló `D:\`. Az Azure Backup által igényelt helyi átmeneti terület beállítható úgy, hogy találhatók `D:\`, és a gyorsítótár helye lehet tenni `C:\`. Ezzel a módszerrel nem kell az Azure Backup Server virtuális géphez csatolt adatlemezek el kell faragottnak.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Biztonsági mentési adatok tárolására, a helyi lemezen, és az Azure-ban
Az Azure Backup Server biztonsági mentési adatokat, a műveleti helyreállítási a virtuális géphez csatolt Azure lemezeken tárolja. Miután a virtuális gép csatolt a lemezek és a tárolóhely, Azure Backup Server kezeli tároló. A biztonsági mentési adatok tárolási mértékét száma és mérete a lemez nem csatolható az egyes [verem Azure virtuális gép](../azure-stack/user/azure-stack-storage-overview.md). Minden Azure verem virtuális gép méretétől rendelkezik a virtuális géphez csatolt lemezek maximális számát. A2 például négy lemezek. A3 méretű nyolc lemezek. A4 16 lemez. Ebben az esetben a lemezek száma és mérete határozza meg a teljes biztonsági mentési tárolókészlet méretét.

> [!IMPORTANT]
> Meg kell **nem** az Azure Backup Server-csatolású lemezeket (biztonsági mentés) műveleti helyreállítási adatok megőrzéséhez a több mint öt napig.
>

Biztonsági mentési adatok tárolására az Azure-ban csökkenti a biztonsági mentési infrastruktúra Azure veremben. Ha öt napnál régebbi adatok, akkor az Azure-ban kell tárolni.

Az Azure biztonsági mentési adatok tárolására, hozzon létre, vagy használja a Recovery Services-tároló. Amikor készül, hogy készítsen biztonsági másolatot az Azure Backup Server munkaterhelés –, akkor [a Recovery Services-tároló konfigurálása](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Konfigurálása után minden alkalommal, amikor egy biztonságimásolat-készítő feladat fut, a helyreállítási pont létrehozása a tárolóban lévő állapottal. Minden Recovery Services-tároló legfeljebb 9999 helyreállítási pontokat tárolja. Attól függően, hogy mennyi ideig maradnak, és létrehozott helyreállítási pontok száma a biztonsági mentési adatok továbbra is sok éve. Például akkor képes havi helyreállítási pontot, és öt évig megőrzi azokat.
 
### <a name="scaling-deployment"></a>Központi telepítés skálázás
Ha azt szeretné, a telepítés méretezésére, lehetősége van a következő:
  - Vertikális felskálázás – Azure Backup Server virtuális gépen a több D sorozat méretének növelése és a helyi tároló növelése [verem Azure virtuális gép utasítások](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Adatok továbbítása - régebbi adatok küldése az Azure-ba, és az Azure biztonsági mentés-kiszolgálóhoz csatolt tárhelyen tárolnia csak a legújabb adatok megőrzése mellett.
  - Horizontális felskálázás -, adjon hozzá további Azure biztonsági mentés kiszolgálókat a munkaterhelések védelme érdekében.

### <a name="net-framework"></a>.NET-keretrendszer

.NET-keretrendszer 3.5 SP1 vagy újabb rendszerre kell telepíteni a virtuális gépen.

### <a name="joining-a-domain"></a>Csatlakozás tartományhoz

Az Azure Backup Server virtuális gép egy tartományhoz kell csatlakoztatni. Rendszergazdai jogosultságokkal rendelkező tartományi felhasználó Azure Backup Server telepítse a virtuális gépen.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Az infrastruktúra-szolgáltatási virtuális gép Azure verem használata

A kiszolgáló Azure Backup Server kiválasztásakor indítsa el a Windows Server 2012 R2 Datacenter vagy a Windows Server 2016 Datacenter gyűjtemény lemezképpel. A cikk [az első Windows rendszerű virtuális gép létrehozása az Azure portálon](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), nyújt segítséget az ajánlott virtuális gép első lépések. A virtuális gép (VM) ajánlott minimális követelményei kell lennie: A2 szabványosnak két maggal és 3.5-GB RAM-MAL.

Sok apró munkaterhelések védelme a Azure Backup Server rendelkezik. A cikk [DPM telepítése Azure virtuális gépként](https://technet.microsoft.com/library/jj852163.aspx), segítséget nyújt a apró ismertetik. A gép történő telepítése előtt olvassa el ebben a cikkben teljesen.

> [!NOTE]
> Az Azure Backup Server futtatásra tervezték, dedikált, meghatározott célú virtuális gépeken. Az Azure Backup Server nem telepíthető:
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógépre, amely fürtcsomópontként működik

Azure Backup Server mindig csatlakoztatása a tartományhoz. Ha szeretné az Azure Backup Server egy másik tartományba helyezi át, először Azure Backup Server telepítése, majd csatlakoztassa azt az új tartományhoz. Amennyiben az Azure Backup Server telepít, akkor nem helyezhető át egy új tartományt.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A Recovery Services tároló tárolási replikációs beállítás lehetővé teszi a georedundáns tárolás és a helyileg redundáns tárolás közül választhat. Alapértelmezés szerint a Recovery Services-tárolók georedundáns tárolás használata. Ha ebben a tárolóban a elsődleges tároló, hagyja a tárolási beállítást, a georedundáns tárolást. Válassza ki a helyileg redundáns tárolás, ha azt szeretné, hogy egy kisebb tartós olcsóbb beállítást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tároló, a tároló irányítópult és a beállítások menü megnyitásához. Ha a **beállítások** menü nem nyitható meg **összes beállítás** a tároló irányítópultjának.
2. A a **beállítások** menüben kattintson a **biztonsági infrastruktúra** > **biztonsági mentési konfigurációhoz** megnyitásához a **biztonsági mentési konfigurációhoz**menü. Az a **biztonsági mentési konfigurációhoz** menüben válassza ki a tárolási replikációs beállítás a tároló számára.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Töltse le az Azure Backup Server telepítő

Töltse le az Azure Backup Server telepítő kétféleképpen történhet. Az Azure Backup Server telepítő a programot letöltheti a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Azure Backup Server telepítő szerint Recovery Services-tároló konfigurálásakor is letölthetők. A következő lépések végigvezetik a telepítő letölti az Azure portálról Recovery Services-tároló konfigurálása közben.

1. A verem Azure virtuális gép [jelentkezzen be az Azure-előfizetéshez az Azure portálon](https://portal.azure.com/).
2. A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![A főmenü minden szolgáltatás választania](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Az a **minden szolgáltatás** párbeszédpanel, írja be *Recovery Services*. Írja be megkezdése előtt, a megadott erőforrások listájának szűrése. Miután látja, válassza ki a **Recovery Services-tárolók**.

    ![Az összes szolgáltatások párbeszédpanelen írja be a helyreállítási szolgáltatások](./media/backup-mabs-install-azure-stack/all-services.png)

    Az előfizetés Recovery Services-tárolók listája jelenik meg.

4. A Recovery Services-tárolók listában jelölje ki a tároló az irányítópult megnyitásához.

    ![Az összes szolgáltatások párbeszédpanelen írja be a helyreállítási szolgáltatások](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. A tároló bevezetés menüben kattintson a **biztonsági mentés** az első lépések varázsló megnyitásához.

    ![Biztonsági mentés első lépések](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    A biztonsági mentési menü megnyitása.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. A biztonsági mentési menüben az a **a számítási feladatok futtató** menüjében válassza **helyszíni**. Az a **miről szeretne biztonsági másolatot készíteni?** legördülő menüben válassza a kívánt munkaterhelések védelme az Azure Backup Server használatával. Ha nem biztos benne, mely munkaterhelések kiválasztásához, válassza a **Hyper-V virtuális gépek** majd **infrastruktúra előkészítése**.

    ![a helyszíni és a munkaterhelések célok](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    A **infrastruktúra előkészítése** menü megnyitása.

7. Az a **infrastruktúra előkészítése** menüben kattintson a **letöltése** Azure Backup Server telepítési fájlok letöltési weblapon megnyitásához.

    ![Első lépések varázsló módosítása](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    A Microsoft Azure Backup Server, a letölthető fájlokat üzemeltető nyílik meg.

8. A Microsoft Azure Backup Server letöltési oldalon válasszon egy nyelvet, és kattintson a **letöltése**.

    ![Töltse le a központ megnyitása](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Az Azure Backup Server telepítő áll nyolc fájlok - telepítő és a hét .bin fájlt. Ellenőrizze **Fájlnév** jelölje ki az összes szükséges fájlokat, és kattintson a **következő**. Töltse le az összes fájl ugyanabba a mappába.

    ![A letöltőközpontból 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Az összes telepítési fájlok letöltési mérete nagyobb, mint 3 GB-os. 10 MB/s letöltésének a hivatkozásra, az összes telepítési fájlok letöltése legfeljebb 60 percig is eltarthat. Töltse le a fájlokat a megadott letöltési helyre.

## <a name="extract-azure-backup-server-install-files"></a>Bontsa ki a fájlok az Azure Backup Server telepítése

Minden fájl letöltése a verem Azure virtuális géphez, után nyissa meg a letöltési helyre. Az Azure Backup Server telepítése első fázisa a fájlok kibontásához.

![A letöltőközpontból 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. A letöltött fájlok listáját a telepítés elindításához kattintson **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Bontsa ki a telepítési fájlok legalább 4GB szabad lemezterület szükséges.
    >

2. Az Azure Backup Server varázslóban kattintson **tovább** a folytatáshoz.

    ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Válassza ki az Azure Backup Server fájlok elérési útját, és kattintson **következő**.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ellenőrizze a kibontási helyét, és kattintson a **kibontása**.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. A varázsló a fájlok kibontása, és a telepítési folyamat készíthet.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Ha a kinyerési folyamat befejeződik, kattintson **Befejezés**. Alapértelmezés szerint **hajtható végre a setup.exe** van kiválasztva. Amikor rákattint **Befejezés**, Setup.exe Microsoft Azure Backup Server telepítése a megadott helyre.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>A szoftver telepítéséhez

Az előző lépésben kattintott **Befejezés** kattintva lépjen ki a kibontási fázisban, és az Azure Backup Server telepítővarázslójának elindításához.

![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Az Azure Backup Server Data Protection Manager osztja meg a kódot. Látni fogja az Azure Backup Server telepítő a Data Protection Manager és a DPM mutató hivatkozásokat. Bár az Azure Backup Server és a Data Protection Manager külön termékek, ezeket a termékeket szorosan kapcsolódnak egymáshoz.

1. Indítsa el a telepítővarázslót, kattintson a **Microsoft Azure Backup Server**.

   ![A Microsoft Azure biztonsági mentési beállítása varázsló](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Az a **üdvözlő** kattintson **következő**.

    ![Az Azure Backup Server - üdvözlő és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Az a **előfeltételek ellenőrzésének** kattintson **ellenőrizze** meghatározni, ha az Azure Backup Server hardver- és előfeltételek teljesülnek.

    ![Az Azure Backup Server - üdvözlő és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Ha a környezetben a szükséges előfeltételeket, látni fogja egy üzenet, amely azt jelzi, hogy a gép megfelel-e a követelményeknek. Kattintson a **Tovább** gombra.  

    ![Az Azure Backup Server - Előfeltételek ellenőrzésén](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Ha a környezet nem felel meg a szükséges előfeltételeket, a problémák kell megadni. Az előfeltételek nem teljesültek a DpmSetup.log is szerepelnek. Javítsa ki az előfeltételeknél észlelt hibákat, és futtassa **ellenőrizze újra**. Telepítés nem folytatható, amíg minden előfeltétele teljesül.

    ![Az Azure Backup Server - telepítési előfeltételek nem teljesültek](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server SQL Server szükséges. Az Azure Backup Server telepítési csomag részeként elérhető csomagolt a megfelelő SQL Server bináris fájljait. Ha azt szeretné, a saját SQL-telepítéshez, is. Azonban célszerű is lehetővé teszik a telepítőprogram adja hozzá az SQL Server új példányát. Győződjön meg arról, a kiválasztott együttműködve biztosítja a környezetben, kattintson a **ellenőrzés és telepítés**.

   > [!NOTE]
   > Az Azure Backup Server egy távoli SQL Server-példány nem fog működni. Az Azure Backup Server által használt példány kell elhelyezkednie.
   >

    ![Az Azure Backup Server - üdvözlő és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Miután ellenőrizte, ha a virtuális gépet az Azure Backup Server telepítéséhez szükséges előfeltételeknek, kattintson a **következő**.

    ![Az Azure Backup Server - üdvözlő és az Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Ha hiba történik az ajánlás indítsa újra a gépet, majd indítsa újra a gépet. A számítógép újraindítása után indítsa újra a telepítő, és mikor jelenik meg a **SQL-beállítások** kattintson **ellenőrizze újra**.

5. Az a **telepítési beállítások**, adjon meg egy helyet, a Microsoft Azure Backup server fájlok telepítéséhez, és kattintson a **következő**.

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Az ideiglenes helyet az Azure biztonsági mentése szükséges. Ellenőrizze, hogy az ideiglenes hely méretét az adatok biztonsági mentése az Azure-bA tervezett legalább 5 %-ának megfelelő-e. A lemezvédelem különálló lemezek be kell állítani a telepítés befejeződése után. Tárolókészletek kapcsolatos további információkért lásd: [tárolókészletek konfigurálása és a lemezes tárolás](https://technet.microsoft.com/library/hh758075.aspx).

6. Az a **biztonsági beállítások** képernyőn, adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **következő**.

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Az a **Microsoft Update Opt-In** képernyőn, adja meg, hogy a használni kívánt *Microsoft Update* frissítések keresését, és kattintson a **következő**.

   > [!NOTE]
   > Javasoljuk, a Windows Update, Microsoft Update szolgáltatásban, ami biztonsági és fontos frissítéseket kínál a Windowshoz és más termékek, például a Microsoft Azure Backup Server átirányítása.
   >

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Tekintse át a *összegzése a beállítások* kattintson **telepítése**.

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Végeztével Azure Backup Server telepítése, a telepítő azonnal elindítja a Microsoft Azure Recovery Services agent telepítőjét.

9. A Microsoft Azure Recovery Services Agent Telepítő megnyílik, és ellenőrzi az internetkapcsolat működését. Ha internetkapcsolat érhető el, a telepítés folytatásához. Ha nincs kapcsolat, adja meg a proxy adatait, az internethez való kapcsolódáshoz. A proxybeállítások megadása után kattintson **következő**.

    ![A Microsoft Azure biztonsági mentési PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. A Microsoft Azure Recovery Services Agent telepítéséhez kattintson **telepítése**.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    A Microsoft Azure Recovery Services Agent ügynököt, az Azure Backup szolgáltatás ügynöke más néven a Azure biztonsági mentés kiszolgáló konfigurálása a Recovery Services-tároló. Konfigurálása után Azure Backup Server mindig ugyanazt a Recovery Services-tároló adatok biztonsági mentését.

11. Amikor a Microsoft Azure Recovery Services Agent ügynököt a telepítése befejeződött, kattintson a **következő** elindítani a következő fázis: Azure Backup-kiszolgáló regisztrálása a Recovery Services-tároló.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    A telepítő elindítja a **kiszolgáló regisztrálása varázsló**.

12. Váltson Azure-előfizetése és a Recovery Services-tároló. Az a **infrastruktúra előkészítése** menüben kattintson a **letöltése** töltheti le a tárolói hitelesítő adatokat. Ha a **letöltése** gomb a 2. lépésben nem aktív, jelölje be **már le, vagy használja a legújabb Azure Backup Server telepítése** a gomb aktiválásához. Töltse le a tárolói hitelesítő adatokat a letöltéseket tároló helyét. Vegye figyelembe az elérési utat a következő lépéshez szüksége.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. A a **tároló azonosító** menüben kattintson a **Tallózás** a Recovery Services tárolói hitelesítő adatok kereséséhez.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    A a **válassza ki a tárolói hitelesítő adatokat** párbeszédpanel, nyissa meg a letöltési helyre, válassza ki a tárolói hitelesítő adatokat, majd kattintson a **nyitott**.

    A hitelesítő adatok elérési útja a tárolót azonosító menüben jelenik meg. Kattintson a **következő** ahhoz, hogy a titkosítási beállítás.

14. Az a **titkosítási beállítás** párbeszédpanelen adjon meg egy jelszót a biztonsági másolat titkosításához, és tárolja a jelszót, majd kattintson a hely **következő**.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Adja meg a saját jelszavát, vagy a jelszó-generátor használatával hozzon létre egyet. A jelszó valóban az Öné, és Microsoft mentéséhez, vagy nem kezelheti ezt a jelszót. Egy olyan vészhelyzet esetén előkészítése, mentse a megadott jelszó elérhető helyen.

    Miután rákattintott **következő**, az Azure Backup Server a Recovery Services tárolóban van regisztrálva. A telepítő továbbra is fennáll, az SQL Server és az Azure Backup Server telepítése.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. A telepítő befejeződése után állapotát jeleníti meg, hogy az összes szoftver telepítése sikeresen megtörtént.

    ![Az Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Telepítésének befejezése után az Azure biztonsági mentés konzolját és az Azure Backup Server PowerShell ikonok jönnek létre a kiszolgáló asztalának.

## <a name="add-backup-storage"></a>Biztonsági mentési tároló hozzáadása

Az első biztonsági másolat tárhely az Azure Backup Server géphez csatolt tárhelyen tárolnia. Lemezek hozzáadásával kapcsolatos további információkért lásd: [hozzáadása Modern Backup-tárhelyre](https://docs.microsoft.com/en-us/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Kell hozzáadnia a biztonságimásolat-tároláshoz, akkor is, ha azt tervezi, hogy az adatok küldése az Azure-bA. Az Azure Backup Server architektúra a Recovery Services tároló tartalmazza a *második* a helyi tároló közben az adatok másolatát tartalmazza az első (és kötelező) biztonsági másolatot.
>
>

## <a name="network-connectivity"></a>Hálózati kapcsolat

Az Azure Backup Server esetén az Azure Backup szolgáltatás a termék sikeres működéséhez kapcsolat szükséges. Hogy a számítógép rendelkezik-e az Azure-bA kapcsolattal érvényesítéséhez használja a ```Get-DPMCloudConnection``` parancsmag az Azure Backup Server PowerShell-konzolban. Ha a parancsmag eredménye IGAZ, akkor van kapcsolat, ellenkező esetben nincs kapcsolat.

Egy időben az Azure-előfizetést kell lennie állapota kifogástalan. Az előfizetés állapotának megállapítása és a kezeléséhez jelentkezzen be a [előfizetés portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

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

Egy tűzfal vagy egy proxy megakadályozza hozzáférés az Azure-ba, ha a tűzfal /-proxy profil címek engedélyezett a következő tartományhoz:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Kapcsolat az Azure-bA az Azure Backup Server visszaállította, miután az Azure-előfizetés állapota határozza meg, a végrehajtható műveletek. Ha a kiszolgáló már **csatlakoztatva**, az a táblázat [hálózati kapcsolatra](backup-mabs-install-azure-stack.md#network-connectivity) a rendelkezésre álló műveletek megjelenítéséhez.

### <a name="handling-subscription-states"></a>– Előfizetési állapotok kezelése

Az Azure-előfizetés módosítása lehetséges *lejárt* vagy *Deprovisioned* állapotra *Active* állapota. Az előfizetés állapota nem *aktív*:

- Előfizetés pedig *Deprovisioned*, emiatt megszűnik a funkciót. Az előfizetés visszaállítása *aktív*, a biztonsági mentési/visszaállítási funkciót revives. Ha a biztonsági mentési adatok a helyi lemezen maradt elég nagy megőrzési időtartam, amely a biztonsági mentési adatokat lehet beolvasni. Az Azure biztonsági mentési adatok azonban végérvényesen elvesznek az előfizetés kerül, ha a *Deprovisioned* állapotát.
- Előfizetés pedig *lejárt*, emiatt megszűnik a funkciót. Ütemezett biztonsági mentések nem futhat, amíg egy előfizetés *lejárt*.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Microsoft Azure Backup server hibákkal megszakad, miközben a telepítési fázis (vagy biztonsági mentési vagy visszaállítási), tekintse meg a [hiba kódok dokumentum](https://support.microsoft.com/kb/3041338).
Azt is jelentheti [Azure biztonsági mentés kapcsolatos gyakori kérdések](backup-azure-backup-faq.md)

## <a name="next-steps"></a>További lépések

A cikk [a környezet előkészítése a DPM](https://docs.microsoft.com/en-us/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), támogatott Azure Backup Server beállításokkal kapcsolatos információkat tartalmazza.

A következő cikkek segítségével egy mélyrehatóbb ismereteket szerezhet a munkaterhelések védelme a Microsoft Azure Backup Server.

- [SQL Server biztonsági másolat](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sql-azure-stack)
- [A SharePoint server biztonsági másolat](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternatív kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
