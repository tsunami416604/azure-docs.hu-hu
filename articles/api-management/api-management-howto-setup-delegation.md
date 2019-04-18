---
title: Annak a felhasználói és termékelőfizetés delegálása
description: Ismerje meg, hogyan delegálhat felhasználói és termékelőfizetés harmadik félnek az Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: 796bea3c64ef7fc03367707461d13e0ea2514b8b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051756"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Annak a felhasználói és termékelőfizetés delegálása

Delegálás lehetővé teszi, hogy használja a meglévő webhely kezelése fejlesztői bejelentkezési / regisztrációs és előfizetés-termékekhez, ellentétben a fejlesztői portál beépített funkciójával. Ez lehetővé teszi a felhasználói adatokat, és hajtsa végre az alábbi lépéseket az érvényesítés egyéni módon webhelyét.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Jelentkezzen be, és regisztráljon fejlesztői delegálása

Fejlesztői bejelentkezési delegálásában, és jelentkezzen be a meglévő webhely, lesz szüksége a webhely speciális delegálási végpont létrehozásához. Ahhoz, mint az API Management fejlesztői portálon kezdeményezett kérelem belépési ponttal kell rendelkeznie.

A végső munkafolyamat a következő lesz:

1. Fejlesztői kattintással a bejelentkezési vagy regisztrációs látható kapcsolat hivatkozásra, az API Management fejlesztői portálon
2. Böngésző átirányítja a delegálási végpont
3. A delegálási végpont cserébe átirányítja a felhasználót, vagy megadja a UI arra kéri a felhasználót a bejelentkezéshez, vagy regisztráljon
4. Sikeres, a rendszer átirányítja a felhasználót vissza az API Management fejlesztői portál oldala ezek a lépések

Első lépésként hozzunk első beállítása az API Management irányíthatja a kérelmeket a delegálási végpont. Az API Management közzétevői portál, kattintson a **biztonsági** és kattintson a **delegálás** fülre. Kattintson a bejelentkezés delegálása & regisztráció engedélyezése jelölőnégyzet bejelölésével.

![Delegálás lap][api-management-delegation-signin-up]

* Döntse el, mi a speciális delegálási végpont URL-címe lesz kell, és írja be a a **delegálási végpont URL-címe** mező. 
* A delegálás-hitelesítési kulcs mező adja meg egy titkos kulcsot, győződjön meg arról, hogy a kérelem valóban érkezik az Azure API Management-ellenőrzés az Ön számára biztosított aláírás kiszámításához használandó. Kattintson a **készítése** gombbal véletlenszerűen hozzon létre egy kulcsot, az API Management rendelkezik.

Most szeretne létrehozni a **delegálási végpont**. Számos művelet végrehajtásához rendelkezik:

1. Kap egy kérelmet a következő formátumban:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Lekérdezési paramétert a bejelentkezési / eset regisztráció:
   
   * **a művelet**: azonosítja a delegálás kérelem – csak azok milyen típusú **bejelentkezési** ebben az esetben
   * **returnUrl**: az oldal, ahol a felhasználó egy bejelentkezési gombra kattintott, vagy -regisztrációs hivatkozást az URL-cím
   * **védőérték**: egy speciális védőérték karakterlánc, amellyel biztonsági kivonatok
   * **SIG**: összehasonlítás a saját használandó számított biztonsági kivonatot kiszámított kivonat
2. Győződjön meg arról, hogy a kérés érkezik az Azure API Management (nem kötelező, de erősen ajánlott a biztonság)
   
   * COMPUTE-HMAC-SHA512 kivonata alapján karakterláncot a **returnUrl** és **védőérték** lekérdezési paramétert ([alábbi példakód]):
     
     > HMAC-val (**védőérték** + "\n" + **returnUrl**)
     > 
     > 
   * Hasonlítsa össze a fenti kiszámított kivonat értékéhez a **sig** lekérdezési paraméter. Ha a két kivonatok megegyeznek, léphet tovább a következő lépéssel, ellenkező esetben visszautasítja a kérelmüket.
3. Győződjön meg arról, hogy a bejelentkezési kérelem azért küldtük Önnek, / regisztrációs: a **művelet** értékre lesz beállítva a lekérdezési paraméter "**bejelentkezési**".
4. Jelentkezzen be vagy Regisztráljon a felhasználói felületének a felhasználók bemutatásához
5. Ha a felhasználó regisztrál akkor hozzon létre egy megfelelő fiókot a számukra az API Management szolgáltatásban. [Felhasználó létrehozása] az API Management REST API-val. Ha ezt arról, hogy ugyanazt az értéket, ahogy a felhasználókhoz tartozó tárolóban vagy egy Azonosítót, amely akkor is nyomon követheti, beállítva a felhasználói azonosító.
6. Amikor a felhasználó sikeres hitelesítése:
   
   * [egyszeri bejelentkezéses (SSO) jogkivonat kérése] az API Management REST API-val
   * lekérdezés returnUrl paraméter hozzáfűzése a fent az API-hívás kapott egyszeri bejelentkezési URL-címe:
     
     > Ha például https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * a fenti előállított URL-címet átirányítja a felhasználót

Mellett a **bejelentkezési** művelet is elvégezheti felhasználóifiók-kezelés az előző lépések mentén, és a következő műveletek valamelyikének használatával:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Fiókkezelési műveletek a lekérdezési paraméterek meg kell felelnie.

* **a művelet**: azonosítja, milyen típusú delegálás kérelem (ChangePassword, ChangeProfile vagy CloseAccount)
* **felhasználói azonosító**: a felhasználói azonosító, a fiók kezelése
* **védőérték**: egy speciális védőérték karakterlánc, amellyel biztonsági kivonatok
* **SIG**: összehasonlítás a saját használandó számított biztonsági kivonatot kiszámított kivonat

## <a name="delegate-product-subscription"> </a>Termék-előfizetés delegálása
Termék-előfizetés delegálása működik, és hasonlóképpen delegálása felhasználói bejelentkezési/fel. A végső munkafolyamat a következő lenne:

1. Fejlesztői termék kiválasztása az API Management fejlesztői portálon, és a feliratkozás gombra kattint.
2. Böngésző a rendszer átirányítja a delegálási végpont.
3. A delegálási végpont szükséges termék-előfizetés lépést hajt végre. Ez legfeljebb lesz tervezheti meg a lépéseket. Előfordulhat, hogy tartalmazzák a átirányítása a számlázási adatokat, további kérdések vagy az információk tárolásának és nem igényelnek felhasználói beavatkozás igénylése egy másik lapot.

A funkció engedélyezéséhez kattintson a **delegálás** lapon kattintson **termék-előfizetés delegálása**.

Ezután győződjön meg, hogy a delegálási végpont does a következő műveleteket:

1. Kap egy kérelmet a következő formátumban:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation= {operation} & productId = {előfizetni a termék} & userId = {a felhasználó kérést} & só = {string} & sig = {string}*
   >
   
    Lekérdezési paraméterek a termék-előfizetés esethez:
   
   * **a művelet**: azonosítja a delegálás kérelem milyen típusú legyen. Termék-előfizetés kérések az érvényes beállítások a következők:
     * "Előfizetés": egy kérelmet a felhasználó számára egy adott termék-előfizetés megadott azonosítója (lásd alább)
     * "Ez az előfizetés lemondása": a felhasználó a termékből leiratkozás irányuló kérelem
     * "Megújítása": (például, hogy előfordulhat, hogy lehet lejár) előfizetés megújításának kéréséhez
   * **Termékazonosító**: a felhasználó azt kérte, iratkozzon fel a termék azonosítója
   * **subscriptionId**: a *Unsubscribe* és *megújítása* – a termék-előfizetés azonosítója
   * **felhasználói azonosító**: a kérelem érkezik a felhasználó azonosítója.
   * **védőérték**: egy speciális védőérték karakterlánc, amellyel biztonsági kivonatok
   * **SIG**: összehasonlítás a saját használandó számított biztonsági kivonatot kiszámított kivonat

2. Győződjön meg arról, hogy a kérés érkezik az Azure API Management (nem kötelező, de erősen ajánlott a biztonság)
   
   * COMPUTE-HMAC-SHA512 egy karakterlánc alapján a **productId**, **userId**, és **védőérték** lekérdezési paramétert:
     
     > HMAC-val (**védőérték** + "\n" + **productId** + "\n" + **userId**)
     > 
     > 
   * Hasonlítsa össze a fenti kiszámított kivonat értékéhez a **sig** lekérdezési paraméter. Ha a két kivonatok megegyeznek, léphet tovább a következő lépéssel, ellenkező esetben visszautasítja a kérelmüket.
3. A kért művelet típusa alapján termék-előfizetés feldolgozását **művelet** – például számlázás, további kérdéseket, és így tovább.
4. Sikeresen megtörtént a felhasználó feliratkoztatása a saját rendszerében a termék, a Feliratkozás a felhasználó által az API Management-termékhez [a REST API hívásának a termék-előfizetés].

## <a name="delegate-example-code"> </a> Példakód

Hogyan code ezek a minták megjelenítése a:

* Igénybe vehet a *delegálás érvényesítési kulcsa*, amelynek beállítása a publisher Portalról delegálás képernyőjén
* Hozzon létre egy HMAC-val, majd használt érvényesíteni az aláírást, az átadott returnUrl érvényességét igazolására.

Ugyanazt a kódot a productId és a felhasználói azonosító enyhe módosítással működik.

**C#kód létrehozásához returnUrl kivonata**

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

**NodeJS-kód készítése returnUrl kivonata**

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
A delegálás további információkért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[egyszeri bejelentkezéses (SSO) jogkivonat kérése]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Felhasználó létrehozása]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[a REST API hívásának a termék-előfizetés]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[alábbi példakód]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
