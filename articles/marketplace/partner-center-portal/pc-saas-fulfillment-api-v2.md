---
title: SaaS-teljesítési API-k v2 a Microsoft kereskedelmi piactéren
description: Ismerje meg, hogyan hozhat létre és kezelhet SaaS-ajánlatokat a Microsoft AppSource és az Azure Marketplace-en a teljesítési API-k 2-es verziójának használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: f40da30ff0d702078861367dea810cc8ca1ab91b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87305142"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>SaaS-teljesítési API-k 2-es verziója a Microsoft kereskedelmi piactéren

Ez a cikk azokat az API-kat ismerteti, amelyek lehetővé teszik, hogy a partnerek a SaaS-ajánlatokat eladják Microsoft AppSource és az Azure Marketplace-en Az ilyen API-kkal való integráció megvalósításához közzétevőre van szükség az SaaS-ajánlatok a partner Centerben való közzétételéhez.

## <a name="managing-the-saas-subscription-life-cycle"></a>Az SaaS-előfizetési életciklus kezelése

Az Azure Marketplace egy SaaS-előfizetés teljes életciklusát a végfelhasználó által megvásárolt vásárlást követően kezeli.  A Kezdőlap, a beteljesülő API-k, az Operations API-k és a webhook használatával hajtja végre a tényleges SaaS-előfizetés aktiválását és használatát, a frissítéseket és az előfizetés törlését.  A végfelhasználói számla a Microsoft által fenntartott SaaS-előfizetés állapotától függ. 

### <a name="states-of-a-saas-subscription"></a>SaaS-előfizetés állapota

A SaaS-előfizetés és a vonatkozó műveletek állapota látható.

![SaaS-előfizetés életciklusa a piactéren](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Megvásárolt, de még nincs aktiválva (*PendingFulfillmentStart*)

Ha egy végfelhasználó (vagy CSP) SaaS-ajánlatot vásárol a piactéren, a közzétevőt értesíteni kell a vásárlásról, hogy új SaaS-fiókot hozzon létre és konfiguráljon a közzétevő oldalán.

Fiók létrehozásához:

1. Az ügyfélnek a Microsoft AppSource vagy Azure Portal sikeres vásárlása után a SaaS-ajánlatok számára elérhető **configure (Konfigurálás** ) gombra kell kattintania. Vagy az e-mailben, amelyet az ügyfél a vásárlás után hamarosan kap.
2. Ezt követően a Microsoft értesíti a partnert a vásárlásról, ha megnyitja az új böngésző lapon a Kezdőlap URL-címét a jogkivonat paraméterrel (a piactér-beli vásárlási azonosító jogkivonat).

Ilyen hívás például az, hogy `https://contoso.com/signup?token=<blob>` az SaaS-ajánlat kezdőlapjának URL-címe a partner Centerben van konfigurálva `https://contoso.com/signup` . Ez a jogkivonat olyan azonosítót biztosít a közzétevőnek, amely egyedileg azonosítja az SaaS-vásárlást és az ügyfelet.

>[!NOTE]
>A közzétevő nem kap értesítést az SaaS-vásárlásról, amíg az ügyfél nem indítja el a Microsoft-oldalról a konfigurációs folyamatot.

A Kezdőlap URL-címének nonstop kell lennie, és futnia kell, és készen kell állnia arra, hogy minden alkalommal új hívásokat kapjon a Microsofttól. Ha a Kezdőlap elérhetetlenné válik, az ügyfelek nem regisztrálhatnak az SaaS szolgáltatásra, és nem tudják használni azt.

Ezt követően a *jogkivonatot* vissza kell adni a Microsoftnak a közzétevőtől a [SaaS-feloldási API](#resolve-a-purchased-subscription)meghívásával a `x-ms-marketplace-token header` header paraméter értékeként.  Az API-hívás feloldásának eredményeképpen a jogkivonat a SaaS-vásárlás részleteit cseréli, például a vásárlás egyedi AZONOSÍTÓját, a megvásárolt ajánlat AZONOSÍTÓját, a megvásárolt csomag AZONOSÍTÓját stb.

A kezdőlapon az ügyfélnek be kell jelentkeznie az új vagy meglévő SaaS-fiókba Azure Active Directory (HRE) egyszeri bejelentkezés (SSO) használatával.

A közzétevőnek be kell vezetnie az SSO-bejelentkezést, hogy a Microsoft ehhez a folyamathoz szükséges felhasználói élményt nyújtson.  Győződjön meg arról, hogy a több-bérlős Azure AD-alkalmazást használja, engedélyezze a munkahelyi és iskolai fiókokat, illetve a személyes Microsoft-fiókokat az egyszeri bejelentkezés konfigurálásakor.  Ez a követelmény csak a kezdőlapra vonatkozik, valamint azokra a felhasználókra, akik a Microsoft hitelesítő adataival való bejelentkezéskor átirányítják a SaaS szolgáltatáshoz. A SaaS szolgáltatás összes bejelentkezésére nem vonatkozik.

> [!NOTE]
>Ha az SSO-bejelentkezés megköveteli, hogy a rendszergazda engedélyt adjon az alkalmazásnak, a partner Centerben lévő ajánlat leírásának közzé kell tennie, hogy rendszergazdai szintű hozzáférésre van szükség. Ez a [Piactéri minősítési szabályzatoknak](https://docs.microsoft.com/legal/marketplace/certification-policies#10003-authentication-options)való megfelelés.

Miután bejelentkezett, az ügyfélnek el kell végeznie az SaaS-konfigurációt a közzétevő oldalán. Ezután a közzétevőnek meg kell hívnia az [aktiválási előfizetési API](#activate-a-subscription) -t, hogy egy olyan jelet küldjön a piactérnek, amelyen az SaaS-fiók üzembe helyezése befejeződött.
Ekkor elindul az ügyfél számlázási ciklusa. Ha az előfizetés aktiválása API-hívás sikertelen, az ügyfél nem számít fel díjat a vásárlásért.


![Üzembe helyezési forgatókönyv API-hívásai](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktív (előfizetett)

Ez az állapot a kiépített SaaS-előfizetés állandó állapota. Miután az [aktiválási API](#activate-a-subscription) -hívást feldolgozzák a Microsoft oldalon, az SaaS-előfizetés feliratkozottként van megjelölve. Az SaaS szolgáltatás most már készen áll arra, hogy az ügyfél felhasználja a kiadó oldalát, és az ügyfél számlázása megtörténik.

Ha az SaaS-előfizetés már aktív, és az ügyfél úgy dönt, hogy **elindítja az** SaaS-élményt a Azure Portal vagy a M365 felügyeleti központban, a Microsoft a *jogkivonat* -paraméterrel megegyezően hívja meg a **Kezdőlap URL-címét** , ugyanúgy, mint az aktiválási folyamatban.  A közzétevőnek különbséget kell tennie a meglévő SaaS-fiókok új vásárlása és kezelése között, és ennek megfelelően kell kezelnie a Kezdőlap URL-címét.

#### <a name="being-updated-subscribed"></a>Folyamatban van a frissítés (előfizetett)

Ez a művelet azt jelenti, hogy egy meglévő aktív SaaS-előfizetés frissítését a Microsoft és a közzétevő is feldolgozta. Ezt a frissítést kezdeményezheti

* az ügyfél a piactéren
* a szolgáltató a piactéren
* az ügyfél a közzétevő SaaS-webhelyéről (nem vonatkozik a CSP által készített vásárlásokra)

Az SaaS-előfizetések esetében két típusú frissítés érhető el:

1. Frissítési terv, ha az ügyfél egy másik csomagot választ az előfizetéshez.
1. Mennyiség frissítése, ha az ügyfél megváltoztatja a megvásárolt helyek számát az előfizetéshez

Csak aktív előfizetés lehet frissítve. Az előfizetés frissítése közben az állapota aktív marad a Microsoft oldalán.

##### <a name="update-initiated-from-the-marketplace"></a>A piactéren kezdeményezett frissítés

Ebben a folyamatban az ügyfél a M365 felügyeleti központban módosítja az előfizetési tervet vagy a helyek mennyiségét.  

1. A frissítés beírása után a Microsoft meghívja a közzétevő webhook URL-címét, amely a partner Center **kapcsolat webhook** mezőjében van konfigurálva, és megfelelő értéket biztosít a *művelethez* és egyéb releváns paraméterekhez.  
1. A közzétevő oldalának végre kell hajtania a szükséges módosításokat az SaaS szolgáltatásban, és értesítenie kell a Microsoftot a változás befejezésekor, ha meghívja az [Operation API frissítési állapotát](#update-the-status-of-an-operation).
1. Ha a javítást sikertelen állapottal küldi el a rendszer, akkor a frissítési folyamat nem fejeződik be a Microsoft oldalán.  Az SaaS-előfizetés a meglévő csomaggal és az ülőhelyek mennyiségétől függ.

A piactéren kezdeményezett frissítési forgatókönyv API-hívásainak sorát alább láthatja.

![API-hívások a piactér által kezdeményezett frissítéshez](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>A közzétevőtől kezdeményezett frissítés

Ebben a folyamatban az ügyfél az SaaS-szolgáltatástól vásárolt előfizetési tervet vagy munkaállomások mennyiségét módosítja. 

1. A közzétevő kódjának meg kell hívnia a [módosítási terv API](#change-the-plan-on-the-subscription) -t, és/vagy [módosítania kell a mennyiségi API](#change-the-quantity-of-seats-on-the-saas-subscription) -t, mielőtt a közzétevő oldalon megtenné a kért változást 

1. A Microsoft alkalmazza a módosítást az előfizetésre, majd a **kapcsolaton** keresztül értesíti a közzétevőt, hogy alkalmazza ugyanezt a módosítást.  

1. Ezt követően a közzétevőnek csak akkor kell végrehajtania a szükséges módosításokat az SaaS-előfizetésben, és értesítenie kell a Microsoftot, ha a módosítás a [művelet API frissítési állapotának](#update-the-status-of-an-operation)meghívásával történik.

Az API-hívások sorozata a közzétevő-oldal kezdeményezett frissítési forgatókönyvhöz.

![API-hívások közzétevői oldalon kezdeményezett frissítéshez](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Felfüggesztve (*felfüggesztve*)

Ez az állapot azt jelzi, hogy a rendszer nem fogadta el az ügyfél SaaS szolgáltatásának fizetését. A közzétevő értesítést kap a SaaS-előfizetések állapotáról a Microsofttól. Az értesítés a *Felfüggesztettre*beállított *Action* paraméterrel rendelkező webhook hívása útján történik.

Előfordulhat, hogy a közzétevő nem módosítja az SaaS szolgáltatást a közzétevő oldalán. Javasoljuk, hogy a közzétevő elérhetővé teszi ezeket az információkat a felfüggesztett ügyfél számára, és korlátozza vagy letiltja az ügyfél hozzáférését az SaaS szolgáltatáshoz.  A fizetés soha nem fog megjelenni.

A Microsoft az előfizetés automatikus megszakítása előtt 30 napos türelmi időszakot biztosít az ügyfélnek. Ha egy előfizetés felfüggesztett állapotban van:

* Az SaaS-fiókot az ISV-nak helyreállítható állapotban kell tartania. A teljes funkcionalitás visszaállítható az adatvesztés és a beállítások elvesztése nélkül is.
* A rendszer az előfizetésre vonatkozó visszavonási kérelmet vár, ha a türelmi időszakon belül befizetést kap, vagy a türelmi időszak végén a kiépítési kérelem a webhook-mechanizmuson keresztül történik.

Az előfizetés állapota felfüggesztve állapotra változik a Microsoft oldalon, mielőtt a közzétevő bármilyen műveletet végrehajt. Csak az aktív előfizetéseket lehet felfüggeszteni.

#### <a name="reinstated-suspended"></a>Visszaállítás (*felfüggesztve*)

Az előfizetés visszaállítás alatt áll.

Ez a művelet azt jelzi, hogy az ügyfél fizetési eszköze újra érvénybe lépett, és az SaaS-előfizetésre vonatkozó fizetés történik.  Az előfizetés visszaállítás alatt áll. Ebben az esetben: 

1. A Microsoft meghívja a webhookot egy *műveleti* paraméterrel, amely a *visszaállítás értékre* van beállítva.  
1. A közzétevő ellenőrzi, hogy az előfizetés teljesen működőképes-e a közzétevő oldalán.
1. A közzétevő meghívja a [javítási művelet API](#update-the-status-of-an-operation) -ját a sikeres állapottal.  
1. Ezután a visszaállított művelet sikeres lesz, és az ügyfél a SaaS-előfizetés után újra lesz számlázva. 
1. Ha a javítást sikertelen állapottal küldi el, a rendszer nem hajtja végre a visszaállítási folyamatot a Microsoft oldalon. Az előfizetés továbbra is felfüggesztve marad.

Ha a javítást sikertelen állapottal küldi el, a rendszer nem hajtja végre a visszaállítási folyamatot a Microsoft oldalon.  Az előfizetés továbbra is felfüggesztve marad.

Csak a felfüggesztett előfizetés állítható vissza.  Az SaaS-előfizetés visszaállítását követően az állapota felfüggesztve marad.  A művelet befejezése után az előfizetés állapota aktív lesz.

#### <a name="renewed-subscribed"></a>Megújított (*előfizetett*)

Az előfizetési időszak végén (egy hónap vagy egy év után) az SaaS-előfizetést a Microsoft automatikusan megújítja.  Az automatikus megújítási beállítás alapértelmezett értéke az összes SaaS-előfizetés esetében *igaz* . Az aktív SaaS-előfizetések továbbra is megújítva lesznek a normál lépésszám használatával. A Microsoft nem értesíti a közzétevőt az Előfizetés megújítása esetén. Az ügyfelek kikapcsolhatják az SaaS-előfizetések automatikus megújítását a M365 felügyeleti portálon vagy a Azure Portalon keresztül.  Ebben az esetben a rendszer az aktuális számlázási időszak végén automatikusan megszakítja az SaaS-előfizetést.  Az ügyfél bármikor megszakíthatja az SaaS-előfizetést.

A rendszer csak az aktív előfizetéseket újítja meg automatikusan.  Az előfizetések aktívak maradnak a megújítási folyamat során, és ha az automatikus megújítás sikeres.  A megújítás után a rendszer frissíti az előfizetési időszak kezdő és záró dátumát az új kifejezés dátumaira.

Ha az automatikus megújítás egy fizetési probléma miatt meghiúsul, az előfizetés fel lesz függesztve.  A kiadó értesítést kap.

#### <a name="canceled-unsubscribed"></a>Megszakított (*leiratkozott*) 

Az előfizetések a közzétevő helyről, Azure Portal vagy M365 felügyeleti központból egy előfizetés lemondásával érik el ezt az állapotot egy explicit ügyfél-vagy CSP-műveletre adott válaszként.  Az előfizetés implicit módon, a díjak fizetése miatt is megszakítható, miután 30 napig felfüggesztett állapotba került.

A lemondási webhook hívásának fogadásakor a kiadónak legalább hét napig meg kell őriznie a helyreállításhoz szükséges ügyféladatokat. Az ügyféladatokat csak akkor lehet törölni.

Az SaaS-előfizetés bármikor megszakítható az életciklusa bármely pontján. A megszakított előfizetés után nem lehet újraaktiválni az előfizetést.

## <a name="api-reference"></a>API-referencia

Ez a szakasz a SaaS-előfizetés és az Operations API-k dokumentálása.

Az **előfizetési API** -k segítségével kezelheti a SaaS-előfizetési életciklusát a vásárlástól a törlésig.

Az **operatív API-kat** a következőknek kell használni:

* a feldolgozott webhook-hívások ellenőrzése és nyugtázása
* azon alkalmazások listájának beolvasása, amelyeknek a közzétevőnek meg kell várnia a jóváhagyást

### <a name="enforcing-tls-12-note"></a>TLS 1,2-Megjegyzés kényszerítése

A TLS-verzió 1,2-es verziója hamarosan érvénybe lép a HTTPS-kommunikáció minimális verziójaként. Ügyeljen rá, hogy ezt a TLS-verziót használja a kódban.  A TLS 1,0-es és 1,1-es verziója hamarosan elavult lesz.

### <a name="subscription-apis"></a>Előfizetési API-k

#### <a name="resolve-a-purchased-subscription"></a>Megvásárolt előfizetés feloldása

A feloldási végpont lehetővé teszi, hogy a közzétevő kicserélje a Piactéri vásárlási *token* azonosító tokent (a [megvásárolt, de még nem aktivált](#purchased-but-not-yet-activated-pendingfulfillmentstart)) egy állandó megvásárolt SaaS-előfizetési azonosítóra és annak részleteire.

Ha a rendszer átirányítja az ügyfelet a partner kezdőlapjának URL-címére, a rendszer az ügyfél-azonosító jogkivonatot az URL-hívásban *jogkivonat* -paraméterként adja át. A partnernek a tokent kell használnia, és el kell végeznie a feloldását. Az API feloldása válasz tartalmazza a SaaS-előfizetés AZONOSÍTÓját, valamint a vásárlás egyedi azonosításának egyéb részleteit. A Kezdőlap URL-hívásához megadott *jogkivonat* általában 24 órára érvényes. Ha a kapott *jogkivonat* már lejárt, javasoljuk, hogy a következő útmutatást adja meg a végfelhasználóknak:

"Nem sikerült azonosítani ezt a vásárlást, nyissa meg újra ezt az SaaS-előfizetést Azure Portal vagy a M365 felügyeleti központban, és kattintson ismét a" fiók konfigurálása "vagy a" fiók kezelése "gombra.

A feloldási API meghívásakor a rendszer az összes támogatott állapotú SaaS-előfizetésre vonatkozó előfizetési adatokat és állapotot ad vissza.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Közzététel`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.   |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt. A formátum az, `"Bearer <accessaccess_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |
|  `x-ms-marketplace-token`  | A piactér-vásárlási azonosító *jogkivonat* paraméter a feloldáshoz.  A rendszer átadja a tokent a Kezdőlap URL-címének hívásakor, ha az ügyfél át lesz irányítva az SaaS-partner webhelyére (például: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  *Megjegyzés:* A kódolt *jogkivonat* -érték a Kezdőlap URL-címének része, ezért dekódolni kell, mielőtt az API-hívásban paraméterként használni kellene.  <br> <br> Az URL-címben szereplő kódolt karakterlánc például a következő: `contoso.com/signup?token=ab%2Bcd%2Fef` , ahol a jogkivonat `ab%2Bcd%2Fef` .  Ugyanaz a jogkivonat dekódolása a következőket eredményezi:`Ab+cd/ef` |
| | |

*Reagálási kódok:*

Kód: a 200 egyedi SaaS-előfizetési azonosítót ad vissza a megadott érték alapján `x-ms-marketplace-token` .

Példa a válasz törzsére:

```json
{
    "id": "<guid>",  // purchased SaaS subscription ID 
    "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name 
    "offerId": "offer1", // purchased offer ID
    "planId": "silver", // purchased offer's plan ID
    "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
    "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
                   startDate": "2019-05-31", 
   "endDate": "2019-06-29",
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": [
      "Delete",
      "Update",
      "Read"
    ],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Kód: 400 hibás kérelem. `x-ms-marketplace-token`hiányzik, helytelen formátumú, érvénytelen vagy lejárt.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet az engedélyezési jogkivonat létrehozásához használt egy másik Azure AD alkalmazás AZONOSÍTÓval közzétettek.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Előfizetés aktiválása

Miután a SaaS-fiók konfigurálva van egy végfelhasználó számára, a közzétevőnek meg kell hívnia az előfizetés API aktiválása a Microsoft oldalon.  Az ügyfél számlázása csak akkor történik meg, ha ez az API-hívás sikeres.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Közzététel`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 használata.   |
| `subscriptionId` | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az [API feloldása](#resolve-a-purchased-subscription)paranccsal.
 |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a karakterlánc a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`      |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt. A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Adattartalom kérése példa:*

```json
{ // needed for validation of the activation request
    "planId": "gold", // purchased plan, cannot be empty
    "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Reagálási kódok:*

Kód: 200 az előfizetés a Microsoft oldalán előfizetettként van megjelölve.

Ehhez a híváshoz nincs válasz törzs.

Kód: 400 hibás kérelem: az érvényesítés nem sikerült.

* `planId`nem létezik a kérelem adattartalmát.
* `planId`a kérelem adattartalma nem egyezik meg a megvásárolttal.
* `quantity`a kérelem adattartalma nem egyezik meg a megvásárolttal
* Az SaaS-előfizetés előfizetett vagy felfüggesztett állapotban van.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva. A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet az engedélyezési jogkivonat létrehozásához használt egy másik Azure AD alkalmazás AZONOSÍTÓval közzétettek.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404. Az SaaS-előfizetés leiratkozott állapotban van.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Az összes előfizetés listájának beolvasása

Lekéri az összes megvásárolt SaaS-előfizetés listáját a piactéren a közzétevő által közzétett összes ajánlathoz.  A rendszer az összes lehetséges állapotú SaaS-előfizetést adja vissza. A rendszer a leiratkozott SaaS-előfizetéseket is visszaadja, mivel ezek az adatok nem törlődnek a Microsoft-oldalon.

Ez az API többoldalas eredményeket ad vissza. Az oldalméret 100.

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Lekérés`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 használata.  |
| `continuationToken`  | Nem kötelező megadni. Az eredmények első oldalának lekéréséhez hagyja üresen a következőt:.  `@nextLink`A következő lap beolvasásához használja a paraméterben visszaadott értéket. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`      |  Egy egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Reagálási kódok:*

Kód: 200 a közzétevő engedélyezési jogkivonatának alapján a közzétevő összes ajánlatához tartozó összes meglévő előfizetés listáját adja vissza.

*Példa a válasz törzsére:*

```json
{
  "subscriptions": [
      {
          "id": "<guid>", // purchased SaaS subscription ID
          "name": "Contoso Cloud Solution", // SaaS subscription name
          "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
          "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
            "term": { // The period for which the subscription was purchased. 
                "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
                "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
                "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
          },
          "allowedCustomerOperations": [
              "Read", "Update", "Delete" 
          ], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
          "sessionMode": "None", // not relevant
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
      },
// next SaaS subscription details, might be a different offer
{
          "id": "<guid1>", 
          "name": "Contoso Cloud Solution1", 
          "publisherId": "contoso", 
          "offerId": "offer2", 
          "planId": "gold", 
          "quantity": "", 
          "beneficiary": {
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { 
              "emailId": "purchase@csp.com ",
              "objectId": "<guid>",
              "tenantId": "<guid>",
               "pid": "<ID of the user>"
          },
            "term": { 
                "startDate": "2019-05-31",
                "endDate": "2020-04-30",
                "termUnit": "P1Y"
          },
          "allowedCustomerOperations": [
              "Read" 
          ], 
          "sessionMode": "None",
          "isFreeTrial": false,
          "isTest": false,
          "sandboxType": "None",
          "saasSubscriptionStatus": "Suspended"
      }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Ha nem található megvásárolt SaaS-előfizetés ehhez a közzétevőhöz, a rendszer üres választ ad vissza.

Kód: 403 Tiltott. Az engedélyezési jogkivonat nem érhető el, érvénytelen vagy lejárt.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

Kód: 500 belső kiszolgálóhiba. Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Előfizetés lekérése

Lekéri a megadott megvásárolt SaaS-előfizetést a piactéren közzétett SaaS-ajánlathoz. Ezzel a hívással lekérheti az adott SaaS-előfizetéshez tartozó összes elérhető információt az azonosítójával, és nem hívja meg az API-t az összes előfizetés listájának beolvasásához.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   2018-08-31 használata. |
| `subscriptionId`     |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `authorization`     | Egy egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt. A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Code: 200 egy SaaS-előfizetés részleteit adja vissza a `subscriptionId` megadott érték alapján.

*Példa a válasz törzsére:*

```json
{
        "id":<guid>, // purchased SaaS subscription ID
        "name":"Contoso Cloud Solution", // SaaS subscription name
         "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
         "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
              "emailId": "test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
              "emailId": "test@test.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
        "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
        "sessionMode": "None", // not relevant
        "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": " Subscribed " // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
          "term": { // the period for which the subscription was purchased 
            "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
            "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
            "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
        }
}
```

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt, és nincs megadva. A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD alkalmazás AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  A megadott SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Elérhető csomagok listázása

Az ajánlat adott vásárlása által azonosított SaaS-ajánlat összes csomagjának beolvasása `subscriptionId` .  Ezzel a hívással lekérheti az összes olyan magán-és nyilvános csomag listáját, amelyet a SaaS-előfizetés kedvezményezettje frissíthet az előfizetésre.  A visszaadott csomagok ugyanabban a földrajzban lesznek elérhetők, mint a már megvásárolt csomag.

Ez a hívás az adott ügyfél számára elérhető csomagok listáját adja vissza, a már megvásároltak mellett.  A lista a közzétevő webhelyén a végfelhasználók számára is megjeleníthető.  A végfelhasználó módosíthatja az előfizetési tervet a visszaadott listán szereplő csomagok bármelyikére.  A tervnek a listában nem szereplőre való módosítása sikertelen lesz.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`    |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid`  |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `authorization`     |  Egy egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Kód: a 200 egy meglévő SaaS-előfizetés összes elérhető csomagjának listáját adja vissza, beleértve a már megvásároltat is.

Példa a válasz törzsére:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
        "isPrivate": true //true or false
    },
{ 
        "planId": "gold",
        "displayName": "Gold plan for Contoso", 
        "isPrivate": false //true or false
    }
]
}
```

Ha `subscriptionId` nem található, az üres válasz törzsét adja vissza.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  Előfordulhat, hogy a kérelem megpróbál hozzáférni egy SaaS-előfizetéshez egy olyan ajánlathoz, amelyet az engedélyezési jogkivonat létrehozásához használt másik Azure AD alkalmazás AZONOSÍTÓval tesznek közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Az előfizetés tervének módosítása

Az SaaS-előfizetéshez megvásárolt meglévő csomag frissítése egy új csomagra (nyilvános vagy privát).  A közzétevőnek meg kell hívnia ezt az API-t, ha a piactéren megvásárolt SaaS-előfizetésre vonatkozó terv megváltozik a közzétevő oldalán.

Ezt az API-t csak aktív előfizetések esetében lehet meghívni.  Bármely terv módosítható bármely más meglévő csomagra (nyilvános vagy magánjellegű), de nem saját magára.  A privát csomagok esetében az ügyfél bérlőjét a csomag célközönségének részeként kell meghatározni a partner Centerben.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
| `subscriptionId`     | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid`  | Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egy egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Adattartalom kérése példa:*

```json
{
    "planId": "gold" // the ID of the new plan to be purchased
}
```

*Reagálási kódok:*

Kód: 202 a terv módosítására irányuló kérelem aszinkron módon lett elfogadva és kezelve.  A partnernek a változási tervre vonatkozó kérelem sikerességének vagy meghibásodásának megállapításához a **művelet-hely URL-címét** kell lekérdezni.  A lekérdezésnek néhány másodpercen belül meg kell történnie, amíg a művelet sikertelen, sikeres vagy ütközési állapotba nem kerül.  A végső műveleti állapotot gyorsan kell visszaadni, de bizonyos esetekben több percet is igénybe vehet.

A partner emellett webhook-értesítést is kap, ha a művelet készen áll a Piactéri oldal sikeres befejezésére.  És csak ezután a közzétevőnek kell megváltoztatnia a tervet a közzétevő oldalán.

*Válasz fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  A művelet állapotának beolvasására szolgáló URL-cím.  Például: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kód: 400 hibás kérelem: érvényesítési hibák.

* Az új csomag nem létezik, vagy nem érhető el ehhez az adott SaaS-előfizetéshez.
* Kísérlet ugyanarra a csomagra való váltásra.
* Az SaaS-előfizetés állapota nincs előfizetve.
* A SaaS-előfizetés frissítési művelete nem része a következőnek: `allowedCustomerOperations` .

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD alkalmazás AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>A csomag vagy az ülések mennyisége egyszerre is módosítható, nem mindkettő.

>[!Note]
>Ezt az API-t csak azután lehet meghívni, hogy a felhasználó explicit módon jóváhagyta a változást.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Az SaaS-előfizetésben lévő ülőhelyek mennyiségének módosítása

A SaaS-előfizetéshez megvásárolt helyek mennyiségének frissítése (növelése vagy csökkentése).  A közzétevőnek meg kell hívnia ezt az API-t, ha a piactéren létrehozott SaaS-előfizetések száma megváltozik a közzétevő oldaláról.

Az ülőhelyek mennyisége nem lehet nagyobb, mint a jelenlegi tervben engedélyezett mennyiség.  Ebben az esetben a tervet a mennyiség módosítása előtt módosítani kell.

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`     | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid`  | Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     | Egy egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Adattartalom kérése példa:*

```json
{
    "quantity": 5 // the new amount of seats to be purchased
}
```

*Reagálási kódok:*

Kód: 202 a mennyiség módosítására irányuló kérelem aszinkron módon lett elfogadva és kezelve. A partnernek a **művelet – hely URL-címével** kell lekérdezni a módosítási mennyiség kérésének sikerességét vagy sikertelenségét.  A lekérdezésnek néhány másodpercen belül meg kell történnie, amíg a művelet sikertelen, sikeres vagy ütközési állapotba nem kerül.  A végső műveleti állapotot gyorsan kell visszaadni, de bizonyos esetekben több percet is igénybe vehet.

A partner emellett webhook-értesítést is kap, ha a művelet készen áll a Piactéri oldal sikeres befejezésére.  És csak ezután a közzétevőnek kell elvégeznie a mennyiség módosítását a közzétevő oldalán.

*Válasz fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.  Például: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Kód: 400 hibás kérelem: érvényesítési hibák.

* Az új mennyiség nagyobb vagy kisebb, mint a jelenlegi csomag korlátja.
* Az új mennyiség hiányzik.
* Ugyanarra a mennyiségre próbál módosítani.
* Az SaaS-előfizetés állapota nincs előfizetve.
* A SaaS-előfizetés frissítési művelete nem része a következőnek: `allowedCustomerOperations` .

Kód: 403 Tiltott.  Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem olyan előfizetéshez próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Egyszerre csak egy csomag vagy mennyiség módosítható, mindkettő nem lehet egyszerre.

>[!Note]
>Ezt az API-t csak azután lehet meghívni, hogy a felhasználó explicit módon jóváhagyta a változást.

#### <a name="cancel-a-subscription"></a>Előfizetés megszakítása

Megadott SaaS-előfizetés lemondása.  A közzétevőnek nem kell használnia ezt az API-t, és javasoljuk, hogy az ügyfeleket a piactérre irányítsa az SaaS-előfizetések megszakításához.

Ha a közzétevő úgy dönt, hogy végrehajtja a piactéren megvásárolt SaaS-előfizetés lemondását a közzétevő oldalán, meg kell hívnia ezt az API-t.  A hívás befejezése után az előfizetés állapota *leiratkozott* lesz a Microsoft oldalán.

Ha a következő türelmi időszakon belül megszakít egy előfizetést, a rendszer nem számláz:

* 24 óra az aktiválás utáni havi előfizetéshez.
* 14 nap az aktiválás utáni éves előfizetéshez.

Az ügyfél számlázása akkor történik meg, ha a fenti türelmi időszak után megszakítják az előfizetést.  Ha a lemondás sikeres, az ügyfél azonnal elveszíti a Microsoft oldalán lévő SaaS-előfizetéshez való hozzáférést.

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Törlés`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`     | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid`  | Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Reagálási kódok:*

Kód: 202 a leiratkozási kérelem elfogadása és kezelése aszinkron módon megtörtént.  A partnernek meg kell kérdezni a **művelet – hely URL-címét** a kérelem sikerességének vagy meghibásodásának megállapításához.  A lekérdezésnek néhány másodpercen belül meg kell történnie, amíg a művelet sikertelen, sikeres vagy ütközési állapotba nem kerül.  A végső műveleti állapotot gyorsan kell visszaadni, de bizonyos esetekben több percet is igénybe vehet.

A partner akkor is bekerül a webhook-értesítésbe, ha a művelet sikeresen befejeződött a piactér oldalán.  És csak ezután a közzétevőnek kell megszüntetnie az előfizetést a közzétevő oldalán.

*Válasz fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.  Például: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kód: 400 hibás kérelem.  A DELETE nem szerepel a listában ehhez az `allowedCustomerOperations` SaaS-előfizetéshez.

Kód: 403 Tiltott.  Az engedélyezési jogkivonat érvénytelen, lejárt vagy nem érhető el. A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD alkalmazás AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba. Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>Operatív API-k

#### <a name="list-outstanding-operations"></a>Függőben lévő műveletek listázása 

A megadott SaaS-előfizetésre vonatkozó függőben lévő műveletek listájának beolvasása.  A visszaadott műveleteket a közzétevőnek kell elfogadnia a [művelet patch API](#update-the-status-of-an-operation)meghívásával.

A rendszer jelenleg csak az **újravisszaállítási műveleteket** adja vissza válaszként ehhez az API-híváshoz.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  2018-08-31 használata.         |
|    `subscriptionId` | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Kód: a 200 visszaadja a megadott SaaS-előfizetés függőben lévő visszahívási műveletét.

*Válasz hasznos adat például:*

```json
{"operations": [{
    "id": "<guid>",  //Operation ID, should be provided in the operations patch API call
    "activityId": "<guid>", //not relevant
    "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
    "offerId": "offer1",  // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver",  // purchased plan ID
    "quantity": "20", // purchased amount of seats, will be empty is not relevant
    "action": "Reinstate", 
    "timeStamp": "2018-12-01T00:00:00",  // UTC
    "status": "InProgress" // the only status that can be returned in this case
}]
}
```

Üres JSON-értéket ad vissza, ha nincs függőben lévő visszaállítási művelet.

Kód: 400 hibás kérelem: érvényesítési hibák.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD alkalmazás AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba. Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Művelet állapotának beolvasása

Lehetővé teszi a közzétevő számára a megadott aszinkron művelet állapotának nyomon követését: **leiratkozás**, **ChangePlan**vagy **ChangeQuantity**.

Az ehhez az `operationId` API-híváshoz a **művelet – hely**, a függőben lévő Operations API-hívás beolvasása, vagy `<id>` egy webhook-hívásban kapott paraméterérték értéke olvasható be.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`    |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |
|  `operationId`       |  A beolvasott művelet egyedi azonosítója. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Code: 200 – a megadott SaaS-művelet részleteinek beolvasása. 

*Válasz hasznos adat például:*

```json
Response body:
{
    "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
    "activityId": "<guid>", //not relevant
    "subscriptionId":"<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
    "offerId": "offer1", // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver", // purchased plan ID
    "quantity": "20", // purchased amount of seats 
    "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
    "timeStamp": "2018-12-01T00:00:00", // UTC
    "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)

"errorStatusCode": "",
"errorMessage": ""
}
```

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD alkalmazás AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  

* A `subscriptionId` nem található előfizetés.
* A művelet `operationId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Művelet állapotának frissítése

Egy függőben lévő művelet állapotának frissítése, hogy jelezze a művelet sikerességét vagy hibáját a közzétevő oldalán.

Az ehhez az `operationId` API-híváshoz a **művelet – hely**, a függőben lévő Operations API-hívás vagy a `<id>` webhook-hívásban kapott paraméterérték értéke olvasható be.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Javítás`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  2018-08-31 használata.  |
|   `subscriptionId`   |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |
|   `operationId`      |  A befejezett művelet egyedi azonosítója. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|   `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `authorization`     |  Egy egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Adattartalom kérése példa:*

```json
{ 
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Reagálási kódok:*

Kód: 200 egy műveletnek a partner oldalán való betöltésére irányuló hívás.  Ez a válasz például megadhatja az ülések vagy a csomagok megváltoztatásának befejezését a közzétevő oldalán.

Kód: 403 Tiltott.  Az engedélyezési jogkivonat nem érhető el, érvénytelen vagy lejárt. Előfordulhat, hogy a kérelem olyan előfizetéshez próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.
Tilos.  Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD alkalmazás AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404.

* A `subscriptionId` nem található előfizetés.
* A művelet `operationId` nem található.

Kód: 409 ütközés.  Egy újabb frissítés például már teljesül.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Webhook megvalósítása az SaaS szolgáltatásban

Az SaaS-ajánlatok a partner Centerben való létrehozásakor a partner biztosítja a **kapcsolati webhook** URL-címét, amelyet http-végpontként kíván használni.  Ezt a webhookot a Microsoft hívja meg a POST HTTP-hívással, hogy értesítse a közzétevő oldalát a következő eseményekről, amelyek a Microsoft oldalán történnek:

* Ha az SaaS-előfizetés előfizetett állapotban van:
    * ChangePlan 
    * ChangeQuantity
    * Felfüggesztés
    * Leiratkozás
* Ha az SaaS-előfizetés felfüggesztett állapotban van:
    * Visszaállítás
    * Leiratkozás

A közzétevőnek be kell vezetnie egy webhookot az SaaS-szolgáltatásban, hogy az SaaS-előfizetés állapota összhangban legyen a Microsoft oldalával.  Az SaaS szolgáltatásnak meg kell hívnia a Get Operation API-t, hogy érvényesítse és engedélyezze a webhook-hívást és a hasznos adatokat, mielőtt a webhook-értesítésen alapuló műveleteket hozna.  A közzétevőnek a HTTP 200-et a Microsoftnak kell visszaadnia, amint a webhook-hívást feldolgozták.  Ez az érték megerősíti, hogy a közzétevő sikeresen megkapta a webhook hívását.

>[!Note]
>A webhook URL-címének nonstop kell lennie és futnia, és készen kell állnia arra, hogy a Microsoft időről időre új hívásokat kapjon.  A Microsoft újrapróbálkozási szabályzattal rendelkezik a webhook-híváshoz (500 újrapróbálkozás több mint 8 órát), de ha a közzétevő nem fogadja el a hívást, és nem ad vissza választ, a webhook által beküldött művelet végül sikertelen lesz a Microsoft oldalán.

*Webhook hasznos adatokra vonatkozó példák:*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": <guid>, // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success  
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": <guid>, 
  "activityId": <guid>, 
  "subscriptionId": "guid", 
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold", 
  "quantity": " 20", 
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress" 
} 
```

## <a name="development-and-testing"></a>Fejlesztés és tesztelés

A fejlesztési folyamat elindításához javasoljuk, hogy hozzon létre dummy API-válaszokat a közzétevő oldalon.  Ezek a válaszok a jelen dokumentumban megadott válaszok alapján is alapulhatnak.

Ha a közzétevő készen áll a végpontok közötti tesztelésre: 

* Egy SaaS-ajánlat közzététele egy korlátozott előzetes verzióban, és megtarthatja az előzetes verziót.
* Ebben az ajánlatban 0 árat kell tervezni, így nem kell a tényleges számlázási költségeket kiváltani a tesztelés során.  Egy másik lehetőség, hogy nem nulla értékű árat állít be, és 24 órán belül megszakítja az összes teszt beszerzését. 
* Győződjön meg arról, hogy az összes folyamat véget ért a végpontig, ugyanúgy, mint amikor az ügyfél megvásárolja az ajánlatot. 
* Ha a partner a teljes vásárlási és számlázási folyamatot szeretné tesztelni, ezt a $0-nál magasabb áron kínáljuk.  A vásárlás után a számlázás után létrejön egy számla.

A vásárlás folyamata az ajánlat közzétételének helyétől függően Azure Portal vagy Microsoft AppSource helyekről indítható el.

A *terv módosítása*, a *mennyiség módosítása*és a *leiratkozási* műveletek tesztelése a közzétevő oldaláról történik.  A Microsoft-oldalról a *leiratkozás* a Azure Portal és a felügyeleti központban is elindítható (a portálon, ahol a Microsoft AppSource megvásárlások vannak kezelve).  A *módosítási mennyiség és a terv* csak a felügyeleti központban indítható el.

## <a name="get-support"></a>Támogatás kérése

A kiadói támogatási lehetőségekért lásd: [a kereskedelmi piactér program támogatása a partner Centerben](support.md) .


## <a name="next-steps"></a>További lépések

A piactéren elérhető SaaS-ajánlatok további lehetőségeiért lásd: Marketplace- [mérési szolgáltatás API](marketplace-metering-service-apis.md) -k.

Tekintse át és használja a jelen dokumentumban ismertetett API-kra épülő [SaaS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) -t.
