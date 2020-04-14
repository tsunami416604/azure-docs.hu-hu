---
title: FSLogix profiltároló létrehozása Azure Files Active Directory tartományi szolgáltatások – Azure
description: Ez a cikk ismerteti, hogyan hozhat létre egy FSLogix profiltároló az Azure Files és az Azure Active Directory tartományi szolgáltatások.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265771"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>FSLogix-profiltároló létrehozása az Azure Files segítségével

Ez a cikk bemutatja, hogyan hozhat létre egy FSLogix profiltárolót az Azure Files és az Azure Active Directory tartományi szolgáltatások (AD DS) használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már beállított egy Azure AD DS-példányt. Ha még nem rendelkezik ilyennel, először kövesse az [Alapszintű felügyelt tartomány létrehozása című](../active-directory-domain-services/tutorial-create-instance.md) rész utasításait, majd térjen vissza ide.

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS-rendszergazdák hozzáadása

További rendszergazdák hozzáadásához hozzon létre egy új felhasználót, és adjon nekik engedélyeket.

Rendszergazda hozzáadása:

1. Válassza az **Azure Active Directory** elemet az oldalsávról, majd válassza a Minden **felhasználó**lehetőséget, majd az **Új felhasználó**lehetőséget.

2.  Adja meg a felhasználó adatait a mezőkbe.

3. Az Azure Active Directory ablaktáblán a képernyő bal oldalán válassza a **Csoportok**lehetőséget.

4. Jelölje ki az **AAD tartományvezérlő rendszergazdák csoportját.**

5. A bal oldali ablaktáblában válassza a **Tagok**lehetőséget, majd válassza a **Tagok hozzáadása** lehetőséget a főablaktáblán. Ez az Azure AD-ben elérhető összes felhasználó listáját jeleníti meg. Válassza ki az imént létrehozott felhasználói profil nevét.

## <a name="set-up-an-azure-storage-account"></a>Azure Storage-fiók beállítása

Most itt az ideje, hogy engedélyezze az Azure AD DS hitelesítését a kiszolgálói üzenetblokkon (SMB) keresztül. 

A hitelesítés engedélyezése:

1. Ha még nem tette meg, hozzon létre és telepítsen egy általános célú v2 Azure Storage-fiókot az [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md)című részben található utasításokat követve.

2. Miután befejezte a fiók beállítását, válassza az **Ugrás az erőforrásra**lehetőséget.

3. Válassza a **konfiguráció a** képernyő bal oldalán lévő ablaktáblában válassza a Konfiguráció lehetőséget, majd engedélyezze az Azure **Active Directory-hitelesítést** az Azure Files számára a fő ablaktáblában. Amikor elkészült, válassza a **Mentés** lehetőséget.

4. Válassza az **Áttekintés** lehetőséget a képernyő bal oldalán, majd a fájlok **lehetőséget** a főablaktáblán.

5. Válassza a **Fájlmegosztás** lehetőséget, és írja be a **Név** és **kvóta értéket** a képernyő jobb oldalán megjelenő mezőkbe.

## <a name="assign-access-permissions-to-an-identity"></a>Hozzáférési engedélyek hozzárendelése identitáshoz

A fájlmegosztás eléréséhez más felhasználóknak hozzáférési engedélyekre lesz szükségük. Ehhez minden felhasználónak hozzá kell rendelnie egy szerepkört a megfelelő hozzáférési engedélyekkel.

A felhasználók hozzáférési engedélyeinek hozzárendelése:

1. Az Azure Portalon nyissa meg az [Azure Storage-fiók beállítása](#set-up-an-azure-storage-account)kor létrehozott fájlmegosztást.

2. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**

3. Válassza **a Szerepkör-hozzárendelés hozzáadása**lehetőséget.

4. A **Szerepkör-hozzárendelés hozzáadása** lapon válassza ki a megfelelő beépített szerepkört a szerepkörlistából. A megfelelő engedélyek hez legalább a **Storage File Data SMB Share Contributor lehetőséget** kell választania a fiókhoz.

5. A **Hozzáférés hozzárendelése a**területen lehetőséget választja az Azure Active Directory **felhasználói, csoport- vagy szolgáltatásnév elemre.**

6. Válassza ki a cél Azure Active Directory-identitás nevét vagy e-mail címét.

7. Kattintson a **Mentés** gombra.

## <a name="get-the-storage-account-access-key"></a>A tárfiók hozzáférési kulcsának beszereznie

Ezután be kell szereznie a tárfiók hozzáférési kulcsát.

A tárfiók hozzáférési kulcsának leése:

1. Az Azure Portal oldalsávján válassza **a Storage-fiókok**lehetőséget.

2. A tárfiókok listájából válassza ki azt a fiókot, amelyhez engedélyezte az Azure AD DS-t, és a fenti lépésekben hozta létre az egyéni szerepköröket.

3. A **Beállítások csoportban**válassza az **Access-kulcsok lehetőséget,** és másolja a kulcsot a **billentyűből1.**

4. Lépjen a **Virtuális gépek** lapra, és keresse meg a virtuális gépet, amely a gazdakészlet részévé válik.

5. Válassza ki a virtuális gép (VM) nevét a **Virtuális gépek (adVM)** csoportban, és válassza a **Csatlakozás** lehetőséget

    Ez letölti az RDP-fájlt, amely lehetővé teszi, hogy a saját hitelesítő adataival jelentkezzen be a virtuális gépre.

    ![Képernyőkép a Csatlakozás a virtuális géphez ablak RDP lapjáról.](media/rdp-tab.png)

6. Miután bejelentkezett a virtuális gépbe, futtasson egy parancssort rendszergazdaként.

7. Futtassa az alábbi parancsot:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Cserélje `<desired-drive-letter>` le az Ön által választott `y:`meghajtóbetűjelre (például ).
    - Cserélje le az `<storage-account-name>` összes példányt a korábban megadott tárfiók nevére.
    - Cserélje `<share-name>` le a korábban létrehozott megosztás nevére.
    - Cserélje `<storage-account-key>` le az Azure-ból származó tárfiók kulcsát.

    Például:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Futtassa a következő parancsot, hogy a felhasználó teljes hozzáférést biztosítson az Azure Files megosztáshoz.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Cserélje `<mounted-drive-letter>` le annak a meghajtónak a betűjelét, amelyet a felhasználó használni szeretne.
    - Cserélje `<user-email>` le annak a felhasználónak az upn-jára, aki ezt a profilt fogja használni a munkamenetgazda virtuális gépeinek eléréséhez.

    Például:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Profiltároló létrehozása

Most, hogy a profilok készen állnak, hozzon létre egy FSLogix profiltárolót.

FSLogix profiltároló konfigurálása:

1. Jelentkezzen be a cikk elején konfigurált munkamenetgazda virtuális gépbe, majd [töltse le és telepítse az FSLogix-ügynököt.](/fslogix/install-ht/)

2. Csomagolja ki a letöltött FSLogix ügynökfájlt, és lépjen az **x64-es** > **kiadásokra,** majd nyissa meg **az FSLogixAppsSetup.exe fájlt.**

3. Miután a telepítő elindul, válassza **az Elfogadom a licenc feltételeket.** Adott esetben adjon meg egy új kulcsot.

4. Válassza az **Install** (Telepítés) lehetőséget.

5. Nyissa **meg a C meghajtót,** majd nyissa meg az**FSLogix-alkalmazások** > **Apps** **programozása** > lapot, és ellenőrizze, hogy az FSLogix ügynök megfelelően van-e telepítve.

     >[!NOTE]
     > Ha több virtuális gép van a gazdakészletben, minden virtuális gépesetében meg kell ismételnie az 1–5.

6. **Futtassa a Rendszerleíróadatbázis-szerkesztőt** (RegEdit) rendszergazdaként.

7. Nyissa meg **a Computer** > **HKEY_LOCAL_MACHINE** > Software**FSLogix****elemet,** > kattintson a jobb gombbal az **FSLogix**elemre, válassza az **Új**parancsot, majd a **Kulcs parancsot.**

8. **Hozzon**létre egy profilok nevű új kulcsot.

9.  Kattintson a jobb gombbal a **Profilok**elemre, válassza az **Új**parancsot, majd a **Duplaszó (32 bites) értéket.** Nevezze el az **Engedélyezve értéket,** és állítsa az **Adat** értéket **1-re.**

    ![Képernyőkép a Profilok kulcsról. A REG_DWORD fájl ki van emelve, és az Adat értéke 1.](media/dword-value.png)

10. Kattintson a jobb gombbal a **Profilok**elemre, válassza az **Új**parancsot, majd a **Többszörös karakterlánc értékét.** Nevezze el a **VHDLocations értéket,** és állítsa `\\fsprofile.file.core.windows.net\share` be az Azure Files megosztásuri-uri-értékét adatértékként.

    ![A Profilok kulcs képernyőképe a VHDLocations fájllal. Az adatok értéke az Azure Files megosztásuri-értékét jeleníti meg.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Felhasználók hozzárendelése munkamenet-gazdagéphez

Most hozzá kell rendelnie a felhasználókat a munkamenet-gazdagéphez.

Felhasználók hozzárendelése:

1. Futtassa a Windows PowerShellt rendszergazdaként, majd futtassa a következő parancsmamot a Windows virtuális asztalra való bejelentkezéshez a PowerShell használatával:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Amikor hitelesítő adatokat kér, adja meg ugyanazt a felhasználót, aki megkapta a TenantCreator, RDS-tulajdonos vagy RDS közreműködői szerepkört a Windows virtuális asztal bérlőjén.

2. Futtassa a következő parancsmagokat a felhasználó távoli asztali csoporthoz rendeléséhez:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    A korábbi parancsmagokhoz hasonlóan a `<your-wvd-tenant>`, `<wvd-pool>`a `<user-principal>` , és a megfelelő értékeket cserélje ki.

    Például:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>A profil működésének biztosítása

Most mindössze annyit kell tennie, hogy győződjön meg róla, a profil létrehozott létezik, és működik a kívánt.

A profil igazolása:

1. Nyisson meg egy böngészőt, és lépjen [a Windows Virtual Desktop webes ügyfélprogramra.](https://rdweb.wvd.microsoft.com/webclient/index.html)

2. Jelentkezzen be a Távoli asztal csoporthoz rendelt felhasználói fiókkal.

3. A felhasználói munkamenet létrehozása után nyissa meg az Azure Portalt, és jelentkezzen be egy rendszergazdai fiókkal.

4. Az oldalsávon válassza **a Storage-fiókok**lehetőséget.

5. Válassza ki a rendszerű tárolófiókot, amelyet a munkamenetgazda-készlet fájlmegosztásaként konfigurált, és amely engedélyezve van az Azure AD DS szolgáltatással.

6. Válassza a **Fájlok** ikont, majd bontsa ki a megosztást.

    Ha minden megfelelően van beállítva, akkor a **következő** formátumú könyvtárat `<user SID>-<username>`kell látnia: .

## <a name="next-steps"></a>További lépések

Ha alternatív módszereket keres az FSLogix profiltárolók létrehozásához, tekintse meg az alábbi cikkeket:

- [Hozzon létre egy profiltárolót egy gazdagépkészlethez fájlmegosztás használatával.](create-host-pools-user-profile.md)
- [FSLogix-profiltároló létrehozása gazdakészlethez az Azure NetApp-fájlok használatával](create-fslogix-profile-container.md)

Az [FSLogix-profiltárolókban és az Azure-fájlokban](fslogix-containers-azure-files.md)az FSlogix-tárolókkal kapcsolatos fogalmakkal kapcsolatos további információkat részletesebben is megtalálhatja.
