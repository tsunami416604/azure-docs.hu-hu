---
title: Mi az eszköz identitása a Azure Active Directoryban?
description: Megtudhatja, hogyan segítheti a környezet erőforrásaihoz hozzáférő eszközök felügyeletét.
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
ms.openlocfilehash: b56319579bc8766bbd8467296daf41e25fc31862
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420513"
---
# <a name="what-is-a-device-identity"></a>Mi az az eszközidentitás?

Az eszközök és méretek, valamint a saját eszközök használata (BYOD) koncepciójának elterjedése révén az informatikai szakemberek két, némileg ellentétes céllal szembesülnek:

- Lehetővé teszi a végfelhasználók számára, hogy bárhol és bármikor termelékenyek legyenek
- A szervezet eszközeinek védelmének biztosítása

Az eszközök védelméhez az informatikai részlegnek először kezelnie kell az eszköz identitásait. A biztonsági és megfelelőségi előírások teljesítése érdekében az informatikai munkatársak az eszköz identitásán keresztül építhetnek be olyan eszközöket, mint a Microsoft Intune. Azure Active Directory (Azure AD) lehetővé teszi az egyszeri bejelentkezést az eszközökre, alkalmazásokra és szolgáltatásokra, bárhonnan ezen eszközökön keresztül.

- A felhasználók hozzáférést kapnak a szervezet eszközeihez, amelyekre szükségük van. 
- Az informatikai munkatársak a szervezet biztonságossá tételéhez szükséges vezérlőket kapják meg.

Az eszközök identitásának kezelése az [eszköz alapú feltételes hozzáférés](../conditional-access/require-managed-devices.md)alapja. Az eszközökön alapuló feltételes hozzáférési házirendek segítségével biztosíthatja, hogy a környezet erőforrásaihoz való hozzáférés csak a felügyelt eszközökön lehetséges.

## <a name="getting-devices-in-azure-ad"></a>Eszközök beszerzése az Azure AD-ben

Az eszköz Azure AD-ben való beszerzéséhez több lehetőség közül választhat:

- **Azure AD-regisztráció**
   - Az Azure AD-ban regisztrált eszközök jellemzően személyes tulajdonú vagy mobil eszközök, és bejelentkeznek személyes Microsoft-fiók vagy egy másik helyi fiókkal.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD-hez csatlakoztatott**
   - Az Azure AD-hez csatlakoztatott eszközök egy szervezet tulajdonában állnak, és bejelentkeznek az adott szervezethez tartozó Azure AD-fiókkal. Csak a felhőben vannak.
      - Windows 10 
- **csatlakozik a Hibrid Azure AD-hez**
   - A hibrid Azure AD-hez csatlakoztatott eszközök egy szervezet tulajdonában vannak, és bejelentkeznek az adott szervezethez tartozó Azure AD-fiókkal. A felhőben és a helyszínen vannak.
      - Windows 7, 8,1 vagy 10
      - Windows Server 2008 vagy újabb

![Az Azure AD-eszközök panelen megjelenő eszközök](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Eszközkezelés

Az Azure AD-ban található eszközök a mobileszköz-kezelési (MDM) eszközök, például a Microsoft Intune, a System Center Configuration Manager, a Csoportházirend (hibrid Azure AD JOIN), a Mobile Application Management (MAM) eszközei vagy más, harmadik féltől származó eszközök használatával kezelhetők.

## <a name="resource-access"></a>Erőforrás-hozzáférés

A regisztrálás és a csatlakozás lehetővé teszi a felhasználók számára, hogy zökkenőmentesen jelentkezzenek be a felhőalapú erőforrásokra és a rendszergazdákra, hogy feltételes hozzáférési szabályzatokat alkalmazzanak ezekre az erőforrásokra. 

Az Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez csatlakoztatott eszközök az SSO-t a szervezet helyi erőforrásaihoz és a felhőalapú erőforrásokhoz is igénybe vehetik. További információt a cikkben talál, [Hogyan működik az SSO a helyszíni erőforrásokkal az Azure ad-hez csatlakoztatott eszközökön](azuread-join-sso.md).

## <a name="device-security"></a>Eszköz biztonsága

- Az **Azure ad-ban regisztrált eszközök** a végfelhasználó által kezelt fiókot használják, ez a fiók vagy egy Microsoft-fiók, vagy egy másik helyileg felügyelt hitelesítő adat, amely az alábbiak közül eggyel vagy többet biztosít.
   - Jelszó
   - PIN
   - Pattern
   - Windows Hello
- Az Azure ad- **hez csatlakoztatott vagy hibrid Azure ad-hez csatlakoztatott eszközök** szervezeti fiókot használnak az Azure ad-ben az alábbiak közül egyet vagy többet.
   - Jelszó
   - Vállalati Windows Hello

## <a name="provisioning"></a>Kiépítés

Az eszközök Azure AD-be való beszerzése önkiszolgáló módon vagy a rendszergazdák által felügyelt kiépítési folyamattal végezhető el.

## <a name="summary"></a>Összegzés

Az eszközök Identitáskezelés az Azure AD-ben a következőket teheti:

- Egyszerűsítse az eszközök Azure AD-ben való üzembe helyezésének és kezelésének folyamatát
- Egyszerű hozzáférést nyújt a felhasználóknak a vállalat felhőbeli erőforrásaihoz

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- További információ az [Azure ad regisztrált eszközeiről](concept-azure-ad-register.md)
- További információ az [Azure ad-hez csatlakoztatott eszközökről](concept-azure-ad-join.md)
- További információ a [hibrid Azure ad-hez csatlakoztatott eszközökről](concept-azure-ad-join-hybrid.md)
- Ha szeretné áttekinteni, hogyan kezelheti az eszközök identitásait a Azure Portalban, tekintse meg [az eszközök identitásának kezelése a Azure Portal használatával](device-management-azure-portal.md)című cikket.
- Ha többet szeretne megtudni az eszközökön alapuló feltételes hozzáférésről, tekintse meg [Azure Active Directory eszközön alapuló feltételes hozzáférési szabályzatok konfigurálása](../conditional-access/require-managed-devices.md)című témakört.
