---
title: "Hogyan kell delegálni a felhasználói regisztráció és a termék-előfizetéshez"
description: "Tudnivalók a felhasználói regisztráció és a termék előfizetés harmadik félnek az Azure API Management delegálása."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: fc8c5774eb616c33c00ecebeacd31e2a07b36e0c
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Hogyan kell delegálni a felhasználói regisztráció és a termék-előfizetéshez
Delegálás lehetővé teszi a meglévő webhely használatát fejlesztői bejelentkezési-a/regisztrációs és termékek a fejlesztői portálra beépített funkcióval szemben-előfizetés kezeléséhez. Ez lehetővé teszi, hogy a webhely tulajdonosai a felhasználó adatait, és hajtsa végre az alábbi lépéseket az érvényesítés egy egyedi módon.

## <a name="delegate-signin-up"> </a>Fejlesztői sign in és regisztrációs delegálása
Delegálása fejlesztői bejelentkezési és regisztrációs meglévő webhelyéhez, szüksége lesz különleges delegálás-végpont létrehozása a webhely, amely különbséglemezként funkcionál az API Management developer portálról kezdeményezett kérelem belépési pontját.

A végső munkafolyamat a következők:

1. Fejlesztői gombra kell kattintania a bejelentkezési és regisztrációs hivatkozásra, az API Management fejlesztői portálján
2. Böngésző átirányítja a delegálás végpont
3. Delegálás végpont ismét átirányítja a felhasználókat a, vagy megadja a felhasználói Felületet kérni a felhasználói bejelentkezés az előfizetési
4. A sikeres, a felhasználót a rendszer átirányítja az API Management developer portálon oldalra azok indította el

Első lépésként most irányíthatja az első telepítés API Management kérelmek delegálás végpontot keresztül. Az API Management publisher portálon, kattintson a **biztonsági** , majd a **delegálás** fülre. Kattintson a "Delegált bejelentkezési és regisztrációs" engedélyezése jelölőnégyzet.

![Delegálási lapra][api-management-delegation-signin-up]

* Döntse el, mi a különleges delegálás végpont URL-CÍMÉT a rendszer, majd adja meg a legyen a **delegálás végponti URL-cím** mező. 
* Delegálás hitelesítési kulcs mezőjében győződjön meg arról, hogy a kérelem valóban származik Azure API Management-ellenőrzés az Ön számára biztosított aláírás kiszámításához használt titkos kulcs megadása Kattintson a **készítése** véletlenszerű előállításához a kulcs akkor API Management gomb.

Most kell létrehoznia a **delegálás végpont**. Van több műveletet végrehajtani:

1. A kérés fogadásához a következő formában:
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    A bejelentkezés paramétereinek lekérdezése / eset regisztráció:
   
   * **a művelet**: delegálás kérés – csak azok milyen típusú azonosítja **SignIn** ebben az esetben
   * **returnUrl**: a lap, ahol a felhasználó egy bejelentkezési vagy előfizetési hivatkozásra kattint az URL-cím
   * **védőérték**: biztonsági kivonatát kiszámításához használt különleges védőérték karakterlánc
   * **SIG**: összehasonlítása a saját használandó számított biztonsági kivonatát számított kivonata
2. Győződjön meg arról, hogy a kérelem érkezik Azure API Management (nem kötelező, de erősen ajánlott a biztonság)
   
   * Egy olyan karakterlánc alapján HMAC-SHA512 kivonatának számítási a **returnUrl** és **védőérték** lekérdezési paramétert ([alábbi példakód]):
     
     > HMAC (**védőérték** + "\n" + **returnUrl**)
     > 
     > 
   * A fenti számított kivonat értékének összehasonlítása a **sig** lekérdezési paraméter. Ha a két kivonatok megegyeznek, lépjen tovább a következő lépés, egyébként visszautasítja a kérelmet.
3. Győződjön meg arról, hogy fordulnak elő a bejelentkezési/sign up kérelmet: a **művelet** úgy lesz beállítva, lekérdezési paraméter "**SignIn**".
4. A felhasználó bevezetését a bejelentkezési, regisztrációs vagy a felhasználói felületen
5. Ha a felhasználó regisztrált kell létrehoznia a megfelelő fiókkal számukra az API Management. [Hozzon létre egy felhasználót] API Management REST API-val. Annak során, győződjön meg arról, hogy állítsa a felhasználói Azonosítóját, ugyanaz a felhasználókhoz tartozó tárolóban van, vagy egy azonosító, akkor is nyomon követésére szolgáló.
6. Amikor a rendszer sikeresen hitelesíteni a felhasználót:
   
   * [egyszeri bejelentkezéses (SSO) jogkivonatot kérni] az API Management REST API-n keresztül
   * egy returnUrl lekérdezési paraméter hozzáfűzése fent API-hívás kapott egyszeri bejelentkezési URL-címe:
     
     > for example, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * a fenti létrehozott URL-címet átirányítja a felhasználót

Kívül a **SignIn** művelet is végrehajthat felhasználóifiók-kezelés az előző lépések és a következő műveletek egyikével:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Át kell adnia a következő lekérdezés-paraméterek a fiókkezelési műveletekhez.

* **a művelet**: azonosítja (a ChangePassword, ChangeProfile vagy CloseAccount) delegálás kérelem típusa
* **userId**: a fiók kezeléséhez a felhasználói azonosító
* **védőérték**: biztonsági kivonatát kiszámításához használt különleges védőérték karakterlánc
* **SIG**: összehasonlítása a saját használandó számított biztonsági kivonatát számított kivonata

## <a name="delegate-product-subscription"> </a>A termék-előfizetéshez delegálása
Termék-előfizetéshez delegálása működik, hasonlóképpen delegálása a felhasználói bejelentkezés/felfelé. A végső munkafolyamata a következőképpen nézne ki:

1. Fejlesztői termék kiválasztása az API Management developer portálon, és az előfizetés gombra kattint
2. Böngésző átirányítja a delegálás végpont
3. Delegálás endpoint szükséges termék előfizetés lépéseket végzi el – ez Öntől, és kérje a számlázási adatokat, további kérdések vagy az információk tárolásának és felhasználói beavatkozást nem igénylő másik lapra irányít át járhat

A funkció engedélyezéséhez kattintson a **delegálás** kattintson **delegálja a termék-előfizetéshez**.

Gondoskodnia kell a delegálás végpont a következő műveleteket hajtja végre:

1. A kérés fogadásához a következő formában:
   
   > *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   > 
   > 
   
    Lekérdezés-paraméterek a termék-előfizetéshez eset:
   
   * **a művelet**: azonosítja a delegálás kérelem milyen típusú legyen. A termék-előfizetéshez tartozó kérelmek érvényes lehetőségek a következők:
     * "Előfizetés": egy kérelem a felhasználó számára az adott termék előfizetni a megadott azonosító (lásd alább)
     * "Leiratkozhat": egy kérelem egy felhasználó egy terméket mondhatja
     * "Megújítása": egy kérelem (például, amely lejár a is) egy előfizetés megújításához
   * **productId**: a felhasználó azt kérte előfizetni a termék Azonosítóját
   * **userId**: a felhasználó, akinek a kérelem azonosítója
   * **védőérték**: biztonsági kivonatát kiszámításához használt különleges védőérték karakterlánc
   * **SIG**: összehasonlítása a saját használandó számított biztonsági kivonatát számított kivonata
2. Győződjön meg arról, hogy a kérelem érkezik Azure API Management (nem kötelező, de erősen ajánlott a biztonság)
   
   * Egy olyan karakterlánc alapján HMAC-SHA512 számítási a **productId**, ** felhasználói azonosítóját, és **védőérték** lekérdezési paramétert:
     
     > HMAC (**védőérték** + "\n" + **productId** + "\n" + **userId**)
     > 
     > 
   * A fenti számított kivonat értékének összehasonlítása a **sig** lekérdezési paraméter. Ha a két kivonatok megegyeznek, lépjen tovább a következő lépés, egyébként visszautasítja a kérelmet.
3. Hajtsa végre a kért művelet típusa alapján bármely termék feldolgozási **művelet** – például számlázási, további kérdésekre, stb.
4. Sikeresen előfizetés a felhasználót, hogy a termék a oldalon, az előfizetés a felhasználó által az API Management termékre [termék-előfizetéshez tartozó REST API hívása].

## <a name="delegate-example-code"> </a> Mintakód
Ezek mintakódok bemutatják, hogyan érvénybe a *delegálás érvényesítési kulcs*, melynek értéke a közzétevő portál delegálás képernyőjén hozzon létre egy HMAC, amit a aláírás érvényesítéséhez, igazolására az átadott érvényességét returnUrl. A termékkód és a felhasználói azonosítóját, enyhe módosítással működik ugyanazt a kódot.

**C#-kódban returnUrl kivonatának létrehozásához**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS kód returnUrl kivonatának létrehozásához**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>További lépések
Delegálás további információkért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[egyszeri bejelentkezéses (SSO) jogkivonatot kérni]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Felhasználó létrehozása]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[termék-előfizetéshez tartozó REST API hívása]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[alábbi példakód]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
