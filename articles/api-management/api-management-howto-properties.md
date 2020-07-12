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
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 28a9f37f58a6c056bf23a85fcf2641f407988891
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243477"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Nevesített értékek használata az Azure API Management-házirendekben

API Management házirendek a rendszer hatékony funkciója, amely lehetővé teszi, hogy a Azure Portal a konfiguráción keresztül megváltoztassa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A házirend-utasítások literális szöveges értékekkel, házirend-kifejezésekkel és elnevezett értékekkel állíthatók össze.

Minden API Management Service-példányhoz kulcs/érték párok gyűjteménye tartozik, amely neve named Values, amely globális a szolgáltatási példány számára. A gyűjtemény elemeinek száma nincs korlátozva. A nevesített értékek használatával állandó karakterlánc-értékeket kezelhet az összes API-konfigurációban és-házirendben. Minden megnevezett érték a következő tulajdonságokkal rendelkezhet:

| Attribútum      | Típus            | Description                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | sztring          | A megnevezett értékre hivatkozik a szabályzatokban. Egy 256 karakterből álló karakterlánc. Csak betűket, számokat, pontokat és kötőjeleket lehet engedélyezni. |
| `Value`        | sztring          | Tényleges érték. Nem lehet üres, és nem állhat csak szóközökből. Legfeljebb 4096 karakter hosszú lehet.                                        |
| `Secret`       | logikai         | Meghatározza, hogy az érték titkos-e, és hogy titkosítva legyen-e.                                                               |
| `Tags`         | sztringek tömbje | A megnevezett értékek listájának szűrésére szolgál. Legfeljebb 32 címkével.                                                                                    |

![Névvel ellátott értékek](./media/api-management-howto-properties/named-values.png)

A nevesített értékek literál karakterláncokat és [házirend-kifejezéseket](./api-management-policy-expressions.md)tartalmazhatnak. Az értéke például `Expression` egy olyan házirend-kifejezés, amely az aktuális dátumot és időpontot tartalmazó karakterláncot ad vissza. A megnevezett érték `Credential` titkosként van megjelölve, ezért az értéke alapértelmezés szerint nem jelenik meg.

| Név       | Érték                      | Titkos | Címkék          |
| ---------- | -------------------------- | ------ | ------------- |
| Érték      | 42                         | Hamis  | létfontosságú számok |
| Hitelesítő adat | ••••••••••••••••••••••     | Igaz   | biztonság      |
| Kifejezés | @ (DateTime. Now. ToString ()) | Hamis  |               |

> [!NOTE]
> Egy API Management szolgáltatásban tárolt névvel ellátott értékek helyett a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) szolgáltatásban tárolt értékeket használhatja, ahogy azt a [példa](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)mutatja.

## <a name="to-add-and-edit-a-named-value"></a>Megnevezett érték hozzáadása és szerkesztése

![Megnevezett érték hozzáadása](./media/api-management-howto-properties/add-property.png)

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Válassza a **nevesített értékek**lehetőséget.
3. Kattintson a **+ Hozzáadás**gombra.

    A név és az érték kötelező érték. Ha az érték titkos, jelölje be a _titkos_ jelölőnégyzetet. Adjon meg egy vagy több opcionális címkét, amely segítséget nyújt az elnevezett értékek megszervezésében, majd kattintson a Mentés gombra.

4. Kattintson a **Létrehozás** lehetőségre.

A megnevezett érték létrehozása után a szerkesztéséhez kattintson rá. Ha megváltoztatja a megnevezett érték nevét, a rendszer automatikusan frissíti az adott nevesített értékre hivatkozó házirendeket az új név használatára.

## <a name="to-delete-a-named-value"></a>Megnevezett érték törlése

Megnevezett érték törléséhez kattintson a **Törlés** lehetőségre a nevesített érték mellett.

> [!IMPORTANT]
> Ha a nevesített érték bármely házirendre hivatkozik, akkor nem tudja sikeresen törölni, amíg el nem távolítja a megnevezett értéket az összes olyan házirendből, amelyik azt használja.

## <a name="to-search-and-filter-named-values"></a>Névvel ellátott értékek keresése és szűrése

Az **elnevezett értékek** lapon keresési és szűrési lehetőségek találhatók a megnevezett értékek kezelésének megkönnyítésére. A nevesített értékek listájának név alapján történő szűréséhez írjon be egy keresési kifejezést a **keresési tulajdonság** szövegmezőbe. Az összes megnevezett érték megjelenítéséhez törölje a jelet a **keresési tulajdonság** szövegmezőből, és nyomja le az ENTER billentyűt.

Ha címkével szeretné szűrni a listát, adjon meg egy vagy több címkét a **Filter by Tags** szövegmezőbe. Az összes megnevezett érték megjelenítéséhez törölje a **szűrés címkék alapján** szövegmezőt, majd nyomja le az ENTER billentyűt.

## <a name="to-use-a-named-value"></a>Megnevezett érték használata

Ha megnevezett értéket szeretne használni egy szabályzatban, helyezze a nevét egy dupla pár kapcsos zárójelbe `{{ContosoHeader}}` , ahogy az a következő példában látható:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában `ContosoHeader` a rendszer a szabályzat fejlécének neveként használja `set-header` , és `ContosoHeaderValue` a fejléc értékeként használja. Ha ezt a házirendet az API Management-átjáróra vonatkozó kérelem vagy válasz alapján értékeli ki, a `{{ContosoHeader}}` `{{ContosoHeaderValue}}` rendszer a megfelelő értékekkel helyettesíti a szabályzatot.

Az elnevezett értékek teljes attribútumként vagy elemként használhatók, ahogy az az előző példában is látható, de az alábbi példában látható módon egy literális kifejezés egy részébe is beilleszthető vagy kombinálható:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

A nevesített értékek házirend-kifejezéseket is tartalmazhatnak. A következő példában a `ExpressionProperty` használatban van.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

A szabályzat kiértékelése után a `{{ExpressionProperty}}` rendszer a következő értékkel cseréli le: `@(DateTime.Now.ToString())` . Mivel az érték egy házirend-kifejezés, a rendszer kiértékeli a kifejezést, és a szabályzat végrehajtásával folytatja.

Ezt kipróbálhatja a fejlesztői portálon egy olyan művelet meghívásával, amelynek a hatókörében megnevezett értékekkel rendelkező házirend található. A következő példában egy műveletet kell meghívni a két korábbi `set-header` , nevesített értékekkel rendelkező házirenddel. Vegye figyelembe, hogy a válasz két olyan egyéni fejlécet tartalmaz, amelyek nevesített értékekkel rendelkező házirendek használatával lettek konfigurálva.

![Fejlesztői portál][api-management-send-results]

Ha megtekinti az [API Inspector nyomkövetését](api-management-howto-api-inspector.md) egy olyan híváshoz, amely tartalmazza az elnevezett értékeket tartalmazó két korábbi minta szabályzatot, akkor a két, `set-header` beszúrt értékkel rendelkező szabályzatot, valamint a házirend kifejezés kiértékelését a házirend kifejezését tartalmazó megnevezett értékre is megtekintheti.

![API Inspector nyomkövetés][api-management-api-inspector-trace]

Míg a nevesített értékek tartalmazhatnak házirend-kifejezéseket, nem tartalmazhatnak más nevesített értékeket. Ha egy megnevezett értékű hivatkozást tartalmazó szöveget használ egy értékhez, például a `Text: {{MyProperty}}` hivatkozást, a hivatkozás nem lesz feloldva és lecserélve.

## <a name="next-steps"></a>Következő lépések

-   További információ a szabályzatok használatáról
    -   [Szabályzatok API Management](api-management-howto-policies.md)
    -   [Házirend-referencia](./api-management-policies.md)
    -   [Házirend-kifejezések](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
