---
title: Elnevezett értékek használata az Azure API-kezelési szabályzatokban
description: Ismerje meg, hogyan használhatja az elnevezett értékeket az Azure API Management szabályzatokban.
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
ms.openlocfilehash: 4362d0875ac2c20fc6963d404f86898a12387dad
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260921"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Elnevezett értékek használata az Azure API-kezelési szabályzatokban

Az API Management szabályzatok a rendszer hatékony képessége, amely lehetővé teszi az Azure Portal az API viselkedésének megváltoztatását a konfiguráción keresztül. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A házirendutasítások literális szöveges értékek, házirend-kifejezések és elnevezett értékek használatával is létrehozhatók.

Minden API Management szolgáltatáspéldány rendelkezik egy kulcs/érték párok gyűjteménye, amely a szolgáltatáspéldány globális a szolgáltatáspéldány neve. Nincs kiszabott korlát a gyűjteményben lévő elemek számára vonatkozóan. Elnevezett értékek segítségével kezelheti az állandó karakterlánc-értékeket az összes API-konfiguráció és -házirendek. Minden elnevezett érték a következő attribútumokkal rendelkezhet:

| Attribútum      | Típus            | Leírás                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | sztring          | Az elnevezett értékhivatkozásra szolgál a házirendekben. Egy-256 karakterből álló karakterlánc. Csak betűk, számok, dot és kötőjel engedélyezett. |
| `Value`        | sztring          | Tényleges érték. Nem lehet üres, vagy csak szóközből állhat. Legfeljebb 4096 karakter hosszú.                                        |
| `Secret`       | logikai         | Azt határozza meg, hogy az érték titkos-e, és titkosítsa-e vagy sem.                                                               |
| `Tags`         | sztringek tömbje | Az elnevezett értéklista szűrésére szolgál. Legfeljebb 32 címke.                                                                                    |

![Névvel ellátott értékek](./media/api-management-howto-properties/named-values.png)

Az elnevezett értékek literális karakterláncokat és [házirend-kifejezéseket tartalmazhatnak.](/azure/api-management/api-management-policy-expressions) Például `Expression` az értéke egy olyan házirend-kifejezés, amely az aktuális dátumot és időt tartalmazó karakterláncot ad vissza. Az elnevezett érték `Credential` titkos névként van megjelölve, így az értéke alapértelmezés szerint nem jelenik meg.

| Név       | Érték                      | Titkos | Címkék          |
| ---------- | -------------------------- | ------ | ------------- |
| Érték      | 42                         | False (Hamis)  | létfontosságú számok |
| Hitelesítő adat | ••••••••••••••••••••••     | True (Igaz)   | biztonság      |
| Kifejezés | @(DateTime.Now.ToString()) | False (Hamis)  |               |

> [!NOTE]
> Az API Management szolgáltatásban tárolt elnevezett értékek helyett használhatja az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) szolgáltatásban tárolt értékeket, amint azt ez a [példa](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)is mutatja.

## <a name="to-add-and-edit-a-named-value"></a>Elnevezett érték hozzáadása és szerkesztése

![Elnevezett érték hozzáadása](./media/api-management-howto-properties/add-property.png)

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Válassza **az Elnevezett értékek lehetőséget.**
3. Nyomja **le a +Add billentyűkombinációt.**

    A név és az érték kötelező értékek. Ha az érték titkos, jelölje be _A titkos_ lista jelölőnégyzetet. Adjon meg egy vagy több választható címkét az elnevezett értékek rendszerezéséhez, majd kattintson a Mentés gombra.

4. Kattintson **a Létrehozás gombra.**

Az elnevezett érték létrehozása után kattintással szerkesztheti azt. Ha módosítja az elnevezett érték nevét, az értéket megnevező házirendek automatikusan frissülnek az új név használatára.

Az elnevezett érték REST API használatával történő szerkesztéséről a [Megnevezett érték szerkesztése a REST API használatával című témakörben](/rest/api/apimanagement/2019-12-01/property?patch)talál.

## <a name="to-delete-a-named-value"></a>Elnevezett érték törlése

Névvel ellátott érték törléséhez kattintson a törölni kívánt névvel ellátott érték melletti **Törlés** gombra.

> [!IMPORTANT]
> Ha a megnevezett értékre bármely házirend hivatkozik, addig nem tudja sikeresen törölni, amíg el nem távolítja az elnevezett értéket az azt használó összes házirendből.

Az elnevezett érték REST API használatával történő törléséről a [Névvel ellátott érték törlése a REST API használatával című témakörben](/rest/api/apimanagement/2019-12-01/property/delete)talál további információt.

## <a name="to-search-and-filter-named-values"></a>Elnevezett értékek keresése és szűrése

Az **Elnevezett értékek** lap keresési és szűrési lehetőségeket tartalmaz az elnevezett értékek kezeléséhez. Az elnevezett értékek listájának név szerinti szűréséhez írjon be egy keresési kifejezést a **Keresés tulajdonság** szövegmezőbe. Az összes elnevezett érték megjelenítéséhez törölje a **törlést** a Keresés tulajdonság szövegmezőjéből, és nyomja le az Enter billentyűt.

A lista címke szerinti szűréséhez írjon be egy vagy több címkét a **Címkék szűrése** szövegmezőbe. Az összes elnevezett érték megjelenítéséhez törölje a törlést a **Szűrés címkékkel** szövegmezőből, és nyomja le az Enter billentyűt.

## <a name="to-use-a-named-value"></a>Elnevezett érték használata

Ha egy házirendben elnevezett értéket szeretne használni, helyezze a `{{ContosoHeader}}`nevét egy dupla kapcsos zárójelpárba, például a , ahogy az a következő példában látható:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a `ContosoHeader` példában `set-header` a házirend fejlécének neveként, `ContosoHeaderValue` és a fejléc értékeként használatos. Ha ezt a szabályzatot kiértékeli egy kérelem `{{ContosoHeader}}` vagy `{{ContosoHeaderValue}}` válasz az API Management átjáró, és lecseréli a megfelelő értékeket.

Az elnevezett értékek teljes attribútum- vagy elemértékként használhatók, ahogy az előző példában is látható, de beszúrhatók egy literális szöveges kifejezés beszúrhatók, vagy kombinálhatók egy konstans szöveges kifejezés egy részével, ahogy az a következő példában látható:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Az elnevezett értékek házirend-kifejezéseket is tartalmazhatnak. A következő példában `ExpressionProperty` a használatban van.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

A házirend kiértékelésekor `{{ExpressionProperty}}` a rendszer a `@(DateTime.Now.ToString())`következő értékkel helyettesíti a következőt: . Mivel az érték egy házirend-kifejezés, a kifejezés kiértékelése és a házirend végrehajtása folytatódik.

Ezt a fejlesztői portálon tesztelheti, ha olyan műveletet hív meg, amelynek hatókörében elnevezett értékekkel rendelkező házirend van. A következő példában egy művelet et a `set-header` két előző példa házirendel nevezett értékkel hív meg egy művelet. Vegye figyelembe, hogy a válasz két egyéni fejlécet tartalmaz, amelyek et elnevezett értékekkel rendelkező házirendek használatával konfiguráltak.

![Fejlesztői portál][api-management-send-results]

Ha megnézi az [API Inspector nyomkövetési](api-management-howto-api-inspector.md) egy hívás, amely tartalmazza a két `set-header` korábbi minta szabályzatok elnevezett értékekkel, láthatja a két szabályzat ot a beszúrt értékeket, valamint a házirend-kifejezést tartalmazó elnevezett érték házirend-kifejezés kiértékelése.

![API-felügyelő nyomkövetése][api-management-api-inspector-trace]

Bár az elnevezett értékek tartalmazhatnak házirend-kifejezéseket, nem tartalmazhatnak más elnevezett értékeket. Ha egy értékhez névvel ellátott értékhivatkozást `Text: {{MyProperty}}`tartalmazó szöveget használ, például ahoz, akkor a hivatkozás nem oldódik fel és nem cserélődik le.

## <a name="next-steps"></a>További lépések

-   További információ a házirendek ről
    -   [Szabályzatok az API Managementben](api-management-howto-policies.md)
    -   [Házirend-referencia](/azure/api-management/api-management-policies)
    -   [Házirend-kifejezések](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
