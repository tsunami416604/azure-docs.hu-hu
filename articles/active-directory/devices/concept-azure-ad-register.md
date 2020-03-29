---
title: Mik azok az Azure AD regisztrált eszközök?
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462758"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-ben regisztrált eszközök

Az Azure AD-ben regisztrált eszközök célja, hogy a felhasználók számára támogatást nyújtson a Saját eszköz (BYOD) vagy a mobileszköz-forgatókönyvek. Ezekben a forgatókönyvekben a felhasználó hozzáférhet a szervezet Azure Active Directory által vezérelt erőforrások személyes eszköz használatával.

|   | Azure AD regisztrálva |
| --- | --- |
| **Meghatározás** | Az Azure AD-re regisztrálva anélkül, hogy szervezeti fiókra lenne szükség az eszközre való bejelentkezéshez |
| **Elsődleges célközönség** | Minden felhasználóra érvényes, a következő kritériumokkal: |
|   | Saját eszközök használata (BYOD) |
|   | Mobileszközök |
| **Az eszközök tulajdonjoga** | Felhasználó vagy szervezet |
| **Operációs rendszerek** | Windows 10, iOS, Android és MacOS |
| **Telepítés** | Windows 10 – Beállítások |
|   | iOS/Android – Céges portál vagy Microsoft Hitelesítő alkalmazás |
|   | MacOS – Vállalati portál |
| **Eszközbejelentkezési beállítások** | Végfelhasználó helyi hitelesítő adatai |
|   | Jelszó |
|   | Windows Hello |
|   | PIN kód |
|   | Biometria vagy minta más eszközökhöz |
| **Eszközfelügyelet** | Mobileszköz-kezelés (például Microsoft Intune) |
|   | Mobilalkalmazás-kezelés |
| **Főbb képességek** | SSO a felhőalapú erőforrásokhoz |
|   | Feltételes hozzáférés az Intune-ba való belépéskor |
|   | Feltételes hozzáférés az alkalmazásvédelmi szabályzaton keresztül |
|   | Telefonos bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással |

![Azure AD-ben regisztrált eszközök](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Az Azure AD regisztrált eszközei egy helyi fiókkal, például egy Windows 10-es eszközön lévő Microsoft-fiókkal vannak bejelentkezve, de emellett rendelkeznek egy Azure AD-fiókkal a szervezeti erőforrásokhoz való hozzáféréshez. A szervezet erőforrásaihoz való hozzáférés tovább korlátozható az adott Azure AD-fiók és az eszközidentitásra alkalmazott feltételes hozzáférési szabályzatok alapján.

A rendszergazdák biztonságossá tehetik és tovább szabályozhatják ezeket az Azure AD-regisztrált eszközöket a Mobileszköz-kezelés (MDM) eszközeivel, például a Microsoft Intune-nal. Az MDM lehetővé teszi a szervezet által igényelt konfigurációk érvényesítését, például a tárolás titkosítását, a jelszó összetettségét és a biztonsági szoftverek naprakészen tartását. 

Az Azure AD-regisztráció akkor hajtható végre, amikor először fér hozzá egy munkaalkalmazáshoz, vagy manuálisan a Windows 10 Beállítások menüjében. 

## <a name="scenarios"></a>Forgatókönyvek

A szervezet egyik felhasználója szeretné elérni az e-mailhez szükséges eszközöket, a munkaidő-jelentési időt és az otthoni számítógépről való regisztráció előnyeit. A szervezet rendelkezik ezekkel az eszközökkel egy feltételes hozzáférési szabályzat mögött, amely hozzáférést igényel egy Intune-kompatibilis eszközről. A felhasználó hozzáadja a szervezeti fiókot, és regisztrálja az otthoni pc az Azure AD-vel, és a szükséges Intune-szabályzatok kényszerítve, amely a felhasználó számára hozzáférést biztosít az erőforrásokhoz.

Egy másik felhasználó szeretné elérni a szervezeti e-mail a személyes Android telefon, amely gyökerezik. A vállalatnak szüksége van egy megfelelő eszközre, és létrehozott egy Intune megfelelőségi szabályzatot a gyökeres eszközök blokkolásához. Az alkalmazott nem férhet hozzá az eszköz szervezeti erőforrásaihoz.

## <a name="next-steps"></a>További lépések

- [Eszközidentitások kezelése az Azure Portal használatával](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
