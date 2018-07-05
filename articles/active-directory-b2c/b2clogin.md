---
title: A b2clogin.com használata |} A Microsoft Docs
description: Ismerje meg a b2clogin.com használata helyett login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440630"
---
# <a name="using-b2clogincom"></a>A b2clogin.com használata

>[!IMPORTANT]
>Ez a funkció csak a nyilvános előzetes verzió 
>

Mostantól lehetősége az Azure AD B2C szolgáltatással rendelkező `<YourTenantName>.b2clogin.com` használata helyett `login.microsoftonline.com`.  Ez számos előnye van:
* Már nem oszthat a cookie-k azonos fejlécek méretkorlátját a más Microsoft-termékekkel.
* Minden hivatkozás, amelyben a Microsoft eltávolíthatja az URL-címben (lecserélheti `<YourTenantName>.onmicrosoft.com` az a bérlő Azonosítóját). Például: `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 Annak érdekében, hogy kihasználhassa a b2clogin.com-on, megadhatja a következőket kell:

1. Az a **Futtatás most végponton** győződjön meg arról, hogy használ `<YourTenantName>.b2clogin.com` használata helyett `login.microsoftonline.com`.
2. Az MSAL használatakor be kell `validateauthority=false`.  Ennek oka, hogy a jogkivonat kibocsátója válik`<YourTenantName>.b2clogin.com`.