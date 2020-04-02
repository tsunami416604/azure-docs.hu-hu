---
title: Az API Management szolgáltatás konfigurálása a Git - Azure | Microsoft dokumentumok
description: Ismerje meg, hogyan mentheti és konfigurálhatja az API Management szolgáltatás konfigurációját a Git használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: b9184808b71cce03882022fd37967fe421e64062
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548991"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Az API Management-szolgáltatáskonfiguráció mentése és konfigurálása a Git használatával

Minden API Management szolgáltatáspéldány egy konfigurációs adatbázist tart fenn, amely a szolgáltatáspéldány konfigurációjával és metaadataival kapcsolatos információkat tartalmaz. A szolgáltatáspéldány módosítása az Azure Portalon, a PowerShell-parancsmag használatával vagy egy REST API-hívás módosításával hajtható végre. Ezeken a módszereken kívül a szolgáltatáspéldány konfigurációját git használatával is kezelheti, amely lehetővé teszi a szolgáltatáskezelési forgatókönyveket, például:

* Konfigurációs verziószámozás – a szolgáltatáskonfiguráció különböző verzióinak letöltése és tárolása
* Tömeges konfigurációs módosítások – a szolgáltatáskonfiguráció több részének módosítása a helyi tárházban, és a módosítások integrálása a kiszolgálóra egyetlen művelettel
* Ismerős Git eszközlánc és munkafolyamat – használja a Git-eszközt és munkafolyamatokat, amelyeket már ismer.

Az alábbi ábra áttekintést nyújt az API Management szolgáltatáspéldány konfigurálásának különböző módjairól.

![Git konfigurálása][api-management-git-configure]

Amikor módosítja a szolgáltatást az Azure Portalon, a PowerShell-parancsmagok vagy a REST API `https://{name}.management.azure-api.net` használatával, a szolgáltatáskonfigurációs adatbázist a végpont használatával kezeli, ahogy az a diagram jobb oldalán látható. A diagram bal oldala bemutatja, hogyan kezelheti a szolgáltatás konfigurációját a Git `https://{name}.scm.azure-api.net`és git tárház segítségével a szolgáltatáshoz.

A következő lépések áttekintést nyújtanak az API Management szolgáltatáspéldány Git használatával történő kezeléséről.

1. A Git-konfiguráció elérése a szolgáltatásban
2. A szolgáltatáskonfigurációs adatbázis mentése a Git-tárházba
3. Klónozza a Git-tárta helyi gépre
4. Húzza le a legújabb tárta le a helyi gépre, és véglegesítse és tolja vissza a módosításokat a tármű
5. A repo változásainak telepítése a szolgáltatáskonfigurációs adatbázisba

Ez a cikk azt ismerteti, hogyan engedélyezheti és használhatja a Gitet a szolgáltatáskonfiguráció kezeléséhez, és a Git-tárházban lévő fájlokra és mappákra vonatkozó hivatkozást tartalmaz.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>A Git-konfiguráció elérése a szolgáltatásban

A Git konfigurációs beállításainak megtekintéséhez és beállításához kattintson a **Biztonság** menüre, és keresse meg a **Konfigurációs tárház** lapot.

![GIT engedélyezése][api-management-enable-git]

> [!IMPORTANT]
> Minden olyan titkos titok, amely nem named értékekként van definiálva, a tárházban tárolódik, és az előzményekben marad, amíg le nem tiltja és újra engedélyezi a Git-hozzáférést. Az elnevezett értékek biztonságos helyet biztosítanak az állandó karakterlánc-értékek , köztük a titkos kulcsok kezeléséhez az összes API-konfigurációban és szabályzatban, így nem kell azokat közvetlenül a házirend-utasításokban tárolnia. További információ: [Named Values in Azure API Management policies](api-management-howto-properties.md).
>
>

A Git-hozzáférés REST API-val történő engedélyezéséről és letiltásáról a [Git-hozzáférés engedélyezése vagy letiltása a REST API használatával című témakörben](/rest/api/apimanagement/2019-01-01/tenantaccess?EnableGit)talál.

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>A szolgáltatáskonfiguráció mentése a Git-tárházba

A tárház klónozása előtt az első lépés a szolgáltatáskonfiguráció aktuális állapotának mentése a tárházba. Kattintson **a Mentés a tárházba gombra.**

Hajtsa végre a kívánt módosításokat a megerősítő képernyőn, és a mentéshez kattintson az **Ok** gombra.

Néhány pillanat múlva a konfiguráció menti, és a tárház konfigurációs állapota jelenik meg, beleértve az utolsó konfigurációs módosítás dátumát és időpontját, valamint a szolgáltatáskonfiguráció és a tárház közötti utolsó szinkronizálást.

Miután a konfiguráció tárat mentette a tárházba, klónozható.

A művelet REST API használatával történő végrehajtásáról a [Konfigurációs pillanatkép véglegesítése a REST API használatával című témakörben](/rest/api/apimanagement/2019-01-01/tenantaccess?CommitSnapshot)olvashat.

## <a name="to-clone-the-repository-to-your-local-machine"></a>A tárház klónozása a helyi számítógépre

A tárház klónozásához szükség van a tárház URL-címére, egy felhasználónévre és egy jelszóra. A felhasználónév és egyéb hitelesítő adatok megszerzéséhez kattintson a lap tetején található **Hitelesítő adatok elérése** elemre.

Jelszó létrehozásához először győződjön meg arról, hogy a **lejárati idő** a kívánt lejárati dátumra és időre van beállítva, majd kattintson a **Létrehozás gombra.**

> [!IMPORTANT]
> Jegyezze fel ezt a jelszót. Ha elhagyja ezt az oldalt, a jelszó nem jelenik meg újra.
>

A következő példák a Git Bash eszközt használják a [Windows-hoz,](https://www.git-scm.com/downloads) de bármilyen Jól ismert Git eszközt használhat.

Nyissa meg a Git-eszközt a kívánt mappában, és futtassa a következő parancsot a git-tárház helyi számítógépre történő klónozásához az Azure Portal által biztosított paranccsal.

```
git clone https://{name}.scm.azure-api.net/
```

Amikor a rendszer kéri, adja meg a felhasználónevet és a jelszót.

Ha bármilyen hibát észlel, próbálja `git clone` meg úgy módosítani a parancsot, hogy az tartalmazza a felhasználónevet és a jelszót, ahogy az a következő példában látható.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Ha ez hibát okoz, próbálja meg az URL-kódolást a parancs jelszórészével. Ennek egyik gyors módja a Visual Studio megnyitása, és a következő parancs kiadása az **Azonnali ablakban**. Az **Azonnali ablak**megnyitásához nyisson meg bármilyen megoldást vagy projektet a Visual Studióban (vagy hozzon létre egy új, üres konzolalkalmazást), és válassza a **Debug** menü **Windows**, **Immediate** parancsát.

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

A git parancs létrehozásához használja a kódolt jelszót a felhasználónévvel és a tárház helyével együtt.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

A tárház klónozása után megtekintheti és használhatja a helyi fájlrendszerben. További információ: [A helyi Git-tárház fájl- és mappastruktúra-hivatkozása.](#file-and-folder-structure-reference-of-local-git-repository)

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>A helyi tárház frissítése a legfrissebb szolgáltatáspéldány-konfigurációval

Ha módosítja az API Management szolgáltatáspéldányát az Azure Portalon vagy a REST API-t, ezeket a módosításokat a tárházba kell mentenie, mielőtt frissítheti a helyi adattárat a legújabb módosításokkal. Ehhez kattintson a **Konfiguráció mentése a tárház tárházaz** Azure **Portalon,** és adja ki a következő parancsot a helyi tárházban.

```
git pull
```

Futtatás `git pull` előtt győződjön meg arról, hogy a mappában a helyi tárház. Ha éppen befejezte `git clone` a parancsot, akkor meg kell változtatnia a könyvtárat a tárban a parancs futtatásával, mint a következő.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>A helyi tárlatról a kiszolgáló-tárlatra történő módosítások leküldése
Ha a helyi tárházból le szeretné adni a módosításokat a kiszolgálótárba, a módosításokat véglegesbe kell követnie, majd le kell nyomnia a kiszolgálótárba. A módosítások véglegesítéséhez nyissa meg a Git parancseszközt, váltson a helyi tárház könyvtárára, és adja ki a következő parancsokat.

```
git add --all
git commit -m "Description of your changes"
```

Ha az összes véglegesítést le szeretné adni a kiszolgálónak, futtassa a következő parancsot.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Az API Management szolgáltatáspéldány bármely szolgáltatáskonfigurációs módosításának üzembe helyezése

Miután a helyi módosítások véglegesítése és a kiszolgálótárba való leküldése után telepítheti őket az API Management szolgáltatáspéldányra.

A művelet REST API használatával történő végrehajtásáról a [Git-módosítások telepítése a KONFIGURÁCIÓs adatbázisba a REST API használatával című témakörben](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/tenantconfiguration)talál tájékoztatást.

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>A helyi Git-tárház fájl- és mappastruktúra-hivatkozása

A helyi git-tárházban található fájlok és mappák a szolgáltatáspéldány konfigurációs adatait tartalmazzák.

| Elem | Leírás |
| --- | --- |
| gyökér api-kezelési mappa |A szolgáltatáspéldány legfelső szintű konfigurációját tartalmazza |
| apis mappa |A szolgáltatáspéldány api-jának konfigurációját tartalmazza |
| csoportok mappa |A szolgáltatáspéldány csoportjainak konfigurációját tartalmazza |
| házirendek mappa |A szolgáltatáspéldány házirendjeit tartalmazza |
| portalStyles mappa |A fejlesztői portál testreszabásának konfigurációját tartalmazza a szolgáltatáspéldányban |
| termékek mappa |A szolgáltatáspéldányban lévő termékek konfigurációját tartalmazza |
| sablonok mappa |A szolgáltatáspéldány e-mail sablonjainak konfigurációját tartalmazza |

Minden mappa tartalmazhat egy vagy több fájlt, és bizonyos esetekben egy vagy több mappát, például egy mappát minden API-hoz, termékhez vagy csoporthoz. Az egyes mappákban lévő fájlok a mappanév által leírt entitástípusra vonatkoznak.

| Fájltípus | Cél |
| --- | --- |
| json |Az adott entitás konfigurációs adatai |
| html |Az entitás leírása, amely gyakran megjelenik a fejlesztői portálon |
| xml |Házirend-utasítások |
| css |Stíluslapok a fejlesztői portál testreszabásához |

Ezek a fájlok a helyi fájlrendszeren hozhatók létre, törölhetők, szerkeszthetők és kezelhetők, valamint az API Management szolgáltatáspéldányra telepített módosítások.

> [!NOTE]
> A következő entitások nem szerepelnek a Git-tárházban, és nem konfigurálhatók a Git használatával.
>
> * [Felhasználók](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user)
> * [Előfizetések](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription)
> * [Elnevezett értékek](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/property)
> * A stílusoktól eltérő fejlesztői portálentitások
>

### <a name="root-api-management-folder"></a>Gyökér api-felügyeleti mappa
A `api-management` gyökérmappa `configuration.json` egy olyan fájlt tartalmaz, amely a következő formátumban tartalmazza a szolgáltatáspéldány legfelső szintű adatait.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

Az első négy`RegistrationEnabled` `UserRegistrationTerms`beállítás `UserRegistrationTermsEnabled`( `UserRegistrationTermsConsentRequired`, , , és ) a Biztonság szakasz **Identitások** lapjának következő beállításaira van leképezve. **Security**

| Identitás beállítása | Térképek a |
| --- | --- |
| RegistrationEnabled |**Felhasználónév és jelszóazonosító** szolgáltató jelenléte |
| UserRegistrationTerms (Felhasználóregisztrációs kifejezések) |**Használati feltételek a felhasználói regisztrációs szövegdobozon** |
| UserRegistrationTermsEnabled |**Használati feltételek megjelenítése a regisztrációs oldalon** jelölőnégyzetben |
| UserRegistrationTermsConsentRequired Required |**Hozzájárulás kérése** jelölőnégyzet |
| RequireUserSigninEnabled |**Névtelen felhasználók átirányítása a bejelentkezési oldalra** jelölőnégyzet |

A következő négy`DelegationEnabled` `DelegationUrl`beállítás `DelegatedSubscriptionEnabled`( `DelegationValidationKey`, , , és ) a Biztonság szakasz **Delegálás** lapjának következő beállításaira van leképezve. **Security**

| Delegálási beállítás | Térképek a |
| --- | --- |
| Delegálás engedélyezve |**Bejelentkezés i & delegálása** jelölőnégyzet |
| DelegationUrl |**Végpont URL-címének delegálása** |
| Delegáltelőfizetés engedélyezve |**Termék-előfizetés delegálása** jelölőnégyzet |
| DelegationValidationKey (DelegationValidationKey) |**Érvényesítési kulcs delegálása** szövegmező |

A végső `$ref-policy`beállítás , a szolgáltatáspéldány globális házirend-utasításfájljára van leképezve.

### <a name="apis-folder"></a>apis mappa
A `apis` mappa a szolgáltatáspéldány minden API-jához tartalmaz egy mappát, amely a következő elemeket tartalmazza.

* `apis\<api name>\configuration.json`- ez az API konfigurációja, és információkat tartalmaz a háttérszolgáltatás URL-címéről és a műveletekről. Ez ugyanazokat az információkat, amelyeket vissza adna, ha `export=true` `application/json` egy adott [API bekapása](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apis/get) formátumban.
* `apis\<api name>\api.description.html`- ez az API leírása, és `description` megfelel az [API entitás](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.entityproperty)tulajdonságának.
* `apis\<api name>\operations\`- ez `<operation name>.description.html` a mappa olyan fájlokat tartalmaz, amelyek leképezik az API műveleteit. Minden fájl egyetlen művelet leírását tartalmazza az API-ban, amely leképezi a `description` [műveletentitás](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) tulajdonságát a REST API-ban.

### <a name="groups-folder"></a>csoportok mappa
A `groups` mappa a szolgáltatáspéldányban definiált minden egyes csoporthoz tartalmaz egy mappát.

* `groups\<group name>\configuration.json`- ez a csoport konfigurációja. Ez ugyanaz az információ, amelyet a rendszer visszaadna, ha [egy adott csoport bekapása](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/group/get) műveletet hívna meg.
* `groups\<group name>\description.html`- ez a csoport leírása, és `description` megfelel a [csoportentitás](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)tulajdonának.

### <a name="policies-folder"></a>házirendek mappa
A `policies` mappa tartalmazza a szolgáltatáspéldány házirend-nyilatkozatait.

* `policies\global.xml`- a szolgáltatáspéldány globális hatókörén definiált házirendeket tartalmazza.
* `policies\apis\<api name>\`- ha bármilyen házirend van definiálva az API hatókörében, akkor ezek ebben a mappában találhatók.
* `policies\apis\<api name>\<operation name>\`mappa – ha a műveleti hatókörben definiált házirendek vannak `<operation name>.xml` definiálva, akkor azok ebben a mappában találhatók az egyes műveletekhez tartozó házirend-utasításokhoz leképező fájlokban.
* `policies\products\`- ha a termékkörön bármilyen házirend van definiálva, akkor `<product name>.xml` azok ebben a mappában találhatók, amely az egyes termékekre vonatkozó irányelvekhez tartozó fájlokat tartalmaz.

### <a name="portalstyles-folder"></a>portalStyles mappa
A `portalStyles` mappa konfigurációs és stíluslapokat tartalmaz a szolgáltatási példány fejlesztői portáljának testreszabásához.

* `portalStyles\configuration.json`- tartalmazza a fejlesztői portál által használt stíluslapok nevét
* `portalStyles\<style name>.css`- `<style name>.css` minden fájl tartalmazza stílusok`Preview.css` a `Production.css` fejlesztői portál (és alapértelmezés szerint).

### <a name="products-folder"></a>termékek mappa
A `products` mappa a szolgáltatáspéldányban definiált minden egyes termékhez tartalmaz egy mappát.

* `products\<product name>\configuration.json`- ez a termék konfigurációja. Ez ugyanaz az információ, amelyet a rendszer visszaadna, ha [egy adott termék behívása](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/product/get) lenne.
* `products\<product name>\product.description.html`- ez a termék leírása, és `description` megfelel a [REST API-ban](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) szereplő termékentitás tulajdonságának.

### <a name="templates"></a>sablonok
A `templates` mappa a szolgáltatáspéldány [e-mail sablonjainak konfigurációját](api-management-howto-configure-notifications.md) tartalmazza.

* `<template name>\configuration.json`- ez az e-mail sablon konfigurációja.
* `<template name>\body.html`- ez az e-mail sablon törzse.

## <a name="next-steps"></a>További lépések
A szolgáltatáspéldány kezelésének egyéb módjairól a következő témakörben talál tájékoztatást:

* A szolgáltatáspéldány kezelése a következő PowerShell-parancsmagokkal
  * [Szolgáltatások üzembe helyezése – PowerShell-parancsmagok leírása](https://docs.microsoft.com/powershell/module/wds)
  * [Szolgáltatásfelügyelet – PowerShell-parancsmagok leírása](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* A szolgáltatáspéldány kezelése a REST API használatával
  * [API-kezelés REST API-jának hivatkozása](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




