---
title: Mik az Azure ad-ben regisztrált eszközök?
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462758"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-ben regisztrált eszközök

Az Azure ad-ben regisztrált eszközök célja, hogy adja meg a felhasználók a Bring Your Own Device (BYOD) vagy a mobileszközökön futtatott forgatókönyvekre támogatását. Ezekben az esetekben egy felhasználó hozzáférhessen a szervezet Azure Active Directory szabályozott erőforrások egy személyes eszköz használatával.

|   | Az Azure AD-ban regisztrálva |
| --- | --- |
| **Definíció** | Az Azure ad-hez anélkül, hogy a szervezeti fiókkal való bejelentkezéshez az eszköz regisztrálva |
| **Elsődleges célközönséget** | Mindenki számára a következő feltételek érvényesek: |
|   | Saját eszközök használata (BYOD) |
|   | Mobileszközök |
| **Eszközök tulajdonjoga** | Felhasználó vagy a szervezet |
| **Operációs rendszerek** | A Windows 10, iOS, Android és MacOS |
| **Kiépítés** | A Windows 10 – beállítások |
|   | iOS/Android – vállalati portálon vagy a Microsoft Authenticator alkalmazás |
|   | MacOS – vállalati portál |
| **Eszköz bejelentkezési lehetőségek** | A végfelhasználói helyi hitelesítő adatokkal |
|   | Jelszó |
|   | Windows Hello |
|   | PIN |
|   | A biometria vagy más eszközök esetében a minta |
| **Eszközkezelés** | Mobileszköz-kezelés (Példa: A Microsoft Intune-nal) |
|   | Mobilalkalmazás-kezelés |
| **Főbb képességek** | Egyszeri bejelentkezés a felhőbeli erőforrások |
|   | Feltételes hozzáférés Intune-ban való regisztrálásakor |
|   | Feltételes hozzáférés az alkalmazásvédelmi szabályzat-n keresztül |
|   | Lehetővé teszi, hogy a telefonos bejelentkezés a Microsoft Authenticator alkalmazás |

![Azure AD-ben regisztrált eszközök](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Az Azure AD-ban regisztrálva eszközök, például a Microsoft-fiók egy helyi fiók használata a Windows 10-es eszközön jelentkezett be, de emellett rendelkezik a szervezeti erőforrásokhoz való hozzáférés csatolt Azure AD-fiókkal. A szervezet erőforrásaihoz való hozzáférés alapján, hogy az Azure AD-fiókot további korlátozott és a feltételes hozzáférési szabályzatokat az eszközidentitást a alkalmazni lehet.

A rendszergazdák biztonságos és további szabályozására, ha ezek az Azure ad-ben regisztrált eszközök mobileszköz-felügyeleti (MDM) eszközök, mint a Microsoft Intune használatával. Mobileszköz-kezelési kényszeríthet konfigurációkat szervezet által megkövetelt, például a tárolás titkosítását, és frissített tartott biztonsági szoftver lehetővé teszi. 

Az Azure AD-regisztrációs is elvégezhető, amikor egy munkahelyi alkalmazás első használatakor, illetve manuálisan, a Windows 10-es beállítások menü segítségével éri el. 

## <a name="scenarios"></a>Forgatókönyvek

A szervezet egy felhasználója szeretne hozzáférni e-mailek, az eszközök reporting-szabadságolási és előnyöket Regisztrálás az otthoni számítógépről. A szervezet rendelkezik ezekkel az eszközökkel egy feltételes hozzáférési szabályzatot az Intune megfelelő eszközről hozzáférést igénylő mögött. A felhasználó hozzáadja a szervezeti fiókjával, és otthoni számítógép regisztrálása az Azure ad-vel, és a szükséges az Intune házirendek érvényben vannak, a felhasználói hozzáférést ad az erőforrásokat.

Egy másik felhasználót szeretne hozzáférni vállalati levelezésükhöz a személyes Android telefont, hogy rootolt. A vállalati van szükség a megfelelő eszköz, és létrehozta az Intune eszközmegfelelőségi szabályzataival bármely rootolt eszközök letiltásához. Az alkalmazott le van állítva, az ezen az eszközön a céges erőforrások elérését.

## <a name="next-steps"></a>További lépések

- [Az Azure portal használatával eszközidentitások kezelése](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure ad-ben](manage-stale-devices.md)
