---
title: Tulajdonságok használata az Azure API-felügyeleti házirendek
description: További tulajdonságok használata az Azure API-felügyeleti házirendek.
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
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197112"
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Tulajdonságok használata az Azure API-felügyeleti házirendek
API-felügyeleti házirendek, amelyek lehetővé teszik az Azure-portálon keresztül konfigurációs API működésének módosításához a rendszer hatékony képesség. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. Házirend-utasításoknál szövegkonstans értékek, a házirend-kifejezések és a tulajdonságok használatával lehet létrehozni. 

Minden API Management service-példány kulcs/érték párok, a service-példány általános tulajdonságok gyűjteményével rendelkezik. Ezek a Tulajdonságok állandó karakterlánc-értékek összes API konfigurálása és házirendek kezelésére használható. Minden egyes tulajdonsága veheti fel a következő attribútumokat:

| Attribútum | Típus | Leírás |
| --- | --- | --- |
| Megjelenített név |karakterlánc |Alfanumerikus karakterlánc, amellyel hivatkozni lehet a tulajdonságra a szabályzatokban. |
| Érték |karakterlánc |A tulajdonság értéke. Nem lehet üres vagy csak szóközök állhatnak. |
|Titkos|logikai|Határozza meg, hogy az érték egy titkos kulcsot, és titkosítani kell, vagy nem.|
| Címkék |A karakterlánc tömbje |Nem kötelező, hogy címkéket, amikor megadja a tulajdonságlista szűréséhez használható. |

![Megnevezett értékek](./media/api-management-howto-properties/named-values.png)

A tulajdonság értékek tartalmazhatnak szövegkonstansok és [házirend-kifejezések](https://msdn.microsoft.com/library/azure/dn910913.aspx). Például értékének `ExpressionProperty` egy házirend-kifejezés, amely az aktuális dátum és idő tartalmazó karakterláncot ad vissza. A tulajdonság `ContosoHeaderValue` titkos kulcs, van megjelölve, ezért az értéke nem jelenik meg.

| Name (Név) | Érték | Titkos | Címkék |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False (Hamis) |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True (Igaz) |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False (Hamis) | |

## <a name="to-add-and-edit-a-property"></a>Adhat hozzá és szerkeszthet egy tulajdonság

![Tulajdonság hozzáadása](./media/api-management-howto-properties/add-property.png)

1. Válassza ki **API-k** a alatt **API MANAGEMENT**.
2. Válassza ki **értékek nevű**.
3. Nyomja le az **+ Hozzáadás**.

  Név-érték szükséges értékeket. Ha ez a tulajdonság értéke egy titkos kulcsot, ellenőrizze az alábbi titkos jelölőnégyzetet. Adjon meg egy vagy több választható címke való rendszerezését a tulajdonságokat, és kattintson a mentés.
4. Kattintson a **Create** (Létrehozás) gombra.

A tulajdonság létrehozása után a tulajdonság kattintva szerkesztheti. Ha módosítja a tulajdonság nevét, a házirendek, amelyek az adott tulajdonsághoz hivatkoznak automatikusan frissülnek az új nevét.

A REST API használatával tulajdonság szerkesztési információkért lásd: [REST API használatával tulajdonság módosítása](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Egy tulajdonság

Törölni egy tulajdonságot, kattintson a **törlése** mellett a tulajdonság törlése.

> [!IMPORTANT]
> Ha a tulajdonság a házirendek által hivatkozott, nem fog tudni sikeresen törölni mindaddig, amíg az azt használó összes házirendektől távolítsa el a tulajdonságot.
> 
> 

A REST API használatával tulajdonság törléséről további információkért lásd: [törölni egy tulajdonságot a REST API használatával](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>A Keresés és szűrés tulajdonságai

A **értékek nevű** lapján található, a Keresés és szűrés lehetőségek közül választhat a tulajdonságainak kezeléséhez. Tulajdonság neve a tulajdonságlista szűréséhez adja meg a kívánt keresőkifejezést a a **tulajdonság keresése** szövegmező. Megjeleníti az összes tulajdonság, vagy törölje a **tulajdonság keresése** szövegmező, és nyomja le az adja meg.

Címke értékek szerint a tulajdonságlista szűréséhez adja meg egy vagy több címkéket, az a **címkék szűrés** szövegmező. Megjeleníti az összes tulajdonság, vagy törölje a **címkék szűrés** szövegmező, és nyomja le az adja meg.

## <a name="to-use-a-property"></a>A tulajdonság

Egy házirend tulajdonságot használja, helyezze a belül kell használni, például a kettős két tulajdonságnév `{{ContosoHeader}}`, a következő példában látható módon:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában `ContosoHeader` a fejléc neveként használatos a `set-header` házirend, és `ContosoHeaderValue` szolgál, hogy a fejléc értékeként. Ez a házirend olyan kérésre vagy válaszra API Management Gateway során kiértékelésekor `{{ContosoHeader}}` és `{{ContosoHeaderValue}}` váltják fel az adott tulajdonságot értékekre.

Tulajdonságok teljes attribútumot vagy elemet értékeket az előző példában látható módon használható, de is lehet beszúrt vagy kombinálva, a következő példában látható módon egy szövegkonstans kifejezés része:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Tulajdonságok házirend kifejezést is tartalmazhat. A következő példában a `ExpressionProperty` szolgál.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Ez a házirend kiértékeléséhez `{{ExpressionProperty}}` értéke helyére: `@(DateTime.Now.ToString())`. Óta egy házirend-kifejezés értéke, a rendszer kiértékeli, és a végrehajtása a házirendet, majd.

A fejlesztői portálra a kimenő tesztelheti egy műveletet, amelynek hatókörében szerepel egy házirend tulajdonságokkal meghívásával. A következő példában egy művelet hívása a két előző példa `set-header` házirendek tulajdonságokkal. Vegye figyelembe, hogy a válasz tartalmazza-e a házirendekkel tulajdonságokkal konfigurált, két egyéni fejléc.

![Fejlesztői portál][api-management-send-results]

Ha megnézi a [API Inspector nyomkövetési](api-management-howto-api-inspector.md) hívás, amely tartalmazza a két előző minta házirendek tulajdonságokkal rendelkező, lásd: a két `set-header` házirendeket és a tulajdonságértékek beszúrni, valamint a házirend-kifejezés kiértékelése a tulajdonsághoz, amely a házirend-kifejezést tartalmaz.

![API-Inspector nyomkövetési][api-management-api-inspector-trace]

Tulajdonságértékek házirend-kifejezést tartalmazhat, amíg a tulajdonságértékek nem tartalmazhat más tulajdonságok. Ha a szöveg tulajdonság a hivatkozást használja a tulajdonság értéke, például a `Property value text {{MyProperty}}`, hivatkozó tulajdonság nem lehet írni, és tartalmazzák a tulajdonság értékének a lesz.

## <a name="next-steps"></a>További lépések
* További információ a házirendek használata
  * [Az API Management házirendek](api-management-howto-policies.md)
  * [Házirend-referencia](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Házirend-kifejezések](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

