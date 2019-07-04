---
title: Mi az eszközidentitást az Azure Active Directoryban?
description: Ismerje meg, hogyan eszköz identity management segítségével kezelheti az eszközöket, amelyek a környezetében erőforrásokat érnek el.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d38ca8bdf93ff201b3f5842f4cb0e8409dcd0c3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481678"
---
# <a name="what-is-a-device-identity"></a>Mi az az eszközidentitás?

Minden bármilyen típusú és a méretek és a Bring Your Own Device (BYOD) fogalmát eszközök elterjedése informatikai szakemberek számára két némileg ellentétes céljait néz szembe:

- Lehetővé teszi a munkavégzéshez, bárhol is legyenek, és minden alkalommal, amikor a végfelhasználók számára
- A szervezet eszközeinek védelme

Ezek az eszközök védelme érdekében informatikai személyzetet tart fenn kell először a az eszközidentitások kezelése. INFORMATIKAI részleg az eszköz identitással rendelkező eszközök, mint például a Microsoft Intune-t a biztonsági és megfelelőségi előírások teljesítése hozhat létre. Az Azure Active Directory (Azure AD) lehetővé teszi, hogy egyszeri bejelentkezést az eszközöket, alkalmazásokat és szolgáltatásokhoz bárhonnan keresztül ezeket az eszközöket.

- A felhasználók is hozzáférhetnek a szervezet eszközökre van szükségük. 
- Az informatikai részleg a vezérlők szükségük van a szervezet biztonságának beolvasása.

Eszköz Identitáskezelés alapját képezi [eszközalapú feltételes hozzáférési](../conditional-access/require-managed-devices.md). Eszközalapú feltételes hozzáférési szabályzatokkal biztosíthatja, hogy a környezetben lévő erőforrások elérése csak akkor lehetséges, a felügyelt eszközökkel.

## <a name="getting-devices-in-azure-ad"></a>Bevezetés az eszközök az Azure ad-ben

Egy eszköz beolvasni az Azure ad-ben, több lehetősége van:

- **Az Azure AD-ban regisztrálva**
   - Azure ad-ben regisztrált eszközök általában személyes vagy mobil eszközökhöz, és személyes Microsoft-fiókkal vagy egy másik helyi fiókkal jelentkezett be.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD-hez**
   - Az Azure AD-hez csatlakoztatott eszközök egy szervezet tulajdonában vannak, és be van jelentkezve a egy olyan Azure ad-ben, hogy a szervezethez tartozó fiókkal. Csak a felhőben is léteznek.
      - Windows 10 
- **csatlakozik a Hibrid Azure AD-hez**
   - Eszközök, amelyek hibrid Azure AD-hez egy szervezet tulajdonában vannak, és be van jelentkezve a egy olyan Azure ad-ben, hogy a szervezethez tartozó fiókkal. Léteznek, a felhőben és a 
      - Windows 7, 8.1 és 10
      - Windows Server 2008 vagy újabb

![Azure AD-Eszközök panelen megjelenő eszközök](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Eszközfelügyelet

Az Azure AD-eszközök mobileszköz-felügyeleti (MDM) eszközök, mint például a Microsoft Intune, System Center Configuration Managerben, Csoportházirend (hibrid Azure AD-csatlakozás), mobilalkalmazás-felügyeleti (MAM) eszközök vagy más harmadik felektől származó eszközök felügyelhetők.

## <a name="resource-access"></a>Erőforrás-hozzáférés

Regisztrálás és csatlakozás a felhasználók, közvetlen bejelentkezés (SSO) a felhőalapú erőforrások és a rendszergazdák feltételes hozzáférési szabályzatok alkalmazása az érintett erőforrásokra lehetővé teszi számára. 

Az Azure AD-hez csatlakoztatott eszközök vagy hibrid Azure AD tartományhoz profitál az egyszeri bejelentkezés a szervezet a helyszíni erőforrásokhoz, valamint a felhőbeli erőforrások. További információ a cikkben található [hogyan a helyszíni erőforrások működik az Azure AD SSO-hez csatlakoztatott eszközök](azuread-join-sso.md).

## <a name="device-security"></a>Eszközbiztonság

- **Az Azure ad-ben regisztrált eszközök** használják a felhasználó által kezelt fiókkal, ezt a fiókot a Microsoft-fiókkal vagy egy másik helyileg kezelt hitelesítő adatok közül egy vagy több, a következő védett.
   - Jelszó
   - PIN
   - Pattern
   - Windows Hello
- **Azure AD-hez vagy a hibrid Azure AD-hez csatlakoztatott eszközök** szervezeti fiókkal használják az Azure AD által védett egy vagy több, a következő.
   - Jelszó
   - Vállalati Windows Hello

## <a name="provisioning"></a>Kiépítés

Az Azure AD-eszközök használata végezhető, önkiszolgáló módon vagy ellenőrzött kiépítési folyamat által a rendszergazdák.

## <a name="summary"></a>Összefoglalás

Az eszköz identity management az Azure ad-ben a következőket teheti:

- Történő visszaállítását, és az Azure AD-eszközök kezelésének leegyszerűsítése
- Egyszerű hozzáférést nyújt a felhasználóknak a vállalat felhőbeli erőforrásaihoz

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure ad-ben regisztrált eszközök](concept-azure-ad-register.md)
- Tudjon meg többet [az Azure AD-hez csatlakoztatott eszközök](concept-azure-ad-join.md)
- Tudjon meg többet [hibrid Azure AD-hez csatlakoztatott eszközök](concept-azure-ad-join-hybrid.md)
- Az Azure Portalon eszközidentitások kezelése áttekintést kaphat, lásd: [az Azure portal használatával eszközidentitások kezelése](device-management-azure-portal.md).
- Eszközalapú feltételes hozzáférési kapcsolatos további információkért lásd: [eszközalapú feltételes hozzáférési szabályzatokat az Azure Active Directory konfigurálása](../conditional-access/require-managed-devices.md).
