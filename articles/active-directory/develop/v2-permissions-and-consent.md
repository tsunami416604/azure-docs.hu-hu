---
title: A Microsoft identitásplatform hatókörei, engedélyei és hozzájárulása
description: A Microsoft identity platform végpontján az engedélyezés leírása, beleértve a hatóköröket, az engedélyeket és a hozzájárulást.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: 55055f65e1b725e079b60e960837e05558ef08d6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886211"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Engedélyek és hozzájárulás a Microsoft identity platform végpontjában

A Microsoft identity platformmal integrálható alkalmazások olyan engedélyezési modellt követnek, amely lehetővé teszi a felhasználók és a rendszergazdák számára az adatok elérésének szabályozását. Az engedélyezési modell megvalósítása frissült a Microsoft identity platform végpontján, és megváltoztatja, hogy az alkalmazásnak hogyan kell együttműködnie a Microsoft-identitásplatformmal. Ez a cikk ismerteti az alapvető fogalmak az engedélyezési modell, beleértve a hatókörök, engedélyek és a hozzájárulás.

> [!NOTE]
> A Microsoft identity platform végpontja nem támogatja az összes forgatókönyvet és szolgáltatást. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md)

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek

A Microsoft identity platform megvalósítja az [OAuth 2.0](active-directory-v2-protocols.md) engedélyezési protokollt. Az OAuth 2.0 egy olyan módszer, amelyen keresztül egy harmadik féltől származó alkalmazás hozzáférhet a felhasználó nevében a webáltal üzemeltetett erőforrásokhoz. Minden olyan webes üzemeltetésű erőforrás, amely integrálható a Microsoft identitásplatformjával, rendelkezik erőforrás-azonosítóval vagy *alkalmazásazonosító URI-val.* A Microsoft néhány webes üzemeltetésű forrása például a következők:

* Microsoft Graph:`https://graph.microsoft.com`
* Office 365 Mail API:`https://outlook.office.com`
* Azure Key Vault:`https://vault.azure.net`

> [!NOTE]
> Javasoljuk, hogy az Office 365 Mail API helyett a Microsoft Graph-ot használja, stb.

Ugyanez vonatkozik minden olyan külső gyártótól származó erőforrásra, amely integrálva van a Microsoft identitásplatformmal. Ezen erőforrások bármelyike is definiálhat olyan engedélyeket, amelyek az erőforrás funkcióit kisebb adattömbökre oszthatják fel. A Microsoft [Graph](https://graph.microsoft.com) például többek között a következő feladatok elvégzésére vonatkozó engedélyekkel rendelkezik:

* Felhasználó naptárának olvasása
* Írás a felhasználó naptárába
* E-mail küldése felhasználóként

Az ilyen típusú engedélyek meghatározásával az erőforrás részletesen szabályozhatja az adatokat, és hogyan érhető el az API-funkciók. Egy harmadik féltől származó alkalmazás kérheti ezeket az engedélyeket a felhasználóktól és a rendszergazdáktól, akiknek jóvá kell hagyniuk a kérelmet, mielőtt az alkalmazás hozzáférhetne az adatokhoz, vagy a felhasználó nevében cselekedhetne. Az erőforrás funkcióinak kisebb engedélykészletekbe való feldarabolásával a harmadik féltől származó alkalmazások úgy alakíthatók ki, hogy csak a funkciójuk végrehajtásához szükséges engedélyeket kérjék. A felhasználók és a rendszergazdák pontosan tudják, hogy az alkalmazás milyen adatokhoz fér hozzá, és biztosak lehetnek abban, hogy nem rosszindulatú szándékkal viselkedik. A fejlesztőknek mindig be kell tartaniuk a legkevésbé kiváltságos fogalmát, és csak azokat az engedélyeket kell kérniük, amelyek az alkalmazások működéséhez szükségesek.

Az OAuth 2.0-s körzetben az ilyen típusú engedélyeket *hatóköröknek nevezzük.* Ezeket gyakran engedélynek is *nevezik.* Az engedély karakterlánc-értékként jelenik meg a Microsoft identitásplatformján. A Microsoft Graph példával folytatva az egyes engedélyek karakterláncértéke a következő:

* Felhasználó naptárának olvasása a következő vel:`Calendars.Read`
* Írás a felhasználó naptárába a`Calendars.ReadWrite`
* E-mail küldése felhasználóként a`Mail.Send`

Egy alkalmazás leggyakrabban kéri ezeket az engedélyeket a Microsoft identity platform authorize point-i kérelmek hatóköreinek megadásával. Bizonyos magas jogosultsági engedélyek azonban csak rendszergazdai hozzájárulással és a [rendszergazda ido-hozzájárulási végpontjának](v2-permissions-and-consent.md#admin-restricted-permissions)használatával adhatók meg. Olvasson tovább, hogy többet.

## <a name="permission-types"></a>Engedélytípusok

A Microsoft identity platform kétféle engedélyt támogat: **delegált engedélyeket** és **alkalmazásengedélyeket.**

* **A delegált engedélyeket** olyan alkalmazások használják, amelyeken bejelentkezési felhasználó van. Ezeknél az alkalmazásokban a felhasználó vagy a rendszergazda hozzájárul az alkalmazás által kért engedélyekhez, és az alkalmazás felhatalmazást kap arra, hogy bejelentkezett felhasználóként működjön a célerőforrás hívásakor. Egyes delegált engedélyekhez nem rendszergazda felhasználók is beleegyezhetnek, de néhány magasabb jogosultsági szintű [engedélyhez rendszergazdai hozzájárulás](v2-permissions-and-consent.md#admin-restricted-permissions)szükséges. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök járulhatnak hozzá a delegált engedélyekhez, olvassa el [a Rendszergazdai szerepkör-engedélyek az Azure AD-ben című témakört.](../users-groups-roles/directory-assign-admin-roles.md)

* **Az alkalmazásengedélyeket** olyan alkalmazások használják, amelyek bejelentkezési felhasználó nélkül futnak; például olyan alkalmazások, amelyek háttérszolgáltatásként vagy démonként futnak.  Az alkalmazásengedélyeket csak [rendszergazda hagyhatja jóvá.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

_A hatályos engedélyek_ azok az engedélyek, amelyekkel az alkalmazás rendelkezik a célerőforrásra irányuló kérelmek kérésekor. Fontos megérteni a különbséget a delegált és az alkalmazás által megadott alkalmazásengedélyek és a tényleges engedélyek között, amikor a célerőforrást hívja.

- Delegált engedélyek esetén az alkalmazás _tényleges engedélyei_ az alkalmazás által megadott delegált engedélyek (hozzájárulás sal) és a jelenleg bejelentkezett felhasználó jogosultságainak legkevésbé kiemelt metszéspontjai lesznek. Az alkalmazásnak soha nem lehet több jogosultsága, mint a bejelentkezett felhasználónak. A cégeken belül a bejelentkezett felhasználó jogosultságait szabályzat vagy egy vagy több rendszergazdai szerepkör tagsága határozhatja meg. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök járulhatnak hozzá a delegált engedélyekhez, olvassa el [a Rendszergazdai szerepkör-engedélyek az Azure AD-ben című témakört.](../users-groups-roles/directory-assign-admin-roles.md)

   Tegyük fel például, hogy az alkalmazás megkapta a _User.ReadWrite.All_ delegált engedélyt. Ez az engedély névlegesen ad engedélyt az alkalmazás számára egy cégben lévő összes felhasználó profiljának olvasásához és frissítéséhez. Ha a bejelentkezett felhasználó egy globális rendszergazda, az alkalmazás frissíteni tudja a cégben lévő összes felhasználó profilját. Ha azonban a bejelentkezett felhasználó nem rendszergazdai szerepkörben van, az alkalmazás csak a bejelentkezett felhasználó profilját tudja frissíteni. Nem tudja frissíteni a cégben lévő többi felhasználó profilját, mert az a felhasználó nem rendelkezik ilyen jogosultságokkal, akinek a nevében eljár.
  
- Az alkalmazásengedélyek esetében az alkalmazás _hatályos engedélyei_ az engedélyáltal függő jogosultságok teljes szintje lesz. Például egy alkalmazás, amely rendelkezik a _User.ReadWrite.All_ alkalmazás engedély frissítheti a profilt minden felhasználó a szervezetben. 

## <a name="openid-connect-scopes"></a>OpenID Connect hatókörök

Az OpenID Connect Microsoft identitásplatform-megvalósítása néhány jól definiált hatókörrel rendelkezik, `profile`amelyek `offline_access`nem vonatkoznak egy adott erőforrásra: `openid`, `email`, és . Az `address` `phone` OpenID Connect hatókörök nem támogatottak.

### <a name="openid"></a>Openid

Ha egy alkalmazás az [OpenID Connect](active-directory-v2-protocols.md)használatával hajt végre `openid` bejelentkezést, meg kell kérnie a hatókört. A `openid` hatókör a munkahelyi fiók hozzájárulási lapján "Bejelentkezés" engedélyként, a személyes Microsoft-fiók hozzájárulási lapján pedig "A profil megtekintése és az alkalmazásokhoz és szolgáltatásokhoz a Microsoft-fiók használatával való csatlakozás" engedélyként jelenik meg. Ezzel az engedéllyel az alkalmazás egyedi azonosítót kaphat a `sub` felhasználó számára a jogcím formájában. Emellett hozzáférést biztosít az alkalmazásnak a UserInfo végponthoz. A `openid` hatókör a Microsoft identity platform tokenvégpontján használható azonosítójog-tokenek beszerzéséhez, amelyeket az alkalmazás hitelesítésre használhat.

### <a name="email"></a>e-mail

A `email` hatókör használható a `openid` hatókörrel és minden más hatókörrel. Hozzáférést biztosít az alkalmazásnak a felhasználó elsődleges e-mail `email` címéhez a jogcím formájában. A `email` jogcím csak akkor szerepel a jogkivonatban, ha egy e-mail cím van társítva a felhasználói fiókhoz, ami nem mindig van így. Ha a `email` hatókört használja, az alkalmazásnak készen kell `email` állnia egy olyan eset kezelésére, amelyben a jogcím nem létezik a jogkivonatban.

### <a name="profile"></a>profil

A `profile` hatókör használható a `openid` hatókörrel és minden más hatókörrel. Ez ad az alkalmazás hozzáférést jelentős mennyiségű információt a felhasználóról. Az általa elérhető információk közé tartozik, de nem kizárólagosan, a felhasználó utónevét, vezetéknevét, elsődleges felhasználónevét és objektumazonosítóját. Az adott felhasználó id_tokens paraméterében elérhető profiljogcímek teljes listáját a [ `id_tokens` hivatkozásban található.](id-tokens.md)

### <a name="offline_access"></a>offline_access

A [ `offline_access` hatókör](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) hosszabb ideig biztosít hozzáférést az alkalmazás nak a felhasználó nevében lévő erőforrásokhoz. A jóváhagyási lapon ez a hatókör a "Hozzáférés fenntartása az adatokhoz, amelyekhez hozzáférést adott" engedélyként jelenik meg. Amikor egy felhasználó `offline_access` jóváhagyja a hatókört, az alkalmazás a Microsoft identity platform tokenvégpontjából kaphat frissítési jogkivonatokat. A frissítési tokenek hosszú élettartamúak. Az alkalmazás új hozzáférési jogkivonatokat kaphat, ahogy a régebbiek lejárnak.

> [!NOTE]
> Ez az engedély ma minden hozzájárulási képernyőn megjelenik, még olyan folyamatok esetében is, amelyek nem biztosítanak frissítési jogkivonatot (az [implicit folyamatot).](v2-oauth2-implicit-grant-flow.md)  Ez olyan forgatókönyveket fed le, amelyekben az ügyfél az implicit folyamaton belül kezdődhet, majd átléphet a kódfolyamatra, ahol frissítési jogkivonat várható.

A Microsoft identity platformon (a v2.0-végpontra irányuló kérelmeken) az alkalmazásnak explicit módon kérnie kell a `offline_access` hatókört a frissítési jogkivonatok fogadásához. Ez azt jelenti, hogy amikor bevált egy engedélyezési kódot az [OAuth 2.0](active-directory-v2-protocols.md)engedélyezési `/token` kód folyamatában, csak egy hozzáférési jogkivonatot kap a végpontról. A hozzáférési jogkivonat rövid ideig érvényes. A hozzáférési jogkivonat általában egy óra múlva lejár. Ezen a ponton az alkalmazásnak vissza kell `/authorize` irányítania a felhasználót a végpontra, hogy új engedélyezési kódot kapjon. Az átirányítás során az alkalmazás típusától függően előfordulhat, hogy a felhasználónak újra meg kell adnia a hitelesítő adatait, vagy újra hozzá kell járulnia az engedélyekhez. 

A frissítési jogkivonatok beszerzéséről és használatáról a [Microsoft identity platform protokollhivatkozása tartalmaz további információt.](active-directory-v2-protocols.md)

## <a name="requesting-individual-user-consent"></a>Egyéni felhasználó beleegyezésének kérése

[OpenID Connect vagy OAuth 2.0](active-directory-v2-protocols.md) engedélyezési kérelemben az alkalmazások a `scope` lekérdezési paraméter használatával kérhetik a szükséges engedélyeket. Ha például egy felhasználó bejelentkezik egy alkalmazásba, az alkalmazás a következő példához hasonló kérést küld (az olvashatóság érdekében hozzáadott sortörésekkel):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

A `scope` paraméter az alkalmazás által kért delegált engedélyek területválasztó listája. Minden engedélyt úgy jeleznek, hogy az engedélyértéket hozzáfűzik az erőforrás azonosítójához (az alkalmazásazonosító URI-jához). A kérelem példában az alkalmazásnak engedélyre van szüksége a felhasználó naptárának olvasásához és a levelezés felhasználóként való elküldéséhez.

Miután a felhasználó megadta a hitelesítő adatait, a Microsoft identity platform végpontja megkeresi a *felhasználói hozzájárulás*megfelelő rekordját. Ha a felhasználó a múltban nem járult hozzá a kért engedélyekhez, és a rendszergazda sem járult hozzá ezekhez az engedélyekhez a teljes szervezet nevében, a Microsoft identity platform végpontja a felhasználót a kért engedélyek megadására kéri.

> [!NOTE]
>Jelenleg a ("Hozzáférés fenntartása az `offline_access` adatokhoz, amelyekhez hozzáférést `user.read` adott neki") és ("Bejelentkezés és a profil olvasása") engedélyek automatikusan bekerülnek az alkalmazás kezdeti hozzájárulása.  Ezek az engedélyek általában szükségesek a `offline_access` megfelelő alkalmazásfunkcióhoz – hozzáférést biztosít az alkalmazásnak `user.read` a natív és webalkalmazások számára kritikus frissítési jogkivonatokhoz, miközben hozzáférést biztosít a `sub` jogcímhez, lehetővé téve az ügyfél vagy az alkalmazás számára, hogy megfelelően azonosítsa a felhasználót az idő múlásával, és hozzáférjen a kezdetleges felhasználói adatokhoz.  

![Példa képernyőképre, amelyen a munkahelyi fiók hozzájárulása látható](./media/v2-permissions-and-consent/work_account_consent.png)

Amikor a felhasználó jóváhagyja az engedélykérelmet, a rendszer rögzíti a hozzájárulást, és a felhasználónak nem kell újra jóváhagynia az alkalmazásba való későbbi bejelentkezéseket.

## <a name="requesting-consent-for-an-entire-tenant"></a>Hozzájárulás kérése egy teljes bérlőhöz

Gyakran előfordul, hogy ha egy szervezet megvásárol egy licencet vagy előfizetést egy alkalmazáshoz, a szervezet proaktív módon be kívánja állítani az alkalmazást a szervezet összes tagja általi használatra. Ennek a folyamatnak a részeként a rendszergazda beleegyezését adhat az alkalmazás nevében eljárni bármely felhasználó a bérlőben. Ha a rendszergazda megadja a teljes bérlő hozzájárulását, a szervezet felhasználói nem fognak látni egy jóváhagyási lapot az alkalmazáshoz.

Ha a bérlő összes felhasználójának delegált engedélyekhez szeretne hozzájárulást kérni, az alkalmazás használhatja a rendszergazdai hozzájárulási végpontot.

Emellett az alkalmazásoknak a rendszergazdai hozzájárulási végpontot kell használniuk az alkalmazásengedélyek kéréséhez.

## <a name="admin-restricted-permissions"></a>Rendszergazdai korlátozások

A Microsoft ökoszisztémájában bizonyos magas jogosultsági engedélyek *rendszergazdai korlátozásokra*állíthatók. Ilyen típusú engedélyek például a következők:

* Az összes felhasználó teljes profiljának olvasása a`User.Read.All`
* Adatok írása a szervezet címtárába a`Directory.ReadWrite.All`
* A szervezet címtárában lévő összes csoport olvasása a`Groups.Read.All`

Bár a fogyasztói felhasználó hozzáférést adhat az alkalmazásnak az ilyen típusú adatokhoz, a szervezeti felhasználók nem adhatnak hozzáférést ugyanazokhoz a bizalmas vállalati adatokhoz. Ha az alkalmazás hozzáférést kér egy szervezeti felhasználótól az engedélyek egyikéhez, a felhasználó hibaüzenetet kap, amely szerint nincs engedélye az alkalmazás engedélyeinek hozzájárulására.

Ha az alkalmazás nak hozzáférésre van szüksége a szervezetek korlátozott rendszergazdai hatóköreihez, kérje őket közvetlenül a vállalati rendszergazdától, a következőként ismertetett rendszergazdai hozzájárulási végpont használatával is.

Ha az alkalmazás magas jogosultságú delegált engedélyeket kér, és a rendszergazda adja ezeket az engedélyeket a rendszergazdai hozzájárulási végponton keresztül, a jóváhagyás a bérlő összes felhasználója számára biztosított.

Ha az alkalmazás alkalmazásengedélyeket kér, és a rendszergazda adja ezeket az engedélyeket a rendszergazdai hozzájárulási végponton keresztül, ez a támogatás nem történik meg egy adott felhasználó nevében. Ehelyett az ügyfélalkalmazás *közvetlenengedélyeket*kap. Az ilyen típusú engedélyeket csak démonszolgáltatások és más, a háttérben futó nem interaktív alkalmazások használják.

## <a name="using-the-admin-consent-endpoint"></a>A rendszergazdai hozzájárulási végpont használata

> [!NOTE] 
> Kérjük, vegye figyelembe, miután rendszergazdai hozzájárulás a rendszergazdai hozzájárulás végpont használatával, befejezte a rendszergazdai hozzájárulás megadása, és a felhasználók nem kell további műveleteket végrehajtani. A rendszergazdai jóváhagyás megadása után a felhasználók egy tipikus hitelesítési folyamaton keresztül kaphatnak hozzáférési jogkivonatot, és az eredményül kapott hozzáférési jogkivonat rendelkezik a jóváhagyott engedélyekkel. 

Amikor egy vállalati rendszergazda használja az alkalmazást, és az engedélyezési végpontra irányítja, a Microsoft identity platform észleli a felhasználó szerepkörét, és megkérdezi, hogy a teljes bérlő nevében szeretnének-e hozzájárulni a kért engedélyekhez. Azonban van egy dedikált rendszergazdai hozzájárulási végpont is, amelyet akkor használhat, ha proaktív módon szeretné kérni, hogy a rendszergazda adja meg az engedélyt a teljes bérlő nevében. Ennek a végpontnak a használata is szükséges az alkalmazásengedélyek igényléséhez (amely nem kérhető az engedélyezett végpont használatával).

Ha követi ezeket a lépéseket, az alkalmazás engedélyt kérhet a bérlő összes felhasználója számára, beleértve a rendszergazda által korlátozott hatóköröket is. Ez egy magas jogosultsági szintű művelet, és csak akkor kell elvégezni, ha szükséges a forgatókönyvhöz.

A lépéseket megvalósító kódminta megtekintéséhez tekintse meg a [korlátozott rendszergazdai hatókörök mintáját.](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Engedélyek kérése az alkalmazásregisztrációs portálon

Az alkalmazások feljegyezhetők, hogy milyen engedélyekre van szükségük (delegáltésre és alkalmazásra egyaránt) az alkalmazásregisztrációs portálon.  Ez lehetővé `/.default` teszi a hatókör és az Azure Portal "Rendszergazdai hozzájárulás megadása" beállítás használatát.  Általában ajánlott biztosítani, hogy az adott alkalmazáshoz statikusan meghatározott engedélyek a dinamikusan/növekményesen kért engedélyek gyűjtőkészletét képezik.

> [!NOTE]
>Alkalmazásengedélyek csak a használata révén kérhető [`/.default`](#the-default-scope) – így ha az alkalmazásnak alkalmazásengedélyekre van szüksége, győződjön meg arról, hogy azok szerepelnek az alkalmazásregisztrációs portálon.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Alkalmazás statikusan kért engedélyeinek beállítása

1. Nyissa meg az alkalmazást az [Azure Portalon – Alkalmazásregisztrációk,](https://go.microsoft.com/fwlink/?linkid=2083908) vagy [hozzon létre egy alkalmazást,](quickstart-register-app.md) ha még nem tette meg.
2. Keresse meg az **API-engedélyek szakaszt,** és az API-engedélyeken belül kattintson az Engedély hozzáadása gombra.
3. Válassza a **Microsoft Graph** elemet az elérhető API-k listájából, majd adja hozzá az alkalmazás által igényelt engedélyeket.
3. **Mentse** az alkalmazás regisztrációját.

### <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: Írja alá a felhasználót az alkalmazásba

Általában, ha olyan alkalmazást hoz létre, amely a rendszergazdai hozzájárulási végpontot használja, az alkalmazásnak szüksége van egy lapra vagy nézetre, amelyben a rendszergazda jóváhagyhatja az alkalmazás engedélyeit. Ez az oldal része lehet az alkalmazás regisztrációs folyamatának, az alkalmazás beállításainak része, vagy lehet egy dedikált "connect" folyamat. Sok esetben célszerű, hogy az alkalmazás csak akkor jelenítse meg ezt a "connect" nézetet, ha a felhasználó bejelentkezett egy munkahelyi vagy iskolai Microsoft-fiókkal.

Amikor bejelentkezik a felhasználóaz alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a rendszergazda tartozik, mielőtt megkérné őket a szükséges engedélyek jóváhagyására. Bár nem feltétlenül szükséges, segíthet egy intuitívabb felhasználói élmény létrehozásában. A felhasználó bejelentkezéséhez kövesse a [Microsoft identity platform protokoll oktatóanyagait.](active-directory-v2-protocols.md)

### <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése címtáradminisztrátortól

Ha készen áll arra, hogy engedélyeket kérjen a szervezet rendszergazdájától, átirányíthatja a felhasználót a Microsoft identity platform *rendszergazdájának hozzájárulási végpontjára.*

```
// Line breaks are for legibility only.
  GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
  &scope=
  https://graph.microsoft.com/calendars.read 
  https://graph.microsoft.com/mail.send
```


| Paraméter        | Állapot        | Leírás                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Kötelező | Az a címtár-bérlő, amelytől engedélyt szeretne kérni. Guid vagy rövid névformátumban vagy általánosan hivatkozott szervezetekkel a példában látható módon adható meg. Ne használja a "közös", a személyes fiókok nem adhat rendszergazdai jóváhagyást, kivéve a bérlő ikörnyezetében. A bérlőket kezelő személyes fiókokkal való legjobb kompatibilitás biztosítása érdekében, ha lehetséges, használja a bérlőazonosítót. |
| `client_id` | Kötelező | Az **Alkalmazás (ügyfél) azonosító,** amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt élmény. |
| `redirect_uri` | Kötelező |Az átirányítási URI, ahol azt szeretné, hogy a választ kell küldeni az alkalmazás kezelni. Pontosan meg kell egyeznie az alkalmazásregisztrációs portálon regisztrált átirányítási URI-k egyikével. |
| `state` | Ajánlott | A kérelemben szereplő érték, amely a jogkivonat-válaszban is vissza kerül. Ez lehet egy sor bármilyen tartalmat akarsz. Az állapot segítségével kódolhat információkat a felhasználó állapotáról az alkalmazásban a hitelesítési kérelem bekövetkezése előtt, például azt az oldalt vagy nézetet, amelyen voltak. |
|`scope`        | Kötelező        | Az alkalmazás által kért engedélyek készletét határozza meg. Ez lehet statikus (használatával) [`/.default`](#the-default-scope)vagy dinamikus hatókörök.  Ez magában foglalhatja az`openid`OIDC hatóköröket ( , `profile`, `email`. ). Ha alkalmazásengedélyekre van szüksége, az engedélyek statikusan konfigurált listájának igényléséhez kell használnia. `/.default`  | 


Ezen a ponton az Azure AD megköveteli a bérlői rendszergazda a kérelem teljesítéséhez. A rendszergazdának jóvá kell hagynia a `scope` paraméterben kért összes engedélyt.  Ha statikus (`/.default`) értéket használt, az a v1.0-s rendszergazdai hozzájárulási végponthoz hasonlóan fog működni, és az alkalmazáshoz szükséges engedélyekben található összes hatókörhöz beleegyezést kér.

#### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | Az a címtár-bérlő, amely megadta az alkalmazásnak a kért engedélyeket GUID formátumban. |
| `state` | A kérelemben szereplő érték, amely a jogkivonat-válaszban is visszakerül. Ez lehet egy sor bármilyen tartalmat akarsz. Az állapot a felhasználó állapotára vonatkozó információk kódolására szolgál az alkalmazásban a hitelesítési kérelem bekövetkezése előtt, például az oldal vagy a nézet, amelyen voltak. |
| `admin_consent` | Lesz beállítva, hogy `True`. |

#### <a name="error-response"></a>Hibaválasz

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőképpen néz ki:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hiba kiváltó okának azonosításában. |

Miután sikeres választ kapott a rendszergazdai hozzájárulási végponttól, az alkalmazás megszerezte a kért engedélyeket. Ezután kérheti a kívánt erőforrás jogkivonatát.

## <a name="using-permissions"></a>Engedélyek használata

Miután a felhasználó beleegyezik az alkalmazás engedélyeihez, az alkalmazás olyan hozzáférési jogkivonatokat szerezhet be, amelyek az alkalmazás bizonyos kapacitású erőforrás-hozzáférési engedélyét képviselik. Egy hozzáférési jogkivonat csak egyetlen erőforráshoz használható, de a hozzáférési jogkivonaton belül kódolt minden engedély, amely az alkalmazás megkapta az adott erőforráshoz. Hozzáférési jogkivonat beszerzéséhez az alkalmazás a microsoftidentity platform token végpontjára kérhet, például:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Az eredményül kapott hozzáférési jogkivonatot http-kérelmekben használhatja az erőforráshoz. Megbízhatóan jelzi az erőforrásnak, hogy az alkalmazás rendelkezik a megfelelő engedéllyel egy adott feladat végrehajtásához. 

Az OAuth 2.0 protokollról és a hozzáférési jogkivonatok beszerzéséről a [Microsoft identity platform végpontprotokoll-hivatkozási útmutatójában talál](active-directory-v2-protocols.md)további információt.

## <a name="the-default-scope"></a>A /.default hatókör

A `/.default` hatókör segítségével az alkalmazások áttelepítése a v1.0 végpontról a Microsoft identity platform végpontra. Ez egy beépített hatókör minden alkalmazáshoz, amely az alkalmazásregisztrációban konfigurált engedélyek statikus listájára hivatkozik. Az `scope` `https://graph.microsoft.com/.default` érték funkcionálisan megegyezik a v1.0-végpontok `resource=https://graph.microsoft.com` – nevezetesen, kér egy jogkivonatot a microsoft graph hatókörökkel, amelyek az alkalmazás regisztrált az Azure Portalon.  Az erőforrás URI + `/.default` használatával épül fel (például ha `https://contosoApp.com`az erőforrás URI-ja , akkor a kért hatókör lesz). `https://contosoApp.com/.default`  Tekintse meg a szakaszt a [záró perjelek](#trailing-slash-and-default) esetekben, ahol meg kell adnia egy második perjel a jogkivonat helyes kérése.

Az /.default hatókör bármely OAuth 2.0-s folyamatban használható, de szükséges a folyamat [és](v2-oauth2-on-behalf-of-flow.md) az [ügyfél hitelesítő adatai folyamatában,](v2-oauth2-client-creds-grant-flow.md)valamint a v2 rendszergazdai hozzájárulási végpont használatakor alkalmazásengedélyek kéréséhez.  

> [!NOTE]
> Az ügyfelek nem kombinálhatják a statikus (`/.default`) és a dinamikus beleegyezést egyetlen kérelemben. Így `scope=https://graph.microsoft.com/.default+mail.read` a hatókörtípusok kombinációja hibát eredményez.

### <a name="default-and-consent"></a>/.default és hozzájárulás

A `/.default` hatókör elindítja a v1.0 `prompt=consent` végpont viselkedését is. Az alkalmazás által regisztrált összes engedélyhez jóváhagyást kér, függetlenül az erőforrástól. Ha szerepel a kérelem részeként, a `/.default` hatókör egy jogkivonatot ad vissza, amely tartalmazza a kért erőforrás hatókörét.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, ha a felhasználó már hozzájárult

Mivel `/.default` funkcionálisan megegyezik `resource`a -centrikus v1.0-végpont viselkedésével, a v1.0-végpont hozzájárulási viselkedését is magával hozza. Az azin kívül csak akkor indít el hozzájárulási kérdést, `/.default` ha a felhasználó nem adott engedélyt az ügyfél és az erőforrás között. Ha létezik ilyen hozzájárulás, akkor egy jogkivonatot ad vissza, amely tartalmazza a felhasználó által az erőforráshoz megadott összes hatókört. Ha azonban nem adtak meg engedélyt, vagy a `prompt=consent` paraméter meg lett adva, az ügyfélalkalmazás által regisztrált összes hatókörhöz megjelenik egy jóváhagyási parancs.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>1. példa: A felhasználó vagy a bérlő rendszergazdája engedélyeket kapott

Ebben a példában a felhasználó (vagy a bérlői rendszergazda) megadta az ügyfélnek a Microsoft Graph engedélyeket `mail.read` és `user.read`. Ha az ügyfél a `scope=https://graph.microsoft.com/.default`programra vonatkozó kérelmet küld, akkor a Microsoft Graph számára regisztrált ügyfélalkalmazások tartalmától függetlenül nem jelenik meg a hozzájárulási kérdés. A rendszer egy tokent ad vissza, amely tartalmazza a hatóköröket `mail.read` és `user.read`a .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>2. példa: A felhasználó nem adott engedélyt az ügyfél és az erőforrás között

Ebben a példában nincs hozzájárulás a felhasználó számára az ügyfél és a Microsoft Graph között. Az ügyfél regisztrált `user.read` a `contacts.read` és az engedélyek, valamint `https://vault.azure.net/user_impersonation`az Azure Key Vault hatóköre. Amikor `scope=https://graph.microsoft.com/.default`az ügyfél jogkivonatot kér, a felhasználó `user.read`a `contacts.read`, és `user_impersonation` a Key Vault-hatókörök jóváhagyási képernyőjét fogja látni. A visszaadott jogkivonat `user.read` `contacts.read` csak a és a hatókörök, és csak akkor használható a Microsoft Graph ellen.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>3. példa: A felhasználó hozzájárult, és az ügyfél további hatóköröket kér

Ebben a példában a felhasználó `mail.read` már hozzájárult az ügyfél számára. Az ügyfél regisztrált `contacts.read` a hatóköra a regisztrációban. Amikor az ügyfél egy jogkivonat `scope=https://graph.microsoft.com/.default` iránti `prompt=consent`kérelmet nyújt be, és a jóváhagyáskérést keresztül kéri, akkor a felhasználó egy jóváhagyási képernyőt fog látni az alkalmazás által regisztrált összes (és csak) engedélyhez. `contacts.read`jelen lesz a hozzájárulási `mail.read` képernyőn, de nem fog. A visszaadott token a Microsoft `mail.read` Graph-hoz lesz, és tartalmazza a és `contacts.read`a .

### <a name="using-the-default-scope-with-the-client"></a>A /.default hatókör használata az ügyféllel

A hatókör egy `/.default` különleges esete akkor áll `/.default` fenn, ha az ügyfél saját hatókört kér. A következő példa bemutatja ezt a forgatókönyvet.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Ez létrehoz egy jóváhagyási képernyőt az összes regisztrált engedélyhez (ha alkalmazható a fenti hozzájárulási leírások alapján, és `/.default`), majd egy id_token ad vissza, nem pedig hozzáférési jogkivonatot.  Ez a viselkedés az ADAL-ról az MSAL-ra költöző egyes örökölt ügyfelek esetében létezik, és a Microsoft identitásplatform-végpontot célzó új ügyfelek **nem** használhatják őket.  

### <a name="trailing-slash-and-default"></a>Záró perjel és /.default

Egyes erőforrás-URI-k egy`https://contoso.com/` záró perjellel rendelkeznek (szemben ), `https://contoso.com`ami problémákat okozhat a jogkivonat-érvényesítéssel.  Ez elsősorban akkor fordulhat elő, ha`https://management.azure.com/`egy jogkivonatot kér az Azure Resource Management ( ), amely egy záró perjel az erőforrás URI-n, és megköveteli, hogy jelen legyen, amikor a jogkivonat ot kéri.  Így, ha kér egy `https://management.azure.com/` token `/.default`és használata `https://management.azure.com//.default` , meg kell kérni - vegye figyelembe a kettős perjel! 

Általában - ha már érvényesítette, hogy a jogkivonat kiadása folyamatban van, és a jogkivonatot az API, amely elfogadja azt, fontolja meg egy második perjel hozzáadása, és próbálja újra. Ez azért történik, mert a bejelentkezési kiszolgáló jogkivonatot `scope` bocsát ki `/.default` a paraméterben lévő URI-knak megfelelő közönséggel - a végén eltávolítva.  Ha ez eltávolítja a követő perjelet, a bejelentkezési kiszolgáló továbbra is feldolgozza a kérelmet, és érvényesíti azt az erőforrás URI-val szemben, annak ellenére, hogy már nem egyezik - ez nem szabványos, és az alkalmazás nem támaszkodhat.  

## <a name="troubleshooting-permissions-and-consent"></a>Engedélyek és beleegyezések elhárítása

Ha Ön vagy az alkalmazás felhasználói váratlan hibákat látnak a jóváhagyási folyamat során, olvassa el ezt a cikket a hibaelhárítási lépések: [Váratlan hiba az alkalmazáshoz való hozzájárulás végrehajtásakor.](../manage-apps/application-sign-in-unexpected-user-consent-error.md)
