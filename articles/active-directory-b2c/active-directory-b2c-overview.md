<properties
    pageTitle="Azure Active Directory B2C előzetes verziója: Áttekintés | Microsoft Azure"
    description="A felhasználók felé néző alkalmazások fejlesztése az Azure Active Directory B2C-vel"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="06/06/2016"
    ms.author="swkrish"/>

# Az Azure Active Directory B2C előzetes verziója: felhasználók regisztrálása és beléptetése az alkalmazásokba 

Az Azure Active Directory B2C egy kiterjedt funkciókészlettel ellátott felhőbeli identitáskezelő megoldás a felhasználók felé néző webes és mobilalkalmazásokhoz. Ez a globális szolgáltatás magas rendelkezésre állást biztosít, és akár több száz millió felhasználói identitásra is felskálázható. A vállalati szintű biztonsági platformra épülő Azure Active Directory B2C védelmet nyújt alkalmazásainak, vállalatának és ügyfeleinek.

A múltban gyakori volt, hogy a felhasználók alkalmazásokra való regisztrációját és bejelentkezését megvalósítani kívánó alkalmazásfejlesztők maguk írták meg az ehhez szükséges kódot. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure Active Directory B2C biztonságos, szabványokon alapuló platformja és bővíthető szabályzatainak széles választéka révén fejlettebb módszert kínál a felhasználói identitáskezelés alkalmazásokba való integrálására. Az Azure Active Directory B2C használata lehetővé teszi, hogy a felhasználók meglévő közösségi hálózati fiókjukkal (Facebook, Google, Amazon, LinkedIn) is regisztrálhassanak az alkalmazásokra, de új fiókot is létrehozhatnak egy e-mail-cím vagy felhasználónév és egy jelszó kiválasztásával (ezeket „helyi fiókoknak” nevezzük).

Az Azure Active Directory B2C jelenleg előzetes verzióban érhető el. Ebben az időszakban különösen kíváncsiak vagyunk véleményére és tapasztalataira. A visszajelzések alapján akár jelentős változásokat is életbe léptethetünk, ha azok javítják a szolgáltatás minőségét.  Javasoljuk, hogy ebben az időszakban éles alkalmazásokban még ne használja az előzetes verziót. Ossza meg velünk gondolatait a [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/) segítségével.

## Első lépések

A felhasználói regisztrációt és bejelentkezést elfogadó alkalmazás létrehozásához először regisztrálnia kell az alkalmazást egy Azure Active Directory B2C-bérlővel. Hozzon létre egy saját bérlőt az [Azure AD B2C-bérlő létrehozása](active-directory-b2c-get-started.md) című részben ismertetett lépések segítségével.

Alkalmazását kétféle módon kötheti össze az Azure Active Directory B2C-vel: küldhet közvetlen protokollüzeneteket az [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) vagy az [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow) segítségével, vagy használhatja kódtárainkat, amelyek elvégzik Ön helyett ezt a munkát. Válassza ki előnyben részesített platformját az alábbi táblázatból, és ismerkedjen meg a részletekkel.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Újdonságok

Keresse fel rendszeresen ezt a lapot, hiszen itt szerezhet tudomást az Azure Active Directory B2C előzetes verzióját érintő változásokról. A frissítésekről az @AzureAD néven Twitter-üzeneteket is küldünk.

- Megismerheti a [bővíthető szabályzat-keretrendszert](active-directory-b2c-reference-policies.md), valamint az alkalmazásokban létrehozható és használható szabályzattípusokat.
- Tájékozódhat az előzetes verzió aktuális [korlátozásairól](active-directory-b2c-limitations.md).

## Útmutatók

Az útmutatókból megtanulhatja az Azure Active Directory B2C előzetes verzió funkcióinak használatát:

- [Facebook-](active-directory-b2c-setup-fb-app.md), [Google+-](active-directory-b2c-setup-goog-app.md), [Microsoft-](active-directory-b2c-setup-msa-app.md), [Amazon-](active-directory-b2c-setup-amzn-app.md) és [LinkedIn-](active-directory-b2c-setup-li-app.md)fiókok beállítása a felhasználók felé néző alkalmazásokban való használatra
- [Egyéni attribútumok használata a felhasználókról való adatgyűjtéshez](active-directory-b2c-reference-custom-attr.md)
- [Az Azure Multi-Factor Authentication alkalmazása a felhasználók felé néző alkalmazásokban](active-directory-b2c-reference-mfa.md)
- [Önkiszolgáló jelszóátállítás a felhasználók számára](active-directory-b2c-reference-sspr.md)
- Az Azure Active Directory B2C által üzemeltetett [felhasználók felé néző regisztrációs, bejelentkezési és más lapok megjelenésnek és használatának testreszabása](active-directory-b2c-reference-ui-customization.md)
- [Felhasználók programokon keresztüli létrehozása, beolvasása, frissítése és törlése az Azure Active Directory Graph API segítségével](active-directory-b2c-devquickstarts-graph-dotnet.md) az Azure Active Directory B2C-bérlőben

## Következő lépések

Az alábbi hivatkozások segítségével mélyebben is megismerheti a szolgáltatást:

- Tekintse meg az[Active Directory B2C díjszabási információit](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Keressen hasznos információkat a Stack Overflow oldalon az [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) vagy az [adal](http://stackoverflow.com/questions/tagged/adal) címkék segítségével.
- Mondja el nekünk véleményét az előzetes verzióról a [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/) segítségével – mindenre kíváncsiak vagyunk! A poszt címe tartalmazza az „AzureADB2C:” kifejezést, így könnyebben megtaláljuk.
- Az Azure Active Directory B2C az „App Model v2.0” nevű alkalmazásregisztrációs modell segítségével támogatja az ipari szabványnak számító protokollokat (az OpenID Connectet és az OAuth 2.0-t). 
  - [Az App Model v2.0 protokoll referenciái](active-directory-b2c-reference-protocols.md)
  - [Az App Model v2.0 jogkivonat referenciái](active-directory-b2c-reference-tokens.md)
- Olvassa el az [Azure Active Directory B2C-vel kapcsolatos gyakori kérdéseket](active-directory-b2c-faqs.md).
- [Az Azure Active Directory B2C-vel kapcsolatos fájltámogatási kérések](active-directory-b2c-support.md).

## Biztonsági frissítések termékeinkhez

Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.



<!--HONumber=Jun16_HO2-->


