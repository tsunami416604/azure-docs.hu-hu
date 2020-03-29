---
title: Mi az a hibrid Azure AD-hez csatlakozott eszköz?
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512249"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-csatlakoztatott eszközök

Több mint egy évtizede számos cég használja a tartományi csatlakoztatást a helyszíni Active Directoryhoz, mivel az a következő előnyökkel jár:

- Az informatikai részlegek egy központi helyről felügyelhetik a vállalati tulajdonú eszközöket.
- A felhasználók bejelentkezhetnek az eszközeiken a munkahelyi vagy iskolai Active Directory-fiókokkal.

A helyszíni alaphelettel rendelkező szervezetek általában képalkotó módszerekre támaszkodnak az eszközök kiépítéséhez, és gyakran a **Configuration Manager** vagy **a csoportházirend (GP)** segítségével kezelik őket.

Ha az Ön környezetének van helyszíni AD-lábnyoma, ugyanakkor az Azure Active Directory nyújtotta lehetőségeket is ki szeretné használni, implementálhat hibrid Azure AD-csatlakoztatott eszközöket. Ezek az eszközök olyan eszközök, amelyek a helyszíni Active Directoryhoz csatlakoznak, és regisztrálva vannak az Azure Active Directoryban.

|   | Hibrid Azure AD-csatlakozás |
| --- | --- |
| **Meghatározás** | Csatlakozott a helyszíni AD-hez és az Azure AD-hez, amely nek szüksége van a szervezeti fiókra az eszközre való bejelentkezéshez |
| **Elsődleges célközönség** | Meglévő helyszíni AD-infrastruktúrával rendelkező hibrid szervezetek számára is alkalmas |
|   | A szervezet összes felhasználója számára alkalmazható |
| **Az eszközök tulajdonjoga** | Szervezet |
| **Operációs rendszerek** | Windows 10, 8.1 és 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 és 2019 |
| **Telepítés** | Windows 10, Windows Server 2016/2019 |
|   | Tartományhoz való csatlakozás informatikai és automatikus csatlakozáshoz az Azure AD Connect vagy az ADFS konfiguráción keresztül |
|   | Tartományhoz való csatlakozás a Windows Autopilot szolgáltatással és automatikus csatlakozás az Azure AD Connect vagy az ADFS konfiguráción keresztül |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2 – MSI megkövetelése |
| **Eszközbejelentkezési beállítások** | Szervezeti fiókok a következő használatával: |
|   | Jelszó |
|   | Windows Win Vállalati verzió, Hello 10 |
| **Eszközfelügyelet** | Csoportházirend |
|   | A Configuration Manager önálló vagy közös kezelés a Microsoft Intune-nal |
| **Főbb képességek** | Egyszeri szolgáltató a felhőbeli és a helyszíni erőforrásokhoz |
|   | Feltételes hozzáférés a tartományhoz való csatlakozáson keresztül vagy az Intune-on keresztül, ha közösen kezelt |
|   | Önkiszolgáló jelszó-visszaállítás és a Windows Hello PIN-kód visszaállítása a zárolási képernyőn |
|   | Vállalati állapotroaming különböző eszközökön |

![Hibrid Azure AD-csatlakoztatott eszközök](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Forgatókönyvek

Azure AD hibrid összekapcsolt eszközök használata, ha:

- Ha Win32-alkalmazások vannak telepítve azokon az eszközökön, amelyek az Active Directory-géphitelesítést használják.
- Továbbra is a csoportházirenddel szeretné kezelni az eszköz konfigurációját.
- Továbbra is a meglévő képalkotó megoldásokat szeretné használni az eszközök telepítéséhez és konfigurálásához.
- A Windows 10 mellett támogatnia kell az olyan rendszerű Windows 7- és 8.1-es eszközöket is,

## <a name="next-steps"></a>További lépések

- [A hibrid Azure AD-csatlakozás implementálásának megtervezése](hybrid-azuread-join-plan.md)
- [Eszközidentitások kezelése az Azure Portal használatával](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
