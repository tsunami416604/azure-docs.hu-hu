---
title: Fejlécalapú hitelesítéskor a pingaccess segítségével az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Tegye közzé az alkalmazásokat a PingAccess és alkalmazásproxy fejléc-alapú hitelesítés támogatásához.
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
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c43498a7829a43fad331841aca045f52ae680be
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481466"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Fejlécalapú hitelesítéskor az egyszeri bejelentkezést az alkalmazásproxy és a PingAccess

Az Azure Active Directory (Azure AD) alkalmazásproxy a pingaccess segítségével alakított ki, hogy az Azure AD-ügyfelek hozzáférhetnek az alkalmazások több. PingAccess kibővíti a [meglévő Application Proxy ajánlatait](application-proxy.md) egyszeri bejelentkezés alkalmazásokhoz való hozzáférést, amelyek a hitelesítési fejléceket tartalmazza.

## <a name="whats-pingaccess-for-azure-ad"></a>Mit jelent a PingAccess Azure AD-hez?

PingAccess Azure AD-hez, az adhat felhasználói hozzáférést és egyszeri bejelentkezés (SSO) olyan alkalmazások, amelyek a fejlécek használata a hitelesítéshez. Application Proxy ezeket az alkalmazásokat, mint bármely más, az Azure AD segítségével hitelesíti a hozzáférést, majd az összekötő szolgáltatás forgalmát kezeli. PingAccess helyezkedik el az alkalmazásokat, és a hozzáférési jogkivonatot az Azure ad-ből fordítja le a fejléc. Az alkalmazás ezután kap a hitelesítés, olvasható formátumban.

A felhasználók nem figyelje meg, hogy bármi más, amikor bejelentkeznek a vállalati alkalmazások használatát. Továbbra is dolgozhatnak bárhol, bármilyen eszközön. Az alkalmazásproxy-összekötők közvetlen során a hitelesítés típusa, függetlenül az összes alkalmazás távoli forgalmat, így azok fog továbbra is elosztása terhelések automatikusan.

## <a name="how-do-i-get-access"></a>Hogyan szerezhetem be a hozzáférést?

Mivel ebben a forgatókönyvben az Azure Active Directory és PingAccess közötti partneri származik,-licenc szükséges mindkét szolgáltatás esetében. Azonban az Azure Active Directory Premium-előfizetések is tartalmazzák, egy alapszintű PingAccess licenc, amely lefedi a akár 20 alkalmazáshoz. Ha több mint 20 fejléc-alapú alkalmazások közzétételét van szüksége, további PingAccess lehet vásárolni.

További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="publish-your-application-in-azure"></a>Az alkalmazás közzététele az Azure-ban

Ez a cikk az ebben a forgatókönyvben az első alkalmazás közzététele a felhasználók számára. Amellett, hogy a közzétételi lépéseket részletesen bemutató, azt hogyan vezeti Önt végig az Ismerkedés az Application Proxy és a PingAccess. Ha már konfigurálta a mindkét szolgáltatás, de a közzétételi lépéseket felelevenítéséhez, ugorjon a [adja hozzá az alkalmazást az Azure AD-alkalmazásproxyval](#add-your-application-to-azure-ad-with-application-proxy) szakaszban.

> [!NOTE]
> Mivel ebben a forgatókönyvben az Azure AD közötti partneri és PingAccess, bizonyos utasítások találhatók a Ping Identity webhelyen.

### <a name="install-an-application-proxy-connector"></a>Az alkalmazásproxy-összekötő telepítése

Ha engedélyezve van a Proxy engedélyezve van, és a egy összekötő már telepítve, kihagyhatja ezt a szakaszt, és nyissa meg [adja hozzá az alkalmazást az Azure AD-alkalmazásproxyval](#add-your-application-to-azure-ad-with-application-proxy).

Az Application Proxy connector egy Windows Server-szolgáltatás, amely arra utasítja a közzétett alkalmazások a távoli alkalmazottak érkező forgalom. További részletes telepítési utasításokat: [oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure Active Directoryban](application-proxy-add-on-premises-application.md).

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/) alkalmazást rendszergazdaként. A **Azure Active Directory felügyeleti központ** lap jelenik meg.
2. Válassza ki **Azure Active Directory** > **alkalmazásproxy** > **összekötőszolgáltatás letöltése**. A **Application Proxy Connector letöltése** lap jelenik meg.

   ![Application proxy connector letöltése](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Kövesse a telepítési utasításokat.

Az összekötő letöltésével kell automatikusan alkalmazásproxy engedélyezése a címtárban, de ha nem, akkor választhat **alkalmazásproxy engedélyezése**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Adja hozzá az alkalmazást az Azure AD-alkalmazásproxyval

Nincsenek két műveletet kell tennie az Azure Portalon. Először az alkalmazásproxy használatával az alkalmazás közzétételéhez. Ezt követően kell gyűjtenie némi információt az alkalmazást a PingAccess lépések során használható.

#### <a name="publish-your-application"></a>Az alkalmazás közzététele

Először kell közzéteheti az alkalmazását. Ez a művelet foglalja magában:

- Az Azure ad-hez a helyszíni alkalmazás hozzáadása
- Felhasználók hozzárendelése az alkalmazás teszteléséhez, és a fejléc-alapú egyszeri bejelentkezés kiválasztása
- Az alkalmazás átirányítási URL-cím beállítása
- A felhasználók és más alkalmazásokat a helyszíni alkalmazások az engedélyek megadása

A saját helyszíni alkalmazás közzététele:

1. Ha még nem az utolsó szakaszban, jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/) alkalmazást rendszergazdaként.
1. Válassza ki **vállalati alkalmazások** > **új alkalmazás** > **helyszíni alkalmazás**. A **saját helyszíni alkalmazás hozzáadása** lap jelenik meg.

   ![Saját helyszíni alkalmazás hozzáadása](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkat. A beállítások használja az alábbi útmutatást.

   > [!NOTE]
   > Ebben a lépésben egy részletesebb leírását lásd: [helyszíni alkalmazás hozzáadása az Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Belső URL-cím**: Normális esetben adja meg az URL-cím, amellyel az alkalmazás bejelentkezési oldala a vállalati hálózaton dolgozik. Ebben a forgatókönyvben az összekötőjét a PingAccess proxy kezelni a az alkalmazás első lapját. Ezt a formátumot használja: `https://<host name of your PingAccess server>:<port>`. A port 3000 alapértelmezés szerint, de a PingAccess konfigurálhatja.

      > [!WARNING]
      > Az ilyen típusú egyszeri bejelentkezést, a belső URL-címet kell használnia `https` , és nem használhat `http`.

   1. **Az előhitelesítési módszer**: Válasszon **az Azure Active Directory**.
   1. **A fejlécek URL-cím fordításának**: Válasszon **nem**.

   > [!NOTE]
   > Ha az első alkalmazását, használja a port 3000 elindításához, és térjen vissza a frissítse ezt a beállítást, ha a PingAccess konfiguráció módosítása. További alkalmazások a portot kell felel meg a figyelő a PingAccess konfigurálta. Tudjon meg többet [figyelői a PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

1. Válassza a **Hozzáadás** lehetőséget. Az új alkalmazás áttekintő oldala jelenik meg.

Most rendelje hozzá a felhasználó az alkalmazás tesztelésének előkészítéséhez, és válasszon fejléc-alapú egyszeri bejelentkezés:

1. Az alkalmazás oldalsávon válassza **felhasználók és csoportok** > **felhasználó hozzáadása** > **felhasználók és csoportok (\<szám > kijelölt)** . Felhasználók és csoportok listáját, amelyek közül választhatnak, jelenik meg.

   ![Felhasználók és csoportok listáját jeleníti meg](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Válasszon ki egy felhasználót az alkalmazás tesztelésének előkészítéséhez, és válassza ki **kiválasztása**. Ellenőrizze, hogy a teszt a helyszíni alkalmazás hozzáféréssel rendelkezik.
1. Válassza a **Hozzárendelés** elemet.
1. Az alkalmazás oldalsávon válassza **egyszeri bejelentkezési** > **fejlécalapú**.

   > [!TIP]
   > Ha ez az első alkalommal használja a fejléc-alapú egyszeri bejelentkezés, a PingAccess telepíteni szeretné. Ahhoz, hogy az Azure-előfizetése a PingAccess telepítés automatikusan társítva, az egyszeri bejelentkezési oldalon a hivatkozás segítségével töltse le a PingAccess. Most nyissa meg a letöltési oldalon, vagy térjen vissza erre a lapra később.

   ![Fejlécalapú bejelentkezés képernyő és a PingAccess jeleníti meg](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Kattintson a **Mentés** gombra.

Majd ellenőrizze, hogy az átirányítási URL-cím beállítása a külső URL-cím:

1. Az a **Azure Active Directory felügyeleti központ** oldalsávon válassza **Azure Active Directory** > **alkalmazásregisztrációk**. Azon alkalmazások listája jelenik meg.
1. Válassza ki az alkalmazását.
1. Válassza ki a kapcsolat melletti **átirányítási URI-k**, azokról az átirányítási URI-k állítható be a web- és nyilvános ügyfelek. A  **\<alkalmazás neve >-hitelesítési** lap jelenik meg.
1. Ellenőrizze, hogy a külső URL-cím, amelyet korábban az alkalmazáshoz rendelt a a **átirányítási URI-k** listája. Ellenkező esetben adja hozzá a külső URL-cím, egy átirányítási URI-t típusú használatával **webes**, és válassza ki **mentése**.

Végül, a helyszíni alkalmazás beállítása, hogy a felhasználó rendelkezik olvasási hozzáféréssel és más alkalmazások olvasási/írási hozzáféréssel rendelkeznek:

1. Az a **alkalmazásregisztrációk** az alkalmazás oldalsávon válassza **API-engedélyek** > **adjon hozzá egy engedélyt**  >   **A Microsoft API-k** > **Microsoft Graph**. A **kérelem API-engedélyek** lapján **Microsoft Graph** jelenik meg, amely tartalmazza az API-k a Windows Azure Active Directoryban.

   ![A kérelem API-engedélyek lapját jeleníti meg](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Válassza ki **delegált engedélyek** > **felhasználói** > **User.Read**.
1. Válassza ki **Alkalmazásengedélyek** > **alkalmazás** > **Application.ReadWrite.All**.
1. Válassza ki **engedélyek hozzáadása**.
1. Az a **API-engedélyek** lapon jelölje be **adja meg a rendszergazdai jóváhagyás \<a könyvtárnév >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>A PingAccess lépéseket-adatainak összegyűjtése

Kell gyűjtenie, ezek három információt (az összes GUID) állíthatja be az alkalmazást a pingaccess segítségével:

| Az Azure AD-mező neve | PingAccess mező neve | Adatformátum |
| --- | --- | --- |
| **Az Alkalmazásazonosítót (ügyfél)** | **Ügyfél-azonosító** | GUID |
| **(Bérlő) címtár-azonosító** | **Kiállító** | GUID |
| `PingAccess key` | **Titkos Ügyfélkód** | Véletlenszerű karakterlánc |

Ezek az információk gyűjtésére:

1. Az a **Azure Active Directory felügyeleti központ** oldalsávon válassza **Azure Active Directory** > **alkalmazásregisztrációk**. Azon alkalmazások listája jelenik meg.
1. Válassza ki az alkalmazását. A **alkalmazásregisztrációk** oldala jelenik meg, az alkalmazás.

   ![Az alkalmazás regisztrációs áttekintése](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Melletti a **Alkalmazásazonosítót (ügyfél)** értéket, válassza a **Másolás a vágólapra** ikonra, majd másolja és mentse. Mindenképp adja meg ezt az értéket később PingAccess az ügyfél-azonosítót.
1. Tovább a **(bérlő) címtár-azonosító** is válassza ki a érték **példányt vágólapra**, majd másolja és mentse azt. Azt adja meg ezt az értéket később PingAccess a kibocsátó.
1. Az oldalsávon a **alkalmazásregisztrációk** válassza ki az alkalmazás **tanúsítványok és titkos kulcsok** > **új titkos ügyfélkulcsot**. A **ügyfélkódot hozzáadása** lap jelenik meg.

   ![Ügyfél titkos oldalt jelenít meg a hozzáadása](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. A **leírás**, típus `PingAccess key`.
1. A **lejárat**, válassza ki a PingAccess kulcs beállítása: **1 év múlva**, **2 évben**, vagy **soha**.
1. Válassza a **Hozzáadás** lehetőséget. A PingAccess megjelenik a kulcs a táblázat az ügyfél titkos adatait, és a egy véletlenszerű karakterlánc az adott autofills a **érték** mező.
1. Mellett a PingAccess kulcs **érték** mezőben válassza a **Másolás a vágólapra** ikonra, majd másolja és mentse azt. Azt adja meg ezt az értéket később PingAccess a titkos Ügyfélkód.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Frissítés GraphAPI küldendő egyéni mezők (nem kötelező)

Ha egy egyéni jogcímszabályok által küldött többi jogkivonatok belül van szüksége a access_token PingAccess paraméterét, állítsa be a `acceptMappedClaims` alkalmazás mezőt `True`. Graph Explorer is használhatja, vagy az Azure AD portálon alkalmazásjegyzék ezt a módosítást.

**Ez a példa Graph Explorer használja:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**Ez a példa a [Azure Active Directory portálon](https://aad.portal.azure.com/) frissíteni a `acceptMappedClaims` mező:**

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/) alkalmazást rendszergazdaként.
1. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk**. Azon alkalmazások listája jelenik meg.
1. Válassza ki az alkalmazását.
1. Az oldalsávon a **alkalmazásregisztrációk** az alkalmazást, válassza a lap **Manifest**. Az alkalmazás regisztrálása a jegyzékfájl JSON-kód jelenik meg.
1. Keresse meg a `acceptMappedClaims` mezőben, majd módosítsa az értéket `True`.
1. Kattintson a **Mentés** gombra.

### <a name="use-of-optional-claims-optional"></a>Használja a választható jogcímek (nem kötelező)

Nem kötelező jogcímek standard-but-not-included-by-default jogcímeket, amely minden felhasználó és a bérlő rendelkezik hozzáadását teszi lehetővé. Az alkalmazás nem kötelező jogcímek az alkalmazásjegyzékben módosításával konfigurálhatja. További információ: a [az Azure AD application manifest cikk ismertetése](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

E-mail cím venni az a, amely felhasználja a PingAccess access_token. példa:
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

### <a name="use-of-claims-mapping-policy-optional"></a>Jogcímek társítása a házirend alól (választható) használata

[Jogcím-szabályzat hozzárendelése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/) attribútumok, amelyek nem léteznek az Azure ad számára. Jogcímtársítások lehetővé teszi, hogy a régi helyszíni alkalmazások migrálása a felhőbe, amely az AD FS vagy a felhasználói objektumok élvezik további egyéni jogcímek hozzáadásával

Ahhoz, hogy az alkalmazás egy egyéni jogcímszabályok használja, és további mezőket tartalmaznak, lehet, hogy megismerte is [egy egyéni jogcímek társítása a házirend létrehozása és hozzárendelése, az alkalmazás](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Egyéni jogcím használatával is rendelkeznie kell meghatározott, és az alkalmazáshoz hozzárendelt egyéni házirendet. Ez a szabályzat tartalmaznia kell az összes szükséges egyéni attribútumokat.
>
> Szabályzat-definíció és a hozzárendelés révén a PowerShell, az Azure AD Graph Explorer vagy a Microsoft Graph teheti meg. Ha ezeket a PowerShell, szüksége lehet, hogy először a `New-AzureADPolicy` , és hozzárendelheti az alkalmazást a `Add-AzureADServicePrincipalPolicy`. További információkért lásd: [jogcím-hozzárendelési szabályzat-hozzárendelés](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Példa:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Egyéni jogcímek használata a PingAccess engedélyezése

Egyéni jogcímek használata a PingAccess engedélyezése nem kötelező, de szükséges, ha az alkalmazás további jogcímek által várt.

PingAccess fogja beállítani a következő lépést, ha a webes munkamenet hoz létre (Beállítások -> hozzáférés -> webes előadások) rendelkeznie kell **kérelem profil** sincs kijelölve, és **felhasználói attribútumok frissítése** Állítsa be **nem**

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess letöltéséhez, és az alkalmazás konfigurálása

Most, hogy végrehajtotta az összes Azure Active Directory-beállítási lépéseket, áthelyezheti PingAccess konfigurálása.

A részletes lépéseket ebben a forgatókönyvben a PingAccess része továbbra is a Ping Identity dokumentációjában. Kövesse a [használatával a Microsoft Azure AD-alkalmazásproxyval közzétett alkalmazások védelmét az Azure AD-alapú PingAccess konfigurálása](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) a Ping Identity a webhelyről.

Ezeket a lépéseket segítséget nyújt a PingAccess telepítéséhez és a PingAccess fiók beállítása (Ha még nem rendelkezik egy). Ezután, egy Azure AD OpenID Connect (OIDC) kapcsolatot szeretne létrehozni, állítsa be a jogkivonat-szolgáltatót a **(bérlő) címtár-azonosító** érték, amely az Azure AD portálon fájlból kimásolt. Ezután hozzon létre a webes munkamenet a PingAccess, használja a **Alkalmazásazonosítót (ügyfél)** és `PingAccess key` értékeket. Ezt követően állítsa be a identitásleképezési, és hozzon létre egy virtuális gazdagép, a hely és az alkalmazás.

### <a name="test-your-application"></a>Az alkalmazás tesztelése

Ha végrehajtotta ezeket a lépéseket, az alkalmazás működik és kell lennie. Tesztelje, nyisson meg egy böngészőt, és keresse meg a külső URL-cím, az Azure-ban az alkalmazás közzétételekor létrehozott. Jelentkezzen be a teszt-fiókkal, amelyet az alkalmazáshoz rendelt.

## <a name="next-steps"></a>További lépések

- [A Microsoft Azure AD-alkalmazásproxy használatával közzétett alkalmazások védelmét az Azure AD-alapú PingAccess konfigurálása](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](what-is-single-sign-on.md)
- [Az alkalmazásproxy-problémák és hibaüzenetek hibaelhárítása](application-proxy-troubleshoot.md)
