---
title: 'Gyors útmutató: alkalmazás konfigurálása webes API-k megjelenítéséhez | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhat egy alkalmazást úgy, hogy az alkalmazás elérhetővé tegye az ügyfélalkalmazások számára az új jogosultságot, hatókört és szerepkört.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830291"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Gyors útmutató: alkalmazás konfigurálása webes API-k megjelenítéséhez

Fejleszthet saját webes API-kat,és [engedélyek/hatókörök](developer-glossary.md#scopes) és [szerepkörök](developer-glossary.md#roles) közzétételével elérhetővé teheti azokat az ügyfélalkalmazások számára. A megfelelően konfigurált webes API-k a Microsoft többi webes API-jához hasonlóan érhetők el, mint például a Graph API vagy az Office 365 API-k.

Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhat egy alkalmazást egy új hatókör megjelenítéséhez, hogy elérhető legyen az ügyfélalkalmazások számára.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A rövid útmutató befejezése [: az alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Bejelentkezés az Azure Portalra és az alkalmazás kiválasztása

Mielőtt konfigurálhatná az alkalmazást, végre kell hajtania az alábbi lépéseket:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kijelölte az alkalmazást, az alkalmazás **Áttekintés** lapja vagy regisztrációs főoldala jelenik meg.
1. Válassza ki az új hatókör közzétételéhez használni kívánt módszert (felhasználói felület vagy alkalmazásjegyzék):
    * [Új hatókör közzététele a felhasználói felületen](#expose-a-new-scope-through-the-ui)
    * [Új hatókör vagy szerepkör közzététele az alkalmazásjegyzékben](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Új hatókör közzététele a felhasználói felületen

[![Bemutatja, hogyan tehet elérhetővé egy API-t a felhasználói felületen](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Ha a felhasználói felületen kíván közzétenni új hatókört:

1. Az alkalmazás **Áttekintés** lapján válassza az **API közzététele** szakaszt.

1. Válassza a **Hatókör hozzáadása** lehetőséget.

1. Ha nem állított be **Alkalmazásazonosító URI-t**, erre vonatkozó kérés jelenik meg. Adja meg alkalmazásazonosító URI-ját, vagy használja a megadottat, majd válassza a **Mentés és folytatás** lehetőséget.

1. A megjelenő **Hatókör hozzáadása** lapon az alábbiak szerint adja meg a hatókör adatait:

    | Mező | Leírás |
    |-------|-------------|
    | **Hatókör neve** | Adjon kifejező nevet a hatókörnek.<br><br>Például: `Employees.Read.All`. |
    | **Ki adhat hozzájárulást?** | Válassza ki, hogy ehhez a hatókörhöz hozzájárulhatnak a felhasználók, vagy rendszergazdai jóváhagyásra van szükség. Magasabb jogosultsági szintű engedélyeknél válassza a **Csak rendszergazdák** lehetőséget. |
    | **Rendszergazdai hozzájárulás megjelenítendő neve** | Adjon meg kifejező leírást a hatókörhöz a rendszergazdák számára.<br><br>Például: `Read-only access to Employee records` |
    | **Rendszergazdai jóváhagyás leírása** | Adjon meg kifejező leírást a hatókörhöz a rendszergazdák számára.<br><br>Például: `Allow the application to have read-only access to all Employee data.` |

    Ha a felhasználók jóváhagyhatják a hatókört, akkor az alábbi mezőket is töltse ki:

    | Mező | Leírás |
    |-------|-------------|
    | **Felhasználói jóváhagyás megjelenítendő neve** | Adjon kifejező nevet a hatókörnek a felhasználók számára.<br><br>Például: `Read-only access to your Employee records` |
    | **Felhasználói jóváhagyás leírása** | Adjon meg kifejező leírást a hatókörhöz a felhasználók számára.<br><br>Például: `Allow the application to have read-only access to your Employee data.` |

1. Állítsa be az **Állapotot**, és ha kész, válassza a **Hatókör hozzáadása** lehetőséget.

1. Választható Ha szeretné letiltani, hogy az alkalmazás felhasználói beleférjenek a megadott hatókörökbe, az ügyfélalkalmazás számára engedélyezheti a webes API-hoz való hozzáférést. Előzetesen engedélyeznie kell *csak* azokat az ügyfélalkalmazások, amelyeket megbízhatónak tart, mivel a felhasználók nem fogják tudni visszautasítani a hozzájárulásukat.
    1. Az **engedélyes ügyfélalkalmazások**területen válassza **az ügyfélalkalmazás hozzáadása** elemet.
    1. Adja meg az előre engedélyezni kívánt ügyfélalkalmazás **alkalmazás-(ügyfél-) azonosítóját** . Például egy korábban regisztrált webalkalmazáshoz.
    1. Az **engedéllyel rendelkező hatókörök**területen válassza ki azokat a hatóköröket, amelyekhez meg kívánja szüntetni az engedély megadását, majd válassza az **alkalmazás hozzáadása**lehetőséget.

    Az ügyfélalkalmazás mostantól egy előre felhatalmazott ügyfélalkalmazás (PEM), és a felhasználóknak nem kell beleegyezniük a bejelentkezéshez.

1. A megadott lépéseket követve [ellenőrizze, hogy a webes API közzététele sikerült-e a többi alkalmazás számára](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Új hatókör vagy szerepkör közzététele az alkalmazásjegyzékben

Az alkalmazás jegyzékfájlja az alkalmazás entitásának frissítésére szolgál, amely meghatározza az Azure AD-alkalmazások regisztrációjának attribútumait.

[![Új hatókör közzététele a oauth2Permissions-gyűjtemény használatával a jegyzékfájlban](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Új hatókör közzététele az alkalmazás jegyzékfájljának szerkesztésével:

1. Az alkalmazás **Áttekintés** lapján válassza az **Alkalmazásjegyzék** szakaszt. Megnyílik egy webalapú jegyzékfájlszerkesztő, amellyel a portálon **szerkesztheti** a jegyzékfájlt. Másik lehetőségként a **Letöltés** lehetőséget választva a helyi gépen is szerkesztheti az alkalmazásjegyzéket, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra.

    Ebben a példában bemutatjuk, hogyan teheti közzé az `Employees.Read.All` nevű új hatókört az erőforrásban/API-ban az alábbi JSON-elem `oauth2Permissions` gyűjteménybe való felvételével.

    Az `id` értéket programozott módon vagy GUID generálási eszközzel (például [Guidgen](https://www.microsoft.com/download/details.aspx?id=55984)) generálhatja.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. Amikor végzett, kattintson a **Mentés** gombra. A webes API most konfigurálva lett a könyvtárban lévő egyéb alkalmazások általi használatra.
1. A megadott lépéseket követve [ellenőrizze, hogy a webes API közzététele sikerült-e a többi alkalmazás számára](#verify-the-web-api-is-exposed-to-other-applications).

Az alkalmazás entitásával és sémájával kapcsolatos további információkért tekintse meg Microsoft Graph [alkalmazási][ms-graph-application] erőforrástípus referenciájának dokumentációját.

További információ az alkalmazás-jegyzékfájlról, beleértve a séma-referenciáját: [Az Azure ad-alkalmazás jegyzékfájljának ismertetése](reference-app-manifest.md).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>A webes API többi alkalmazás számára való közzétételének ellenőrzése

1. Térjen vissza az Azure AD-bérlőhöz, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg és válassza ki a konfigurálni kívánt ügyfélalkalmazás.
1. Hajtsa végre ismét az [Ügyfélalkalmazás konfigurálása webes API-k elérésére](quickstart-configure-app-access-web-apis.md) című cikkben leírt lépéseket.
1. Ha [egy API kiválasztásának](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)lépését választja, válassza ki az erőforrást (a webes API-alkalmazás regisztrációja).
    * Ha a Azure Portal használatával hozta létre a webes API-alkalmazás regisztrációját, az API-erőforrás a **saját API** -k lapon jelenik meg.
    * Ha lehetővé tette, hogy a Visual Studio létrehozza a webes API-alkalmazás regisztrációját a projekt létrehozása során, az API-erőforrás megjelenik a **saját szervezet által használt API** -k lapon.

Miután kiválasztotta a webes API-erőforrást, az ügyfél-hozzáférési kérelmekhez elérhető új hatókört kell látnia.

## <a name="using-the-exposed-scopes"></a>A feltehetően elérhető hatókörök használata

Miután az ügyfél megfelelően konfigurálva van a webes API eléréséhez szükséges engedélyekkel, OAuth 2,0 hozzáférési jogkivonatot adhat ki az Azure AD-ben. Amikor az ügyfél meghívja a webes API-t, megjeleníti azt a hozzáférési jogkivonatot, amelynek a hatóköre ( `scp` ) jogcím az alkalmazás regisztrálásakor kért engedélyekre van beállítva.

A későbbiekben igény szerint további hatóköröket is közzétehet. Vegye figyelembe, hogy a webes API több hatókört is közzétehet, amelyek különféle függvényekkel vannak társítva. Az erőforrás a futásidőben a kapott OAuth 2.0 hozzáférési jogkivonatban lévő hatókör (`scp`) jogcímének vagy jogcímeinek értékelésével szabályozhatja a hozzáférést a webes API-hoz.

Az alkalmazásokban a teljes hatókör érték a webes API **ALKALMAZÁSSPECIFIKUS azonosítójának URI-ja** (az erőforrás) és a **hatókör neve**összefűzése.

Ha például a webes API alkalmazásspecifikus AZONOSÍTÓjának URI-ja, `https://contoso.com/api` és a hatókör neve `Employees.Read.All` , a teljes hatókör a következő:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Következő lépések

Most, hogy kitette a webes API-t a hatókörök konfigurálásával, konfigurálja az ügyfélalkalmazás regisztrációját, és engedélyezze a hatókörök elérését.

> [!div class="nextstepaction"]
> [Alkalmazás-regisztráció konfigurálása webes API-hozzáféréshez](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
