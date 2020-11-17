---
title: Az Azure AD bejelentkezési oldalán Elfogadom a Microsoft-fiókokat | Microsoft Docs
description: A bejelentkezéskor a képernyőn megjelenő üzenetkezelés a felhasználónevek keresését tükrözi
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2a3c2257311f208ae704a54f1369e10b2ff160b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646848"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Bejelentkezési lehetőségek a Azure Active Directory Microsoft-fiókjaihoz

A Azure Active Directory (Azure AD) Microsoft 365 bejelentkezési lapja támogatja a munkahelyi vagy iskolai fiókokat és a Microsoft-fiókokat, de a felhasználó helyzetének függvényében az egyik vagy a másik, vagy mindkettő. Az Azure AD bejelentkezési oldala például a következőket támogatja:

* Mindkét típusú fiókból érkező bejelentkezéseket elfogadó alkalmazások
* A vendégeket elfogadó szervezetek

## <a name="identification"></a>Azonosítás
Megtudhatja, hogy a szervezet által használt bejelentkezési oldal támogatja-e a Microsoft-fiókokat a Felhasználónév mezőben található tipp szövegének megkeresésével. Ha a tipp szövege "e-mail-cím, telefon vagy Skype", a bejelentkezési oldal támogatja a Microsoft-fiókokat.

![A fiók bejelentkezési oldalai közötti különbség](./media/signin-account-support/ui-prompt.png)

A [további bejelentkezési lehetőségek csak személyes Microsoft-fiókokhoz működnek](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , de nem használhatók munkahelyi vagy iskolai fiók erőforrásaiba való bejelentkezésre.

## <a name="next-steps"></a>Következő lépések

[A bejelentkezési arculat testreszabása](../fundamentals/add-custom-domain.md)