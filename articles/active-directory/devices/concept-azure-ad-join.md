---
title: Mi az Azure AD-hez csatlakozott eszköz?
description: Ismerje meg, hogyan segíthet az eszközidentitás-kezelés a környezeterőforrásait elérő eszközök kezelésében.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672680"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-hez csatlakoztatott eszközök

Az Azure AD-csatlakozás olyan szervezetek számára készült, amelyek csak felhőalapú vagy csak felhőalapú akarnak lenni. Bármely szervezet telepítheti az Azure AD-hez csatlakozó eszközöket, méretétől és iparágátattól függetlenül. Az Azure AD-csatlakozás még hibrid környezetben is működik, lehetővé téve a felhőbeli és helyszíni alkalmazásokhoz és erőforrásokhoz való hozzáférést.

|   | Azure AD-csatlakozás |
| --- | --- |
| **Meghatározás** | Csak az Azure AD-hez csatlakozott, amely nek szüksége van szervezeti fiókra az eszközre való bejelentkezéshez |
| **Elsődleges célközönség** | Alkalmas mind a felhő-csak és hibrid szervezetek. |
|   | A szervezet összes felhasználója számára alkalmazható |
| **Az eszközök tulajdonjoga** | Szervezet |
| **Operációs rendszerek** | Minden Windows 10-es eszköz |
| **Telepítés** | Önkiszolgáló: Windows OOBE vagy Beállítások |
|   | Csoportos regisztráció |
|   | Windows Autopilot |
| **Eszközbejelentkezési beállítások** | Szervezeti fiókok a következő használatával: |
|   | Jelszó |
|   | Vállalati Windows Hello |
|   | FIDO2.0 biztonsági kulcsok (előzetes verzió) |
| **Eszközfelügyelet** | Mobileszköz-kezelés (például Microsoft Intune) |
|   | Közös kezelés a Microsoft Intune-nal és a Microsoft Endpoint Configuration Managerrel |
| **Főbb képességek** | Egyszeri szolgáltató a felhőbeli és a helyszíni erőforrásokhoz |
|   | Feltételes hozzáférés az MDM-regisztrációés az MDM-megfelelőség értékelése révén |
|   | Önkiszolgáló jelszó-visszaállítás és a Windows Hello PIN-kód visszaállítása a zárolási képernyőn |
|   | Vállalati állapotroaming különböző eszközökön |

Az Azure AD-hez csatlakozott eszközök egy szervezeti Azure AD-fiók használatával vannak bejelentkezve. A szervezet erőforrásaihoz való hozzáférés tovább korlátozható az adott Azure AD-fiók és az eszközidentitásra alkalmazott [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) alapján.

A rendszergazdák biztonságossá és tovább vezérelhetik az Azure AD-hez csatlakozó eszközöket a Mobileszköz-kezelés (MDM) eszközeivel, például a Microsoft Intune-nal vagy a Microsoft Endpoint Configuration Manager használatával. Ezek az eszközök lehetővé tették a szervezet által igényelt konfigurációk érvényesítését, például a tárolás titkosítását, a jelszó összetettségét, a szoftvertelepítéseket és a szoftverfrissítéseket. A rendszergazdák a Configuration Manager segítségével elérhetővé tehetik a szervezeti alkalmazásokat az Azure AD-hez csatlakozott eszközök számára [a Microsoft Store vállalati és oktatási verzióinak kezeléséhez.](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)

Az Azure AD-csatlakozás önkiszolgáló beállításokkal valósítható meg, mint például a Kezdő élmény (OOBE), a tömeges regisztráció vagy a [Windows Autopilot.](/intune/enrollment-autopilot)

Az Azure AD-hez csatlakozó eszközök továbbra is fenntarthatnak egyszeri bejelentkezési hozzáférést a helyszíni erőforrásokhoz, amikor a szervezet hálózatán vannak. Az Azure AD-hez csatlakozott eszközök továbbra is hitelesíthetik magukat a helyszíni kiszolgálókon, például a fájl- és nyomtatási és egyéb alkalmazásokban.

## <a name="scenarios"></a>Forgatókönyvek

Habár az Azure AD elsősorban az olyan cégek számára készült, amelyek nem rendelkeznek helyszíni Windows Server Active Directory-infrastruktúrával, a szolgáltatás a következő helyzetekben is használható:

- Ha váltani kíván egy felhőalapú infrastruktúrára az Azure AD és egy mobileszköz-felügyeleti megoldás, például az Intune használatával.
- Ha nem használhat helyszíni tartománycsatlakoztatást, például ha mobileszközöket, táblagépeket és telefonokat szeretne felügyelet alá vonni.
- Ha a felhasználóknak elsősorban Office 365- vagy egyéb Azure AD-ben integrált SaaS-alkalmazásokhoz kell hozzáférnie.
- Ha felhasználók egy csoportját az Azure AD-ben szeretné felügyelni az Active Directory helyett. Ez a forgatókönyv vonatkozhat például idénymunkásokra, vállalkozókra vagy diákokra.
- Ha csatlakozási lehetőséget kíván nyújtani a korlátozott helyszíni infrastruktúrájú távoli fiókirodákban tartózkodó felhasználóknak.

Az Azure AD-csatlakoztatott eszközök csak Windows 10 rendszerű eszközök lehetnek.

Az Azure AD-hez csatlakoztatott eszközök leegyszerűsítik:

- A Windows üzembe helyezését a vállalati tulajdonú eszközökön
- A hozzáférést a vállalati alkalmazásokhoz és erőforrásokhoz bármilyen Windows-eszközről
- A vállalati tulajdonú eszközök felhőalapú felügyeletét
- A felhasználók bejelentkeznek az eszközeikre az Azure AD-vel, vagy szinkronizált Active Directory-munkahelyi vagy iskolai fiókokkal.

![Azure AD-hez csatlakoztatott eszközök](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Az alábbi módszerekkel létesíthet Azure AD-csatlakozást:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Tömeges üzembe helyezés](/intune/windows-bulk-enroll)
- [Önkiszolgáló megoldás használata](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>További lépések

- [Az Azure AD-csatlakozás implementálásának megtervezése](azureadjoin-plan.md)
- [A helyi rendszergazdák csoport kezelése az Azure AD-hez csatlakozó eszközökön](assign-local-admin.md)
- [Eszközidentitások kezelése az Azure Portal használatával](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
