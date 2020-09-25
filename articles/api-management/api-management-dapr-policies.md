---
title: Azure API Management Dapr integrációs szabályzatok | Microsoft Docs
description: Ismerkedjen meg az Azure API Management-szabályzatokkal a Dapr-szolgáltatások bővítményeivel való interakcióhoz.
author: vladvino
ms.author: vlvinogr
ms.date: 9/13/2020
ms.topic: article
ms.service: api-management
ms.openlocfilehash: d537040be4ed4cbf961a4621980d3d290e306359
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343027"
---
# <a name="api-management-dapr-integration-policies"></a>API Management Dapr-integrációs szabályzatok

Ez a témakör a Dapr-integrációs API Management házirendjeire mutató hivatkozást tartalmaz. A Dapr egy hordozható futtatókörnyezet, amellyel állapot nélküli és állapot-nyilvántartó, bármilyen nyelven vagy keretrendszerrel rendelkező alkalmazások hozhatók létre. A szolgáltatás a közös kodifikálja-mintákat, például a szolgáltatások felderítését és meghívását, valamint az újrapróbálkozási logikát, a közzétételt és az előfizetést a legalább egyszeri kézbesítési szemantikai szabályokkal, illetve a kihasználható kötési erőforrásokkal segíti a külső szolgáltatások használatának megkönnyítéséhez. A [dapr.IO](https://dapr.io) részletes információit és útmutatását a dapr megismerésével foglalkozó témakörben olvashatja el. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](api-management-howto-policies.md).

> [!CAUTION]
> A jelen témakörben hivatkozott szabályzatok nyilvános előzetes verzióban érhetők el, és a [Microsoft Azure előnézetekre vonatkozó kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)vonatkoznak rájuk.

> [!IMPORTANT]
> A jelen témakörben hivatkozott házirendek csak az [API Management-átjáró saját üzemeltetésű verziójában](self-hosted-gateway-overview.md) működnek, amelyen engedélyezve van a Dapr-támogatás.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Dapr-támogatás engedélyezése a saját üzemeltetésű átjáróban

Ha be szeretné kapcsolni a Dapr-támogatást a saját üzemeltetésű átjárón, adja hozzá az alábbi [Dapr-megjegyzéseket](https://github.com/dapr/docs/blob/master/howto/configure-k8s/README.md) a [Kubernetes telepítési sablonhoz](how-to-deploy-self-hosted-gateway-kubernetes.md) az "app-Name" helyett a kívánt névvel. A API Management és Dapr beállításának és használatának teljes áttekintése [itt](https://aka.ms/apim/dapr/walkthru)érhető el.
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Distributed Application Runtime (Dapr) integrációs szabályzatok

-  [Kérelem küldése a szolgáltatásnak](api-management-dapr-policies.md#invoke): a Dapr Runtime használatával megkeresheti és megbízhatóan kommunikálhat egy Dapr-szolgáltatással. Ha többet szeretne megtudni a Dapr található szolgáltatás meghívásáról, tekintse meg a leírását ebben a [readme](https://github.com/dapr/docs/blob/master/concepts/service-invocation/README.md#service-invocation) fájlban.
-  [Üzenet küldése a pub/sub témakörbe](api-management-dapr-policies.md#pubsub): az Dapr Runtime használatával tesz közzé egy üzenetet egy közzététel/előfizetés témakörben. Ha többet szeretne megtudni a Dapr-beli közzétételről és előfizetésekről, tekintse meg a jelen [readme](https://github.com/dapr/docs/blob/master/concepts/publish-subscribe-messaging/README.md) fájlban található leírást.
-  [Kimeneti kötés kiváltása](api-management-dapr-policies.md#bind): a Dapr Runtime használatával kezdeményez egy külső rendszer kimeneti kötés használatával. Ha többet szeretne megtudni a Dapr lévő kötésekről, tekintse meg a leírását ebben a [readme](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md) fájlban.

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Kérelem küldése egy szolgáltatásnak

Ez a házirend beállítja a jelenlegi kérelem cél URL-címét a `http://localhost:3500/v1.0/invoke/{app-id}/method/{method-name}` sablon paramétereinek a szabályzat utasításban megadott értékekkel való lecseréléséhez.

A házirend feltételezi, hogy a Dapr egy oldalkocsis tárolóban fut, amely az átjáróval megegyező Pod-ban található. A kérelem kézhezvétele után a Dapr Runtime a szolgáltatás felderítését és a tényleges meghívást végzi, beleértve a HTTP és a gRPC, az újrapróbálkozások, az elosztott nyomkövetés és a hibakezelés közötti lehetséges protokollok fordítását.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" />
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

Az alábbi példa azt mutatja be, hogy a "back" nevű metódust a "Echo" nevű szolgáltatásban hívja meg. A `set-backend-service` házirend beállítja a cél URL-címet. A szabályzat elküldi `forward-request` a kérelmet a Dapr futtatókörnyezetnek, amely továbbítja azt a szolgáltatásnak.

A `forward-request` szabályzat itt látható az érthetőség kedvéért. A szabályzat általában a globális hatókör "örökölt", a `base` kulcsszón keresztül.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elemek

| Elem             | Leírás  | Kötelező |
|---------------------|--------------|----------|
| set-háttér-szolgáltatás | Gyökérelem | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum        | Leírás                     | Kötelező | Alapértelmezett |
|------------------|---------------------------------|----------|---------|
| háttér-azonosító       | "Dapr" értékre kell állítani           | Yes      | N.A.     |
| dapr-app-ID      | A cél-szolgáltatás neve. Leképezi a [AppID](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) paramétert a Dapr-ben.| Yes | N.A. |
| dapr – metódus      | A metódus vagy URL-cím neve, amelyet a célként szolgáló szolgáltatásban kell meghívni. Leképezi a [metódus-Name](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) paramétert a Dapr-ben.| Yes | N.A. |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Üzenet küldése a pub/sub témakörbe

Ez a szabályzat arra utasítja API Management átjárót, hogy üzenetet küldjön egy Dapr közzétételi/előfizetési témakörnek. A szabályzat úgy valósul meg, hogy egy HTTP POST-kéréssel váltja fel a `http://localhost:3500/v1.0/publish/{{pub-name}}/{{topic}}` sablon paramétereit, és hozzáadja a szabályzat utasításban megadott tartalmat.

A házirend feltételezi, hogy az Dapr Runtime egy olyan oldalkocsis tárolóban fut, amely az átjáróval megegyező Pod-tárolóban található. A Dapr Runtime a pub/sub szemantikaát valósítja meg.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<publish-to-dapr topic=”topic-name” ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid” content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

Az alábbi példa azt mutatja be, hogyan küldi el a jelenlegi kérelem törzsét az "Orders" pub/sub [összetevő](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters)"új" [témakörében](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters) . Az Dapr-futtatókörnyezettől kapott választ a [környezeti](api-management-policy-expressions.md#ContextVariables) objektum változók gyűjteményének "Dapr-Response" bejegyzése tárolja.

Ha a Dapr Runtime nem találja a cél témakört, például egy hibával válaszol, a "hiba" szakasz aktiválódik. A Dapr-futtatókörnyezettől kapott választ a rendszer visszaküldi a hívónak. Ellenkező esetben a `200 OK` rendszer az alapértelmezett választ adja vissza.

A "háttér" szakasz üres, és a kérés nem lett továbbítva a háttérbe.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
               topic="@("orders/new")"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elemek

| Elem             | Leírás  | Kötelező |
|---------------------|--------------|----------|
| közzététel a dapr     | Gyökérelem | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum        | Leírás                     | Kötelező | Alapértelmezett |
|------------------|---------------------------------|----------|---------|
| témakör            | Cél témakör neve               | Yes      | N.A.     |
| Mellőzés – hiba     | Ha a beállítás értéke arra `true` utasítja a szabályzatot, hogy ne aktiválja [a "hiba"](api-management-error-handling-policies.md) szakaszt a Dapr-futtatókörnyezettől érkező hiba esetén. | No | `false` |
| Válasz-változó – név | A Dapr-futtatókörnyezet válaszának tárolásához használandó [változók](api-management-policy-expressions.md#ContextVariables) -gyűjtemény bejegyzésének neve | No | Nincsenek |
| timeout | Az a várakozási idő (másodpercben), ameddig a Dapr-futtatókörnyezet válaszol. 1 és 240 másodperc között lehet. | No | 5 |
| sablon | Az üzenet tartalmának átalakításához használni kívánt sablonrendszer-motor. A "Liquid" az egyetlen támogatott érték. | No | Nincsenek |
| Content-Type | Az üzenet tartalmának típusa. az "Application/JSON" az egyetlen támogatott érték. | No | Nincsenek |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő, kimenő, on-Error
- **Házirend-hatókörök:** az összes hatókör

## <a name="trigger-output-binding"></a><a name="bind"></a> Kimeneti kötés indítása

Ez a szabályzat arra utasítja API Management átjárót, hogy kiváltsa a kimenő Dapr [kötését](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md). A szabályzat úgy teljesíti ezt, hogy egy HTTP POST-kérést ad a `http://localhost:3500/v1.0/bindings/{{bind-name}}` sablon paraméterének lecserélésére és a házirend-utasításban megadott tartalom hozzáadására.

A házirend feltételezi, hogy az Dapr Runtime egy olyan oldalkocsis tárolóban fut, amely az átjáróval megegyező Pod-tárolóban található. A Dapr Runtime feladata a kötés által reprezentált külső erőforrás meghívása.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<invoke-dapr-binding name=”bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid content-type="application/json">
    <metadata>
        <item key=”item-name”><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

Az alábbi példa azt mutatja be, hogy a "Create" művelet nevű kimenő kötés "External-Systems" néven aktiválódik, a metaadatok pedig a "forrás" és az "ügyfél-IP" nevű kulcs/érték elemekből állnak, és az eredeti kérelemből származó törzs. A Dapr-futtatókörnyezettől kapott válasz rögzítése a [környezeti](api-management-policy-expressions.md#ContextVariables) objektum változók gyűjteményének "kötési válasz" bejegyzésében történik.

Ha a Dapr-futtatókörnyezet valamilyen okból meghiúsul, és hiba miatt válaszol, a "hiba" szakasz aktiválódik, és a Dapr-futtatókörnyezettől kapott választ a rendszer visszaadja a hívónak. Ellenkező esetben a `200 OK` rendszer az alapértelmezett választ adja vissza.

A "háttér" szakasz üres, és a kérés nem lett továbbítva a háttérbe.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elemek

| Elem             | Leírás  | Kötelező |
|---------------------|--------------|----------|
| dapr-kötés meghívása | Gyökérelem | Yes      |
| metaadatok            | A megadott metaadatokat kulcs/érték párok formájában kell megkötni. Leképezi a [metaadatok](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) tulajdonságot a Dapr-ben. | No |
| adatok            | Az üzenet tartalma. Leképezi a Dapr [adattulajdonságát](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) . | No |


### <a name="attributes"></a>Attribútumok

| Attribútum        | Leírás                     | Kötelező | Alapértelmezett |
|------------------|---------------------------------|----------|---------|
| név            | Cél kötési neve Meg kell egyeznie a Dapr-ben [definiált](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#bindings-structure) kötések nevével.           | Yes      | N.A.     |
| művelet       | Cél művelet neve (kötési specifikus). A Dapr [művelet](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) tulajdonságának leképezése. | No | Nincsenek |
| Mellőzés – hiba     | Ha a beállítás értéke arra `true` utasítja a szabályzatot, hogy ne aktiválja [a "hiba"](api-management-error-handling-policies.md) szakaszt a Dapr-futtatókörnyezettől érkező hiba esetén. | No | `false` |
| Válasz-változó – név | A Dapr-futtatókörnyezet válaszának tárolásához használandó [változók](api-management-policy-expressions.md#ContextVariables) -gyűjtemény bejegyzésének neve | No | Nincsenek |
| timeout | Az a várakozási idő (másodpercben), ameddig a Dapr-futtatókörnyezet válaszol. 1 és 240 másodperc között lehet. | No | 5 |
| sablon | Az üzenet tartalmának átalakításához használni kívánt sablonrendszer-motor. A "Liquid" az egyetlen támogatott érték. | No | Nincsenek |
| Content-Type | Az üzenet tartalmának típusa. az "Application/JSON" az egyetlen támogatott érték. | No | Nincsenek |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

- **Házirend fejezetei:** bejövő, kimenő, on-Error
- **Házirend-hatókörök:** az összes hatókör
