---
title: "Tulajdonságok használata az Azure API-felügyeleti házirendek"
description: "További tulajdonságok használata az Azure API-felügyeleti házirendek."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 3b0fe2a300038e13cc488bdb4f50f8be270ea8f4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Tulajdonságok használata az Azure API-felügyeleti házirendek
API-felügyeleti házirendek, amelyek lehetővé teszik a közzétevőt úgy, hogy az API-t konfigurálással működésének módosításához a rendszer hatékony képesség. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. Házirend-utasításoknál szövegkonstans értékek, a házirend-kifejezések és a tulajdonságok használatával lehet létrehozni. 

Minden API Management service-példány kulcs/érték párok, a service-példány általános tulajdonságok gyűjteményével rendelkezik. Ezek a Tulajdonságok állandó karakterlánc-értékek összes API konfigurálása és házirendek kezelésére használható. Minden egyes tulajdonsága a következő attribútumokkal rendelkezik.

| Attribútum | Típus | Leírás |
| --- | --- | --- |
| Név |Karakterlánc |A tulajdonság nevét. Ez előfordulhat, hogy csak betűket, számokat, időszak, kötőjelet tartalmazhat, és aláhúzás karaktereket tartalmazhat. |
| Érték |Karakterlánc |A tulajdonság értéke. Nem lehet üres vagy csak szóközök állhatnak. |
| Titkos |Logikai érték |Határozza meg, hogy az érték egy titkos kulcsot, és titkosítani kell, vagy nem. |
| Címkék |A karakterlánc tömbje |Nem kötelező, hogy címkéket, amikor megadja a tulajdonságlista szűréséhez használható. |

Tulajdonságok konfigurálása a közzétevő portálon a a **tulajdonságok** fülre. A következő példában három tulajdonságainak vannak konfigurálva.

![Tulajdonságok][api-management-properties]

A tulajdonság értékek tartalmazhatnak szövegkonstansok és [házirend-kifejezések](https://msdn.microsoft.com/library/azure/dn910913.aspx). A következő táblázat az előző három minta tulajdonságok és attribútumaik. Értékének `ExpressionProperty` egy házirend-kifejezés, amely az aktuális dátum és idő tartalmazó karakterláncot ad vissza. A tulajdonság `ContosoHeaderValue` titkos kulcs, van megjelölve, ezért az értéke nem jelenik meg.

| Név | Érték | Titkos | Címkék |
| --- | --- | --- | --- |
| ContosoHeader |trackingId |False (Hamis) |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True (Igaz) |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False (Hamis) | |

## <a name="to-use-a-property"></a>A tulajdonság
Egy házirend tulajdonságot használja, helyezze a belül kell használni, például a kettős két tulajdonságnév `{{ContosoHeader}}`, a következő példában látható módon.

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

Vegye figyelembe, hogy közben a tulajdonságértékek házirend-kifejezést tartalmazhat, tulajdonságértékek nem tartalmazhat más tulajdonságok. Ha a szöveg tulajdonság a hivatkozást használja a tulajdonság értéke, például a `Property value text {{MyProperty}}`, hivatkozó tulajdonság nem lehet írni, és tartalmazzák a tulajdonság értékének a lesz.

## <a name="to-create-a-property"></a>A tulajdonság létrehozása
Hozzon létre egy tulajdonságot, kattintson a **tulajdonság hozzáadása** a a **tulajdonságok** fülre.

![Tulajdonság hozzáadása][api-management-properties-add-property-menu]

**Név** és **érték** szükséges érték. Ha ez a tulajdonság értéke egy titkos kulcsot, ellenőrizze a **Ez a titkos kulcs** jelölőnégyzetet. Adja meg egy vagy több választható címkéket a Tulajdonságok rendszerezéséhez számára, és kattintson a **mentése**.

![Tulajdonság hozzáadása][api-management-properties-add-property]

Új tulajdonság mentésekor a rendszer a **tulajdonság keresése** szövegmező a telepítéskor az új tulajdonság nevét, és az új tulajdonság jelenik meg. Megjeleníti az összes tulajdonság, vagy törölje a **tulajdonság keresése** szövegmező, és nyomja le az adja meg.

![Tulajdonságok][api-management-properties-property-saved]

A REST API használatával tulajdonság létrehozásáról további információért lásd: [hozzon létre egy tulajdonságot a REST API használatával](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Tulajdonság módosítása
A tulajdonság szerkesztéséhez kattintson **szerkesztése** szerkesztése tulajdonság mellett.

![Tulajdonság szerkesztése][api-management-properties-edit]

Végezze el a szükséges módosításokat, és kattintson a **mentése**. Ha módosítja a tulajdonság nevét, a házirendek, amelyek az adott tulajdonsághoz hivatkoznak automatikusan frissülnek az új nevét.

![Tulajdonság szerkesztése][api-management-properties-edit-property]

A REST API használatával tulajdonság szerkesztési információkért lásd: [REST API használatával tulajdonság módosítása](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Egy tulajdonság
Törölni egy tulajdonságot, kattintson a **törlése** mellett a tulajdonság törlése.

![Tulajdonság törlése][api-management-properties-delete]

Kattintson a **Igen, törölje azt** megerősítéséhez.

![Törlés megerősítése][api-management-delete-confirm]

> [!IMPORTANT]
> Ha a tulajdonság a házirendek által hivatkozott, nem fog tudni sikeresen törölni mindaddig, amíg az azt használó összes házirendektől távolítsa el a tulajdonságot.
> 
> 

A REST API használatával tulajdonság törléséről további információkért lásd: [törölni egy tulajdonságot a REST API használatával](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>A Keresés és szűrés tulajdonságai
A **tulajdonságok** lapján található, a Keresés és szűrés lehetőségek közül választhat a tulajdonságainak kezeléséhez. Tulajdonság neve a tulajdonságlista szűréséhez adja meg a kívánt keresőkifejezést a a **tulajdonság keresése** szövegmező. Megjeleníti az összes tulajdonság, vagy törölje a **tulajdonság keresése** szövegmező, és nyomja le az adja meg.

![Keresés][api-management-properties-search]

Címke értékek szerint a tulajdonságlista szűréséhez adja meg egy vagy több címkéket, az a **címkék szűrés** szövegmező. Megjeleníti az összes tulajdonság, vagy törölje a **címkék szűrés** szövegmező, és nyomja le az adja meg.

![Szűrés][api-management-properties-filter]

## <a name="next-steps"></a>Következő lépések
* További információ a házirendek használata
  * [Az API Management házirendek](api-management-howto-policies.md)
  * [Házirend-referencia](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Házirend-kifejezések](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Áttekintő videó megtekintése
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

