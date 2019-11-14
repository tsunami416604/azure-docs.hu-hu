---
title: Az Azure AD bejelentkezési oldalán Elfogadom a Microsoft-fiókokat | Microsoft Docs
description: A bejelentkezéskor a képernyőn megjelenő üzenetkezelés a felhasználónevek keresését tükrözi
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024288"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Bejelentkezési lehetőségek a Azure Active Directory Microsoft-fiókjaihoz

A Azure Active Directory (Azure AD) Microsoft 365 bejelentkezési lapja támogatja a munkahelyi vagy iskolai fiókokat és a Microsoft-fiókokat, de a felhasználó helyzetének függvényében az egyik vagy a másik, vagy mindkettő. Az Azure AD bejelentkezési oldala például a következőket támogatja:

* Mindkét típusú fiókból érkező bejelentkezéseket elfogadó alkalmazások
* A vendégeket elfogadó szervezetek

## <a name="identification"></a>Szoftverazonosító
Megtudhatja, hogy a szervezet által használt bejelentkezési oldal támogatja-e a Microsoft-fiókokat a Felhasználónév mezőben található tipp szövegének megkeresésével. Ha a tipp szövege "e-mail-cím, telefon vagy Skype", a bejelentkezési oldal támogatja a Microsoft-fiókokat.

![A fiók bejelentkezési oldalai közötti különbség](./media/signin-account-support/ui-prompt.png)

A [további bejelentkezési lehetőségek csak személyes Microsoft-fiókokhoz működnek](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , de nem használhatók munkahelyi vagy iskolai fiók erőforrásaiba való bejelentkezésre.

## <a name="next-steps"></a>Következő lépések

[A bejelentkezési arculat testreszabása](../fundamentals/add-custom-domain.md)