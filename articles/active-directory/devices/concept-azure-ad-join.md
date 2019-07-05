---
title: Mi az Azure AD-csatlakoztatott eszközök?
description: Ismerje meg, hogyan eszköz identity management segítségével kezelheti az eszközöket, amelyek a környezetében erőforrásokat érnek el.
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462810"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-hez csatlakoztatott eszközök

Az Azure AD join rendszer-és felhőközpontú, vagy csak felhőalapú igénylő szervezeteknek szól. Minden olyan szervezet üzembe helyezhet az Azure AD-csatlakoztatott eszközök, függetlenül attól, hogy a méretének vagy iparági. Az Azure AD join működik, akár hibrid környezetben, felhőbeli és a helyszíni alkalmazásokhoz és erőforrásokhoz való hozzáférés engedélyezése.

|   | Azure AD-csatlakozás |
| --- | --- |
| **Definíció** | Csak az Azure AD igénylő szervezeti fiókkal való bejelentkezéshez az eszköz csatlakoztatva |
| **Elsődleges célközönséget** | Megfelelő mindkét csak felhőalapú és hibrid szervezetek számára. |
|   | A szervezet összes felhasználójára |
| **Eszközök tulajdonjoga** | Szervezet |
| **Operációs rendszerek** | Minden Windows 10-eszközök |
| **Kiépítés** | Önkiszolgáló: Windows OOBE vagy beállításai |
|   | Tömeges regisztrálás |
|   | Windows Autopilot |
| **Eszköz bejelentkezési lehetőségek** | Szervezeti fiókok használatával: |
|   | Jelszó |
|   | Vállalati Windows Hello |
|   | FIDO2.0 biztonsági kulcsok (előzetes verzió) |
| **Eszközkezelés** | Mobileszköz-kezelés (Példa: A Microsoft Intune-nal) |
|   | Közös felügyelet a Microsoft Intune és System Center Configuration Managerben |
| **Főbb képességek** | Egyszeri bejelentkezés a felhőbeli és a helyszíni erőforrásokhoz |
|   | MDM-regisztráció és a mobileszköz-kezelési a megfelelés kiértékelésének feltételes hozzáférés |
|   | Az önkiszolgáló jelszó-visszaállítási és a Windows Hello PIN-kód alaphelyzetbe állítása a zárolási képernyőn |
|   | Enterprise State Roaming eszközök között |

Az Azure AD-csatlakoztatott eszközök be vannak jelentkezve a szervezeti használatával az Azure AD-fiókot. Lehet, hogy a szervezet erőforrásaihoz való hozzáférés további korlátozott alapján, hogy az Azure AD-fiókot és [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) alkalmazza az eszközidentitást.

Biztonságossá teheti a rendszergazdák és -vezérlés az Azure AD további hez csatlakoztatott eszközök mobileszköz-felügyeleti (MDM) eszközökkel, mint a Microsoft Intune vagy a System Center Configuration Managerrel megosztott kezelési helyzetekben. Ezek az eszközök, amelyikkel a szervezet által megkövetelt konfigurációk, például a tárolás titkosítását, jelszó összetettségét, szoftvertelepítések és szoftverfrissítéseket igénylő kényszerítésére. A rendszergazdák számára is elérhetővé szervezeti alkalmazások használata az Azure AD-csatlakoztatott eszközök [System Center Configuration Manager és a Microsoft Store vállalatoknak](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Az Azure AD join hasonlóan a kívüli kezdőélmény (OOBE), csoportos regisztráció, önkiszolgáló-beállítások használatával is elvégezhető, vagy [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Az Azure AD-csatlakoztatott eszközök továbbra is megtarthatja egyszeri bejelentkezéses hozzáférést a helyszíni erőforrásokhoz, amelyek a szervezet hálózatán. Az Azure AD-hez csatlakoztatott eszközök továbbra is hitelesítheti magát a helyszíni kiszolgálók, például a fájl, nyomtatás és más alkalmazások.

## <a name="scenarios"></a>Forgatókönyvek

Habár az Azure AD elsősorban az olyan cégek számára készült, amelyek nem rendelkeznek helyszíni Windows Server Active Directory-infrastruktúrával, a szolgáltatás a következő helyzetekben is használható:

- Ha váltani kíván egy felhőalapú infrastruktúrára az Azure AD és egy mobileszköz-felügyeleti megoldás, például az Intune használatával.
- Ha nem használhat helyszíni tartománycsatlakoztatást, például ha mobileszközöket, táblagépeket és telefonokat szeretne felügyelet alá vonni.
- Ha a felhasználóknak elsősorban Office 365- vagy egyéb Azure AD-ben integrált SaaS-alkalmazásokhoz kell hozzáférnie.
- Ha felhasználók egy csoportját az Azure AD-ben szeretné felügyelni az Active Directory helyett. Ebben a forgatókönyvben alkalmazhatja, például idénymunkásoknak, alvállalkozók és diákok.
- Ha csatlakozási lehetőséget kíván nyújtani a korlátozott helyszíni infrastruktúrájú távoli fiókirodákban tartózkodó felhasználóknak.

Az Azure AD-csatlakoztatott eszközök csak Windows 10 rendszerű eszközök lehetnek.

Az Azure AD-hez csatlakoztatott eszközök leegyszerűsítik:

- A Windows üzembe helyezését a vállalati tulajdonú eszközökön
- A hozzáférést a vállalati alkalmazásokhoz és erőforrásokhoz bármilyen Windows-eszközről
- A vállalati tulajdonú eszközök felhőalapú felügyeletét
- Jelentkezzen be az Azure AD-vel eszközeiket, hogy a felhasználók vagy a szinkronizált Active Directory munkahelyi vagy iskolai fiókokat.

![Azure AD-hez csatlakoztatott eszközök](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Az alábbi módszerekkel létesíthet Azure AD-csatlakozást:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Tömeges üzembe helyezés](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Önkiszolgáló megoldás használata](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>További lépések

- [Az Azure AD join implementáció megtervezésébe](azureadjoin-plan.md)
- [A helyi Rendszergazdák csoport kezelése az Azure AD-hez csatlakoztatott eszközök](assign-local-admin.md)
- [Az Azure portal használatával eszközidentitások kezelése](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure ad-ben](manage-stale-devices.md)
