---
title: Oktatóanyag – kiterjesztése az Azure File Sync Windows-fájlkiszolgálók |} A Microsoft Docs
description: Ismerje meg, hogyan bővítheti az Azure File Sync Windows-fájlkiszolgálók elejétől a végéig.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 661df6039948539d6b50b4c8caf8ca7bd9b58730
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321495"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Oktatóanyag: Windows-fájlkiszolgálók kiterjesztése az Azure File Sync használatával

A cikk egy Windows server tárolási kapacitását kiterjesztése az Azure File Sync használatával alapvető lépéseit mutatja be. Az oktatóanyag egy Azure virtuális gépként (VM) a Windows Server szolgáltatásokat, bár a helyszíni kiszolgálók általában teheti ezt a folyamatot. A saját környezetben az Azure File Sync építve annak a [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md) cikk.

> [!div class="checklist"]
> * A Társzinkronizálási szolgáltatás üzembe helyezése
> * A Windows Server előkészítése az Azure File Sync használatára
> * Az Azure File Sync-ügynök telepítése
> * A Storage Sync Service regisztrálja a Windows Server
> * Szinkronizálási csoport és felhő végpont létrehozása
> * Kiszolgálói végpont létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prepare-your-environment"></a>A környezet előkészítése

Ebben az oktatóanyagban az Azure File Sync telepítése előtt, hajtsa végre a következőket kell:

- Hozzon létre egy Azure storage-fiók és -fájlmegosztást
- A Windows Server 2016 Datacenter virtuális gép beállítása
- A Windows Server virtuális gép előkészítése az Azure File Sync

### <a name="create-a-folder-and-txt-file"></a>Mappa és .txt fájl létrehozása

A helyi számítógépen hozzon létre egy új, _FilesToSync_ nevű mappát, és adjon hozzá egy _mytestdoc.txt_ nevű szöveges fájlt. később a fájlmegosztáshoz ezt a fájlt fogja feltölteni ebben az oktatóanyagban.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Miután telepített egy Azure storage-fiókot, hozzon létre fájlmegosztást.

1. Az Azure Portalon válassza ki a **erőforrás megnyitása**.
1. Válassza ki **fájlok** a storage-fiók panelen.

    ![Fájlok kiválasztása](./media/storage-sync-files-extend-servers/click-files.png)

1. Válassza ki **+ fájlmegosztás**.

    ![Válassza ki a fájlmegosztás hozzáadása gombra](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nevezze el az Új fájlmegosztás _afsfileshare_. Adja meg az "1" számára a **kvóta**, majd válassza ki **létrehozás**. A kvóta legfeljebb 5 TiB lehet, de ebben az oktatóanyagban csak 1 GB-ra van szükségünk.

    ![Adjon meg nevet és kvótát az új fájlmegosztás számára](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Válassza ki az Új fájlmegosztás. Válassza ki a fájlmegosztás helyét, **feltöltése**.

    ![Fájl feltöltése](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Keresse meg a _FilesToSync_ mappát, ahol létrehozta a .txt fájlt, jelölje be _mytestdoc.txt_ válassza **feltöltése**.

    ![Fájlmegosztás tallózása](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Ezen a ponton Ezzel létrehozott egy tárfiókot és a egy fájlmegosztást, egy fájlt. Ezután üzembe helyezi a Windows Server 2016 Datacenter, amelyek a helyszíni kiszolgáló ebben az oktatóanyagban egy Azure virtuális Gépen.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Virtuális gép üzembe helyezése és adatlemez csatolása

1. Nyissa meg az Azure Portalon, és bontsa ki a bal oldali menüben. Válasszon **erőforrás létrehozása** a bal felső sarkában.
1. A keresőmezőbe az eszközlista feletti **Azure Marketplace-en** erőforrásokat, keressen **Windows Server 2016 Datacenter** , és jelölje ki az eredményeket. Válassza a **Létrehozás** elemet.
1. Nyissa meg a **alapjai** fülre. A **projektadatok**, válassza ki az ehhez az oktatóanyaghoz létrehozott erőforráscsoportot.

   ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. A **példány részletei**, adja meg a virtuális gép nevét. Például _myVM_.
1. Ne módosítsa az alapértelmezett beállításokat az **régió**, **rendelkezésre állási beállítások**, **kép**, és **mérete**.
1. A **Rendszergazdai fiók** területen adjon meg egy **Felhasználónevet** és egy **Jelszót** a virtuális géphez.
1. Alatt **bejövőport-szabályok**, válassza a **lehetővé teszi a kiválasztott portok** , majd **RDP (3389-es)** és **HTTP** a legördülő menüből.

1. A virtuális gép létrehozása előtt el kell készíteni az adatlemezt.

   1. Válassza ki **tovább: lemezek**.

      ![Adatlemezek hozzáadása](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Az a **lemezek** lap **beállítások lemez**, meghagyhatja az alapértelmezett beállításokat.
   1. A **ADATLEMEZEK**válassza **létrehozása és a egy új lemez csatolása**.

   1. Használhatja az alapértelmezett beállításokat az alábbiakat kivéve **mérete (GiB)**, amely módosíthatja **1 GB-os** ehhez az oktatóanyaghoz.

      ![Adatlemez adatai](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Kattintson az **OK** gombra.
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** gombra.

   Kiválaszthatja a **értesítések** ikonra kattintva tekintse meg a **üzembe helyezés folyamatban**. Új virtuális gép létrehozása néhány percet is igénybe vehet.

1. A virtuális gép üzembe helyezésének befejezése után jelölje ki a **erőforrás megnyitása**.

   ![Erőforrás megnyitása](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Ezután csatlakozzon a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. Az Azure Portalon válassza ki a **Connect** a virtuális gép tulajdonságai lapon.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Az a **csatlakozhat a virtuális gép** lapon, a csatlakozás az alapértelmezett beállításokat tartsa **IP-cím** 3389-es porton keresztül. Válassza az **RDP-fájl letöltése** parancsot.

   ![Az RDP-fájl letöltése](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Nyissa meg a letöltött RDP-fájlt, és válassza ki **Connect** amikor a rendszer kéri.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet, *localhost\username*, adja meg a jelszót, amelyet a virtuális gép, és válassza **OK**.

   ![További lehetőségek](./media/storage-sync-files-extend-servers/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** vagy **Folytatás** a kapcsolat létrehozásához.

### <a name="prepare-the-windows-server"></a>A Windows-kiszolgáló előkészítése

A Windows Server 2016 Datacenter-kiszolgáló tiltsa le az Internet Explorer fokozott biztonsági beállításai. Ez a lépés csak a kiszolgáló kezdeti regisztrációjakor szükséges. A kiszolgáló regisztrációja után újra engedélyezheti.

A Windows Server 2016 Datacenter VM, a Kiszolgálókezelő automatikusan megnyílik.  Ha alapértelmezés szerint a Kiszolgálókezelő nem nyílik meg, keresse meg azt a Fájlkezelőben.

1. A **Kiszolgálókezelő**válassza **helyi kiszolgáló**.

   ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Az a **tulajdonságok** ablaktáblán jelölje ki a hivatkozást **Internet Explorer fokozott biztonsági beállításai**.  

    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Az **Internet Explorer fokozott biztonsági beállítások** párbeszédpanelen válassza a **Kikapcsolva** lehetőséget a **Rendszergazdák** és a **Felhasználók** elemekre.

    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Most már az adatlemezt is hozzáadhatja a virtuális géphez.

### <a name="add-the-data-disk"></a>Az adatlemez hozzáadása

1. Mialatt továbbra is a **Windows Server 2016 Datacenter** virtuális gép, jelölje ki **fájl- és tárolási szolgáltatások** > **kötetek** > **lemezek** .

    ![Adatlemez](media/storage-sync-files-extend-servers/your-disk.png)

1. Kattintson a jobb gombbal az 1 GB-os lemezt nevű **Msft virtuális lemez** válassza **új kötet**.
1. A varázsló befejezése. Az alapértelmezett beállításokat használja, és jegyezze fel a hozzárendelt meghajtóbetűjelet.
1. Kattintson a **Létrehozás** gombra.
1. Válassza ki **Bezárás**.

   Ezen a ponton a lemez online állapotba került és létrehozott rajta egy kötetet. Nyissa meg a Fájlkezelőt a Windows Server virtuális gépen a nemrégiben hozzáadott adatlemez jelenlétét.

1. A Fájlkezelőben a virtuális gép, bontsa ki a **Ez a gép** , és nyissa meg az új meghajtó. Ebben a példában az F: meghajtóról van szó.
1. Kattintson a jobb gombbal, és válassza ki az **Új** > **mappa** lehetőséget. A mappa neve legyen _FilesToSync_.
1. Nyissa meg a **FilesToSync** mappát.
1. Kattintson a jobb gombbal, és válassza ki az **Új** > **Szöveges dokumentum** lehetőséget. A szövegfájl neve legyen _MyTestFile_.

    ![Új szöveges fájl hozzáadása](media/storage-sync-files-extend-servers/new-file.png)

1. Bezárás **fájlkezelő** és **Kiszolgálókezelő**.

### <a name="download-the-azure-powershell-module"></a>Az Azure PowerShell-modul letöltése

Ezután a Windows Server 2016 Datacenter VM, telepítse az Azure PowerShell-modul a kiszolgálón.

1. A virtuális gépen nyissa meg egy rendszergazda jogú PowerShell-ablakban.
1. Futtassa az alábbi parancsot:

   ```powershell
   Install-Module -Name AzureRm
   ```

   > [!NOTE]
   > Ha a NuGet 2.8.5.201-esnél régebbi verziót, töltse le és telepítse a legújabb verzió kéri.

   Alapértelmezés szerint a PowerShell-galéria nincs konfigurálva a PowerShellGet megbízható adattáraként. A PSGallery első használatakor, a következő üzenet jelenik meg:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Válasz **Igen** vagy **Igen, az összeset** a telepítés folytatásához.

Az `AzureRM` modul az Azure PowerShell-parancsmagok összesített modulja. A modul telepítésével letölti az összes elérhető Azure Resource Manager-modult, és használatra elérhetővé teszi a parancsmagjaikat.

Ezen a ponton beállította a környezet-t az oktatóanyaghoz. Készen áll a Storage Sync Service telepítése.

## <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

Az Azure File Sync üzembe helyezéséhez először helyez egy **Társzinkronizálási szolgáltatás** helyezzen egy erőforráscsoportban a kiválasztott előfizetéshez tartozó erőforrás. A Storage Sync Service a hozzáférési engedélyek örökli az előfizetésben és erőforráscsoportban.

1. Az Azure Portalon válassza ki a **erőforrás létrehozása** és keressen **Azure File Sync**.
1. Válassza ki az **Azure File Sync** elemet a keresési eredmények közül.
1. Válassza ki **Létrehozás** elemet a **Társzinkronizálás üzembe helyezése** lap megnyitásához.

   ![Társzinkronizálás üzembe helyezése](media/storage-sync-files-extend-servers/afs-info.png)

   A megnyíló panelen adja meg a következőket:

   | Érték | Leírás |
   | ----- | ----- |
   | **Name (Név)** | A Társzinkronizálási szolgáltatás egyedi neve (előfizetésenként).<br><br>Használat _afssyncservice02_ ehhez az oktatóanyaghoz. |
   | **Előfizetés** | Az Azure-előfizetés ehhez az oktatóanyaghoz használja. |
   | **Erőforráscsoport** | A Storage Sync Service tartalmazó erőforráscsoportot.<br><br>Használat _afsresgroup101918_ ehhez az oktatóanyaghoz. |
   | **Hely** | USA keleti régiója |

1. Ha elkészült, válassza ki a **Létrehozás** elemet a **Társzinkronizálási szolgáltatás** üzembe helyezéséhez.
1. Válassza ki a **értesítések** lap > **erőforrás megnyitása**.

## <a name="install-the-agent"></a>Az ügynök telepítése

Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással.

1. Az a **Windows Server 2016 Datacenter** VM, nyílt **az Internet Explorer**.
1. Nyissa meg a [Microsoft Download Centert](https://go.microsoft.com/fwlink/?linkid=858257). Görgessen le a **Azure File Sync ügynök** szakaszt, és válassza **letöltése**.

   ![A szinkronizálási ügynök letöltése](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Jelölje be a jelölőnégyzetet a **StorageSyncAgent_V3_WS2016. EXE** válassza **tovább**.

   ![Ügynök kiválasztása](media/storage-sync-files-extend-servers/select-agent.png)

1. Válassza ki **egyszeri engedélyezés** > **futtatása** > **nyílt**.
1. Ha még nem tette meg, zárja be a PowerShell-ablakot.
1. Fogadja el az alapértelmezett beállításokat a **Társzinkronizálási ügynök telepítő varázslóban**.
1. Válassza az **Install** (Telepítés) lehetőséget.
1. Válassza a **Finish** (Befejezés) elemet.

Sikeresen üzembe helyezve az Azure-szinkronizálási szolgáltatás és a Windows Server 2016 Datacenter virtuális gép az ügynök telepítve. Most, regisztrálnia kell a virtuális gép a Storage Sync Service.

## <a name="register-windows-server"></a>A Windows Server regisztrálása

A Windows-kiszolgáló regisztrálása a Társzinkronizálási szolgáltatás között a kiszolgáló (vagy fürt) megbízhatósági kapcsolatot hoz létre, és a Storage Sync Service. A kiszolgáló csak egy Társzinkronizálási szolgáltatást kell regisztrálni. Más kiszolgálók és az Azure-fájlmegosztások a Storage Sync Service társított szinkronizálhatja.

A kiszolgáló regisztrációs felhasználói felület automatikusan meg kell az Azure File Sync-ügynök telepítése után. Ha nem, manuálisan is megnyithatja, a fájlok helyéről: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. A kiszolgáló regisztrációs felhasználói felülete megnyitásakor a virtuális gépen válassza **OK**.
1. Válassza ki **bejelentkezési** megkezdéséhez.
1. Jelentkezzen be Azure-fiók hitelesítő adataival, és válassza ki **bejelentkezési**.
1. Adja meg az alábbi információkat:

   ![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Azure előfizetés** | Az előfizetés, amely tartalmazza a Társzinkronizálási szolgáltatást ehhez az oktatóanyaghoz. |
   | **Erőforráscsoport** | A Storage Sync Service tartalmazó erőforráscsoportot. Használat _afsresgroup101918_ ehhez az oktatóanyaghoz. |
   | **Társzinkronizálási szolgáltatás** | A Társzinkronizálási szolgáltatás neve. Használat _afssyncservice02_ ehhez az oktatóanyaghoz. |

1. Válassza ki **regisztrálása** a kiszolgáló regisztráció befejezéséhez.
1. A regisztrációs folyamat részeként a kér egy további bejelentkezésre. Jelentkezzen be, és válassza ki **tovább**.
1. Kattintson az **OK** gombra.

## <a name="create-a-sync-group"></a>Szinkronizálási csoport létrehozása

A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. Szinkronizálási csoport tartalmaznia kell egy felhőbeli végpont, amely Azure-fájlmegosztások. Szinkronizálási csoport is tartalmaznia kell legalább egy kiszolgálói végpontot. A kiszolgálói végpont egy elérési utat jelöl egy regisztrált kiszolgálón. Szinkronizálási csoport létrehozása:

1. Az a [az Azure portal](https://portal.azure.com/)válassza **+ szinkronizálási csoport** , a Storage Sync Service. Használat *afssyncservice02* ehhez az oktatóanyaghoz.

   ![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Adja meg a szinkronizálási csoport létrehozása a felhőbeli végpont a következő információkat:

   | Érték | Leírás |
   | ----- | ----- |
   | **Szinkronizálási csoport neve** | A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név. Használat *afssyncgroup* ehhez az oktatóanyaghoz.|
   | **Előfizetés** | Az előfizetés, ahova a Társzinkronizálási szolgáltatást telepítette ehhez az oktatóanyaghoz. |
   | **Storage-fiók** | Válasszon **válassza ki a tárfiókot**. A megjelenő panelen válassza ki a tárfiókot, amely rendelkezik a létrehozott Azure-fájlmegosztást. Használat *afsstoracct101918* ehhez az oktatóanyaghoz. |
   | **Azure-fájlmegosztás** | A létrehozott Azure-fájlmegosztás neve. Használat *afsfileshare* ehhez az oktatóanyaghoz. |

1. Kattintson a **Létrehozás** gombra.

Ha kiválasztja a szinkronizálási csoportot, akkor láthatja, hogy most már rendelkezik egy **felhőbeli végponttal**.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele

Kiszolgálói végpont egy adott helyen, egy regisztrált kiszolgálón jelöli. Ha például egy kötetet az egyik mappájába. Kiszolgálói végpont hozzáadása:

1. Válassza ki az újonnan létrehozott szinkronizálási csoport majd **kiszolgálói végpont felvétele**.

   ![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Az a **kiszolgálói végpont felvétele** panelen adja meg a kiszolgálói végpont létrehozása a következő információkat:

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Regisztrált kiszolgáló** | A létrehozott kiszolgáló nevét. Használat *afsvm101918* ehhez az oktatóanyaghoz. |
   | **Elérési út** | A Windows Server meghajtó elérési útját a létrehozott. Használat *f:\filestosync* ebben az oktatóanyagban. |
   | **Felhőbeli rétegzés** | Hagyja letiltott állapotban az oktatóanyaghoz. |
   | **Szabad terület a köteten** | Ebben az oktatóanyagban üresen hagyjuk. |

1. Kattintson a **Létrehozás** gombra.

A fájlok az Azure-fájlmegosztások és a Windows Server között most már szinkronizálva vannak.

![Az Azure Storage szinkronizálása sikeresen megtörtént](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, az alapvető lépéseken, a tárolási kapacitás, a Windows Server kiterjesztése az Azure File Sync használatával. Alaposan tekintse meg az Azure File Sync üzembe helyezésének megtervezése lásd:

> [!div class="nextstepaction"]
> [Azure File Sync üzembe helyezésének megtervezése](./storage-sync-files-planning.md)
