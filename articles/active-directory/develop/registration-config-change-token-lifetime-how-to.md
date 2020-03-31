---
title: Token élettartamának alapértelmezett értékeinek módosítása az egyéni Azure AD-alkalmazásokban | Microsoft dokumentumok
description: Az Azure AD-n fejlesztett alkalmazás token élettartamra vonatkozó szabályzatai frissítése
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
ms.openlocfilehash: 431f18b9babb52b5000d3bf4cca75a0f5e29bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702810"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Egyéni fejlesztésű alkalmazások jogkivonat-élettartam-alapértelmezéseinek módosítása

Ez a cikk bemutatja, hogyan használhatja az Azure AD PowerShell egy token élettartamra vonatkozó szabályzat beállítása. Az Azure AD Premium lehetővé teszi az alkalmazásfejlesztők és a bérlői rendszergazdák számára a nem bizalmas ügyfelek számára kiadott jogkivonatok élettartamának konfigurálását. A jogkivonat élettartamára vonatkozó szabályzatok bérlői alapon vagy az elérésben lévő erőforrásokkal vannak beállítva.

1. Token élettartamra vonatkozó szabályzat beállításához le kell töltenie az [Azure AD PowerShell-modult.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Futtassa a **Connect-AzureAD -Confirm** parancsot.

    Íme egy példa házirend, amely beállítja a maximális kor egytényezős frissítési jogkivonatot. Hozza létre a házirendet:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>További lépések

* [Tekintse meg a konfigurálható jogkivonat élettartamát az Azure AD-ben,](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) hogy megtudja, hogyan konfigurálhatja az Azure AD által kiadott jogkivonat-élettartamokat, beleértve azt is, hogyan állíthatja be a jogkivonat élettartamát a szervezet összes alkalmazásához, egy több-bérlős alkalmazáshoz vagy a szervezet egy adott egyszerű szolgáltatásához. 
* [Azure AD token hivatkozás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
