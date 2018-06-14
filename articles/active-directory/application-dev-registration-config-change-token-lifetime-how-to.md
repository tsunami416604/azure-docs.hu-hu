---
title: A jogkivonatok élettartama módosítása egy egyéni által fejlesztett alkalmazás alapértelmezés szerint |} Microsoft Docs
description: A jogkivonatok élettartama házirendek az Azure ad-val kifejlesztett alkalmazás frissítése
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8067ecf3e274f65abe2c82f20dd2f4469344f3b6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614377"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>A jogkivonatok élettartama módosítása egy egyéni által fejlesztett alkalmazás alapértelmezés szerint használt érték

Prémium szintű Azure AD lehetővé teszi az alkalmazásfejlesztők és a bérlői rendszergazda nem bizalmas ügyfelek számára kiállított jogkivonatokat élettartama konfigurálása. A jogkivonatok élettartama házirend-beállításokat a bérlői szintű vagy az éppen elért erőforrásokat.

 * A jogkivonatok élettartama szabály beállításához le kell töltenie a [Azure AD PowerShell modult](https://www.powershellgallery.com/packages/AzureADPreview).

 * Futtassa a **Connect-AzureAD-megerősítése** parancsot.

 * Íme egy példa szabályzattal, amely beállítja azt a maximális életkora egyetlen tényező frissítési jogkivonat. A szabályzat létrehozása:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Kivételt a [konfigurálása a jogkivonatok élettartama](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokumentum áttekintésével megismerheti, hogyan más egyéni létrehozásához.

## <a name="next-steps"></a>Következő lépések
[A jogkivonatok élettartama konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Az Azure AD-jogkivonat-hivatkozás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

