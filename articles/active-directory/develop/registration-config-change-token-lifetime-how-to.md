---
title: Token élettartamalapértelmezett beállításainak módosítása az egyéni Azure AD-alkalmazásokban
description: Az Azure AD-n fejlesztett alkalmazás token élettartamra vonatkozó szabályzatai frissítése
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883353"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Egyéni fejlesztésű alkalmazások jogkivonat-élettartam-alapértelmezéseinek módosítása

Ez a cikk bemutatja, hogyan használhatja az Azure AD PowerShell egy token élettartamra vonatkozó szabályzat beállítása. Az Azure AD Premium lehetővé teszi az alkalmazásfejlesztők és a bérlői rendszergazdák számára a nem bizalmas ügyfelek számára kiadott jogkivonatok élettartamának konfigurálását. A jogkivonat élettartamára vonatkozó szabályzatok bérlői alapon vagy az elérésben lévő erőforrásokkal vannak beállítva.

1. Token élettartamra vonatkozó szabályzat beállításához le kell töltenie az [Azure AD PowerShell-modult.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Futtassa a **Connect-AzureAD -Confirm** parancsot.

    Íme egy példa házirend, amely beállítja a maximális kor egytényezős frissítési jogkivonatot. Hozza létre a házirendet:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>További lépések

* [Tekintse meg a konfigurálható jogkivonat élettartamát az Azure AD-ben,](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) hogy megtudja, hogyan konfigurálhatja az Azure AD által kiadott jogkivonat-élettartamokat, beleértve azt is, hogyan állíthatja be a jogkivonat élettartamát a szervezet összes alkalmazásához, egy több-bérlős alkalmazáshoz vagy a szervezet egy adott egyszerű szolgáltatásához. 
* [Azure AD token hivatkozás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
