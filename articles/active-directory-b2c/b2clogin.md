---
title: B2clogin.com használatával |} Microsoft Docs
description: További tudnivalók login.microsoftonline.com b2clogin.com helyett.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c4b3122984cdcb324f7b86e44a62e111d6ca0a29
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131592"
---
# <a name="using-b2clogincom"></a>A b2clogin.com használata

>[!IMPORTANT]
>Ez a szolgáltatás csak a nyilvános előzetes verzió 
>

Most már rendelkezik a lehetőséget az Azure AD B2C-bA `<YourTenantName>.b2clogin.com` helyett `login.microsoftonline.com`.  Ennek a számos előnye van:
* Többé nem osztja a más Microsoft-termékekkel a azonos cookie-k fejlécek méretkorlátját.
* Eltávolíthatja a Microsoft mutató összes hivatkozást az URL-címben (lecserélheti `<YourTenantName>.onmicrosoft.com` a bérlő azonosítójú). Például: `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 B2clogin.com előnyeit, be kell állítani néhány a következőket:

1. Az a **futtatása most végpont** győződjön meg arról, hogy azok be `<YourTenantName>.b2clogin.com` helyett `login.microsoftonline.com`.
2. Ha MSAL használ, be kell `validateauthority=false`.  Ennek oka, hogy a jogkivonat-kibocsátó válik`<YourTenantName>.b2clogin.com`.