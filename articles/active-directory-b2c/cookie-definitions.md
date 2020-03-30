---
title: Cookie-definíciók
titleSuffix: Azure AD B2C
description: Definíciókat biztosít az Azure Active Directory B2C szolgáltatásban használt cookie-khoz.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189514"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Cookie-definíciók az Azure AD B2C-hez

Az alábbi szakaszok az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban használt cookie-król nyújtanak tájékoztatást.

## <a name="samesite"></a>SameSite (Ugyanaz a webhely)

A Microsoft Azure AD B2C szolgáltatás kompatibilis a SameSite `SameSite=None` böngészőkonfigurációkkal, beleértve az `Secure` attribútum támogatását is.

A webhelyekhez való hozzáférés biztosítása érdekében a webböngészők egy új, alapértelmezés szerint biztonságos modellt vezetnek be, amely feltételezi, hogy minden cookie-t védeni kell a külső hozzáféréstől, kivéve, ha másként rendelkeznek. A Chrome böngésző az első, amely végrehajtja ezt a változást, kezdve [a Chrome 80-val 2020 februárjában](https://www.chromium.org/updates/same-site). A Chrome-ban a változásra való felkészülésről a Fejlesztők: Felkészülés az [új SameSite=None; Biztonságos cookie-beállítások](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) a Chromium Blog.

A fejlesztőknek az új `SameSite=None`cookie-beállítást kell használniuk a cookie-k kijelöléséhez a webhelyek közötti hozzáféréshez. Ha `SameSite=None` az attribútum jelen van, egy további `Secure` attribútumot kell használni, hogy a webhelyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül érhetők el. Érvényesítse és tesztelje az összes alkalmazást, beleértve az Azure AD B2C-t használó alkalmazásokat is.

További információkért lásd:

* [SameSite-cookie-k változásainak kezelése a Chrome böngészőben](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Az ügyfelek webhelyeire és a Microsoft szolgáltatásaira és termékeire gyakorolt hatás a Chrome 80-as vagy újabb verziójában](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookie-k

Az alábbi táblázat az Azure AD B2C-ben használt cookie-kat sorolja fel.

| Név | Domain | Lejárat | Cél |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | A [böngészőmunkamenet vége](session-behavior.md) | Felhasználói tagsági adatokat tart a bérlők között. A bérlők a felhasználó tagja, és a tagság i. szint (rendszergazda vagy felhasználó). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | A kérelmek megfelelő termelési példányba történő továbbítására szolgál. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | A tranzakciók (az Azure AD B2C hitelesítési kérelmek száma) és az aktuális tranzakció nyomon követésére szolgál. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | Az SSO-munkamenet karbantartására szolgál. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet](session-behavior.md)vége , sikeres hitelesítés | A kérelem állapotának karbantartására szolgál. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | A CRSF védelméhez használt helyek közötti kérelemhamisítási token. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | Az Azure AD B2C hálózati útválasztáshoz használt. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | Környezet |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | Az erőforrás-szolgáltató bérlőtagsági adatainak tárolására szolgál. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, márkás tartomány | A [böngészőmunkamenet vége](session-behavior.md) | A váltósüti tárolására szolgál. |
