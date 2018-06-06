---
title: Azure Active Directory feltételes hozzáférés – gyakori kérdések |} Microsoft Docs
description: Válaszok feltételes hozzáférésével kapcsolatos gyakori kérdések az Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ea93dab7df920f5a8ff8a1ae518b0bdbdcc80ae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723813"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory – gyakori kérdések a feltételes hozzáférés

## <a name="which-applications-work-with-conditional-access-policies"></a>Mely alkalmazások használható feltételes hozzáférési szabályzatokat?

A feltételes hozzáférési házirendekkel együtt használható alkalmazások kapcsolatos információkért lásd: [alkalmazások és az Azure Active Directoryban feltételes hozzáférési szabályai használó](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Feltételes hozzáférési szabályzatok érvényesítik a B2B együttműködés és a vendégfelhasználók?

Üzleti vállalatközi (B2B) együttműködés felhasználók számára a házirendek érvényben vannak. Azonban néhány esetben a felhasználó nem feltétlenül teljesíteni a házirend követelményeinek. A Vendég felhasználó szervezet például előfordulhat, hogy támogatja a többtényezős hitelesítés. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>A SharePoint Online-szabályzat is vonatkozik onedrive vállalati verzió?

Igen. A SharePoint Online-házirend a onedrive vállalati verzió is vonatkozik.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Miért nem állítható be egy házirendet az ügyfélalkalmazások, például a Word és Outlook?

Feltételes hozzáférési szabályzatot állítja be a szolgáltatások elérésére vonatkozó követelmények. Az adott szolgáltatáshoz hitelesítés esetén van kényszerítve. A házirend nincs beállítva közvetlenül egy ügyfélalkalmazást. Ehelyett azt akkor érvényes, ha egy ügyfél meghívja a szolgáltatást. A SharePoint beállított házirend például SharePoint hívó ügyfelek vonatkozik. Exchange a beállított házirend Outlook vonatkozik.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>A feltételes hozzáférési házirend vonatkozik szolgáltatásfiókok?

Feltételes hozzáférési házirendek felhasználói fiókokhoz vonatkoznak. Ez magában foglalja a felhasználói fiókok, szolgáltatásfiókok használatosak. Gyakran a felügyelet nélküli futtató szolgáltatásfiókot nem felel meg a feltételes hozzáférési házirendet. Például a multi-factor authentication lehet szükség. Szolgáltatásfiókok k zárhatók ki a házirend a feltételes hozzáférési házirend beállítások használatával. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Graph API-k érhetők el a feltételes hozzáférési szabályzatok konfigurálása?

Jelenleg nincs. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Mi az az alapértelmezett kizárási házirend nem támogatott eszközplatformok?

Feltételes hozzáférési házirendek jelenleg, iOS és Android rendszerű eszközök felhasználóinak szelektív érvényesítik. Alkalmazások eszköz platformokon, alapértelmezés szerint nem érinti a feltételes hozzáférési házirend az iOS és Android-eszközökön. Egy Bérlői rendszergazda megadhat bírálja felül a globális házirend nem engedélyezi a hozzáférést a felhasználók által nem támogatott platformokon.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hogyan működik a Microsoft Teams feltételes hozzáférési szabályzatokat?

Microsoft Teams erősen támaszkodik a Exchange Online és SharePoint Online core termelékenység forgatókönyvek esetén, mint az értekezletek, naptárak és fájlmegosztás. Amikor a felhasználó bejelentkezhet a Microsoft Teams közvetlenül a Microsoft Teams ezekbe a felhőalkalmazásokba beállított feltételes hozzáférési házirendek vonatkoznak.

Microsoft Teams is támogatott külön-külön, a felhőalapú alkalmazások az Azure Active Directory feltételes hozzáférési házirendek. A cloud app beállított feltételes hozzáférési házirendek Microsoft Teams vonatkozik, amikor egy felhasználó bejelentkezik. Azonban a helyes-e más alkalmazásokhoz, például az Exchange Online és SharePoint Online-házirendek nélkül felhasználók továbbra is lehet a azokhoz az erőforrásokhoz való közvetlen hozzáféréshez.

Microsoft Teams asztali ügyfelek Windows és Mac modern hitelesítést támogatja. Modern hitelesítéssel bejelentkezhet a az Azure Active Directory hitelesítési könyvtár (ADAL) a Microsoft Office ügyfélalkalmazások alapú különböző platformokon.