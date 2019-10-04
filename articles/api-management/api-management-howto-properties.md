---
title: Nevesített értékek használata az Azure API Management-házirendekben
description: Megtudhatja, hogyan használhatja a nevesített értékeket az Azure API Management-házirendekben.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/22/2019
ms.author: apimpm
ms.openlocfilehash: d71d71c4d289235e5b67a5201c1f7417274b8fca
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072327"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Nevesített értékek használata az Azure API Management-házirendekben

API Management házirendek a rendszer hatékony funkciója, amely lehetővé teszi, hogy a Azure Portal a konfiguráción keresztül megváltoztassa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A házirend-utasítások literális szöveges értékekkel, házirend-kifejezésekkel és elnevezett értékekkel állíthatók össze.

Minden API Management Service-példányhoz kulcs/érték párokat tartalmazó tulajdonságok gyűjteménye tartozik, amely neve named Values, amely globális a szolgáltatási példány számára. A gyűjtemény elemeinek száma nincs korlátozva. A nevesített értékek használatával állandó karakterlánc-értékeket kezelhet az összes API-konfigurációban és-házirendben. Minden megnevezett érték a következő tulajdonságokkal rendelkezhet:

| Attribútum      | Type            | Leírás                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | Karakterlánc          | A tulajdonságnak a házirendekben való hivatkozására szolgál. Egy 256 karakterből álló karakterlánc. Csak betűket, számokat, pontokat és kötőjeleket lehet engedélyezni. |
| `Value`        | Karakterlánc          | Tényleges érték. Nem lehet üres, és nem állhat csak szóközökből. Legfeljebb 4096 karakter hosszú lehet.                                     |
| `Secret`       | boolean         | Meghatározza, hogy az érték titkos-e, és hogy titkosítva legyen-e.                                                            |
| `Tags`         | sztringek tömbje | A tulajdonságok listájának szűrésére szolgál. Legfeljebb 32 címkével.                                                                                    |

![Megnevezett értékek](./media/api-management-howto-properties/named-values.png)

A nevesített értékek literál karakterláncokat és [házirend](/azure/api-management/api-management-policy-expressions)-kifejezéseket tartalmazhatnak. Az értéke `Expression` például egy olyan házirend-kifejezés, amely az aktuális dátumot és időpontot tartalmazó karakterláncot ad vissza. A megnevezett `Credential` érték titkosként van megjelölve, ezért az értéke alapértelmezés szerint nem jelenik meg.

| Name (Név)       | Value                      | Secret | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| Value      | 42                         | False (Hamis)  | létfontosságú számok |
| Hitelesítő adat | ••••••••••••••••••••••     | True   | biztonság      |
| Kifejezés | @(DateTime.Now.ToString()) | False (Hamis)  |               |

## <a name="to-add-and-edit-a-property"></a>Tulajdonság hozzáadása és szerkesztése

![Tulajdonság hozzáadása](./media/api-management-howto-properties/add-property.png)

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Válassza a **nevesített értékek**lehetőséget.
3. Kattintson a **+ Hozzáadás**gombra.

    A név és az érték kötelező érték. Ha a tulajdonság értéke titkos, jelölje be a titkos jelölőnégyzetet. Adjon meg egy vagy több opcionális címkét, amely segítséget nyújt az elnevezett értékek megszervezésében, majd kattintson a Mentés gombra.

4. Kattintson a **Create** (Létrehozás) gombra.

A tulajdonság létrehozása után a tulajdonságra kattintva szerkesztheti azt. Ha megváltoztatja a tulajdonság nevét, az adott tulajdonságra hivatkozó házirendek automatikusan frissülnek az új név használatára.

A tulajdonságok a REST API használatával történő szerkesztésével kapcsolatos információkért lásd: [tulajdonság szerkesztése a REST API használatával](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>Tulajdonság törlése

Ha törölni szeretne egy tulajdonságot, kattintson a Delete (Törlés) elem melletti **törölni** kívánt tulajdonságra.

> [!IMPORTANT]
> Ha a tulajdonságot bármely házirend hivatkozik, akkor nem tudja sikeresen törölni, amíg el nem távolítja a tulajdonságot az összes olyan házirendből, amelyik azt használja.

A tulajdonságok a REST API használatával történő törlésével kapcsolatos információkért lásd: [tulajdonság törlése a REST API használatával](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Névvel ellátott értékek keresése és szűrése

Az **elnevezett értékek** lapon keresési és szűrési lehetőségek találhatók a megnevezett értékek kezelésének megkönnyítésére. Ha a tulajdonságok neve alapján szeretné szűrni a tulajdonságokat, adjon meg egy keresési kifejezést a **keresési tulajdonság** szövegmezőben. Az összes megnevezett érték megjelenítéséhez törölje a jelet a **keresési tulajdonság** szövegmezőből, és nyomja le az ENTER billentyűt.

Ha a tulajdonságokat címkéző értékek alapján szeretné szűrni, írjon be egy vagy több címkét a **Filter by Tags** szövegmezőbe. Az összes megnevezett érték megjelenítéséhez törölje a **szűrés címkék alapján** szövegmezőt, majd nyomja le az ENTER billentyűt.

## <a name="to-use-a-property"></a>Tulajdonság használata

Ha egy házirendben egy tulajdonságot szeretne használni, helyezze a tulajdonság nevét egy dupla pár kapcsos zárójelbe `{{ContosoHeader}}`, ahogy az a következő példában látható:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában `ContosoHeader` a rendszer a `set-header` szabályzat fejlécének neveként használja, és `ContosoHeaderValue` a fejléc értékeként használja. Ha ezt a házirendet az API Management-átjáróra irányuló kérelem vagy válasz során `{{ContosoHeader}}` kiértékelik, `{{ContosoHeaderValue}}` a helyük a megfelelő tulajdonságértékek alapján történik.

Az elnevezett értékek teljes attribútumként vagy elemként használhatók, ahogy az az előző példában is látható, de az alábbi példában látható módon egy literális kifejezés egy részébe is beilleszthető vagy kombinálható:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

A nevesített értékek házirend-kifejezéseket is tartalmazhatnak. A következő példában a `ExpressionProperty` használatban van.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

A szabályzat kiértékelése `{{ExpressionProperty}}` után a rendszer a következő értékkel cseréli le: `@(DateTime.Now.ToString())`. Mivel az érték egy házirend-kifejezés, a rendszer kiértékeli a kifejezést, és a szabályzat végrehajtásával folytatja.

Ezt kipróbálhatja a fejlesztői portálon egy olyan művelet meghívásával, amelynek a hatókörében megnevezett értékekkel rendelkező házirend található. A következő példában egy műveletet kell meghívni a két korábbi `set-header` , nevesített értékekkel rendelkező házirenddel. Vegye figyelembe, hogy a válasz két olyan egyéni fejlécet tartalmaz, amelyek nevesített értékekkel rendelkező házirendek használatával lettek konfigurálva.

![Fejlesztői portál][api-management-send-results]

Ha megtekinti az [API Inspector nyomkövetését](api-management-howto-api-inspector.md) egy olyan híváshoz, amely tartalmazza a két korábbi, névvel ellátott minta szabályzatot, `set-header` akkor a két házirendet láthatja a beszúrt tulajdonságértékek mellett, valamint a házirend-kifejezés kiértékelését azon tulajdonság esetében, amely a szabályzat kifejezése szerepel.

![API Inspector nyomkövetés][api-management-api-inspector-trace]

Míg a tulajdonságértékek tartalmazhatnak házirend-kifejezéseket, a tulajdonságértékek nem tartalmazhatnak más nevesített értékeket. Ha tulajdonságot tartalmazó szöveget (például) `Property value text {{MyProperty}}`használ, a tulajdonság hivatkozása nem lesz lecserélve, és a tulajdonság értéke részeként fog szerepelni.

## <a name="next-steps"></a>További lépések

-   További információ a szabályzatok használatáról
    -   [Szabályzatok API Management](api-management-howto-policies.md)
    -   [Házirend-referencia](/azure/api-management/api-management-policies)
    -   [Házirend-kifejezések](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
