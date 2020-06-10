---
title: Microsoft Azure Backup-kiszolgáló beállítása Azure VMware-megoldáshoz (AVS)
description: Az Azure VMware Solution (AVS) környezet beállítása a virtuális gépek Microsoft Azure Backup kiszolgáló használatával történő biztonsági mentéséhez.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 23c29f453587ac7a232c21e43fa6fb45193881bc
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613109"
---
# <a name="set-up-microsoft-azure-backup-server-for-avs"></a>Microsoft Azure Backup-kiszolgáló beállítása az AVS-hez

A Microsoft Azure Backup Server egy robusztus nagyvállalati biztonsági mentési és helyreállítási rendszer, amely hozzájárul az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához. Az AVS előzetes verziójában a Azure Backup Server használatával konfigurálhatja a virtuális gépek szintjének biztonsági mentését. 

A Azure Backup Server a következő helyre tudja tárolni a biztonsági mentési fájlokat:

- **Lemez**: a rövid távú tároláshoz Azure Backup Server biztonsági mentést készít az adatlemez-készletekre.

- **Azure**: mind a rövid, mind a hosszú távú tárolás esetén Azure Backup Server a lemezes készletekben tárolt adatairól biztonsági másolatot készíthet a Microsoft Azure felhőbe a Azure Backup szolgáltatás használatával.

Ha kimaradások történnek, és a forrásadatok nem érhetők el, akkor a Azure Backup Server segítségével egyszerűen visszaállíthatja az adatforrást vagy egy másik helyet. Így ha az eredeti adat a tervezett vagy váratlan problémák miatt nem érhető el, egyszerűen visszaállíthatja az adatmennyiséget egy másik helyre.

Ez a cikk segítséget nyújt az AVS-környezet előkészítésében a virtuális gépek (VM-EK) Azure Backup Server használatával történő biztonsági mentéséhez.  Végigvezeti a következő lépéseken: 

> [!div class="checklist"]
> * Határozza meg a használni kívánt VM-lemez típusát és méretét
> * Helyreállítási pontokat tároló Recovery Services-tár létrehozása
> * Recovery Services-tároló tárolási replikálásának beállítása
> * Tárterület hozzáadása az Azure Backup Serverhez

## <a name="supported-vmware-features"></a>Támogatott VMware-funkciók

- **Ügynök nélküli biztonsági mentés:** Azure Backup Server nem szükséges, hogy az ügynök telepítve legyen a vCenter vagy ESXi-kiszolgálón a virtuális gép biztonsági mentéséhez. Ehelyett csak adja meg az IP-címet vagy a teljes tartománynevet (FQDN), valamint a VMware-kiszolgáló Azure Backup Server használatával történő hitelesítéséhez használt bejelentkezési hitelesítő adatokat.

- **Felhőbe integrált biztonsági mentés:** Azure Backup Server védi a munkaterheléseket a lemezre és a felhőre. Azure Backup Server biztonsági mentési és helyreállítási munkafolyamata segítségével kezelheti a hosszú távú adatmegőrzést és a telephelyen kívüli biztonsági mentést.

- **A vCenter által felügyelt virtuális gépek észlelése és biztosítása:** Azure Backup Server észleli és védi a vCenter-vagy ESXi-kiszolgálón üzembe helyezett virtuális gépeket. A Azure Backup Server észleli a vCenter által felügyelt virtuális gépeket is, így a nagyméretű telepítések is védhetők.

- **Mappa szintű automatikus védelem:** a vCenter lehetővé teszi a virtuális gépek megszervezését a virtuálisgép-mappákban. Azure Backup Server észleli ezeket a mappákat, és lehetővé teszi, hogy a virtuális gépeket a mappa szintjén védjék, és tartalmazza az összes almappát. A mappák védelme során Azure Backup Server nem csak a mappában lévő virtuális gépeket védi, hanem a később hozzáadott virtuális gépeket is. A Azure Backup Server naponta észleli az új virtuális gépeket, és automatikusan megvédi azokat. Amikor a virtuális gépeket rekurzív mappákba rendezi, Azure Backup Server automatikusan észleli és védi a rekurzív mappákban üzembe helyezett új virtuális gépeket.

- **Azure Backup Server továbbra is biztosítja a fürtön belüli VMotioned virtuális gépeket:** Mivel a virtuális gépek a fürtön belüli terheléselosztás vMotioned, Azure Backup Server automatikusan észleli és folytatja a virtuális gép védelmét.

- A **szükséges fájlok gyorsabb helyreállítása:** A Azure Backup Server a teljes virtuális gép helyreállítása nélkül állíthatja helyre a fájlokat és mappákat a Windows rendszerű virtuális gépekről.

## <a name="limitations"></a>Korlátozások

- Telepíteni kell az 1. kumulatív frissítést a Azure Backup Server v3 verzióhoz.

- Nem készíthető biztonsági másolat a felhasználói pillanatképekről az első Azure Backup Server biztonsági mentés előtt. Miután Azure Backup Server elvégezte az első biztonsági mentést, elvégezheti a felhasználói Pillanatképek biztonsági mentését.

- Azure Backup Server nem tudja biztosítani a VMware virtuális gépeket csatlakoztatott lemezekkel és fizikai nyers eszközök hozzárendelésével (pRDM).

- Azure Backup Server nem tudja felderíteni vagy védelemmel ellátni a VMware-Vapp.

**Az Azure VMware-megoldás (AVS) Microsoft Azure Backup-kiszolgálójának beállításához a következő lépéseket kell végrehajtania:**

- Az előfeltételek és a környezet beállítása

- Azure Recovery Services-tároló létrehozása

- Töltse le és telepítse a Azure Backup Server 

- Tárterület hozzáadása az Azure Backup Serverhez 

**Üzembe helyezési architektúra**  
Az Microsoft Azure Backup-kiszolgáló Azure IaaS VM-ként van üzembe helyezve az AVS-alapú virtuális gépek biztonsága érdekében.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="AVS üzembe helyezési architektúra" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>A Azure Backup Server környezet előfeltételei

Tekintse át az ebben a szakaszban ismertetett javaslatokat az Azure-környezetben a Azure Backup Server telepítésekor.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Győződjön meg arról, hogy az [Azure-ban konfigurálja a VMware Private-felhő hálózatkezelését](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>A virtuális gép méretének meghatározása

A fenti lépésben létrehozott virtuális hálózaton létre kell hoznia egy Windows rendszerű virtuális gépet. Azure Backup Server futtatására szolgáló kiszolgáló kiválasztásakor javasoljuk, hogy kezdje a Windows Server 2019 Datacenter katalógusának rendszerképével. Az [első Windows rendszerű virtuális gép létrehozása a Azure Portal](../virtual-machines/windows/quick-create-portal.md) oktatóanyagban az Azure-ban ajánlott virtuális géppel indul el, még akkor is, ha soha nem használta az Azure-t.

Az alábbi táblázat összefoglalja a védett munkaterhelések maximális számát az egyes Azure Backup Server virtuális gépek méretének megfelelően. Az információk belső teljesítmény- és méretezési teszteken alapulnak, amelyek a munkaterhelések méretére és forgalmára kanonikus értékeket alkalmaznak. A tényleges munkaterhelés mérete nagyobb is lehet, de a Azure Backup Server virtuális géphez csatlakoztatott lemezek számára is elérhetőnek kell lennie.

| Védett munkaterhelések max. száma | Munkaterhelés átlagos mérete | Munkaterhelés átlagos forgalma (naponta) | Minimális tárolási IOPS | Ajánlott lemez típusa/mérete      | Ajánlott virtuális gép mérete |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Nettó 5%-os forgalom                   | 2000             | Standard HDD (8 TB vagy újabb méret/lemez)  | A4V2       |
| 40                      | 150 GB                | Nettó 10%-os forgalom                  | 4500             | Prémium SSD * (1 TB vagy nagyobb méretű lemez) | DS3_V2     |
| 60                      | 200 GB                | Nettó 10%-os forgalom                  | 10500            | Prémium SSD * (8 TB vagy újabb méret/lemez) | DS3_V2     |

* A szükséges IOPs lekéréséhez használja a minimálisan ajánlott vagy magasabb méretű lemezeket. A kisebb méretű lemezek alacsonyabb IOPs biztosítanak.

> [!NOTE]
> Azure Backup Server úgy lett kialakítva, hogy dedikált, egycélú kiszolgálón fusson. A Azure Backup Server nem telepíthető a számítógépre:
> * Futtatás tartományvezérlőként
> * Telepítette az Alkalmazáskiszolgáló szerepkört
> * Ez egy System Center Operations Manager felügyeleti kiszolgáló
> * Exchange-kiszolgáló futtatása
> * Ez egy fürt csomópontja

### <a name="disks-and-storage"></a>Lemezek és tárolás

A Azure Backup Server lemezeket igényel a telepítéshez, beleértve a rendszerfájlokat, a telepítési fájlokat, az előfeltételként szükséges szoftvereket, az adatbázis-fájlokat és a tároló dedikált lemezeit.

| Követelmény                      | Ajánlott méret  |
|----------------------------------|-------------------------|
| Azure Backup Server telepítés                | Telepítési hely: 3 GB<br />Adatbázisfájlok meghajtója: 900 MB<br />Rendszermeghajtó: 1 GB az SQL-telepítéshez<br /><br />Emellett szükség van a Azure Backup Serverre, hogy a rendszer archiválja a katalógusfájlt egy ideiglenes telepítési helyre.      |
| A tárolókészlet lemeze<br />(Alapszintű köteteket használ, nem lehet dinamikus lemezen.) | 2 – 3-szor a védett adatmennyiségek mérete<br />A tárterület részletes kiszámításához tekintse meg a [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

A [felügyelt adatlemez csatlakoztatása egy Windows rendszerű virtuális géphez a Azure Portal cikkek használatával](../virtual-machines/windows/attach-managed-disk-portal.md) bemutatjuk, hogyan csatolhat új felügyelt adatlemezt egy meglévő Azure-beli virtuális géphez.

> [!NOTE]
> Egyetlen Azure Backup Server 120 TB-os puha korláttal rendelkezik a tárolási készlethez.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>A helyi lemezen és az Azure-ban tárolt biztonsági mentési adatgyűjtés

A biztonsági mentési adattárolók tárolása az Azure-ban csökkenti a biztonsági mentési infrastruktúrát Azure Backup Server virtuális gépen. Az Operational Recovery (biztonsági mentés) esetében a Azure Backup Server a virtuális géphez csatolt Azure-lemezeken tárolja a biztonsági mentési adatkészleteket. Ha a lemezeket és a tárolóhelyet csatlakoztatja a virtuális géphez, Azure Backup Server kezeli a tárolót. A biztonsági mentési adatok tárolásának mennyisége az egyes Azure-beli virtuális gépekhez csatolt lemezek számától és méretétől, valamint az Azure-beli virtuális gép minden egyes méretének számától függ. Az a2 például négy lemez. Az a3 nyolc lemez. Az A4 16 lemez. A lemezek mérete és száma is meghatározza a biztonsági mentési tár teljes kapacitását.

> [!IMPORTANT]
> A Azure Backup Server csatolt lemezeken több mint öt napig **ne** őrizze meg az operatív helyreállítási adatait. Ha az adattábla öt napnál régebbi, akkor tárolja azt az Azure Recovery Services-tárolóban.

A biztonsági mentési adattárolók Azure-ban való tárolásához hozzon létre vagy használjon Recovery Services-tárolót. A Azure Backup Server munkaterhelés biztonsági mentésének előkészítésekor [konfigurálnia kell a Recovery Services](#create-a-recovery-services-vault)-tárolót. A konfigurálást követően minden alkalommal, amikor egy online biztonsági mentési feladatot futtat, a rendszer létrehoz egy helyreállítási pontot a tárolóban. Minden Recovery Services-tár legfeljebb 9999 helyreállítási pontot tart. A létrehozott helyreállítási pontok számától és a megőrzött mennyiségtől függően sok évig megőrizheti a biztonsági másolatok mennyiségét. Létrehozhat például havi helyreállítási pontokat, és öt évig megtarthatja őket.

> [!IMPORTANT]
> Függetlenül attól, hogy az Azure-ba küldi a biztonsági mentési vagy helyi tárolást, Azure Backup Server Recovery Services-tárolóval kell regisztrálnia.

### <a name="scale-deployment"></a>Központi telepítés méretezése

Ha szeretné méretezni az üzemelő példányt, a következő lehetőségek közül választhat:

- Vertikális **felskálázás** – növelje a Azure Backup Server virtuális gép méretét egy sorozatról DS3 sorozatra, és növelje a helyi tárterületet.

- **Adat** -kiszervezés – a régebbi típusú adatküldés az Azure-ba, és csak a legújabb adat marad a Azure Backup Serverhoz csatolt tárolóban.

- **Kibővíthető** – további Azure Backup-kiszolgálók hozzáadása a munkaterhelések elleni védelemhez.

### <a name="net-framework"></a>.NET-keretrendszer

A virtuális gépnek telepítve kell lennie a .NET-keretrendszer 3,5 SP1 vagy újabb verziójának.

### <a name="join-a-domain"></a>Csatlakozás tartományhoz

A Azure Backup Server virtuális gépnek csatlakoznia kell egy tartományhoz, és a virtuális gépen rendszergazdai jogosultságokkal rendelkező tartományi felhasználónak kell telepítenie Azure Backup Server.

Bár az előzetes verzió használatakor nem támogatott, az Azure-beli virtuális gépen üzembe helyezett Azure Backup Server biztonsági mentést készíthet a virtuális gépekről az AVS-ben, de ugyanabban a tartományban kell lenniük a biztonsági mentési művelet engedélyezéséhez.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan tárolási entitás, amely az idő múlásával létrehozott helyreállítási pontokat tárolja. Emellett a védett elemekhez társított biztonsági mentési házirendeket is tartalmaz.

1. Jelentkezzen be az előfizetésbe a [Azure Portalban](https://portal.azure.com/).

1. A bal oldali menüben válassza a **minden szolgáltatás**lehetőséget.

   ![Minden szolgáltatás kiválasztása](../backup/media/backup-create-rs-vault/click-all-services.png)

1. A **minden szolgáltatás** párbeszédpanelen írja be a *Recovery Services* , és válassza ki **Recovery Services** tárolókat a listából.

   ![Adja meg és válassza ki Recovery Services tárolókat](../backup/media/backup-create-rs-vault/all-services.png)

   Megjelenik az előfizetésben található Recovery Services-tárolók listája.

1. A **Recovery Services** -tárolók irányítópultján válassza a **Hozzáadás**lehetőséget.

   ![Recovery Services-tároló hozzáadása](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Megnyílik az **Recovery Services** -tároló párbeszédpanel.

1. Adja meg a **név**, az **előfizetés**, az **erőforráscsoport**és a **hely**értékét.

   ![A Recovery Services-tároló konfigurálása](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: adjon meg egy rövid nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetésben. Olyan nevet adjon meg, amely legalább kettőnél több mint 50 karakterből áll. A névnek betűvel kell kezdődnie, és csak betűt, számot és kötőjelet tartalmazhat.

   - **Előfizetés**: válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, akkor ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Több választási lehetőség is van, ha a munkahelyi vagy iskolai fiók egynél több Azure-előfizetéshez van társítva.

   - **Erőforráscsoport**: használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza az **új létrehozása** lehetőséget, és adja meg a nevet.

   - **Hely**: válassza ki a tároló földrajzi régióját. Ha egy tárolót szeretne létrehozni az AVS virtuális gépekhez, a tárolónak ugyanabban a régióban *kell lennie* , mint az AVS Private Cloud.

1. Ha készen áll az Recovery Services-tároló létrehozására, válassza a **Létrehozás**lehetőséget.

   ![A Recovery Services-tároló létrehozása](../backup/media/backup-create-rs-vault/click-create-button.png)

   A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az **értesítéseket** a portál jobb felső sarkában található értesítések területén. Miután létrehozta a tárolót, megjelenik a Recovery Services-tárolók listájában. Ha nem látja a tárolót, válassza a **frissítés**lehetőséget.

   ![A Backup-tárolók listájának frissítése](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Tárreplikáció beállítása

A tárolási replikáció lehetőséggel választhat a Geo-redundáns tárolók (az alapértelmezett) és a helyileg redundáns tárolás között. A Geo-redundáns tároló egy másodlagos régióba másolja a Storage-fiókban tárolt adatait, így tartós adatvédelmet tesz elérhetővé. A helyileg redundáns tárolás egy olcsóbb megoldás, amely nem tartós. További információ az [Azure Storage-redundanciával](../storage/common/storage-redundancy.md)kapcsolatos geo-redundáns és helyileg redundáns tárolási lehetőségekről.

> [!IMPORTANT]
> A helyreállítási **tár replikálási típusának** (helyileg redundáns/földrajzi redundáns) módosítását a tárolóban lévő biztonsági mentések konfigurálása előtt kell elvégezni. A biztonsági mentés konfigurálása után a módosítás lehetőség le van tiltva, és nem módosíthatja a **tárolási replikálás típusát**.

1. **Recovery Services**-tárolóban kattintson az új tárolóra. 

1. A **Beállítások**területen válassza a **Tulajdonságok**lehetőséget, majd a **biztonsági mentés konfigurálása**területen kattintson a **frissítés**elemre.

1. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés**gombra.

   ![Az új tároló tárolási konfigurációjának beállítása](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-software-package"></a>Szoftvercsomag letöltése és telepítése

### <a name="downloading-the-software-package"></a>A szoftvercsomag letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a következő lépéssel. Ha nincs megnyitva Recovery Services tároló, de a Azure Portal, a főmenüben kattintson a **Tallózás**gombra.

   1. Az erőforrások listájába írja be a következőt: **Recovery Services**.

   1. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

   ![Recovery Services-tároló létrehozása – 1. lépés](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

   Megnyílik a kiválasztott tároló irányítópultja.

   ![Tároló panelének megnyitása](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   Alapértelmezés szerint a **Beállítások** nyílnak meg. Ha be van zárva, válassza a **Beállítások** lehetőséget a megnyitásához.

   ![Tároló panelének megnyitása](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Kattintson a **Backup (biztonsági mentés** ) elemre a első lépések varázsló megnyitásához.

   ![Biztonsági mentés – első lépések](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. A megnyíló ablakban tegye a következőket:

   1. A **Hol van a számítási feladatok futtatása** menü, válassza **a**helyszíni lehetőséget.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Hol fut a munkaterhelés?":::

   1. A **Miről szeretne biztonsági másolatot készíteni** menüben válassza ki a védelemmel ellátni kívánt munkaterheléseket Azure Backup Server használatával.

   1. Kattintson az **infrastruktúra előkészítése** elemre a Azure Backup Server és a tár hitelesítő adatainak letöltéséhez és telepítéséhez.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Az infrastruktúra előkészítése":::

1. A megnyíló **előkészítési infrastruktúra** ablakban tegye a következőket:

   1. Kattintson a **Letöltés** hivatkozásra a Azure Backup Server telepítéséhez.

   1. Töltse le a tároló hitelesítő adatait a **már letöltött vagy a legújabb Azure Backup Server telepítés** jelölőnégyzet bejelölésével, majd kattintson a **Letöltés**gombra. A tároló hitelesítő adatait a Azure Backup Server regisztrációja során használja a Recovery Services-tárolóra. A hivatkozások a letöltőközpontból tölthetik le, ahol letölti a szoftvercsomagot.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Infrastruktúra előkészítése – Azure Backup Server":::

1. A letöltési oldalon válassza ki az összes fájlt, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Az összes fájlt ugyanabba a mappába kell letöltenie.  Mivel a fájlok letöltésének mérete > 3GB, akár 60 percet is igénybe vehet, amíg a letöltés be nem fejeződik. 

   ![1. letöltőközpont](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extracting-the-software-package"></a>A szoftvercsomag kibontása

Ha a szoftvercsomagot egy másik kiszolgálóra töltötte le, másolja a fájlokat a létrehozott virtuális gépre a Azure Backup Server telepítéséhez.

> [!WARNING]
> A telepítőfájlok kibontásához legalább 4 GB szabad terület szükséges.

1. Miután letöltötte az összes fájlt, kattintson duplán a **MicrosoftAzureBackupInstaller. exe** fájlra a **Microsoft Azure Backup telepítővarázsló** megnyitásához, majd kattintson a **tovább**gombra.

1. Válassza ki a helyet a fájlok kibontásához, majd kattintson a **tovább**gombra.

1. A kinyerési folyamat megkezdéséhez kattintson a **Kibontás** gombra.

   ![Microsoft Azure Backup telepítővarázslója](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. A kibontás után válassza a **Setup. exe végrehajtásának** lehetőségét, majd kattintson a **Befejezés**gombra.

> [!TIP]
> A Setup. exe fájlt arra a mappára is megkeresheti, ahová kibontotta a szoftvercsomagot.

### <a name="installing-the-software-package"></a>A szoftvercsomag telepítése

1. A telepítés ablakában kattintson a **Microsoft Azure Backup** elemre a telepítővarázsló megnyitásához.

   ![Microsoft Azure Backup telepítővarázslója](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Az üdvözlőképernyőn kattintson a **tovább** gombra az előfeltételek ellenőrzéséhez.

1. Az **ellenőrzés** gombra kattintva megállapíthatja, hogy teljesülnek-e a Azure Backup Server hardver-és szoftver-előfeltételei. Ha a Met sikeresen megtörtént, kattintson a **tovább**gombra.

   ![Azure Backup Server – Üdvözöljük és Előfeltételek ellenőrzése](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. A Azure Backup Server telepítési csomagja a megfelelő SQL Server bináris fájljaival van ellátva. Új Azure Backup Server telepítésének indításakor válassza a **SQL Server új példányának telepítése ezzel a beállítással** lehetőséget, majd kattintson az **ellenőrzések és telepítés**gombra.

   ![Azure Backup Server – SQL-vizsgálat](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Ha saját SQL Server-kiszolgálót szeretne használni, a támogatott SQL Server verziók a következők: SQL Server 2014 SP1 vagy újabb, 2016 és 2017. Minden SQL Server verziónak standard vagy Enterprise 64 bitesnek kell lennie. A Azure Backup Server távoli SQL Server példánnyal nem működik. A Azure Backup Server által használt példánynak helyinek kell lennie. Ha Azure Backup Server meglévő SQL Servert használ, a telepítő csak az SQL Server *nevesített példányainak* használatát támogatja.

   Ha hiba lép fel a gép újraindítására vonatkozó javaslattal, tegye a következőt, majd kattintson **ismét az ismételt vizsgálat**gombra. Ha vannak SQL-konfigurációs problémák, konfigurálja újra az SQL-t az SQL-irányelvek alapján, majd próbálja meg újra telepíteni/frissíteni Azure Backup Server a meglévő SQL-példány használatával.

   **Manuális konfigurálás**

   Ha saját SQL-példányt használ, ügyeljen arra, hogy Builtin\rendszergazda adjon hozzá a sysadmin (rendszergazda) szerepkörhöz a Master ADATBÁZIShoz.

   **SSRS-konfiguráció SQL 2017-mel**

   Ha az SQL 2017 saját példányát használja, manuálisan kell konfigurálnia az SSRS-t. Az SSRS konfigurálása után ellenőrizze, hogy az SSRS *IsInitialized* tulajdonsága *true*értékre van-e állítva. Ha igaz értékre van állítva, a MABS feltételezi, hogy az SSRS már konfigurálva van, és kihagyja az SSRS-konfigurációt.

   Az SSRS konfigurációs állapotának megtekintéséhez futtassa a következő parancsot:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Az SSRS konfigurálásához használja a következő értékeket:
   * Szolgáltatásfiók: a "beépített fiók használata" hálózati szolgáltatásnak kell lennie
   * Webszolgáltatás URL-címe: a "virtuális könyvtár" legyen ReportServer_\<SQLInstanceName>
   * Adatbázis: a DatabaseName ReportServer $\<SQLInstanceName>
   * Webes portál URL-címe: a "virtuális könyvtár" legyen Reports_\<SQLInstanceName>

   [További](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) információ az SSRS-konfigurációról.

   > [!NOTE]
   > A [Microsoft Online Services használati feltételei](https://www.microsoft.com/licensing/product-licensing/products) (OST) szabályozzák a Azure Backup Server-adatbázisként használt SQL Server licencelését. Az OST-nek megfelelően a SQL Server a csomagban Azure Backup Server csak a Azure Backup Server adatbázisa használható.

1. A sikeres telepítést követően kattintson a **tovább**gombra.

1. Adja meg Microsoft Azure Backup-kiszolgáló fájljainak telepítéséhez szükséges helyet, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Az Azure-ba történő biztonsági mentéshez a semmiből kell megadni. Győződjön meg arról, hogy a hely a felhőbe történő biztonsági mentéshez tervezett adatmennyiség legalább 5%-a. A lemezek védelme érdekében a telepítés befejeződése után külön lemezeket kell konfigurálni. További információ a Storage-készletekről: a [tárolási készletek és a lemezes tárolás konfigurálása](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **tovább**gombra.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Válassza ki, hogy szeretné-e használni a Microsoft Update a frissítések kereséséhez, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Javasoljuk, hogy Windows Update átirányítást a Microsoft Updatera, amely biztonsági és fontos frissítéseket kínál a Windows és más termékek, például a Microsoft Azure Backup-kiszolgáló számára.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Tekintse át a *Beállítások összegzését* , és kattintson a **telepítés**gombra.

   A telepítés fázisokban történik. Az első fázis telepíti a Microsoft Azure Recovery Services ügynököt, és a második fázis ellenőrzi az internetkapcsolatot. Ha az internetkapcsolat elérhető, folytathatja a telepítést. Ha nem, meg kell adnia a proxy adatait az internethez való csatlakozáshoz. Az utolsó fázis ellenőrzi az előfeltételként szükséges szoftvereket, és ha nincs telepítve, a rendszer az Microsoft Azure Recovery Services ügynökkel együtt telepíti a hiányzó szoftvereket.

1. Kattintson a **Tallózás** gombra, és keresse meg a tároló hitelesítő adatait, hogy regisztrálja a gépet a Recovery Services-tárolóban, majd kattintson a **tovább**gombra.

1. Válasszon egy hozzáférési kódot az Azure és a telephely között továbbított adattitkosításhoz/visszafejtéshez.

   > [!TIP]
   > Automatikusan létrehozhat egy jelszót, vagy megadhatja a saját minimum 16 karakterből álló jelszót.

1. Adja meg a jelszó mentéséhez szükséges helyet, majd kattintson a **tovább** gombra a kiszolgáló regisztrálásához.

   > [!IMPORTANT]
   > Fontos, hogy a jelszót a helyi kiszolgálótól eltérő biztonságos helyre is mentse. A Microsoft nyomatékosan javasolja egy Azure Key Vault használatát a jelszó tárolásához.

   A Microsoft Azure Recovery Services ügynök telepítésének befejezése után a rendszer áthelyezi a SQL Server és a Azure Backup Server összetevők telepítését és konfigurációját.

   ![Azure Backup Server](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. A telepítési lépés befejezése után kattintson a **Bezárás**gombra.

### <a name="install-update-rollup-1"></a>1. kumulatív frissítés telepítése

Az 1. kumulatív frissítés telepítése a Microsoft Azure Backup Server V3 esetében kötelező, a munkaterhelések védelme előtt. Ha meg szeretné tekinteni a hibajavítások listáját és a Microsoft Azure Backup Server v3 UR1 telepítési utasításait, tekintse meg a TUDÁSBÁZISCIKK [4534062](https://support.microsoft.com/en-us/help/4534062/). számú cikkét.

## <a name="add-storage-to-azure-backup-server"></a>Tárterület hozzáadása az Azure Backup Serverhez

A Azure Backup Server v3 modern biztonsági másolati tárhely a következőket kínálja:

-  Tárterület-megtakarítás 50%-ban

-  Háromszor gyorsabb biztonsági másolatok

-  Hatékonyabb tárolás

-  Munkaterhelés-kompatibilis tároló

### <a name="volumes-in-backup-server"></a>Kötetek a biztonsági mentési kiszolgálón

Ha még nincs hozzáadva, adja hozzá az adatlemezeket a szükséges tárolási kapacitással a Azure Backup Server virtuális géphez.

A Backup Server v3 csak a tárolási köteteket fogadja el. Kötet hozzáadásakor a biztonsági mentési kiszolgáló a kötetet a rugalmas fájlrendszerre (ReFS) formázza, amelyhez modern biztonsági másolati tárhely szükséges.

### <a name="add-volumes-to-backup-server-disk-storage"></a>Kötetek hozzáadása a biztonsági mentési kiszolgáló lemezes tárolásához

1. A **felügyelet** ablaktáblán Ellenőrizze újra a tárolót, majd kattintson a **Hozzáadás**gombra. 

1. Válasszon a rendelkezésre álló kötetek közül a tárolóhoz való hozzáadáshoz. 

1. Az elérhető kötetek hozzáadása után adjon meg egy felhasználóbarát nevet, amely segít a kezelésében. 

1. A kötetek ReFS való formázásához kattintson az **OK** gombra, így a Backup Server a modern biztonsági másolati tárhely előnyeit használhatja.

![Elérhető kötetek hozzáadása](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan konfigurálhatja az Azure VMware-megoldáson (AVS) futó VMware virtuális gépek biztonsági mentését Azure Backup Server használatával.

> [!div class="nextstepaction"]
> [AVS virtuális gépek biztonsági mentésének konfigurálása](backup-avs-vms-with-mabs.md)

