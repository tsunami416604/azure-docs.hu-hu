---
title: Mi a hibrid Azure ad-ben csatlakoztatott eszköz?
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462745"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-csatlakoztatott eszközök

Több mint egy évtizede számos cég használja a tartományi csatlakoztatást a helyszíni Active Directoryhoz, mivel az a következő előnyökkel jár:

- Az informatikai részlegek egy központi helyről felügyelhetik a vállalati tulajdonú eszközöket.
- A felhasználók bejelentkezhetnek az eszközeiken a munkahelyi vagy iskolai Active Directory-fiókokkal.

A helyszíni lábnyommal rendelkező cégek általában rendszerképkezelő megoldásokat használnak az eszközök üzembe helyezéséhez, és a **System Center Configuration Manager (SCCM)** vagy **csoportszabályzatok (GP)** használatával felügyelik azokat.

Ha az Ön környezetének van helyszíni AD-lábnyoma, ugyanakkor az Azure Active Directory nyújtotta lehetőségeket is ki szeretné használni, implementálhat hibrid Azure AD-csatlakoztatott eszközöket. Ezeken az eszközökön, amelyek a helyszíni Active Directoryhez csatlakozik, és az Azure Active Directoryban regisztrált eszközökön is.

|   | Hibrid Azure AD-csatlakozás |
| --- | --- |
| **Definíció** | Csatlakozik a helyszíni AD és az Azure AD, igénylő szervezeti fiókkal bejelentkezni az eszköz |
| **Elsődleges célközönséget** | Megfelelő hibrid cégekhez és vállalkozásokhoz a meglévő helyszíni AD-infrastruktúrát |
|   | A szervezet összes felhasználójára |
| **Eszközök tulajdonjoga** | Szervezet |
| **Operációs rendszerek** | A Windows 10, 8.1 és a 7 |
|   | A Windows Server 2008/R2, 2012/R2, 2016 és a 2019 |
| **Kiépítés** | Windows 10, Windows Server 2016/2019 |
|   | A tartományhoz való csatlakozás informatikai és az automatikus illesztés keresztül az Azure AD Connect vagy az ADFS-config |
|   | Windows Autopilot és az automatikus illesztés keresztül az Azure AD Connect és az AD FS konfigurációs csatlakozás tartományhoz |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2 - MSI megkövetelése |
| **Eszköz bejelentkezési lehetőségek** | Szervezeti fiókok használatával: |
|   | Jelszó |
|   | Win10 a vállalati Windows Hello |
| **Eszközkezelés** | Csoportházirend |
|   | A System Center Configuration Manager önálló vagy a Microsoft Intune-nal közös felügyelet |
| **Főbb képességek** | Egyszeri bejelentkezés a felhőbeli és a helyszíni erőforrásokhoz |
|   | Feltételes hozzáférés csatlakozás tartományhoz vagy az Intune-ban, ha egy felügyelt |
|   | Az önkiszolgáló jelszó-visszaállítási és a Windows Hello PIN-kód alaphelyzetbe állítása a zárolási képernyőn |
|   | Enterprise State Roaming eszközök között |

![Hibrid Azure AD-csatlakoztatott eszközök](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Forgatókönyvek

Ha használja a hibrid Azure AD-hez csatlakoztatott eszközök:

- Ha Win32-alkalmazások vannak telepítve azokon az eszközökön, amelyek az Active Directory-géphitelesítést használják.
- Eszköz konfigurációjának kezelése csoportházirend használatával továbbra is szeretné.
- Szeretne továbbra is használhatja a meglévő lemezkép-készítési megoldások üzembe helyezését, és konfigurálhatja az eszközöket.
- Támogatnia kell a régebbi verziójú Windows 7 és Windows 10-es mellett 8.1 rendszerű eszközök

## <a name="next-steps"></a>További lépések

- [A hibrid Azure AD-csatlakozás implementálásának megtervezése](hybrid-azuread-join-plan.md)
- [Az Azure portal használatával eszközidentitások kezelése](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure ad-ben](manage-stale-devices.md)
