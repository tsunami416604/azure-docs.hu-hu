---
title: Alkalmazás-regisztráció létrehozása
titleSuffix: Azure Digital Twins
description: 'Lásd: Azure AD-alkalmazás regisztrációjának létrehozása hitelesítési lehetőségként ügyfélalkalmazások számára.'
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209770"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Alkalmazás-regisztráció létrehozása az Azure Digital Twins használatával

Az Azure Digital Twins-példányok használatakor gyakori, hogy az ügyfélalkalmazások, például egy egyéni ügyfélalkalmazás vagy egy például a [ADT Explorer](quickstart-adt-explorer.md)alkalmazással kommunikálnak az adott példánnyal. Ezeknek az alkalmazásoknak hitelesíteniük kell az Azure Digital Twins szolgáltatással, hogy együttműködjön vele, és az alkalmazások által használható [hitelesítési mechanizmusok](how-to-authenticate-client.md) némelyike egy [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) **alkalmazás regisztrálását**vonja maga után.

Ez az összes hitelesítési forgatókönyv esetében nem szükséges. Ha azonban olyan hitelesítési stratégiát vagy kódrészletet használ, amely az alkalmazás regisztrálását igényli, beleértve az ügyfél- **azonosítót** és a **bérlő azonosítóját**is, ez a cikk bemutatja, hogyan állíthat be egyet.

## <a name="using-azure-ad-app-registrations"></a>Az Azure AD-alkalmazások regisztrációjának használata

[Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Az alkalmazások Azure AD-beli **regisztrációjának** beállítása az egyik módja, ha az ügyfélalkalmazások hozzáférést biztosítanak az Azure digitális Twins szolgáltatáshoz.

Az alkalmazás regisztrálása az [Azure Digital Twins API](how-to-use-apis-sdks.md)-khoz való hozzáférési engedélyek konfigurálására szolgál. Később az ügyfélalkalmazások a regisztráció **ügyfelének és a bérlői azonosító értékének**használatával hitelesíthetők az alkalmazás regisztrálásával, így a konfigurált hozzáférési engedélyek is megadhatók az API-khoz.

>[!TIP]
> Érdemes lehet új alkalmazás-regisztrációt beállítani minden alkalommal, amikor csak egyszer kell megtennie, *vagy* ezt csak egyszer kell megadnia.

## <a name="create-the-registration"></a>A regisztráció létrehozása

Először navigáljon a [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a Azure Portalban (ezt a hivatkozást használhatja, vagy megkeresheti a portálon található keresősáv használatával). Válassza a szolgáltatás menü *Alkalmazásregisztrációk* elemét, majd az *+ új regisztráció*lehetőséget.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

A következő *alkalmazás regisztrálása* lapon adja meg a kért értékeket:
* **Név**: a regisztrációhoz TÁRSÍTANDÓ Azure ad-alkalmazás megjelenítendő neve
* **Támogatott fióktípus**: *csak az ebben a szervezeti könyvtárban lévő fiókok kijelölése (csak alapértelmezett címtár – egyetlen bérlő)*
* **Átirányítási URI**: Azure *ad-alkalmazás válaszának URL-címe* az Azure ad-alkalmazáshoz. Adjon hozzá egy *nyilvános ügyfél/natív (mobile & Desktop) URI-* t a következőhöz: `http://localhost` .

Ha elkészült, kattintson a *regisztráció* gombra.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

A regisztráció befejezését követően a portál átirányítja a részleteket tartalmazó lapjára.

## <a name="collect-client-id-and-tenant-id"></a>Ügyfél-azonosító és bérlői azonosító gyűjtése

Ezután gyűjtsön néhány fontos értéket az alkalmazás regisztrációjának részletei lapról:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

Jegyezze **fel a lapon** megjelenő _**alkalmazás (ügyfél) azonosítóját**_ és _**KÖNYVTÁRát (bérlői azonosítóját)**_ . Ezek az értékek, amelyeknek az ügyfélalkalmazás ezt a regisztrációt kell használnia az Azure digitális ikrekkel való hitelesítéshez.

## <a name="provide-azure-digital-twins-api-permission"></a>Azure digitális Twins API-engedély megadása

Ezután konfigurálja a létrehozott alkalmazás-regisztrációt az Azure Digital Twins API-k alapkonfigurációs engedélyeivel.

Az alkalmazás regisztrációjának portál lapján válassza az API- *engedélyek* lehetőséget a menüből. A következő engedélyek lapon kattintson a *+ engedély hozzáadása* gombra.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

Az alábbi *API-engedélyek kérése* lapon váltson a *saját szervezet által használt API* -k lapra, és keressen rá az *Azure Digital Twins*kifejezésre. Válassza az _**Azure Digital Twins**_ lehetőséget a keresési eredmények közül, és folytassa az engedélyek hozzárendelését az Azure Digital Twins API-khoz.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

>[!NOTE]
> Ha az előfizetése továbbra is rendelkezik egy meglévő Azure Digital Twins-példánnyal a szolgáltatás előző nyilvános előzetes verziójából (2020. július előtt), akkor helyette az _**Azure Smart Spaces szolgáltatást**_ kell keresnie és kiválasztania. Ez az API-k ugyanazon készletének régebbi neve (figyelje meg, hogy az *alkalmazás (ügyfél) azonosítója* megegyezik a fenti képernyőképen szereplővel), és a felhasználói élmény ezen a lépésen túl nem változik.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

Ezután kiválaszthatja, hogy mely engedélyeket kell megadni ezekhez az API-khoz. Bontsa ki az **olvasási (1)** engedélyt, és jelölje be az *olvasás. írás* az alkalmazás regisztrációs olvasójának és az író engedélyének megadására szolgáló jelölőnégyzetet.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

Ha elkészült, kattintson a *hozzáadási engedélyek* elemre.

### <a name="verify-success"></a>Sikeres ellenőrzés

Az *API-engedélyek* lapon ellenőrizze, hogy már van-e olyan bejegyzés az Azure digitális Twins számára, amely az olvasási/írási jogosultságokat tükrözi:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

Azt is ellenőrizheti, hogy az Azure Digital Twins-hoz kapcsolódott-e az alkalmazás regisztrációjának *manifest.jsján*, amely automatikusan frissült az Azure digitális Twins információi között az API-engedélyek hozzáadásakor.

Ehhez válassza ki a *jegyzékfájlt* a menüből az alkalmazás regisztrációs jegyzékfájljának megtekintéséhez. Görgessen a Code (kód) ablak aljára, és keresse meg a következő mezőket `requiredResourceAccess` . Az értékeknek meg kell egyezniük az alábbi képernyőképen láthatókkal:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

Ha ezek az értékek hiányoznak, próbálkozzon újra az [API-engedély hozzáadására szolgáló szakaszban](#provide-azure-digital-twins-api-permission)leírt lépésekkel.

## <a name="other-possible-steps-for-your-organization"></a>A szervezet egyéb lehetséges lépései

Lehetséges, hogy a szervezete további műveleteket igényel az előfizetés tulajdonosai/rendszergazdái számára az alkalmazások regisztrálásának sikeres beállítása érdekében. A szükséges lépések a szervezete által megadott beállításoktól függően változhatnak.

Íme néhány gyakori lehetséges tevékenység, amelyet az előfizetéshez tartozó tulajdonosnak/rendszergazdának kell elvégeznie. Ezek és egyéb műveletek a Azure Portal [*Azure ad alkalmazás regisztrációk*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) oldaláról is elvégezhetők.
* Adja meg a rendszergazdai jóváhagyást az alkalmazás regisztrálásához. Előfordulhat, hogy a szervezete *rendszergazdai* jogosultságokkal rendelkezik, globálisan be van kapcsolva az Azure ad-ben az előfizetésben lévő összes alkalmazás regisztrálásához. Ha igen, a tulajdonosnak/rendszergazdának be kell jelölnie ezt a gombot a vállalat számára az alkalmazás regisztrációjának *API-engedélyei* lapján, hogy az alkalmazás regisztrációja érvényes legyen:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::
  - Ha a beleegyezikés sikeresen megtörtént, az Azure Digital Twins bejegyzésében szerepelnie kell a _ **(vállalata)** számára engedélyezett_ *állapot* értékének.
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::
* Nyilvános ügyfél-hozzáférés aktiválása
* A webes és asztali hozzáférés adott válasz URL-címeinek beállítása
* Implicit OAuth2 hitelesítési folyamatok engedélyezése

Az alkalmazások regisztrálásával és a különböző telepítési lehetőségekkel kapcsolatos további információkért lásd: [*alkalmazás regisztrálása a Microsoft Identity platformon*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Azure AD-alkalmazás regisztrációját állítja be, amely az ügyfélalkalmazások Azure digitális Twins API-kkal való hitelesítésére használható.

Ezután olvassa el a hitelesítési mechanizmusokkal kapcsolatos tudnivalókat, például az alkalmazások regisztrációját használó alkalmazásokat, valamint azokat, amelyek nem:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)