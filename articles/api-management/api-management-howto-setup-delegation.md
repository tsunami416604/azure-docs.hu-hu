---
title: A felhasználói regisztráció és a termék-előfizetés delegálása
description: Ismerje meg, hogyan delegálhatja a felhasználói regisztrációt és a termék-előfizetést egy harmadik félnek az Azure API Management ben.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454354"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>A felhasználói regisztráció és a termék-előfizetés delegálása

A delegálás lehetővé teszi, hogy a meglévő webhelyét a fejlesztői bejelentkezés/regisztráció és a termékekre való előfizetés kezelésére használja, szemben a fejlesztői portál beépített funkcióinak használatával. Lehetővé teszi, hogy a webhely a felhasználói adatok at birtokolja, és egyéni módon hajtsa végre a lépések érvényesítését.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Fejlesztői bejelentkezés és regisztráció delegálása

Fejlesztő delegálásához, bejelentkezéshez és a meglévő webhelyre való regisztrációhoz létre kell hoznia egy speciális delegálási végpontot a webhelyén. Az API Management fejlesztői portálról kezdeményezett ilyen kérelmek belépési pontjaként kell működnie.

A végső munkafolyamat a következő lesz:

1. A fejlesztő az API Management fejlesztői portálon található bejelentkezési vagy regisztrációs hivatkozásra kattint
2. A böngésző átlesz irányítva a delegálási végpontra
3. A végpont delegálása a felhasználói felületre irányít, vagy bemutatja a felhasználói felületet, amely arra kéri a felhasználót, hogy jelentkezzen be vagy regisztráljon
4. A siker, a felhasználó átirányítja vissza az API Management fejlesztői portál oldalon indult

Először is, először állítsa be az API Managementet a kérelmek továbbításához a delegálási végponton keresztül. Az Azure Portalon keressen **a biztonság** az API-felügyeleti erőforrásban, majd kattintson a **delegálási** elemre. A "Meghatalmazottbejelentkezés & regisztráció" engedélyezéséhez kattintson a jelölőnégyzetre.

![Delegálás lap][api-management-delegation-signin-up]

* Döntse el, hogy mi legyen a különleges delegálási végpont URL-címe, és írja be a **delegálási végpont URL-mezőjébe.** 
* A Delegálás hitelesítési kulcs mezőben adjon meg egy titkos kulcsot, amely et az ellenőrzéshez megadott aláírás kiszámításához kell használni annak érdekében, hogy a kérelem valóban az Azure API Managementtől származik.Within the Delegation authentication key field, enter a secret that will be used to compute a signature provided to verification to ensure that the request is from Azure API Management. A **létrehozás** gombra kattintva az API Management véletlenszerűen generálhat egy kulcsot.

Most létre kell hoznia a **delegálási végpontot.** Számos műveletet kell végrehajtania:

1. Kérés fogadása a következő formában:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL forráslap}&salt={string}&sig={string}*
   > 
   > 
   
    Lekérdezési paraméterek a bejelentkezési/regisztrációs esethez:
   
   * **operation**: azonosítja, hogy milyen típusú delegálási kérelemről van szó - ebben az esetben csak **a SignIn** lehet
   * **returnUrl**: annak az oldalnak az URL-címe, amelyre a felhasználó egy bejelentkezési vagy feliratkozási linkre kattintott
   * **só:** egy speciális sóhúr, amelyet biztonsági kivonat kiszámításához használnak
   * **szig:** egy számított biztonsági kivonat, amelyet a saját számítási kivonatával való összehasonlításra kell használni
2. Ellenőrizze, hogy a kérelem az Azure API Management-től érkezik-e (nem kötelező, de erősen ajánlott a biztonság érdekében)
   
   * Számítson ki egy karakterlánc HMAC-SHA512 kivonatát a **returnUrl** és a **só** lekérdezési paraméterei alapján[(példakód alább):]
     
     > HMAC(**só** + '\n' + **returnUrl**)
     > 
     > 
   * Hasonlítsa össze a fent számított kivonatot a **sig** lekérdezési paraméter értékével. Ha a két kihek egyeznek, lépjen a következő lépésre, ellenkező esetben utasítsa el a kérést.
3. Ellenőrizze, hogy kap-e bejelentkezési/bejelentkezési kérelmet: a **működési** lekérdezési paraméter "**SignIn**" lesz.
4. A felhasználó felhasználói felületének bemutatása a bejelentkezéshez vagy a regisztrációhoz
5. Ha a felhasználó regisztrál, létre kell hoznia egy megfelelő fiókot az API Management ben. [Hozzon létre egy felhasználót] az API Management REST API-val. Ha így tesz, győződjön meg arról, hogy a felhasználói azonosítót ugyanarra az értékre állítja, mint a felhasználói tárolóban, vagy egy olyan azonosítóra, amelyet nyomon követhet.
6. A felhasználó sikeres hitelesítése után:
   
   * [egyszeri bejelentkezési (SSO) jogkivonat kérése] az API Management REST API-n keresztül
   * csatoljon egy returnUrl lekérdezési paramétert a fenti API-hívásból kapott SSO URL-címhez:
     
     > például,https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * a felhasználó átirányítása a fenti URL-re

A **SignIn-műveletmellett** az előző lépések végrehajtásával és az alábbi műveletek egyikének használatával is végrehajthatja a fiókkezelést:

* **ChangePassword (Jelszó módosítása)**
* **ChangeProfile (Változásprofil)**
* **Fiók bezárása**

A fiókkezelési műveletekhez a következő lekérdezési paramétereket kell átadnia.

* **operation**: azonosítja, hogy milyen típusú delegálási kérelemről van szó (ChangePassword, ChangeProfile vagy CloseAccount)
* **userId**: a kezelni a kezelt fiók felhasználói azonosítója
* **só:** egy speciális sóhúr, amelyet biztonsági kivonat kiszámításához használnak
* **szig:** egy számított biztonsági kivonat, amelyet a saját számítási kivonatával való összehasonlításra kell használni

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Termék-előfizetés delegálása
A termék-előfizetés delegálása hasonlóan működik a felhasználó bejelentkezési/-up delegálásához. A végső munkafolyamat a következő lenne:

1. A fejlesztő kiválaszt egy terméket az API Management fejlesztői portálon, és rákattint az Előfizetés gombra.
2. A böngésző átlesz irányítva a delegálási végpontra.
3. A delegálási végpont végrehajtja a szükséges termék-előfizetési lépéseket. Ez rajtad múlik, hogy tervezze meg a lépéseket. Ezek közé tartozik az átirányítás egy másik oldalra, hogy számlázási adatokat kérjen, további kérdéseket tesz fel, vagy egyszerűen tárolja az információkat, és nem igényel felhasználói beavatkozást.

A funkció engedélyezéséhez a **Delegálás** lapon kattintson a **Termék-előfizetés delegálása gombra.**

Ezután győződjön meg arról, hogy a delegálási végpont a következő műveleteket végzi:

1. Kérés fogadása a következő formában:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    A termék-előfizetési eset lekérdezési paraméterei:
   
   * **operation**: meghatározza, hogy milyen típusú delegálási kérelemről van szó. A termék-előfizetési kérelmek esetében az érvényes lehetőségek a következők:
     * "Feliratkozás": a felhasználó feliratkozási kérése egy adott termékre a megadott azonosítóval (lásd alább)
     * "Leiratkozás": egy felhasználó leiratkozására irányuló kérés egy termékről
     * "Megújítás": előfizetés megújítására irányuló kérelem (például lejáró)
   * **productId:** annak a terméknek az azonosítója, amelyre a felhasználó elő fizetni kívánt
   * **subscriptionId**: a *Leiratkozás* és *a Megújítás* - a termék-előfizetés azonosítója
   * **userId**: annak a felhasználónak az azonosítója, amelyhez a kérelem érkezik
   * **só:** egy speciális sóhúr, amelyet biztonsági kivonat kiszámításához használnak
   * **szig:** egy számított biztonsági kivonat, amelyet a saját számítási kivonatával való összehasonlításra kell használni

2. Ellenőrizze, hogy a kérelem az Azure API Management-től érkezik-e (nem kötelező, de erősen ajánlott a biztonság érdekében)
   
   * Számítson ki egy hmac-SHA512 karakterláncot a **productId,** **userId**és **salt** query paraméterek alapján:
     
     > HMAC(**só** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Hasonlítsa össze a fent számított kivonatot a **sig** lekérdezési paraméter értékével. Ha a két kihek egyeznek, lépjen a következő lépésre, ellenkező esetben utasítsa el a kérést.
3. A termék-előfizetés feldolgozása a **működés** során kért művelet típusa alapján - például számlázás, további kérdések stb.
4. Ha sikeresen előfizet a felhasználóra az Ön oldalán lévő termékre, iratkozzon fel a felhasználóra az API Management [termékre az előfizetések REST API-jának felhívásával.]

## <a name="example-code"></a><a name="delegate-example-code"> </a> Példa kód

Ezek a kódminták bemutatják, hogyan:

* A *közzétevői*portál Delegálás irtógombjának elvetése
* Hozzon létre egy HMAC, amely ezután az aláírás érvényesítésére szolgál, bizonyítva az átadott returnUrl érvényességét.

Ugyanez a kód működik a productId és userId enyhe módosítással.

**C# kód a returnUrl kivonatának létrehozásához**

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

**NodeJS-kód a returnUrl kivonatának létrehozásához**

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

> [!IMPORTANT]
> A [delegálási](api-management-howto-developer-portal-customize.md#publish) módosítások érvénybe léptetéséhez újra közzé kell tennie a fejlesztői portált.

## <a name="next-steps"></a>További lépések
A delegálással kapcsolatos további információkért lásd az alábbi videót:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[egyszeri bejelentkezési (SSO) jogkivonat kérése]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[felhasználó létrehozása]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[a REST API hívása előfizetésekhez]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[példa kód alább megadott]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
