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
ms.date: 11/05/2019
ms.author: apimpm
ms.openlocfilehash: d11239aa49a53a90a38f2b5336d36cea6c97e9df
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824176"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Nevesített értékek használata az Azure API Management-házirendekben

API Management házirendek a rendszer hatékony funkciója, amely lehetővé teszi, hogy a Azure Portal a konfiguráción keresztül megváltoztassa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A házirend-utasítások literális szöveges értékekkel, házirend-kifejezésekkel és elnevezett értékekkel állíthatók össze.

Minden API Management Service-példányhoz kulcs/érték párokat tartalmazó tulajdonságok gyűjteménye tartozik, amely neve named Values, amely globális a szolgáltatási példány számára. A gyűjtemény elemeinek száma nincs korlátozva. A nevesített értékek használatával állandó karakterlánc-értékeket kezelhet az összes API-konfigurációban és-házirendben. Minden megnevezett érték a következő tulajdonságokkal rendelkezhet:

| Attribútum      | Típus            | Leírás                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | sztring          | A megnevezett értékre hivatkozik a szabályzatokban. Egy 256 karakterből álló karakterlánc. Csak betűket, számokat, pontokat és kötőjeleket lehet engedélyezni. |
| `Value`        | sztring          | Tényleges érték. Nem lehet üres, és nem állhat csak szóközökből. Legfeljebb 4096 karakter hosszú lehet.                                     |
| `Secret`       | logikai         | Meghatározza, hogy az érték titkos-e, és hogy titkosítva legyen-e.                                                            |
| `Tags`         | sztringek tömbje | A megnevezett értékek listájának szűrésére szolgál. Legfeljebb 32 címkével.                                                                                    |

![Névvel ellátott értékek](./media/api-management-howto-properties/named-values.png)

A nevesített értékek literál karakterláncokat és [házirend-kifejezéseket](/azure/api-management/api-management-policy-expressions)tartalmazhatnak. Az `Expression` értéke például egy olyan házirend-kifejezés, amely az aktuális dátumot és időpontot tartalmazó karakterláncot ad vissza. Az elnevezett érték `Credential` titkos kulcsként van megjelölve, ezért az értéke alapértelmezés szerint nem jelenik meg.

| Név       | Érték                      | Titkos | Címkék          |
| ---------- | -------------------------- | ------ | ------------- |
| Érték      | 42                         | False (Hamis)  | létfontosságú számok |
| Hitelesítő adat | ••••••••••••••••••••••     | True (Igaz)   | biztonság      |
| Kifejezés | @ (DateTime. Now. ToString ()) | False (Hamis)  |               |

## <a name="to-add-and-edit-a-named-value"></a>Megnevezett érték hozzáadása és szerkesztése

![Megnevezett érték hozzáadása](./media/api-management-howto-properties/add-property.png)

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Válassza a **nevesített értékek**lehetőséget.
3. Kattintson a **+ Hozzáadás**gombra.

    A név és az érték kötelező érték. Ha az érték titkos, jelölje be a *titkos* jelölőnégyzetet. Adjon meg egy vagy több opcionális címkét, amely segítséget nyújt az elnevezett értékek megszervezésében, majd kattintson a Mentés gombra.

4. Kattintson a **Create** (Létrehozás) gombra.

A megnevezett érték létrehozása után a szerkesztéséhez kattintson rá. Ha megváltoztatja a megnevezett érték nevét, a rendszer automatikusan frissíti az adott nevesített értékre hivatkozó házirendeket az új név használatára.

A megnevezett értékek a REST API használatával történő szerkesztésével kapcsolatos információkért lásd: [nevesített érték szerkesztése a REST API használatával](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Megnevezett érték törlése

Megnevezett érték törléséhez kattintson a **Törlés** lehetőségre a nevesített érték mellett.

> [!IMPORTANT]
> Ha a nevesített érték bármely házirendre hivatkozik, akkor nem tudja sikeresen törölni, amíg el nem távolítja a megnevezett értéket az összes olyan házirendből, amelyik azt használja.

A megnevezett értékek a REST API használatával történő törlésével kapcsolatos információkért tekintse meg a [nevesített érték törlése a REST API használatával](/rest/api/apimanagement/2019-01-01/property/delete)című témakört.

## <a name="to-search-and-filter-named-values"></a>Névvel ellátott értékek keresése és szűrése

Az **elnevezett értékek** lapon keresési és szűrési lehetőségek találhatók a megnevezett értékek kezelésének megkönnyítésére. A nevesített értékek listájának név alapján történő szűréséhez írjon be egy keresési kifejezést a **keresési tulajdonság** szövegmezőbe. Az összes megnevezett érték megjelenítéséhez törölje a jelet a **keresési tulajdonság** szövegmezőből, és nyomja le az ENTER billentyűt.

Ha címkével szeretné szűrni a listát, adjon meg egy vagy több címkét a **Filter by Tags** szövegmezőbe. Az összes megnevezett érték megjelenítéséhez törölje a **szűrés címkék alapján** szövegmezőt, majd nyomja le az ENTER billentyűt.

## <a name="to-use-a-named-value"></a>Megnevezett érték használata

Ha megnevezett értéket szeretne használni egy házirendben, helyezze a nevét egy dupla pár kapcsos zárójelbe (például `{{ContosoHeader}}`) a következő példában látható módon:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában a `ContosoHeader` egy `set-header`-házirend fejlécének neveként van használatban, és a `ContosoHeaderValue` a fejléc értékeként lesz használva. Ha ezt a házirendet az API Management-átjáróra vonatkozó kérelem vagy válasz alapján értékeli ki, `{{ContosoHeader}}` és `{{ContosoHeaderValue}}` a megfelelő értékekkel lesznek lecserélve.

A nevesített értékek teljes attribútumként vagy elemként használhatók, ahogy az az előző példában is látható, de az alábbi példában látható módon egy literális kifejezés egy részébe is beszúrhatók vagy kombinálhatók: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

A nevesített értékek házirend-kifejezéseket is tartalmazhatnak. A következő példában a rendszer a `ExpressionProperty` használja.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

A szabályzat kiértékelése után `{{ExpressionProperty}}` helyére a következő érték kerül: `@(DateTime.Now.ToString())`. Mivel az érték egy házirend-kifejezés, a rendszer kiértékeli a kifejezést, és a szabályzat végrehajtásával folytatja.

Ezt kipróbálhatja a fejlesztői portálon egy olyan művelet meghívásával, amelynek a hatókörében megnevezett értékekkel rendelkező házirend található. A következő példában egy műveletet kell meghívni az előző két példa `set-header` szabályzatok névvel ellátott értékekkel. Vegye figyelembe, hogy a válasz két olyan egyéni fejlécet tartalmaz, amelyek nevesített értékekkel rendelkező házirendek használatával lettek konfigurálva.

![Fejlesztői portál][api-management-send-results]

Ha megtekinti az [API Inspector nyomkövetését](api-management-howto-api-inspector.md) egy olyan híváshoz, amely tartalmazza a két korábbi, nevesített értékkel rendelkező minta szabályzatot, akkor a két `set-header` házirendet láthatja a beszúrt névvel, valamint a szabályzat kifejezés kiértékelése a megnevezett értékre a szabályzat kifejezése szerepel.

![API Inspector nyomkövetés][api-management-api-inspector-trace]

Míg a nevesített értékek tartalmazhatnak házirend-kifejezéseket, nem tartalmazhatnak más nevesített értékeket. Ha egy megnevezett értékű hivatkozást tartalmazó szöveg szerepel egy értékhez (például `Text: {{MyProperty}}`), a hivatkozás nem lesz feloldva és lecserélve.

## <a name="next-steps"></a>További lépések

-   További információ a szabályzatok használatáról
    -   [Szabályzatok API Management](api-management-howto-policies.md)
    -   [Házirend-referencia](/azure/api-management/api-management-policies)
    -   [Házirend-kifejezések](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
