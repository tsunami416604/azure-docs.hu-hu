---
title: 'Gyors útmutató: alkalmazás regisztrálása a Microsoft Identity platformon | Azure'
description: Ebből a rövid útmutatóból megtudhatja, hogyan regisztrálhat egy alkalmazást a Microsoft Identity platformon.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 9cd59d6bf5b9bf6e17cba0786bfac27ed12d7638
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91258132"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Gyors útmutató: alkalmazás regisztrálása a Microsoft Identity platformmal

Ebben a rövid útmutatóban egy alkalmazást regisztrál a Azure Portal, így a Microsoft Identity platform hitelesítési és engedélyezési szolgáltatásokat biztosíthat az alkalmazáshoz és a felhasználóihoz.

Minden egyes alkalmazásnak regisztrálnia kell a Microsoft Identity platformot az identitás-és hozzáférés-kezelés (IAM) elvégzéséhez. Függetlenül attól, hogy egy ügyfélalkalmazás, például web vagy Mobile alkalmazás, vagy egy olyan webes API, amely egy ügyfélalkalmazás biztonsági mentését támogatja, regisztrálja az alkalmazás és az identitás-szolgáltató, a Microsoft Identity platform közötti megbízhatósági kapcsolatot.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók – [ingyenes fiók létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A rövid útmutató befejezése [: bérlő beállítása](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Az alkalmazás regisztrálása megbízhatósági kapcsolatot létesít az alkalmazás és a Microsoft Identity platform között. A bizalmi kapcsolat egyirányú: az alkalmazás megbízhatónak tartja a Microsoft Identity platformot, nem pedig fordítva.

Az alkalmazás regisztrációjának létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/quickstart-register-app/portal-01-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés**területen válassza a **Alkalmazásregisztrációk**, majd az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. Itt adhatja meg, hogy ki használhatja az alkalmazást, más néven a *bejelentkezési célközönségnek*.

    | Támogatott fióktípusok | Leírás |
    |-------------------------|-------------|
    | **Csak az ebben a szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha olyan alkalmazást hoz létre, amelyet csak felhasználók (vagy vendégek) használhatnak *a* bérlőben.<br><br>Gyakran *üzletági (LOB* ) alkalmazásnak nevezik, ez egy **egybérlős** alkalmazás a Microsoft Identity platformon. |
    | **Tetszőleges szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha *bármely* Azure ad-bérlő felhasználói számára szeretné használni az alkalmazást. Ez a beállítás akkor lehet hasznos, ha például olyan szoftveres (SaaS) alkalmazást készít, amelyet több szervezet számára szeretne biztosítani.<br><br>Ezt nevezzük **több-bérlős** alkalmazásnak a Microsoft Identity platformon. |
    | **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha a lehető legszélesebb ügyfélkört szeretbé megcélozni.<br><br>Ha ezt a lehetőséget választja, egy **több-bérlős** alkalmazást regisztrál, amely támogatja a személyes **Microsoft-fiókokkal** (MSA) rendelkező felhasználókat is. |
    | **Személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha olyan alkalmazást hoz létre, amelyet csak személyes Microsoft-fiókkal rendelkező felhasználók használhatnak. A személyes Microsoft-fiókok közé tartoznak a Skype, az Xbox, az élő és a Hotmail-fiókok. |

1. Ne adja meg az **átirányítási URI-t (nem kötelező)**, majd a következő szakaszban konfiguráljon egyet.
1. A kezdeti alkalmazás regisztrációjának befejezéséhez válassza a **regisztráció** lehetőséget.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Képernyőkép a Azure Portalról egy webböngészőben, amely az alkalmazás regisztrálása ablaktáblát jeleníti meg.":::

Ha a regisztráció befejeződik, a Azure Portal megjeleníti az alkalmazás regisztrációjának **Áttekintés** paneljét, amely tartalmazza az **alkalmazás (ügyfél) azonosítóját**. Más néven az ügyfél- *azonosító*is, ez az érték egyedileg azonosítja az alkalmazást a Microsoft Identity platformon.

Az alkalmazás kódja, vagy általában az alkalmazásban használt hitelesítési tár is az ügyfél-azonosítót használja az identitási platformtól kapott biztonsági jogkivonatok érvényesítéséhez.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Képernyőkép a Azure Portalról egy webböngészőben, amely az alkalmazás regisztrálása ablaktáblát jeleníti meg.":::

## <a name="add-a-redirect-uri"></a>Átirányítási URI hozzáadása

Az átirányítási URI az a hely, ahol a Microsoft Identity platform átirányítja a felhasználó ügyfelét, és a hitelesítés után biztonsági jogkivonatokat küld.

Egy éles webalkalmazásban például az átirányítási URI általában egy nyilvános végpont, amelyben az alkalmazás fut, például: `https://contoso.com/auth-response` . A fejlesztés során gyakori, hogy hozzáadja azt a végpontot is, amelyben az alkalmazást helyileg futtatja, például `https://127.0.0.1/auth-response` vagy `http://localhost/auth-response` .

A regisztrált alkalmazások átirányítási URI-k hozzáadásával és módosításával konfigurálhatja a [platform beállításait](#configure-platform-settings).

### <a name="configure-platform-settings"></a>A platform beállításainak konfigurálása

Az egyes alkalmazások, például az átirányítási URI-k beállításai a Azure Portal **platform konfigurációjában** konfigurálhatók. Egyes platformokon, például a **web** -és **egyoldalas alkalmazásokban**manuálisan kell megadnia egy átirányítási URI-t. Más platformokhoz, például a mobil-és asztali eszközökhöz a más beállítások konfigurálásakor a létrehozott átirányítási URI-k közül választhat.

Alkalmazásbeállítások konfigurálása a célzott platform vagy eszköz alapján:

1. Válassza ki az alkalmazást a Azure Portal **Alkalmazásregisztrációkban** .
1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
1. A **platform-konfigurációk**területen válassza **a platform hozzáadása**lehetőséget.
1. A **platformok konfigurálása**területen válassza ki az alkalmazás típusa (platform) csempéjét a beállítások konfigurálásához.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Képernyőkép a Azure Portalról egy webböngészőben, amely az alkalmazás regisztrálása ablaktáblát jeleníti meg." border="false":::

    | Platform | Konfigurációs beállítások |
    | -------- | ---------------------- |
    | **Web** | Adjon meg egy **átirányítási URI** -t az alkalmazáshoz, ahol a Microsoft Identity platform átirányítja a felhasználó ügyfelét, és biztonsági jogkivonatokat küld a hitelesítés után.<br/><br/>Válassza ezt a platformot a kiszolgálón futó szabványos webalkalmazásokhoz. |
    | **Egyoldalas alkalmazás** | Adjon meg egy **átirányítási URI** -t az alkalmazáshoz, ahol a Microsoft Identity platform átirányítja a felhasználó ügyfelét, és biztonsági jogkivonatokat küld a hitelesítés után.<br/><br/>Válassza ezt a platformot, ha ügyféloldali webalkalmazást hoz létre a JavaScriptben, vagy olyan keretrendszert, mint például a szögletes, Vue.js, React.js vagy a Blazer webassembly. |
    | **iOS/macOS** | Adja meg az XCode az *info. plist* fájlban vagy a Build-beállításokban található ALKALMAZÁSCSOMAG- **azonosítót**.<br/><br/>A rendszer létrehoz egy átirányítási URI-t a köteg-azonosító megadásakor. |
    | **Android** | Adja meg az **alkalmazáscsomag nevét**, amelyet megtalálhat a *AndroidManifest.xml* fájlban, majd létrehozhatja és megadhatja az **aláírási kivonatot**.<br/><br/>A rendszer létrehoz egy átirányítási URI-t, amikor megadja ezeket a beállításokat. |
    | **Mobil-és asztali alkalmazások** | Válasszon egy **javasolt átirányítási URI** -t, vagy adjon meg egy **Egyéni átirányítási URI**-t.<br/>Asztali alkalmazások esetén a következőket javasoljuk:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Válassza ezt a platformot olyan mobileszközök esetében, amelyek nem a legújabb Microsoft Authentication Library (MSAL) szolgáltatást használják, vagy nem használ közvetítőt. A platformot asztali alkalmazásokhoz is kiválaszthatja. |
1. Válassza a **Konfigurálás** lehetőséget a platform konfigurációjának befejezéséhez.

### <a name="redirect-uri-restrictions"></a>Átirányítási URI-korlátozások

Bizonyos korlátozások vonatkoznak az alkalmazások regisztrálásához hozzáadott átirányítási URI-k formátumára. Ezekről a korlátozásokról az [átirányítási URI (válasz URL-cím) korlátozásai és korlátozásai](reply-url.md)című témakörben olvashat bővebben.

## <a name="add-credentials"></a>Hitelesítő adatok hozzáadása

A hitelesítő adatokat a webes API-hoz hozzáférő bizalmas ügyfélalkalmazások használják. Ilyenek például a webalkalmazások, más webes API-k, valamint a szolgáltatás-és démon típusú alkalmazások. A hitelesítő adatok lehetővé teszik az alkalmazás számára, hogy önmagát hitelesítse magát, ami nem igényel interakciót a felhasználótól futásidőben.

A bizalmas ügyfélalkalmazás regisztrációjának hitelesítő adataiként is hozzáadhat tanúsítványokat és ügyfél-titkot (karakterláncot).

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Képernyőkép a Azure Portalról egy webböngészőben, amely az alkalmazás regisztrálása ablaktáblát jeleníti meg.":::

### <a name="add-a-certificate"></a>Tanúsítvány hozzáadása

Más néven *nyilvános kulcs*, a tanúsítványok az ajánlott hitelesítő adatok, mivel magasabb szintű megbízhatóságot biztosítanak, mint az ügyfél titkos kulcsa.

1. Válassza ki az alkalmazást a Azure Portal **Alkalmazásregisztrációkban** .
1. Válassza a **tanúsítványok & titkok**  >  **feltöltési tanúsítvány**elemet.
1. Válassza ki a feltölteni kívánt fájlt. A fájlnak a következő típusok egyikébe kell tartoznia: .cer, .pem vagy .crt.
1. Válassza a **Hozzáadás** lehetőséget.

### <a name="add-a-client-secret"></a>Ügyfél titkos kulcsának hozzáadása

Az ügyfél titkos kulcsa (más néven *alkalmazás jelszava*) egy olyan karakterlánc-érték, amelyet az alkalmazás a tanúsítvány helyett saját maga személyazonosságára használhat fel. Ez a két használandó hitelesítő típus egyszerűbb, és gyakran használatos a fejlesztés során, de kevésbé biztonságos, mint a tanúsítvány. Az éles környezetben futó alkalmazásokban tanúsítványokat kell használnia.

1. Válassza ki az alkalmazást a Azure Portal **Alkalmazásregisztrációkban** .
1. Válassza a **tanúsítványok & titkos kulcsok**  >   **új ügyfél titka**lehetőséget.
1. Adja meg titkos ügyfélkódja leírását.
1. Válassza ki az időtartamot.
1. Válassza a **Hozzáadás** lehetőséget.
1. **Jegyezze fel a titkos kulcs értékét** az ügyfélalkalmazás kódjában való használathoz – ez a lap *nem jelenik meg többé* , miután elhagyja ezt a lapot.

## <a name="next-steps"></a>További lépések

Az ügyfélalkalmazások általában a webes API-k erőforrásaihoz férnek hozzá. Az ügyfélalkalmazás Microsoft Identity platformmal való védelme mellett a platformon is engedélyezheti a hatókörön belüli, a webes API-hoz való hozzáféréshez szükséges engedélyeket.

A sorozat következő rövid útmutatójának használatával hozzon létre egy másik alkalmazást a webes API-hoz, és tegye elérhetővé a hatókörét.

> [!div class="nextstepaction"]
> [Alkalmazás konfigurálása webes API-k megjelenítéséhez](quickstart-configure-app-expose-web-apis.md)
