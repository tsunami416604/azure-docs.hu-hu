---
title: A feltételes hozzáférés Azure Active Directory GYIK | Microsoft Docs
description: Válaszok a feltételes hozzáféréssel kapcsolatos gyakori kérdésekre Azure Active Directoryban.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1704fb50586168be6f960e62b918019cb67ce4c8
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167808"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Feltételes hozzáférés Azure Active Directory – gyakori kérdések

## <a name="which-applications-work-with-conditional-access-policies"></a>Mely alkalmazások működnek a feltételes hozzáférési szabályzatokkal?

A feltételes hozzáférési szabályzatokkal működő alkalmazásokkal kapcsolatos további információkért tekintse [meg a Azure Active Directory a feltételes hozzáférési szabályokat használó alkalmazásokat és böngészőket](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>A feltételes hozzáférési szabályzatok érvénybe lépjenek a VÁLLALATKÖZI együttműködés és a vendég felhasználók számára?

A szabályzatokat a vállalatok közötti (B2B) együttműködési felhasználók kényszerítik ki. Bizonyos esetekben azonban előfordulhat, hogy a felhasználók nem tudják kielégíteni a szabályzat követelményeit. Előfordulhat például, hogy a vendég felhasználó szervezete nem támogatja a többtényezős hitelesítést. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>A SharePoint Online-szabályzat a OneDrive for Business szolgáltatásra is érvényes?

Igen. A SharePoint Online-szabályzat a vállalati OneDrive is vonatkozik. További információkért tekintse meg a [feltételes hozzáférési szolgáltatás függőségeivel](service-dependencies.md) foglalkozó témakört, és vegye fontolóra a szabályzatok megcélzását az [Office 365 alkalmazáshoz](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Miért nem állítható be házirend közvetlenül az ügyfélalkalmazások, például a Word vagy az Outlook?

A feltételes hozzáférési szabályzatok a szolgáltatások elérésére vonatkozó követelményeket határozzák meg. Ez a szolgáltatás akkor lép érvénybe, ha a szolgáltatás hitelesítése megtörténik. A házirend nincs beállítva közvetlenül egy ügyfélalkalmazás számára. Ehelyett akkor alkalmazza a rendszer, amikor egy ügyfél meghívja a szolgáltatást. A SharePoint házirend-beállítása például a SharePointot hívó ügyfelekre vonatkozik. Az Exchange-ben beállított házirend az Outlookra vonatkozik. További információkért tekintse meg a [feltételes hozzáférési szolgáltatás függőségeivel](service-dependencies.md) foglalkozó témakört, és vegye fontolóra a szabályzatok megcélzását az [Office 365 alkalmazáshoz](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Érvényes a feltételes hozzáférési szabályzat a szolgáltatásfiókok esetében?

A feltételes hozzáférési szabályzatok minden felhasználói fiókra érvényesek. Ez magában foglalja a szolgáltatásfiókokként használt felhasználói fiókokat is. Gyakran előfordul, hogy egy felügyelet nélküli szolgáltatást futtató szolgáltatásfiók nem tudja kielégíteni a feltételes hozzáférési szabályzat követelményeit. Előfordulhat például, hogy a többtényezős hitelesítésre van szükség. A szolgáltatásfiókok kizárhatók egy házirendből egy [felhasználó vagy csoport kizárásával](concept-conditional-access-users-groups.md#exclude-users). 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Mi a nem támogatott eszköz platformokra vonatkozó alapértelmezett kizárási szabályzat?

A feltételes hozzáférési szabályzatok jelenleg az iOS-és Android-eszközök felhasználói számára szelektíven kényszerítve vannak. Az iOS-és Android-eszközök feltételes hozzáférési szabályzata alapértelmezés szerint nem érinti a többi eszköz platformon futó alkalmazásokat. A bérlői rendszergazdák megadhatják, hogy a globális házirend felülbírálásával ne engedélyezzék a felhasználók hozzáférését a nem támogatott platformokon.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hogyan működnek a feltételes hozzáférési szabályzatok a Microsoft Teams szolgáltatásban?

A Microsoft Teams nagymértékben támaszkodik az Exchange Online-ra és a SharePoint Online-ra az alapvető hatékonyságnövelő forgatókönyvek, például az értekezletek, a naptárak és a fájlmegosztás terén. Az ezekhez a felhőalapú alkalmazásokhoz beállított feltételes hozzáférési szabályzatok a Microsoft Teams szolgáltatásra vonatkoznak, ha a felhasználó közvetlenül a Microsoft Teams szolgáltatásba jelentkezik be.

A Microsoft Teams szolgáltatást a feltételes hozzáférési szabályzatokban külön Felhőbeli alkalmazásként is támogatja. A felhőalapú alkalmazásokhoz beállított feltételes hozzáférési házirendek a felhasználó bejelentkezésekor érvényesek a Microsoft Teams szolgáltatásra. Azonban a megfelelő szabályzatok nélkül más alkalmazásokhoz, például az Exchange Online-hoz és a SharePoint Online-hoz tartozó felhasználók továbbra is hozzáférhetnek ezekhez az erőforrásokhoz.

A Microsoft Teams asztali Windows-és Mac-ügyfelei támogatják a modern hitelesítést. A modern hitelesítés a Azure Active Directory Authentication Library (ADAL) alapján a különböző platformokon Microsoft Office ügyfélalkalmazások számára teszi lehetővé a bejelentkezést.

További információkért tekintse meg a [feltételes hozzáférési szolgáltatás függőségeivel](service-dependencies.md) foglalkozó témakört, és vegye fontolóra a szabályzatok megcélzását az [Office 365 alkalmazáshoz](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="next-steps"></a>Következő lépések

- A környezet feltételes hozzáférési házirendjeinek konfigurálásához tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md). 
