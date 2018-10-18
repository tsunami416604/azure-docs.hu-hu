---
title: Konfigurálja az API Management szolgáltatás használatával Git – Azure |} A Microsoft Docs
description: Ismerje meg, mentse, és a Git használatával az API Management-szolgáltatás konfigurációjának konfigurálása.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: ce4ff33a8af9cf864ad4902599a0ab18b2b27182
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387225"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Mentse, és a Git használatával az API Management-szolgáltatás konfigurációjának beállítása

Minden API Management szolgáltatáspéldányt a konfigurációs és a szolgáltatáspéldány metaadatokkal kapcsolatos információkat tartalmazó konfigurációs adatbázis tárolja. Módosítások módosítja a beállítást, az Azure Portalon, egy PowerShell-parancsmag használatával, vagy a REST API-hívás gondoskodik a szolgáltatás példánya lehet tenni. Ezek a metódusok mellett is kezelheti a szolgáltatáspéldány konfigurációjának készítése a Git használatával, például a szolgáltatás felügyeleti forgatókönyvek engedélyezése:

* Konfigurációs verziószámozása – töltse le, és tárolja a szolgáltatás konfigurációjának különböző verziói
* Konfigurációs módosítások tömeges – több részből állnak a helyi tárházban a szolgáltatás konfigurációjának módosításához, és egyetlen művelettel a módosítások a kiszolgáló integrálása
* Ismeri a Git-eszközlánc és a munkafolyamat - Git azokat az eszközöket és, hogy már ismeri a munkafolyamatok használata

Az alábbi ábrán látható a különböző módokon konfigurálhatja az API Management szolgáltatáspéldányhoz áttekintése.

![A Git konfigurálása][api-management-git-configure]

Ha módosítja a szolgáltatás az Azure portal, PowerShell-parancsmagok vagy a REST API használatával, a szolgáltatás konfigurációs adatbázis használatával kezeli a `https://{name}.management.azure-api.net` végpont, a diagram jobb oldalán látható módon. A diagram bal oldalán látható, hogyan kezelheti a Git használatával szolgáltatás konfigurációjának és helyén található a szolgáltatás a Git-tárház `https://{name}.scm.azure-api.net`.

Az alábbi lépéseket a Git használatával az API Management szolgáltatáspéldány kezelése áttekintést nyújtanak.

1. Git a szolgáltatási konfiguráció
2. A szolgáltatás konfigurációs adatbázis mentse a Git-tárház
3. Klónozza a Git-adattár helyi számítógépre
4. A legújabb tárházat a helyi gépen, és a módosítások véglegesítése és leküldése visszavonni az adattárhoz
5. A módosítások az adattárból a szolgáltatás konfigurációs adatbázis üzembe helyezése

Ez a cikk azt ismerteti, hogyan engedélyezheti és kezelheti a szolgáltatás konfigurációját a git szoftver használatával, és a egy hivatkozást kínál a fájlokat és mappákat a Git-adattárban.

## <a name="access-git-configuration-in-your-service"></a>Git a szolgáltatási konfiguráció

Megtekintheti, és a Git-konfigurációs beállítások konfigurálása, kattintson a **biztonsági** menüben, és keresse meg a **konfigurációs adattárhoz** fülre.

![A GIT engedélyezése][api-management-enable-git]

> [!IMPORTANT]
> Nem definiált tulajdonságok a tárházban fogja tárolni, és a kapcsolódó előzmények marad, amíg ki nem titkos kulcsok tiltsa le, majd engedélyezze újra a Git-hozzáférés. Tulajdonságok kezelése állandó karakterlánc értékeit, így nem kell tárolni őket közvetlenül a házirend-utasítások API konfigurálása és a házirendeket, titkos adatait, beleértve biztonságos helyet biztosítanak. További információkért lásd: [tulajdonságok használata az Azure API Management házirendek](api-management-howto-properties.md).
> 
> 

Engedélyezés vagy letiltás Git-hozzáférés REST API használatával kapcsolatos információkért lásd: [engedélyezheti vagy letilthatja a REST API használatával Git hozzáférését](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>A szolgáltatás konfigurációjának mentéséhez, a Git-adattár

Az első lépés előtt klónozta a tárházat, hogy a szolgáltatás konfigurációját aktuális állapotának mentése az adattárba. Kattintson a **tárházba mentéséhez**.

Végezze el a kívánt módosításokat a megerősítő képernyőn, és kattintson a **Ok** mentéséhez.

A konfiguráció mentése után néhány percet, és a konfigurációs a tárház állapota, beleértve a dátum és az utolsó konfigurációmódosítás és a szolgáltatás konfigurációját és azon tárház között a legutóbbi szinkronizálás időpontja.

A tárház menti a konfigurációt, miután is lehet klónozni.

Információ a REST API használatával a művelet végrehajtása: [véglegesítési konfiguráció pillanatkép készítése a REST API-val](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>A helyi gépen a tárház klónozásához

A tárház klónozása, kell az URL-cím a tárház, egy felhasználónevet és jelszót. Felhasználónév- és egyéb hitelesítő adatok lekéréséhez kattintson a **hozzáférési hitelesítő adatokat** az oldal tetején.  
 
Jelszót létrehozni, először győződjön meg arról, hogy a **lejárati** állítsa be a kívánt lejárati dátumát és idejét, és kattintson a **Generate**.

> [!IMPORTANT]
> Jegyezze fel ezt a jelszót. Ha elhagyja az oldalt a jelszó nem jelennek újra.
> 

A következő példákban a Git Bash eszközt [Git for Windows](http://www.git-scm.com/downloads) , de bármilyen ismeri a Git-eszközt is használhatja.

Nyissa meg a Git-eszközt a kívánt mappába, és futtassa a következő parancsot a helyi gépre, az Azure portal által biztosított a paranccsal a git-tárház klónozásához.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Adja meg a felhasználónevet és jelszót, amikor a rendszer kéri.

Ha hibákat kap, próbálja meg módosítani a `git clone` parancs tartalmazza a felhasználónevet és jelszót, az alábbi példában látható módon.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Ha ez biztosítja, hogy a hiba, próbálja meg a parancs jelszóval része kódolás URL-címe. Ennek egyik gyors módja az, hogy nyissa meg a Visual Studiót, és az alábbi parancsot a a **azonnali ablak**. Megnyitásához a **azonnali ablak**, nyissa meg a megoldás vagy a projekt a Visual Studio (vagy hozzon létre egy új üres konzolalkalmazást), és válassza a **Windows**, **Immediate** a a **Debug** menü.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

A felhasználó nevét és az adattár helye együtt a kódolt jelszó használatával a git-paranccsal hozhat létre.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

Ha a tárház klónozták, megtekintheti és a helyi fájl rendszerben dolgozni. További információkért lásd: [fájlok és mappák szerkezetének hivatkozást a helyi Git-tárház](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>A helyi tárház a legfrissebb szolgáltatáspéldány konfigurációjának frissítéséhez

Ha módosítja az Azure Portalon vagy a REST API használatával az API Management szolgáltatáspéldányt, mentenie kell ezeket a módosításokat a tárházba a legutóbbi változtatásokat a saját helyi tárházában frissítése előtt. Ehhez kattintson **mentés konfigurációs adattárhoz** a a **konfigurációs adattárhoz** lapra az Azure Portalon, és hogyan adhat ki az alábbi parancsot a helyi tárházban.

```
git pull
```

Futtatása előtt `git pull` ellenőrizze, hogy a mappa a helyi tárházhoz. Ha befejezte a `git clone` parancsot, akkor módosítania kell a címtár az adattárhoz az alábbihoz hasonló parancs futtatásával.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>A helyi tárház változásainak leküldése a kiszolgáló-adattárat a
Küldje le változások a helyi adattárból a kiszolgáló adattárba, mentse a módosításokat, és juttathatja el őket a kiszolgáló-tárházba. A módosítások véglegesítéséhez, nyissa meg a Git-paranccsal eszközt, lépjen abba a könyvtárba, a helyi tárház és adja ki a következő parancsokat.

```
git add --all
git commit -m "Description of your changes"
```

Továbbítsa az összes véglegesítés arra a kiszolgálóra, futtassa a következő parancsot.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>A szolgáltatás konfigurációs változásokat az API Management-szolgáltatáspéldány üzembe helyezése

Ha a helyi módosítások véglegesítése és leküldte a kiszolgáló tárház, az API Management szolgáltatáspéldányhoz telepíthetné őket.

Információ a REST API használatával a művelet végrehajtása: [üzembe helyezése a Git változik a REST API-val konfigurációs adatbázis](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Fájl- és helyi Git-tárház szerkezete-referencia

A fájlok és mappák, a helyi git-tárházban a konfigurációs információkat tartalmaznak arról a szolgáltatáspéldányt.

| Elem | Leírás |
| --- | --- |
| api-felügyeleti gyökérmappa |A szolgáltatáspéldány legfelső szintű konfigurációját tartalmazza |
| API-k mappa |Az API-k a szolgáltatáspéldány konfigurációt tartalmazza |
| csoportok mappa |A csoportokat a szolgáltatáspéldány konfigurációt tartalmazza |
| házirend mappa |A szolgáltatás példánya a szabályzatokat tartalmaz |
| portalStyles mappa |A fejlesztői portál testreszabása a szolgáltatáspéldány konfigurációt tartalmazza |
| termékek mappa |A termékek a szolgáltatáspéldány konfigurációt tartalmazza |
| sablonok mappa |Az e-mail-sablonok, a szolgáltatás-példányában konfigurációt tartalmazza |

Minden mappa tartalmazhat egy vagy több fájlt, és olykor egy vagy több mappát, például egy mappát az egyes API-t, a termék vagy a csoport. Minden egyes mappában levő fájlok konkrétan az entitástípus leírtak szerint a mappa nevét.

| Fájltípus | Cél |
| --- | --- |
| JSON |Konfigurációs információkat az adott entitás |
| HTML |Entitás, a fejlesztői portál gyakran megjelenik leírásai |
| xml |Házirend-utasítások |
| CSS |A fejlesztői portál testreszabása stíluslapok |

Ezeket a fájlokat létrehozása, törlése, szerkeszteni és a helyi fájlrendszerben és a módosításokat vissza az API Management-szolgáltatáspéldány üzembe felügyelt.

> [!NOTE]
> A következő entitásokat nem találhatók meg a Git-tárházban, és a Git használatával nem konfigurálható.
> 
> * Felhasználók
> * Előfizetések
> * Tulajdonságok
> * Fejlesztői portál entitások eltérő stílusok
> 

### <a name="root-api-management-folder"></a>api-felügyeleti gyökérmappa
A legfelső szintű `api-management` mappa tartalmaz egy `configuration.json` fájlt, amely a szolgáltatás-példánya a következő formátumban legfelső szintű információkat tartalmaz.

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
    "DelegationValidationKey": ""
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

Az első négy beállítások (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, és `UserRegistrationTermsConsentRequired`) az alábbi beállításokat a térkép a **identitások** lapján a **biztonsági** szakaszban.

| Identitás beállítása | A Maps |
| --- | --- |
| RegistrationEnabled |**Névtelen felhasználók átirányítása a bejelentkezési oldal** jelölőnégyzet |
| UserRegistrationTerms |**Használati feltételek felhasználói regisztrációkor** szövegmező |
| UserRegistrationTermsEnabled |**Használati feltételek megjelenítése a regisztrációs lapon** jelölőnégyzet |
| UserRegistrationTermsConsentRequired |**Jóváhagyás szükséges** jelölőnégyzet |

A következő négy beállításokat (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, és `DelegationValidationKey`) az alábbi beállításokat a térkép a **delegálás** lapján a **biztonsági** szakaszban.

| Delegálási beállítás | A Maps |
| --- | --- |
| DelegationEnabled |**Bejelentkezés és regisztráció delegálása** jelölőnégyzet |
| DelegationUrl |**Delegálási végpont URL-címe** szövegmező |
| DelegatedSubscriptionEnabled |**Termék-előfizetés delegálása** jelölőnégyzet |
| DelegationValidationKey |**Érvényesítési kulcs delegálása** szövegmező |

A végső beállítás `$ref-policy`, leképezi a globális szabályzat utasítások fájlt a szolgáltatáspéldány számára.

### <a name="apis-folder"></a>API-k mappa
A `apis` mappa tartalmaz egy mappát az egyes API-t a szolgáltatás példánya, amely a következő elemeket tartalmazza.

* `apis\<api name>\configuration.json` – Ez a konfiguráció az API-hoz és a háttérkiszolgáló URL-címe és a műveletek tartalmaz információkat. Ez az, hogy ugyanazokat az információkat, amelyek a rendszer visszaadna, ha hívása [beolvasása egy adott API](https://docs.microsoft.com/rest/api/apimanagement/api/get) a `export=true` a `application/json` formátumban.
* `apis\<api name>\api.description.html` – Ez az API-t leírása, amely megfelel a `description` tulajdonságát a [API-entitás](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` – Ez a mappa tartalmaz `<operation name>.description.html` fájlok, amelyek leképezik a műveletek az API-ban. Minden fájl tartalmaz, amely az API-ban egyetlen művelet leírása a `description` tulajdonságát a [művelet entitás](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) a REST API-ban.

### <a name="groups-folder"></a>csoportok mappa
A `groups` mappa tartalmaz egy mappát a szolgáltatáspéldány meghatározott csoportokra.

* `groups\<group name>\configuration.json` – Ez az a csoport konfigurációját. Ez az, hogy ugyanazokat az információkat, amelyek a rendszer visszaadna, ha hívása a [beolvasása egy adott csoport](https://docs.microsoft.com/rest/api/apimanagement/group/get) műveletet.
* `groups\<group name>\description.html` – Ez a csoport leírását, amely megfelel a `description` tulajdonságát a [entitás csoport](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>házirend mappa
A `policies` mappa tartalmazza a házirend-utasítások a szolgáltatáspéldányához.

* `policies\global.xml` – a szolgáltatáspéldány, globális hatókörű meghatározott szabályzatokat tartalmaz.
* `policies\apis\<api name>\` -Ha lett definiálva az API-hatókörben házirendeknek, szerepel ebben a mappában.
* `policies\apis\<api name>\<operation name>\` mappa – Ha rendelkezik bármilyen házirendek műveleti hatókör meg van határozva, szerepel ebben a mappában lévő `<operation name>.xml` fájlok, amelyek a házirend-utasítások minden művelethez.
* `policies\products\` – Ha rendelkezik bármilyen házirendek termék hatókör meg van határozva, ez a mappa, amely tartalmazza a benne van `<product name>.xml` fájlok, amelyek az egyes termékekhez tartozó házirend-utasítások.

### <a name="portalstyles-folder"></a>portalStyles mappa
A `portalStyles` mappa tartalmazza a konfigurációs és stílus táblázatok a fejlesztői portál testreszabása a szolgáltatáspéldány számára.

* `portalStyles\configuration.json` -a stíluslapok, a fejlesztői portál által használt nevét tartalmazza,
* `portalStyles\<style name>.css` -minden `<style name>.css` fájl tartalmazza a fejlesztői portál stílusainak (`Preview.css` és `Production.css` alapértelmezés szerint).

### <a name="products-folder"></a>termékek mappa
A `products` mappa tartalmaz egy mappát az egyes termékek, a szolgáltatáspéldány meghatározott.

* `products\<product name>\configuration.json` – Ez a termék a konfiguráció. Ez az, hogy ugyanazokat az információkat, amelyek a rendszer visszaadna, ha hívása a [beolvasása egy bizonyos termékben](https://docs.microsoft.com/rest/api/apimanagement/product/get) műveletet.
* `products\<product name>\product.description.html` – Ez a termék leírását, amely megfelel a `description` tulajdonságát a [termék entitás](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) a REST API-ban.

### <a name="templates"></a>sablonok
A `templates` mappa konfigurációt tartalmazza a [e-mail-sablonok](api-management-howto-configure-notifications.md) a példányok.

* `<template name>\configuration.json` – Ez az e-mailt sablon konfigurációját.
* `<template name>\body.html` – Ez az e-mailt sablon törzsét.

## <a name="next-steps"></a>További lépések
A szolgáltatáspéldány felügyeletének további lehetőségeiről további információért lásd:

* A szolgáltatáspéldány, a következő PowerShell-parancsmagok használatával kezelheti.
  * [Szolgáltatások üzembe helyezése – PowerShell-parancsmagok leírása](https://docs.microsoft.com/powershell/module/wds)
  * [Szolgáltatásfelügyeleti PowerShell-parancsmagok leírása](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Kezelheti a szolgáltatáspéldány, a REST API használatával
  * [API Management REST API-referencia](https://msdn.microsoft.com/library/azure/dn776326.aspx)


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




