---
title: Mi az eszközidentitás az Azure Active Directoryban?
description: Ismerje meg, hogyan segíthet az eszközidentitás-kezelés a környezeterőforrásait elérő eszközök kezelésében.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba0630474224c34eb96429cd7592028362e03381
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024438"
---
# <a name="what-is-a-device-identity"></a>Mi az az eszközidentitás?

A különböző formájú és méretű eszközök elterjedésével és a Bring Your Own Device (BYOD) koncepcióval az informatikai szakemberek két, kissé ellentétes céllal szembesülnek:

- Lehetővé teszi a végfelhasználók számára, hogy bárhol és bármikor termelékenyek legyenek
- A szervezet eszközeinek védelme

Ezek az eszközök védelme érdekében az informatikai személyzetnek először kezelnie kell az eszköz identitásait. Az informatikai személyzet a Microsoft Intune-hoz hasonló eszközökkel építhet az eszköz identitására, így biztosíthatja a biztonsági és megfelelőségi szabványok betartását. Az Azure Active Directory (Azure AD) lehetővé teszi az eszközökre, alkalmazásokra és szolgáltatásokra való egyszeri bejelentkezést bárhonnan ezeken az eszközökön keresztül.

- A felhasználók hozzáférhetnek a szervezet szükséges eszközeihez. 
- Az informatikai munkatársak megkapják a szervezet védelméhez szükséges vezérlőket.

Az eszközidentitás-kezelés az [eszközalapú feltételes hozzáférés](../conditional-access/require-managed-devices.md)alapja. Az eszközalapú feltételes hozzáférési szabályzatok segítségével biztosíthatja, hogy a környezeterőforrásaihoz való hozzáférés csak felügyelt eszközökkel lehetséges.

## <a name="getting-devices-in-azure-ad"></a>Eszközök beszerzése az Azure AD-ben

Ahhoz, hogy egy eszköz az Azure AD-ben, több lehetőség közül választhat:

- **Az Azure AD-ban regisztrálva**
   - Az Azure AD-ben regisztrált eszközök általában személyes tulajdonban lévő vagy mobileszközök, és egy személyes Microsoft-fiókkal vagy más helyi fiókkal vannak bejelentkezve.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD-hez csatlakoztatva**
   - Az Azure AD-hez csatlakozott eszközök egy szervezet tulajdonában vannak, és az adott szervezethez tartozó Azure AD-fiókkal vannak bejelentkezve. Csak a felhőben léteznek.
      - Windows 10 
      - Windows Server 2019 (a kiszolgálómag nem támogatott)
- **csatlakozik a Hibrid Azure AD-hez**
   - A hibrid Azure AD-hez csatlakozott eszközök egy szervezet tulajdonában vannak, és az adott szervezethez tartozó Azure AD-fiókkal vannak bejelentkezve. A felhőben és a helyszíni környezetben léteznek.
      - Windows 7, 8.1 vagy 10
      - Windows Server 2008 vagy újabb

![Az Azure AD-eszközök panelen megjelenített eszközök](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> A hibrid állapot nem csak az eszköz állapotára utal. Ahhoz, hogy egy hibrid állapot érvényes legyen, egy érvényes Azure AD-felhasználó is szükséges.

## <a name="device-management"></a>Eszközfelügyelet

Az Azure AD-ben lévő eszközök kezelhetők a Mobileszköz-kezelés (MDM) eszközeivel, például a Microsoft Intune-nal, a Microsoft Endpoint Configuration Managerrel, a csoportházirenddel (hibrid Azure AD-csatlakozás), a Mobilalkalmazás-kezelés (MAM) eszközeivel vagy más külső eszközökkel.

## <a name="resource-access"></a>Erőforrás-hozzáférés

Az eszközök regisztrálása és az Azure AD-hez való csatlakoztatása zökkenőmentes bejelentkezést (SSO) biztosít a felhőalapú erőforrásokhoz. Ez a folyamat azt is lehetővé teszi a rendszergazdák számára, hogy feltételes hozzáférési házirendeket alkalmazzanak az erőforrásokra azon eszköz alapján, amelyről hozzáfértek. 

> [!NOTE]
> Az eszközalapú feltételes hozzáférés-házirendek hibrid Azure AD-hez csatlakozó eszközöket vagy megfelelő Azure AD-illesztést vagy Az Azure AD-ben regisztrált eszközöket igényelnek.

Az elsődleges frissítési jogkivonat (PRT) információkat tartalmaz az eszközről, és az egyszeri biztonsági felelőshöz szükséges. Ha egy alkalmazáson eszközalapú feltételes hozzáférési házirend et állít be a PRT nélkül, a rendszer megtagadja a hozzáférést. A hibrid feltételes hozzáférési házirendek hibrid állapotú eszközt és egy bejelentkezett érvényes felhasználót igényelnek.

Az Azure AD-hez csatlakozott vagy hibrid Azure AD-hez csatlakozott eszközök az egyszeri szolgáltató előnyeit élvezik a szervezet helyszíni erőforrásaival és a felhőbeli erőforrásokkal. További információ a cikkben található, [hogyan sso a helyszíni erőforrások az Azure AD-hez csatlakozott eszközök.](azuread-join-sso.md)

## <a name="device-security"></a>Az eszköz biztonsága

- **Az Azure AD regisztrált eszközei** a végfelhasználó által kezelt fiókot használnak, ez a fiók vagy egy Microsoft-fiók, vagy egy másik helyileg felügyelt hitelesítő adat, amely az alábbiak közül egy vagy több.
   - Jelszó
   - PIN kód
   - Mintázat
   - Windows Hello
- **Az Azure AD-hez csatlakozott vagy hibrid Azure AD-hez kapcsolódó eszközök** egy szervezeti fiókot használnak az Azure AD-ben, amelyet az alábbiak közül egy vagy több biztosít.
   - Jelszó
   - Vállalati Windows Hello

## <a name="provisioning"></a>Kiépítés

Az Azure AD-be való bejutáshoz önkiszolgáló módon vagy a rendszergazdák által ellenőrzött kiépítési folyamattal lehet eszközelni az eszközöket.

## <a name="summary"></a>Összefoglalás

Az Azure AD-ben az eszközidentitás-kezeléssel a következőket teheti:

- Az eszközök azure AD-ben való leindításának és kezelésének egyszerűsítése
- A felhasználók számára könnyen használható hozzáférés biztosítása a szervezet felhőalapú erőforrásaihoz

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- További információ az [Azure AD-ben regisztrált eszközökről](concept-azure-ad-register.md)
- További információ az [Azure AD-hez csatlakozó eszközökről](concept-azure-ad-join.md)
- További információ a [hibrid Azure AD-hez csatlakozó eszközökről](concept-azure-ad-join-hybrid.md)
- Ha áttekintést szeretne kapni az eszközidentitások Azure Portalon történő kezeléséről, olvassa [el az Eszközidentitások kezelése az Azure Portal használatával című témakört.](device-management-azure-portal.md)
- Az eszközalapú feltételes hozzáférésről az [Azure Active Directory eszközalapú feltételes hozzáférési szabályzatok konfigurálása című témakörben](../conditional-access/require-managed-devices.md)olvashat bővebben.
