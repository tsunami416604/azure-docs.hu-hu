---
title: Offline biztonsági mentés Azure Data Box használatával
description: Megtudhatja, hogyan használhatja a Azure Data Boxt a nagyméretű kezdeti biztonsági mentési adatoknak a MARS-ügynökből egy Azure Recovery Services-tárolóba való összevetéséhez.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027029"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Offline – biztonsági mentés Azure Backup Azure Data Box használatával

A [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) szolgáltatással a nagyméretű kezdeti Mars-biztonsági mentések az Azure Recovery Services-tárolóra való offline (hálózat nélkül) alapján is kiírhatók.  Ez az időt és a hálózati sávszélességet is elmenti, ami egyébként nagy mennyiségű biztonsági mentési adat online mozgatása nagy késleltetésű hálózaton keresztül. Ez a fejlesztés jelenleg előzetes verzióban érhető el. A Azure Data Box-alapú offline biztonsági mentés két különböző előnyt biztosít az [Azure import/export szolgáltatás-alapú offline biztonsági mentéshez](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Nincs szükség saját Azure-kompatibilis lemezek és összekötők beszerzésére. Azure Data Box a szolgáltatás a kiválasztott [Data Box SKU](https://azure.microsoft.com/services/databox/data/) -hoz társított lemezeket.

2. A Azure Backup (MARS-ügynök) közvetlenül a Azure Data Box támogatott SKU-jéről írhatja fel a biztonsági mentési adatmennyiséget. Így nem kell kiépíteni egy átmeneti helyet a kezdeti biztonsági mentési adataihoz, és szükség van a segédprogramokra az adatlemezek formázásához és másolásához.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box a MARS-ügynökkel

Ez a cikk azt ismerteti, hogyan használható a Azure Data Box a nagyméretű kezdeti biztonsági mentési adatoknak a MARS-ügynökből egy Azure Recovery Services-tárolóba való összevetéséhez. A cikk a következő részekre oszlik:

* Támogatott platformok
* Támogatott Data Box SKU-i
* Előfeltételek
* MARS-ügynök beállítása
* Telepítő Azure Data Box
* Adatbiztonsági mentési adatok átvitele Azure Data Boxre
* Biztonsági mentés utáni lépések

## <a name="supported-platforms"></a>Támogatott platformok

A MARS-ügynökről az Azure Data Box használatával végzett adatelemzési folyamat a következő Windows-SKU-kal támogatott:

| **OS**                                 | **Termékváltozat**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Munkaállomás**                        |                                                              |
| Windows 10 64 bit                     | Enterprise, Pro, Home                                       |
| Windows 8,1 64 bit                    | Enterprise, Pro                                             |
| Windows 8 64 bit                      | Enterprise, Pro                                             |
| Windows 7 64 bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Kiszolgáló**                             |                                                              |
| Windows Server 2019 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 bit            | Datacenter, alap, standard                            |
| Windows Storage Server 2016 64 bit    | Standard, munkacsoport                                         |
| Windows Storage Server 2012 R2 64 bit | Standard, munkacsoport, alapvető                              |
| Windows Storage Server 2012 64 bit    | Standard, munkacsoport                                         |
| Windows Server 2008 R2 SP1 64 bites     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bites        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Biztonsági másolatok méretének és támogatott Data Box SKU-i

| Biztonsági másolati adatméret (a MARS által végzett tömörítés után) * kiszolgálónkénti bontásban | Támogatott Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7,2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB és < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* A tipikus tömörítési arányok 10-20%-ban változnak <br>
\* * Ha egy MARS-kiszolgáló esetében várhatóan több mint 80 TB-os kezdeti biztonsági mentési adatra számíthat, * * elérheti [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Az egyetlen kiszolgálóról származó kezdeti biztonsági mentési adatoknak egyetlen Azure Data Boxon vagy Azure Data Box Disk belül kell szerepelniük, és nem oszthatók meg több azonos vagy eltérő SKU-hoz tartozó eszköz között. Előfordulhat azonban, hogy egy Azure Data Box eszköz több kiszolgálóról származó kezdeti biztonsági másolatokat tartalmaz.

## <a name="pre-requisites"></a>Előfeltételek

### <a name="azure-subscription-and-required-permissions"></a>Azure-előfizetés és szükséges engedélyek

* A folyamathoz Azure-előfizetés szükséges
* A folyamat megköveteli, hogy az offline biztonsági mentési szabályzat elvégzésére kijelölt felhasználó az Azure-előfizetés tulajdonosa legyen
* A Data Box-és a Recovery Services-tárolónak (amelybe az adatok betöltését kell végezni) ugyanahhoz az előfizetéshez kell tartoznia.
* Azt javasoljuk, hogy a Azure Data Box feladatokhoz és a Recovery Services-tárolóhoz társított célként megadott Storage-fiók ugyanabban a régióban legyen. Azonban ez nem szükséges.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 beolvasása

**Ez a folyamat legfontosabb előfeltétele**. Azure PowerShell telepítése előtt (Ver. 3.7.0), hajtsa végre a következő ellenőrzéseket * *

#### <a name="step-1-check-powershell-version"></a>1\. lépés: a PowerShell-verzió keresése

* Indítsa el a Windows PowerShellt, és futtassa az alábbi parancsot:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Ha a kimenet a **3.7.0**-nál nagyobb verziószámot jelenít meg, hajtsa végre az alábbi 2. lépést. Máskülönben folytassa a 3. lépéssel.

#### <a name="step-2-uninstall-the-powershell-version"></a>2\. lépés: a PowerShell verziójának eltávolítása

A következő műveletek végrehajtásával távolítsa el a PowerShell aktuális verzióját:

* Távolítsa el a függő modulokat a következő parancs futtatásával a PowerShellben:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Futtassa az alábbi parancsot az összes függő modul sikeres törlésének biztosításához:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3\. lépés: a PowerShell-verzió 3.7.0 telepítése

Miután ellenőrizte, hogy nincsenek-e AzureRM modulok, telepítse a 3.7.0 verzióját az alábbi módszerek egyikének használatával:

* A GitHubról, [hivatkozás](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

VAGY

* Futtassa a következő parancsot a PowerShell-ablakban:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

A Azure PowerShell msi-fájllal is telepíthető. A eltávolításához távolítsa el azt a Vezérlőpult Programok eltávolítása beállításával.

### <a name="order-and-receive-the-data-box-device"></a>A Data Box eszköz megrendelése és fogadása

Az offline biztonsági mentési folyamat a MARS és a Azure Data Box használatával megköveteli, hogy a szükséges Data Box eszközök "kézbesítési" állapotban legyenek, mielőtt a MARS-ügynök használatával aktiválja az offline biztonsági mentést. Tekintse át a [biztonsági másolatok mérete és a támogatott Data Box SKU](#backup-data-size-and-supported-data-box-skus) -ket, hogy a követelménynek megfelelő SKU-t rendelje hozzá. A Data Box-eszközök megrendeléséhez és fogadásához kövesse az [ebben a cikkben](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) ismertetett lépéseket.

>[!IMPORTANT]
>Ne válassza a BlobStorage lehetőséget a fiók típusához. A MARS-ügynöknek olyan fiókra van szüksége, amely támogatja a BlobStorage kiválasztásakor nem támogatott lapok blobokat. Javasoljuk, hogy a *Storage v2* (*általános célú v2*) lehetőséget válassza a fiók típusaként a Azure Data Box feladathoz tartozó cél Storage-fiók létrehozásakor.

![A fiók típusának kiválasztása a példány részleteiben](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>A MARS-ügynök telepítése és beállítása

* Győződjön meg arról, hogy eltávolítja a MARS-ügynök korábbi telepítését.

* Töltse le [innen](https://aka.ms/azurebackup_agent)a legújabb Mars-ügynököt.

* Futtassa a *MARSAgentInstaller. exe fájlt* , és hajtsa végre a ***csak** azokat a lépéseket, amelyekkel [telepíti és regisztrálja az ügynököt](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) ahhoz a Recovery Services-tárolóhoz, amelyre a biztonsági másolatokat tárolni szeretné.

  >[!NOTE]
  > A Recovery Services-tárolónak ugyanabban az előfizetésben kell lennie, mint a Azure Data Box feladatoknak.

* Miután regisztrálta az ügynököt az Recovery Services-tárolóba, kövesse az alábbi szakasz lépéseit.

## <a name="setup-azure-data-box-devices"></a>Azure Data Box eszköz (ek) beállítása

A megrendelt Azure Data Box SKU-tól függően végezze el az alábbi megfelelő fejezetekben ismertetett lépéseket a MARS-ügynökhöz tartozó Data Box-eszköz (ök) beállításához és előkészítéséhez a kezdeti biztonsági mentési adatok azonosításához és átviteléhez.

### <a name="setup-azure-data-box-disk"></a>Telepítő Azure Data Box Disk

Ha egy vagy több Azure Data Box lemezt (egyenként 8 TB-ig) rendelt, kövesse az itt leírt lépéseket a [Data Box Disk kicsomagolásához, összekapcsolásához és feloldásához](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Előfordulhat, hogy a MARS-ügynökkel rendelkező kiszolgáló nem rendelkezik USB-porttal. Ebben az esetben összekapcsolhatók a Azure Data Box Disk egy másik kiszolgálóval vagy ügyféllel, és elérhetővé tehetik az eszköz gyökerét hálózati megosztásként.

### <a name="setup-azure-data-box"></a>Telepítő Azure Data Box

Ha megrendelt egy Azure Data Box (akár 100 TB-ig), kövesse az itt leírt lépéseket a [Data Box beállításához](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-as-local-system"></a>Azure Data Box csatlakoztatása helyi rendszerszolgáltatásként

A MARS-ügynök a helyi rendszerkörnyezetben működik, ezért ugyanazt a jogosultsági szintet kell megadni ahhoz a csatlakoztatási útvonalhoz, ahol a Azure Data Box csatlakoztatva van. Az alábbi lépésekkel biztosíthatja, hogy a Data Box eszközt helyi rendszerként csatlakoztassa az NFS protokoll használatával:

* Engedélyezze az NFS-ügyfélszolgáltatás szolgáltatást a Windows Serveren (amelyen telepítve van a MARS-ügynök).<br>
  Alternatív forrás meghatározása: *wim: D: \Sources\Install.wim: 4*

* Töltse le a PSExec-t a <https://download.sysinternals.com/files/PSTools.zip>ról a kiszolgálóra a MARS-ügynök telepítésével.

* Nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot a PSExec. exe fájlt tartalmazó könyvtárba az aktuális könyvtárként:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* A fenti parancs eredményeképpen megnyíló parancssori ablak a helyi rendszerkörnyezetben található. Ezen a parancsablakban hajthatja végre az Azure-oldal blob-megosztásának hálózati meghajtóként való csatlakoztatásához szükséges lépéseket a Windows-kiszolgálón.

* Kövesse az alábbi [lépéseket a](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) kiszolgáló és a Mars-ügynök összekapcsolásához a Data Box eszköz NFS-en keresztül, és futtassa a következő parancsot a helyi rendszer parancssorában az Azure-oldal blob-megosztásának csatlakoztatásához:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* A csatlakoztatás után ellenőrizze, hogy a kiszolgálóról fér-e el az X:. Ha igen, folytassa a cikk következő szakaszával.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>A kezdeti biztonsági mentési adatok átvitele Azure Data Box eszközre

* Nyissa meg a **Microsoft Azure Backup** alkalmazást a kiszolgálón.

* Kattintson a **biztonsági mentés időzítése** elemre a **műveletek** ablaktáblán.

    ![Kattintson a biztonsági mentés időzítése lehetőségre](./media/offline-backup-azure-data-box/schedule-backup.png)

* Kövesse a **biztonsági mentés időzítése varázsló** lépéseit.

* **Elemeket adhat hozzá** úgy, hogy az elemek teljes mérete a megrendelt és fogadott [Azure Data Box SKU által támogatott méretkorlát](#backup-data-size-and-supported-data-box-skus) keretén belül legyen.

    ![Elemek hozzáadása a biztonsági mentéshez](./media/offline-backup-azure-data-box/add-items.png)

* Válassza ki a megfelelő biztonsági mentési ütemtervet és adatmegőrzési szabályzatot a fájlok és mappák és a rendszerállapot számára (a rendszerállapot csak Windows-kiszolgálók esetében érvényes, Windows-ügyfelek esetén nem)

* A varázsló **kezdeti biztonsági mentési típus (fájlok és mappák)** képernyőjén válassza az **átvitel lehetőséget Microsoft Azure Data Box lemezek használatával** , és kattintson a **tovább** gombra.

    ![A kezdeti biztonsági mentés típusának kiválasztása](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Jelentkezzen be az Azure-ba, amikor a rendszer felszólítja az Azure-előfizetéshez tulajdonosi hozzáféréssel rendelkező felhasználói hitelesítő adatok használatával. Ha így tesz, a következőhöz hasonló képernyő jelenik meg:

    ![Erőforrások létrehozása és a szükséges engedélyek alkalmazása](./media/offline-backup-azure-data-box/creating-resources.png)

* A MARS-ügynök ezután beolvassa az előfizetésben lévő Data Box feladatokat a "kézbesítés" állapotban.

    ![Databox-feladatok beolvasása az előfizetés-AZONOSÍTÓhoz](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Válassza ki a megfelelő adatmező-sorrendet, amelyhez kicsomagolta, csatlakoztatta és zárolta a Data Box lemezét. Kattintson a **Tovább** gombra.

    ![Data Box rendelés (ek) kiválasztása](./media/offline-backup-azure-data-box/select-databox-order.png)

* Kattintson az **eszköz** észlelése elemre a **Data Box eszköz észlelése** képernyőn. Így a MARS-ügynök beolvassa a helyileg csatlakoztatott Azure Data Box lemezeket, és felismeri azokat az alábbiak szerint:

    ![Eszköz Data Box észlelése](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Ha csatlakoztatta a Azure Data Box hálózati megosztásként (az USB-portok nem rendelkezésre állása miatt, vagy azért, mert az 100 TB-os Data Box eszközt megrendelte és csatlakoztatta), az észlelési művelet először sikertelen lesz, de lehetőséget ad az Data Box eszköz hálózati elérési útjának megadására hown alább:

    ![Adja meg a hálózati elérési utat](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Adja meg a Azure Data Box lemez gyökérkönyvtárának hálózati elérési útját. Ennek a könyvtárnak tartalmaznia kell egy könyvtárat a *PageBlob* néven az alábbi ábrán látható módon:
    >
    >![Azure Data Box lemez gyökérkönyvtára](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Ha például a lemez elérési útja `\\mydomain\myserver\disk1\`, és a *Disk1* a *PageBlob*nevű könyvtárat tartalmazza, akkor a Mars-ügynök varázslóban megadott elérési út `\\mydomain\myserver\disk1\`
    >
    >Ha [Azure Data Box 100 TB-os eszközt állít](#setup-azure-data-box)be, adja meg a következőt az eszköz hálózati elérési útjaként `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Kattintson a **tovább** gombra, majd a következő képernyőn a **Befejezés** gombra kattintva mentse a biztonsági mentési és adatmegőrzési szabályzatot az offline biztonsági mentés konfigurálásával Azure Data Box használatával.

* A következő képernyő megerősíti, hogy a házirend mentése sikeresen megtörtént:

    ![A szabályzat mentése sikerült](./media/offline-backup-azure-data-box/policy-saved.png)

* Kattintson a fenti képernyő **Bezárás** gombjára.

* Kattintson a ***biztonsági mentés most** lehetőségre a Mars-ügynök konzoljának **műveletek** paneljén, majd a varázsló képernyőjén kattintson a **biztonsági mentés** lehetőségre a lent látható módon:

    ![Most biztonsági mentés varázsló](./media/offline-backup-azure-data-box/backup-now.png)

* A MARS-ügynök elindítja a kiválasztott adatAzure Data Box eszközre történő biztonsági mentést. Ez több órát is igénybe vehet, attól függően, hogy hány fájl és kapcsolat sebessége van a kiszolgáló és a MARS-ügynök között, valamint a Azure Data Box Disk.

* Miután az adatbiztonsági másolat elkészült, megjelenik egy képernyő a MARS-ügynökön, amely az alábbihoz hasonló:

    ![A biztonsági mentés állapota látható](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Biztonsági mentés utáni lépések

Ez a szakasz azokat a lépéseket ismerteti, amelyekkel az Azure Data Box Disk sikeres biztonsági másolata sikeresen elvégezhető.

* A [Azure Data Box lemez Azure-ba való szállításához](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)kövesse az ebben a cikkben ismertetett lépéseket. Ha Azure Data Box 100-TB eszközt használt, kövesse az alábbi lépéseket a Azure Data Box Azure-ba való [szállításához](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Figyelje a Data Box feladatot](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) a Azure Portalban. Miután a Azure Data Boxi feladatot befejezte, a MARS-ügynök automatikusan áthelyezi az adatait a Storage-fiókból a Recovery Services-tárolóba a következő ütemezett biztonsági mentés időpontjában. Ezt követően a biztonsági mentési feladatot a "feladatok befejezve" állapotúként jelöli meg, ha a helyreállítási pont sikeresen létrejött.

    >[!NOTE]
    >A MARS-ügynök a házirendek létrehozásakor ütemezett időközönként elindítja a biztonsági mentéseket. Ezek a feladatok azonban a "várakozás Azure Data Box feladat befejeződéséig" jelzőt jelzik, amíg a feladat befejeződik.

* Miután a MARS-ügynök sikeresen létrehozta a kezdeti biztonsági mentéshez tartozó helyreállítási pontot, törölheti a Azure Data Box feladatokhoz társított Storage-fiókot (vagy adott tartalmakat).

## <a name="troubleshooting"></a>Hibaelhárítás

A Microsoft Azure Backup (Mohácsi) ügynök létrehoz egy Azure AD-alkalmazást a bérlőben. Az alkalmazáshoz tanúsítvány szükséges az offline kiindulási házirend konfigurálásakor létrehozott és feltöltött hitelesítéshez. A Azure PowerShell a tanúsítvány Azure AD-alkalmazásba való létrehozásához és feltöltéséhez használjuk.

### <a name="issue"></a>Probléma

A kapcsolat nélküli biztonsági mentés konfigurálásakor probléma merülhet fel, ha a Azure PowerShell parancsmag hibája miatt nem tud több tanúsítványt hozzáadni ugyanahhoz az Azure AD-alkalmazáshoz, amelyet a Mohácsi-ügynök hozott létre. Ez hatással lesz arra, ha a kapcsolat nélküli előkészítési szabályzatot konfigurálta ugyanarra vagy egy másik kiszolgálóra.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Annak ellenőrzése, hogy a problémát az adott alapvető ok okozza-e

A következő lépések egyikének végrehajtásával biztosíthatja, hogy a hiba a fenti probléma miatt megtörténjen:

#### <a name="step-1"></a>1\. lépés

Ellenőrizze, hogy az offline biztonsági mentés konfigurálásakor a következő hibaüzenet jelenik-e meg a Mohácsi-konzolon:

![Nem lehet offline biztonsági mentési szabályzatot létrehozni az aktuális Azure-fiókhoz](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>2\. lépés

* Nyissa meg a **temp** mappát a telepítési útvonalon (alapértelmezett ideiglenes mappa elérési útja: *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Keresse meg a **CBUICurr** fájlt, és nyissa meg a fájlt.

* A **CBUICurr** fájlban görgessen az utolsó sorra, és ellenőrizze, hogy a hiba `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`miatt történt-e.

### <a name="workaround"></a>Áthidaló megoldás

A probléma megoldásához végezze el a következő lépéseket, majd próbálja megismételni a házirend-konfigurációt.

#### <a name="first-step"></a>Első lépés

Jelentkezzen be a (z) a Mohácsi felhasználói felületén megjelenő PowerShellbe egy másik, rendszergazdai hozzáféréssel rendelkező fiókkal azon az előfizetésen, amely az importálási exportálási feladatot létrehozta.

#### <a name="second-step"></a>Második lépés

Ha egy másik kiszolgáló nem rendelkezik kapcsolat nélküli előkészítéssel, és nem függ más kiszolgálótól a `AzureOfflineBackup_<Azure User Id>` alkalmazástól, akkor törölje az alkalmazást **Azure Portal** > **Azure Active Directory** > **Alkalmazásregisztrációk.**

>[!NOTE]
> Ellenőrizze, hogy az alkalmazás `AzureOfflineBackup_<Azure User Id>` nem rendelkezik-e más kapcsolat nélküli előkészítéssel, és nem függ-e az alkalmazástól. Lépjen a **beállítások** > **kulcsok** területen a **nyilvános kulcsok** szakaszban, hogy ne legyenek hozzáadva más nyilvános kulcsok. Tekintse meg a következő képernyőképet a hivatkozáshoz:
>
>![Nyilvános kulcsok](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Harmadik lépés

Az offline biztonsági mentést futtató kiszolgálóról végezze el a következő műveleteket:

1. Nyissa meg a **számítógép-tanúsítvány kezelése** > **személyes** lapot, és keresse meg a nevet a tanúsítványnak `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Válassza ki a fenti tanúsítványt, kattintson a jobb gombbal a **minden feladat** elemre, és exportálja a titkos kulcs nélküli **exportálást** . cer formátumban.

3. Nyissa meg a **2. pontban**említett Azure offline Backup alkalmazást. A **beállítások** > **kulcsok** > **töltse fel a nyilvános kulcsot,** töltse fel a fenti lépésben exportált tanúsítványt.

    ![Nyilvános kulcs feltöltése](./media/offline-backup-azure-data-box/upload-public-key.png)

4. A-kiszolgálón nyissa meg a beállításjegyzéket úgy, hogy beírja a **Regedit parancsot** a Futtatás ablakba.

5. Ugrás a beállításjegyzék- *számítógép \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Kattintson a jobb gombbal a **CloudBackupProvider** elemre, és adjon hozzá egy új karakterlánc-értéket, amelynek neve `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Az Azure User ID beszerzéséhez hajtsa végre az alábbi műveletek egyikét:
    >
    >1. Az Azure-hoz csatlakoztatott PowerShellben futtassa a `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` parancsot.
    > 2. Navigáljon a beállításjegyzék elérési útjára: *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*; Név: *CurrentUserId*

6. Kattintson a jobb gombbal a fenti lépésben hozzáadott sztringre, és válassza a **módosítás**lehetőséget. Az érték mezőben adja meg a **2. pontban** exportált tanúsítvány ujjlenyomatát, majd kattintson az **OK**gombra.

7. Az ujjlenyomat értékének lekéréséhez kattintson duplán a tanúsítványra, majd válassza a **részletek** lapot, és görgessen lefelé, amíg meg nem jelenik az ujjlenyomat mező. Kattintson az **ujjlenyomat** elemre, és másolja az értéket.

    ![Tanúsítvány ujjlenyomatának mezője](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Kérdések

Ha bármilyen kérdése vagy pontosítása van, az esetleges problémákkal kapcsolatban [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
