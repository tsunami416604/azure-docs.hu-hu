---
title: A Azure Backup Server telepítése Azure Stackon | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhatja a Azure Backup Servert a munkaterhelések védeleméhez vagy biztonsági mentéséhez Azure Stackban.
author: dcurwin
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: dacurwin
ms.openlocfilehash: 7716fba7a16b393143ffb9c97e7203e15bc07d82
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012936"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server telepítése az Azure Stacken

Ez a cikk azt ismerteti, hogyan telepítheti a Azure Backup Servert Azure Stack. A Azure Backup Server segítségével az infrastruktúra-szolgáltatás (IaaS) számítási feladatait (például a Azure Stackban futó virtuális gépeket) is védetté teheti. A munkaterhelések védelmének Azure Backup Server használatának előnye, hogy egyetlen konzolról kezelheti az összes számítási feladatot.

> [!NOTE]
> A biztonsági képességekkel kapcsolatos további tudnivalókért tekintse meg [Azure Backup biztonsági szolgáltatások dokumentációját](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Az Azure Backup Server védelmi mátrixa

A Azure Backup Server a következő Azure Stack virtuális gépek munkaterheléseit védi.

| Védett adatforrás | Védelem és helyreállítás |
| --------------------- | ----------------------- |
| Windows Server Semi éves Channel-Datacenter/Enterprise/standard | Kötetek, fájlok, mappák |
| Windows Server 2016 – Datacenter/Enterprise/standard | Kötetek, fájlok, mappák |
| Windows Server 2012 R2-Datacenter/Enterprise/standard | Kötetek, fájlok, mappák |
| Windows Server 2012 – Datacenter/Enterprise/standard | Kötetek, fájlok, mappák |
| Windows Server 2008 R2-Datacenter/Enterprise/standard | Kötetek, fájlok, mappák |
| SQL Server 2016 | Adatbázis |
| SQL Server 2014 | Adatbázis |
| SQL Server 2012 SP1 | Adatbázis |
| SharePoint 2016 | Farm, adatbázis, előtér, webkiszolgáló |
| SharePoint 2013 | Farm, adatbázis, előtér, webkiszolgáló |
| SharePoint 2010 | Farm, adatbázis, előtér, webkiszolgáló |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>A Azure Backup Server környezet előfeltételei

Vegye figyelembe az ebben a részben ismertetett ajánlásokat, amikor a Azure Stack környezetben telepíti Azure Backup Server. A Azure Backup Server-telepítő ellenőrzi, hogy a környezet rendelkezik-e a szükséges előfeltételekkel, de a telepítés előtt időt takaríthat meg.

### <a name="determining-size-of-virtual-machine"></a>A virtuális gép méretének meghatározása

A Azure Backup Server Azure Stack virtuális gépen való futtatásához használja az A2-es vagy nagyobb méretet. A virtuális gép méretének kiválasztásával kapcsolatos segítségért töltse le a [Azure stack VM méretének számológépét](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuális hálózatok Azure Stack virtuális gépeken

Egy Azure Stack munkaterhelésben használt összes virtuális gépnek ugyanahhoz az Azure-beli virtuális hálózathoz és Azure-előfizetéshez kell tartoznia.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM-teljesítmény

Ha más virtuális gépekkel közösen van megosztva, a Storage-fiók mérete és a IOPS-korlát befolyásolja Azure Backup Server virtuális gép teljesítményét. Emiatt külön Storage-fiókot kell használnia a Azure Backup Server virtuális géphez. A Azure Backup Server futó Azure Backup ügynöknek ideiglenes tárterületre van szüksége a következőhöz:

- saját használatra (gyorsítótár helye),
- a felhőből visszaállított adatok (helyi átmeneti terület)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure Backup ideiglenes lemezes tárolásának konfigurálása

Minden Azure Stack virtuális gép ideiglenes lemezes tárterülettel rendelkezik, amely a felhasználó számára mennyiségi `D:\`ként érhető el. A Azure Backup által igényelt helyi átmeneti környezet beállítható úgy, hogy `D:\`, és a gyorsítótár helye `C:\`helyezhető el. Így nem kell a tárterületet a Azure Backup Server virtuális géphez csatolt adatlemezekről kifaragni.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>A biztonsági mentési adattárolók tárolása a helyi lemezen és az Azure-ban

A Azure Backup Server a virtuális géphez csatolt Azure-lemezeken tárolt biztonsági mentési adatkészleteket tárolja az operatív helyreállításhoz. Ha a lemezek és a tárolóhelyek a virtuális géphez vannak csatlakoztatva, Azure Backup Server kezeli a tárolót. A biztonsági mentési adattárolás mennyisége az egyes [Azure stack virtuális gépekhez](/azure-stack/user/azure-stack-storage-overview)csatolt lemezek számától és méretétől függ. Azure Stack virtuális gép minden méretének van egy maximális száma, amely csatlakoztatható a virtuális géphez. Az a2 például négy lemez. Az a3 nyolc lemez. Az A4 16 lemez. A lemezek mérete és száma is meghatározza a biztonsági mentési tár teljes készletét.

> [!IMPORTANT]
> A Azure Backup Server csatolt lemezeken több mint öt napig **ne** őrizze meg az operatív helyreállítási (biztonsági mentési) adatait.
>

A biztonsági mentési adattárolók tárolása az Azure-ban a Azure Stack biztonsági mentési infrastruktúráját is csökkenti. Ha az adattábla öt napnál régebbi, azt az Azure-ban kell tárolni.

A biztonsági mentési adattárolók Azure-ban való tárolásához hozzon létre vagy használjon Recovery Services-tárolót. A Azure Backup Server munkaterhelés biztonsági mentésének előkészítésekor [konfigurálnia kell a Recovery Services](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault)-tárolót. A konfigurálást követően a biztonsági mentési feladatok minden futtatásakor létrejön egy helyreállítási pont a tárolóban. Minden Recovery Services-tár legfeljebb 9999 helyreállítási pontot tart. A létrehozott helyreállítási pontok számától és a megőrzött mennyiségtől függően sok évig megőrizheti a biztonsági másolatok mennyiségét. Létrehozhat például havi helyreállítási pontokat, és öt évig megtarthatja őket.

### <a name="scaling-deployment"></a>Központi telepítés skálázása

Ha szeretné méretezni az üzemelő példányt, a következő lehetőségek közül választhat:

- Vertikális felskálázás – növelje a Azure Backup Server virtuális gép méretét sorozatról D sorozatra, és [a Azure stack virtuális gép utasításai szerint](/azure-stack/user/azure-stack-manage-vm-disks)növelje a helyi tárterületet.
- Adat-kiszervezés – a régebbi típusú adatküldés az Azure-ba, és csak a legújabb adat marad a Azure Backup Serverhoz csatolt tárolóban.
- Vertikális felskálázás – további Azure Backup-kiszolgálókat adhat hozzá a munkaterhelések elleni védelemhez.

### <a name="net-framework"></a>.NET-keretrendszer

A .NET-keretrendszer 3,5 SP1 vagy újabb verziójának telepítve kell lennie a virtuális gépen.

### <a name="joining-a-domain"></a>Csatlakozás tartományhoz

A Azure Backup Server virtuális gépnek csatlakoznia kell egy tartományhoz. A rendszergazdai jogosultsággal rendelkező tartományi felhasználóknak Azure Backup Server kell telepíteniük a virtuális gépre.

## <a name="using-an-iaas-vm-in-azure-stack"></a>IaaS virtuális gép használata Azure Stack

Azure Backup Server kiszolgáló kiválasztásakor Kezdje a Windows Server 2012 R2 Datacenter vagy a Windows Server 2016 Datacenter Gallery rendszerképével. Az [első Windows rendszerű virtuális gép létrehozása a Azure Portalben](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című cikk útmutatást nyújt a javasolt virtuális gép használatába. A kiszolgáló virtuális gépnek ajánlott minimális követelményei a következőek: a2 standard két maggal és 3,5 GB RAM-mal.

A munkaterhelések Azure Backup Serversal való védelme számos árnyalattal rendelkezik. A cikk a [DPM Azure-beli virtuális gépként való telepítésével](https://technet.microsoft.com/library/jj852163.aspx)segít megmagyarázni ezeket az árnyalatokat. A gép üzembe helyezése előtt olvassa el ezt a cikket teljesen.

> [!NOTE]
> Azure Backup Server úgy lett kialakítva, hogy egy dedikált, egyetlen célra készült virtuális gépen fusson. A Azure Backup Server nem telepíthető a következőre:
>
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógépre, amely fürtcsomópontként működik

Mindig csatlakozzon Azure Backup Server tartományhoz. Ha Azure Backup Server egy másik tartományba kell áthelyeznie, először telepítse a Azure Backup Server, majd csatlakoztassa az új tartományhoz. A Azure Backup Server telepítése után nem helyezheti át azt egy új tartományba.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A Recovery Services tároló tárolási replikációs lehetőséggel választhat a Geo-redundáns tárolás és a helyileg redundáns tárolás között. Alapértelmezés szerint a Recovery Services-tárolók a Geo-redundáns tárolást használják. Ha ez a tár az elsődleges tároló, hagyja a Storage beállítást a Geo-redundáns tárolás beállításnál. Ha kevésbé tartós megoldást szeretne használni, válassza a helyileg redundáns tárolás lehetőséget. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tárolót a tároló irányítópultjának és a beállítások menü megnyitásához. Ha a **Beállítások** menü nem nyílik meg, kattintson az **összes beállítás** elemre a tároló irányítópultján.
2. A **Beállítások** menüben kattintson a biztonsági mentési **infrastruktúra** > **biztonsági mentési konfiguráció** elemre a **biztonsági mentési konfiguráció** menü megnyitásához. A **biztonsági mentés konfigurálása** menüben válassza a tároló replikációja lehetőséget.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Azure Backup Server telepítőjének letöltése

Az Azure Backup Server telepítőt kétféleképpen töltheti le. A Azure Backup Server-telepítőt a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=55269)töltheti le. Azure Backup Server telepítőt Recovery Services-tároló konfigurálásakor is letöltheti. A következő lépések végigvezetik a telepítő letöltésének lépésein a Azure Portal egy Recovery Services-tároló konfigurálása során.

1. A Azure Stack virtuális gépről [Jelentkezzen be az Azure-előfizetésbe a Azure Portal](https://portal.azure.com/).
2. A bal oldali menüben válassza a **minden szolgáltatás**lehetőséget.

    ![Válassza a minden szolgáltatás lehetőséget a főmenüben](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. A **minden szolgáltatás** párbeszédpanelen írja be a következőt: *Recovery Services*. Ahogy elkezd gépelni, a bemenet szűri az erőforrások listáját. Ha ezt látja, válassza a **Recovery Services**-tárolók lehetőséget.

    ![Írja be a Recovery Services a minden szolgáltatás párbeszédpanelen](./media/backup-mabs-install-azure-stack/all-services.png)

    Megjelenik az előfizetésben található Recovery Services-tárolók listája.

4. A Recovery Services-tárolók listájából válassza ki a tárolót az irányítópult megnyitásához.

    ![Írja be a Recovery Services a minden szolgáltatás párbeszédpanelen](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. A tároló Első lépések menüjében kattintson a **biztonsági mentés** elemre a első lépések varázsló megnyitásához.

    ![Biztonsági mentés – első lépések](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Megnyílik a biztonsági mentés menü.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. A biztonsági mentés menüben, a **Hol van a** számítási feladat fut menüben válassza **a**helyszíni lehetőséget. A **Miről szeretne biztonsági másolatot készíteni?** legördülő menüből válassza ki a védelemmel ellátni kívánt munkaterheléseket Azure Backup Server használatával. Ha nem tudja biztosan, hogy melyik számítási feladatot szeretné kiválasztani, válassza a **Hyper-V Virtual Machines** , majd az **infrastruktúra előkészítése**lehetőséget.

    ![a helyszíni és a számítási feladatok céljaként](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Megnyílik az **infrastruktúra előkészítése** menü.

7. Az **infrastruktúra előkészítése** menüben kattintson a **Letöltés** lehetőségre a Azure Backup Server telepítési fájlok letöltéséhez szükséges weblapok megnyitásához.

    ![Első lépések varázsló módosítása](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Megnyílik a Azure Backup Server letölthető fájljait futtató Microsoft-weblap.

8. A Microsoft Azure Backup kiszolgáló letöltése lapon válasszon ki egy nyelvet, majd kattintson a **Letöltés**gombra.

    ![Megnyílik a letöltőközpontban](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. A Azure Backup Server-telepítő nyolc fájlból áll – egy telepítőből és hét. bin fájlból. Jelölje ki a **fájlnevet** az összes szükséges fájl kiválasztásához, majd kattintson a **tovább**gombra. Töltse le az összes fájlt ugyanabba a mappába.

    ![1\. letöltőközpont](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Az összes telepítési fájl letöltési mérete meghaladja a 3 GB-ot. 10 Mbps letöltési hivatkozás esetén az összes telepítési fájl letöltése akár 60 percet is igénybe vehet. A fájlok a megadott letöltési helyre tölthetők le.

## <a name="extract-azure-backup-server-install-files"></a>Azure Backup Server telepítési fájlok kinyerése

Miután letöltötte az összes fájlt a Azure Stack virtuális gépre, lépjen a letöltési helyre. A Azure Backup Server telepítésének első fázisa a fájlok kibontása.

![1\. letöltőközpont](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. A telepítés elindításához a letöltött fájlok listájából kattintson a **MicrosoftAzureBackupserverInstaller. exe**fájlra.

    > [!WARNING]
    > A telepítőfájlok kibontásához legalább 4 GB szabad terület szükséges.
    >

2. A folytatáshoz kattintson a **tovább** gombra a Azure Backup Server varázslóban.

    ![Microsoft Azure Backup telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Válassza ki a Azure Backup Server-fájlok elérési útját, és kattintson a **tovább**gombra.

   ![Microsoft Azure Backup telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ellenőrizze a kinyerési helyet, majd kattintson a **Kibontás**elemre.

   ![Microsoft Azure Backup telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. A varázsló kibontja a fájlokat, és beolvassa a telepítési folyamatot.

   ![Microsoft Azure Backup telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. A kinyerési folyamat befejezése után kattintson a **Befejezés**gombra. Alapértelmezés szerint a **Setup. exe futtatása** van kiválasztva. Ha a **Befejezés**gombra kattint, a Setup. exe telepíti Microsoft Azure Backup-kiszolgálót a megadott helyre.

   ![Microsoft Azure Backup telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>A szoftvercsomag telepítése

Az előző lépésben a **Befejezés** gombra kattintva kiléphet a kinyerési fázisból, és elindíthatja a Azure Backup Server telepítővarázsló.

![Microsoft Azure Backup telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server a megosztási kódot a Data Protection Manager. A Azure Backup Server telepítő Data Protection Manager és DPM mutató hivatkozásokat fog látni. Bár Azure Backup Server és Data Protection Manager különálló termékek, ezek a termékek szorosan kapcsolódnak egymáshoz.

1. A telepítővarázsló elindításához kattintson **Microsoft Azure Backup kiszolgáló**elemre.

   ![Microsoft Azure Backup telepítővarázslója](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Az **üdvözlő** képernyőn kattintson a **tovább**gombra.

    ![Azure Backup Server – Üdvözöljük és Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Az **Előfeltételek ellenőrzése** képernyőn kattintson a **Check (ellenőrzés** ) elemre annak megállapításához, hogy teljesülnek-e a Azure Backup Server hardver-és szoftver-előfeltételei.

    ![Azure Backup Server – Üdvözöljük és Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Ha a környezet rendelkezik a szükséges előfeltételekkel, akkor megjelenik egy üzenet, amely jelzi, hogy a gép megfelel a követelményeknek. Kattintson a **Tovább** gombra.  

    ![Azure Backup Server – az Előfeltételek ellenőrzése sikeres volt](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Ha a környezet nem felel meg a szükséges előfeltételeknek, a rendszer megadja a problémákat. A nem teljesített előfeltételek a DpmSetup. log naplófájlban is szerepelnek. Oldja meg az előfeltételként szükséges hibákat, majd futtassa **újból az ellenőrzés műveletet**. A telepítés nem folytatható, amíg az összes előfeltétel teljesül.

    ![Azure Backup Server – a telepítés előfeltételei nem teljesülnek](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup a kiszolgálónak SQL Server szükséges. A Azure Backup Server telepítési csomagja a megfelelő SQL Server bináris fájljaival van ellátva. Ha saját SQL-telepítést szeretne használni, a következőt használhatja:. Az ajánlott lehetőség azonban lehetővé teszi, hogy a telepítő új SQL Server-példányt adjon hozzá. Az **ellenőrzés és telepítés**lehetőségre kattintva győződjön meg arról, hogy a saját környezete működik.

   > [!NOTE]
   > A Azure Backup Server távoli SQL Server példánnyal nem fog működni. A Azure Backup Server által használt példánynak helyinek kell lennie.
   >

    ![Azure Backup Server – Üdvözöljük és Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Ha ellenőrizte, hogy a virtuális gép rendelkezik-e a Azure Backup Server telepítéséhez szükséges előfeltételekkel, kattintson a **tovább**gombra.

    ![Azure Backup Server – Üdvözöljük és Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Ha hiba lép fel a gép újraindítására vonatkozó javaslattal, indítsa újra a gépet. A gép újraindítása után indítsa újra a telepítőt, és amikor az **SQL-beállítások** képernyőre kattint, kattintson **ismét a vizsgálat**gombra.

5. A **telepítési beállítások**területen adjon meg egy helyet a Microsoft Azure Backup Server-fájlok telepítéséhez, majd kattintson a **tovább**gombra.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Az Azure-ba való biztonsági mentéshez a semmiből van szükség. Győződjön meg arról, hogy a megjelenő hely mérete megegyezik az Azure-ba történő biztonsági mentésre tervezett adatmennyiség legalább 5%-ában. A lemezek védelme érdekében a telepítés befejeződése után külön lemezeket kell konfigurálni. További információ a Storage-készletekről: a [tárolási készletek és a lemezes tárolás konfigurálása](https://technet.microsoft.com/library/hh758075.aspx).

6. A **biztonsági beállítások** képernyőn adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **tovább**gombra.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. A **Microsoft Update** a bejelentkezési képernyőn válassza ki, hogy szeretné-e használni a *Microsoft Update* a frissítések kereséséhez, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Javasoljuk, hogy Windows Update átirányítást a Microsoft Updatera, amely biztonsági és fontos frissítéseket kínál a Windows és más termékek, például a Microsoft Azure Backup-kiszolgáló számára.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Tekintse át a *Beállítások összegzését* , és kattintson a **telepítés**gombra.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Amikor Azure Backup Server befejezi a telepítést, a telepítő azonnal elindítja a Microsoft Azure Recovery Services Agent telepítőjét.

9. Megnyílik az Microsoft Azure Recovery Services ügynök telepítője, és ellenőrzi az internetkapcsolatot. Ha az internetkapcsolat elérhető, folytassa a telepítést. Ha nincs kapcsolat, adja meg a proxy adatait az internethez való csatlakozáshoz. A proxybeállítások megadása után kattintson a **tovább**gombra.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. A Microsoft Azure Recovery Services-ügynök telepítéséhez kattintson a **telepítés**gombra.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    A Microsoft Azure Recovery Services ügynök, más néven Azure Backup ügynök, a Recovery Services-tárolóra konfigurálja a Azure Backup Server. A konfigurálást követően a Azure Backup Server mindig ugyanarra a Recovery Services-tárolóra készít biztonsági másolatot.

11. Miután a Microsoft Azure Recovery Services ügynök befejezte a telepítést, kattintson a **tovább** gombra a következő fázis elindításához: a Azure Backup Server regisztrálása a Recovery Services-tárolóval.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    A telepítő elindítja a **kiszolgáló regisztrálása varázslót**.

12. Váltson az Azure-előfizetésre és a Recovery Services-tárolóra. Az **infrastruktúra előkészítése** menüben kattintson a **Letöltés** gombra a tár hitelesítő adatainak letöltéséhez. Ha a 2. lépésben a **Letöltés** gomb nem aktív, válassza a **már letöltött vagy a legújabb Azure Backup Server telepítéssel** lehetőséget a gomb aktiválásához. A tároló hitelesítő adatai letöltve arra a helyre, ahol a letöltéseket tárolja. Vegye figyelembe ezt a helyet, mert a következő lépéshez szüksége lesz rá.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. A tár **azonosítási** menüjében kattintson a **tallózás** gombra a Recovery Services tároló hitelesítő adatainak megkereséséhez.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    A tár **hitelesítő adatainak kiválasztása** párbeszédpanelen lépjen a letöltési helyre, válassza ki a tároló hitelesítő adatait, majd kattintson a **Megnyitás**gombra.

    A hitelesítő adatok elérési útja megjelenik a tároló azonosítási menüjében. Kattintson a **tovább** gombra a titkosítási beállításhoz való továbblépés előtt.

14. A **titkosítási beállítások** párbeszédpanelen adjon meg egy jelszót a biztonsági másolat titkosításához, valamint egy helyet a jelszó tárolásához, és kattintson a **tovább**gombra.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Megadhatja a saját jelszavát, vagy a jelszó-előállító használatával létrehozhat egyet. A jelszó a tiéd, és a Microsoft nem menti és nem kezeli ezt a jelszót. A katasztrófa előkészítéséhez mentse a hozzáférési kódot egy elérhető helyre.

    Ha a **tovább**gombra kattint, a Azure Backup Server regisztrálva van a Recovery Services-tárolóban. A telepítő folytatja a SQL Server telepítését és a Azure Backup Server.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Ha a telepítő befejeződik, az állapot azt jelzi, hogy az összes szoftver telepítése sikeresen megtörtént.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    A telepítés befejeztével a Azure Backup Server-konzol és a Azure Backup Server PowerShell-ikonok a kiszolgáló asztalán jönnek létre.

## <a name="add-backup-storage"></a>Biztonsági mentési tár hozzáadása

Az első biztonsági másolat a Azure Backup Server géphez csatolt tárterületen marad. A lemezek hozzáadásával kapcsolatos további információkért lásd: [modern biztonsági mentési tár hozzáadása](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Akkor is fel kell vennie a biztonsági mentési tárolót, ha az Azure-ba történő adatküldést tervezi. A Azure Backup Server architektúrában a Recovery Services-tároló tárolja az adatgyűjtés *második* másolatát, miközben a helyi tárterület az első (és a kötelező) biztonsági másolatot tárolja.
>
>

## <a name="network-connectivity"></a>Hálózati kapcsolat

Azure Backup Server a termék sikeres működéséhez kapcsolódnia kell a Azure Backup szolgáltatáshoz. Annak ellenőrzéséhez, hogy a számítógép rendelkezik-e az Azure-kapcsolattal, használja a ```Get-DPMCloudConnection``` parancsmagot a Azure Backup Server PowerShell-konzolon. Ha a parancsmag kimenete igaz, akkor a kapcsolat létezik, máskülönben nincs kapcsolat.

Ugyanakkor az Azure-előfizetésnek kifogástalan állapotban kell lennie. Az előfizetés állapotának megállapításához és a kezeléséhez jelentkezzen be az [előfizetési portálra](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Ha már ismeri az Azure-kapcsolat állapotát és az Azure-előfizetést, az alábbi táblázat segítségével megtudhatja, milyen hatással van a biztonsági mentési/helyreállítási funkciókra.

| Kapcsolat állapota | Azure-előfizetés | Biztonsági mentés az Azure-ba | Biztonsági mentés lemezre | Visszaállítás az Azure-ból | Visszaállítás lemezről |
| --- | --- | --- | --- | --- | --- |
| Csatlakozva |Aktív |Engedélyezett |Engedélyezett |Engedélyezett |Engedélyezett |
| Csatlakozva |Lejárt |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Csatlakozva |Felfüggesztést |Leállítva |Leállítva |Leállított és Azure helyreállítási pontok törölve |Leállítva |
| Elveszett kapcsolat > 15 nap |Aktív |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Elveszett kapcsolat > 15 nap |Lejárt |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Elveszett kapcsolat > 15 nap |Felfüggesztést |Leállítva |Leállítva |Leállított és Azure helyreállítási pontok törölve |Leállítva |

### <a name="recovering-from-loss-of-connectivity"></a>Helyreállítás a kapcsolat elvesztése miatt

Ha egy tűzfal vagy egy proxy megakadályozza az Azure-hoz való hozzáférést, adja hozzá a következő tartományi címeket a tűzfal/proxy profil engedélyezési listájában:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Ha az Azure-hoz való kapcsolódást visszaállítja a Azure Backup Server, az Azure-előfizetés állapota határozza meg a végrehajtandó műveleteket. Ha a kiszolgáló **csatlakoztatva**van, a [hálózati kapcsolat](backup-mabs-install-azure-stack.md#network-connectivity) táblázatban található táblázat használatával tekintheti meg az elérhető műveleteket.

### <a name="handling-subscription-states"></a>Előfizetési állapotok kezelésére

Lehetséges, hogy az Azure-előfizetést *lejárt* vagy *kiépített* állapotról *aktív* állapotra szeretné módosítani. Az előfizetés állapota nem *aktív*:

- *Az előfizetés*megszüntetése közben elveszíti a funkciót. Az előfizetés *aktívra*való visszaállítása a biztonsági mentési/visszaállítási funkciót újítja meg. Ha a helyi lemezen lévő biztonsági mentési adatok tárolása elég nagy, a biztonsági mentési adatok lekérése megmaradt. Az Azure-beli biztonsági mentési adatgyűjtés azonban visszavonhatatlanul elvész, ha az előfizetés a *kiépített* állapotba kerül.
- Amíg egy előfizetés *lejárt*, elveszíti a funkciót. Az ütemezett biztonsági mentések nem futnak, amíg egy előfizetés *lejár*.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha Microsoft Azure Backup kiszolgáló hibával leáll a telepítési fázisban (vagy biztonsági mentés vagy visszaállítás), tekintse meg a [hibakódok dokumentumát](https://support.microsoft.com/kb/3041338).
[Azure Backup kapcsolódó gyakori kérdések](backup-azure-backup-faq.md) is megtekinthetők

## <a name="next-steps"></a>Következő lépések

A [DPM környezetének előkészítésével](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)kapcsolatos cikk a támogatott Azure Backup Server konfigurációkról tartalmaz információkat.

A következő cikkek segítségével mélyebben megismerheti a munkaterhelés-védelmet Microsoft Azure Backup kiszolgáló használatával.

- [SQL Server biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [SharePoint Server biztonsági másolat](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
