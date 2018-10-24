---
title: Oktatóanyag kiterjesztése Windows fájlkiszolgálók az Azure File Sync |} A Microsoft Docs
description: Ismerje meg, hogyan kiterjesztheti Windows-fájlkiszolgálók az Azure File Sync, a indítsa el a befejezéshez.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 4c3ceead792f60ceac7c5eddb64dc4644d662f83
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960567"
---
<!---Customer intent: As a IT Administrator, I want see how to extend Windows file servers with Azure File Sync, so I can evaluate the process for extending storage capacity of my Windows Servers.
--->

# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Oktatóanyag: Kiterjesztése az Azure File Sync Windows-fájlkiszolgálók
A szervezet fájlmegosztásainak, és a tárolási kapacitás, a helyszíni fájlkiszolgálók kiterjesztése az Azure File Sync használhatja. Az Azure File Sync Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át.

Ebben az oktatóanyagban bemutatjuk a tárolási kapacitás, a Windows Server kiterjesztésére vonatkozó alapvető lépéseit az Azure File Sync használatával. Ebben az oktatóanyagban egy Windows Server Azure virtuális Gépet használunk, de a folyamat általában teheti a helyszíni kiszolgálókhoz. Ha már készen áll a saját környezetben az Azure File Sync üzembe helyezése, használja a [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md) . helyette a cikk.

> [!div class="checklist"]
> * A Storage Sync Service telepítése
> * Készítse elő a Windows Serverről az Azure File Sync használata
> * Az Azure File Sync ügynök telepítése
> * Windows-kiszolgálót regisztrálja a Társzinkronizálási szolgáltatás
> * Szinkronizálási csoport és a felhőbeli végpont létrehozása
> * Kiszolgálói végpont létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="prepare-your-environment-for-the-tutorial"></a>A környezet előkészítése az oktatóanyaghoz
Néhány dolgot végre kell bevezetni ehhez az oktatóanyaghoz az Azure File Sync üzembe helyezése előtt. Egy Azure Storage-fiók és a fájl létrehozása és megosztása, akkor egy Windows Server 2016 Datacenter virtuális Gépet hozhat létre, és a kiszolgáló előkészítése az Azure File Sync.

### <a name="create-a-folder-and-txt-file"></a>Hozzon létre egy mappát, és a .txt fájlt

A helyi számítógépen, hozzon létre egy új mappát *FilesToSync* , és adja hozzá a nevű szövegfájlba *mytestdoc.txt*. később a fájlmegosztáshoz ezt a fájlt fogja feltölteni ebben az oktatóanyagban.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Ezután hozzon létre egy fájlmegosztást.

1. Az Azure storage-fiók üzembe helyezésének befejeződése után kattintson a **erőforrás megnyitása**.
1. Kattintson a **fájlok** a storage-fiók panelen.

    ![Kattintson a fájlok](./media/storage-sync-files-extend-servers/click-files.png)

1. Kattintson a **+ fájlmegosztás**.

    ![Kattintson a fájlmegosztás hozzáadása gombra](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nevezze el az Új fájlmegosztás *afsfileshare* és adja meg az "1" a **kvóta**, majd kattintson a **létrehozás**. A kvóta legfeljebb 5 TiB lehet, de csak 1 GB-os kell ehhez az oktatóanyaghoz.

    ![Adjon meg egy nevet és a kvóta az Új fájlmegosztás](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Válassza ki az új fájlmegosztást, majd kattintson a fájlmegosztás helyét, **feltöltése**.

    ![Fájl feltöltése](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Keresse meg a *FilesToSync* mappát, ahol létrehozta a .txt fájlt, jelölje be *mytestdoc.txt* kattintson **feltöltése**.

    ![Keresse meg a fájlmegosztás](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Ezen a ponton Ezzel létrehozott egy Azure Storage-fiókot és a egy fájlmegosztást, egy fájl Azure-ban. Most már a Windows Server 2016 Datacenter, amelyek a helyszíni kiszolgáló ebben az oktatóanyagban az Azure virtuális gép fog létrehozni.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Virtuális gép üzembe helyezése és a egy adatlemez csatolása

1. Ezután bontsa ki a portál bal oldalán, és válassza a **erőforrás létrehozása** az Azure portal bal felső sarkában található.
1. A keresőmezőbe az eszközlista feletti **Azure Marketplace-en** erőforrásokat, keresése és kiválasztása **Windows Server 2016 Datacenter**, majd válassza ki **létrehozás**.
1. Az a **alapjai** lap **projektadatok**, válassza ki az ehhez az oktatóanyaghoz létrehozott erőforráscsoportot.

   ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. A **példány részletei**, adja meg a virtuális gép nevét – például *myVM*.
1. Hagyja bejelölve az alapértelmezett beállításokat az **régió**, **rendelkezésre állási beállítások**, **kép**, és **mérete**.
1. Alatt **rendszergazdai fiók**, adjon meg egy **felhasználónév** és **jelszó** a virtuális gép számára.
1. A **Bejövőport-szabályok** területen válassza a **Kijelölt portok engedélyezése**lehetőséget, majd válassza az **RDP (3389)** és a **HTTP** elemeket a legördülő listából.

   A virtuális Gépet hoz létre, mielőtt egy adatlemez létrehozásához szüksége.

1. Kattintson a **tovább: lemezek**

   ![Adatlemezek hozzáadása](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. Az a **lemezek** lap **beállítások lemez**, meghagyhatja az alapértelmezett beállításokat. 
1. A **ADATLEMEZEK**, kattintson a **létrehozása és a egy új lemez csatolása**.

1. Meghagyhatja az alapértelmezett beállításokat, de úgy módosítsa a **mérete (GiB)** való **1 GB-os** ehhez az oktatóanyaghoz.

   ![Adatok lemez adatai](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Kattintson az **OK** gombra.
1. Kattintson az **Áttekintés + létrehozás** elemre.
1. Kattintson a **Create** (Létrehozás) gombra.

   Kattintson a **értesítések** ikonra kattintva tekintse meg a **üzembe helyezés folyamatban**. Új virtuális gép létrehozása eltarthat néhány percig.

1. A virtuális gép üzembe helyezésének befejeződése után kattintson a **erőforrás megnyitása**.

   ![Erőforrás megnyitása](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   Ezen a ponton létrehozta az új virtuális gép és egy adatlemezt csatolni. Most már szüksége a virtuális Géphez való csatlakozáshoz.

### <a name="connect-to-your-vm"></a>Csatlakozzon a virtuális Géphez

1. Az Azure Portalon kattintson a **Connect** a virtuális gép tulajdonságai lapon.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Az a **csatlakozhat a virtuális gép** lapon, a csatlakozás az alapértelmezett beállításokat tartsa **IP-cím** több mint 3389-es portot, és kattintson a **töltse le az RDP-fájl**.

   ![Az RDP-fájl letöltése](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Csatlakozás** gombra, amikor a rendszer erre kéri.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet, *localhost\username*, adja meg a jelszót, amelyet a virtuális gép, és kattintson **OK**.

   ![További lehetőségek](./media/storage-sync-files-extend-servers/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat létrehozásához kattintson az **Igen** vagy **Folytatás** gombra.

### <a name="prepare-the-windows-server"></a>A Windows-kiszolgáló előkészítése
Az a **Windows Server 2016 Datacenter** kiszolgáló, tiltsa le **az Internet Explorer fokozott biztonsági beállításai**. Ez a lépés akkor szükséges, csak a kezdeti kiszolgálói regisztrációhoz. Engedélyezheti újra azt követően a kiszolgáló regisztrálva van.

Az a **Windows Server 2016 Datacenter** VM, **Kiszolgálókezelő** automatikusan megnyílik.  Ha **Kiszolgálókezelő** Explorerben keresse meg alapértelmezés szerint nem nyílik meg.

1. A **Kiszolgálókezelő** kattintson **helyi kiszolgáló**.

   ![A Server Manager felhasználói felületén bal oldalán a "helyi kiszolgáló"](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Az a **tulajdonságok** subpane, válassza ki a hivatkozást **Internet Explorer fokozott biztonsági beállításai**.  

    ![A Server Manager felhasználói felületén a "Internet Explorer – fokozott biztonsági beállítások" panelen](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Az a **az Internet Explorer fokozott biztonsági beállításai** párbeszédpanelen jelölje ki **ki** a **rendszergazdák** és **felhasználók**:  

    ![Az Internet Explorer fokozott biztonsági beállításai pop-ablak "Kikapcsolva" a kijelölt](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Most már az adatlemezt is hozzáadhat a virtuális géphez.

### <a name="add-the-data-disk"></a>Az adatlemez hozzáadása

1. Mialatt továbbra is a **Windows Server 2016 Datacenter** virtuális Gépet, kattintson a **fájl- és tárolási szolgáltatások** > **kötetek** > **lemezek** .

    ![Adatlemez](media/storage-sync-files-extend-servers/your-disk.png)

1. Kattintson a jobb gombbal az 1 GB-os lemezt nevű **Msft virtuális lemez** kattintson **új kötet**.
1. Fejezze be a varázslót, és az alapértelmezett helyen, figyelembe véve a hozzárendelt meghajtó betűjelét, és kattintson a **létrehozás**.
1. Kattintson a **Bezárás** gombra.

   Ezen a ponton hogy a lemez online állapotba, és létrehozott egy kötetet. Ellenőrizheti, hogy az adatlemez felvétele sikeres volt a virtuális gép Explorer megnyitásával, és megerősíti, hogy jelen-e az új meghajtó.

1. A virtuális gép Explorerben bontsa ki a **Ez a gép** , és kattintson duplán az új meghajtó. Ebben a példában a F: meghajtón.
1. Kattintson a jobb gombbal, és válassza ki **új** > **mappa**. A mappa neve *FilesToSync*.
1. Kattintson duplán a **FilesToSync** mappát.
1. Kattintson a jobb gombbal, és válassza ki **új** > **szöveges dokumentum**. A szövegfájl neve *MyTestFile*.

    ![Adjon hozzá egy új szöveges fájlt](media/storage-sync-files-extend-servers/new-file.png)

1. Bezárás **Explorer** és **Kiszolgálókezelő**.

### <a name="download-the-azurerm-powershell-module"></a>Az AzureRM PowerShell-modul letöltése
Ezután a **Windows Server 2016 Datacenter** virtuális Gépet, telepítse a **AzureRM PowerShell-modul** a kiszolgálón.

1. A virtuális gépen nyisson meg egy rendszergazda jogú PowerShell-ablakban
1. Futtassa az alábbi parancsot:

   ```powershell
   Install-Module -Name AzureRM -AllowClobber
   ```

   > [!NOTE]
   > Ha a NuGet 2.8.5.201-esnél régebbi verzióval rendelkezik, a rendszer a legújabb verzió letöltését és telepítését kéri.

   Alapértelmezés szerint a PowerShell-galéria nincs konfigurálva a PowerShellGet megbízható adattáraként. A PSGallery első használatakor a következő üzenet jelenik meg:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. A telepítés folytatásához válassza a `Yes` vagy a `Yes to All` lehetőséget.

Az `AzureRM` modul az Azure PowerShell-parancsmagok összesített modulja. Telepíti azt letölti az összes elérhető Azure Resource Manager-modulokat, és elérhetővé teszi a parancsmagok használata.

Ezen a ponton a beállítás befejeztével be a környezetet az oktatóanyaghoz, és készen áll üzembe helyezni a **Társzinkronizálási szolgáltatás**.

## <a name="deploy-the-storage-sync-service"></a>A Storage Sync Service telepítése 
Azure File Sync üzembe helyezése kezdődik elhelyezése egy **Társzinkronizálási szolgáltatás** helyezzen egy erőforráscsoportban a kiválasztott előfizetéshez tartozó erőforrás. A Storage Sync Service a hozzáférési engedélyek örökli az előfizetésben és erőforráscsoportban helyezi üzembe azt be.

1. Az Azure Portalon kattintson a **erőforrás létrehozása** és keressen **Azure File Sync**.
1. A keresési eredmények között, válassza ki a **Azure File Sync**.
1. Válassza ki **létrehozás** megnyitásához a **Társzinkronizálási üzembe helyezése** fülre.

   ![Társzinkronizálási fiók üzembe helyezése](media/storage-sync-files-extend-servers/afs-info.png)

   A megnyíló panelen adja meg a következőket:

   | Érték | Leírás |
   | ----- | ----- |
   | **Name (Név)** | A Storage Sync Service egyedi nevet (előfizetésenként).<br><br>Ebben az oktatóanyagban használunk *afssyncservice02*. |
   | **Előfizetés** | Az előfizetés ebben az oktatóanyagban használja. |
   | **Erőforráscsoport** | Az erőforráscsoport, ebben az oktatóanyagban használja.<br><br>Ebben az oktatóanyagban használunk *afsresgroup101918*. |
   | **Hely** | USA keleti régiója |

1. Ha elkészült, válassza ki a **létrehozás** üzembe helyezéséhez a **Társzinkronizálási szolgáltatás**.
1. Kattintson a **értesítések** lap > **erőforrás megnyitása**.

## <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök telepítése
Az Azure File Sync ügynök csomag egy letölthető, amely lehetővé teszi a Windows Serverről az Azure-fájlmegosztások való szinkronizálása megtörténik.

1. Váltson vissza a **Windows Server 2016 Datacenter** virtuális Gépet, és nyissa meg **az Internet Explorer**
1. Nyissa meg a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Görgessen le a **Azure File Sync ügynök** szakaszt, és kattintson a **letöltése**.

   ![A szinkronizálási ügynök letöltése](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Jelölje be a **StorageSyncAgent_V3_WS2016. EXE** kattintson **tovább**.

   ![Válassza ki az ügynök](media/storage-sync-files-extend-servers/select-agent.png)

1. **Egyszeri engedélyezés** > **futtatása** > **nyílt** a fájlt.
1. Ha ezt még nem tette meg, zárja be a PowerShell-ablakban.
1. Elfogadhatja az alapértelmezett beállításokat, az a **tárolási szinkronizálási ügynök telepítővarázslójának**.
1. Kattintson az **Install** (Telepítés) gombra.
1. Kattintson a **Befejezés** gombra.

Sikeresen üzembe helyezve az Azure-szinkronizálási szolgáltatás és az ügynök telepítve a **Windows Server 2016 Datacenter** virtuális Gépet. Most a virtuális Gépet a regisztrálnia kell a **Társzinkronizálási szolgáltatás**.

## <a name="register-windows-server-with-storage-sync-service"></a>Windows-kiszolgálót regisztrálja a Társzinkronizálási szolgáltatás
A Windows Server regisztrálása a Társzinkronizálási szolgáltatás létrehozza a kiszolgáló (vagy fürt) közötti megbízhatósági kapcsolat, és a Storage Sync Service. A kiszolgáló csak egy Társzinkronizálási szolgáltatás regisztrálhatók és szinkronizálhatók más kiszolgálók és az Azure-fájlmegosztások a azonos Storage Sync Service társított.

A kiszolgáló regisztrációs felhasználói felület telepítése után automatikusan megnyitja a **Azure File Sync ügynök**. Ha nem, manuálisan is megnyithatja, a fájlok helyéről: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. A kiszolgáló regisztrációs felhasználói felülete megnyitásakor a virtuális gépen kattintson **OK**.
1. Kattintson a **bejelentkezési** megkezdéséhez.
1. Jelentkezzen be Azure-fiók hitelesítő adataival, és kattintson a **bejelentkezési**.
1. Adja meg az alábbi információkat:

   ![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Azure-előfizetés** | Az előfizetés, amely tartalmazza a Storage Sync Service ehhez az oktatóanyaghoz. |
   | **Erőforráscsoport** | Ebben az oktatóanyagban a Storage Sync Service tartalmazó erőforráscsoportot. Használtunk *afsresgroup101918* oktatóanyag során. |
   | **Társzinkronizálási szolgáltatás** | A Storage Sync Service ebben az oktatóanyagban használt neve. Használtunk *afssyncservice02* oktatóanyag során. |

1. Kattintson a **regisztrálása** a kiszolgáló regisztráció befejezéséhez.
1. A regisztrációs folyamat részeként a rendszer kér egy további bejelentkezésre. Jelentkezzen be, és kattintson a **tovább**.
1. Kattintson az **OK** gombra

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Szinkronizálási csoport és a felhőbeli végpont létrehozása
Szinkronizálási csoport határozza meg, hogy a fájlokat a sync-topológiát. Szinkronizálási csoport tartalmaznia kell egy felhőbeli végpont, amely az Azure-fájlmegosztások és a egy vagy több kiszolgáló végpontot jelenti. Kiszolgálói végpont egy-egy regisztrált kiszolgáló elérési útját jelöli.

1. A szinkronizálási csoport létrehozása a [az Azure portal](https://portal.azure.com/), jelölje be **+ szinkronizálási csoport** , a Storage Sync Service ebben az oktatóanyagban létrehozott. Használtuk *afssyncservice02* ebben az oktatóanyagban a példaként.

   ![Új szinkronizálási csoport létrehozása az Azure Portalon](media/storage-sync-files-extend-servers/add-sync-group.png)

1. A megnyíló panelen adja meg a szinkronizálási csoport létrehozása a felhőbeli végpont a következő információkat:

   | Érték | Leírás |
   | ----- | ----- |
   | **Szinkronizálási csoport neve** | Ez a név a Storage Sync Service egyedinek kell lennie, de bármilyen nevet, amely logikus az Ön számára. Ebben az oktatóanyagban használunk *afssyncgroup*.|
   | **Előfizetés** | Az előfizetés, amelybe telepítette a Storage Sync Service ehhez az oktatóanyaghoz. |
   | **Storage-fiók** |Kattintson a **válassza ki a tárfiókot**. A megjelenő panelen válassza ki a tárfiókot, amely rendelkezik az ehhez az oktatóanyaghoz létrehozott Azure-fájlmegosztást. Használtuk *afsstoracct101918*. |
   | **Azure-fájlmegosztás** | Ebben az oktatóanyagban létrehozott Azure-fájlmegosztás nevét. Használtuk *afsfileshare*. |

1. Kattintson a **Create** (Létrehozás) gombra.

Ha a szinkronizálási csoport, látható, hogy most már rendelkezik ilyennel **felhőbeli végpont**.

## <a name="add-a-server-endpoint"></a>A kiszolgálói végpont felvétele
Kiszolgálói végpont egy adott helyen, egy regisztrált kiszolgálón, például az egyik mappájába, egy kötetet jelöl.

1. Kiszolgálói végpont hozzáadásához válassza ki az újonnan létrehozott szinkronizálási csoportot, majd **kiszolgálói végpont felvétele**.

   ![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Az a **kiszolgálói végpont felvétele** panelen adja meg a kiszolgálói végpont létrehozása a következő információkat:

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Regisztrált kiszolgáló** | Ebben az oktatóanyagban létrehozott kiszolgáló nevét. Használtuk *afsvm101918* ebben az oktatóanyagban ||
   | **Elérési út** | Ebben az oktatóanyagban létrehozott Windows Server elérési útját a meghajtót. Ebben a példában van *f:\filestosync*. |
   | **Felhőbeli rétegzés** | Le van tiltva ehhez az oktatóanyaghoz hagyja. |
   | **Szabad terület a köteten** | Ebben az oktatóanyagban üresen hagyja. |

1. Kattintson a **Create** (Létrehozás) gombra.

Vannak, a fájlok mostantól szinkronban tartani az Azure-fájlmegosztást és a Windows Server.

![Az Azure Storage sikeresen szinkronizálva](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, az alapvető lépéseken kiterjesztése az Azure File Sync használatával Windows Server tárolási kapacitását. Erre a hivatkozásra az alaposabb tekintse meg az Azure File Sync üzembe helyezésének megtervezése.

> [!div class="nextstepaction"]
> [Az Azure File Sync üzembe helyezésének tervezése](./storage-sync-files-planning.md)