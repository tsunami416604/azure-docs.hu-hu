---
title: Windows Virtual Desktop FSLogix profiltároló-megosztás – Azure
description: FSLogix profiltároló beállítása Windows virtual desktop gazdakészlethez virtuális gépalapú fájlmegosztás használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250919"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Profiltároló létrehozása gazdagépkészlet számára fájlmegosztás használatával

A Windows Virtual Desktop szolgáltatás FSLogix profiltárolókat kínál ajánlott felhasználói profilmegoldásként. Nem javasoljuk a Felhasználói profillemez (UPD) megoldás használatát, amely a Windows Virtual Desktop későbbi verzióiban elavult.

Ez a cikk bemutatja, hogyan állíthat be FSLogix-profiltároló-megosztást egy gazdagépkészlethez egy virtuálisgép-alapú fájlmegosztás használatával. További FSLogix dokumentáció, lásd az [FSLogix oldalon](https://docs.fslogix.com/).

>[!NOTE]
>Ha az Azure-beli FSLogix-profiltároló különböző tárolási lehetőségeivel kapcsolatos összehasonlító anyagokat keres, olvassa el [az FSLogix-profiltárolók tárolási beállításai című témakört.](store-fslogix-profile.md)

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Új virtuális gép létrehozása, amely fájlmegosztásként fog működni

A virtuális gép létrehozásakor ügyeljen arra, hogy helyezze el a gazdagépkészlet virtuális gépeivel azonos virtuális hálózaton, vagy egy olyan virtuális hálózaton, amely a gazdakészlet virtuális gépeihez való kapcsolódással rendelkezik. Virtuális gépet többféleképpen hozhat létre:

- [Virtuális gép létrehozása azure-galériaképből](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt lemezképből](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Virtuális gép létrehozása nem felügyelt lemezképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

A virtuális gép létrehozása után a következő módon csatlakozzon a tartományhoz:

1. [Csatlakozzon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. A virtuális gépen indítsa el a **Vezérlőpultot,** és válassza a **Rendszer**lehetőséget.
3. Válassza **a Számítógép név lehetőséget,** válassza a Beállítások **módosítása**lehetőséget, majd a **Módosítás...**
4. Válassza **a Tartomány lehetőséget,** majd írja be az Active Directory tartományt a virtuális hálózaton.
5. Hitelesítse a hitelesítést olyan tartományi fiókkal, amely jogosultságokkal rendelkezik a tartományhoz való csatlakozáshoz szükséges gépekhez.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>A virtuális gép előkészítése a felhasználói profilok fájlmegosztási rendszerére

Az alábbiakban általános útmutatást talál arra vonatkozóan, hogy miként készíthet ő egy virtuális gépet fájlmegosztásként a felhasználói profilokhoz:

1. Adja hozzá a Windows virtuális asztal Active Directory felhasználóit az [Active Directory biztonsági csoportjához](/windows/security/identity-protection/access-control/active-directory-security-groups/). Ez a biztonsági csoport a Windows virtuális asztal felhasználóinak hitelesítésére szolgál az imént létrehozott fájlmegosztási virtuális gépen.
2. [Csatlakozás a fájlmegosztási virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. A fájlmegosztási virtuális gépen hozzon létre egy mappát a **C meghajtón,** amelyet profilmegosztásként fog használni.
4. Kattintson a jobb gombbal az új mappára, válassza a **Tulajdonságok parancsot,** válassza **a Megosztás**parancsot, majd válassza a Speciális **megosztás parancsot...**.
5. Válassza **a Mappa megosztása**lehetőséget, válassza az **Engedélyek...** lehetőséget, majd a **Hozzáadás...** lehetőséget.
6. Keresse meg azt a biztonsági csoportot, amelyhez hozzáadta a Windows virtuális asztal felhasználóit, majd győződjön meg arról, hogy a csoport **teljes hozzáféréssel**rendelkezik.
7. A biztonsági csoport hozzáadása után kattintson a jobb gombbal a mappára, válassza a **Tulajdonságok**parancsot, válassza **a Megosztás parancsot,** majd másolja lefelé a **hálózati elérési utat,** amelyet később használni szeretne.

Az engedélyekről további információt az [FSLogix dokumentációjában](/fslogix/fslogix-storage-config-ht/)talál.

## <a name="configure-the-fslogix-profile-container"></a>Az FSLogix profiltároló konfigurálása

A virtuális gépek FSLogix szoftverrel való konfigurálásához tegye a következőket a gazdakészletbe regisztrált minden egyes gépen:

1. [Csatlakozzon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Indítson el egy internetböngészőt, és keresse meg ezt a [linket](https://go.microsoft.com/fwlink/?linkid=2084562) az FSLogix ügynök letöltéséhez.
3. Nyissa meg \\ \\a\\Win32 \\ \\Release\\vagy az X64 Release fájlt a .zip fájlban, és futtassa az **FSLogixAppsSetup** programot az FSLogix ügynök telepítéséhez.  Az FSLogix telepítéséről az [FSLogix letöltése és telepítése](/fslogix/install-ht/)című témakörben olvashat bővebben.
4. A telepített ügynök megerősítéséhez nyissa meg az**FSLogix** > **Apps** **programfájlokat.** > 
5. A start menüben futtassa a **RegEdit parancsot** rendszergazdaként. Keresse meg **az FSLogix\\\\HKEY_LOCAL_MACHINE számítógépes szoftverét.\\**
6. **Profilok**nevű kulcs létrehozása.
7. Hozza létre a profilok kulcs következő értékeit:

| Név                | Típus               | Adat/érték                        |
|---------------------|--------------------|-----------------------------------|
| Engedélyezve             | DWORD              | 1                                 |
| VHDLocations (VHDHelyek)        | Többkarakterláncos érték | "A fájlmegosztás hálózati elérési útja"     |

>[!IMPORTANT]
>A Windows virtuális asztali környezet azure-beli védelméhez azt javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows virtuális asztal nem igényel nyitott bejövő port3389 a felhasználók számára, hogy hozzáférjenek a gazdakészlet virtuális gépek. Ha hibaelhárítási célból meg kell nyitnia a 3389-es portot, javasoljuk, hogy [just-in-time virtuális gép-hozzáférést használjon.](../security-center/security-center-just-in-time.md)
