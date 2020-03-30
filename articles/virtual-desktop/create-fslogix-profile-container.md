---
title: FSLogix profiltárolók NetApp Windows Virtual Desktop - Azure
description: FSLogix profiltároló létrehozása az Azure NetApp-fájlokkal a Windows virtual desktop ban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 272188b50fe59435031a4a2fb9c252f3f358bb6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535731"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>FSLogix-profiltároló létrehozása gazdakészlethez az Azure NetApp-fájlok használatával

Javasoljuk, hogy az FSLogix profiltárolókat használja felhasználói profilmegoldásként a [Windows virtuális asztali szolgáltatáshoz.](overview.md) Az FSLogix profiltárolók egy teljes felhasználói profilt tárolnak egyetlen tárolóban, és úgy tervezték, hogy profilokat barangoljon nem állandó távoli számítástechnikai környezetekben, például a Windows Virtual Desktop környezetben. Bejelentkezéskor a tároló dinamikusan csatlakozik a számítógépes környezethez egy helyileg támogatott virtuális merevlemez (VHD) és Hyper-V virtuális merevlemez (VHDX) használatával. Ezek a fejlett szűrő-illesztőprogram-technológiák lehetővé teszik, hogy a felhasználói profil azonnal elérhető legyen, és pontosan úgy jelenjen meg a rendszerben, mint egy helyi felhasználói profil. Az FSLogix profiltárolókról az [FSLogix-profiltárolók és az Azure-fájlok további információiban olvashat bővebben.](fslogix-containers-azure-files.md)

FSLogix profiltárolókat hozhat létre [az Azure NetApp Files](https://azure.microsoft.com/services/netapp/)használatával, amely egy könnyen használható Azure natív platformszolgáltatás, amely segítségével az ügyfelek gyorsan és megbízhatóan biztosíthatnak nagyvállalati szintű SMB-köteteket windowsos virtuális asztali környezetükhöz. Ha többet szeretne megtudni az Azure NetApp-fájlokról, olvassa el [a Mi az Azure NetApp-fájlok?](../azure-netapp-files/azure-netapp-files-introduction.md)

Ez az útmutató bemutatja, hogyan állíthat be egy Azure NetApp Files-fiókot, és hogyan hozhat létre FSLogix profiltárolókat a Windows virtuális asztalon.

Ez a cikk feltételezi, hogy már rendelkezik [gazdagépkészletek](create-host-pools-azure-marketplace.md) beállítással, és a Windows virtuális asztal környezetben egy vagy több bérlőbe van csoportosítva. A bérlők beállításáról a [Bérlő létrehozása a Windows virtuális asztalon](tenant-setup-azure-active-directory.md) és a Technikai közösség [blogbejegyzéscímű témakörben olvashat.](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)

Az útmutatóban található utasítások kifejezetten a Windows virtuális asztali felhasználók számára találhatók. Ha általánosabb útmutatást szeretne az Azure NetApp-fájlok beállításához és az FSLogix-profiltárolók windowsos virtuális asztalon kívüli létrehozásához, olvassa el az [Azure NetApp-fájlok beállítása és nfs-kötetek rövid útmutatójának létrehozását.](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)

>[!NOTE]
>Ez a cikk nem ismerteti az Azure NetApp-fájlok megosztáshoz való hozzáférés biztosításával kapcsolatos gyakorlati tanácsokat.

>[!NOTE]
>Ha az Azure-beli FSLogix-profiltároló különböző tárolási lehetőségeivel kapcsolatos összehasonlító anyagokat keres, olvassa el [az FSLogix-profiltárolók tárolási beállításai című témakört.](store-fslogix-profile.md)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt FSLogix profiltárolót hozhatna létre egy gazdakészlethez, a következőket kell tennie:

- A Windows virtuális asztal beállítása és konfigurálása
- Windows virtuális asztal gazdakészletének kiépítése
- [Az Azure NetApp Files-előfizetés engedélyezése](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Az Azure NetApp Files-fiók beállítása

A kezdéshez be kell állítania egy Azure NetApp Files-fiókot.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Ellenőrizze, hogy fiókja rendelkezik-e közreműködői vagy rendszergazdai engedélyekkel.

2. Válassza ki az **Azure Cloud Shell ikont** a keresősáv jobb oldalán az Azure Cloud Shell megnyitásához.

3. Miután az Azure Cloud Shell meg van nyitva, válassza a **PowerShell**lehetőséget.

4. Ha ez az első alkalom az Azure Cloud Shell használatával, hozzon létre egy tárfiókot ugyanabban az előfizetésben, amelyben megtartja az Azure NetApp-fájlokat és a Windows virtuális asztalt.

   ![A tárfiók ablaka, ahol a tárház létrehozása gomb pirossal van kiemelve.](media/create-storage-button.png)

5. Miután az Azure Cloud Shell betöltődik, futtassa a következő két parancsmagokat.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Az ablak bal oldalán válassza a **Minden szolgáltatás lehetőséget.** Írja be az **Azure NetApp-fájlokat** a menü tetején megjelenő keresőmezőbe.

   ![Képernyőkép a "Azure NetApp Files" kifejezést beírásról a Minden szolgáltatás keresőmezőbe. A keresési eredmények az Azure NetApp Files erőforrást jelenítik meg.](media/azure-netapp-files-search-box.png)


7. A keresési eredmények között válassza az **Azure NetApp-fájlok** elemet, majd a **Létrehozás gombot.**

8. Kattintson a **Hozzáadás** gombra.
9. Amikor megnyílik az **Új NetApp-fiók** lap, adja meg a következő értékeket:

    - A **Név**mezőbe írja be netapp-fiókjának nevét.
    - **Előfizetés esetén**válassza ki a 4.
    - **Az Erőforráscsoport csoportban**válasszon ki egy meglévő erőforráscsoportot a legördülő menüből, vagy hozzon létre egy újat az **Új létrehozása**lehetőség kiválasztásával.
    - A **Hely menüben**válassza ki a NetApp-fiók régióját a legördülő menüből. Ennek a régiónak meg kell egyeznie a munkamenetgazda virtuális gépeivel.

   >[!NOTE]
   >Az Azure NetApp Files jelenleg nem támogatja a kötetek régiók közötti csatlakoztatását.

10. Ha végeztél, válaszd a **Létrehozás** lehetőséget a NetApp-fiókod létrehozásához.

## <a name="create-a-capacity-pool"></a>Kapacitáskészlet létrehozása

Ezután hozzon létre egy új kapacitáskészletet: 

1. Nyissa meg az Azure NetApp Files menüt, és válassza ki az új fiókot.
2. A fiók menüben válassza **a Kapacitáskészletek lehetőséget a** Tárolási szolgáltatás csoportban.
3. Válassza **a Készlet hozzáadása**lehetőséget.
4. Az **Új kapacitáskészlet** lap megnyitásakor adja meg a következő értékeket:

    - A **Név mezőbe**írja be az új kapacitáskészlet nevét.
    - A **Szolgáltatási szint menüben**válassza ki a kívánt értéket a legördülő menüből. A legtöbb környezetben a **Premium** ot ajánljuk.
       >[!NOTE]
       >A Prémium beállítás biztosítja a prémium szintű szolgáltatásszinthez elérhető minimális átviteli sebességet, amely 256 Mb/s. Előfordulhat, hogy módosítania kell ezt az átviteli mertét egy éles környezethez. A végső átviteli átmenő erő az [átviteli határértékekben](../azure-netapp-files/azure-netapp-files-service-levels.md)leírt kapcsolaton alapul.
    - A **Méret (TiB)** esetében adja meg az igényeinek leginkább megfelelő kapacitáskészlet-méretet. A legkisebb méret 4 TiB.

5. Amikor végzett, válassza az **OK** gombot.

## <a name="join-an-active-directory-connection"></a>Csatlakozás Active Directory-kapcsolathoz

Ezt követően csatlakoznia kell egy Active Directory-kapcsolathoz.

1. Válassza az **Active Directory-kapcsolatok lehetőséget** a lap bal oldalán található menüben, majd a **Csatlakozás gombra** kattintva nyissa meg az **Active Directoryhoz való csatlakozás** lapot.

   ![Képernyőkép az Active Directory-kapcsolatokhoz való csatlakozás menüről.](media/active-directory-connections-menu.png)

2. A csatlakozáshoz írja be a következő értékeket az **Active Directoryhoz való csatlakozás** lapra:

    - Az **elsődleges DNS**mezőben adja meg a környezetében lévő DNS-kiszolgáló IP-címét, amely feloldhatja a tartománynevet.
    - **Domain esetén**adja meg a teljesen minősített tartománynevet (FQDN).
    - Az **SMB-kiszolgáló (számítógépfiók) előtag**hoz adja meg a számítógépfiók nevéhez hozzáfűző karakterláncot.
    - A **Felhasználónév**mezőbe írja be annak a fióknak a nevét, amely rendelkezik a tartományhoz való csatlakozás végrehajtására vonatkozó engedélyekkel.
    - A **Jelszó**mezőbe írja be a fiók jelszavát.

  >[!NOTE]
  >Ajánlott meggyőződni arról, hogy az [Active Directory-kapcsolathoz való csatlakozás](create-fslogix-profile-container.md#join-an-active-directory-connection) kor létrehozott számítógépfiók megjelent-e a tartományvezérlőn a **Számítógépek** vagy a vállalat megfelelő **szervezeti egysége**területen.

## <a name="create-a-new-volume"></a>Új kötet létrehozása

Ezután új kötetet kell létrehoznia.

1. Válassza **a Kötetek**lehetőséget, majd a **Kötet hozzáadása**lehetőséget.

2. Amikor megnyílik a **Kötet létrehozása** lap, írja be a következő értékeket:

    - A **Kötet neve**mezőbe írja be az új kötet nevét.
    - A **Kapacitáskészlet lapon**válassza ki az imént létrehozott kapacitáskészletet a legördülő menüből.
    - A **Kvóta (GiB)** mezőbe írja be a környezetének megfelelő kötetméretet.
    - **Virtuális hálózat**esetén válasszon ki egy meglévő virtuális hálózatot, amely kapcsolatban van a tartományvezérlővel a legördülő menüből.
    - Az **Alhálózat csoportban**válassza **az Új létrehozása lehetőséget.** Ne feledje, hogy ez az alhálózat delegált lesz az Azure NetApp-fájlok ban.

3.  Válassza a **Tovább: Protokoll lehetőséget \> ** a Protokoll lap megnyitásához és a kötetelérési paraméterek konfigurálásához.

## <a name="configure-volume-access-parameters"></a>Kötet-hozzáférési paraméterek konfigurálása

A kötet létrehozása után konfigurálja a kötethozzáférési paramétereket.

1.  Válassza az **SMB** protokolltípust.
2.  Az Active **Directory** legördülő menüKonfiguráció parancsában válassza ki ugyanazt a könyvtárat, amelyhez eredetileg csatlakozott [az Active Directory-kapcsolathoz való csatlakozás](create-fslogix-profile-container.md#join-an-active-directory-connection)kor. Ne feledje, hogy előfizetésenként legfeljebb egy Active Directory lehet.
3.  A **Megosztási név** mezőbe írja be a munkamenetgazda-készlet és annak felhasználói által használt megosztás nevét.

4.  Válassza a Lap alján válassza a **Véleményezés + létrehozás** lehetőséget. Ezzel megnyitja az érvényesítési lapot. A kötet sikeres ellenőrzése után válassza a **Létrehozás lehetőséget.**

5.  Ezen a ponton az új kötet üzembe kerül. A telepítés befejezése után használhatja az Azure NetApp-fájlok megosztását.

6.  A csatlakoztatási útvonal megtekintéséhez válassza az Ugrás az **erőforrásra** lehetőséget, és keresse meg az Áttekintés lapon.

    ![Az Áttekintő képernyő képernyőképe, amelyen egy piros nyíl mutat a csatlakoztatási útvonalra.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Az FSLogix konfigurálása munkamenetgazda virtuális gépeken (VM-eken)

Ez a szakasz a [Profiltároló létrehozása egy gazdakészlethez fájlmegosztás használatával című](create-host-pools-user-profile.md)szakaszon alapul.

1. [Töltse le az FSLogix ügynök .zip fájlt,](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) amíg még távol van a munkamenetgazda virtuális gépében.

2. Csomagolja ki a letöltött fájlt.

3. A fájlban nyissa meg az **x64-es** > kiadások lapot, és futtassa **az FSLogixAppsSetup.exe programot.****Releases** Megnyílik a telepítési menü.

4.  Ha rendelkezik termékkulccsal, írja be azt a Termékkulcs mezőbe.

5. Jelölje be az **Elfogadom a licencfeltételek melletti jelölőnégyzetet.**

6. Válassza az **Install** (Telepítés) lehetőséget.

7. Keresse meg a **C:\\Program Files\\\\FSLogix Apps,** hogy erősítse meg az ügynök telepítve.

8. A Start menüben futtassa a **RegEdit parancsot** rendszergazdaként.

9. Keresse meg **az FSLogix\\\\HKEY_LOCAL_MACHINE számítógépes szoftverét.\\**

10. **Profilok**nevű kulcs létrehozása.

11.  **Engedélyezve** nevű érték létrehozása **1 REG_DWORD** típusú REG_DWORD **értékkel.**

12. Hozzon létre egy **VHDLocations** nevű értéket **többkarakterláncos típussal,** és állítsa be az adatértékét az Azure NetApp-fájlok megosztásának URI-jára.

13. Hozzon létre egy **DeleteLocalProfileWhenVHDShouldApply** nevű értéket 1 duplaszó értékkel, hogy a bejelentkezés előtt elkerülje a meglévő helyi profilokkal kapcsolatos problémákat.

     >[!WARNING]
     >Legyen óvatos a DeleteLocalProfileWhenVHDShouldApply érték létrehozásakor. Ha az FSLogix Profiles rendszer megállapítja, hogy a felhasználónak FSLogix-profillal kell rendelkeznie, de már létezik helyi profil, a Profiltároló véglegesen törli a helyi profilt. A felhasználó ezután be jelentkezik az új FSLogix profillal.

## <a name="assign-users-to-session-host"></a>Felhasználók hozzárendelése a munkamenet-gazdagéphez

1. Nyissa meg **a PowerShell ISE-t** rendszergazdaként, és jelentkezzen be a Windows virtuális asztalra.

2. Futtassa a következő parancsmagokat:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Amikor a rendszer hitelesítő adatokat kér, adja meg a felhasználó hitelesítő adatait a Bérlő létrehozója vagy az RDS-tulajdonos/RDS közreműködői szerepkörrel a Windows virtuális asztal bérlőjén.

4. A következő parancsmagok futtatásával rendeljen felhasználót egy Távoli asztal csoporthoz:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Győződjön meg arról, hogy a felhasználók hozzáférhetnek az Azure NetApp fájlmegosztáshoz

1. Nyissa meg az internetböngészőt, és nyissa meg a . <https://rdweb.wvd.microsoft.com/webclient/index.html>

2. Jelentkezzen be a Távoli asztal csoporthoz rendelt felhasználó hitelesítő adataival.

3. Miután létrehozta a felhasználói munkamenetet, jelentkezzen be az Azure Portalra egy felügyeleti fiókkal.

4. Nyissa meg **az Azure NetApp-fájlokat,** válassza ki az Azure NetApp-fájlok fiókot, majd válassza **a Kötetek**lehetőséget. A Kötetek menü megnyitása után válassza ki a megfelelő kötetet.

   ![Képernyőkép az Azure Portalon korábban beállított NetApp-fiókról, amelyen a Kötetek gomb van kiválasztva.](media/netapp-account.png)

5. Nyissa meg az **Áttekintés** lapot, és ellenőrizze, hogy az FSLogix profiltároló tárhelyet használ-e.

6. Csatlakozzon közvetlenül a gazdagép készlet bármely virtuális géphez a Távoli asztal használatával, és nyissa meg a **Fájlkezelőt.** Ezután keresse meg a **Mount elérési utat** (a következő példában a csatlakoztatási útvonal anf-SMB-3863.gt1107.onmicrosoft.com \\ \\\\anf-VOL).

   Ebben a mappában kell lennie egy profil virtuális merevlemez (vagy VHDX), mint az alábbi példában.

   ![Képernyőkép a csatlakoztatási útvonalon lévő mappa tartalmáról. Belül van egy "Profile_ssbb" nevű vHD fájl.](media/mount-path-folder.png)

## <a name="next-steps"></a>További lépések

Az FSLogix profiltárolók segítségével felhasználói profilmegosztást állíthat be. Ha tudni szeretné, hogyan hozhat létre felhasználói profilmegosztásokat az új tárolókkal, olvassa el a [Profiltároló létrehozása egy gazdagépkészlethez fájlmegosztás használatával](create-host-pools-user-profile.md)című témakört.
