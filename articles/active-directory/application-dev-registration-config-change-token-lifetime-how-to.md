---
title: A jogkivonat élettartamát módosítása egy egyénileg fejlesztett alkalmazás alapértelmezés szerint |} A Microsoft Docs
description: Az alkalmazás, amely az Azure ad-ben fejleszt jogkivonat élettartama házirendek frissítése
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0ecb1f55309901abd2c623d2c3d23ef717fb176b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365102"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>A jogkivonat élettartamát módosítása egy egyénileg fejlesztett alkalmazás alapértelmezés szerint

Prémium szintű Azure AD lehetővé teszi a fejlesztők és a bérlői rendszergazdák konfigurálása a nem bizalmas ügyfelek számára kiállított jogkivonatok élettartamát. Jogkivonat élettartama házirend-beállításokat a bérlői szintű alapján vagy az elért erőforrások.

 * A jogkivonat élettartama házirend beállítása, le kell töltenie a [Azure AD PowerShell-modul](https://www.powershellgallery.com/packages/AzureADPreview).

 * Futtassa a **Connect-AzureAD-megerősítése** parancsot.

 * Íme egy példa a szabályzatot, amely a maximális időtartamnak egyetlen tényező frissítési jogkivonatot. A szabályzat létrehozása: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Kivétel a [konfigurálása jogkivonat élettartama](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) megtudhatja, hogyan hozhat létre más egyéni dokumentumot.

## <a name="next-steps"></a>További lépések
[Az élettartam konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Az Azure AD-jogkivonat-referencia](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

