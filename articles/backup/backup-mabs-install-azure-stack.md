---
title: Azure Backup Server telepítése az Azure Stacken
description: Ebben a cikkben megtudhatja, hogyan használhatja az Azure Backup Server használatával az Azure Stackben lévő számítási feladatok védelmére vagy biztonsági mentésére.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b78e5a662bdcf23ad38cb33292658d4d2455e579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583435"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server telepítése az Azure Stacken

Ez a cikk bemutatja, hogyan telepítheti az Azure Backup Server az Azure Stack. Az Azure Backup Server használatával megvédheti az Infrastructure as a Service (IaaS) számítási feladatait, például az Azure Stackben futó virtuális gépeket. Az Azure Backup Server használatával védheti meg a számítási feladatokat, így egyetlen konzolról kezelheti az összes számítási feladat védelmét.

> [!NOTE]
> A biztonsági funkciókról az [Azure Backup biztonsági biztonsági szolgáltatásainak dokumentációjában](backup-azure-security-feature.md)olvashat.
>

## <a name="azure-backup-server-protection-matrix"></a>Az Azure Backup Server védelmi mátrixa

Az Azure Backup Server a következő Azure Stack virtuálisgép-számítási feladatokat védi.

| Védett adatforrás | Védelem és helyreállítás |
| --------------------- | ----------------------- |
| Windows Server féléves csatorna – Adatközpont/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2016 – Adatközpont/Nagyvállalati szabvány | Kötetek, fájlok, mappák |
| Windows Server 2012 R2 – Adatközpont/Nagyvállalati szabvány | Kötetek, fájlok, mappák |
| Windows Server 2012 – Adatközpont/Nagyvállalati szabvány | Kötetek, fájlok, mappák |
| Windows Server 2008 R2 – Adatközpont/Nagyvállalati szabvány | Kötetek, fájlok, mappák |
| SQL Server 2016 | Adatbázis |
| SQL Server 2014 | Adatbázis |
| SQL Server 2012 SP1 | Adatbázis |
| SharePoint 2016 | Farm, adatbázis, előtér, webkiszolgáló |
| SharePoint 2013 | Farm, adatbázis, előtér, webkiszolgáló |
| SharePoint 2010 | Farm, adatbázis, előtér, webkiszolgáló |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Az Azure Backup Server környezet előfeltételei

Vegye figyelembe az ebben a szakaszban található javaslatokat, amikor az Azure Backup Server t az Azure Stack környezetben telepíti. Az Azure Backup Server telepítője ellenőrzi, hogy a környezet rendelkezik-e a szükséges előfeltételekkel, de a telepítés előtt történő előkészítéssel időt takaríthat meg.

### <a name="determining-size-of-virtual-machine"></a>A virtuális gép méretének meghatározása

Az Azure Backup Server futtatása egy Azure Stack virtuális gépen, használja az A2-es vagy nagyobb méretet. Ha segítségre van szüksége a virtuális gép méretének kiválasztásához, töltse le az [Azure Stack virtuálisgép-méretkalkulátorát.](https://www.microsoft.com/download/details.aspx?id=56832)

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuális hálózatok az Azure Stack virtuális gépein

Az Azure Stack-számítási feladatokban használt összes virtuális gépnek ugyanahhoz az Azure virtuális hálózathoz és Azure-előfizetéshez kell tartoznia.

### <a name="azure-backup-server-vm-performance"></a>Az Azure Backup Server virtuális gépének teljesítménye

Ha más virtuális gépekkel van megosztva, a tárfiók mérete és az IOPS korlátozza az Azure Backup Server virtuális gép teljesítményét. Ebből az okból kell használnia egy külön tárfiókot az Azure Backup Server virtuális gép. Az Azure Backup Server en futó Azure Backup ügynöknek ideiglenes tárolásra van szüksége:

- saját használatra (gyorsítótár-hely),
- a felhőből visszaállított adatok (helyi átmeneti terület)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Az Azure Backup ideiglenes lemeztárolójának konfigurálása

Minden Azure Stack virtuális gép ideiglenes lemezes tárhellyel `D:\`rendelkezik, amely a felhasználó számára kötetként érhető el. Az Azure Backup által szükséges helyi átmeneti terület `D:\`beállítható úgy, hogy a `C:\`alkalmazásban tartózkodjon, és a gyorsítótár helye elhelyezhető legyen a rendszeren. Ily módon nem kell a tároló tanait az Azure Backup Server virtuális géphez csatlakoztatott adatlemezektől kell kifaragni.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Biztonsági mentési adatok tárolása helyi lemezen és az Azure-ban

Az Azure Backup Server biztonsági mentési adatokat tárol a virtuális géphez csatlakoztatott Azure-lemezeken a működéshez. Miután a lemezek és a tárhely a virtuális géphez van csatlakoztatva, az Azure Backup Server kezeli a tárhelyet. A biztonsági mentési adatok tárolásának mennyisége az egyes [Azure Stack virtuális gépekhez](/azure-stack/user/azure-stack-storage-overview)csatlakoztatott lemezek számától és méretétől függ. Az Azure Stack virtuális gép minden egyes mérete a virtuális géphez csatlakoztatható lemezek maximális számával rendelkezik. Az A2 például négy lemez. A3 nyolc lemez. Az A4 16 lemez. A lemezek mérete és száma is meghatározza a teljes biztonsági mentési tárolókészletet.

> [!IMPORTANT]
> Az Azure Backup Server hez csatlakoztatott lemezeken öt napnál tovább **nem** őrizheti meg a működési helyreállítási (biztonsági mentési) adatokat.
>

A biztonsági mentési adatok Azure-ban való tárolása csökkenti a biztonsági mentési infrastruktúrát az Azure Stackben. Ha az adatok több mint öt napos, az Azure-ban kell tárolni.

Biztonsági mentési adatok tárolásához az Azure-ban hozzon létre, vagy használja a Recovery Services-tároló. Az Azure Backup Server számítási feladatának biztonsági mentésekor [konfigurálja a Helyreállítási szolgáltatások tárolóját.](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault) Konfigurálás után, minden alkalommal, amikor egy biztonsági mentési feladat fut, egy helyreállítási pont jön létre a tárolóban. Minden helyreállítási szolgáltatások tároló jaleg -hoz 9999 helyreállítási pontok. A létrehozott helyreállítási pontok számától és a megőrzésük től függően sok évig megőrizheti a biztonsági mentési adatokat. Létrehozhat például havi helyreállítási pontokat, és öt évig megtarthatja azokat.

### <a name="scaling-deployment"></a>Központi telepítés méretezése

Ha szeretné méretezni a központi telepítést, a következő lehetőségek közül választhat:

- Felskálázás – Az Azure Backup Server virtuális gép méretének növelése A sorozatról D sorozatra, és az [Azure Stack virtuális gép utasításai szerint a](/azure-stack/user/azure-stack-manage-vm-disks)helyi tárterület növelése.
- Adatok kiszervezésé – régebbi adatok küldése az Azure-ba, és csak az Azure Backup Serverhez csatlakoztatott tárolón tárolt legújabb adatok megőrzése.
- Horizontális felskálázás – További Azure biztonsági mentési kiszolgálók hozzáadása a számítási feladatok védelméhez.

### <a name="net-framework"></a>.NET-keretrendszer

A .

### <a name="joining-a-domain"></a>Csatlakozás tartományhoz

Az Azure Backup Server virtuális gép kell csatlakoztatni egy tartományhoz. A rendszergazdai jogosultságokkal rendelkező tartományi felhasználónak telepítenie kell az Azure Backup Server alkalmazást a virtuális gépen.

## <a name="using-an-iaas-vm-in-azure-stack"></a>IaaS virtuális gép használata az Azure Stackben

Amikor kiszolgálót választ az Azure Backup Server kiszolgálóhoz, kezdje egy Windows Server 2012 R2 Datacenter vagy Windows Server 2016 Datacenter galéria képpel. A cikk, [az első Windows virtuális gép létrehozása az Azure Portalon,](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)oktatóanyagot biztosít az ajánlott virtuális gép első lépésekhez. A kiszolgáló virtuális gép (VM) ajánlott minimális követelményeinek a következőnek kell lennie: A2 Standard két maggal és 3,5 GB RAM-mal.

A számítási feladatok védelme az Azure Backup Server használatával számos árnyalatot. A cikk, [a DPM telepítése Azure virtuális gépként,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))segít megmagyarázni ezeket az árnyalatokat. A gép üzembe helyezése előtt olvassa el teljesen ezt a cikket.

> [!NOTE]
> Az Azure Backup Server egy dedikált, egycélú virtuális gépen való futtatásra van tervezve. Az Azure Backup Server nem telepíthető:
>
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógépre, amely fürtcsomópontként működik

Mindig csatlakozzon az Azure Backup Server egy tartományhoz. Ha át kell helyeznie az Azure Backup Server-t egy másik tartományba, először telepítse az Azure Backup Servert, majd csatlakozzon az új tartományhoz. Miután telepítette az Azure Backup Servert, nem helyezheti át egy új tartományba.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A Recovery Services tárolótároló replikációs beállítás lehetővé teszi, hogy válasszon a georedundáns tárolás és a helyileg redundáns tárolás között. Alapértelmezés szerint a Recovery Services-tárolók georedundáns tárolást használnak. Ha ez a tároló az elsődleges tároló, hagyja a tárolási beállítást georedundáns tárolóra. Válassza a helyileg redundáns tárolást, ha olcsóbb, kevésbé tartós megoldást szeretne. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a trezort a tároló irányítópultjának és a Beállítások menü megnyitásához. Ha a **Beállítások** menü nem nyílik meg, kattintson a tároló irányítópultján a **Minden beállítás** elemre.
2. A **Beállítások** menüben kattintson a **Biztonsági másolat beállítása** > **parancsra** a Biztonsági másolat **konfigurációja** menü megnyitásához. A **Biztonsági másolat konfigurációja** menüben válassza ki a tároló replikációs beállítását.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Az Azure Backup Server telepítőjének letöltése

Az Azure Backup Server telepítőjének letöltése kétféleképpen tölthető le. Az Azure Backup Server telepítője a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=55269)tölthető le. Az Azure Backup Server telepítőjét is letöltheti a Helyreállítási szolgáltatások tárolójának konfigurálásakor. A következő lépések végigvezetik a telepítő azure-portálról való letöltésén a Recovery Services-tároló konfigurálása közben.

1. Az Azure Stack virtuális gépéről [jelentkezzen be az Azure-előfizetésbe az Azure Portalon.](https://portal.azure.com/)
2. A bal oldali menüben válassza a **Minden szolgáltatás lehetőséget.**

    ![Válassza a Főmenü Minden szolgáltatás parancsát](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. A **Minden szolgáltatás** párbeszédpanelen írja be a *Helyreállítási szolgáltatások című szöveget.* A gépelés megkezdésekor a bevitel szűri az erőforrások listáját. Miután látta, válassza a **Recovery Services-tárolók lehetőséget.**

    ![A Helyreállítási szolgáltatások típusa a Minden szolgáltatás párbeszédpanelen](./media/backup-mabs-install-azure-stack/all-services.png)

    Megjelenik a Helyreállítási szolgáltatások tárolóinak listája az előfizetésben.

4. A Recovery Services-tárolók listájából válassza ki a tárolót az irányítópult megnyitásához.

    ![A Helyreállítási szolgáltatások típusa a Minden szolgáltatás párbeszédpanelen](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. A tároló első lépései menüjében kattintson a **Biztonsági mentés** parancsra az Első lépések varázsló megnyitásához.

    ![Biztonsági mentés első lépések](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Megnyílik a biztonsági mentés menü.

    ![Backup-goals-default-megnyitva](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. A biztonsági másolat **menüHol fut a számítási feladatok menüjében** válassza a **Helyszíni**lehetőséget. A **Mit szeretne biztonsági másolatot készíteni?** legördülő menüből válassza ki az Azure Backup Server használatával védeni kívánt számítási feladatokat. Ha nem biztos abban, hogy mely számítási feladatokközül válassza ki a kiválasztható számítási feladatokat, válassza a **Hyper-V virtuális gépek** lehetőséget, majd kattintson **az Infrastruktúra előkészítése**gombra.

    ![helyszíni és számítási feladatok céljaként](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Megnyílik **az Infrastruktúra előkészítése** menü.

7. Az **Infrastruktúra előkészítése** menüben kattintson a **Letöltés** parancsra az Azure Backup Server telepítési fájljainak letöltéséhez egy weblap megnyitásához.

    ![Az első lépések varázsló módosítása](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Megnyílik a Microsoft weblapja, amely az Azure Backup Server letölthető fájljait üzemelteti.

8. A Microsoft Azure Backup Server letöltési lapján válasszon egy nyelvet, és kattintson a **Letöltés gombra.**

    ![Megnyílik a letöltőközpont](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Az Azure Backup Server telepítő nyolc fájlból áll – egy telepítőből és hét .bin fájlból. A **Fájlnév** jelölőnégyzet bejelöléséhez jelölje ki az összes szükséges fájlt, majd kattintson a **Tovább**gombra. Töltse le az összes fájlt ugyanabba a mappába.

    ![Letöltési központ 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Az összes telepítőfájl letöltési mérete nagyobb, mint 3 GB. Egy 10 Mb/s sebességű letöltési linken az összes telepítési fájl letöltése akár 60 percet is igénybe vehet. A fájlok a megadott letöltési helyre töltődnek le.

## <a name="extract-azure-backup-server-install-files"></a>Az Azure Backup Server telepítési fájljainak kibontása

Miután letöltötte az összes fájlt az Azure Stack virtuális gépére, lépjen a letöltési helyre. Az Azure Backup Server telepítésének első fázisa a fájlok kibontása.

![Letöltési központ 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. A telepítés elindításához kattintson a letöltött fájlok listájából a **MicrosoftAzureBackupserverInstaller.exe fájlra.**

    > [!WARNING]
    > A telepítőfájlok kibontásához legalább 4 GB szabad terület szükséges.
    >

2. Az Azure Backup Server varázslóban kattintson a **Tovább** gombra a folytatáshoz.

    ![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Válassza ki az Azure Backup Server-fájlok elérési útját, és kattintson a **Tovább**gombra.

   ![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ellenőrizze a kivonás helyét, és kattintson **a Kibontás gombra.**

   ![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. A varázsló kibontja a fájlokat, és beolvassa a telepítési folyamatot.

   ![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Az extrakciós folyamat befejezése után kattintson a **Befejezés**gombra. Alapértelmezés szerint **a Setup.exe végrehajtása** van kiválasztva. Ha a **Befejezés**gombra kattint, a Telepítő.exe telepíti a Microsoft Azure Backup Server alkalmazást a megadott helyre.

   ![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>A szoftvercsomag telepítése

Az előző lépésben a **Befejezés** gombra kattintott a kibontási fázisból való kilépéshez, és indítsa el az Azure Backup Server telepítővarázslóját.

![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Az Azure Backup Server megosztja a kódot az Adatvédelmi kezelővel. Az Azure Backup Server telepítőjében az Adatvédelmi kezelőre és a DPM-re mutató hivatkozások jelennek meg. Bár az Azure Backup Server és az Adatvédelmi kezelő külön termékek, ezek a termékek szorosan kapcsolódnak egymáshoz.

1. A telepítővarázsló elindításához kattintson a **Microsoft Azure Backup Server**elemre.

   ![Microsoft Azure biztonsági másolat telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Az **üdvözlőképernyőn** kattintson a **Tovább** gombra.

    ![Azure Backup Server – Üdvözlő és előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Az **Előfeltétel-ellenőrzések** képernyőn kattintson az **Ellenőrzés** gombra, és állapítsa meg, hogy az Azure Backup Server hardver- és szoftverelőfeltételei teljesültek-e.

    ![Azure Backup Server – Üdvözlő és előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Ha a környezet rendelkezik a szükséges előfeltételekkel, megjelenik egy üzenet, amely jelzi, hogy a gép megfelel a követelményeknek. Kattintson a **Tovább** gombra.  

    ![Azure Backup Server – Az előfeltételek ellenőrzése átment](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Ha a környezet nem felel meg a szükséges előfeltételeknek, a problémák meg lesznek adva. A nem teljesült előfeltételek a DpmSetup.log fájlban is megjelennek. Oldja meg az előfeltételi hibákat, majd futtassa **a Check Again című programot.** A telepítés nem folytatható, amíg az összes előfeltétel nem teljesül.

    ![Azure Backup Server – a telepítési előfeltételek nem teljesülnek](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. A Microsoft Azure Backup Server használatához SQL Server szükséges. Az Azure Backup Server telepítőcsomag a megfelelő SQL Server bináris fájlokkal együtt érkezik. Ha saját SQL-telepítést szeretne használni, megteheti. Az ajánlott választás azonban az, hogy a telepítő adjon hozzá egy új példányt az SQL Serverből. Annak érdekében, hogy a választás a környezettel működjön, kattintson **az Ellenőrzés és telepítés gombra.**

   > [!NOTE]
   > Az Azure Backup Server nem működik távoli SQL Server-példányokkal. Az Azure Backup Server által használt példánynak helyinek kell lennie.
   >

    ![Azure Backup Server – Üdvözlő és előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Ellenőrzése után, ha a virtuális gép rendelkezik az Azure Backup Server telepítéséhez szükséges előfeltételekkel, kattintson a **Tovább**gombra.

    ![Azure Backup Server – Üdvözlő és előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Ha hiba történik a számítógép újraindítására vonatkozó javaslattal, indítsa újra a számítógépet. A számítógép újraindítása után indítsa újra a telepítőt, és amikor az **SQL Beállítások** képernyőre ér, kattintson az **Újra ellenőrzés gombra.**

5. A **Telepítési beállítások párbeszédpanelen**adja meg a Microsoft Azure Backup kiszolgálófájlok telepítésének helyét, és kattintson a **Tovább**gombra.

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    A semmiből hely szükséges az Azure-ba való biztonsági másolatot. Győződjön meg arról, hogy a scratch hely mérete megegyezik az Azure-ba biztonsági másolatot létrehozni tervezett adatok legalább 5%-ával. A lemezvédelemhez a telepítés befejezése után külön lemezeket kell konfigurálni. A tárolókészletekkel kapcsolatos további információkért [lásd: Tárolókészletek és lemeztároló konfigurálása.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))

6. A **Biztonsági beállítások** képernyőn adjon meg erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **Tovább**gombra.

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. A **Microsoft Update engedélyezés** képernyőn adja meg, hogy a *Microsoft Update szolgáltatással* szeretné-e megkeresni a frissítéseket, majd kattintson a **Tovább**gombra.

   > [!NOTE]
   > Azt javasoljuk, hogy a Windows Update-et irányítsa át a Microsoft Update szolgáltatásra, amely biztonsági és fontos frissítéseket kínál a Windows és más termékek, például a Microsoft Azure Backup Server számára.
   >

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Tekintse át a *Beállítások összegzését,* és kattintson a **Telepítés gombra.**

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Amikor az Azure Backup Server telepítése befejeződik, a telepítő azonnal elindítja a Microsoft Azure Recovery Services ügynök telepítője.

9. Megnyílik a Microsoft Azure Recovery Services Ügynök telepítője, és ellenőrzi az internetkapcsolatot. Ha van elérhető internetkapcsolat, folytassa a telepítést. Ha nincs kapcsolat, adja meg a proxy adatait az internethez való csatlakozáshoz. Miután megadta a proxybeállításokat, kattintson a **Tovább**gombra.

    ![Microsoft Azure biztonsági másolat készítése PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. A Microsoft Azure helyreállítási szolgáltatások ügynökének telepítéséhez kattintson a **Telepítés gombra.**

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    A Microsoft Azure Recovery Services ügynök, más néven az Azure Backup ügynök, konfigurálja az Azure Backup Server a Recovery Services-tároló. Konfigurálás után az Azure Backup Server mindig biztonsági másolatot készít az adatokról ugyanahhoz a Helyreállítási szolgáltatások tárolóba.

11. Miután a Microsoft Azure Recovery Services ügynök befejezte a telepítést, kattintson a **Tovább** gombra a következő fázis elindításához: az Azure Backup Server regisztrálása a Helyreállítási szolgáltatások tárolójával.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    A telepítő elindítja a **Kiszolgáló regisztrálása varázslót**.

12. Váltson azure-előfizetésére és helyreállítási szolgáltatások tárolójára. Az **Infrastruktúra előkészítése** menüben kattintson a **Letöltés** gombra a tároló hitelesítő adatainak letöltéséhez. Ha a **2.** **Already downloaded or using the latest Azure Backup Server installation** A tároló hitelesítő adatai a letöltések tárolására. Legyen tisztában ezzel a hellyel, mert szüksége lesz rá a következő lépéshez.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. A **Tároló azonosítása** menüben kattintson a **Tallózás gombra** a Helyreállítási szolgáltatások tároló hitelesítő adatainak megkereséséhez.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    A **Tároló hitelesítő adatainak kiválasztása** párbeszédpanelen lépjen a letöltési helyre, jelölje ki a tároló hitelesítő adatait, és kattintson a **Megnyitás gombra.**

    A hitelesítő adatok elérési útja megjelenik a Tároló azonosító menüjében. Kattintson a **Tovább** gombra a Titkosítási beállításhoz való ugráshoz.

14. A **Titkosítási beállítás** párbeszédpanelen adjon meg egy jelszót a biztonsági másolat titkosításához, és adjon meg egy helyet a jelszó tárolásához, majd kattintson a **Tovább**gombra.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Megadhat saját jelszót, vagy a jelszógenerátor segítségével létrehozhat egyet az Ön számára. A jelszó az Öné, és a Microsoft nem menti és nem kezeli ezt a jelszót. A katasztrófára való felkészüléshez mentse a jelszót egy elérhető helyre.

    Ha a **Tovább**gombra kattint, az Azure Backup Server regisztrálva van a Helyreállítási szolgáltatások tárolójában. A telepítő folytatja az SQL Server és az Azure Backup Server telepítését.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Amikor a telepítő befejeződik, az Állapot azt mutatja, hogy az összes szoftver telepítése sikeresen megtörtént.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    A telepítés befejezése után az Azure Backup Server konzol és az Azure Backup Server PowerShell-ikonok jönnek létre a kiszolgáló asztalán.

## <a name="add-backup-storage"></a>Biztonsági másolat tárolásának hozzáadása

Az első biztonsági másolat az Azure Backup Server-géphez csatlakoztatott tárolón marad. A lemezek hozzáadásáról további információt a [Modern biztonsági másolat tárolásának hozzáadása című témakörben talál.](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801)

> [!NOTE]
> Akkor is hozzá kell adnia a biztonsági mentési tárhelyet, ha adatokat szeretne küldeni az Azure-ba. Az Azure Backup Server architektúrában a Recovery Services-tároló tartalmazza az adatok *második* példányát, míg a helyi tároló az első (és kötelező) biztonsági másolat.
>
>

## <a name="network-connectivity"></a>Hálózati kapcsolat

Az Azure Backup Server a termék sikeres működéséhez kapcsolatot igényel az Azure Backup szolgáltatással. Annak ellenőrzéséhez, hogy a gép rendelkezik-e az Azure-kapcsolattal, használja a ```Get-DPMCloudConnection``` parancsmagát az Azure Backup Server PowerShell konzolon. Ha a parancsmag kimenete IGAZ, akkor létezik kapcsolat, különben nincs kapcsolat.

Ugyanakkor az Azure-előfizetés kifogástalan állapotban kell lennie. Az előfizetés állapotának és kezelésének megtudásához jelentkezzen be az [előfizetési portálra.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

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

Ha egy tűzfal vagy egy proxy megakadályozza az Azure-hoz való hozzáférést, adja hozzá a következő tartománycímeket a tűzfal/proxyprofil engedélyezési listájához:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Miután az Azure-hoz való csatlakozás visszaáll az Azure Backup Server, az Azure-előfizetés állapota határozza meg a végrehajtható műveleteket. Miután **csatlakoztatta a kiszolgálót,** a [Hálózati kapcsolat](backup-mabs-install-azure-stack.md#network-connectivity) tábla segítségével megtekintheti a rendelkezésre álló műveleteket.

### <a name="handling-subscription-states"></a>Előfizetési állapotok kezelése

Az Azure-előfizetés *texpired* vagy *deprovisioned* állapotról *aktív* állapotra módosítható. Bár az előfizetési állapot nem *aktív:*

- Míg egy előfizetés *deprovisioned*, elveszíti a funkcionalitást. Az *aktív*előfizetés visszaállítása újraéleszti a biztonsági mentési/visszaállítási funkciót. Ha a helyi lemezen lévő biztonsági mentési adatok kellően nagy megőrzési időszakkal őrződtek meg, akkor a biztonsági mentési adatok lehívhatók. Azonban az Azure-ban a biztonsági mentési adatok helyrehozhatatlanul elvesznek, amint az előfizetés a *deprovisioned* állapotba kerül.
- Amíg egy előfizetés *lejárt,* elveszíti a funkcionalitását. Az ütemezett biztonsági mentések nem futnak, amíg az előfizetés *lejárt.*

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Microsoft Azure Backup kiszolgáló hibaokkal nem működik a telepítési fázis (vagy biztonsági mentés vagy visszaállítás közben), olvassa el a [hibakódokat tartalmazó dokumentumot.](https://support.microsoft.com/kb/3041338)
Az [Azure Backup tal kapcsolatos gyakori kérdéseket](backup-azure-backup-faq.md) is megkaphatja

## <a name="next-steps"></a>További lépések

A cikk, [a környezet előkészítése a DPM,](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)információkat tartalmaz a támogatott Azure Backup Server konfigurációk.

Az alábbi cikkekkel mélyebben megismerheti a számítási feladatok védelmét a Microsoft Azure Backup Server használatával.

- [SQL Server biztonsági másolatkészítése](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [SharePoint-kiszolgáló biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
