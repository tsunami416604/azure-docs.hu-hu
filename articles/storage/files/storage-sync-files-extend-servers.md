---
title: Oktatóanyag - A Windows fájlkiszolgálók kiterjesztése az Azure File Sync segítségével | Microsoft dokumentumok
description: Ismerje meg, hogyan bővítheti a Windows fájlkiszolgálókat az Azure File Sync segítségével, az elejétől a végéig.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: eb00234fb7522c763dbaa910bee99cf327bebaf1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77597898"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Oktatóanyag: Windows-fájlkiszolgálók kiterjesztése az Azure File Sync használatával

A cikk bemutatja az azure-kiszolgáló tárolókapacitásának bővítéséhez szükséges alapvető lépéseket. Bár az oktatóanyag a Windows Server t egy Azure virtuális gépként (VM) rendelkezik, ezt a folyamatot általában a helyszíni kiszolgálókon végezze el. Az Azure File Sync saját környezetben történő üzembe helyezésére vonatkozó utasításokat az [Azure File Sync központi telepítése](storage-sync-files-deployment-guide.md) című cikkben találja.

> [!div class="checklist"]
> * A Társzinkronizálási szolgáltatás üzembe helyezése
> * A Windows Server előkészítése az Azure File Sync használatára
> * Az Azure File Sync-ügynök telepítése
> * A Windows Server regisztrálása a tárolószinkronizálási szolgáltatással
> * Szinkronizálási csoport és felhő végpont létrehozása
> * Kiszolgálói végpont létrehozása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="prepare-your-environment"></a>A környezet előkészítése

Ebben az oktatóanyagban az Azure File Sync üzembe helyezése előtt a következőket kell tennie:

- Azure-tárfiók és fájlmegosztás létrehozása
- Windows Server 2016 Datacenter virtuális gép beállítása
- A Windows Server virtuális gép előkészítése az Azure File Sync szolgáltatáshoz

### <a name="create-a-folder-and-txt-file"></a>Mappa és .txt fájl létrehozása

A helyi számítógépen hozzon létre egy új, _FilesToSync_ nevű mappát, és adjon hozzá egy _mytestdoc.txt_ nevű szöveges fájlt. Ezt a fájlt az oktatóanyag későbbi részében tölti fel a fájlmegosztásba.

### <a name="create-a-storage-account"></a>Create a storage account

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Miután üzembe helyezett egy Azure storage-fiókot, hozzon létre egy fájlmegosztást.

1. Az Azure Portalon válassza az **Ugrás az erőforrásra**lehetőséget.
1. Válassza a **Fájlok lehetőséget** a tárfiók ablaktábláján.

    ![Fájlok kijelölése](./media/storage-sync-files-extend-servers/click-files.png)

1. Válassza **a + Fájlmegosztás lehetőséget.**

    ![A Fájlmegosztás hozzáadása gomb kijelölése](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nevezze el az új _fájlmegosztást afsfileshare_néven. Írja be a **kvóta**"1" megadását, majd válassza a **Létrehozás gombot.** A kvóta legfeljebb 5 TiB lehet, de ebben az oktatóanyagban csak 1 GB-ra van szükségünk.

    ![Adjon meg nevet és kvótát az új fájlmegosztás számára](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Jelölje ki az új fájlmegosztást. A fájlmegosztás helyén válassza a **Feltöltés**lehetőséget.

    ![Fájl feltöltése](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Tallózással keresse meg azt a _FilesToSync_ mappát, ahol létrehozta a .txt fájlt, válassza _a mytestdoc.txt_ fájlt, és válassza **a Feltöltés**lehetőséget.

    ![Fájlmegosztás tallózása](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Ezen a ponton létrehozott egy tárfiókot és egy fájlmegosztást egy fájllal. Ezután üzembe helyez egy Azure virtuális gép a Windows Server 2016 Datacenter, hogy képviselje a helyszíni kiszolgáló ebben az oktatóanyagban.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Virtuális gép üzembe helyezése és adatlemez csatolása

1. Nyissa meg az Azure Portalt, és bontsa ki a bal oldali menüt. Válassza **az Erőforrás létrehozása lehetőséget** a bal felső sarokban.
1. Az **Azure Marketplace-erőforrások** listájának keresőmezőjében keresse meg a **Windows Server 2016 Datacenter alkalmazást,** és válassza ki az eredmények között. Válassza a **Létrehozás** elemet.
1. Nyissa meg az **Alapok** lapot. A **Project részletei**csoportban válassza ki az oktatóanyaghoz létrehozott erőforráscsoportot.

   ![Adja meg a virtuális gép alapvető adatait a portálpanelen](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. A **Példány részletei**csoportban adja meg a virtuális gép nevét. Használja például a _myVM-et._
1. Ne módosítsa a **Régió,** **az Elérhetőségi beállítások,** **a Kép**és a Méret alapértelmezett **beállításait.**
1. A **Rendszergazdai fiók** területen adjon meg egy **Felhasználónevet** és egy **Jelszót** a virtuális géphez.
1. A **Bejövő portszabályok csoportban**válassza **a Kijelölt portok engedélyezése lehetőséget,** majd válassza a legördülő menü **RDP (3389)** és **HTTP** parancsát.

1. A virtuális gép létrehozása előtt el kell készíteni az adatlemezt.

   1. Válassza a **Tovább:Lemezek**lehetőséget.

      ![Adatlemezek hozzáadása](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. A **Lemezek** lap **Lemez beállításai**csoportjában hagyja meg az alapértelmezett beállításokat.
   1. A **DATA DISKS**csoportban jelölje **be az Új lemez létrehozása és csatolása**jelölőnégyzetet.

   1. Használja az alapértelmezett beállításokat, kivéve a **Méret (GiB)** lehetőséget, amely az oktatóanyaghoz **1 GB-ra** módosítható.

      ![Adatlemez adatai](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Válassza **az OK gombot.**
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** gombra.

   Az **Értesítések** ikonra választva megtekintheti a **telepítés előrehaladását.** Egy új virtuális gép létrehozása eltarthat néhány percet.

1. A virtuális gép üzembe helyezése befejezése után válassza **az Ugrás az erőforrásra**lehetőséget.

   ![Erőforrás megnyitása](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Ezután csatlakozik a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. Az Azure Portalon válassza **a Csatlakozás** lehetőséget a virtuális gép tulajdonságai lapon.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-sync-files-extend-servers/connect-vm.png)

1. A Csatlakozás a **virtuális géphez** lapon tartsa meg az alapértelmezett beállításokat az **IP-cím** alapján való csatlakozáshoz a 3389-es porton keresztül. Válassza az **RDP-fájl letöltése** parancsot.

   ![Az RDP-fájl letöltése](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Nyissa meg a letöltött RDP-fájlt, és amikor a rendszer kéri, válassza a **Csatlakozás** lehetőséget.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet *localhost\username*néven, írja be a virtuális géphez létrehozott jelszót, majd kattintson az **OK gombra.**

   ![További lehetőségek](./media/storage-sync-files-extend-servers/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat létrehozásához válassza az **Igen** vagy a **Folytatás** lehetőséget.

### <a name="prepare-the-windows-server"></a>A Windows-kiszolgáló előkészítése

A Windows Server 2016 Datacenter kiszolgálóra tiltsa le az Internet Explorer fokozott biztonsági beállításokat. Ez a lépés csak a kiszolgáló kezdeti regisztrációjakor szükséges. A kiszolgáló regisztrációja után újra engedélyezheti.

A Windows Server 2016 Datacenter VM rendszerben a Kiszolgálókezelő automatikusan megnyílik.  Ha a Kiszolgálókezelő alapértelmezés szerint nem nyílik meg, keresse meg a Start menüben.

1. A **Kiszolgálókezelőben**válassza a **Helyi kiszolgáló**lehetőséget.

   ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. A **Tulajdonságok** ablaktáblán jelölje ki az **IE fokozott biztonsági konfigurációja hivatkozást.**  

    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Az **Internet Explorer fokozott biztonsági beállítások** párbeszédpanelen válassza a **Kikapcsolva** lehetőséget a **Rendszergazdák** és a **Felhasználók** elemekre.

    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Most már az adatlemezt is hozzáadhatja a virtuális géphez.

### <a name="add-the-data-disk"></a>Az adatlemez hozzáadása

1. AWindows **Server 2016 Datacenter** virtuális gép ben továbbra is válassza a **Fájlok és tárolási szolgáltatások** > **kötetek** > **lemezei**lehetőséget.

    ![Adatlemez](media/storage-sync-files-extend-servers/your-disk.png)

1. Kattintson a jobb gombbal az Msft virtuális lemez nevű 1 GB-os **lemezre,** és válassza **az Új kötet parancsot.**
1. Végezze el a varázsló lépéseit. Használja az alapértelmezett beállításokat, és jegyezze fel a hozzárendelt meghajtóbetűjelet.
1. Kattintson a **Létrehozás** gombra.
1. Kattintson a **Bezárás** gombra.

   Ezen a ponton a lemez online állapotba került és létrehozott rajta egy kötetet. Nyissa meg a Fájlkezelőt a Windows Server virtuális gépben a legutóbb hozzáadott adatlemez jelenlétének megerősítéséhez.

1. A Virtuálisgép Fájlkezelőjében bontsa ki a **Számítógép csomópontot,** és nyissa meg az új meghajtót. Ebben a példában az F: meghajtóról van szó.
1. Kattintson a jobb gombbal, és válassza az **Új** > **mappa parancsot.** A mappa neve legyen _FilesToSync_.
1. Nyissa meg a **FilesToSync** mappát.
1. Kattintson a jobb gombbal, és válassza az **Új** > **szövegdokumentum parancsot.** A szövegfájl neve legyen _MyTestFile_.

    ![Új szöveges fájl hozzáadása](media/storage-sync-files-extend-servers/new-file.png)

1. Zárja be **a Fájlkezelőt** és **a Kiszolgálókezelőt**.

### <a name="download-the-azure-powershell-module"></a>Az Azure PowerShell-modul letöltése

Ezután a Windows Server 2016 Datacenter VM-ben telepítse az Azure PowerShell-modult a kiszolgálóra.

1. A virtuális gép egy emelt szintű PowerShell-ablak megnyitása.
1. Futtassa az alábbi parancsot:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Ha a NuGet verziója régebbi, mint a 2.8.5.201, a rendszer kéri a NuGet legújabb verziójának letöltését és telepítését.

   Alapértelmezés szerint a PowerShell-galéria nincs konfigurálva a PowerShellGet megbízható adattáraként. A PSGallery első használatakor a következő üzenet jelenik meg:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. A telepítés folytatásához válaszoljon **igennel** vagy **igennel** mindenkinek.

Az `Az` modul az Azure PowerShell-parancsmagok összesített modulja. A modul telepítésével letölti az összes elérhető Azure Resource Manager-modult, és használatra elérhetővé teszi a parancsmagjaikat.

Ezen a ponton beállította a környezetet az oktatóanyaghoz. Készen áll a Storage Sync szolgáltatás üzembe helyezésére.

## <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

Az Azure File Sync üzembe helyezéséhez először helyezzen egy **Storage Sync Service-erőforrást** egy erőforráscsoportba a kiválasztott előfizetéshez. A Storage Sync Szolgáltatás örökli a hozzáférési engedélyeket az előfizetési és erőforráscsoport.

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget,** majd keresse meg az **Azure File Sync (Azure File Sync)** kifejezést.
1. Válassza ki az **Azure File Sync** elemet a keresési eredmények közül.
1. Válassza ki **Létrehozás** elemet a **Társzinkronizálás üzembe helyezése** lap megnyitásához.

   ![Társzinkronizálás üzembe helyezése](media/storage-sync-files-extend-servers/afs-info.png)

   A megnyíló panelen adja meg a következőket:

   | Érték | Leírás |
   | ----- | ----- |
   | **Név** | A Társzinkronizálási szolgáltatás egyedi neve (előfizetésenként).<br><br>Az oktatóanyaghoz használja az _afssyncservice02-t._ |
   | **Előfizetés** | Az oktatóanyaghoz használt Azure-előfizetés. |
   | **Erőforráscsoport** | A Storage Sync szolgáltatást tartalmazó erőforráscsoport.<br><br>Az oktatóanyaghoz használja az _afsresgroup101918-ot._ |
   | **Helyen** | USA keleti régiója |

1. Ha elkészült, válassza ki a **Létrehozás** elemet a **Társzinkronizálási szolgáltatás** üzembe helyezéséhez.
1. Válassza az **Értesítések** lapot > **Ugrás az erőforrásra**.

## <a name="install-the-agent"></a>Az ügynök telepítése

Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással.

1. A **Windows Server 2016 Datacenter** virtuális gépben nyissa meg az **Internet Explorer t.**
1. Nyissa meg a [Microsoft Download Centert](https://go.microsoft.com/fwlink/?linkid=858257). Görgessen le az **Azure File Sync Agent** szakaszhoz, és válassza a **Letöltés**lehetőséget.

   ![A szinkronizálási ügynök letöltése](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Jelölje be a **StorageSyncAgent_V3_WS2016 jelölőnégyzetét. EXE** és válassza a **Tovább**gombot.

   ![Ügynök kiválasztása](media/storage-sync-files-extend-servers/select-agent.png)

1. Válassza **az Engedélyezés a** > **megnyitás****után** > lehetőséget.
1. Ha még nem tette meg, zárja be a PowerShell-ablakot.
1. Fogadja el az alapértelmezett beállításokat a **Társzinkronizálási ügynök telepítő varázslóban**.
1. Válassza az **Install** (Telepítés) lehetőséget.
1. Válassza a **Finish** (Befejezés) elemet.

Sikeresen üzembe helyezte az Azure Sync szolgáltatást és telepítette az ügynököt a Windows Server 2016 Datacenter virtuális gépre. Most regisztrálnia kell a virtuális gépet a Társzinkronizálási szolgáltatásra.

## <a name="register-windows-server"></a>A Windows Server regisztrálása

A Windows-kiszolgáló storage-szinkronizálási szolgáltatással történő regisztrálása bizalmi kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a Tárolószinkronizálási szolgáltatás között. A kiszolgáló csak egy tárolószinkronizálási szolgáltatáshoz regisztrálható. Szinkronizálhat más kiszolgálókkal és az adott Storage Sync Szolgáltatáshoz társított Azure-fájlmegosztásokkal.

A kiszolgálóregisztrációs felhasználói felület automatikusan meg nyílik az Azure File Sync ügynök telepítése után. Ha nem, manuálisan is megnyithatja a fájl helyéről:`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Amikor megnyílik a kiszolgálóregisztrációs felhasználói felület a virtuális gépben, válassza az **OK gombot.**
1. A kezdéshez válassza **a Bejelentkezés** lehetőséget.
1. Jelentkezzen be az Azure-fiók hitelesítő adataival, és válassza **a Bejelentkezés**lehetőséget.
1. Adja meg az alábbi információkat:

   ![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Azure-előfizetés** | Az előfizetés, amely tartalmazza a Társzinkronizálási szolgáltatást ehhez az oktatóanyaghoz. |
   | **Erőforráscsoport** | A Storage Sync szolgáltatást tartalmazó erőforráscsoport. Az oktatóanyaghoz használja az _afsresgroup101918-ot._ |
   | **Társzinkronizálási szolgáltatás** | A storage sync szolgáltatás neve. Az oktatóanyaghoz használja az _afssyncservice02-t._ |

1. A kiszolgáló regisztrációjának befejezéséhez válassza a **Regisztráció** lehetőséget.
1. A regisztrációs folyamat részeként a rendszer további bejelentkezést kér. Jelentkezzen be, és válassza a **Tovább gombot.**
1. Válassza **az OK gombot.**

## <a name="create-a-sync-group"></a>Szinkronizálási csoport létrehozása

A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoportnak egy felhővégpontot kell tartalmaznia, amely egy Azure-fájlmegosztást jelöl. A szinkronizálási csoportnak egy vagy több kiszolgálóvégpontot is tartalmaznia kell. A kiszolgálói végpont egy elérési utat jelöl egy regisztrált kiszolgálón. Szinkronizálási csoport létrehozása:

1. Az [Azure Portalon](https://portal.azure.com/)válassza a **+ Szinkronizálás csoport** a Storage Sync Szolgáltatás. Az oktatóanyaghoz használja az *afssyncservice02-t.*

   ![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Adja meg a következő adatokat egy felhővégpontot létrehozó szinkronizálási csoport létrehozásához:

   | Érték | Leírás |
   | ----- | ----- |
   | **Szinkronizálási csoport neve** | A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név. Használja *az afssyncgroup-ot* ehhez az oktatóanyaghoz.|
   | **Előfizetés** | Az előfizetés, ahova a Társzinkronizálási szolgáltatást telepítette ehhez az oktatóanyaghoz. |
   | **Tárfiók** | Válassza **a Tárfiók kiválasztása**lehetőséget. A megjelenő ablaktáblán válassza ki azt a tárfiókot, amely rendelkezik a létrehozott Azure-fájlmegosztással. Az oktatóanyaghoz használja az *afsstoracct101918* programot. |
   | **Azure fájlmegosztás** | A létrehozott Azure-fájlmegosztás neve. Ehhez az oktatóanyaghoz használja az *afsfileshare-t.* |

1. Kattintson a **Létrehozás** gombra.

Ha kiválasztja a szinkronizálási csoportot, akkor láthatja, hogy most már rendelkezik egy **felhőbeli végponttal**.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele

A kiszolgálóvégpont egy regisztrált kiszolgáló ni adott helyét jelöli. Például egy kiszolgálóköteten lévő mappa. Kiszolgálóvégpont hozzáadása:

1. Jelölje ki az újonnan létrehozott szinkronizálási csoportot, majd válassza **a Kiszolgálóvégpont hozzáadása**lehetőséget.

   ![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. A **Kiszolgálóvégpont hozzáadása** ablaktáblán adja meg a következő adatokat a kiszolgálóvégpont létrehozásához:

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Regisztrált kiszolgáló** | A létrehozott kiszolgáló neve. Az oktatóanyaghoz használja az *afsvm101918 programot.* |
   | **Elérési út** | A létrehozott meghajtó Windows Server elérési útja. Használja *az f:\filestosync programot* ebben az oktatóanyagban. |
   | **Felhőbeli rétegzés** | Hagyja letiltott állapotban az oktatóanyaghoz. |
   | **Szabad terület a köteten** | Ebben az oktatóanyagban üresen hagyjuk. |

1. Kattintson a **Létrehozás** gombra.

A fájlok az Azure-fájlmegosztások és a Windows Server között most már szinkronizálva vannak.

![Az Azure Storage szinkronizálása sikeresen megtörtént](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta az alapvető lépéseket a Windows-kiszolgáló tárolókapacitásának bővítéséhez az Azure File Sync használatával. Az Azure File Sync központi telepítésének tervezését a következő témakörben talál:

> [!div class="nextstepaction"]
> [Azure File Sync üzembe helyezésének megtervezése](./storage-sync-files-planning.md)
