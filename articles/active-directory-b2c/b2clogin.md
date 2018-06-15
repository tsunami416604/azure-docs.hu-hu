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
ms.openlocfilehash: c41c02acaeffa170d55f3c59f34a4b1ecae1c523
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712453"
---
# <a name="using-b2clogincom"></a>A b2clogin.com használata

>[!IMPORTANT]
>Ez a szolgáltatás csak a nyilvános előzetes verzió 
>

Most már rendelkezik a lehetőséget az Azure AD B2C-bA `<YourTenantName>.b2clogin.com` helyett `login.microsoftonline.com`.  Ennek a számos előnye van:
* Az azonos cookie-k fejlécek méretkorlátját lesz többé nem osztja, a más Microsoft-termékekkel
* Eltávolíthatja a Microsoft mutató összes hivatkozást az URL-címben (lecserélheti `<YourTenantName>.onmicrosoft.com` a bérlő azonosítójú)

 B2clogin.com előnyeit, be kell állítani néhány a következőket:

1. Az a **futtatása most végpont** győződjön meg arról, hogy azok be `<YourTenantName>.b2clogin.com` helyett `login.microsoftonline.com`.
2. Ha MSAL használ, be kell `validateauthority=false`.  Ennek oka, hogy a jogkivonat-kibocsátó válik`<YourTenantName>.b2clogin.com`.