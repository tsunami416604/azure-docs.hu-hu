---
title: Cookie-definíciók
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használt cookie-k definícióit tartalmazza.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 399b63cab2594610260997f8e5ecef9c3c05318f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712823"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>A Azure AD B2C cookie-definíciói

A következő szakaszokban a Azure Active Directory B2C (Azure AD B2C) által használt cookie-kkal kapcsolatos információkat talál.

## <a name="samesite"></a>SameSite

Az Microsoft Azure AD B2C szolgáltatás kompatibilis a SameSite böngésző-konfigurációkkal, beleértve a `Secure` attribútummal való `SameSite=None` támogatását is.

A webhelyekhez való hozzáférés védelme érdekében a böngészők egy új, biztonságos alapértelmezett modellt vezetnek be, amely feltételezi, hogy az összes cookie-t védeni kell a külső hozzáféréstől, hacsak másként nincs megadva. Ennek a változásnak a megkezdéséhez a Chrome böngésző az első lépés a [chrome 80-es](https://www.chromium.org/updates/same-site)verziójában a 2020 februárjában. A Chrome változásának előkészítésével kapcsolatos további információkért lásd [: fejlesztőknek: Felkészülés az új SameSite = none; Biztonságos cookie-beállítások](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) a Chromium blogon.

A fejlesztőknek az új cookie-beállítást kell használniuk, `SameSite=None`, hogy kijelölje a cookie-kat a helyek közötti hozzáféréshez. Ha a `SameSite=None` attribútum létezik, akkor további `Secure` attribútumot kell használni, hogy a helyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül legyenek elérhetők. Érvényesítse és tesztelje az összes alkalmazást, beleértve a Azure AD B2Ct használó alkalmazásokat is.

További információ: [az ügyfél webhelyein és a Microsoft-szolgáltatásokban és-termékeken a Chrome 80-es vagy újabb verziójának hatása](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome).

## <a name="cookies"></a>Sütik

A következő táblázat felsorolja a Azure AD B2C használt cookie-kat.

| Név | Domain | Lejárati | Rendeltetés |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Böngésző- [munkamenet](session-behavior.md) vége | Felhasználói tagsági adattárolást tart fenn a bérlők között. A bérlők a felhasználók és a tagsági szint (rendszergazda vagy felhasználó) tagjai. |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A kérelmeknek a megfelelő éles példányra való továbbítására szolgál. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A tranzakciók nyomon követésére szolgál (a Azure AD B2C hitelesítési kérések száma) és az aktuális tranzakcióval. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Az SSO-munkamenet fenntartásához használatos. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md)vége, sikeres hitelesítés | A kérelem állapotának fenntartásához használatos. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A CRSF-védelemhez használt, helyek közötti kérelem hamis jogkivonata. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Azure AD B2C hálózati útválasztáshoz használatos. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Környezet |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | Az erőforrás-szolgáltató bérlő tagsági adattárolására szolgál. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, márkás tartomány | Böngésző- [munkamenet](session-behavior.md) vége | A Relay cookie tárolására szolgál. |
