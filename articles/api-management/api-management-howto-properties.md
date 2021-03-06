---
title: Nevesített értékek használata az Azure API Management-házirendekben
description: Megtudhatja, hogyan használhatja a nevesített értékeket az Azure API Management-házirendekben. A nevesített értékek tartalmazhatnak literál karakterláncokat, házirend-kifejezéseket és Azure Key Vault tárolt titkos kulcsokat.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 12/14/2020
ms.author: apimpm
ms.openlocfilehash: 4cde4dadee33ec1c3f91ab4770dbfe697289cef3
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504732"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Nevesített értékek használata az Azure API Management-házirendekben

[API Management a házirendek](api-management-howto-policies.md) a rendszer hatékony funkciója, amely lehetővé teszi a közzétevő számára, hogy konfiguráción változtassa meg az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A házirend-utasítások literális szöveges értékekkel, házirend-kifejezésekkel és elnevezett értékekkel állíthatók össze.

A *nevesített értékek* a név/érték párok globális gyűjteménye minden API Management-példányban. A gyűjtemény elemeinek száma nincs korlátozva. A nevesített értékek használatával állandó karakterlánc-értékeket és titkos kulcsokat kezelhet az összes API-konfiguráció és-szabályzat között. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Megnevezett értékek a Azure Portal":::

## <a name="value-types"></a>Értékek típusai

|Típus  |Description  |
|---------|---------|
|Egyszerű     |  Literális karakterlánc vagy házirend kifejezése     |
|Titkos     |   A API Management által titkosított literális karakterlánc vagy házirend-kifejezés      |
|[Key Vault](#key-vault-secrets)     |  Egy Azure Key vaultban tárolt titok azonosítója.      |

Az egyszerű értékek vagy titkos kódok tartalmazhatnak [házirend-kifejezéseket](./api-management-policy-expressions.md). A kifejezés például `@(DateTime.Now.ToString())` egy karakterláncot ad vissza, amely az aktuális dátumot és időt tartalmazza.

Az elnevezett érték attribútumaival kapcsolatos részletekért tekintse meg a API Management [REST API-referenciát](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate).

## <a name="key-vault-secrets"></a>Key Vault-titkok

A titkos értékek tárolhatók titkosított karakterláncként API Managementban (egyéni titok), vagy [Azure Key Vault](../key-vault/general/overview.md)a titkos kulcsokra hivatkozva. 

A Key Vault-titkok használata ajánlott, mert segít javítani a API Management biztonságot:

* A Key vaultokban tárolt titkos kódok újra felhasználhatók a szolgáltatások között
* A részletes [hozzáférési szabályzatok](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) a titkokra alkalmazhatók
* A Key vaultban frissített titkokat API Management automatikusan elforgatja a rendszer. A Key vaultban történt frissítés után a rendszer 4 órán belül frissíti API Managementban megnevezett értéket. 

### <a name="prerequisites-for-key-vault-integration"></a>A Key Vault-integráció előfeltételei

1. A kulcstartó létrehozásának lépéseiért lásd: gyors útmutató [: kulcstartó létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md).
1. A rendszer által hozzárendelt vagy felhasználó által hozzárendelt [felügyelt identitás](api-management-howto-use-managed-service-identity.md) engedélyezése a API Management-példányban.
1. Rendeljen hozzá egy [Key Vault-hozzáférési szabályzatot](../key-vault/general/assign-access-policy-portal.md) a felügyelt identitáshoz, amely engedélyekkel rendelkezik a titkos kódok lekéréséhez és listázásához a tárból. A szabályzat hozzáadása:
    1. A portálon navigáljon a kulcstartóhoz.
    1. Válassza a **beállítások > hozzáférési szabályzatok > + hozzáférési házirend hozzáadása** lehetőséget.
    1. Válassza a **titkos engedélyek**, majd a **beolvasás** és a **lista** lehetőséget.
    1. A **rendszerbiztonsági tag kiválasztása** területen válassza ki a felügyelt identitás erőforrásának nevét. Ha rendszer által hozzárendelt identitást használ, a rendszerbiztonsági tag a API Management példányának neve.
1. Hozzon létre vagy importáljon egy titkos kulcsot a kulcstartóba. Lásd [: gyors útmutató: Azure Key Vault titkos kód beállítása és beolvasása a Azure Portal használatával](../key-vault/secrets/quick-create-portal.md).

A Key Vault-titok használatához [adjon hozzá vagy szerkesszen egy megnevezett értéket](#add-or-edit-a-named-value), és adja meg a **Key Vault** típusát. Válassza ki a titkos kulcsot a Key vaultból.

> [!CAUTION]
> Ha API Management Key Vault-titkot használ, ügyeljen arra, hogy ne törölje a Key Vault eléréséhez használt titkos kulcsot, kulcstartót vagy felügyelt identitást.

Ha [Key Vault tűzfal](../key-vault/general/network-security.md) engedélyezve van a kulcstartón, a következő további követelmények vonatkoznak a Key Vault-titkok használatára:

* A Key Vault eléréséhez a API Management példány **rendszer által hozzárendelt** felügyelt identitását kell használnia.
* Key Vault tűzfalon engedélyezze a **megbízható Microsoft-szolgáltatások számára a tűzfal megkerülésének engedélyezése** beállítást.

Ha a API Management példány egy virtuális hálózaton van telepítve, konfigurálja a következő hálózati beállításokat is:
* A API Management alhálózaton Azure Key Vault engedélyezése a [szolgáltatási végpontoknak](../key-vault/general/overview-vnet-service-endpoints.md) .
* Konfiguráljon egy hálózati biztonsági csoport (NSG) szabályt, hogy engedélyezze a kimenő forgalmat a AzureKeyVault és a AzureActiveDirectory [szolgáltatás címkéi](../virtual-network/service-tags-overview.md)számára. 

Részletekért lásd: hálózati konfiguráció részletei a [Kapcsolódás virtuális hálózathoz](api-management-using-with-vnet.md#-common-network-configuration-issues).

## <a name="add-or-edit-a-named-value"></a>Megnevezett érték hozzáadása vagy szerkesztése

### <a name="add-a-key-vault-secret"></a>Key Vault-titok hozzáadása

Lásd: [a Key Vault-integráció előfeltételei](#prerequisites-for-key-vault-integration).

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Az **API**-k területen válassza a **nevesített értékek**  >  **+ Hozzáadás** lehetőséget.
1. Adja meg a **név** azonosítóját, és adjon meg egy **megjelenítendő nevet** , amely a tulajdonságra hivatkozik a szabályzatokban.
1. Az **érték típusa mezőben** válassza a **Key Vault** elemet.
1. Adja meg egy Key Vault-titok azonosítóját (verzió nélkül), vagy válassza a **kijelölés** lehetőséget egy kulcstartó titkos kódjának kiválasztásához.
    > [!IMPORTANT]
    > Ha saját maga adja meg a Key Vault titkos azonosítóját, győződjön meg róla, hogy nem rendelkezik a verzióval kapcsolatos információkkal. Ellenkező esetben a titkos kulcs nem forog automatikusan API Management a Key Vault frissítését követően.
1. Az **ügyfél identitása** területen válasszon ki egy rendszer által hozzárendelt vagy egy meglévő felhasználó által hozzárendelt felügyelt identitást. Ismerje meg, hogyan [adhat hozzá vagy módosíthat felügyelt identitásokat a API Management szolgáltatásban](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Az identitásnak jogosultsággal kell rendelkeznie a Key vaultban található titkos kódok beolvasásához és listázásához. Ha még nem konfigurálta a Key vaulthoz való hozzáférést, API Management felszólítja, hogy automatikusan konfigurálja az identitást a szükséges engedélyekkel.
1. Adjon hozzá egy vagy több opcionális címkét a megnevezett értékek rendszerezéséhez, majd **mentse** a következőt:.
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Key Vault titkos értékének hozzáadása":::

### <a name="add-a-plain-or-secret-value"></a>Egyszerű vagy titkos érték hozzáadása

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Az **API**-k területen válassza a **nevesített értékek**  >  **+ Hozzáadás** lehetőséget.
1. Adja meg a **név** azonosítóját, és adjon meg egy **megjelenítendő nevet** , amely a tulajdonságra hivatkozik a szabályzatokban.
1. Az **érték típusa** mezőben válassza az **egyszerű** vagy a **titkos** lehetőséget.
1. Az **érték** mezőben adjon meg egy karakterlánc-vagy házirend-kifejezést.
1. Adjon hozzá egy vagy több opcionális címkét a megnevezett értékek rendszerezéséhez, majd **mentse** a következőt:.
1. Válassza a **Létrehozás** lehetőséget.

A megnevezett érték létrehozása után a név kiválasztásával szerkesztheti. Ha megváltoztatja a megjelenítendő nevet, a rendszer automatikusan frissíti az adott nevesített értékre hivatkozó házirendeket az új megjelenítendő név használatára.

## <a name="use-a-named-value"></a>Névvel ellátott érték használata

Az ebben a szakaszban szereplő példák az alábbi táblázatban látható megnevezett értékeket használják.

| Name               | Érték                      | Titkos | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Hamis  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Igaz   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Hamis  | 

Ha megnevezett értéket szeretne használni egy házirendben, a megjelenítendő nevet a kapcsos zárójelek közé helyezheti, `{{ContosoHeader}}` ahogy az a következő példában látható:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában `ContosoHeader` a rendszer a szabályzat fejlécének neveként használja `set-header` , és `ContosoHeaderValue` a fejléc értékeként használja. Ha ezt a házirendet az API Management-átjáróra vonatkozó kérelem vagy válasz alapján értékeli ki, a `{{ContosoHeader}}` `{{ContosoHeaderValue}}` rendszer a megfelelő értékekkel helyettesíti a szabályzatot.

Az elnevezett értékek teljes attribútumként vagy elemként használhatók, ahogy az az előző példában is látható, de az alábbi példában látható módon egy literális kifejezés egy részébe is beilleszthető vagy kombinálható: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

A nevesített értékek házirend-kifejezéseket is tartalmazhatnak. A következő példában a `ExpressionProperty` kifejezés van használatban.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Ha kiértékelik ezt a házirendet, a helyére az értékét adja meg `{{ExpressionProperty}}` `@(DateTime.Now.ToString())` . Mivel az érték egy házirend-kifejezés, a rendszer kiértékeli a kifejezést, és a szabályzat végrehajtásával folytatja.

Ezt a Azure Portal vagy a [fejlesztői portálon](api-management-howto-developer-portal.md) tesztelheti úgy, hogy olyan műveletet hív meg, amelynek a hatókörében megnevezett értékekkel rendelkező szabályzat található. A következő példában egy műveletet kell meghívni a két korábbi `set-header` , nevesített értékekkel rendelkező házirenddel. Figyelje meg, hogy a válasz két olyan egyéni fejlécet tartalmaz, amelyek nevesített értékekkel rendelkező házirendek használatával lettek konfigurálva.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API-válasz tesztelése":::

Ha megtekinti a kimenő [API-nyomkövetést](api-management-howto-api-inspector.md) egy olyan híváshoz, amely tartalmazza a két korábbi, nevesített értékkel rendelkező minta szabályzatot, akkor a két, `set-header` beszúrt értékkel rendelkező szabályzatot, valamint a házirend kifejezés kiértékelését a házirend kifejezését tartalmazó megnevezett értékre.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API Inspector nyomkövetés":::

> [!CAUTION]
> Ha egy házirend Azure Key Vault titkos kulcsra hivatkozik, akkor a kulcstartóban lévő érték látható lesz azoknak a felhasználóknak, akik hozzáférnek az [API-kérelmek nyomkövetéséhez](api-management-howto-api-inspector.md)engedélyezett előfizetésekhez.

Míg a nevesített értékek tartalmazhatnak házirend-kifejezéseket, nem tartalmazhatnak más nevesített értékeket. Ha egy megnevezett értékű hivatkozást tartalmazó szöveget használ egy értékhez, például a `Text: {{MyProperty}}` hivatkozást, a hivatkozás nem lesz feloldva és lecserélve.

## <a name="delete-a-named-value"></a>Megnevezett érték törlése

Megnevezett érték törléséhez válassza ki a nevet, majd válassza a helyi menü **Törlés** elemét (**...**).

> [!IMPORTANT]
> Ha az elnevezett érték bármely API Management házirendre hivatkozik, akkor nem törölheti, amíg el nem távolítja a megnevezett értéket az összes olyan házirendből, amelyik azt használja.

## <a name="next-steps"></a>Következő lépések

-   További információ a szabályzatok használatáról
    -   [Szabályzatok API Management](api-management-howto-policies.md)
    -   [Házirend-referencia](./api-management-policies.md)
    -   [Házirend-kifejezések](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

