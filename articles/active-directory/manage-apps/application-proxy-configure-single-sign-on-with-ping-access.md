---
title: Fejlécalapú hitelesítés a PingAccess for Azure AD alkalmazásproxyval | Microsoft dokumentumok
description: A fejlécalapú hitelesítés támogatásához pingaccess-alapú és alkalmazásproxyval rendelkező alkalmazások közzététele.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367976"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Fejlécalapú hitelesítés egyszeri bejelentkezéshez alkalmazásproxyval és PingAccess-el

Az Azure Active Directory (Azure AD) alkalmazásproxy javallotta a PingAccess-t, így az Azure AD-ügyfelek több alkalmazáshoz is hozzáférhetnek. A PingAccess kibővíti a [meglévő alkalmazásproxy-ajánlatokat,](application-proxy.md) hogy egyszeri bejelentkezési hozzáférést biztosítson a hitelesítéshez fejléceket használó alkalmazásokhoz.

## <a name="whats-pingaccess-for-azure-ad"></a>Mi az az Azure AD PingAccess?

A PingAccess for Azure AD segítségével hozzáférést és egyszeri bejelentkezést (SSO) biztosíthat a felhasználóknak a hitelesítéshez fejléceket használó alkalmazásokhoz. Az alkalmazásproxy ezeket az alkalmazásokat ugyanúgy kezeli, mint bármely más alkalmazást, az Azure AD használatával hitelesíti a hozzáférést, majd továbbítja a forgalmat az összekötő szolgáltatáson keresztül. A PingAccess az alkalmazások előtt helyezkedik el, és az Azure AD-ből származó hozzáférési jogkivonatot fejlécé alakítja át. Az alkalmazás ezután megkapja a hitelesítést az olvasható formátumban.

A felhasználók nem vesznek észre semmi mást, amikor bejelentkeznek a vállalati alkalmazások használatához. Továbbra is működhetnek bárhonnan bármilyen eszközön. Az alkalmazásproxy-összekötők a hitelesítési típustól függetlenül irányítják a távoli forgalmat az összes alkalmazáshoz, így továbbra is automatikusan kiegyensúlyozzák a terheléseket.

## <a name="how-do-i-get-access"></a>Hogyan juthatok hozzá?

Mivel ez a forgatókönyv az Azure Active Directory és a PingAccess közötti partnerségből származik, mindkét szolgáltatáshoz licencre van szüksége. Az Azure Active Directory Premium-előfizetések azonban tartalmaznak egy alapvető PingAccess-licencet, amely legfeljebb 20 alkalmazást fed le. Ha 20-nál több fejlécalapú alkalmazást kell közzétennie, további licencet vásárolhat a PingAccess-től.

További információ: [Azure Active Directory-kiadások.](../fundamentals/active-directory-whatis.md)

## <a name="publish-your-application-in-azure"></a>Az alkalmazás közzététele az Azure-ban

Ez a cikk a személyek számára, hogy közzé egy alkalmazást ebben a forgatókönyvben az első alkalommal. A közzétételi lépések részletezése mellett végigvezeti Önt az alkalmazásproxy és a PingAccess első lépéseihez. Ha már konfigurálta mindkét szolgáltatást, de szeretne egy frissítőt a közzétételi lépésekről, ugorjon az [Alkalmazás hozzáadása az Azure AD-hez alkalmazásproxyval](#add-your-application-to-azure-ad-with-application-proxy) szakaszra.

> [!NOTE]
> Mivel ez a forgatókönyv az Azure AD és a PingAccess közötti partnerség, néhány utasítás létezik a Ping Identity webhelyen.

### <a name="install-an-application-proxy-connector"></a>Alkalmazásproxy-összekötő telepítése

Ha engedélyezte az alkalmazásproxyt, és már telepített egy összekötőt, kihagyhatja ezt a szakaszt, és az [Alkalmazás hozzáadása az Azure AD-hez alkalmazásproxyval](#add-your-application-to-azure-ad-with-application-proxy)című szakaszban látható.

Az alkalmazásproxy-összekötő egy Windows Server-szolgáltatás, amely a távoli alkalmazottaktól a közzétett alkalmazásokhoz irányítja a forgalmat. További részletes telepítési utasításokat az [Oktatóanyag: Egy helyszíni alkalmazás hozzáadása az Azure Active Directory alkalmazásproxyn keresztüli távoli eléréséhez című témakörben talál.](application-proxy-add-on-premises-application.md)

1. Jelentkezzen be az [Azure Active Directory-portálra](https://aad.portal.azure.com/) alkalmazásrendszergazdaként. Megjelenik **az Azure Active Directory felügyeleti központ** lapja.
1. Válassza az **Azure Active Directory** > **alkalmazásproxy** > **letöltési összekötő szolgáltatás átválasztását.** Megjelenik **az Alkalmazásproxy-összekötő letöltése** lap.

   ![Alkalmazásproxy-összekötő letöltése](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Kövesse a telepítési utasításokat.

Az összekötő letöltésének automatikusan engedélyeznie kell az alkalmazásproxyt a könyvtárhoz, de ha nem, akkor válassza az **Alkalmazásproxy engedélyezése**lehetőséget.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Az alkalmazás hozzáadása az Azure AD-hez alkalmazásproxyval

Az Azure Portalon két műveletet kell végrehajtania. Először közzé kell tennie az alkalmazást az alkalmazásproxyval. Ezután össze kell gyűjtenie néhány információt az alkalmazásról, amelyet a PingAccess lépések során használhat.

#### <a name="publish-your-application"></a>Az alkalmazás közzététele

Először közzé kell tennie az alkalmazást. Ez a művelet a következőket foglalja magában:

- A helyszíni alkalmazás hozzáadása az Azure AD-hez
- Felhasználó hozzárendelése az alkalmazás teszteléséhez és a fejlécalapú egyszeri bejelentkezés kiválasztásához
- Az alkalmazás átirányítási URL-címének beállítása
- Engedélyek megadása a felhasználók nak és más alkalmazásoknak a helyszíni alkalmazás használatához

Saját helyszíni alkalmazás közzététele:

1. Ha nem az utolsó szakaszban, jelentkezzen be az [Azure Active Directory-portálon,](https://aad.portal.azure.com/) mint egy alkalmazás-rendszergazda.
1. Válassza **a Vállalati alkalmazások** > **új alkalmazás** > **hozzáadása helyszíni alkalmazás**lehetőséget. Megjelenik **a Saját helyszíni alkalmazás** hozzáadása lap.

   ![Saját helyszíni alkalmazás hozzáadása](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Töltse ki a szükséges mezőket az új alkalmazással kapcsolatos információkkal. A beállításokhoz használja az alábbi útmutatót.

   > [!NOTE]
   > A lépés részletesebb áttekintését a Helyszíni alkalmazás hozzáadása az [Azure AD-hez](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)című témakörben ismerteti.

   1. **Belső URL:** Általában megadja az URL-t, amely az alkalmazás bejelentkezési oldalára vezet, amikor a vállalati hálózaton van. Ebben a forgatókönyvben az összekötő kell kezelni a PingAccess proxy az alkalmazás főoldalaként. Használja ezt `https://<host name of your PingAccess server>:<port>`a formátumot: . A port alapértelmezés szerint 3000, de a PingAccess programban konfigurálható.

      > [!WARNING]
      > Az ilyen típusú egyszeri bejelentkezéshez a belső `https` URL-címet `http`kell használnia, és nem használhatja a .

   1. **Hitelesítés előtti módszer:** Válassza az **Azure Active Directory**lehetőséget.
   1. **URL fordítása a Fejlécekben:** Válassza a **Nem**lehetőséget.

   > [!NOTE]
   > Ha ez az első alkalmazása, a 3000-es portot használja a kezdéshez, és ha módosítja a PingAccess konfigurációját, frissítse ezt a beállítást. A későbbi alkalmazások esetében a portnak meg kell egyeznie a PingAccess-ben konfigurált figyelővel. További információ [a figyelőkről a PingAccess programban.](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)

1. Válassza a **Hozzáadás** lehetőséget. Megjelenik az új alkalmazás áttekintő lapja.

Most rendeljen hozzá egy felhasználót az alkalmazás teszteléséhez, és válassza a fejlécalapú egyszeri bejelentkezést:

1. Az alkalmazás oldalsávján válassza a **Felhasználók és csoportok** > **Felhasználói** > felhasználók és csoportok hozzáadása **(Number\<> Selected)** lehetőséget. Megjelenik a felhasználók és csoportok listája, amelyből választhat.

   ![A felhasználók és csoportok listájának megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Jelöljön ki egy felhasználót az alkalmazás teszteléséhez, és válassza **a Kijelölés lehetőséget.** Győződjön meg arról, hogy a tesztfiók rendelkezik-e hozzáféréssel a helyszíni alkalmazáshoz.
1. Válassza a **Hozzárendelés** elemet.
1. Az alkalmazás oldalsávján válassza **az Egyszeri bejelentkezés fejlécalapú** > **lehetőséget.**

   > [!TIP]
   > Ha ez az első alkalom, hogy fejlécalapú egyszeri bejelentkezést használ, telepítenie kell a PingAccess alkalmazást. Annak érdekében, hogy az Azure-előfizetés automatikusan társítva legyen a PingAccess-telepítéssel, használja az egyetlen bejelentkezési lapon található hivatkozást a PingAccess letöltéséhez. Megnyithatja a letöltési webhelyet most, vagy később visszatérhet erre az oldalra.

   ![Fejlécalapú bejelentkezési képernyő és PingAccess megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Kattintson a **Mentés** gombra.

Ezután győződjön meg arról, hogy az átirányítási URL-cím a külső URL-re van állítva:

1. Az **Azure Active Directory felügyeleti központ** oldalsávján válassza az Azure Active **Directory** > **alkalmazás regisztrációit.** Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Jelölje ki az **Átirányítás URI-k**melletti hivatkozást, amely a webes és nyilvános ügyfelekhez beállított átirányítási URI-k számát mutatja. Megjelenik az ** \<alkalmazás neve> - Hitelesítés** lap.
1. Ellenőrizze, hogy az alkalmazáshoz korábban hozzárendelt külső URL-cím szerepel-e az **Átirányítás URI-k** listájában. Ha nem, adja hozzá most a külső URL-címet, **Web**egy átirányítási URI-webtípussal, és válassza a **Mentés gombot.**

Végül állítsa be a helyszíni alkalmazást úgy, hogy a felhasználók olvasási hozzáféréssel és más alkalmazások olvasási/írási hozzáféréssel rendelkezhessenek:

1. Az **alkalmazás regisztrációi** oldalsávja az alkalmazáshoz lehetőséget **választja az API-engedélyek** > **hozzáadása** > **Microsoft API-k** > **microsoft graph**. Megjelenik a **Microsoft Graph** **API-engedélyek kérése** lapja, amely a Windows Azure Active Directory API-jait tartalmazza.

   ![Az API-engedélyek kérése lap megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Válassza **a Delegált engedélyek et** > **User** > **User.Read lehetőséget.**
1. Válassza **az Alkalmazásengedélyek** > **Alkalmazásalkalmazás.ReadWrite.All**lehetőséget.**Application** > 
1. Válassza **az Engedélyek hozzáadása**lehetőséget.
1. Az **API-engedélyek** lapon válassza a **Rendszergazdai hozzájárulás megadása lehetőséget a könyvtárnévhez \<>. **

#### <a name="collect-information-for-the-pingaccess-steps"></a>Adatok gyűjtése a PingAccess lépéseihez

Az alkalmazás PingAccess segítségével történő beállításához össze kell gyűjtenie ezt a három információt (az összes GUID-ot):

| Az Azure AD mező neve | A PingAccess mező neve | Adatformátum |
| --- | --- | --- |
| **Alkalmazás (ügyfél) azonosítója** | **Ügyfélazonosító** | GUID |
| **Címtár (bérlő) azonosítója** | **Kibocsátó** | GUID |
| `PingAccess key` | **Ügyfél titok** | Véletlen karakterlánc |

Az adatok összegyűjtése:

1. Az **Azure Active Directory felügyeleti központ** oldalsávján válassza az Azure Active **Directory** > **alkalmazás regisztrációit.** Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást. Megjelenik az **alkalmazás regisztrációi** lapja.

   ![A bejelentés regisztrációjának áttekintése](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Az **alkalmazásazonosító (ügyfélazonosítója)** érték mellett jelölje ki a **Másolás vágólapra** ikont, majd másolja és mentse. Ezt az értéket később a PingAccess ügyfélazonosítójaként adhatja meg.
1. Ezután a **Címtár (bérlői) azonosító** értéke után válassza **a Másolás a vágólapra**lehetőséget, majd másolja és mentse azt. Ezt az értéket később a PingAccess kibocsátójaként adhatja meg.
1. Az **alkalmazás regisztrációinak** oldalsávján válassza **a Tanúsítványok és titkok** > **Új ügyféltitok**lehetőséget. Megjelenik **az Ügyféltitkos adatok hozzáadása** lap.

   ![Az Ügyféltitkos lista hozzáadása lap megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. A Leírás `PingAccess key`mezőbe írja be a következőt: .In Description ( **Leírás)** mezőbe írja
1. A **Lejár,** válassza ki, hogyan kell beállítani a PingAccess billentyűt: **1 év**, 2 év **vagy** **soha**.
1. Válassza a **Hozzáadás** lehetőséget. A PingAccess kulcs megjelenik az ügyféltitkos kulcsok táblázatában, egy véletlenszerű karakterlánccal, amely automatikusan kitölti az **ÉRTÉK** mezőt.
1. A PingAccess billentyű **ÉRTÉKE** mezője mellett jelölje ki a **Másolás vágólapra** ikont, majd másolja és mentse. Ezt az értéket később pingaccess ügyféltként adja meg.

**A `acceptMappedClaims` mező frissítése:**

1. Jelentkezzen be az [Azure Active Directory-portálra](https://aad.portal.azure.com/) alkalmazásrendszergazdaként.
1. Válassza az **Azure Active Directory** > **alkalmazásregisztrációk lehetőséget.** Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Az **alkalmazás regisztrációi** lapjának oldalsávján válassza a **Manifest**lehetőséget. Megjelenik az alkalmazás regisztrációjának json-kódja.
1. Keresse meg `acceptMappedClaims` a mezőt, `True`és módosítsa az értéket .
1. Kattintson a **Mentés** gombra.

### <a name="use-of-optional-claims-optional"></a>Választható jogcímek használata (nem kötelező)

Választható jogcímek lehetővé teszi, hogy hozzá szabványos, de nem szerepel-by-alapértelmezett jogcímek, hogy minden felhasználó és a bérlő rendelkezik. Az alkalmazás jegyzékfájljának módosításával konfigurálhatja az alkalmazás választható jogcímeket. További információkért tekintse [meg az Azure AD-alkalmazás jegyzékfájljának ismertetése című cikket.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Példa az e-mail cím nek a PingAccess által felhasznált access_token való felvételére:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Jogcímleképezési házirend használata (nem kötelező)

[Jogcímleképezési szabályzat (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) az AzureAD-ban nem létező attribútumokhoz. A jogcímleképezés lehetővé teszi a régi helyszíni alkalmazások felhőbe való áttelepítését további egyéni jogcímek hozzáadásával, amelyeket az ADFS vagy a felhasználói objektumok támogatnak

Ha azt szeretné, hogy az alkalmazás egyéni jogcímet használjon, és további mezőket tartalmazzon, győződjön meg arról, hogy [egyéni jogcímleképezési szabályzatot is létrehozott, és hozzárendelte az alkalmazáshoz.](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)

> [!NOTE]
> Egyéni jogcím használatához egyéni házirendet is meg kell adnia és hozzá kell rendelnie az alkalmazáshoz. Ennek a házirendnek tartalmaznia kell az összes szükséges egyéni attribútumot.
>
> Szabályzat-definíció és -hozzárendelés a PowerShell en vagy a Microsoft Graph-on keresztül. Ha a PowerShellben végzi őket, előfordulhat, `New-AzureADPolicy` hogy először használnia `Add-AzureADServicePrincipalPolicy`kell, majd hozzá kell rendelnie az alkalmazáshoz a segítségével. További információt a [Jogcímhozzárendelési házirend-hozzárendelés című](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)témakörben talál.

Példa:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Egyéni jogcímek használatának engedélyezése a PingAccess számára

Engedélyezve PingAccess egyéni jogcímek használata nem kötelező, de szükséges, ha azt várja, hogy az alkalmazás további jogcímeket.

Amikor a következő lépésben konfigurálja a PingAccess programot, a létrehozandó webmunkamenetnek (Settings->Access->websessions) a **kérelemprofilnak** be kell jelölve, **a Felhasználói attribútumok frissítése** pedig **Nem** értékre kell állnia.

## <a name="download-pingaccess-and-configure-your-application"></a>A PingAccess letöltése és az alkalmazás konfigurálása

Most, hogy elvégezte az Azure Active Directory összes beállítási lépését, átléphet a PingAccess konfigurálására.

A forgatókönyv PingAccess részének részletes lépései a Ping Identity dokumentációban folytatódnak. Kövesse a [PingAccess konfigurálása az Azure AD-hez](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) című, a Ping Identity webhelyen a Microsoft Azure AD alkalmazásproxy használatával közzétett alkalmazások védelmére című, a Ping Access for Azure AD című útmutató utasításait.

Ezek a lépések segítenek a PingAccess telepítésében és a PingAccess-fiók beállításában (ha még nem rendelkezik ilyenekkel). Ezután hozzon létre egy Azure AD OpenID Connect (OIDC) kapcsolatot, hozzon létre egy jogkivonat-szolgáltatót a **címtár (bérlői) azonosító** értékkel, amelyet az Azure AD-portálról másolt. Ezután hozzon létre egy webes munkamenetet a PingAccess-en, használja az **alkalmazás (ügyfél) azonosítóját** és `PingAccess key` értékeit. Ezt követően beállíthatja az identitásleképezést, és létrehozhat egy virtuális állomást, webhelyet és alkalmazást.

### <a name="test-your-application"></a>Az alkalmazás tesztelése

Miután elvégezte ezeket a lépéseket, az alkalmazásnak futnia kell. A teszteléshez nyisson meg egy böngészőt, és keresse meg azt a külső URL-címet, amelyet az alkalmazás Azure-beli közzétételekor hozott létre. Jelentkezzen be az alkalmazáshoz rendelt tesztfiókkal.

## <a name="next-steps"></a>További lépések

- [A PingAccess for Azure AD konfigurálása a Microsoft Azure AD alkalmazásproxyval közzétett alkalmazások védelmére](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](what-is-single-sign-on.md)
- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)
