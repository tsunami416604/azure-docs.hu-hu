---
title: Az Azure Active Directory feltételes hozzáféréssel kapcsolatos gyakori kérdések | Microsoft dokumentumok
description: Válaszok az Azure Active Directory feltételes hozzáférésével kapcsolatos gyakori kérdésekre.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368087"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Az Azure Active Directory feltételes hozzáféréssel kapcsolatos gyakori kérdések

## <a name="which-applications-work-with-conditional-access-policies"></a>Mely alkalmazások működnek a feltételes hozzáférési házirendekkel?

A feltételes hozzáférési szabályzatokkal működő alkalmazásokról az [Azure Active Directory feltételes hozzáférési szabályait használó alkalmazások és böngészők című témakörben](concept-conditional-access-cloud-apps.md)talál.

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>A feltételes hozzáférési házirendek kényszerítettek a B2B-együttműködésre és a vendégfelhasználókra?

A házirendek a vállalkozások közötti (B2B) együttműködési felhasználók számára vannak érvényben. Bizonyos esetekben azonban előfordulhat, hogy a felhasználó nem tudja teljesíteni a házirend-követelményeket. Előfordulhat például, hogy egy vendégfelhasználó szervezete nem támogatja a többtényezős hitelesítést. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>A SharePoint Online-házirendek a OneDrive Vállalati verzióra is vonatkoznak?

Igen. A SharePoint Online-házirendek a OneDrive Vállalati verzióra is vonatkoznak.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Miért nem tudok házirendet közvetlenül beállítani az ügyfélalkalmazásokra, például a Wordre vagy az Outlookra?

A feltételes hozzáférési házirend a szolgáltatások elérésének követelményeit határozza meg. A szolgáltatás hitelesítése esetén kényszerítve történik. A házirend nincs közvetlenül beállítva egy ügyfélalkalmazáson. Ehelyett akkor kerül alkalmazásra, amikor egy ügyfél hív egy szolgáltatást. A SharePoint-házirendek például a SharePointot hívó ügyfelekre vonatkoznak. Az Exchange egyik házirendje az Outlook programra vonatkozik.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Feltételes hozzáférési házirend vonatkozik a szolgáltatásfiókokra?

A feltételes hozzáférési házirendek minden felhasználói fiókra vonatkoznak. Ide tartoznak a szolgáltatásfiókként használt felhasználói fiókok is. Gyakran egy szolgáltatásfiók, amely felügyelet nélkül fut nem felel meg a feltételes hozzáférési szabályzat követelményeinek. Például többtényezős hitelesítésre lehet szükség. A szolgáltatásfiókok a feltételes hozzáférés házirend-kezelési beállításaival kizárhatók a házirendből. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Elérhetők a Microsoft Graph API-k a feltételes hozzáférési házirendek konfigurálásához?

Jelenleg nem. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Mi a nem támogatott eszközplatformok alapértelmezett kizárási házirendje?

Jelenleg a feltételes hozzáférési házirendek szelektíven vannak érvényben az iOS és Az Android-eszközök felhasználóira. A más eszközökplatformjain lévő alkalmazásokat alapértelmezés szerint nem érinti az iOS és Android rendszerű eszközök feltételes hozzáférési házirendje. A bérlői rendszergazda felülbírálhatja a globális szabályzatot, hogy lehetővé tegye a felhasználók hozzáférését a nem támogatott platformokon.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hogyan működnek a feltételes hozzáférési házirendek a Microsoft Teams ben?

A Microsoft Teams nagymértékben támaszkodik az Exchange Online-ra és a SharePoint Online-ra az alapvető hatékonysági forgatókönyvekhez, például értekezletekhez, naptárakhoz és fájlmegosztáshoz. Az ezekhez a felhőalkalmazásokhoz beállított feltételes hozzáférési szabályzatok akkor vonatkoznak a Microsoft Teamsre, amikor egy felhasználó közvetlenül bejelentkezik a Microsoft Teamsbe.

A Microsoft Teams az Azure Active Directory feltételes hozzáférési szabályzataiban külön-külön is támogatott felhőalapú alkalmazásként. A felhőalapú alkalmazásokhoz beállított feltételes hozzáférési szabályzatok a Microsoft Teamsre is vonatkoznak, amikor egy felhasználó bejelentkezik. Más alkalmazások, például az Exchange Online és a SharePoint Online megfelelő házirendjei nélkül azonban a felhasználók továbbra is közvetlenül hozzáférhetnek ezekhez az erőforrásokhoz.

A Microsoft Teams windowsos és Mac es asztali ügyfelei támogatják a modern hitelesítést. A modern hitelesítés az Azure Active Directory hitelesítési könyvtárán (ADAL) alapuló bejelentkezést eredményez a Microsoft Office ügyfélalkalmazásokhoz különböző platformokon.

## <a name="next-steps"></a>További lépések

- A feltételes hozzáférési szabályzatok konfigurálásához tekintse meg az [Azure Active Directory feltételes hozzáféréssel kapcsolatos gyakorlati tanácsait.](best-practices.md) 
