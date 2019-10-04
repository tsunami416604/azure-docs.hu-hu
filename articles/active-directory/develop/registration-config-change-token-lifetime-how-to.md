---
title: A jogkivonat élettartamának alapértelmezett értékeinek módosítása egyéni fejlesztésű alkalmazásokhoz | Microsoft Docs
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
ms.openlocfilehash: 8dd69ff41c890c654fc41bb601b17d135ff0e984
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320944"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>A jogkivonat élettartamának alapértelmezett értékeinek módosítása egyéni fejlesztésű alkalmazásokhoz

Ez a cikk bemutatja, hogyan használható az Azure AD PowerShell a jogkivonat-élettartam szabályzatának beállításához. Prémium szintű Azure AD lehetővé teszi az alkalmazások fejlesztői és bérlői rendszergazdái számára a nem bizalmas ügyfelek számára kiállított jogkivonatok élettartamának konfigurálását. A jogkivonat élettartamára vonatkozó szabályzatok a bérlőre érvényesek, vagy az elérni kívánt erőforrások.

1. A jogkivonat-élettartam szabályzatának beállításához le kell töltenie az [Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureADPreview)-modult.
1. Futtassa a **kapcsolat-AzureAD-Confirm** parancsot.

    Az alábbi példa olyan szabályzatot állít be, amely beállítja a maximális életkorú, egytényezős frissítési tokent. Hozza létre a szabályzatot:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>További lépések

* Tekintse meg a [konfigurálható jogkivonat-élettartamok az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) című témakört, amelyből megtudhatja, hogyan konfigurálhatja az Azure ad által kiadott jogkivonat-élettartamokat, beleértve a jogkivonat-élettartamok beállítását a szervezet összes alkalmazásához, egy több-bérlős alkalmazáshoz vagy egy adott egyszerű szolgáltatásnév számára szervezet. 
* [Azure AD-jogkivonat – dokumentáció](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

