---
title: B2clogin.com| használatával Microsoft Docs
description: További tudnivalók login.microsoftonline.com b2clogin.com helyett.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
#<a name="using-b2clogincom"></a>A b2clogin.com használata

>[!IMPORTANT]
>Ez a szolgáltatás csak a nyilvános előzetes verzió 
>

Most már rendelkezik a lehetőséget az Azure AD B2C-bA `<YourTenantName>.b2clogin.com` helyett `login.microsoftonline.com`.  Ennek a számos előnye van:
* Az azonos cookie-k fejlécek méretkorlátját lesz többé nem osztja, a más Microsoft-termékekkel
* Eltávolíthatja a Microsoft mutató összes hivatkozást az URL-címben (lecserélheti `<YourTenantName>.onmicrosoft.com` a bérlő azonosítójú)

 B2clogin.com előnyeit, be kell állítani néhány a következőket:

1. Az a **futtatása most végpont** győződjön meg arról, hogy azok be `<YourTenantName>.b2clogin.com` helyett `login.microsoftonline.com`.
2. Ha MSAL használ, be kell `validateauthority=false`.  Ennek oka, hogy a jogkivonat-kibocsátó válik`<YourTenantName>.b2clogin.com`.