---
title: Mi az Azure AD-ban regisztrált eszközök?
description: Megtudhatja, hogyan segítheti a környezet erőforrásaihoz hozzáférő eszközök felügyeletét.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67462758"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-ben regisztrált eszközök

Az Azure AD-ban regisztrált eszközök célja, hogy a felhasználók számára támogatást nyújtson a saját eszközök használata (BYOD) vagy a mobileszköz-forgatókönyvek számára. Ezekben a forgatókönyvekben a felhasználók személyes eszköz használatával férhetnek hozzá a szervezet Azure Active Directory felügyelt erőforrásaihoz.

|   | Azure AD-regisztráció |
| --- | --- |
| **Meghatározás** | Regisztrálva van az Azure AD-be anélkül, hogy szervezeti fiókot kellene bejelentkeznie az eszközre |
| **Elsődleges célközönség** | Az összes felhasználóra érvényes a következő feltételekkel: |
|   | Saját eszközök használata (BYOD) |
|   | Mobileszközök |
| **Az eszközök tulajdonjoga** | Felhasználó vagy szervezet |
| **Operációs rendszerek** | Windows 10, iOS, Android és MacOS |
| **Telepítés** | Windows 10 – beállítások |
|   | iOS/Android – Céges portál vagy Microsoft Authenticator alkalmazás |
|   | MacOS – Céges portál |
| **Eszköz bejelentkezési beállításai** | Végfelhasználói helyi hitelesítő adatok |
|   | Jelszó |
|   | Windows Hello |
|   | PIN kód |
|   | Biometria vagy minta más eszközökhöz |
| **Eszközfelügyelet** | Mobileszköz-kezelés (példa: Microsoft Intune) |
|   | Mobilalkalmazás-kezelés |
| **Főbb képességek** | Egyszeri bejelentkezés a Felhőbeli erőforrásokhoz |
|   | Feltételes hozzáférés az Intune-ban való regisztráláskor |
|   | Feltételes hozzáférés az App Protection-szabályzaton keresztül |
|   | Telefonos bejelentkezés engedélyezése Microsoft Authenticator alkalmazással |

![Azure AD-ben regisztrált eszközök](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Az Azure AD-ban regisztrált eszközök bejelentkeznek helyi fiókkal, például egy Windows 10-es eszközön lévő Microsoft-fiókre, de a szervezeti erőforrásokhoz való hozzáféréshez hozzá kell férniük egy Azure AD-fiókhoz is. A szervezet erőforrásaihoz való hozzáférés az Azure AD-fiók és az eszköz identitására alkalmazott feltételes hozzáférési szabályzatok alapján tovább korlátozható.

A rendszergazdák a mobileszköz-kezelési (MDM) eszközök, például a Microsoft Intune használatával védhetik és vezérelhetik ezeket az Azure AD-beli regisztrált eszközöket. A MDM lehetővé teszi olyan szervezeti konfigurációk betartatását, mint például a tárolás titkosítása, a jelszó bonyolultsága és a biztonsági szoftverek naprakészen tartása. 

Az Azure AD-regisztráció akkor végezhető el, amikor első alkalommal fér hozzá egy munkahelyi alkalmazáshoz, vagy manuálisan használja a Windows 10 beállítások menüt. 

## <a name="scenarios"></a>Forgatókönyvek

A szervezet egyik felhasználója szeretné elérni az e-mailek, a jelentéskészítési időkorlát és az otthoni SZÁMÍTÓGÉPRŐL való regisztráció előnyeit. A szervezete ezeket az eszközöket egy olyan feltételes hozzáférési szabályzat mögé írja, amely egy Intune-kompatibilis eszköz hozzáférését igényli. A felhasználó hozzáadja a szervezeti fiókját, és regisztrálja az otthoni SZÁMÍTÓGÉPét az Azure AD-ben, és a szükséges Intune-szabályzatok érvénybe lép, hogy a felhasználók hozzáférhessenek az erőforrásaihoz.

Egy másik felhasználó szeretné elérni a szervezeti e-mail-címét a személyes androidos telefonján, amely fel van gyökerezve. A vállalatának megfelelő eszközre van szüksége, és létrehozott egy Intune-megfelelőségi szabályzatot a feltört eszközök blokkolásához. Az alkalmazott nem fér hozzá a szervezeti erőforrásokhoz az eszközön.

## <a name="next-steps"></a>További lépések

- [Az eszközök identitásának kezelése a Azure Portal használatával](device-management-azure-portal.md)
- [Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)
