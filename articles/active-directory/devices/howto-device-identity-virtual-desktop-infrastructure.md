---
title: Eszköz-identitás és asztali virtualizáció – Azure Active Directory
description: Ismerje meg, hogyan használhatók együtt a VDI és az Azure AD-eszköz identitásai
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dde9d8b50233c3c4033daf618e0e626c0174b0c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903153"
---
# <a name="device-identity-and-desktop-virtualization"></a>Eszköz-identitás és asztali virtualizálási

A rendszergazdák gyakran telepítik a Windows operációs rendszert üzemeltető virtuális asztali infrastruktúrát (VDI) használó platformokat a szervezetekben. A rendszergazdák a következőre telepítik a VDI-t:

- Egyszerűsítse a felügyeletet.
- Az erőforrások összevonásával és központosításával csökkentheti a költségeket.
- A végfelhasználók mobilitását és a virtuális asztalok elérésének szabadságát bármikor, bárhonnan, bármely eszközről elérheti.

A virtuális asztalok két elsődleges típusa létezik:

- Állandó
- Nem állandó

Az állandó verziók minden felhasználóhoz vagy felhasználói készlethez egyedi asztali rendszerképet használnak. Ezek az egyedi asztalok testreszabhatók és későbbi használatra menthetők. 

A nem állandó verziók olyan asztali számítógépek gyűjteményét használják, amelyekhez a felhasználók igény szerint hozzáférhetnek. Ezek a nem állandó asztalok a felhasználó kijelentkezése után visszaállnak eredeti állapotukba.

Ez a cikk a Microsoft útmutatását ismerteti a rendszergazdáknak az eszköz identitásának és a VDI-nek a támogatásával kapcsolatban. Az eszköz identitásával kapcsolatos további információkért tekintse meg az [eszköz identitását](overview.md)ismertető cikket.

## <a name="supported-scenarios"></a>Támogatott esetek

Mielőtt az eszköz identitásait az Azure AD-ben konfigurálja a VDI-környezethez, ismerkedjen meg a támogatott forgatókönyvekkel. Az alábbi táblázat bemutatja, hogy mely kiépítési forgatókönyvek támogatottak. A kiépítés ebben a kontextusban azt jelenti, hogy a rendszergazdák a teljes felhasználói beavatkozás nélkül konfigurálhatják az eszköz identitásait.

| Eszköz identitásának típusa | Identitás-infrastruktúra | Windows rendszerű eszközök | VDI platform verziója | Támogatott |
| --- | --- | --- | --- | --- |
| csatlakozik a Hibrid Azure AD-hez | Összevont | Windows jelenlegi * * * és Windows Down-Level * * * * | Állandó | Igen |
|   |   | Windows jelenlegi | Nem állandó | Nem |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen |
|   | Felügyelt * * | Windows jelenlegi és Windows Down-Level | Állandó | Igen |
|   |   | Windows jelenlegi | Nem állandó | Nem |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen |
| Azure AD-hez csatlakoztatva | Összevont | Windows jelenlegi | Állandó | Nem |
|   |   |   | Nem állandó | Nem |
|   | Felügyelt | Windows jelenlegi | Állandó | Nem |
|   |   |   | Nem állandó | Nem |
| Az Azure AD-ban regisztrálva | Összevont | Windows jelenlegi | Állandó | Nem alkalmazható |
|   |   |   | Nem állandó | Nem alkalmazható |
|   | Felügyelt | Windows jelenlegi | Állandó | Nem alkalmazható |
|   |   |   | Nem állandó | Nem alkalmazható |

\*Az **összevont** identitás-infrastruktúra környezete olyan identitás-szolgáltatói környezetet képvisel, mint például a AD FS vagy más harmadik féltől származó identitásszolgáltató.

\*\*A **felügyelt** identitás-infrastruktúra környezete az Azure ad-vel rendelkező környezetet jelöli, mivel a [jelszó-kivonatos szinkronizálással (PHS)](../hybrid/whatis-phs.md) vagy [átmenő hitelesítéssel (PTA ESP)](../hybrid/how-to-connect-pta.md) telepített identitás-szolgáltató, [zökkenőmentes egyszeri bejelentkezéssel](../hybrid/how-to-connect-sso.md).

\*\*\*A **Windows jelenlegi** eszközei a Windows 10, a windows Server 2016 és a windows Server 2019.

\*\*\*\*A **Windows Down-szintű** eszközök a Windows 7, a Windows 8,1, a windows Server 2008 R2, a windows Server 2012 és a windows Server 2012 R2 rendszernek felelnek meg. A Windows 7 támogatási információi: a [Windows 7 támogatása véget ér](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). A Windows Server 2008 R2-vel kapcsolatos támogatási információk: [felkészülés a Windows server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008)-re a támogatás befejezése után.

## <a name="microsofts-guidance"></a>A Microsoft útmutatása

A rendszergazdáknak az identitás-infrastruktúrájuk alapján kell megismerniük a következő cikkeket, hogy megtudja, hogyan konfigurálhatja a hibrid Azure AD Joint.

- [Hibrid Azure Active Directory csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
- [Hibrid Azure Active Directory csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

Ha a rendszer-előkészítő eszközre (sysprep.exe) támaszkodik, és ha a telepítés előtt Windows 10 1809 rendszerképet használ, győződjön meg arról, hogy a rendszerkép nem egy olyan eszközről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-hez.

Ha virtuális gép (VM) pillanatképét szeretné létrehozni további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem olyan virtuális gépről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-csatlakozásként.

A nem állandó VDI-hez készült hibrid Azure AD-csatlakozás a Windows jelenlegi verzióiban nem támogatott. A nem állandó VDI Windows-szintű üzembe helyezése esetén a rendszergazdáknak Kiemelt figyelmet kell fordítaniuk az elavult eszközök Azure AD-ben való felügyeletére. A Microsoft javasolja, hogy a rendszergazdák implementálják az alábbi útmutatást. Ennek elmulasztása miatt a címtárban sok olyan elavult hibrid Azure AD-eszköz található, amely a nem állandó VDI-platformról lett regisztrálva.

- Hozzon létre és használjon egy előtagot annak a számítógépnek a megjelenítendő neveként, amely VDI-alapúként jelöli meg az asztalt.
- A következő parancs végrehajtása a kijelentkezési parancsfájl részeként. Ez a parancs elindítja az Azure AD-nek a legjobb erőfeszítést az eszköz törléséhez.
   - Windows Down-szintű eszközök esetén – autoworkplace.exe/Leave
- Az [elavult eszközök kezelésére](manage-stale-devices.md)szolgáló folyamat meghatározása és implementálása.
   - Ha már van egy stratégia a nem állandó hibrid Azure AD-hez csatlakoztatott eszközök azonosítására, akkor agresszívebb lehet ezen eszközök tisztítása, hogy a címtár ne legyen felhasználva sok elavult eszközzel.
 
## <a name="next-steps"></a>Következő lépések

[Hibrid Azure Active Directory illesztés konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
