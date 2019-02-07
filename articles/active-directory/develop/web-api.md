---
title: Webes API-alkalmazások az Azure Active Directoryban
description: Mik azok a webes API-alkalmazások és az alapokat ismerteti protokoll flow, a regisztráció és a jogkivonat lejárati az alkalmazástípushoz.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 58cff9be154e693a378f55941e8662563c366b27
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820215"
---
# <a name="web-api"></a>Webes API

Webes API-alkalmazások olyan webes alkalmazások, a webes API-k erőforrások le kell töltenie. Ebben a forgatókönyvben két identitás típusa van, a webes alkalmazás hitelesítéséhez és a webes API hívása segítségével:

- **Identita aplikace** – ebben a példában az OAuth 2.0 ügyfélhitelesítő adatok hitelesítse magát az alkalmazást, és a webes API-k eléréséhez. A webes API-t csak azt észleli, hogy a webalkalmazás, hívja az Alkalmazásidentitás használatakor, a webes API-t nem kapja meg a felhasználó semmilyen információt. Az alkalmazás fogad a felhasználó adatait, ha az alkalmazás protokollon keresztül küld, és nem írta alá a Azure ad-ben. A webes API megbízik, hogy a webalkalmazás hitelesítette a felhasználót. Ezért ez a minta egy megbízható alrendszer neve.
- **Felhasználói identitás delegált** – ebben a forgatókönyvben két módon is elvégezhető: OpenID Connect, és az OAuth 2.0 hitelesítési kódmegadás bizalmas-ügyféllel. A webalkalmazás lekéri a hozzáférési jogkivonatot a felhasználóhoz, amely igazolja, a webes API-t, hogy a webalkalmazás sikeresen hitelesíteni a felhasználót és a webes alkalmazás volt a webes API meghívásához egy meghatalmazott felhasználói identitás szerezhetik be. A hozzáférési jogkivonatot a webes API-t, amely engedélyezi a felhasználó, és adja vissza a kívánt erőforrást. a kérelem küldése.

Az alkalmazás azonosítóját és a delegált felhasználói identitás típusai a folyamat az alábbi tárgyalja. A fő különbség az, hogy a delegált felhasználó identitását először kell szerezni az engedélyezési kódot, mielőtt a felhasználó bejelentkezhet és a webes API-k eléréséhez.

## <a name="diagram"></a>Ábra

![Webalkalmazás, webes API-diagram](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokoll folyamat

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace az OAuth 2.0 ügyfél-hitelesítő adatok megadása

1. A webalkalmazás az Azure AD a bejelentkezett felhasználó (lásd a **Web apps** további információ a következő szakaszban).
1. A webalkalmazás kell, hogy a webes API-t hitelesítésre és a kívánt erőforrást beolvasni a hozzáférési jogkivonat beszerzése. Adatbeolvasási kérelmet küld az Azure AD jogkivonat végpontra, a hitelesítő adatokat, a Alkalmazásazonosító és a webes API-k alkalmazás Alkalmazásazonosító URI-t biztosít.
1. Az Azure AD hitelesíti magát az alkalmazást, és a webes API meghívásához használt JWT hozzáférési jogkivonatot ad vissza.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-openid-connect"></a>A meghatalmazott felhasználó identitását az OpenID Connect

1. A felhasználó van bejelentkezve az Azure AD-vel webalkalmazásnak (lásd a webböngésző a webalkalmazás című fenti szakaszban). A webes alkalmazás a felhasználó még nem járult hozzá, így a webalkalmazás a webes API meghívásához nyújtsanak a nevében, ha a felhasználó jóváhagyást kell. Az alkalmazás megjeleníti a szükséges engedélyekkel, és ha ezek egyikét sem rendszergazdai engedélyekkel, nem lesz képes, hogy engedélyt adjanak a normál felhasználót a címtárban. A jóváhagyási folyamat csak több-bérlős alkalmazások, nem egybérlős alkalmazások arra vonatkozik, ahogy az alkalmazás már rendelkezik a szükséges engedélyekkel. Amikor a felhasználó bejelentkezett, a webes alkalmazás kapott egy azonosító jogkivonat a felhasználó, valamint egy hozzáférési kóddal kapcsolatos információkat.
1. Használja az Azure AD által kiállított hozzáférési kód, a webes alkalmazás egy kérést küld az Azure AD jogkivonat-végpont, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrást (Alkalmazásazonosító URI a webes API-t).
1. Az engedélyezési kódot, valamint a webalkalmazás és webes API Azure ad-ben érvényesítette. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT-jogkivonatokkal és a egy JWT frissítési jogkivonatot.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>A meghatalmazott felhasználó identitását az OAuth 2.0 hitelesítési kódmegadás

1. A felhasználó már bejelentkezett egy webalkalmazás, amelynek hitelesítési mechanizmust független az Azure ad-ben.
1. Az alkalmazás egy hozzáférési jogkivonatot beszerezni, így a problémák az egy kérelmet a böngészőben az Azure AD engedélyezési végpont, így az alkalmazás azonosítója, és átirányítási URI a webes alkalmazás sikeres hitelesítés után az engedélyezési kódot igényel. A felhasználó bejelentkezik az Azure ad-hez.
1. A webes alkalmazás a felhasználó még nem járult hozzá, így a webalkalmazás a webes API meghívásához nyújtsanak a nevében, ha a felhasználó jóváhagyást kell. Az alkalmazás megjeleníti a szükséges engedélyekkel, és ha ezek egyikét sem rendszergazdai engedélyekkel, nem lesz képes, hogy engedélyt adjanak a normál felhasználót a címtárban. A jóváhagyás egyetlen és több-bérlős alkalmazás vonatkozik. Egyetlen bérlő esetben egy rendszergazda hajthat végre a rendszergazdai jóváhagyás való hozzájárulás azok a felhasználók nevében. Ehhez használja a `Grant Permissions` gombra a [az Azure portal](https://portal.azure.com). 
1. Miután a felhasználó hozzájárult, a webes alkalmazás kap az engedélyezési kódot, amely a szükséges hozzáférési jogkivonat beszerzése.
1. Használja az Azure AD által kiállított hozzáférési kód, a webes alkalmazás egy kérést küld az Azure AD jogkivonat-végpont, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrást (Alkalmazásazonosító URI a webes API-t).
1. Az engedélyezési kódot, valamint a webalkalmazás és webes API Azure ad-ben érvényesítette. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT-jogkivonatokkal és a egy JWT frissítési jogkivonatot.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

## <a name="code-samples"></a>Kódminták

Tekintse meg a Kódminták webalkalmazás, webes API-forgatókönyvekhez. És térjen vissza később gyakran – új mintát gyakran kerülnek. Webes [webes API alkalmazást](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Appok regisztrálása

Alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont, lásd: [alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont](quickstart-v1-add-azure-ad-app.md).

* Egyetlen bérlő – az alkalmazás azonosítóját és delegált felhasználói identitás esetek, a webalkalmazás és a webes API regisztrálni kell ugyanabban a címtárban az Azure ad-ben. A webes API elérhetővé engedélykészletet, a webes alkalmazás férjenek hozzá az erőforrásokhoz használt konfigurálható. A delegált felhasználói identitástípus használja, ha a webalkalmazás kell válassza ki a kívánt engedélyeket, a **egyéb alkalmazások engedélyei** legördülő menüből az Azure Portalon. Ebben a lépésben nincs szükség, ha az identitás alkalmazástípus használatban van.
* Több-bérlős – először a webalkalmazás jelzi a megfelelő működéséhez szükséges engedélyekkel van konfigurálva. Szükséges engedélyek listája egy párbeszédpanel jelenik meg, amikor egy felhasználó vagy rendszergazda a célkönyvtárban duplikátum beleegyezésével az alkalmazáshoz, ami lehetővé teszi a szervezet számára elérhető. Egyes alkalmazások csak a felhasználói szintű engedélyeket, amelyeket a szervezet bármely felhasználója jóváhagyhat van szükségük. Más alkalmazások szükséges rendszergazdai engedélyekkel, amelyek a szervezet egy felhasználója nem járulhatnak hozzá. Csak egy könyvtár rendszergazda engedélyezheti, hogy ez a jogosultsági szint szükséges alkalmazásokat. Járul hozzá a felhasználó vagy rendszergazda, ha a webalkalmazás és a webes API egyaránt regisztrálva vannak a címtárban.

## <a name="token-expiration"></a>Jogkivonat lejáratáról

Amikor a webalkalmazás eléréséhez a JWT jogkivonat a hozzáférési kód használ, a JWT-frissítési jogkivonatok is fogad. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat segítségével hitelesítse magát újra a felhasználó anélkül, hogy jelentkezzen be újra őket. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonatot és a frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>További lépések

- További információk egyéb [alkalmazástípusok és forgatókönyvek](app-types.md)
- További tudnivalók az Azure AD [hitelesítés alapjai](authentication-scenarios.md)
