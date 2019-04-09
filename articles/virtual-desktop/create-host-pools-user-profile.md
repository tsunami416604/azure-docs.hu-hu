---
title: Hozzon létre egy felhasználói profil megosztást egy Windows virtuális asztal előzetes gazdagép készlet – Azure
description: Hogyan állítható be egy Windows virtuális asztal előzetes gazdagép készlet FSLogix profil tárolója.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/04/2019
ms.author: helohr
ms.openlocfilehash: a7e2f3c95819c6ab6d2e63e5c7a2f62649ebd15c
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056095"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Felhasználói profil megosztásának beállítása a gazdagépcsoporton

A Windows Virtual Desktop előzetes verziójú szolgáltatások FSLogix profil tárolók a javasolt felhasználói profil megoldás kínál. Nem ajánlott, a felhasználói profil lemezre (UPD) megoldást, amely lesz, és elavult későbbi Windows virtuális asztali verzióiban.

Ebben a szakaszban megtudhatja, hogyan állítható be a gazdagép-készletben FSLogix profil tároló megosztást. FSLogix kapcsolatos általános dokumentációjáért lásd: a [FSLogix hely](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Hozzon létre egy új virtuális gépet, amely úgy működik, mivel a fájlmegosztás

A virtuális gép létrehozásakor ügyeljen arra, helyezze el, vagy az azonos virtuális hálózatban, mint a gazdagép-készlet virtuális gépek vagy egy virtuális hálózatot, amely kapcsolódik a készlet virtuális gépek futtatásához. Többféle módon is létrehozhat egy virtuális gépet:

- [Virtuális gép létrehozása az Azure-katalógus-lemezkép](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt rendszerképből](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [A virtuális gépek nem felügyelt rendszerkép létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Miután létrehozta a virtuális gépet, csatlakoztassa a tartományhoz tegye az alábbiakat:

1. [Csatlakozzon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Indítsa el a virtuális gépen **Vezérlőpult** válassza **rendszer**.
3. Válassza ki **számítógépnév**válassza **beállításainak módosítása**, majd válassza ki **módosítása...**
4. Válassza ki **tartomány** és írja be az Active Directory-tartomány a virtuális hálózaton.
5. Hitelesítés a tartományhoz csatlakozó gépek jogosultságokkal rendelkező tartományi fiókkal.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>A virtuális gépet ügyfélgépként felhasználói profilok fájlmegosztás előkészítése

Általános útmutatás segítségével történő virtuális gép előkészítése a felhasználói profilok fájlmegosztás-kiszolgálóként a következők:

1. A Windows virtuális asztal Active Directory-felhasználók hozzáadása egy [Active Directory biztonsági csoportban](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Ez a biztonsági csoport a fájl megosztási újonnan létrehozott virtuális géphez a Windows virtuális asztali felhasználók hitelesítéséhez használható.
2. [A fájl megosztási virtuális géphez csatlakozni](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. A fájl megosztási virtuális gépen, hozzon létre egy mappát a **C meghajtó** , hogy a profil megosztás szolgál.
4. Kattintson a jobb gombbal az új mappára, válassza ki **tulajdonságok**válassza **megosztási**, majd **Speciális megosztás...** .
5. Válassza ki **a mappa megosztása**válassza **engedélyek...** , majd **hozzáadása...** .
6. Keresse meg a biztonsági csoportot, amelyhez hozzáadta a Windows virtuális asztal felhasználói, és győződjön meg arról, hogy a csoport **teljes hozzáférés**.
7. Után adja hozzá a biztonsági csoporthoz, kattintson a jobb gombbal a mappára, válassza ki **tulajdonságok**válassza **megosztási**, majd másolja le a **hálózati elérési út** való későbbi használatra.

Engedélyekkel kapcsolatos további információkért lásd: a [FSLogix dokumentáció](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers).

## <a name="configure-the-fslogix-profile-container"></a>A profil FSLogix tároló konfigurálása

Konfigurálja a virtuális gépek a FSLogix szoftverrel, tegye a következőt az összes olyan számítógépen, a gazdagép-készlethez regisztrálva:

1. [Csatlakozzon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Indítsa el egy webböngészőt, és navigáljon a [ezt a hivatkozást](https://go.microsoft.com/fwlink/?linkid=2084562) a FSLogix ügynök letöltéséhez. A Windows virtuális asztal nyilvános előzetes verzió része egy licenckulcs aktiválásához FSLogix fog kapni. A kulcs a a LicenseKey.txt fájlt a FSLogix ügynök .zip-fájlban.
3. Keresse meg vagy \\ \\Win32\\kiadás vagy \\ \\X64\\a .zip-fájlt, és futtassa a kiadási **FSLogixAppsSetup** FSLogix-ügynök telepítése.
4. Navigáljon a **Program Files** > **FSLogix** > **alkalmazások** ellenőrizze az ügynök telepítve van.
5. Futtassa a start menüből **RegEdit** rendszergazdaként. Navigáljon a **számítógép\\HKEY_LOCAL_MACHINE\\szoftver\\FSLogix**.
6. Hozzon létre egy kulcsot nevű **profilok**.
7. Hozza létre a profilok kulcs a következő értékeket:

| Name (Név)                | Typo               | Data/Value                        |
|---------------------|--------------------|-----------------------------------|
| Engedélyezve             | DWORD              | 1                                 |
| VHDLocations        | Karakterláncsoros érték | "A fájlmegosztás hálózati elérési út"     |
