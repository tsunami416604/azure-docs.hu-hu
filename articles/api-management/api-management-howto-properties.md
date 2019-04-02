---
title: Megnevezett értékek használata az Azure API Management házirendek
description: Ismerje meg, hogyan nevű értékeket használja az Azure API Management házirendek.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: 478b80b021b4df36e2eccc37ac9c74f75e43a5bb
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791626"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Megnevezett értékek használata az Azure API Management házirendek
Az API Management-házirendek olyan egy hatékony képesség, a rendszer, amelyek lehetővé teszik az Azure Portalon keresztül konfigurációs API-változtathatja meg. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. Házirend-utasítások szövegkonstans érték, a házirend-kifejezések, és az értékek nevű lehet létrehozni. 

Minden API Management-szolgáltatáspéldány tulajdonságok gyűjteménye, kulcs/érték párok, nevű értékek, a szolgáltatáspéldány globális nevezett rendelkezik. Ezek az értékek nevű használható állandó karakterlánc-értékek kezelheti minden API konfigurálása és szabályzatokat. Minden egyes tulajdonság veheti fel a következő attribútumokat:

| Attribútum | Typo | Leírás |
| --- | --- | --- |
| Megjelenített név |sztring |Alfanumerikus sztring, amellyel hivatkozni lehet a tulajdonságra a szabályzatokban. |
| Érték |sztring |A tulajdonság értéke. Nem lehet üres vagy állhatnak csak térközökből. |
|Titkos|logikai|Meghatározza, hogy az érték egy titkos kulcs-e, és a rendszer titkosítsa-e.|
| Címkék |sztringek tömbje |Nem kötelező, címkék, akkor használható a tulakdonságlista szűréséhez. |

![Névvel ellátott értékek](./media/api-management-howto-properties/named-values.png)

Tulajdonságértékek tartalmazhat szövegkonstansok és [házirend-kifejezések](/azure/api-management/api-management-policy-expressions). Például értékét `ExpressionProperty` egy házirend-kifejezés, amely az aktuális dátumot és időt tartalmazó karakterláncot ad vissza. A tulajdonság `ContosoHeaderValue` egy titkos kulcsot van megjelölve, ezért nem jelenik meg az értékét.

| Name (Név) | Érték | Titkos | Címkék |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False (Hamis) |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True (Igaz) |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False (Hamis) | |

## <a name="to-add-and-edit-a-property"></a>Adja hozzá, és a egy tulajdonság szerkesztése

![Tulajdonság hozzáadása](./media/api-management-howto-properties/add-property.png)

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Válassza ki **értékek nevű**.
3. Nyomja meg **+ Hozzáadás**.

   Neve és értéke szükséges értékeket. Ha ez a tulajdonság értéke egy titkos kulcsot, ellenőrizze, a titkos jelölőnégyzetet. Adjon meg egy vagy több választható címkék segítség a névvel ellátott értékek rendszerezése, majd kattintson a mentés.
4. Kattintson a **Create** (Létrehozás) gombra.

A tulajdonság létrehozása után szerkesztheti a tulajdonság kattintva. Ha módosítja a tulajdonság nevét, minden olyan szabályzatokat, hogy a tulajdonság automatikusan frissülnek az új nevét.

A REST API-val tulajdonság szerkesztési információkért lásd: [a REST API-val tulajdonság módosítása](/rest/api/apimanagement/property?Patch).

## <a name="to-delete-a-property"></a>Egy tulajdonság

Vlastnost törléséhez kattintson **törlése** törli a tulajdonság mellett.

> [!IMPORTANT]
> Ha a tulajdonságot minden olyan házirendek hivatkozik, nem lehet sikeresen törölni, amíg nem törli a tulajdonság azt használó összes szabályzatokból származó fogjuk.
> 
> 

A törölni egy tulajdonságot a REST API-val további információkért lásd: [törölni egy tulajdonságot a REST API-val](/rest/api/apimanagement/property?Delete).

## <a name="to-search-and-filter-named-values"></a>Keresés és nevű értékeinek szűrése

A **értékek nevű** lapon a keresési és szűrési lehetőségek válnak elérhetővé, amelyek segítségével kezelheti a névvel ellátott értékek is. Név tulajdonság szerint a tulakdonságlista szűréséhez, adja meg a kívánt keresőkifejezést a a **tulajdonság keresése** szövegmezőbe. Minden elnevezett értékek megjelenítéséhez törölje a jelet a **tulajdonság keresése** szövegmezőbe, majd nyomja le adja meg.

A tulajdonságlista szűréséhez címkeértékeket által, adjon meg egy vagy több címkét helyezünk el a **szűrés címkék szerint** szövegmezőbe. Minden elnevezett értékek megjelenítéséhez törölje a jelet a **szűrés címkék szerint** szövegmezőbe, majd nyomja le adja meg.

## <a name="to-use-a-property"></a>Egy tulajdonság használata

Egy házirend tulajdonságot használja, helyezze a tulajdonságnév belül zárójelek között, például a double párjai `{{ContosoHeader}}`, az alábbi példában látható módon:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában `ContosoHeader` egy fejléc a nevet használja egy `set-header` szabályzatot, és `ContosoHeaderValue` szolgál, hogy a fejléc értékeként. Ez a szabályzat kérelem vagy válasz az API Management-átjáró során kiértékelésekor `{{ContosoHeader}}` és `{{ContosoHeaderValue}}` cserélése megfelelő tulajdonságot értékekre.

Névvel ellátott értékek is használható, teljes körű attribútum vagy az elem értékeket az előző példában látható módon, de ezek is lehet szúr be vagy kombinálva egy szövegkonstans kifejezés részeként a következő példában látható módon: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Névvel ellátott értékek házirend-kifejezések is tartalmazhat. A következő példában a `ExpressionProperty` szolgál.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Ez a szabályzat kiértékelésekor `{{ExpressionProperty}}` értékének helyére: `@(DateTime.Now.ToString())`. Egy házirend-kifejezés értéke, mert a kifejezés ki lesz értékelve, és hitelesíti a házirend a végrehajtása során.

Tesztelheti ezt ki a fejlesztői portálon, amely rendelkezik egy névvel ellátott értékekkel a házirend hatókörébe művelet meghívásával. A következő példában egy műveletet hívja meg az előző két példában `set-header` házirendek névvel ellátott értékekkel. Vegye figyelembe, hogy a válasz tartalmazza-e a házirendek segítségével névvel ellátott értékekkel konfigurált, két egyéni fejlécek.

![Fejlesztői portál][api-management-send-results]

Ha megtekinti a [API Inspectorral nyomkövetési](api-management-howto-api-inspector.md) hívás, amely tartalmazza a két előző minta szabályzatok névvel ellátott értékek, tekintse meg a két `set-header` szabályzatok a tulajdonságértékeket és a házirend-kifejezés beszúrva a tulajdonság, amely a házirend-kifejezés kiértékelési.

![Nyomkövetési API Inspectorral][api-management-api-inspector-trace]

Tulajdonságértékek tartalmazhat házirend-kifejezések, míg a tulajdonságértékek nem tartalmazhat más névvel ellátott értékek. Ha egy tulajdonság rá mutató hivatkozást tartalmazó szöveg szolgál egy tulajdonság értéke például `Property value text {{MyProperty}}`, adott tulajdonsághivatkozás nem kell cserélni, és a tulajdonság értéke része lesz.

## <a name="next-steps"></a>További lépések
* További információ a házirendek használata
  * [Az API Management házirendek](api-management-howto-policies.md)
  * [Házirend-referencia](/azure/api-management/api-management-policies)
  * [Házirend-kifejezések](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

