---
title: Cookie-definíciók – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C használt cookie-k definícióit tartalmazza.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930894"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>A Azure Active Directory B2C cookie-definíciói

A következő táblázat felsorolja a Azure Active Directory B2C használt cookie-kat.

| Név | Domain | Lejárat | Rendeltetés |
| ----------- | ------ | -------------------------- | --------- |
| x-MS-cpim-admin | main.b2cadmin.ext.azure.com | Böngésző- [munkamenet](session-behavior.md) vége | Felhasználói tagsági adattárolást tart fenn a bérlők között. A bérlők a felhasználók és a tagsági szint (rendszergazda vagy felhasználó) tagjai. |
| x-MS-cpim-slice | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A kérelmeknek a megfelelő éles példányra való továbbítására szolgál. |
| x-MS-cpim-Trans | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A tranzakciók nyomon követésére szolgál (a Azure AD B2C hitelesítési kérések száma) és az aktuális tranzakcióval. |
| x-MS-cpim-SSO: {ID} | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Az SSO-munkamenet fenntartásához használatos. |
| x-MS-cpim-cache: {ID} _n | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md)vége, sikeres hitelesítés | A kérelem állapotának fenntartásához használatos. |
| x-MS-cpim-CSRF | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A CRSF-védelemhez használt, helyek közötti kérelem hamis jogkivonata. |
| x-MS-cpim-DC | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Azure AD B2C hálózati útválasztáshoz használatos. |
| x-MS-cpim-CTX | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Környezet |
| x-MS-cpim-RP | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Az erőforrás-szolgáltató bérlő tagsági adattárolására szolgál. |
| x-MS-cpim-RC | login.microsoftonline.com, b2clogin.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A Relay cookie tárolására szolgál. |
