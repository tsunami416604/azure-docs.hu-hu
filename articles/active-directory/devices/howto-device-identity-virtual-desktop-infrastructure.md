---
title: Eszközidentitás és asztali virtualizáció – Azure Active Directory
description: Ismerje meg, hogyan használhatók együtt a VDI- és az Azure AD-eszközidentitások
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
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900371"
---
# <a name="device-identity-and-desktop-virtualization"></a>Eszközidentitás és asztali virtualizáció

A rendszergazdák általában virtuális asztali infrastruktúra (VDI) platformokat telepítenek, amelyek Windows operációs rendszereket üzemeltetnek a szervezetükben. A rendszergazdák a következő kit a következőkre telepítik a VDI-t:

- Egyszerűsítse a felügyeletet.
- Csökkentse költségeit az erőforrások konszolidációjával és központosításával.
- A végfelhasználók mobilitását és a virtuális asztalokhoz való szabad hozzáférést bármikor, bárhonnan, bármilyen eszközön elérheti.

A virtuális asztaloknak két elsődleges típusa van:

- Állandó
- Nem perzisztens

Az állandó verziók minden felhasználóhoz vagy felhasználókészlethez egyedi asztali lemezképet használnak. Ezek az egyedi asztalok testre szabhatók és menthetők későbbi használatra. 

A nem állandó verziók olyan asztali számítógépek gyűjteményét használják, amelyeket a felhasználók szükség szerint érhetnek el. Ezek a nem állandó asztalok a felhasználó kijelentkezése után visszaállnak eredeti állapotukba.

Ez a cikk ismerteti a Microsoft útmutatást a rendszergazdák számára az eszköz identitásának és a VDI támogatásának. Az eszközidentitásról a Mi az eszközidentitás című témakörben olvashat [bővebben.](overview.md)

## <a name="supported-scenarios"></a>Támogatott esetek

Az azure-beli AD-ben eszközidentitások konfigurálása a VDI-környezetben, ismerkedjen meg a támogatott forgatókönyvekkel. Az alábbi táblázat bemutatja, hogy mely kiépítési forgatókönyvek támogatottak. Ebben a környezetben a kiépítés azt jelenti, hogy a rendszergazda konfigurálhatja az eszközidentitások nagy méretekben anélkül, hogy a végfelhasználói beavatkozás.

| Eszköz identitástípusa | Identitás-infrastruktúra | Windows rendszerű eszközök | VDI platformverzió | Támogatott |
| --- | --- | --- | --- | --- |
| csatlakozik a Hibrid Azure AD-hez | Összevont* | Windows jelenlegi*** és Windows down-level**** | Állandó | Igen |
|   |   | Windows aktuális | Nem állandó | Nem |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen |
|   | Felügyelt** | Windows jelenlegi és Windows down-level | Állandó | Igen |
|   |   | Windows aktuális | Nem állandó | Nem |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen |
| Azure AD-hez csatlakoztatva | Összevont | Windows aktuális | Állandó | Nem |
|   |   |   | Nem állandó | Nem |
|   | Kezelt | Windows aktuális | Állandó | Nem |
|   |   |   | Nem állandó | Nem |
| Az Azure AD-ban regisztrálva | Összevont | Windows aktuális | Állandó | Nem |
|   |   |   | Nem állandó | Nem |
|   | Kezelt | Windows aktuális | Állandó | Nem |
|   |   |   | Nem állandó | Nem |

\*Az **összevont** identitás-infrastruktúra környezet egy identitásszolgáltatóval, például az AD FS-sel vagy más külső IDP-vel rendelkező környezetet jelöl.

\*\*A **felügyelt** identitás-infrastruktúra környezet olyan környezetet jelöl, ahol az Azure AD a [jelszókivonat-szinkronizálással (PHS)](../hybrid/whatis-phs.md) vagy a [zökkenőmentes egyszeri bejelentkezéssel](../hybrid/how-to-connect-sso.md)rendelkező [átadó hitelesítéssel (PTA)](../hybrid/how-to-connect-pta.md) telepített identitásszolgáltató.

\*\*\***A Windows aktuális** eszközei a Windows 10, a Windows Server 2016 és a Windows Server 2019 rendszert jelölik.

\*\*\*\***A Windows nem vízszintes** eszközök a Windows 7, a Windows 8.1, a Windows Server 2008 R2, a Windows Server 2012 és a Windows Server 2012 R2 rendszert jelölik. A Windows 7 rendszerhez nyújtott támogatásról a Windows 7 támogatása véget ér című témakörben [talál.](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support) A Windows Server 2008 R2 rendszerrel kapcsolatos támogatási információkról a [Felkészülés a Windows Server 2008 támogatási rendszerének megszűnése című témakörben találhatók.](https://www.microsoft.com/cloud-platform/windows-server-2008)

## <a name="microsofts-guidance"></a>A Microsoft útmutatása

A rendszergazdáknak az alábbi cikkekre kell hivatkozniuk az identitás-infrastruktúrájuk alapján, hogy megtanulják, hogyan konfigurálhatja a hibrid Azure AD-csatlakozást.

- [Hibrid Azure Active Directory-csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
- [Hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

Ha a Rendszerelőkészítő eszközre (sysprep.exe) támaszkodik, és a Telepítéshez windows 10 1809 előtti lemezképet használ, győződjön meg arról, hogy a lemezkép nem olyan eszközről származik, amely már regisztrálva van az Azure AD-vel hibrid Azure AD-hez.

Ha egy virtuális gép (VM) pillanatkép további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem egy virtuális gép, amely már regisztrálva van az Azure AD hibrid Azure AD-csatlakozásként.

Nem állandó VDI telepítésekor a rendszergazdáknak nagy figyelmet kell fordítaniuk az elavult eszközök kezelésére az Azure AD-ben. A Microsoft azt javasolja, hogy a rendszergazdák valósítsák meg az alábbi útmutatót. Ennek elmulasztása azt eredményezi, hogy a címtárban sok elavult hibrid Azure AD-hez csatlakozott eszközök, amelyek a nem állandó VDI-platformregisztrált.

- Hozzon létre és használjon előtagot a számítógép megjelenítendő nevéhez, amely az asztalt VDI-alapúként jelzi.
- A következő parancs végrehajtása a kijelentkezési parancsfájl részeként. Ez a parancs elindítja a legjobb erőfeszítés hívás az Azure AD az eszköz törléséhez.
   - Windows-alapú, nem vízszintes eszközök esetén – autoworkplace.exe /leave
- Az [elavult eszközök kezelésére](manage-stale-devices.md)szolgáló folyamat meghatározása és megvalósítása.
   - Miután rendelkezik egy stratégiával a nem állandó hibrid Azure AD-hez csatlakozott eszközök azonosítására, agresszívebb lehet ezen eszközök karbantartása során, hogy a címtár ne kerüljön igénybe sok elavult eszközzel.
 
## <a name="next-steps"></a>További lépések

[Hibrid Azure Active Directory-csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
