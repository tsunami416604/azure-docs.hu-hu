---
title: "Azure Active Directory B2C: Alkalmazás regisztrációja | Microsoft Docs"
description: "Hogyan regisztrálhatja alkalmazását Azure Active Directory B2C-vel?"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: hu-hu
ms.lasthandoff: 07/04/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Az alkalmazás regisztrációja

> [!IMPORTANT]
> Az Azure Portal Azure AD B2C paneljén létrehozott alkalmazásokat ugyanarról a helyről kell kezelnie. Ha a B2C-alkalmazásokat a PowerShell vagy valamely másik portál használatával szerkeszti, a rendszer nem támogatja többé az alkalmazásokat, és nem működnek majd az Azure AD B2C-vel. További információ [alább](#faulted-apps) olvasható.
>

## <a name="prerequisite"></a>Előfeltétel

A felhasználói regisztrációt és bejelentkezést elfogadó alkalmazás létrehozásához először regisztrálnia kell az alkalmazást egy Azure Active Directory B2C-bérlővel. Hozzon létre egy saját bérlőt az [Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md) részben ismertetett lépések segítségével. Miután elvégezte ezeket a lépéseket, a B2C funkciók panelje rögzítésre kerül a kezdőpulton.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Lépjen a B2C funkciók paneljére

Ha a B2C funkciók panelje rögzítésre kerül a kezdőpulton, azonnal látni fogja a panelt, amint B2C-bérlő globális rendszergazdájaként bejelentkezik az [Azure Portalra](https://portal.azure.com/).

A panelre úgy is beléphet, hogy a **További szolgáltatások** elemre kattint, majd megkeresi az **Azure AD B2C** elemet az [Azure Portal](https://portal.azure.com/) bal oldali navigációs ablaktábláján.

> [!IMPORTANT]
> A B2C bérlő globális rendszergazdájának kell lennie ahhoz, hogy hozzáférhessen a B2C funkciók panelhez. Más bérlők globális rendszergazdái vagy felhasználói nem férhetnek hozzá a panelhez.  A B2C-bérlőre az Azure Portal jobb felső sarkában található bérlőváltó használatával válthat át.
>
>

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

1. Kattintson az Azure Portal B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
1. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
1. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
1. Állítsa az **Include web app / web API** (Webappal/webes API-val együtt) kapcsolót **Yes** (Igen) állásba.
1. A **válasz URL-címek** esetében adjon meg [megfelelő](#limitations) értéket. Ezek a címek olyan végpontok, ahol az Azure AD B2C visszalép az alkalmazás által kért jogkivonatokhoz. Adja meg például a következőt: `https://localhost:44316/`.
1. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
1. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.
1. Ha a webalkalmazás az Azure AD B2C által biztonságossá tett webes API-kat is meghív majd, ne feledkezzen meg a következőkről:
    1. **Application Secret** (Alkalmazástitok) létrehozásához lépjen a **Keys** (Kulcsok) panelre, majd kattintson a **Generate Key** (Kulcs létrehozása) gombra.
    1. Kattintson az **API Access** (API-hozzáférés), majd az **Add** (Hozzáadás) elemre, és válassza ki a webes API-t és a hatóköröket (engedélyeket).

> [!NOTE]
> Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
>

## <a name="register-a-web-api"></a>Webes API regisztrálása

1. Kattintson az Azure Portal B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
1. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
1. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
1. Állítsa az **Include web app / web API** (Webappal/webes API-val együtt) kapcsolót **Yes** (Igen) állásba.
1. A **válasz URL-címek** esetében adjon meg [megfelelő](#choosing-a-web-app/api-reply-url) értéket. Ezek a címek olyan végpontok, ahol az Azure AD B2C visszalép az alkalmazás által kért jogkivonatokhoz. Adja meg például a következőt: `https://localhost:44316/`.
1. Adjon meg egy **Alkalmazásazonosító URI-t**. Ez a webes API-hoz használt azonosító. Írja be például a következőt: „megjegyzések”. Ez alatta létrehozza a teljes azonosító URI-t.
1. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
1. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.
1. Kattintson a **Published scopes** (Közzétett hatókörök) elemre. Itt határozza meg a más alkalmazásoknak megadható engedélyeket (hatóköröket).
1. Szükség szerint több hatókört is felvehet. Alapértelmezés szerint a „user_impersonation” hatókör van meghatározva. Ez lehetővé teszi az egyéb alkalmazások számára, hogy a bejelentkezett felhasználó nevében érjék el az API-t. Ha szeretné, ezt eltávolíthatja.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="register-a-mobilenative-application"></a>Mobil-/natív alkalmazás regisztrálása

1. Kattintson az Azure Portal B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
1. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
1. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
1. Állítsa az **Include native client** (Natív ügyféllel együtt) kapcsolót **Yes** (Igen) állásba.
1. Adjon meg egy **Átirányítási URI-t** egy egyéni sémával. Például: com.onmicrosoft.contoso.appnév://átirányítási/útvonal. Ügyeljen arra, hogy [megfelelő átirányítási URI-t](#choosing-a-native-application-redirect-uri) válasszon, és különleges karakterek, például aláhúzásjelek ne szerepeljenek benne.
1. Kattintson a **Save** (Mentés) gombra az alkalmazás regisztrálásához.
1. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.
1. Ha natív alkalmazása az Azure AD B2C által biztonságossá tett webes API-kat is meghív majd, ne feledkezzen meg a következőkről:
    1. **Application Secret** (Alkalmazástitok) létrehozásához lépjen a **Keys** (Kulcsok) panelre, majd kattintson a **Generate Key** (Kulcs létrehozása) gombra.
    1. Kattintson az **API Access** (API-hozzáférés), majd az **Add** (Hozzáadás) elemre, és válassza ki a webes API-t és a hatóköröket (engedélyeket).

> [!NOTE]
> Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
>

## <a name="limitations"></a>Korlátozások

### <a name="choosing-a-web-appapi-reply-url"></a>Webalkalmazás/api válasz URL-címének kiválasztása

Az Azure AD B2C-vel regisztrált alkalmazások jelenleg csak meghatározott válasz URL-címértékekre korlátozódnak. A webalkalmazások és -szolgáltatások válasz URL-címének a `https`-sémával kell kezdődnie, és valamennyi válasz URL-címértéknek egyetlen DNS-tartományba kell tartoznia. Például nem regisztrálhat olyan webalkalmazást, amely az alábbi URL-címek egyikével rendelkezik:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

A regisztrációs rendszer összehasonlítja a meglévő válasz URL-cím teljes DNS-nevét a hozzáadni kívánt válasz URL-cím DNS-nevével. A DNS-név hozzáadására irányuló kérelem sikertelen lesz, ha a következő feltételek egyikének értéke igaz:

* Az új válasz URL-cím teljes DNS-neve nem egyezik meg a meglévő válasz URL-cím DNS-nevével.
* Az új válasz URL-cím teljes DNS-neve nem tartozik a meglévő válasz URL-cím altartományába.

Ha például az alkalmazás válasz URL-címe a következő:

`https://login.contoso.com`

A következő módon adhatja hozzá:

`https://login.contoso.com/new`

Ebben az esetben a DNS-név pontosan egyezik. Esetleg a következőt teheti meg:

`https://new.login.contoso.com`

Ebben az esetben a login.contoso.com egyik DNS-altartományára hivatkozik. Ha azt szeretné, hogy az alkalmazás a login-east.contoso.com vagy a login-west.contoso.com válasz URL-címmel rendelkezzen, ezeket a válasz URL-címeket az alábbi sorrendben kell hozzáadnia:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Hozzáadhatja az utóbbi kettőt, mivel az első válasz URL-cím, a contoso.com altartományába tartoznak.

### <a name="choosing-a-native-application-redirect-uri"></a>Natív alkalmazás átirányítási URI-jának kiválasztása

A mobil-/natív alkalmazások átirányítási URI-jának kiválasztásakor két fontos szempontot kell megfontolnia:

* **Egyedi**: Az átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példánkban (com.onmicrosoft.contoso.appname://redirect/path) a com.onmicrosoft.contoso.appnév a séma. Javasoljuk, hogy ezt a mintát kövesse. Ha két alkalmazás ugyanazt a sémát használja, akkor a felhasználó egy „Alkalmazás kiválasztása” párbeszédpanelt fog látni. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul.
* **Teljes**: Az átirányítási URI-nak sémával és elérési útvonallal kell rendelkeznie. Az útvonalnak a tartomány után legalább egy perjelet kell tartalmaznia (például a //contoso/ helyes, de a //contoso nem).

Ügyeljen rá, hogy az átirányítási uri ne tartalmazzon különleges karaktereket, például aláhúzásjeleket.

### <a name="faulted-apps"></a>Hibás alkalmazások

A B2C-alkalmazások NEM szerkeszthetők:

* Egyéb alkalmazáskezelési portálokon, például a [klasszikus Azure portálon](https://manage.windowsazure.com/) vagy az [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/).
* Graph API vagy PowerShell használata

Ha a fent leírt módon szerkeszti a B2C-alkalmazást, és az Azure Portalon lévő Azure AD B2C funkciók paneljén próbálja ismét szerkeszteni, hibás alkalmazás lesz belőle, és az alkalmazást a továbbiakban nem lehet az Azure AD B2C-vel használni. Ekkor törölnie kell majd az alkalmazást, és újra létre kell hoznia.

Az alkalmazás törléséhez lépjen az [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/), ahol törölheti az alkalmazást. Ahhoz, hogy láthatóvá lehessen tenni az alkalmazást, Önnek kell lennie az alkalmazás tulajdonosának (és nem csak a bérlő rendszergazdájának).

## <a name="next-steps"></a>Következő lépések

Miután az Azure AD B2C-vel regisztrált egy alkalmazást, [gyors üzembe helyezési oktatóanyagunk](active-directory-b2c-overview.md#get-started) segítségével elsajátíthatja a használatát.

