---
title: "Az Azure Active Directory feltételes hozzáférési eszközházirendek Office 365-szolgáltatásokhoz |} Microsoft Docs"
description: "További tudnivalók kiépítése a feltételes hozzáférési eszközházirendek segít a vállalati erőforrásokhoz, több biztonságos, felhasználói megfelelőség fenntartása mellett, a érhetnek el szolgáltatásokat."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d33babd331570624ca6a0fc967c79dbc467a1b40
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Active Directory feltételes hozzáférési eszközházirendek Office 365-szolgáltatásokhoz

Feltételes hozzáférés működéséhez több adatokra van szükség. A multi-factor Authentication magában foglalja a hitelesített felhasználó, egy hitelesített eszköz és a megfelelő eszköz, többek között. Ebben a cikkben azt elsősorban összpontosítani eszközalapú feltételeket, amelyek segítséget nyújtanak a szervezet használható Office 365-szolgáltatásokhoz való hozzáférést. 

Vállalati felhasználók a személyes eszközeikről szeretné a munkahelyi és iskolai például Exchange és SharePoint Online hozzáférés Office 365-szolgáltatások. Azt szeretné, hogy a biztonságos hozzáférést. Feltételes hozzáférési szabályzatok segítségével biztonságosabbá a vállalati erőforrásokhoz, a felhasználóknak, akik az előírásoknak megfelelő eszközök szolgáltatásokhoz való hozzáférés vezérlését építhető ki. Feltételes hozzáférési szabályzatok az Office 365 a Microsoft Intune feltételes hozzáférési portál állíthatja be.

Azure Active Directory (Azure AD) érvénybe lépteti a feltételes hozzáférési házirendek segítségével biztonságossá tehető az Office 365 szolgáltatásaiban. Létrehozhat egy feltételes hozzáférési szabályzatot, amely blokkolja a hozzáférését az Office 365-szolgáltatás egy nem megfelelő eszközt használó felhasználó. A felhasználónak meg kell felelnie a vállalati szabályzatok a szolgáltatáshoz való hozzáférés megadása előtt. Alternatív megoldásként létrehozhat egy házirendet, amely megköveteli a felhasználóktól regisztrálhatják az eszközeiket, hozzáférhetnek az Office 365 szolgáltatáshoz. Házirendek alkalmazása a szervezet minden felhasználója számára, vagy néhány célcsoportok korlátozódik. Egy házirend több célcsoportok időbeli adhat hozzá.

Eszköz-házirendek érvényesítéséről előfeltétel, hogy felhasználók regisztrálnia kell az eszközeiket, az Azure AD eszközregisztrációs szolgáltatással. Úgy is dönthet eszközöket, amelyeket regisztrálni az Azure AD eszközregisztrációs szolgáltatással többtényezős hitelesítés bekapcsolása. Többtényezős hitelesítés az Azure Active Directory eszközregisztrációs szolgáltatás ajánlott. Többtényezős hitelesítés be van kapcsolva, amikor a felhasználók regisztrálják az eszközeiket az Azure AD eszközregisztrációs szolgáltatás kéttényezős hitelesítéshez sor kerül.

## <a name="how-does-a-conditional-access-policy-work"></a>Hogyan működik a feltételes hozzáférési házirendet?

Amikor egy felhasználó hozzáférést kér az Office 365 szolgáltatás támogatott eszköz platformról származó, Azure AD akkor hitelesíti a felhasználót és az eszközt. Az Azure AD hozzáférést a szolgáltatás csak a felhasználó megfelel-e a szolgáltatás a beállított házirend. A nem regisztrált eszközökön felhasználók útmutatást hogyan kell regisztrálni, és az Office 365 vállalati szolgáltatások elérésére szabályzatnak megfelelővé tette. IOS és Android-eszközök a felhasználók regisztrálhatják az eszközeiket az Intune vállalati portál alkalmazás használatával szükségesek. Amikor a felhasználó regisztrál egy eszközt, az Azure ad-val regisztrálja az eszközt, és regisztrálva van a felügyeleti eszközök és megfelelőség. A Microsoft Intune-nal az Azure AD eszközregisztrációs szolgáltatást kell használnia a mobileszközök felügyeletéhez Office 365-szolgáltatásokhoz. Eszközök beléptetése szabályzatok érvényesítik szükség a felhasználók számára az Office 365 szolgáltatást.

Amikor a felhasználó sikeresen regisztrál egy eszközt, az eszköz megbízható válik. Az Azure AD a hitelesített felhasználó egyszeri bejelentkezéses hozzáférést biztosít a vállalati alkalmazások. Az Azure AD érvénybe lépteti a feltételes hozzáférési házirend hozzáférést ad a szolgáltatáshoz nem csak az első alkalommal a felhasználó hozzáférést kér, de minden alkalommal a felhasználó megújítja hozzáférési kérelmet. A felhasználó bejelentkezési hitelesítő adatok megváltoznak, az eszköz elvesztésekor vagy ellopásakor, vagy a házirend feltételeinek nem teljesülnek a megújítási kérést időpontjában szolgáltatásokhoz való hozzáférés megtagadva.

## <a name="deployment-considerations"></a>Telepítési szempontok

Eszközök regisztrálása az Azure AD eszközregisztrációs szolgáltatást kell használnia.

Ha a helyszíni felhasználók készül kell hitelesíteni, Active Directory összevonási szolgáltatások (AD FS) (1.0-s verziója és újabb verziók) szükséges. A multi-factor authentication a munkahelyi csatlakoztatás esetén az identitásszolgáltató nem képes a multi-factor Authentication hitelesítés sikertelen lesz. Például többtényezős hitelesítés nem használható az AD FS 2.0-s. Győződjön meg arról, hogy a helyszíni AD FS működik a multi-factor Authentication hitelesítéshez, és egy érvényes multi-factor authentication módszer van beállítva, az Azure AD eszközregisztrációs szolgáltatást a többtényezős hitelesítést bekapcsolása előtt. Például a Windows Server 2012 R2 AD FS többtényezős hitelesítés képességekkel rendelkezik. Is be kell egy további érvényes hitelesítés (többtényezős hitelesítés) módszer az AD FS-kiszolgálón többtényezős hitelesítés az Azure AD eszközregisztrációs szolgáltatás bekapcsolása előtt. Az AD FS-ben támogatott többtényezős hitelesítési módszerekkel kapcsolatos további információkért lásd: [további hitelesítési módszerek konfigurálása az AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Következő lépések

*   Gyakori kérdésekre adott válaszok, lásd: [Azure Active Directory feltételes hozzáférés – gyakori kérdések](active-directory-conditional-faqs.md).
