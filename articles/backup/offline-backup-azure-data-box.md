---
title: Offline biztonsági mentés az Azure Data Box használatával
description: Megtudhatja, hogyan használhatja az Azure Data Box ot a nagy kezdeti biztonsági mentési adatok offline elszállításához a MARS-ügynöktől a Recovery Services-tárolóba.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672968"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Az Azure Backup offline biztonsági mentése az Azure Data Box használatával

[Az Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) segítségével a nagy kezdeti Microsoft Azure Recovery Services (MARS) biztonsági mentések offline (hálózat használata nélkül) egy Recovery Services-tárolóba. Ez a folyamat időt és hálózati sávszélességet takarít meg, amely egyébként nagy mennyiségű biztonsági mentési adatot használna fel online nagy késleltetésű hálózaton keresztül. Ez a fejlesztés jelenleg előzetes verzióban érhető el. Az Azure Data Box szolgáltatáson alapuló offline biztonsági mentés két különböző előnyt biztosít [az offline biztonsági mentéshez az Azure import/export szolgáltatása alapján:](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

* Nincs szükség saját Azure-kompatibilis lemezek és összekötők beszerzésére. Az Azure Data Box a kiválasztott [Data Box termékváltozathoz](https://azure.microsoft.com/services/databox/data/)társított lemezeket szállítja.
* Az Azure Backup (MARS Agent) közvetlenül írhat biztonsági mentési adatokat az Azure Data Box támogatott suk-jaira. Ez a funkció szükségtelenné teszi, hogy átmeneti helyet létesítsen a kezdeti biztonsági mentési adatokhoz. Az adatok lemezre való formázásához és másolásához nincs szükség segédprogramokra sem.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box a MARS ügynökkel

Ez a cikk bemutatja, hogyan használhatja az Azure Data Box nagy kezdeti biztonsági mentési adatok offline a MARS-ügynök egy Recovery Services-tároló.

## <a name="supported-platforms"></a>Támogatott platformok

A marsügynökből az Azure Data Box használatával történő adatok bemagolására irányuló folyamat a következő Windows-ska-kban támogatott.

| **OS**                                 | **Sku**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Munkaállomás**                        |                                                              |
| Windows 10, 64 bites                     | Enterprise, Pro, Home                                       |
| Windows 8.1, 64 bites                    | Enterprise, Pro                                             |
| Windows 8, 64 bites                      | Enterprise, Pro                                             |
| Windows 7, 64 bites                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Kiszolgáló**                             |                                                              |
| Windows Server 2019, 64 bites            | Standard, Datacenter, Essentials                            |
| Windows Server 2016, 64 bites            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2, 64 bites         | Standard, Datacenter, Foundation                            |
| Windows Server 2012, 64 bites            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016, 64 bites    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2, 64 bites | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012, 64 bites    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1, 64 bites     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bites        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Biztonsági másolat adatainak mérete és a támogatott Data Box suk-ok

| Biztonsági mentési adatok mérete (marsos tömörítés után)* kiszolgálónként | Támogatott Azure Data Box Termékváltozat                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Azure Data Box lemez](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7,2 TB és <=80 TB**                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*A tipikus tömörítési arány 10% és 20% között változik. <br>
**Ha várhatóan több mint 80 TB kezdeti biztonsági mentési [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)adattal rendelkezik egyetlen MARS-kiszolgálóhoz, lépjen kapcsolatba a programmal.

>[!IMPORTANT]
>Az egyetlen kiszolgálóról származó kezdeti biztonsági mentési adatokat egyetlen Azure Data Box-példányban vagy Azure Data Box-lemezben kell elhelyezni, és nem oszthatók meg több azonos vagy különböző sk-t tartalmazó eszközök között. Az Azure Data Box-eszközök azonban több kiszolgálóról is tartalmazhatnak kezdeti biztonsági mentéseket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription-and-required-permissions"></a>Azure-előfizetés és a szükséges engedélyek

* A folyamathoz Azure-előfizetés szükséges.
* A folyamat megköveteli, hogy az offline biztonsági mentési szabályzat végrehajtására kijelölt felhasználó az Azure-előfizetés tulajdonosa.
* A Data Box feladat és a Recovery Services-tároló (amelyhez az adatokat kell beadni) kell ugyanabban az előfizetésben.
* Azt javasoljuk, hogy az Azure Data Box-feladathoz és a Recovery Services-tárolóhoz társított céltár-fiók ugyanabban a régióban található. Ez azonban nem szükséges.

### <a name="get-azure-powershell-370"></a>Az Azure PowerShell 3.7.0 beszereznie

*Ez a folyamat legfontosabb előfeltétele*. Az Azure PowerShell 3.7.0-s verziójának telepítése előtt hajtsa végre a következő ellenőrzéseket.

#### <a name="step-1-check-the-powershell-version"></a>1. lépés: Ellenőrizze a PowerShell-verziót

1. Nyissa meg a Windows PowerShellt, és futtassa a következő parancsot:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Ha a kimenet 3.7.0-nál magasabb verziót jelenít meg, tegye a "2. Ellenkező esetben ugorjon a "3.

#### <a name="step-2-uninstall-the-powershell-version"></a>2. lépés: A PowerShell-verzió eltávolítása

Távolítsa el a PowerShell jelenlegi verzióját.

1. Távolítsa el a függő modulokat a következő parancs futtatásával a PowerShellben:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Az összes függő modul sikeres törlésének biztosításához futtassa a következő parancsot:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3. lépés: A PowerShell 3.7.0-s verziójának telepítése

Miután meggyőződött arról, hogy nincsenek Jelen AzureRM-modulok, telepítse a 3.7.0-s verziót az alábbi módszerek egyikével:

* A GitHubról használja [ezt a hivatkozást.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

Vagy:

* Futtassa a következő parancsot a PowerShell ablakban:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Az Azure PowerShell is telepíthető egy msi fájl használatával. Az eltávolításhoz távolítsa el a Vezérlőpult **Programok eltávolítása** beállításával.

### <a name="order-and-receive-the-data-box-device"></a>A Data Box eszköz megrendelése és fogadása

A MARS és az Azure Data Box használatával az offline biztonsági mentési folyamat megköveteli, hogy a Data Box-eszközök kézbesített állapotban legyenek, mielőtt a MARS-ügynök használatával offline biztonsági mentést indítana. A követelménynek leginkább megfelelő termékváltozat megrendeléséhez olvassa el az Adatok biztonsági mentése és a [támogatott adatdoboz-termékváltozatok című témakört.](#backup-data-size-and-supported-data-box-skus) Kövesse az [oktatóanyag lépéseit: Rendeljen egy Azure Data Box lemezt](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) a Data Box-eszközök rendeléséhez és fogadásához.

> [!IMPORTANT]
> Ne válassza a *BlobStorage lehetőséget* a **Fiók fajtához.** A MARS-ügynök olyan fiókot igényel, amely támogatja a lapblobokat, amelyek nem támogatottak, ha a *BlobStorage* van kiválasztva. Válassza ki **a Storage V2 (általános célú v2)** **a fiók típus,** amikor létrehozza a céltárfiók az Azure Data Box-feladat hoz létre.

![Fiókfajta kiválasztása a példány részleteiben](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>A MARS-ügynök telepítése és beállítása

1. Győződjön meg arról, hogy eltávolítja a MARS-ügynök korábbi telepítéseit.
1. Töltse le a legújabb MARS Agent [erről a honlapról](https://aka.ms/azurebackup_agent).
1. *Futtassa a MARSAgentInstaller.exe*programot, és *csak* azokat a lépéseket tegye az [ügynök telepítéséhez és regisztrálásához](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) a Recovery Services tárolóba, ahol a biztonsági másolatokat tárolni szeretné.

   > [!NOTE]
   > A Recovery Services-tárolónak ugyanabban az előfizetésben kell lennie, mint az Azure Data Box-feladatnak.

   Miután az ügynök regisztrálva van a Recovery Services-tárolóban, kövesse a következő szakaszokban leírt lépéseket.

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box-eszközök beállítása

A megrendelt Azure Data Box termékváltozattól függően hajtsa végre a megfelelő szakaszokban ismertetett lépéseket. A lépések bemutatják, hogyan állíthatja be és készítheti elő a Data Box-eszközöket a MARS-ügynök számára a kezdeti biztonsági mentési adatok azonosításához és átviteléhez.

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box-lemezek beállítása

Ha egy vagy több Azure Data Box lemezt rendelt (egyenként legfeljebb 8 TB-ot), kövesse az itt leírt lépéseket a [Data Box lemez kicsomagolásához, csatlakoztatásához és zárolásának feloldásához.](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)

>[!NOTE]
>Lehetséges, hogy a MARS-ügynökkel rendelkező kiszolgálónak nincs USB-portja. Ebben a helyzetben csatlakoztathatja az Azure Data Box lemezt egy másik kiszolgálóhoz vagy ügyfélhez, és hálózati megosztásként elérhetővé teheti az eszköz gyökerét.

### <a name="set-up-azure-data-box"></a>Az Azure Data Box beállítása

Ha egy Azure Data Box-példányt rendelt (legfeljebb 100 TB-ot), kövesse az alábbi lépéseket [a Data Box-példány beállításához.](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Az Azure Data Box-példány csatlakoztatása helyi rendszerként

A MARS-ügynök a helyi rendszer környezetben működik, ezért ugyanazt a jogosultsági szintet kell biztosítani a csatlakoztatási útvonalhoz, ahol az Azure Data Box-példány csatlakozik. 

Annak biztosítása érdekében, hogy a Data Box eszközt helyi rendszerként csatlakoztathassa az NFS protokoll használatával:

1. Engedélyezze az ügyfelet az NFS-szolgáltatáshoz azon a Windows-kiszolgálón, amelyen telepítve van a MARS-ügynök. Adja meg az alternatív *wim:d:\sources\install.wim:4 forrást.*
1. Töltse le a <https://download.sysinternals.com/files/PSTools.zip> PSExec-et a MARS-ügynök telepítésével rendelkező szerverre.
1. Nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot a *PSExec.exe* könyvtárat tartalmazó könyvtárral az aktuális könyvtárként.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Az előző parancs eredményeként megnyíló parancsablak a Helyi rendszer környezetben található. Ezzel a parancsablakkal hajtsa végre az Azure-lapblob-megosztás hálózati meghajtóként való csatlakoztatásának lépéseit a Windows-kiszolgálón.
1. Kövesse a Csatlakozás az [adatdobozhoz](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) című részben leírt lépéseket, hogy a kiszolgálót a MARS-ügynökkel csatlakoztassa a Data Box eszközhöz az NFS-en keresztül. Futtassa a következő parancsot a Helyi rendszer parancssorban az Azure-lapblobok megosztásának csatlakoztatásához.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   A megosztás csatlakoztatása után ellenőrizze, hogy el tudja-e érni az X: alkalmazást a kiszolgálóról. Ha tudja, folytassa a cikk következő részével.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Kezdeti biztonsági mentési adatok átvitele az Azure Data Box-eszközökre

1. Nyissa meg a **Microsoft Azure Backup** alkalmazást a kiszolgálón.
1. A **Műveletek** ablaktáblán válassza a **Biztonsági másolat ütemezése**lehetőséget.

    ![Biztonsági másolat ütemezése kiválasztása](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Kövesse a **Biztonsági másolat ütemezése varázsló**lépéseit.

1. Elemek hozzáadása az **Elemek hozzáadása** gomb kiválasztásával. Tartsa meg az elemek teljes méretét a megrendelt és fogadott [Azure Data Box Termékváltozat által támogatott mérethatárokon](#backup-data-size-and-supported-data-box-skus) belül.

    ![Elemek hozzáadása a biztonsági mentéshez](./media/offline-backup-azure-data-box/add-items.png)

1. Válassza ki a fájlok és mappák és a rendszerállapot megfelelő biztonsági mentési **ütemezését és adatmegőrzési házirendjét.** **System State** A rendszerállapot csak Windows-kiszolgálókra vonatkozik, Windows-ügyfelekre nem.
1. A varázsló **Kezdeti biztonsági másolat típusának kiválasztása (Fájlok és mappák)** lapján válassza az Átvitel a Microsoft Azure Data Box lemezekkel lehetőséget, majd a **Tovább** **gombot.**

    ![A kezdeti biztonsági másolat típusának kiválasztása](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Jelentkezzen be az Azure-ba, amikor a rendszer kéri az Azure-előfizetéstulajdonosi hozzáféréssel rendelkező felhasználói hitelesítő adatok használatával. Miután sikerült ezt, meg kell jelennie egy oldal, amely hasonlít ez.

    ![Erőforrások létrehozása és a szükséges engedélyek alkalmazása](./media/offline-backup-azure-data-box/creating-resources.png)

   A MARS-ügynök ezután lekéri a Data Box-feladatokat a Kézbesített állapotban lévő előfizetésben.

    ![Adatdoboz-feladatok lekérése az előfizetés-azonosítóhoz](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Válassza ki a megfelelő Data Box sorrendet, amelyhez kicsomagolta, csatlakoztatta és feloldotta a Data Box lemezzárolást. Válassza a **Tovább lehetőséget.**

    ![Adatmező-rendelések kiválasztása](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Válassza az **Eszköz észlelése lehetőséget** az **Adatdoboz-eszközészlelés** lapon. Ez a művelet lekéri a MARS-ügynök helyileg csatlakoztatott Azure Data Box lemezek et, és észleli azokat.

    ![Adatdoboz-eszköz felismerése](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Ha az Azure Data Box-példányt hálózati megosztásként csatlakoztatta (az USB-portok elérhetetlensége miatt, vagy azért, mert megrendelte és csatlakoztatta a 100 TB-os Data Box-eszközt), az észlelés először sikertelen lesz. Lehetősége van a Data Box eszköz hálózati elérési útjának megadására.

    ![Adja meg a hálózati elérési utat](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Adja meg a hálózati elérési utat az Azure Data Box lemez gyökérkönyvtárához. Ennek a könyvtárnak PageBlob nevű könyvtárat kell *tartalmaznia.*
    >
    >![Az Azure Data Box lemez gyökérkönyvtára](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Ha például a lemez elérési `\\mydomain\myserver\disk1\` útja, és a *disk1* egy *PageBlob*nevű könyvtárat `\\mydomain\myserver\disk1\`tartalmaz, akkor a MARS Ügynök varázsló lapján megadott elérési út a .
    >
    >Ha [egy Azure Data Box 100-TB-os eszközt állít be,](#set-up-azure-data-box-devices)adja meg `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` az eszköz hálózati elérési útjaként.

1. Válassza a **Tovább**lehetőséget, és a következő lapon a **Befejezés** gombra a biztonsági mentési és adatmegőrzési házirend mentéséhez az Azure Data Box használatával az offline biztonsági mentés konfigurációjával.

   A következő lap megerősíti, hogy a házirend mentése sikeresen megtörtént.

    ![A házirend mentése sikeresen megtörtént](./media/offline-backup-azure-data-box/policy-saved.png)

1. Válassza a **Bezárás** lehetőséget az előző lapon.

1. Válassza a **Biztonsági másolatot a** MARS-ügynök konzol **Műveletek** ablaktábláján. A varázsló lapján válassza a **Biztonsági másolatok** kiválasztása lehetőséget.

    ![Biztonsági másolatot készítő varázsló](./media/offline-backup-azure-data-box/backup-now.png)

A MARS-ügynök megkezdi a kiválasztott adatok biztonsági mentését az Azure Data Box-eszközre. Ez a folyamat több órától néhány napig is eltarthat. Az idő attól függ, hogy a fájlok száma és a kapcsolat sebessége a kiszolgáló a MARS-ügynök és az Azure Data Box lemez között.

Miután az adatok biztonsági mentése befejeződött, megjelenik egy oldal a MARS-ügynök, amely hasonlít ez.

![A biztonsági mentés előrehaladása látható](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>A biztonsági mentés utáni lépések

Ez a szakasz ismerteti a lépéseket, miután az adatok biztonsági mentése az Azure Data Box Disk sikeres.

* Kövesse a cikkben leírt lépéseket [az Azure Data Box lemez azure-ba szállításához.](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up) Ha 100 TB-os Azure Data Box-eszközt használt, kövesse az alábbi lépéseket [az Azure Data Box-eszköz Azure-ba történő szállításához.](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)

* [Figyelheti a Data Box-feladatot](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) az Azure Portalon. Az Azure Data Box-feladat befejezése után a MARS-ügynök automatikusan áthelyezi az adatokat a tárfiókból a Recovery Services-tárolóba a következő ütemezett biztonsági mentés időpontjában. Ezután a biztonsági mentési feladatot *feladat ként jelöli meg,* ha egy helyreállítási pont sikeresen létrejött.

    >[!NOTE]
    >A MARS-ügynök biztonsági mentéseket indít el a házirend létrehozása során ütemezett időpontokban. Ezek a feladatok a "Várakozás az Azure Data Box feladat befejezésére" jelzőt jelölik meg a feladat befejezéséig.

* Miután a MARS-ügynök sikeresen létrehozott egy helyreállítási pontot, amely megfelel a kezdeti biztonsági mentésnek, törölheti a tárfiókot vagy az Azure Data Box-feladathoz társított konkrét tartalmakat.

## <a name="troubleshooting"></a>Hibaelhárítás

A Microsoft Azure Backup (MAB) ügynök létrehoz egy Azure Active Directory (Azure AD) alkalmazást a bérlőben. Ez az alkalmazás megköveteli a hitelesítési tanúsítványt, amely létrehozott és feltöltött, amikor konfigurálja a kapcsolat nélküli vetési házirend. Az Azure PowerShell használatával hozzuk létre és töltsük fel a tanúsítványt az Azure AD-alkalmazásba.

### <a name="problem"></a>Probléma

Az offline biztonsági mentés konfigurálásakor előfordulhat, hogy az Azure PowerShell-parancsmag ban hiba miatt problémákkal szembesül. Előfordulhat, hogy nem tud több tanúsítványt hozzáadni ugyanahhoz az Azure AD-alkalmazáshoz, amelyet a MAB-ügynök hozott létre. Ez a probléma hatással lesz, ha offline vetési házirendet konfigurált ugyanahhoz vagy egy másik kiszolgálóhoz.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Annak ellenőrzése, hogy a problémát ez a konkrét kiváltó ok okozza-e

Ha meg szeretné tudni, hogy a probléma megegyezik-e a korábban leírtkal, tegye az alábbi lépések egyikét.

#### <a name="step-1"></a>1. lépés

Ellenőrizze, hogy a következő hibaüzenet jelenik-e meg a MAB konzolon az offline biztonsági mentés konfigurálásakor.

![Nem lehet létrehozni az aktuális Azure-fiók offline biztonsági mentési szabályzatát](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>2. lépés

1. Nyissa meg a **Temp** mappát a telepítési útvonalon. Az ideiglenes mappa alapértelmezett elérési útja a *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Keresse meg a *CBUICurr* fájlt, és nyissa meg a fájlt.

1. A *CBUICurr* fájlban görgessen az utolsó sorhoz, és ellenőrizze, hogy a `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`probléma megegyezik-e a hibaüzenetben szereplővel: .

### <a name="workaround"></a>Áthidaló megoldás

A probléma megoldásához kerülő megoldásként tegye a következő lépéseket, és próbálkozzon újra a házirend-konfigurációval.

#### <a name="step-1"></a>1. lépés

Jelentkezzen be a PowerShellbe, amely a MAB felhasználói felületén jelenik meg egy másik fiók használatával rendszergazdai hozzáféréssel az előfizetésen, amely az importálási vagy exportálási feladatot létrehozza.

#### <a name="step-2"></a>2. lépés

Ha egyetlen más kiszolgáló sem konfigurált offline vetést, és nincs más kiszolgáló, amely az `AzureOfflineBackup_<Azure User Id>` alkalmazástól függne, törölje ezt az alkalmazást. Válassza az **Azure Portal** > **Azure Active Directory** > **alkalmazásregisztrációk lehetőséget.**

>[!NOTE]
> Ellenőrizze, hogy `AzureOfflineBackup_<Azure User Id>` az alkalmazás nem rendelkezik-e más kapcsolat nélküli vetés konfigurálva, és akkor is, ha nincs más kiszolgáló függ az alkalmazástól. Nyissa meg a **Beállítások** > **kulcsok lapot** a Nyilvános **kulcsok** szakaszban. Nem kell más nyilvános kulcsokat hozzáadni. Az alábbi képernyőképen további tájékoztató jellegű.
>
>![Nyilvános kulcsok](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>3. lépés

Az offline biztonsági mentéshez konfigurálni kívánt kiszolgálóról hajtsa végre a következő műveleteket.

1. Nyissa meg a **Számítógép-tanúsítványalkalmazás** > **Kezelése A Személyes** lap `CB_AzureADCertforOfflineSeeding_<ResourceId>`lapot, és keresse meg a nevet.

2. Jelölje ki a tanúsítványt, kattintson a jobb gombbal **az Összes feladat parancsra,** és válassza az **Exportálás** titkos kulcs nélkül parancsot a .cer formátumban.

3. Nyissa meg a 2. Válassza a **Beállítások kulcsok** > **Keys** > **feltöltése nyilvános kulcsot**lehetőséget. Töltse fel az előző lépésben exportált tanúsítványt.

    ![Nyilvános kulcs feltöltése](./media/offline-backup-azure-data-box/upload-public-key.png)

4. A kiszolgálón nyissa meg a rendszerleíró adatbázist úgy, hogy beírja a **regeditet** a futtatási ablakba.

5. Nyissa meg a rendszerleíró *adatbázist: Számítógép\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Kattintson a jobb gombbal **a CloudBackupProvider** `AzureADAppCertThumbprint_<Azure User Id>`elemre, és adjon hozzá egy új karakterláncértéket a nevével.

    >[!NOTE]
    > Az Azure felhasználói azonosítójának lekért, hajtsa végre az alábbi műveletek egyikét:
    >
    >* Az Azure-hoz csatlakoztatott PowerShell, futtassa a `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` parancsot.
    > * Nyissa meg a rendszerleíró adatbázis elérési útját *Számítógép\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup* *CurrentUserId*néven.

6. Kattintson a jobb gombbal az előző lépésben megadott karakterláncra, és válassza a **Módosítás parancsot.** Az értékben adja meg a 2. Válassza **az OK gombot.**

7. Az ujjlenyomat értékének leigazolásához kattintson duplán a tanúsítványra. Válassza a **Részletek** lapot, és görgessen lefelé, amíg meg nem jelenik az ujjlenyomatmező. Válassza **az Ujjlenyomat**lehetőséget, és másolja az értéket.

    ![A tanúsítvány ujjlenyomatmezője](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Kérdések

Ha bármilyen kérdése vagy pontosítása van [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)az ön által tapasztalt problémákkal kapcsolatban, forduljon a .
