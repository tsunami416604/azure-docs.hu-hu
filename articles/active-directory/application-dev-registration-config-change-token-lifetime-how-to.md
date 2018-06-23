---
title: A jogkivonatok élettartama módosítása egy egyéni által fejlesztett alkalmazás alapértelmezés szerint |} Microsoft Docs
description: A jogkivonatok élettartama házirendek az Azure ad-val kifejlesztett alkalmazás frissítése
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: bd963253ee796fb56405e8da855475df2d2f5d08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335339"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>A jogkivonatok élettartama módosítása egy egyéni által fejlesztett alkalmazás alapértelmezés szerint használt érték

Prémium szintű Azure AD lehetővé teszi az alkalmazásfejlesztők és a bérlői rendszergazda nem bizalmas ügyfelek számára kiállított jogkivonatokat élettartama konfigurálása. A jogkivonatok élettartama házirend-beállításokat a bérlői szintű vagy az éppen elért erőforrásokat.

 * A jogkivonatok élettartama szabály beállításához le kell töltenie a [Azure AD PowerShell modult](https://www.powershellgallery.com/packages/AzureADPreview).

 * Futtassa a **Connect-AzureAD-megerősítése** parancsot.

 * Íme egy példa szabályzattal, amely beállítja azt a maximális életkora egyetlen tényező frissítési jogkivonat. A szabályzat létrehozása: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Kivételt a [konfigurálása a jogkivonatok élettartama](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokumentum áttekintésével megismerheti, hogyan más egyéni létrehozásához.

## <a name="next-steps"></a>További lépések
[A jogkivonatok élettartama konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Az Azure AD-jogkivonat-hivatkozás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

