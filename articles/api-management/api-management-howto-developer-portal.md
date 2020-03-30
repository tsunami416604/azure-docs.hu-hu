---
title: Az Azure API Management fejlesztői portál áttekintése
titleSuffix: Azure API Management
description: További információ a fejlesztői portálról az API Management ben.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335909"
---
# <a name="azure-api-management-developer-portal-overview"></a>Az Azure API Management fejlesztői portál – áttekintés

A fejlesztői portál egy automatikusan generált, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza. Ez az a hely, ahol az API-felhasználók felfedezhetik az API-kat, megtudhatják, hogyan használhatják őket, kérhetnek hozzáférést, és kipróbálhatják őket.

Ez a cikk ismerteti a különbségeket a saját üzemeltetésű és felügyelt verziói a fejlesztői portál API Management. Azt is elmagyarázza az architektúra és választ ad a gyakran feltett kérdésekre.

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Felügyelt és saját üzemeltetésű verziók

A fejlesztői portált kétféleképpen hozhatja létre:

- **Felügyelt verzió** - a portál szerkesztésével és testreszabásával, amely be van `<your-api-management-instance-name>.developer.azure-api.net`építve az API Management példányba, és az URL-en keresztül érhető el. [Ebből a dokumentációs cikkből](api-management-howto-developer-portal-customize.md) megtudhatja, hogyan érheti el és szabhatja testre a felügyelt portált.
- **Saját üzemeltetésű verzió** – a portál telepítése és saját üzemeltetése egy API Management-példányon kívül. Ez a megközelítés lehetővé teszi a portál kódbázisának szerkesztését és a megadott alapvető funkciók kiterjesztését - például egyéni widgeteket valósíthat meg a harmadik fél rendszerekkel való integrációhoz. Ebben a forgatókönyvben Ön a portál karbantartója, és ön felelős a portál legújabb verziójára való frissítésért. A részleteket és az utasításokat a [portál forráskódjával][1] és a [widget megvalósításával kapcsolatos oktatóanyaggal][3]együtt találja. A [felügyelt verzió oktatóanyaga](api-management-howto-developer-portal-customize.md) végigvezeti a portál felügyeleti paneljén, amely gyakori a felügyelt és a saját üzemeltetésű verziók.

## <a name="portal-architectural-concepts"></a>Portál architekturális fogalmai

A portál összetevői logikailag két kategóriába sorolhatók: *kód* és *tartalom*.

*A kód* a [GitHub-tárházban][1] van megtartva, és a következőket tartalmazza:

- Widgets - amelyek vizuális elemeket képviselnek, és egyesítik a HTML, JavaScript, stílus képesség, beállítások, és a tartalom feltérképezése. Ilyenpéldául egy kép, egy szöveges bekezdés, egy űrlap, az API-k listája stb.
- Stílus meghatározások - amelyek meghatározzák, hogyan lehet kütyü lehet stílusú
- Motor - amely statikus weboldalakat hoz létre a portál tartalmából, és JavaScript nyelven íródott
- Visual editor - amely lehetővé teszi a böngésző testreszabási és szerzői élményt

*A tartalom* két alkategóriára oszlik: *portáltartalom* és *API Management tartalom*.

*A portál tartalma* a portálra jellemző, és a következőket tartalmazza:

- Oldalak - például céloldal, API oktatóanyagok, blogbejegyzések
- Média – képek, animációk és egyéb fájlalapú tartalmak
- Elrendezések - sablonok, amelyek egy URL-hez igazodnak, és meghatározzák az oldalak megjelenítésének módját
- Stílusok - értékek stílus definíciók, pl. betűtípusok, színek, határok
- Beállítások - konfiguráció, pl. favicon, weboldal metaadatok

*A portál tartalma*– a média kivételével – JSON-dokumentumokban van kifejezve.

*Az API Management-tartalom* olyan entitásokat tartalmaz, mint az API-k, műveletek, termékek, előfizetések.

A portál a [Paperbits keretrendszer](https://paperbits.io/)adaptált elágazásán alapul . Az eredeti Paperbits funkció ki lett bővítve, hogy API Management-specifikus widgeteket (például API-k listáját, termékek listáját) és egy összekötőt biztosítson az API Management szolgáltatáshoz a tartalom mentése és beolvasása érdekében.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Gyakori kérdések

Ebben a szakaszban általános jellegű fejlesztői portállal kapcsolatos gyakori kérdésekre válaszolunk. A saját üzemeltetésű verzióval kapcsolatos kérdésekről [a GitHub-tárház wikiszakaszában tájékot](https://github.com/Azure/api-management-developer-portal/wiki)kell tudnia.

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Hogyan telepíthetők át a portál előzetes verziójából?

A fejlesztői portál előzetes verziójának használatával kiépítette az előzetes tartalom az API Management szolgáltatásban. Az alapértelmezett tartalom jelentősen módosult az általánosan elérhető verzióban a jobb felhasználói élmény érdekében. Ez is beleértve új fütyülőréce.

Ha a felügyelt verziót használja, állítsa alaphelyzetbe a portál tartalmát a Műveletek menüszakasz **Tartalom visszaállítása** **parancsára** kattintva. A művelet megerősítése eltávolítja a portál összes tartalmát, és kiépíti az új alapértelmezett tartalmat. A portál motorja automatikusan frissült az API Management szolgáltatásban.

![Portál tartalmának alaphelyzetbe állítása](media/api-management-howto-developer-portal/reset-content.png)

Ha a saját üzemeltetésű verziót `scripts/cleanup.bat` használja, használja a és `scripts/generate.bat` a GitHub-tárházból a meglévő tartalom eltávolításához és új tartalom kiépítéséhez. Győződjön meg arról, hogy előzetesen frissíti a portál kódját a GitHub-tárházból a legújabb kiadásra.

Ha nem szeretné visszaállítani a portál tartalmát, érdemes lehet az újonnan elérhető widgeteket használni az oldalakon. A meglévő widgetek automatikusan frissültek a legújabb verziókra.

Ha a portál kiépítése után az általános rendelkezésre állási értesítés, akkor már az új alapértelmezett tartalom. Az Ön részéről nincs szükség beavatkozásra.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Hogyan tudok áttérni a régi fejlesztői portálról a fejlesztői portálra?

A portálok nem kompatibilisek, és manuálisan kell áttelepítenie a tartalmat.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>A portál rendelkezik a régi portál összes funkciójával?

A fejlesztői portál már nem támogatja az *Alkalmazások* és *problémák*alkalmazást.

Az interaktív fejlesztői konzolon az OAuth-mal való hitelesítés még nem támogatott. Nyomon követheti a folyamatot [a GitHub-problémán](https://github.com/Azure/api-management-developer-portal/issues/208)keresztül.

### <a name="has-the-old-portal-been-deprecated"></a>A régi portál elavult?

A régi fejlesztői és megjelenítői portálok mostantól *örökölt* funkciók – csak biztonsági frissítéseket fognak kapni. Az új funkciók csak az új fejlesztői portálon lesznek megvalósítva.

Az örökölt portálok eprecációját külön jelentik be. Ha kérdése, problémája vagy megjegyzése van, vesse fel őket [egy dedikált GitHub-problémában.](https://github.com/Azure/api-management-developer-portal/issues/121)

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A portálnem támogatja a szükséges funkciókat

Megnyithat egy [szolgáltatáskérelmet,](https://aka.ms/apimwish) vagy [saját maga valósíthatja meg a hiányzó funkciókat.][3] Ha saját kezűleg valósítja meg a funkciót, üzemeltetheti a fejlesztői portált, vagy megnyithat egy lekéréses kérelmet a GitHubon, hogy a módosításokat a felügyelt verzióba is belefoglalhassa.

### <a name="how-can-i-automate-portal-deployments"></a>Hogyan automatizálhatom a portál-üzembe helyezéseket?

A fejlesztői portál tartalmát programozott módon érheti el és kezelheti a REST API-n keresztül, függetlenül attól, hogy felügyelt vagy saját üzemeltetésű verziót használ.You can programmatically access and manage the developer portal's content through the REST API, regardless you're using a managed or a self-hosted version.

Az API a [GitHub-tárház wikiszakaszában][2]van dokumentálva. A portáltartalom környezetek közötti áttelepítésének automatizálására használható – például egy tesztkörnyezetből az éles környezetbe. Erről a folyamatról ebben a githubos [dokumentációs cikkben](https://aka.ms/apimdocs/migrateportal) olvashat bővebben.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Támogatja a portál az Azure Resource Manager-sablonokat, és/vagy kompatibilis az API Management DevOps Resource Kit-tel?

Nem.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>További virtuális hálózat-kapcsolatot kell engedélyeznem a felügyelt portálfüggőségekhez?

A legtöbb esetben - nem.

Ha az API Management szolgáltatás egy belső virtuális hálózatban van, a fejlesztői portál csak a hálózaton belül érhető el. A felügyeleti végpont állomásnevének fel kell oldania a szolgáltatás belső IP-címére a portál felügyeleti felületének eléréséhez használt gépről. Győződjön meg arról, hogy a felügyeleti végpont regisztrálva van a DNS-ben. Helytelen konfigurálás esetén a következő hibaüzenet `Unable to start the portal. See if settings are specified correctly in the configuration (...)`jelenik meg: .

Ha az API Management szolgáltatás egy belső virtuális hálózatban van, és az internetről az Application Gateway-en keresztül éri el, győződjön meg arról, hogy engedélyezi a kapcsolatot a fejlesztői portálhoz és az API Management felügyeleti végpontjaihoz.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Egyéni API Management tartományt rendeltem hozzá, és a közzétett portál nem működik

A tartomány frissítése után újra közzé kell [tennie a portált a](api-management-howto-developer-portal-customize.md#publish) módosítások érvénybe léptetéséhez.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Hozzáadtam egy identitásszolgáltatót, és nem látom a portálon

Az identitásszolgáltató (például AAD, AAD B2C) konfigurálása után újra közzé kell [tennie a portált](api-management-howto-developer-portal-customize.md#publish) a módosítások érvénybe léptetéséhez.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Létrehoztam a küldöttséget, és a portál nem használja

A delegálás beállítása után újra közzé kell [tennie a portált](api-management-howto-developer-portal-customize.md#publish) a módosítások érvénybe léptetéséhez.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>A többi API Management-konfigurációs módosítás omat nem propagálták a fejlesztői portálon

A legtöbb konfigurációs módosítás (például virtuális hálózat, bejelentkezés és termékfeltételek) esetében újra közzé kell [tenni a portált.](api-management-howto-developer-portal-customize.md#publish)

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>CORS-hibaüzenet jelenik meg az interaktív konzol használatakor

Az interaktív konzol ügyféloldali API-kérelmet küld a böngészőből. Oldja meg a CORS-problémát [egy CORS-házirend](api-management-cross-domain-policies.md#CORS) hozzáadásával az API-khoz.

A CORS-szabályzat állapotát az Azure-portálon az API Management szolgáltatás **Portál áttekintése** szakaszában ellenőrizheti. A figyelmeztető mező hiányzó vagy helytelenül konfigurált házirendet jelez.

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cors-azure-portal.png)

A CORS-házirend automatikus alkalmazása a **CORS engedélyezése** gombra kattintva.

A CORS-t manuálisan is engedélyezheti.

1. Kattintson a **Manuális alkalmazás a globális szintű hivatkozásra** a létrehozott házirendkód megtekintéséhez.
2. Keresse meg az **összes API-t** az API-k szakaszában az API Management szolgáltatás az Azure Portalon. **APIs**
3. Kattintson **</>** az ikonra a **Bejövő feldolgozás** részben.
4. Szúrja be a **<inbound>** házirendet az XML-fájl szakaszába. Győződjön **<origin>** meg arról, hogy az érték megegyezik a fejlesztői portál tartományával.

> [!NOTE]
> 
> Ha a CORS-szabályzatot a Termék hatókörben alkalmazza az API(s) hatóköre helyett, és az API egy fejlécen keresztül használja az előfizetési kulcs hitelesítését, a konzol nem fog működni.
>
> A böngésző automatikusan kiad egy OPTIONS HTTP-kérelmet, amely nem tartalmaz fejlécet az előfizetési kulccsal. A hiányzó előfizetési kulcs miatt az API Management nem tudja társítani a OPTIONS-hívást egy termékhez, így nem tudja alkalmazni a CORS-szabályzatot.
>
> Kerülő megoldásként átadhatja az előfizetési kulcsot egy lekérdezési paraméterben.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Milyen engedélyekre van szükségem a fejlesztői portál szerkesztéséhez?

Ha a hibát `Oops. Something went wrong. Please try again later.` a portál felügyeleti módban való megnyitásakor látja, előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel (RBAC).

Az örökölt portálok `Microsoft.ApiManagement/service/getssotoken/action` engedélyre volt`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`szükség a szolgáltatás hatókörén ( ) ahhoz, hogy a felhasználó rendszergazda számára hozzáférhessen a portálokhoz. Az új portálhoz `Microsoft.ApiManagement/service/users/token/action` a `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`hatókörengedélye szükséges.

A következő PowerShell-parancsfájl használatával hozhat létre egy szerepkört a szükséges engedélyekkel. Ne felejtse `<subscription-id>` el módosítani a paramétert. 

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
 
A szerepkör létrehozása után bármely felhasználó számára megadható az Azure Portal **hozzáférés-vezérlési (IAM)** szakaszából. Ha ezt a szerepkört egy felhasználóhoz rendeli hozzá, akkor az engedélyt a szolgáltatáshatókörhöz rendeli hozzá. A felhasználó képes lesz sas-jogkivonatokat generálni a szolgáltatás *bármely* felhasználója nevében. Ezt a szerepkört legalább a szolgáltatás rendszergazdájához kell rendelni. A következő PowerShell-parancs bemutatja, hogyan `user1` rendelheti hozzá a szerepkört a legalacsonyabb hatókörű felhasználóhoz, hogy elkerülje a szükségtelen engedélyek megadását a felhasználó számára: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Miután az engedélyeket megkapta a felhasználó, a felhasználónak ki kell jelentkeznie, és jelentkezzen be újra az Azure Portalon az új engedélyek érvénybe léptetéséhez.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>A `Unable to start the portal. See if settings are specified correctly (...)` hibát látom

Ez a hiba `GET` akkor `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` jelenik meg, ha egy hívás sikertelen. A hívást a portál felügyeleti felülete bocsátja ki a böngészőből.

Ha az API Management szolgáltatás egy virtuális hálózatban található – tekintse meg a fenti virtuális hálózat-kapcsolat kérdését.

A híváshibát egy TLS/SSL-tanúsítvány is okozhatja, amely egyéni tartományhoz van rendelve, és amelyet a böngésző nem bízik meg. Ennek mérsékléseként eltávolíthatja a felügyeleti végpont egyéni tartomány – API Management esik vissza az alapértelmezett végpont egy megbízható tanúsítványt.

### <a name="whats-the-browser-support-for-the-portal"></a>Mi a portál böngészőtámogatása?

| Böngésző                     | Támogatott       |
|-----------------------------|-----------------|
| Apple Safari                | Igen<sup>1</sup> |
| Google Chrome               | Igen<sup>1</sup> |
| Microsoft Edge              | Igen<sup>1</sup> |
| Microsoft Internet Explorer | Nem              |
| Mozilla Firefox             | Igen<sup>1</sup> |

 <small><sup>1</sup> A két legújabb gyártási verzió ban támogatott.</small>

## <a name="next-steps"></a>További lépések

További információ az új fejlesztői portálról:

- [A felügyelt fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)
- [A portál saját üzemeltetésű verziójának beállítása][2]
- [Valósítsa meg saját widgetjét][3]

Böngésszen más források:

- [GitHub-tárház a forráskóddal][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend