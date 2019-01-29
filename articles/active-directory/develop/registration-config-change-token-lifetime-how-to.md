---
title: A jogkivonat élettartamát módosítása egy egyénileg fejlesztett alkalmazás alapértelmezés szerint |} A Microsoft Docs
description: Az alkalmazás, amely az Azure ad-ben fejleszt jogkivonat élettartama házirendek frissítése
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 7895d5d70b9dc264d6adae42060acc5d3fdf3237
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094342"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>A jogkivonat élettartamát módosítása egy egyénileg fejlesztett alkalmazás alapértelmezés szerint

Prémium szintű Azure AD lehetővé teszi a fejlesztők és a bérlői rendszergazdák konfigurálása a nem bizalmas ügyfelek számára kiállított jogkivonatok élettartamát. Jogkivonat élettartama házirend-beállításokat a bérlői szintű alapján vagy az elért erőforrások.

1. A jogkivonat élettartama házirend beállítása, le kell töltenie a [Azure AD PowerShell-modul](https://www.powershellgallery.com/packages/AzureADPreview).
1. Futtassa a **Connect-AzureAD-megerősítése** parancsot.

    Íme egy példa a szabályzatot, amely a maximális időtartamnak egyetlen tényező frissítési jogkivonatot. A szabályzat létrehozása: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>További lépések

* Lásd: [konfigurálható jogkivonatok élettartamának, az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) megtudhatja, hogyan konfigurálhatja, többek között a szervezet összes alkalmazáshoz, több-bérlős alkalmazás vagy egy adott szolgáltatáshoz a jogkivonatok élettartamának beállítása az Azure AD által kiállított jogkivonatok élettartama egyszerű a szervezetben. 
* [Az Azure AD-jogkivonat-referencia](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

