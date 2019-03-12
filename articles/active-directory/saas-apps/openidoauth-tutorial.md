---
title: Az Azure AD-alkalmazásgyűjtemény egy OpenID/OAuth-alkalmazás konfigurálása |} A Microsoft Docs
description: Az OpenID/OAuth-alkalmazás, az Azure AD-alkalmazásgyűjtemény a konfigurálásához szükséges lépésekről.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713e4e7874b2ca650ab669d52f9d3026b5e80899
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780983"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Az Azure AD-alkalmazásgyűjtemény egy OpenID/OAuth-alkalmazás konfigurálása

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>OpenID alkalmazás hozzáadása a katalógusból

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb](common/select-azuread.png))

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Válassza ki **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be az alkalmazás nevét. Válassza ki a kívánt alkalmazást az eredmény panelen, és iratkozzon fel az alkalmazáshoz.

    ![Az eredmények listájában Openid](common/search-new-app.png)

    > [!NOTE]
    > OpenID Connect és az OAuth-alkalmazások esetén a **Hozzáadás** gomb alapértelmezés szerint le van tiltva. Itt a bérlői rendszergazda kell választania a regisztráció gombra, és adja meg a hozzájárulás az alkalmazáshoz. Az alkalmazás ezután kerül az ügyfélbérlőn, ahol megteheti a konfigurációkat. Hiba esetén nem kell explicit módon hozzáadni az alkalmazást.

    ![Hozzáadás gomb](./media/openidoauth-tutorial/addbutton.png)

5. Ha az előfizetési hivatkozást választja, a program átirányítja a bejelentkezési hitelesítő adatokat az Azure Active Directory (Azure AD) oldal.

6. Sikeres hitelesítés után fogadja el a feltételeket a jóváhagyás lapon. Ezt követően az alkalmazás kezdőlapja jelenik meg.

    > [!NOTE]
    > Az alkalmazás csak egy példányát is hozzáadhat. Ha már felvett egy próbálta újra adja meg a feltételeket, nem kerül ismét a bérlőben. Így logikusan csak egy alkalmazáspéldány is használhatja a bérlőben.

## <a name="authentication-flow-using-openid-connect"></a>Hitelesítési folyamat OpenID Connect használatával

A legalapvetőbb bejelentkezési folyamata a következő lépéseket tartalmazza:

![Hitelesítési folyamat OpenID Connect használatával](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Több-bérlős alkalmazásban 
Egy több-bérlős alkalmazás használatra szánt szervezetekben, nem csak egyetlen szervezet. Ezek a független szoftverszállító (ISV-k) által írt általában-as-szoftverszolgáltatások (SaaS) alkalmazások. 

Több-bérlős alkalmazások szükség lesz minden könyvtárban, ahol fogja használni őket. Felhasználó vagy rendszergazda hozzájárulását, regisztrálja őket igényelnek. A jóváhagyási folyamat akkor kezdődik, amikor egy alkalmazás regisztrálva van a címtárban, és a Graph API vagy akár egy másik webes API-hoz való hozzáféréssel kap. Amikor az alkalmazás regisztrál egy felhasználó vagy egy másik szervezet rendszergazdájának, egy párbeszédpanel megjeleníti az engedélyeket, az alkalmazásnak. 

A felhasználó vagy a rendszergazda ezután engedélyezhetik, hogy az alkalmazás. A jóváhagyás az alkalmazás hozzáférést biztosít a megadott adatokat, és végül regisztrálja az alkalmazást a címtárban.

> [!NOTE]
> Ha az alkalmazás elérhető a felhasználók számára több címtár, határozza meg, melyik bérlőhöz egy olyan mechanizmust kell azokat az Ön által használt. Csak egy egybérlős alkalmazást kell keresse meg a felhasználó a saját címtárban. Egy több-bérlős alkalmazásban kell azonosítani egy adott felhasználó összes címtárakból az Azure ad-ben.
> 
> Ennek a feladatnak, az Azure AD biztosít egy közös hitelesítési végpont, ahol minden olyan több-bérlős alkalmazás irányíthatók a bejelentkezési kérelmek, a bérlő-specifikus végpont helyett. Ez a végpont [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) az Azure ad-ben minden címtár esetében. Lehet, hogy egy bérlő-specifikus végpont [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> A közös végpontot fontos figyelembe kell venni az alkalmazást fejleszt. Bejelentkezési, kijelentkezési, és a jogkivonat érvényesítése során több bérlő kezeléséhez szükséges logikát kell.

Alapértelmezés szerint az Azure ad-ben elősegíti a több-bérlős alkalmazásokban. Egyszerűen szervezetek között már elérhető, és azok könnyen használható után elfogadja a feltételeket.

## <a name="consent-framework"></a>Jóváhagyási keretrendszer

Az Azure ad-ben hozzájárulási keretrendszer használatával több-bérlős webes és natív ügyfélalkalmazások fejlesztéséhez. Ezek az alkalmazások engedélyezheti a bejelentkezési felhasználói fiókokat az Azure AD-bérlővel, ahol az alkalmazás regisztrálva van-e eltérő. Webes API-k eléréséhez például is szükséges lehet, hogy:
- A Microsoft Graph API, az Azure ad-ben, Intune-ban, és az Office 365-ben. 
- Egyéb Microsoft-szolgáltatás API-k.
- A saját webes API-kat. 

A keretrendszer alapul egy felhasználó vagy rendszergazda engedélyezi hogy egy alkalmazás, amely rákérdez, a címtárban kell regisztrálni. A regisztráció is járhat, directory-adatok elérése. Jóváhagyás után, az ügyfélalkalmazás is a Microsoft Graph API meghívása a felhasználó nevében, és szükség szerint használja.

A [Microsoft Graph API](https://developer.microsoft.com/graph/) adatokhoz hozzáférést biztosít az Office 365, például:

- Naptárak és üzenetek az Exchange-ből.
- A helyek és a Sharepointból listák.
- A dokumentumokat, a onedrive-ról.
- Notebookok a OneNote-ban.
- Planner műveletekhez.
- Az Excelből munkafüzetek.

A Graph API is hozzáférést biztosít a felhasználók és csoportok Azure AD-ből és más objektumok további Microsoft cloud servicesből.

A következő lépések bemutatják, hogyan a a jóváhagyási működik az alkalmazás fejlesztői és felhasználói élményt:

1. Fel, hogy egy ügyfél webalkalmazást, amely egy erőforrás vagy az API eléréséhez adott engedélyek kéréséhez szükséges. Az Azure portal segítségével alkalmazásengedély-kérelmeket deklarálja a konfiguráláskor. Egyéb olyan konfigurációs beállításoknak, például az alkalmazás Azure AD-regisztrációs részét képezik azok:

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Vegye figyelembe, hogy az alkalmazás engedélyeinek frissítése megtörtént. Az alkalmazás fut, és a egy felhasználó arra készül, hogy első alkalommal használja. Először az alkalmazás-hozzáférési kód lekérése az Azure AD-ből / authorize végponton kell. Az engedélyezési kód majd egy új hozzáférési beszerezni, és a jogkivonat frissítésére használható.

3. Ha a felhasználó már nem hitelesített, az Azure AD / authorize végpont kér be.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Után a felhasználó jelentkezett be, az Azure AD határozza meg, ha a felhasználónak megjelenítendő egy hozzájárulást kérő lap. Ez a döntés e a felhasználó (vagy a szervezet rendszergazdája) már megadta az alkalmazás jóváhagyásának alapul.

   Jóváhagyás nem kapott, ha az Azure AD felkéri a felhasználót a hozzájárulásra, és megjeleníti a működéséhez szükséges engedélyeket. Az engedélyeket, amelyek megjelennek a hozzájárulási párbeszédpanel a delegált engedélyeket az Azure Portalon a kijelölt megegyeznek.

    ![Hozzájárulást kérő lap](./media/openidoauth-tutorial/consentpage.png)

Az átlagos felhasználók maguk is jóváhagyhatják az néhány engedélyt. Egyéb engedélyek egy bérlői rendszergazdai jóváhagyás szükséges.

## <a name="difference-between-admin-consent-and-user-consent"></a>Rendszergazdai jóváhagyás és felhasználói beleegyezés közötti különbség

A rendszergazdák is is beleegyezik az összes felhasználó nevében egy alkalmazás delegált engedélyeit a bérlőben. Rendszergazdai jóváhagyás megakadályozza, hogy a hozzájárulási párbeszédpanel a bérlő összes felhasználója számára jelenik meg. A rendszergazdai szerepkörrel rendelkező felhasználók számára biztosíthat jóváhagyás az Azure Portalon. Az a **beállítások** az alkalmazást, válassza a lap **szükséges engedélyek** > **engedélyek megadása**.

![Engedélyek megadása gombra](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Explicit hozzájárulás használatával a **engedélyek megadása** gomb már ADAL.js használó egyoldalas alkalmazások (gyógyfürdők) szükséges. Ellenkező esetben a kérelem sikertelen lesz, amikor a hozzáférési jogkivonatot kér.

Csak az alkalmazásra vonatkozó engedélyeket mindig szükség van egy bérlői rendszergazdai jóváhagyást. Ha az alkalmazás egy csak az alkalmazásra vonatkozó engedélyt kér, és a egy felhasználó megpróbál bejelentkezni az alkalmazásba, hibaüzenet jelenik meg. Az üzenet azt jelzi, hogy a felhasználó nem tudja, hogy engedélyt adjanak.

Az alkalmazás használja az engedélyeket, amelyek rendszergazdai jóváhagyás megkövetelése, ha szüksége van egy kézmozdulatot, például egy gomb vagy hivatkozás, a rendszergazda a művelet elindításához. A kérelemhez, amely az alkalmazás által a művelet a szokásos OAuth2/OpenID Connect hitelesítési kérelem. A kérelem tartalmazza a *kérdezzen rá = admin_consent* lekérdezési karakterlánc paraméter. 

Miután a rendszergazda hozzájárult, és az egyszerű szolgáltatás létrehozása az ügyfél-bérlőben, újabb bejelentkezési kérelmek nem kell a *kérdezzen rá = admin_consent* paraméter. A rendszergazda úgy döntött, hogy a kért engedélyeket is, mert nem a bérlő többi felhasználójával a rendszer ettől kezdve beleegyezését kéri.

Bérlői rendszergazda letilthatja a normál felhasználók, hogy engedélyt adjanak az alkalmazások. Ez a funkció le van tiltva, ha rendszergazdai jóváhagyás mindig szükség az alkalmazás a bérlő által használandó. Ha szeretné a végfelhasználói jóváhagyási le van tiltva az alkalmazás teszteléséhez, Észreveheti, hogy a konfigurációs kapcsoló a [az Azure portal](https://portal.azure.com/). Van a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) szakaszba **vállalati alkalmazások**.

A *kérdezzen rá = admin_consent* paraméter is használhatják az alkalmazásokat, amelyek rendszergazdai jóváhagyás nem igénylő engedélyek kéréséhez. Ilyen például, egy környezetet, ahol a bérlői rendszergazda "regisztrál" egy időt, és nincs más a rendszer kéri a hozzájárulási ettől a igénylő alkalmazás.

Tegyük fel, hogy egy alkalmazás rendszergazdai jóváhagyás szükséges, és anélkül bejelentkezik egy rendszergazda a *kérdezzen rá = admin_consent* elküldött paraméter. Ha a rendszergazda sikeresen hozzájárul az alkalmazáshoz, csak a felhasználói fiókjuk vonatkozik. Normál felhasználók még mindig nem tudja a bejelentkezéshez vagy a hozzájárulás az alkalmazáshoz. Ez a funkció akkor hasznos, ha szeretne adni a bérlői rendszergazda lehetővé teszi az alkalmazás más felhasználói hozzáférés engedélyezése előtt ismerje meg.
