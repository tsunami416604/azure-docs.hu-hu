---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Zscaler használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható Azure AD B2C hitelesítés a Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ff51c2a71dfcaec580733a92e265628ac816e229
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096061"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Oktatóanyag a Zscaler privát hozzáférésének konfigurálásához Azure Active Directory B2C a biztonságos hibrid hozzáféréshez

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja Azure AD B2C hitelesítését a [Zscaler privát elérésével (ZPA)](https://www.zscaler.com/products/zscaler-private-access). A ZPA házirend-alapú, biztonságos hozzáférést biztosít a privát alkalmazásokhoz és eszközökhöz a virtuális magánhálózat (VPN) költséghatékony, problémamentes vagy biztonsági kockázata nélkül. A Zscaler biztonságos hibrid hozzáférési ajánlata nulla támadási felületet tesz lehetővé a felhasználók felé irányuló alkalmazások számára a Azure AD B2C kombinálva.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Egy Azure ad B2C bérlő](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , amely az Azure-előfizetéshez van csatolva.

- [ZPA-előfizetés](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Forgatókönyv leírása

A ZPA-integráció a következő összetevőket tartalmazza:

- Azure AD B2C – a felhasználó hitelesítő adatainak ellenőrzéséhez felelős identitásszolgáltató. Egy új felhasználó regisztrálásához is felelős.

- ZPA – az a szolgáltatás, amely a webalkalmazás biztonságossá tételéhez szükséges a [megbízhatósági hozzáférés](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)kényszerítésével.

- Webalkalmazás – azt a szolgáltatást üzemelteti, amelyhez a felhasználó hozzáfér.

Az alábbi ábra azt mutatja be, hogyan integrálható a ZPA a Azure AD B2Cokkal.

![A kép a zscaler architektúra diagramját mutatja](media/partner-zscaler/zscaler-architecture-diagram.png)

|Lépés | Leírás |
|:-----| :-----------|
| 1. | A felhasználó egy ZPA felhasználói portálon vagy egy ZPA Browser Access alkalmazásban érkezik meg.
| 2. | Az ZPA megköveteli a felhasználói környezet információit, mielőtt eldöntheti, hogy engedélyezi-e a felhasználó számára a webalkalmazás elérését. A felhasználó hitelesítéséhez a ZPA SAML-átirányítást végez a Azure AD B2C bejelentkezési oldalára.  
| 3. | A felhasználó megérkezik az Azure AB B2C bejelentkezési oldalára. Ha ez egy új felhasználó, a felhasználó egy új fiók létrehozásakor regisztrál. Egy meglévő felhasználó a meglévő hitelesítő adataival jelentkezik be. Azure AD B2C érvényesíti a felhasználó identitását.
| 4. | A sikeres hitelesítés után Azure AD B2C átirányítja a felhasználót a ZPA-re az SAML-kijelentéssel együtt. A ZPA ellenőrzi az SAML-kijelentést, és beállítja a felhasználói környezetet.
| 5. | A ZPA kiértékeli a felhasználó hozzáférési szabályzatait. Ha a felhasználó hozzáférhet a webalkalmazáshoz, a kapcsolat továbbítása engedélyezett.

## <a name="onboard-to-zpa"></a>Beléptetés a ZPA

Ez az oktatóanyag feltételezi, hogy már rendelkezik a ZPA egy működő beállításával. Ha megkezdi a ZPA-t, tekintse meg a [ZPA részletes konfigurációs útmutatóját](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>1. rész – Azure AD B2C konfigurálása identitásszolgáltató a ZPA-ben

Azure AD B2C konfigurálása [identitás-szolgáltatóként (identitásszolgáltató) a ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign)-ben:

1. Bejelentkezés a [ZPA felügyeleti portálra](https://admin.private.zscaler.com)

2. Ugrás az **Adminisztráció**  >  **identitásszolgáltató-konfigurációra**

3. Válassza a **identitásszolgáltató-konfiguráció hozzáadása** lehetőséget

4. **1 identitásszolgáltató-információ** esetén adja meg a következőket:

   a. **Név**: Azure ad B2C

   b. **Egyszeri bejelentkezés**: felhasználó kiválasztása

   c. **Tartományok**: válassza ki azokat a hitelesítési tartományokat, amelyeket hozzá szeretne rendelni ehhez a identitásszolgáltató, majd válassza a **kész** lehetőséget.

   ![A képen a identitásszolgáltató adatai láthatók](media/partner-zscaler/add-idp-configuration.png)

5. Kattintson a **Tovább** gombra.

6. **2 SP**-es metaadatok esetén:

   a. Másolja a szolgáltatói URL-címet, és jegyezze fel későbbi használatra.

   b. Másolja a szolgáltatói entitás AZONOSÍTÓját, és jegyezze fel későbbi használatra.

   ![A képen az SP metaadat-információi láthatók](media/partner-zscaler/sp-metadata.png)

7. **Szüneteltetés** kiválasztása

A identitásszolgáltató többi beállítása a Azure AD B2C konfigurálása után is folytatódik.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>2. rész – egyéni házirend konfigurálása Azure AD B2C

>[!Note]
>Ez a lépés csak akkor szükséges, ha még nem rendelkezik egyéni szabályzatokkal. Ha már rendelkezik egy vagy több egyéni házirenddel, akkor kihagyhatja ezt a lépést.

Az egyéni [szabályzatok beszerzése a Azure Active Directory B2Cben](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) című cikk útmutatást nyújt az egyéni szabályzatok Azure ad B2C bérlőre való konfigurálásához.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>3. rész – a ZPA SAML-alkalmazásként való regisztrálása Azure AD B2C

Az [SAML-alkalmazás regisztrálása a Azure ad B2Cban](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) című cikk útmutatást nyújt az SAML-alkalmazások Azure ad B2C-ben történő konfigurálásához. Az [3,2. lépésben](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)a identitásszolgáltató SAML-metaadatok URL-címét kell megadnia, amelyet a Azure ad B2C használ. Erre szüksége lesz a későbbi használatra.

Kövesse az utasításokat a [4,2. lépésen](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest)keresztül. Az utasítás 4,2. lépésében frissítenie kell az alkalmazás jegyzékfájlját. A tulajdonságokat a következőképpen frissítheti:

- **identifierUris**: használja a szolgáltatói entitás azonosítóját az **1. rész, 6a. lépés**.

- **samlMetadataUrl**: hagyja ki ezt a tulajdonságot, mert a ZPA nem tartalmaz SAML-metaadatok URL-címét.

- **replyUrlsWithType**: használja a szolgáltatói URL-címet **,** amely a 6b. részben található.

- **logoutUrl**: hagyja ki ezt a tulajdonságot, mert a ZPA nem támogatja a kijelentkezési URL-címet.

A további lépések nem vonatkoznak ehhez az oktatóanyaghoz.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>4. rész – a identitásszolgáltató SAML-metaadatok kibontása Azure AD B2C

Az előző lépésben be kell szereznie egy SAML-metaadatok URL-címét a következő formátumban:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

ahol a a `<tenant-name>` Azure ad B2C bérlő neve, és az az `<policy-name>` Egyéni SAML-szabályzat neve, amelyet az előző lépésben hozott létre.

Például: https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata

Nyisson meg egy webböngészőt, és navigáljon az SAML-metaadatok URL-címéhez. Amikor az oldal betöltődik, kattintson a jobb gombbal az oldal bármely pontjára. Válassza az **oldal mentése másként** lehetőséget, és mentse a fájlt a számítógépre; ezt a következő részben fogja használni.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>5. rész – a identitásszolgáltató-konfiguráció befejezése a ZPA

Fejezze be a [identitásszolgáltató-konfigurációt a ZPA felügyeleti portálon](https://help.zscaler.com/zpa/configuring-idp-single-sign) , amelyet részben az 1. részben konfiguráltak:

1. Bejelentkezés a [ZPA felügyeleti portálra](https://admin.private.zscaler.com)

2. Nyissa meg az **Adminisztráció**  >  **identitásszolgáltató konfigurációját**.

3. Válassza ki az 1. részben konfigurált identitásszolgáltató, és válassza a **Folytatás** lehetőséget.

4. **3. identitásszolgáltató létrehozása**

   a. Nyissa meg a **identitásszolgáltató metaadatait tartalmazó**  >  **fájlt** , és töltse fel a 4. részben mentett metaadat-fájlt.

   b. Ellenőrizze,  hogy az identitásszolgáltató-konfiguráció állapota **engedélyezve** van-e.

   c. Válassza a **Mentés** lehetőséget.

      ![A képen a identitásszolgáltató létrehozása információ látható](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>A megoldás tesztelése

Nyissa meg a ZPA felhasználói portált vagy egy böngésző-hozzáférési alkalmazást, és tesztelje a regisztrálási vagy bejelentkezési folyamatot. A művelet sikeres SAML-hitelesítést eredményez.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Ismerkedés az egyéni szabályzatokkal Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [SAML-alkalmazás regisztrálása Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Részletes konfigurációs útmutató a ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Identitásszolgáltató konfigurálása egyszeri bejelentkezéshez](https://help.zscaler.com/zpa/configuring-idp-single-sign)
