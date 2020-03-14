---
title: Az Azure API Management fejlesztői portál áttekintése
titleSuffix: Azure API Management
description: Ismerje meg API Management fejlesztői portálját.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: b6b11242831e68787fe225d4d0b66638f1388de6
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297985"
---
# <a name="azure-api-management-developer-portal-overview"></a>Az Azure API Management fejlesztői portál áttekintése

A fejlesztői portál egy automatikusan létrehozott, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza. Ahol az API-felhasználók felfedezhetik az API-kat, megtudhatják, hogyan használhatják őket, hozzáférést igényelhetnek, és kipróbálhatja őket.

Ez a cikk a API Management fejlesztői portál saját üzemeltetésű és felügyelt verziói közötti különbségeket ismerteti. Emellett ismerteti az architektúráját, és választ ad a gyakori kérdésekre.

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Felügyelt és saját üzemeltetésű verziók

A fejlesztői portált kétféleképpen is létrehozhatja:

- **Felügyelt verzió** – az API Management-példányba épített portál szerkesztésével és testreszabásával, és az URL-`<your-api-management-instance-name>.developer.azure-api.net`keresztül érhető el. A [jelen dokumentációs cikkből](api-management-howto-developer-portal-customize.md) megtudhatja, hogyan érheti el és szabhatja testre a felügyelt portált.
- **Saját** üzemeltetésű verzió – a portál API Management-példányon kívüli üzembe helyezésével és önálló üzemeltetésével. Ezzel a módszerrel szerkesztheti a portált, és kiterjesztheti a megadott alapfunkciókat – például egyéni widgeteket valósíthat meg a külső rendszerekkel való integrációhoz. Ebben az esetben Ön a portál karbantartója, és Ön felelős a portál legújabb verzióra való frissítéséhez. A részletekért és az utasításokért tekintse meg a [GitHub-tárházat a portál forráskódját][1] és [a widget megvalósítását ismertető oktatóanyagot][3]. A [felügyelt verzióhoz tartozó oktatóanyag](api-management-howto-developer-portal-customize.md) végigvezeti a portál felügyeleti paneljén, amely a felügyelt és a saját üzemeltetésű verziók esetében gyakori.

## <a name="portal-architectural-concepts"></a>Portál építészeti fogalmak

A portál összetevői logikailag két kategóriába sorolhatók: *kód* és *tartalom*.

A *kód* [a GitHub-tárházban][1] van fenntartva, és a következőket tartalmazza:

- Widgetek – amelyek vizuális elemeket és HTML-, JavaScript-, formázási képességeket, beállításokat és tartalom-hozzárendelést képviselnek. Ilyenek például a képek, a szöveges bekezdések, az űrlapok, az API-k listája stb.
- Formázási definíciók – a widgetek stílusának meghatározása
- Motor – amely statikus weblapokat hoz létre a portál tartalmából, és JavaScript nyelven íródott
- Vizuális szerkesztő – lehetővé teszi a böngészőn belüli testreszabást és a szerzői műveletek elvégzését

A *tartalom* két alkategóriára oszlik: a *portál tartalma* és a *API Management tartalma*.

A *portál tartalma* a portálra vonatkozik, és a következőket tartalmazza:

- Lapok – például Kezdőlap, API-oktatóanyagok, blogbejegyzések
- Média – képek, animációk és egyéb fájl alapú tartalmak
- Elrendezések – sablonok, amelyek megfelelnek egy URL-címnek, és megadják a lapok megjelenítésének módját
- Stílusok – a formázási definíciók értékei, például betűtípusok, színek, szegélyek
- Beállítások – konfiguráció, például favicon, webhely metaadatainak

A *portál tartalma*, a média kivételével, JSON-dokumentumként van megadva.

*API Management tartalom* olyan entitásokat tartalmaz, mint az API-k, a műveletek, a termékek és az előfizetések.

A portál a [Paperbits-keretrendszer](https://paperbits.io/)adaptált villáján alapul. Az eredeti Paperbits funkció ki lett bővítve API Management-specifikus widgetek (például az API-k listája, a termékek listája) és egy összekötő API Management szolgáltatás számára a tartalom mentéséhez és lekéréséhez.

## <a name="faq"></a>Gyakori kérdések

Ebben a szakaszban a fejlesztői portál általános jellegű kérdéseit választjuk. A saját üzemeltetésű verzióra vonatkozó kérdésekért tekintse meg a [GitHub-adattár wiki szakaszát](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> hogyan telepíthetek át a portál előzetes verziójáról?

A fejlesztői portál előzetes verziójának használatával kiépített egy előnézeti tartalmat a API Management szolgáltatásban. Az alapértelmezett tartalom jelentősen módosult az általánosan elérhető verzióban a jobb felhasználói élmény érdekében. Emellett új widgeteket is tartalmaz.

Ha felügyelt verziót használ, állítsa alaphelyzetbe a portál tartalmát az **Operations** ( **tartalom visszaállítása** ) lehetőségre kattintva. A művelet megerősítése eltávolítja a portál összes tartalmát, és kiépíti az új alapértelmezett tartalmat. A portál motorja automatikusan frissült a API Management szolgáltatásban.

![Portál tartalmának visszaállítása](media/api-management-howto-developer-portal/reset-content.png)

Ha a saját üzemeltetésű verziót használja, a GitHub-tárház `scripts/cleanup.bat` és `scripts/generate.bat` használatával távolítsa el a meglévő tartalmakat, és hozzon létre új tartalmakat. Győződjön meg róla, hogy a legújabb kiadásra frissíti a portál kódját a GitHub-adattárból.

Ha nem szeretné alaphelyzetbe állítani a portál tartalmát, érdemes lehet az újonnan elérhető widgeteket használni az összes oldalon. A meglévő widgetek automatikusan frissültek a legújabb verzióra.

Ha a portál az általános elérhetőségi bejelentés után lett kiépítve, akkor már az új alapértelmezett tartalmat is be kell építenie. Az oldaláról nincs szükség beavatkozásra.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Hogyan telepíthetek át a régi fejlesztői portálról a fejlesztői portálra?

A portálok nem kompatibilisek, és manuálisan kell áttelepíteni a tartalmat.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>A portál rendelkezik a régi portál összes szolgáltatásával?

A fejlesztői portál már nem támogatja az *alkalmazásokat* és a *problémákat*.

Az interaktív Fejlesztői konzolon az OAuth-mel való hitelesítés még nem támogatott. Az előrehaladás nyomon követhető [a GitHub-probléma](https://github.com/Azure/api-management-developer-portal/issues/208)használatával.

### <a name="has-the-old-portal-been-deprecated"></a>Elavult a régi portál?

A régi fejlesztői és közzétevői portálok már *örökölt* funkciók – csak a biztonsági frissítéseket kapják meg. Az új szolgáltatások csak az új fejlesztői portálon lesznek implementálva.

Az örökölt portálok elavulása külön lesz bejelentve. Ha kérdése, problémája vagy megjegyzése van, emelje fel őket [egy dedikált GitHub-problémával](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A portálon nem támogatottak a szükséges funkciók

Megnyithat egy [szolgáltatási kérelmet](https://aka.ms/apimwish) , vagy [saját maga is megvalósíthatja a hiányzó funkciókat][3]. Ha saját maga hajtja végre a funkciót, a fejlesztői portált saját maga is futtathatja, vagy megnyithatja a GitHubon a lekéréses kérelmet, hogy tartalmazza a kezelt verzióban történt módosításokat.

### <a name="how-can-i-automate-portal-deployments"></a>Hogyan automatizálható a portál üzembe helyezése?

A REST API keresztül programozott módon érheti el és kezelheti a fejlesztői portál tartalmát, függetlenül attól, hogy felügyelt vagy saját üzemeltetésű verziót használ-e.

Az API-t [a GitHub-adattár wiki szakasza ismerteti][2]. A portál tartalmának a környezetek közötti áttelepítésének automatizálására is használható – például egy tesztkörnyezetben az éles környezetbe. Erről a folyamatról a GitHubról szóló [cikkben](https://aka.ms/apimdocs/migrateportal) olvashat bővebben.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Támogatja a portál a Azure Resource Manager sablonokat, és/vagy kompatibilis-e a API Management DevOps Resource Kit-vel?

Nem.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Engedélyezni kell további VNet-kapcsolatot a felügyelt portál függőségeihez?

A legtöbb esetben – nem.

Ha a API Management szolgáltatás belső VNet található, a fejlesztői portál csak a hálózaton belülről érhető el. A felügyeleti végpont állomásneve fel kell oldania a szolgáltatás belső VIP-címére a portál felügyeleti felületének elérésére használt gépről. Győződjön meg arról, hogy a felügyeleti végpont regisztrálva van a DNS-ben. Helytelen konfiguráció esetén a következő hibaüzenet jelenik meg: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Ha a API Management szolgáltatás belső VNet van, és az internetről Application Gateway keresztül fér hozzá, ügyeljen arra, hogy engedélyezze a kapcsolatot a fejlesztői portálon és a API Management felügyeleti végpontján.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Egyéni API Management tartományt rendeltem hozzá, és a közzétett portál nem működik

A tartomány frissítése után újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish) , hogy a módosítások életbe lépnek.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Felvettem egy identitás-szolgáltatót, és nem látom a portálon

Miután konfigurálta az identitás-szolgáltatót (például HRE, AAD B2C), újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish) , hogy a módosítások életbe lépnek.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Beállítottam a delegálást, és a portál nem használja

A delegálás beállítása után újra közzé kell tennie [a portált](api-management-howto-developer-portal-customize.md#publish) , hogy a módosítások életbe lépnek.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>A többi API Management konfiguráció módosítása nem lett propagálva a fejlesztői portálon

A legtöbb konfigurációs módosítás (például a VNet, a bejelentkezés és a termék kifejezés) [a portál újbóli közzétételét](api-management-howto-developer-portal-customize.md#publish)igényli.

### <a name="cors"></a>CORS hibaüzenetet kapok az interaktív konzol használatakor

Az interaktív konzol ügyféloldali API-kérést tesz elérhetővé a böngészőből. A CORS probléma megoldásához vegyen fel [egy CORS-szabályzatot](api-management-cross-domain-policies.md#CORS) az API (ok) ra. Az összes paramétert manuálisan is megadhatja, vagy helyettesítő karakteres `*` értékeket használhat. Például:

```XML
<cors allow-credentials="true">
    <allowed-origins>
        <origin>https://contoso.com</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>*</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

Alkalmazza a CORS a globális hatókörön annak biztosítására, hogy az minden API-ra engedélyezve legyen.

1. A Azure Portal a API Management szolgáltatás **API** -k szakaszában navigáljon az **összes API** -hoz.
2. Kattintson a **</>** ikonra a **bejövő feldolgozás** szakaszban.
3. Szúrja be a szabályzatot az XML-fájl **<inbound>** szakaszában. Győződjön meg arról, hogy a **<origin>** érték megegyezik a fejlesztői portál tartományával.

> [!NOTE]
> 
> Ha a CORS szabályzatot a termék hatókörében alkalmazza, az API-k hatóköre helyett, és az API az előfizetés-kulcs hitelesítését használja egy fejlécen keresztül, a konzol nem fog működni.
>
> A böngésző automatikusan kiadja a beállítások HTTP-kérését, amely nem tartalmaz az előfizetési kulccsal rendelkező fejlécet. A hiányzó előfizetési kulcs miatt API Management nem tudja hozzárendelni a beállításokat a termékhez, így nem tudja alkalmazni a CORS házirendet.
>
> Megkerülő megoldásként átadhatja az előfizetési kulcsot egy lekérdezési paraméterben.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Milyen engedélyekre van szükségem a fejlesztői portál szerkesztéséhez?

Ha a portál felügyeleti módban való megnyitásakor a `Oops. Something went wrong. Please try again later.` hibaüzenet jelenik meg, akkor előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel (RBAC).

Az örökölt portáloknak a szolgáltatás hatókörében (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) `Microsoft.ApiManagement/service/getssotoken/action` engedélyre van szükségük ahhoz, hogy a felhasználói rendszergazda hozzáférjen a portálokhoz. Az új portálon a hatókör `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1``Microsoft.ApiManagement/service/users/token/action` engedélyre van szükség.

A következő PowerShell-parancsfájl használatával létrehozhat egy szerepkört a szükséges engedélyekkel. Ne felejtse el módosítani a `<subscription-id>` paramétert. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
A szerepkör létrehozása után bármely felhasználó számára megadható a Azure Portal **Access Control (iam)** szakasza. Ha ezt a szerepkört hozzárendeli a felhasználóhoz, az engedélyt a szolgáltatás hatókörében kell kiosztania. A felhasználó a szolgáltatás *bármely* felhasználója nevében képes sas-jogkivonatokat előállítani. Legalább ezt a szerepkört a szolgáltatás rendszergazdájához kell rendelni. A következő PowerShell-parancs bemutatja, hogyan rendelhető hozzá a szerepkör a legalacsonyabb hatókörű felhasználói `user1`hoz, hogy ne kelljen felesleges engedélyeket biztosítani a felhasználónak: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Miután megadta az engedélyeket egy felhasználó számára, a felhasználónak ki kell jelentkeznie, majd újra be kell jelentkeznie a Azure Portal az új engedélyek érvénybe léptetéséhez.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>A `Unable to start the portal. See if settings are specified correctly (...)` hibaüzenet jelenik meg

Ez a hiba akkor jelenik meg, ha a `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` `GET`-hívás sikertelen. A hívást a böngésző a portál felügyeleti felülete adja ki.

Ha a API Management szolgáltatás VNet található – tekintse meg a fenti VNet-kapcsolat kérdését.

A hívási hibát egy olyan SSL-tanúsítvány is okozhatja, amely egy egyéni tartományhoz van rendelve, és a böngésző nem tartja megbízhatónak. Enyhítő megoldásként eltávolíthatja a felügyeleti végpont egyéni tartományát – API Management a megbízható tanúsítvánnyal rendelkező alapértelmezett végpontra kerül vissza.

### <a name="whats-the-browser-support-for-the-portal"></a>Milyen böngésző-támogatást nyújt a portál?

| Böngésző                     | Támogatott       |
|-----------------------------|-----------------|
| Apple Safari                | Igen<sup>1</sup> |
| Google Chrome               | Igen<sup>1</sup> |
| Microsoft Edge              | Igen<sup>1</sup> |
| Microsoft Internet Explorer | Nem              |
| Mozilla Firefox             | Igen<sup>1</sup> |

 <small><sup>1</sup> támogatott a két legújabb üzemi verzióban.</small>

## <a name="next-steps"></a>További lépések

További információ az új fejlesztői portálról:

- [A felügyelt fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)
- [A portál saját üzemeltetésű verziójának beállítása][2]
- [Saját widget implementálása][3]

Egyéb erőforrások tallózása:

- [GitHub-adattár a forráskódtal][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend