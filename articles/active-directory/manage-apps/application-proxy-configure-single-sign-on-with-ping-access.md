---
title: Fejléc-alapú hitelesítés az Azure AD Application Proxy PingAccess-mel | Microsoft Docs
description: Alkalmazások közzététele a PingAccess és az App proxyval a fejléc-alapú hitelesítés támogatásához.
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
ms.openlocfilehash: ec115e0fa76e695809ba140202d5f13a319d33dd
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062706"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Fejléc-alapú hitelesítés egyszeri bejelentkezéshez alkalmazásproxy és PingAccess

Az Azure Active Directory (Azure AD) alkalmazásproxy partneri kapcsolatban áll a PingAccess, így az Azure AD-ügyfelek több alkalmazáshoz is hozzáférhetnek. A PingAccess kibővíti a [meglévő alkalmazásproxy-ajánlatokat](application-proxy.md) , hogy egyszeri bejelentkezéses hozzáférést biztosítson az olyan alkalmazásokhoz, amelyek fejléceket használnak a hitelesítéshez.

## <a name="whats-pingaccess-for-azure-ad"></a>Mi a PingAccess az Azure AD-hez?

A PingAccess for Azure AD lehetővé teszi a felhasználók számára, hogy hozzáférést és egyszeri bejelentkezést (SSO) biztosítson a fejléceket használó alkalmazásokhoz a hitelesítéshez. Az alkalmazásproxy ezeket az alkalmazásokat, mint bármely más, az Azure AD használatával hitelesíti a hozzáférést, majd továbbítja a forgalmat az összekötő szolgáltatáson keresztül. A PingAccess az alkalmazások előtt ül, és lefordítja a hozzáférési jogkivonatot az Azure AD-ből egy fejlécbe. Az alkalmazás ezután az általa olvasható formátumban fogadja a hitelesítést.

A felhasználók nem láthatnak mást, amikor bejelentkeznek a céges alkalmazásai használatára. Továbbra is bárhonnan dolgozhatnak bármely eszközről. Az alkalmazásproxy összekapcsolja az összes alkalmazáshoz való közvetlen távoli forgalmat a hitelesítési típusuk nélkül, így továbbra is automatikusan betöltődik.

## <a name="how-do-i-get-access"></a>Hogyan a hozzáférés?

Mivel ez a forgatókönyv Azure Active Directory és PingAccess közötti partnerségből származik, mindkét szolgáltatáshoz licenc szükséges. Prémium szintű Azure Active Directory előfizetések azonban egy alapszintű PingAccess-licencet tartalmaznak, amely akár 20 alkalmazást is magában foglal. Ha több mint 20 fejléc-alapú alkalmazást kell közzétennie, vásárolhat további licencet a PingAccess.

További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="publish-your-application-in-azure"></a>Az alkalmazás közzététele az Azure-ban

Ez a cikk azt ismerteti, hogy a felhasználók első alkalommal teszik közzé az alkalmazást ebben a forgatókönyvben. A közzétételi lépések részletes ismertetése mellett végigvezeti Önt az alkalmazásproxy és a PingAccess első lépésein is. Ha már konfigurálta mindkét szolgáltatást, de a közzétételi lépéseket szeretné frissíteni, ugorjon az [alkalmazás hozzáadása az Azure ad](#add-your-application-to-azure-ad-with-application-proxy) -hez az alkalmazásproxy szakaszban.

> [!NOTE]
> Mivel ez a forgatókönyv az Azure AD és a PingAccess közötti együttműködés, néhány útmutató létezik a ping Identity webhelyen.

### <a name="install-an-application-proxy-connector"></a>Alkalmazásproxy-összekötő telepítése

Ha engedélyezte az alkalmazásproxy engedélyezését, és már telepített egy összekötőt, ugorja át ezt a szakaszt, és lépjen az [alkalmazás hozzáadása az Azure ad-hez az alkalmazásproxy](#add-your-application-to-azure-ad-with-application-proxy)használatával lehetőségre.

Az alkalmazásproxy-összekötő egy olyan Windows Server-szolgáltatás, amely a távoli alkalmazottaktól a közzétett alkalmazásokba irányítja a forgalmat. Részletesebb telepítési útmutatót a következő témakörben talál [: oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez az Application proxyn keresztül Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként. Megjelenik a **Azure Active Directory felügyeleti központ** lap.
1. Válassza ki **Azure Active Directory** > **alkalmazásproxy** > **Download Connector szolgáltatást**. Megjelenik az **alkalmazásproxy-összekötő letöltési** lapja.

   ![Alkalmazásproxy-összekötő letöltése](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Kövesse a telepítési utasításokat.

Az összekötő letöltésének automatikusan engedélyeznie kell az alkalmazásproxy-t a címtárban, de ha nem, akkor válassza az **alkalmazásproxy engedélyezése**lehetőséget.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Alkalmazás felvétele az Azure AD-be alkalmazásproxy használatával

A Azure Portal két műveletet kell végrehajtania. Először közzé kell tennie az alkalmazást az Application proxyval. Ezután össze kell gyűjtenie az alkalmazással kapcsolatos adatokat, amelyeket a PingAccess lépések során használhat.

#### <a name="publish-your-application"></a>Az alkalmazás közzététele

Először közzé kell tennie az alkalmazást. Ez a művelet a következőket foglalja magában:

- Helyszíni alkalmazás hozzáadása az Azure AD-hez
- Felhasználó kiosztása az alkalmazás teszteléséhez és a fejléc-alapú egyszeri bejelentkezés kiválasztásához
- Az alkalmazás átirányítási URL-címének beállítása
- Engedélyek megadása a felhasználók és más alkalmazások számára a helyszíni alkalmazás használatához

Saját helyszíni alkalmazás közzététele:

1. Ha nem az utolsó szakaszban, jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként.
1. Válassza a **vállalati alkalmazások** > **új alkalmazás** lehetőséget > helyszíni **alkalmazás hozzáadása lehetőséget**. Megjelenik a **saját helyszíni alkalmazás hozzáadása** lap.

   ![Saját helyszíni alkalmazás hozzáadása](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkkal. Az alábbi útmutatást használhatja a beállításokhoz.

   > [!NOTE]
   > A lépés részletes ismertetését lásd: helyszíni [alkalmazás hozzáadása az Azure ad-](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)hez.

   1. **Belső URL-cím**: általában megadja azt az URL-címet, amely az alkalmazás bejelentkezési lapjára viszi, amikor a vállalati hálózaton dolgozik. Ebben az esetben az összekötőnek a PingAccess-proxyt kell kezelnie az alkalmazás első oldalán. Használja ezt a formátumot: `https://<host name of your PingAccess server>:<port>`. Alapértelmezés szerint a port 3000, de a PingAccess-ben is konfigurálható.

      > [!WARNING]
      > Ilyen típusú egyszeri bejelentkezés esetén a belső URL-címnek `https`t kell használnia, és nem használhatja a `http`.

   1. **Előhitelesítési módszer**: válassza a **Azure Active Directory**lehetőséget.
   1. **URL-cím lefordítása a fejlécekben**: válassza a **nem**lehetőséget.

   > [!NOTE]
   > Ha ez az első alkalmazása, az 3000-es porton kezdje el, és térjen vissza a beállítás frissítéséhez, ha módosítja a PingAccess-konfigurációt. További alkalmazások esetén a portnak meg kell egyeznie a PingAccess-ben konfigurált figyelővel. További információ a [PingAccess lévő figyelőkről](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Válassza a **Hozzáadás** lehetőséget. Megjelenik az új alkalmazás Áttekintés lapja.

Most rendeljen hozzá egy felhasználót az alkalmazás teszteléséhez, és válassza a fejléc-alapú egyszeri bejelentkezés lehetőséget:

1. Az alkalmazás-oldalsávon válassza a **felhasználók és csoportok** > **felhasználói** > **felhasználók és csoportok hozzáadása lehetőséget (\<szám > kiválasztva)** . Megjelenik a felhasználók és csoportok listája.

   ![A felhasználók és csoportok listájának megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Válasszon ki egy felhasználót az alkalmazás teszteléséhez, és válassza a **kiválasztás**lehetőséget. Győződjön meg arról, hogy a teszt fiók hozzáfér a helyszíni alkalmazáshoz.
1. Válassza a **Hozzárendelés** elemet.
1. Az alkalmazás-oldalsávon válassza az **egyszeri bejelentkezés** > **fejléc-alapú**lehetőséget.

   > [!TIP]
   > Ha első alkalommal használja a fejléc-alapú egyszeri bejelentkezést, telepítenie kell a PingAccess. Annak ellenőrzéséhez, hogy az Azure-előfizetés automatikusan társítva van-e a PingAccess-telepítéshez, a PingAccess letöltéséhez használja az egyszeri bejelentkezési oldalon található hivatkozást. Most már megnyithatja a letöltési helyet, vagy később is visszatérhet erre a lapra.

   ![Fejléc-alapú bejelentkezési képernyő és PingAccess megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Kattintson a **Mentés** gombra.

Ezután ellenőrizze, hogy az átirányítási URL-cím be van-e állítva a külső URL-címre:

1. A **Azure Active Directory felügyeleti központ** oldalsávján válassza a **Azure Active Directory** > **Alkalmazásregisztrációk**lehetőséget. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Válassza az **átirányítási URI**-k elem melletti hivatkozást, amely megjeleníti a webes és a nyilvános ügyfelek számára beállított átirányítási URI-k számát. Megjelenik a **\<alkalmazás neve > – Authentication** lap.
1. Győződjön meg arról, hogy az alkalmazáshoz hozzárendelt külső URL-cím az **átirányítási URI** -k listájában van-e. Ha nem, adja hozzá a külső URL-címet most a **webes**ÁTirányítási URI-típus használatával, majd válassza a **Mentés**lehetőséget.

Végül állítsa be a helyszíni alkalmazást úgy, hogy a felhasználók olvasási hozzáféréssel rendelkezzenek, és más alkalmazásokhoz írási/olvasási hozzáférésük van:

1. Az alkalmazáshoz tartozó **Alkalmazásregisztrációk** oldalsávon válassza az **API-engedélyek** lehetőséget > a **Microsoft API** -k > **Microsoft Graph** **engedély hozzáadása** > . Megjelenik a **Microsoft Graph** **API-engedélyei** lap, amely a Windows Azure Active Directory API-kat tartalmazza.

   ![Megjeleníti a kérelem API-engedélyei lapot.](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Válassza a **delegált engedélyek** > **felhasználó** > **felhasználó. olvasás**lehetőséget.
1. Válassza az alkalmazás **engedélyei** > **alkalmazás** > **Application. ReadWrite. All**elemet.
1. Válassza az **engedélyek hozzáadása**lehetőséget.
1. Az **API-engedélyek** lapon jelölje be a **rendszergazdai jóváhagyás megadása \<a címtár neve >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Információk gyűjtése a PingAccess lépéseiről

Az alkalmazás PingAccess való beállításához össze kell gyűjtenie ezeket a három információt (az összes GUID-azonosítót):

| Az Azure AD-mező neve | PingAccess mező neve | Adatformátum |
| --- | --- | --- |
| **Alkalmazás (ügyfél) azonosítója** | **Ügyfél-azonosító** | GUID |
| **Címtár (bérlő) azonosítója** | **Kibocsátó** | GUID |
| `PingAccess key` | **Ügyfél titka** | Véletlenszerű karakterlánc |

Az adatok összegyűjtése:

1. A **Azure Active Directory felügyeleti központ** oldalsávján válassza a **Azure Active Directory** > **Alkalmazásregisztrációk**lehetőséget. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást. Megjelenik az alkalmazás **Alkalmazásregisztrációk** lapja.

   ![Az alkalmazások regisztrációjának áttekintése](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Az **alkalmazás (ügyfél) azonosítójának** értéke mellett válassza a **Másolás vágólapra** ikont, majd másolja és mentse. Ezt az értéket később a PingAccess ügyfél-AZONOSÍTÓJAként kell megadni.
1. A **könyvtár (bérlő) azonosítójának** értékeként válassza a **Másolás a vágólapra**lehetőséget, majd másolja és mentse. Ezt az értéket később a PingAccess kiállítójának kell megadnia.
1. Az alkalmazás **Alkalmazásregisztrációk** oldalsávján válassza ki a **tanúsítványok és titkok** > **új ügyfél titka**lehetőséget. Megjelenik az **ügyfél titkos kulcsának hozzáadása** lap.

   ![Az ügyfél titkos kulcsának hozzáadása oldal megjelenítése](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. A **Leírás**mezőbe írja be a következőt: `PingAccess key`.
1. A **lejárat**szakaszban válassza ki a PingAccess kulcs beállításának módját: **1 év**, **2 év**vagy **soha**.
1. Válassza a **Hozzáadás** lehetőséget. A PingAccess kulcs az ügyfél titkos kulcsainak táblázatában jelenik meg, és egy véletlenszerű karakterláncot, amely az **érték** mezőben kitöltést eredményez.
1. A PingAccess kulcs **értéke** mező mellett válassza a **Másolás vágólapra** ikont, majd másolja és mentse. Ezt az értéket később is megadhatja a PingAccess-ügyfél titkos kulcsaként.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>GraphAPI frissítése egyéni mezők küldéséhez (nem kötelező)

Ha olyan egyéni jogcímre van szüksége, amely a PingAccess által használt access_token belül más jogkivonatokat küld, a `True`értékre állítsa a `acceptMappedClaims` alkalmazás mezőt. Ezt a módosítást a Graph Explorer vagy az Azure AD Portal alkalmazás-jegyzékfájljának használatával végezheti el.

**Ez a példa a Graph Explorert használja:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**Ez a példa a [Azure Active Directory portál](https://aad.portal.azure.com/) használatával frissíti a `acceptMappedClaims` mezőt:**

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) alkalmazás-rendszergazdaként.
1. Válassza ki **Azure Active Directory** > **Alkalmazásregisztrációk**. Megjelenik az alkalmazások listája.
1. Válassza ki az alkalmazást.
1. Az alkalmazás **Alkalmazásregisztrációk** oldalának oldalsávján válassza a **jegyzékfájl**lehetőséget. Megjelenik az alkalmazás regisztrálásához tartozó JSON-kód.
1. Keresse meg a `acceptMappedClaims` mezőt, és módosítsa az értéket `True`re.
1. Kattintson a **Mentés** gombra.

### <a name="use-of-optional-claims-optional"></a>Választható jogcímek használata (nem kötelező)

A választható jogcímek lehetővé teszik, hogy a felhasználók és a bérlők által biztosított szabványos, de nem belefoglalt alapértelmezett jogcímeket adja hozzá. Az alkalmazás jegyzékfájljának módosításával opcionális jogcímeket konfigurálhat az alkalmazáshoz. További információ: az [Azure ad-alkalmazás jegyzékfájljának ismertetése.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Például az e-mail-cím belefoglalása a access_token, amelyet a PingAccess fog használni:
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

### <a name="use-of-claims-mapping-policy-optional"></a>Jogcím-hozzárendelési szabályzat használata (opcionális)

A [jogcím-hozzárendelési szabályzat (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) olyan attribútumok esetében, amelyek nem léteznek a AzureAD-ben. A jogcímek leképezése lehetővé teszi a régi helyszíni alkalmazások áttelepítése a felhőbe olyan további egyéni jogcímek hozzáadásával, amelyeket az ADFS vagy a felhasználói objektumok támogatnak.

Ahhoz, hogy az alkalmazás egyéni jogcímet használjon, és további mezőket is tartalmazzon, győződjön meg arról, hogy [létrehozta az egyéni jogcím-hozzárendelési szabályzatot, és hozzárendelte azt az alkalmazáshoz](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Egyéni jogcím használatához egyéni szabályzatot is meg kell adni, és hozzá kell rendelni az alkalmazáshoz. Ennek a szabályzatnak tartalmaznia kell az összes kötelező egyéni attribútumot.
>
> A házirend-definíciót és a hozzárendelést a PowerShell, az Azure AD Graph Explorer vagy a Microsoft Graph segítségével végezheti el. Ha ezeket a PowerShellben végzi el, előfordulhat, hogy először a `New-AzureADPolicy` kell használnia, majd hozzá kell rendelnie az alkalmazáshoz `Add-AzureADServicePrincipalPolicy`. További információ: jogcím- [hozzárendelési szabályzat-hozzárendelés](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Példa:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Egyéni jogcímek használatának engedélyezése a PingAccess

A PingAccess egyéni jogcímek használatára való engedélyezése nem kötelező, de szükség van rá, ha azt szeretné, hogy az alkalmazás további jogcímeket használjon.

Ha a következő lépésben konfigurálja a PingAccess, a létrehozandó webes munkamenetnek (Settings-> Access-> Web Sessions) tartalmaznia kell a **kérelem profilját** , és a **nem** értékre állított **felhasználói attribútumokat kell frissítenie** .

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess letöltése és az alkalmazás konfigurálása

Most, hogy végrehajtotta az összes Azure Active Directory telepítési lépést, áthelyezheti a PingAccess konfigurálásához.

A forgatókönyv PingAccess részének részletes lépései a ping Identity dokumentációjában olvashatók. Kövesse a PingAccess konfigurálása az Azure AD-hoz című témakör utasításait a ping Identity webhely [Microsoft Azure ad alkalmazásproxy használatával közzétett alkalmazások védelemmel való ellátásához](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) .

Ezek a lépések segítséget nyújtanak a PingAccess telepítéséhez és egy PingAccess-fiók beállításához (ha még nem rendelkezik ilyennel). Ezután egy Azure AD OpenID Connect-(OIDC-) kapcsolat létrehozásához állítson be egy jogkivonat-szolgáltatót az Azure AD-portálról másolt **címtár-(bérlői) azonosító** értékkel. Következő lépésként hozzon létre egy webes munkamenetet a PingAccess-on, használja az **alkalmazás (ügyfél) azonosítóját** és a `PingAccess key` értékeket. Ezután beállíthatja az identitás-hozzárendelést, és létrehozhat egy virtuális gazdagépet, webhelyet és alkalmazást.

### <a name="test-your-application"></a>Az alkalmazás tesztelése

Az összes lépés elvégzése után az alkalmazásnak futnia kell. A teszteléshez nyisson meg egy böngészőt, és navigáljon arra a külső URL-címre, amelyet az alkalmazás Azure-ban való közzétételekor hozott létre. Jelentkezzen be az alkalmazáshoz rendelt tesztelési fiókkal.

## <a name="next-steps"></a>Következő lépések

- [A PingAccess konfigurálása az Azure AD-hoz a Microsoft Azure AD alkalmazásproxy használatával közzétett alkalmazások védelemmel való ellátásához](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](what-is-single-sign-on.md)
- [Alkalmazásproxy-problémák és hibaüzenetek hibaelhárítása](application-proxy-troubleshoot.md)
