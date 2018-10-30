---
title: 'Oktatóanyag: Windows-fájlkiszolgálók kiterjesztése az Azure File Sync használatával | Microsoft Docs'
description: Ismerje meg, hogyan bővítheti ki a Windows-fájlkiszolgálókat az Azure File Sync segítségével, az első lépéstől az utolsóig.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: cc34411cc27870dbd9c707a34ebf34b96c7253dc
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986117"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Oktatóanyag: Windows-fájlkiszolgálók kiterjesztése az Azure File Sync használatával
Ebben az oktatóanyagban bemutatjuk a Windows Server tárterület kapacitásának megnöveléséhez szükséges alapvető lépéseket az Azure File Sync használatával. Bár ebben az oktatóanyagban egy Windows Server Azure VM gépet használunk, a folyamatot általában helyszíni kiszolgálókra végzik el. Ha már készen áll a saját környezetében az Azure File Sync üzembe helyezésére, akkor használja az [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md) cikket.

> [!div class="checklist"]
> * A Társzinkronizálási szolgáltatás üzembe helyezése
> * A Windows Server előkészítése az Azure File Sync használatára
> * Az Azure File Sync-ügynök telepítése
> * Windows-kiszolgáló regisztrálása a Társzinkronizálási szolgáltatással
> * Szinkronizálási csoport és felhő végpont létrehozása
> * Kiszolgálói végpont létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="prepare-your-environment"></a>A környezet előkészítése
Az oktatóanyaghoz néhány dolgot be kell állítania még az Azure File Sync üzembe helyezése előtt. Az Azure Storage-fiók és fájlmegosztás létrehozása mellett létrehozunk egy Windows Server 2016 Datacenter VM gépet és felkészítjük az Azure File Sync használatára.

### <a name="create-a-folder-and-txt-file"></a>Mappa és .txt fájl létrehozása

A helyi számítógépen hozzon létre egy új, *FilesToSync* nevű mappát, és adjon hozzá egy *mytestdoc.txt* nevű szöveges fájlt. ezt a fájl az oktatóanyagban a későbbiekben feltöltjük majd a fájlmegosztáshoz.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Utána a fájlmegosztás létrehozása következik.

1. Amikor az Azure tárfiók üzembe helyezése kész, kattintson az **Erőforrás megnyitása** lehetőségre.
1. A tárfiók panelen kattintson a **Fájlok**-ra.

    ![Kattintson a Fájlokra](./media/storage-sync-files-extend-servers/click-files.png)

1. Kattintson **+ Fájlmegosztás** lehetőségre.

    ![Kattintson a fájlmegosztás hozzáadása gombra](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nevezze el az új *afsfileshare* fájlmegosztást, a **kvóta** értékének "1"-et adjon meg, majd kattintson a **Létrehozás** gombra. A kvóta legfeljebb 5 TiB lehet, de ebben az oktatóanyagban csak 1 GB-ra van szükségünk.

    ![Adjon meg nevet és kvótát az új fájlmegosztás számára](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Válassza ki az új fájlmegosztást, majd a fájlmegosztás helyénél kattintson a **Feltöltésre**.

    ![Fájl feltöltése](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Menjen ahhoz a *FilesToSync* mappához, ahol a .txt fájlt létrehozta és a *mytestdoc.txt* kiválasztása után kattintson a **Feltöltésre**.

    ![Fájlmegosztás tallózása](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Ezen a ponton már létrehozta az Azure Storage-fiókot és az Azure-ban a fájlmegosztást benne egy fájllal. Most pedig következik az Azure VM létrehozása a Windows Server 2016 Datacenterrel, ami a helyi kiszolgálót fogja jelölni ebben az oktatóanyagban.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Virtuális gép üzembe helyezése és adatlemez csatolása

1. Ezután bontsa ki a menüt a portál bal oldalán, és válassza ki az **Erőforrás létrehozása** lehetőséget az Azure portál bal felső sarkában.
1. Az **Azure Marketplace**-erőforrások fölött lévő keresési mezőben keressen a **Windows Server 2016 Datacenter** elemre, majd válassza a **Létrehozás** lehetőséget.
1. Az **Alapadatok** lapon a **Projektadatok** rész alatt, válassza ki az ehhez az oktatóanyaghoz létrehozott erőforráscsoportot.

   ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. A **Példány adatok** részen adja meg a virtuális gép nevét, például *myVM*.
1. Hagyja bejelölve az alapértelmezett beállításokat a **Régió**, **Rendelkezésre állási beállítások**, **Kép** és **Méret** mezőkben.
1. A **Rendszergazdai fiók** területen adjon meg egy **Felhasználónevet** és egy **Jelszót** a virtuális géphez.
1. A **Bejövőport-szabályok** területen válassza a **Kijelölt portok engedélyezése**lehetőséget, majd válassza az **RDP (3389)** és a **HTTP** elemeket a legördülő listából.

   A virtuális gép létrehozása előtt el kell készíteni az adatlemezt.

1. Kattintson a **Tovább: Lemezek** elemre

   ![Adatlemezek hozzáadása](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. A **Lemezek** lapon a **Lemez beállítások** területen hagyja meg az alapértelmezett beállításokat.
1. Az **ADATLEMEZEK** részen kattintson az **Új lemez létrehozása és csatolása** lehetőségre.

1. Hagyja meg az alapértelmezett beállításokat, csak a **Méretet (GiB)** állítsa be **1 GB-ra** ehhez az oktatóanyaghoz.

   ![Adatlemez adatai](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Kattintson az **OK** gombra.
1. Kattintson az **Áttekintés + létrehozás** elemre.
1. Kattintson a **Create** (Létrehozás) gombra.

   Az **Értesítések** ikonra kattintva figyelemmel kísérheti majd az **Üzembe helyezés folyamatát**. Az új virtuális gép létrehozása eltarthat pár percig.

1. A virtuális gép üzembe helyezésének befejeződése után kattintson az **Erőforrás megnyitása** gombra.

   ![Erőforrás megnyitása](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Most pedig csatlakoznunk kell a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. Az Azure portálon kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságainak lapján.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-sync-files-extend-servers/connect-vm.png)

1. A **Csatlakozás virtuális géphez** oldalon tartsa meg az alapértelmezett beállításokat az **IP-cím** alapján a 3389-es porton keresztül való csatlakozáshoz, és kattintson az **RDP-fájl letöltése** parancsra.

   ![Az RDP-fájl letöltése](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Csatlakozás** gombra, amikor a rendszer erre kéri.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet *localhost\felhasználónév* formátumban, és adja meg a virtuális géphez létrehozott jelszót, majd kattintson az **OK** gombra.

   ![További lehetőségek](./media/storage-sync-files-extend-servers/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat létrehozásához kattintson az **Igen** vagy **Folytatás** gombra.

### <a name="prepare-the-windows-server"></a>A Windows Server előkészítése
A **Windows Server 2016 Datacenter** kiszolgálóra tiltsa le az **Internet Explorer fokozott biztonsági beállításokat**. Ez a lépés csak a kiszolgáló kezdeti regisztrációjakor szükséges. A kiszolgáló regisztrációja után újra engedélyezheti.

A **Windows Server 2016 Datacenter** virtuális gépben a **Kiszolgálókezelő** automatikusan megnyílik.  Ha a **Kiszolgálókezelő** nem nyílik meg magától, keresse meg az Explorerben.

1. A **Kiszolgálókezelőben** kattintson a **Helyi kiszolgáló** elemre.

   ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. A **Tulajdonságok** lapon, válassza ki az **Internet Explorer fokozott biztonsági beállításai** hivatkozást.  

    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Az **Internet Explorer fokozott biztonsági beállítások** párbeszédpanelen válassza a **Kikapcsolva** lehetőséget a **Rendszergazdák** és a **Felhasználók** elemekre.

    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Most már az adatlemezt is hozzáadhatja a virtuális géphez.

### <a name="add-the-data-disk"></a>Az adatlemez hozzáadása

1. Mialatt továbbra is a **Windows Server 2016 Datacenter** virtuális gépen belül marad, kattintson a **Fájl- és tárolási szolgáltatások** > **Kötetek** > **Lemezek**  menüpontra.

    ![Adatlemez](media/storage-sync-files-extend-servers/your-disk.png)

1. Kattintson a jobb gombbal az **Msft virtuális lemez** nevű 1 GB-os lemezre és kattintson **Új kötetre**.
1. Vigye végig a varázslót az alapértelmezések meghagyásával, jegyezze meg a hozzárendelt meghajtó betűjelét, majd kattintson a **Létrehozásra**.
1. Kattintson a **Bezárás** gombra.

   Ezen a ponton a lemez online állapotba került és létrehozott rajta egy kötetet. Ellenőrizheti, hogy az adatlemez felvétele sikeres volt-e, ha a virtuális gépen megnyitja az Explorert, és megnézi, hogy látható-e az új meghajtó.

1. A virtuális gépen az Explorerben bontsa ki az **Ez a gép** elemet, és kattintson duplán az új meghajtóra. Ebben a példában az F: meghajtóról van szó.
1. Kattintson a jobb gombbal, és válassza ki az **Új** > **mappa** lehetőséget. A mappa neve legyen *FilesToSync*.
1. Kattintson duplán a **FilesToSync** mappán.
1. Kattintson a jobb gombbal, és válassza ki az **Új** > **Szöveges dokumentum** lehetőséget. A szövegfájl neve legyen *MyTestFile*.

    ![Új szöveges fájl hozzáadása](media/storage-sync-files-extend-servers/new-file.png)

1. Zárja be az **Explorert** és a **Kiszolgálókezelőt**.

### <a name="download-the-azurerm-powershell-module"></a>Az AzureRM PowerShell-modul letöltése
Ezután a **Windows Server 2016 Datacenter** virtuális gépen telepítse az **AzureRM PowerShell-modult** a kiszolgálóra.

1. A virtuális gépen nyisson meg egy rendszergazda jogú PowerShell-ablakot
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

Az `AzureRM` modul az Azure PowerShell-parancsmagok összesített modulja. A modul telepítésével letölti az összes elérhető Azure Resource Manager-modult, és használatra elérhetővé teszi a parancsmagjaikat.

Ezen a ponton elkészült az oktatóanyaghoz szükséges környezeti beállításokkal és készen áll a **Társzinkronizálási szolgáltatás** üzembe helyezésére.

## <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése 
Az Azure File Sync üzembe helyezésének első lépése egy **Társzinkronizálási szolgáltatás** elhelyezése a kijelölt előfizetéshez tartozó erőforráscsoportba. A Társzinkronizálási szolgáltatás örökli a telepítés helyén érvényben levő előfizetés és erőforráscsoporttól a hozzáférési engedélyeket.

1. Az Azure portálon kattintson az **Erőforrás létrehozása** elemre, majd keresse az **Azure File Sync** elemet.
1. Válassza ki az **Azure File Sync** elemet a keresési eredmények közül.
1. Válassza ki **Létrehozás** elemet a **Társzinkronizálás üzembe helyezése** lap megnyitásához.

   ![Társzinkronizálás üzembe helyezése](media/storage-sync-files-extend-servers/afs-info.png)

   A megnyíló panelen adja meg a következőket:

   | Érték | Leírás |
   | ----- | ----- |
   | **Name (Név)** | A Társzinkronizálási szolgáltatás egyedi neve (előfizetésenként).<br><br>Ebben az oktatóanyagban az *afssyncservice02* nevet használjuk. |
   | **Előfizetés** | Az ehhez az oktatóanyaghoz használt előfizetés. |
   | **Erőforráscsoport** | Az ehhez az oktatóanyaghoz használt erőforráscsoport.<br><br>Ebben az oktatóanyagban az *afsresgroup101918* nevet használjuk. |
   | **Hely** | USA keleti régiója |

1. Ha elkészült, válassza ki a **Létrehozás** elemet a **Társzinkronizálási szolgáltatás** üzembe helyezéséhez.
1. Kattintson az **Értesítések** lapon az **Erőforrás megnyitása** elemre.

## <a name="install-the-agent"></a>Az ügynök telepítése
Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással.

1. Váltson vissza a **Windows Server 2016 Datacenter** virtuális gépre és nyissa meg **az Internet Explorert**.
1. Nyissa meg a [Microsoft Download Centert](https://go.microsoft.com/fwlink/?linkid=858257). Görgessen le az **Azure File Sync ügynök** szakaszra és kattintson a **Letöltésre**.

   ![A szinkronizálási ügynök letöltése](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Jelölje be a **StorageSyncAgent_V3_WS2016. EXE** jelölőnégyzetet és kattintson **Tovább** gombra.

   ![Ügynök kiválasztása](media/storage-sync-files-extend-servers/select-agent.png)

1. **Egyszeri engedélyezés** > **Futtatás** > **Megnyitás** a fájlra.
1. Ha még nem tette meg, zárja be a PowerShell-ablakot.
1. Fogadja el az alapértelmezett beállításokat a **Társzinkronizálási ügynök telepítő varázslóban**.
1. Kattintson az **Install** (Telepítés) gombra.
1. Kattintson a **Befejezés** gombra.

Sikeresen üzembe helyezte az Azure Sync szolgáltatást és telepítette az ügynököt a **Windows Server 2016 Datacenter** virtuális gépre. Most regisztrálnia kell a virtuális gépet a **Társzinkronizálási szolgáltatásra**.

## <a name="register-windows-server"></a>A Windows Server regisztrálása
A Windows Server regisztrálásával a Társzinkronizálási szolgáltatásra megbízhatósági kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a Társzinkronizálási szolgáltatás között. A kiszolgáló csak egy Társzinkronizálási szolgáltatásra regisztrálható és az ugyanahhoz a Társzinkronizálási szolgáltatáshoz társított kiszolgálókkal és Azure-fájlmegosztásokkal képes szinkronizálni.

Az **Azure File Sync ügynök** telepítése után automatikusan megnyílik a kiszolgáló regisztrációs felhasználói felület. Ha nem, akkor manuálisan is megnyithatja, a fájl helye: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Amikor a kiszolgáló regisztrációs felhasználói felülete megnyílik a virtuális gépen, kattintson az **OK** gombra.
1. Kattintson a **Bejelentkezésre** a kezdéshez.
1. Jelentkezzen be Azure-fiók hitelesítő adataival, és kattintson a **Bejelentkezésre**.
1. Adja meg az alábbi információkat:

   ![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Azure előfizetés** | Az előfizetés, amely tartalmazza a Társzinkronizálási szolgáltatást ehhez az oktatóanyaghoz. |
   | **Erőforráscsoport** | Az erőforráscsoport, amely tartalmazza a Társzinkronizálási szolgáltatást ehhez az oktatóanyaghoz. Az oktatóanyagban az *afsresgroup101918* nevet használtuk. |
   | **Társzinkronizálási szolgáltatás** | A Társzinkronizálási szolgáltatásnak az ebben az oktatóanyagban használt neve. Az oktatóanyagban az *afssyncservice02* nevet használtuk. |

1. A kiszolgáló regisztráció befejezéséhez kattintson a **Regisztráció** elemre.
1. A regisztrációs folyamat részeként a rendszer újabb bejelentkezésre kéri fel. Jelentkezzen be, és kattintson a **Tovább** gombra.
1. Kattintson az **OK** gombra.

## <a name="create-a-sync-group"></a>Szinkronizálási csoport létrehozása
A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoportnak tartalmaznia kell egy felhőbeli végpontot, amely Azure-fájlmegosztást és egy vagy több kiszolgáló végpontot jelöl. A kiszolgálói végpont egy elérési utat jelöl egy regisztrált kiszolgálón.

1. Szinkronizálási csoport létrehozásához az [Azure portálon](https://portal.azure.com/) jelölje be a **+ szinkronizálási csoportot** az oktatóanyaghoz létrehozott Társzinkronizálási szolgáltatásban. Az oktatóanyagban példaként az *afssyncservice02*-t használtuk.

   ![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-extend-servers/add-sync-group.png)

1. A megnyíló panelen adja meg a következő információkat a szinkronizálási csoport létrehozásához egy felhőbeli végponttal:

   | Érték | Leírás |
   | ----- | ----- |
   | **Szinkronizálási csoport neve** | A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név. Ebben az oktatóanyagban az *afssyncgroup* nevet használjuk.|
   | **Előfizetés** | Az előfizetés, ahova a Társzinkronizálási szolgáltatást telepítette ehhez az oktatóanyaghoz. |
   | **Storage-fiók** |Kattintson a **Tárfiók kiválasztása** elemre. A megjelenő panelen válassza ki a tárfiókot, amelyhez az ehhez az oktatóanyaghoz létrehozott Azure-fájlmegosztás tartozik. Mi az *afsstoracct101918* nevet használtuk. |
   | **Azure-fájlmegosztás** | Az oktatóanyaghoz létrehozott Azure-fájlmegosztás neve. Mi az *afsfileshare* nevet használtuk. |

1. Kattintson a **Create** (Létrehozás) gombra.

Ha kiválasztja a szinkronizálási csoportot, akkor láthatja, hogy most már rendelkezik egy **felhőbeli végponttal**.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele
A kiszolgálói végpont a regisztrált kiszolgálón egy konkrét helyet jelöl, például egy mappát egy kiszolgálói köteten.

1. Kiszolgálói végpont hozzáadásához válassza ki az újonnan létrehozott szinkronizálási csoportot, majd válassza a **Kiszolgálói végpont felvétele** elemet.

   ![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. A **Kiszolgálói végpont felvétele** panelen adja meg a következő információkat a kiszolgálói végpont létrehozásához:

   | | |
   | ----- | ----- |
   | Érték | Leírás |
   | **Regisztrált kiszolgáló** | Az oktatóanyaghoz létrehozott kiszolgáló neve. Mi az *afsvm101918* nevet használtuk ebben az oktatóanyagban |
   | **Elérési út** | Az oktatóanyaghoz létrehozott meghajtó Windows Server elérési útja. A példánkban *f:\filestosync*. |
   | **Felhőbeli rétegzés** | Hagyja letiltott állapotban az oktatóanyaghoz. |
   | **Szabad terület a köteten** | Ebben az oktatóanyagban üresen hagyjuk. |

1. Kattintson a **Create** (Létrehozás) gombra.

A fájlok az Azure-fájlmegosztások és a Windows Server között most már szinkronizálva vannak.

![Az Azure Storage szinkronizálása sikeresen megtörtént](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta a Windows Server tárterület kapacitásának megnöveléséhez szükséges alapvető lépéseket az Azure File Sync használatával. Erre a hivatkozásra kattintva részletesebben megtekintheti az Azure File Sync üzembe helyezésének megtervezését.

> [!div class="nextstepaction"]
> [Azure File Sync üzembe helyezésének megtervezése](./storage-sync-files-planning.md)