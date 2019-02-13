---
title: Az Azure Active Directory natív alkalmazások
description: Protokoll flow, a regisztráció és a jogkivonat lejárati az alkalmazástípushoz a natív alkalmazások vannak és alapjainak ismertetése
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c15890a5693235e8f2554ba8d0fdefc161770f3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165008"
---
# <a name="native-apps"></a>A natív alkalmazások

A natív alkalmazások olyan alkalmazások, amelyek egy felhasználó nevében webes API-hívás. Ebben a forgatókönyvben épül az OAuth 2.0 engedélyezési kód engedélyezési típusának nyilvános ügyfél, 4.1 szakaszában leírtak szerint a [OAuth 2.0 ismertetőjének](https://tools.ietf.org/html/rfc6749). A natív alkalmazás az OAuth 2.0 protokoll használatával szerzi be a felhasználó hozzáférési jogkivonatot. Ez a jogkivonat a kérelemben a webes API-t, amely engedélyezi a felhasználó, és adja vissza a kívánt erőforrást elküldi.

## <a name="diagram"></a>Ábra

![Natív alkalmazás webes API-Diagram](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokoll folyamat

Az AD hitelesítési Kódtárai használja, ha a protokoll részletei az alábbiakban a legtöbb kezelik, például a böngészőben előugró ablak, a token-gyorsítótárazási és a frissítési biztonsági jogkivonat kezelését.

1. Egy böngészővel előugró ablakban a natív alkalmazás kérést küld az engedélyezési végpont az Azure ad-ben. A kérelem magában foglalja az Alkalmazásazonosítót és az átirányítási URI-ját a natív alkalmazás, ahogyan az az Azure Portalon, és az alkalmazás Alkalmazásazonosító URI-ja a webes API-hoz. Ha a felhasználó még nem tette meg, jelentkezzen be újra a figyelmeztetést kapnak
1. Az Azure AD akkor hitelesíti a felhasználót. Ha egy több-bérlős alkalmazásban, és az hozzájárulásra szükség az alkalmazás, a felhasználó hozzájárul, ha azok még nem tette volna meg kell adni. Engedély megadása után, és a sikeres hitelesítést követően az Azure AD kibocsát egy engedélyezési kód választ az ügyfélalkalmazásnak átirányítási URI-t.
1. Az Azure AD kibocsát egy engedélyezési kód választ az átirányítási URI-t, ha az ügyfélalkalmazás leállítja a böngésző interakció, és a hozzáférési kód kigyűjti a választ. Az engedélyezési kód használatával, az ügyfélalkalmazás kérelmet küld, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrásra vonatkozó részletek az Azure AD-jogkivonat végpontra (alkalmazás Alkalmazásazonosító URI-ja számára a webes API-t).
1. Az Azure AD érvényesíti az engedélyezési kódot, valamint az ügyfél és a webes API-val kapcsolatos információkat. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT-jogkivonatokkal és a egy JWT frissítési jogkivonatot. Emellett adja vissza az Azure AD az alapvető információkat szeretne a felhasználó, például a megjelenített nevét és a bérlő azonosítóját.
1. HTTPS-en keresztüli az ügyfélalkalmazás a kapott JWT jogkivonat a kérelem az engedélyezési fejléc a JWT-karakterlánc a "Tulajdonos" megnevezéssel hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.
1. Ha a hozzáférési jogkivonat lejár, az ügyfélalkalmazás fog egy hibaüzenetet, amely jelzi, hogy a felhasználónak újra hitelesítenie kell. Ha az alkalmazás egy érvényes frissítési jogkivonatot, akkor egy új hozzáférési jogkivonat beszerzéséhez jelentkezzen be újra, a felhasználó értesítése nélkül használható. Ha a frissítési jogkivonat lejár, az alkalmazás kell ismét interaktív módon a felhasználó hitelesítéséhez.

> [!NOTE]
> A frissítési jogkivonatot az Azure AD által kiadott több erőforrások eléréséhez használható. Például ha egy ügyfélalkalmazás, amely jogosult arra, hogy két webes API-k hívása, a frissítési jogkivonatot az beolvasni a hozzáférési jogkivonatot a más webes API-hoz is használható.

## <a name="code-samples"></a>Kódminták

Tekintse meg a Kódminták natív alkalmazás webes API-forgatókönyvekhez. És térjen vissza később gyakran – gyakran hozzáadjuk az új mintát. [Webes API a natív alkalmazás](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Appok regisztrálása

Alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont, lásd: [alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont](quickstart-v1-add-azure-ad-app.md).

* Egyetlen bérlő – mind a natív alkalmazás és a webes API-t kell regisztrálni az ugyanabban a címtárban az Azure ad-ben. A webes API elérhetővé engedélykészletet, a natív alkalmazás férjenek hozzá az erőforrásokhoz használt konfigurálható. Az ügyfélalkalmazás ezután kiválasztja a kívánt engedélyekkel a "Engedélyeket az egyéb alkalmazások" legördülő menüből az Azure Portalon.
* Több-bérlős – először a natív alkalmazás csak a fejlesztői vagy regisztrált kiadó könyvtár. A második a natív alkalmazás jelzi a megfelelő működéséhez szükséges engedélyekkel van konfigurálva. Szükséges engedélyek listája egy párbeszédpanel jelenik meg, amikor egy felhasználó vagy rendszergazda a célkönyvtárban duplikátum beleegyezésével az alkalmazáshoz, ami lehetővé teszi a szervezet számára elérhető. Egyes alkalmazások csak a felhasználói szintű engedélyeket, amelyeket a szervezet bármely felhasználója jóváhagyhat van szükségük. Más alkalmazások szükséges rendszergazdai engedélyekkel, amelyek a szervezet egy felhasználója nem járulhatnak hozzá. Csak egy könyvtár rendszergazda engedélyezheti, hogy ez a jogosultsági szint szükséges alkalmazásokat. A felhasználó vagy rendszergazda hozzájárul, csak a webes API regisztrálva lesz a címtárban. 

## <a name="token-expiration"></a>Jogkivonat lejáratáról

A natív alkalmazás eléréséhez a JWT jogkivonat a hozzáférési kód használja, amikor a JWT-frissítési jogkivonatok is fogad. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat használható újra hitelesíteni kell a felhasználó anélkül, hogy jelentkezzen be újra őket. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonatot és a frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>További lépések

- További információk egyéb [alkalmazástípusok és forgatókönyvek](app-types.md)
- További tudnivalók az Azure AD [hitelesítés alapjai](authentication-scenarios.md)
