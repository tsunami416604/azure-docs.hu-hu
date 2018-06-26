---
title: Konfigurálja a használatával a Git - Azure API Management szolgáltatást |} Microsoft Docs
description: Ismerje meg, mentse, és a Git használatával az API Management-konfigurációjának beállítása.
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
ms.openlocfilehash: 0165de82850c0c80052564c5f31a5e5cf5effb11
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938308"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Mentse, és a Git használatával az API Management-konfigurációjának beállítása

Minden API Management service-példány konfigurációs és a szolgáltatáspéldány metaadatok kapcsolatos adatokat tartalmazó konfigurációs adatbázis tárolja. Is módosítható a szolgáltatáspéldány módosításával az Azure-portál a megfelelő értéket, PowerShell-parancsmag használatával, vagy a REST API-hívással. Ezek a módszerek mellett is kezelhet, a példány konfigurációjának Git használatával, a szolgáltatás felügyeleti lehetőségeket, mint engedélyezése:

* Konfigurációs rendszerverzió - töltse le, és tárolja a szolgáltatás konfigurációs különböző verziói
* Konfigurációs módosítások tömeges - módosítja a szolgáltatás konfigurációját a helyi tárházban több részét, és a módosítások a kiszolgáló integrálása egyetlen műveletben
* Munkafolyamat - és ismerős Git toolchain használja a Git tooling és, amelyek már ismeri a munkafolyamatok

Az alábbi ábra a különböző módokon konfigurálhatja az API Management szolgáltatáspéldány áttekintését mutatja.

![Git konfigurálása][api-management-git-configure]

Ha módosítja a szolgáltatás az Azure-portálon, PowerShell-parancsmagokkal vagy a REST API-t használó, kezelt a szolgáltatás konfigurációs adatbázis használata a `https://{name}.management.azure-api.net` végpont, a diagram jobb oldalán látható módon. Bal oldalán található a diagram bemutatja, hogyan kezelheti a Git használatával konfigurációjának és a szolgáltatás Git-tárházban található `https://{name}.scm.azure-api.net`.

Az alábbi lépések nyújtanak a Git használatával az API Management szolgáltatáspéldány kezelését.

1. A szolgáltatás a Git-konfiguráció
2. A szolgáltatás konfigurációs adatbázis mentse a Git-tárház
3. A helyi számítógépen a Git-tárház klónozása
4. A legújabb tárház le a helyi számítógép és a véglegesítési és leküldéses módosításokat visszavonni a tárházban
5. A módosítások a tárházból üzembe helyezés a szolgáltatás konfigurációs adatbázis

Ez a cikk ismerteti, hogyan lehet engedélyezése és a Git segítségével kezelheti a szolgáltatás konfigurációját, és nyújt a fájlok és mappák a Git-tárházban.

## <a name="access-git-configuration-in-your-service"></a>A szolgáltatás a Git-konfiguráció

Tekintheti meg és konfigurálhatja a Git-konfigurációs beállításokat, kattintson a **biztonsági** menüt, és keresse meg a **konfigurációs tárház** fülre.

![GIT engedélyezése][api-management-enable-git]

> [!IMPORTANT]
> Bármely kulcsait, tulajdonságait a tárházban fogja tárolni, és az előzmények marad, amíg meg nem definiált tiltsa le, és engedélyezze újra a Git-hozzáférés. Tulajdonságok adjon meg egy biztonságos helyen kezelhetők a állandó karakterlánc-értékek, többek között a titkos kulcsok, összes API konfigurálása és házirendek, így nem kell közvetlenül a házirend-utasításoknál letölthetők. További információkért lásd: [tulajdonságok használata az Azure API-felügyeleti házirendek](api-management-howto-properties.md).
> 
> 

Engedélyezésével vagy letiltásával Git hozzáférés a REST API használatával kapcsolatos tudnivalókért lásd: [engedélyezheti vagy letilthatja a REST API használatával Git hozzáférést](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>A szolgáltatáskonfiguráció a Git-tárházba mentéséhez

Az első lépéseket, mielőtt a tárház klónozása, hogy a szolgáltatáskonfiguráció a jelenlegi állapotában a tárházba mentéséhez. Kattintson a **tárházba mentéséhez**.

Adja meg a kívánt módosításokat a megerősítő képernyőn a, és kattintson a **Ok** mentéséhez.

A konfiguráció mentése után néhány percet, és az állapotot a tárház jelenik meg, beleértve a dátum és idő az utolsó konfigurációváltozás és a szolgáltatás konfigurációját és a tárház között a legutóbbi szinkronizálás.

Ha a tárház menti a konfigurációt, akkor klónozható.

Információ a REST API használatával a művelet végrehajtása: [véglegesítési konfiguráció pillanatkép-REST API használatával](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Klónozza a tárházat a helyi számítógép számára

Klónozza a tárházat, kell az URL-cím a tárház, a felhasználónevet és jelszót. Ahhoz, hogy a felhasználó nevét és más hitelesítő adatokat, kattintson a **hozzáférési hitelesítő adatokat** az oldal tetején.  
 
Olyan jelszót létrehozni, először győződjön meg róla, hogy a **lejárati** állítsa be a kívánt lejárati dátumát és idejét, és kattintson a **Generate**.

> [!IMPORTANT]
> Jegyezze fel ezt a jelszót. Ha bezárja ezt az oldalt a jelszó nem jelenik meg újra.
> 

Az alábbi példák a Git Bash eszközt használja [Git for Windows](http://www.git-scm.com/downloads) , de bármely, a ismeri a Git-eszközt is használhatja.

A Git eszköz nyissa meg a kívánt mappa, és futtassa a következő parancsot a helyi számítógépen, az Azure portál által szolgáltatott paranccsal git-tárház klónozása.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Adja meg a felhasználónevet és jelszót.

Ha bármilyen hiba jelentkezik, próbálja meg módosítani a `git clone` parancs tartalmazza a felhasználónevet és jelszót, a következő példában látható módon.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Ez biztosítja, hogy a hiba, ha próbálja meg a jelszót a parancs része kódolás URL-címet. Nyissa meg a Visual Studio, és adja ki a következő parancsot a egy gyors módja, ha ehhez a **parancsablakban**. Lehetőségre a **parancsablakban**, nyissa meg a bármely megoldás vagy a projektet a Visual Studio (vagy hozzon létre egy új üres konzolalkalmazást), és válassza a **Windows**, **Immediate** a a **Debug** menü.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

A kódolt jelszót használhatja a felhasználó nevét és a tárház helye a git parancs összeállításához.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

Ha a tárház klónozták tekintheti meg és a helyi rendszer használható. További információkért lásd: [fájl- és helyi Git-tárház hivatkozás szerkezeti](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>A helyi tárház frissítheti a legfrissebb példányának konfigurációja

Ha módosítja a API-kezelés szolgáltatás példányát az Azure-portálon vagy REST API használatával, mentenie kell ezeket a módosításokat a tárházba a helyi tárház legújabb módosításainak frissítése előtt. Ehhez kattintson **mentés konfigurációs tárházba** a a **konfigurációs tárház** az Azure portálon lapot, és hogyan adhat ki az alábbi parancsot a helyi tárházba.

```
git pull
```

Futtatása előtt `git pull` érdekében, hogy a mappa a helyi tárház. Ha befejezte a `git clone` parancsot, majd módosítsa a könyvtárat a tárház a következő parancs futtatásával.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Az a helyi tárház változásainak leküldése a kiszolgáló-tárház
A tárház változásainak leküldése a helyi tárházból a kiszolgáló, meg kell a változtatások véglegesítése a határidő, majd küldje le őket a kiszolgáló tárház. A módosítások véglegesítéséhez, a Git parancs eszköz, a helyi tárház directory váltani, és a következő parancsokat.

```
git add --all
git commit -m "Description of your changes"
```

Kiszolgálóra történő leküldésére összes a véglegesíti a, a következő parancsot.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>A szolgáltatás konfigurációs változásokat az API Management szolgáltatáspéldány központi telepítése

Ha a helyi módosításokat vannak, és a kiszolgáló tárházba leküldött, az API Management szolgáltatáspéldány telepíthetné őket.

Információ a REST API használatával a művelet végrehajtása: [telepítése Git vált, a REST API használatával konfigurációs adatbázis](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Fájl- és helyi Git-tárház szerkezete-hivatkozás

A fájlok és mappák a helyi git-tárházban tartalmaz a szolgáltatáspéldány konfigurációs adatait.

| Elem | Leírás |
| --- | --- |
| api-felügyeleti gyökérmappa |A szolgáltatáspéldány legfelső szintű konfigurációját tartalmazza |
| API-k mappa |Az API-k a szolgáltatáspéldány a konfigurációját tartalmazza |
| csoportok mappa |A szolgáltatáspéldány a csoportok konfigurációját tartalmazza |
| házirend mappa |A szolgáltatáspéldány házirendjeinek tartalmazza |
| portalStyles mappa |A fejlesztői portálon a testreszabások a szolgáltatáspéldány a konfigurációját tartalmazza |
| termékek mappa |A szolgáltatáspéldány a termék konfigurációját tartalmazza |
| sablonok mappa |Az e-mail sablonok a szolgáltatáspéldány a konfigurációját tartalmazza |

Minden mappa tartalmazhat egy vagy több fájlt, és bizonyos esetekben egy vagy több mappát, például egy mappát az egyes API-t, a termék vagy a csoport. A fájlok minden egyes mappákban lévő mappa nevével entitástípus vonatkoznak.

| Fájltípus | Cél |
| --- | --- |
| JSON |A megfelelő entitás konfigurációs adatait |
| HTML |Entitás, gyakran megjelenik a fejlesztői portálon leírása |
| xml |Házirend-utasítások |
| CSS |A fejlesztői portálon testreszabáshoz stíluslapok |

Ezeket a fájlokat létrehozása, törlése, szerkeszthető, és a helyi fájlrendszerben felügyelt, és a telepített módosítások a az API Management szolgáltatáspéldány.

> [!NOTE]
> A következő entitások nem találhatók a Git-tárházban, és nem lehet konfigurálni a Git használatával.
> 
> * Felhasználók
> * Előfizetések
> * Tulajdonságok
> * Fejlesztői portálon entitások eltérő stílusok
> 

### <a name="root-api-management-folder"></a>Api-felügyeleti gyökérmappa
A legfelső szintű `api-management` a mappa tartalmaz egy `configuration.json` a szolgáltatáspéldány, a következő formátumban legfelső szintű információkat tartalmazó fájlt.

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

Az első négy beállítások (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, és `UserRegistrationTermsConsentRequired`) leképezése a következő beállításokat, a a **identitások** lapra a **biztonsági** szakasz.

| Azonosító beállítása | Van leképezve |
| --- | --- |
| RegistrationEnabled |**A névtelen felhasználók átirányítása bejelentkezési oldal** jelölőnégyzet |
| UserRegistrationTerms |**Használati feltételek a felhasználói regisztráció** szövegmező |
| UserRegistrationTermsEnabled |**Használati feltételek megjelenítése a bejelentkezési lapon** jelölőnégyzet |
| UserRegistrationTermsConsentRequired |**Hozzájárulás szükséges** jelölőnégyzet |

A következő négy beállítások (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, és `DelegationValidationKey`) leképezése a következő beállításokat, a a **delegálás** lapra a **biztonsági** szakasz.

| Delegálási beállítás | Van leképezve |
| --- | --- |
| DelegationEnabled |**Delegált bejelentkezési és regisztrációs** jelölőnégyzet |
| DelegationUrl |**Delegálás végponti URL-cím** szövegmező |
| DelegatedSubscriptionEnabled |**Termék-előfizetéshez delegálása** jelölőnégyzet |
| DelegationValidationKey |**Érvényesítési kulcs delegálása** szövegmező |

A végső beállítás `$ref-policy`, a globális utasítások házirendfájl a szolgáltatáspéldány van leképezve.

### <a name="apis-folder"></a>API-k mappa
A `apis` mappa tartalmaz egy mappát minden API-nak a szolgáltatáspéldány, amely a következő elemeket tartalmazza.

* `apis\<api name>\configuration.json` – Ez a konfiguráció az API-hoz és a háttérkiszolgáló URL-címe és a műveletek tartalmaz információkat. Ez az ugyanazokat az információkat, akkor adja vissza, ha hívása [egy meghatározott API beszerzésének](https://docs.microsoft.com/en-us/rest/api/apimanagement/api/get) rendelkező `export=true` a `application/json` formátumban.
* `apis\<api name>\api.description.html` -Ez az API leírása, megfelel-e a `description` tulajdonsága a [API entitás](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` – Ez a mappa tartalmaz `<operation name>.description.html` fájlokat, a műveletek az API-ban van leképezve. Minden fájl tartalmazza az API-ban, amely egyetlen műveletben leírása a `description` tulajdonsága a [művelet entitás](https://docs.microsoft.com/en-us/rest/api/visualstudio/operations/list#operationproperties) a REST API-ban.

### <a name="groups-folder"></a>csoportok mappa
A `groups` mappa tartalmaz egy mappát az egyes csoportokhoz, a szolgáltatáspéldány definiálva.

* `groups\<group name>\configuration.json` -a csoport beállítani. Ez az ugyanazokat az információkat, akkor adja vissza, ha hívása a [egy adott csoport lekérése](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) műveletet.
* `groups\<group name>\description.html` – Ez a csoport leírását, és megfelel-e a `description` tulajdonsága a [entitás csoport](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>házirend mappa
A `policies` mappa tartalmazza a szolgáltatáspéldány a házirend-utasításoknál.

* `policies\global.xml` -a szolgáltatáspéldány globális hatókörben meghatározott házirendek szerint tartalmazza.
* `policies\apis\<api name>\` -Ha bármely API hatókörből meghatározott házirendek szerint, akkor ebben a mappában találhatók.
* `policies\apis\<api name>\<operation name>\` mappa - Ha minden házirendet a hatókör művelet van megadva, akkor az a mappában található `<operation name>.xml` fájlok, amelyek a házirend-utasításoknál minden művelethez.
* `policies\products\` -Ha minden házirendet a termék hatókörben van megadva, ezt tartalmazza ezt a mappát, amelybe `<product name>.xml` fájlokat, a házirend-utasításoknál az egyes termékek van leképezve.

### <a name="portalstyles-folder"></a>portalStyles mappa
A `portalStyles` mappa tartalmazza a konfigurációs és stílus lapok developer portálon testreszabni a szolgáltatáspéldány.

* `portalStyles\configuration.json` -a stíluslapok, a fejlesztői portál által használt nevét tartalmazza,
* `portalStyles\<style name>.css` -minden `<style name>.css` fájl tartalmazza a fejlesztői portálhoz stílusok (`Preview.css` és `Production.css` alapértelmezés szerint).

### <a name="products-folder"></a>termékek mappa
A `products` mappa tartalmaz egy mappát az egyes termékek, a szolgáltatáspéldány definiálva.

* `products\<product name>\configuration.json` -a a termék beállítani. Ez az ugyanazokat az információkat, akkor adja vissza, ha hívása a [beolvasni a termék](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) műveletet.
* `products\<product name>\product.description.html` – Ez a termék, és megfelel-e a `description` tulajdonsága a [termék entitás](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) a REST API-ban.

### <a name="templates"></a>sablonok
A `templates` mappa konfigurációját tartalmazza a [e-mail sablonok](api-management-howto-configure-notifications.md) service-példány.

* `<template name>\configuration.json` -az e-mail sablon beállítani.
* `<template name>\body.html` -Ez az az e-mail sablon szövegtörzsét.

## <a name="next-steps"></a>További lépések
Más módokon kezelheti a szolgáltatáspéldány információkért lásd:

* A szolgáltatáspéldány, a következő PowerShell-parancsmagok használatával kezelheti.
  * [Szolgáltatások üzembe helyezése – PowerShell-parancsmagok leírása](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [Szolgáltatás-felügyelet PowerShell parancsmag-referencia](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* A szolgáltatáspéldány, a REST API használatával kezelése
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




