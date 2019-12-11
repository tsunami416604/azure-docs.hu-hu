---
title: A jogkivonat élettartamának alapértelmezett értékeinek módosítása egyéni Azure AD-alkalmazásokhoz | Microsoft Docs
description: Az Azure AD-ben fejleszthető, az alkalmazásra vonatkozó jogkivonat élettartamára vonatkozó szabályzatok frissítése
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: a603be6d57581541c0868b9f48a0bf9997cadd71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962831"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>A jogkivonat élettartamának alapértelmezett értékeinek módosítása egyéni fejlesztésű alkalmazásokhoz

Ez a cikk bemutatja, hogyan használható az Azure AD PowerShell a jogkivonat-élettartam szabályzatának beállításához. Prémium szintű Azure AD lehetővé teszi az alkalmazások fejlesztői és bérlői rendszergazdái számára a nem bizalmas ügyfelek számára kiállított jogkivonatok élettartamának konfigurálását. A jogkivonat élettartamára vonatkozó szabályzatok a bérlőre érvényesek, vagy az elérni kívánt erőforrások.

1. A jogkivonat-élettartam szabályzatának beállításához le kell töltenie az [Azure ad PowerShell-modult](https://www.powershellgallery.com/packages/AzureADPreview).
1. Futtassa a **kapcsolat-AzureAD-Confirm** parancsot.

    Az alábbi példa olyan szabályzatot állít be, amely beállítja a maximális életkorú, egytényezős frissítési tokent. Hozza létre a szabályzatot: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [konfigurálható jogkivonat élettartamait az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) , amelyből megtudhatja, hogyan konfigurálhatja az Azure ad által kiadott jogkivonat-élettartamokat, beleértve a jogkivonat-élettartamok beállítását a szervezet összes alkalmazásához, egy több-bérlős alkalmazáshoz vagy a szervezet egy adott egyszerű szolgáltatásához. 
* [Azure AD-jogkivonat – dokumentáció](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
