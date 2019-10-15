---
title: FSLogix-profil tároló létrehozása egy gazdagép-készlethez virtuálisgép-alapú fájlmegosztás használatával – Azure
description: FSLogix-profilok tárolójának beállítása egy Windows rendszerű virtuális asztali készlethez virtuális gépi alapú fájlmegosztás használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: 0e7516fc4874e4cbc387f2f494efc6ef745d64f7
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311590"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Profiltároló létrehozása gazdagépkészlet számára fájlmegosztás használatával

A Windows rendszerű virtuális asztali szolgáltatás az ajánlott felhasználói profil megoldásként kínálja a FSLogix-profilok tárolóit. Nem javasoljuk a felhasználói profil lemez (UPD) megoldás használatát, amely a Windows rendszerű virtuális asztal jövőbeli verzióiban elavulttá válik.

Ebből a cikkből megtudhatja, hogyan állíthat be egy FSLogix-profil tároló-megosztást egy gazdagép-készlethez egy virtuálisgép-alapú fájlmegosztás használatával. További FSLogix dokumentációt a [FSLogix webhelyen](https://docs.fslogix.com/)talál.

>[!NOTE]
>Ha összehasonlító anyagot keres az Azure-beli különböző FSLogix-profilok tárolási lehetőségeivel kapcsolatban, tekintse meg a [FSLogix-profilok tárolási lehetőségei](store-fslogix-profile.md)című témakört.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Hozzon létre egy új virtuális gépet, amely fájlmegosztásként fog működni

A virtuális gép létrehozásakor ügyeljen arra, hogy ugyanazt a virtuális hálózatot helyezze el, mint a gazdagép-készlet virtuális gépei vagy egy virtuális hálózat, amely a gazdagép-készlet virtuális gépekhez kapcsolódik. Több módon is létrehozhat egy virtuális gépet:

- [Virtuális gép létrehozása Azure Gallery-rendszerképből](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt rendszerképből](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Virtuális gép létrehozása nem felügyelt rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

A virtuális gép létrehozása után csatlakoztassa azt a tartományhoz a következő műveletek végrehajtásával:

1. [Kapcsolódjon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. A virtuális gépen indítsa el a **Vezérlőpultot** , és válassza a **rendszer**elemet.
3. Válassza ki a **számítógép nevét**, válassza a **beállítások módosítása**lehetőséget, majd válassza a **módosítás...** lehetőséget.
4. Válassza a **tartomány** lehetőséget, majd adja meg a Active Directory tartományt a virtuális hálózaton.
5. A hitelesítést olyan tartományi fiókkal végezze el, amely jogosultságokkal rendelkezik a tartományhoz csatlakozó gépekhez.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>A virtuális gép előkészítése a felhasználói profilok fájlmegosztásként való elvégzésére

A virtuális gépeknek a felhasználói profilok fájlmegosztásként való előkészítésére vonatkozó általános utasítások a következők:

1. Adja hozzá a Windows rendszerű virtuális asztali Active Directory felhasználókat egy [Active Directory biztonsági csoporthoz](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Ez a biztonsági csoport fogja használni a Windows rendszerű virtuális asztali felhasználókat az imént létrehozott fájlmegosztás virtuális gépre.
2. [Kapcsolódjon a fájlmegosztás virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. A fájlmegosztás virtuális gépen hozzon létre egy mappát a **C meghajtón** , amelyet a rendszer a profil megosztásaként fog használni.
4. Kattintson a jobb gombbal az új mappára, válassza a **Tulajdonságok**lehetőséget, válassza a **megosztás**, majd a **speciális megosztás**lehetőséget.
5. Jelölje be **a mappa megosztása**jelölőnégyzetet, majd válassza az **engedélyek...** lehetőséget, majd kattintson a **Hozzáadás...** elemre.
6. Keresse meg azt a biztonsági csoportot, amelyhez hozzáadta a Windows rendszerű virtuális asztali felhasználókat, majd győződjön meg arról, hogy a csoport **teljes hozzáféréssel**rendelkezik.
7. A biztonsági csoport hozzáadása után kattintson a jobb gombbal a mappára, válassza a **Tulajdonságok parancsot**, válassza a **megosztás**lehetőséget, majd másolja le a **hálózati elérési utat** későbbi használatra.

Az engedélyekkel kapcsolatos további információkért tekintse meg a [FSLogix dokumentációját](https://docs.microsoft.com/fslogix/fslogix-storage-config-ht).

## <a name="configure-the-fslogix-profile-container"></a>A FSLogix-profil tárolójának konfigurálása

A virtuális gépek FSLogix-szoftverrel való konfigurálásához tegye a következőket a gazdagépen regisztrált összes gépen:

1. [Kapcsolódjon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Nyisson meg egy webböngészőt, és navigáljon [erre a hivatkozásra](https://go.microsoft.com/fwlink/?linkid=2084562) a FSLogix-ügynök letöltéséhez.
3. Navigáljon \\ @ no__t-1Win32 @ no__t-2Release vagy \\ @ no__t-4X64 @ no__t-5Release a. zip fájlban, és futtassa a **FSLogixAppsSetup** -t a FSLogix-ügynök telepítéséhez.  További információ a FSLogix telepítéséről: a [FSLogix letöltése és telepítése](https://docs.microsoft.com/fslogix/install-ht).
4. Navigáljon a **programfájlok** > **FSLogix** > **alkalmazások** elemre, hogy erősítse meg az ügynök telepítését.
5. A Start menüben futtassa a **Regedit parancsot** rendszergazdaként. Navigáljon a következő **számítógéphez: @ no__t-1HKEY_LOCAL_MACHINE @ no__t-2software @ no__t-3FSLogix**.
6. Hozzon létre egy **profilok**nevű kulcsot.
7. Hozza létre a következő értékeket a profilok kulcshoz:

| Név                | Type (Típus)               | Az adatértékek/értékek                        |
|---------------------|--------------------|-----------------------------------|
| Engedélyezve             | DWORD              | 1                                 |
| VHDLocations        | Több karakterláncos érték | "Fájlmegosztás hálózati elérési útja"     |

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára. Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja a virtuális gépek igény szerinti [elérését](../security-center/security-center-just-in-time.md).
