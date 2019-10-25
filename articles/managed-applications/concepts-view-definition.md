---
title: A View definition áttekintése a Azure Managed Applicationsban | Microsoft Docs
description: A Azure Managed Applications nézet definíciójának létrehozásának fogalmát ismerteti.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: f51dbce3c251f4e89483d925ac657aac7eb928d8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804073"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Definíciós összetevő megtekintése Azure Managed Applications

A View definition egy opcionális összetevő a Azure Managed Applicationsban. Lehetővé teszi az áttekintő lap testreszabását, és további nézetek, például mérőszámok és egyéni erőforrások hozzáadását.

Ez a cikk áttekintést nyújt a View definition összetevőről és annak képességeiről.

## <a name="view-definition-artifact"></a>Meghatározás megtekintése összetevő

A View definition összetevőnek **viewDefinition. JSON** nevűnek kell lennie, és a felügyelt alkalmazás definícióját létrehozó. zip csomagban található **createUiDefinition. JSON** és **mainTemplate. JSON** fájlnak megfelelő szintre kell helyeznie. A. zip csomag létrehozásával és a felügyelt alkalmazás definíciójának közzétételével kapcsolatos információkért lásd: [Azure felügyelt alkalmazás definíciójának közzététele](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Definíciós séma megtekintése

A **viewDefinition. JSON** fájlnak csak egyetlen legfelső szintű `views` tulajdonsága van, amely a nézetek tömbje. A felügyelt alkalmazás felhasználói felületén minden nézet külön menüelemként jelenik meg a tartalomjegyzékben. Minden nézet rendelkezik egy `kind` tulajdonsággal, amely a nézet típusát állítja be. A következő értékek egyikére kell beállítani: [Áttekintés](#overview), [metrikák](#metrics), [CustomResources](#custom-resources). További információ: [a viewDefinition. JSON jelenlegi JSON-sémája](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Példa a JSON-ra a nézet definíciójában:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>Áttekintés

`"kind": "Overview"`

Ha ezt a nézetet megadja a **viewDefinition. JSON**fájlban, a felülbírálja a felügyelt alkalmazás alapértelmezett áttekintés lapját.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|header|Nem|Az Áttekintés lap fejléce|
|leírás|Nem|A felügyelt alkalmazás leírása.|
|parancsok|Nem|Az Áttekintés lap további eszköztár gombjainak tömbje: [parancsok](#commands).|

![Áttekintés](./media/view-definition/overview.png)

## <a name="metrics"></a>Metrikák

`"kind": "Metrics"`

A metrikák nézet lehetővé teszi, hogy [Azure monitor mérőszámokban](../azure-monitor/platform/data-platform-metrics.md)lévő felügyelt alkalmazás-erőforrások adatait összegyűjtse és összesítse.

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|DisplayName|Nem|A nézet megjelenített címe|
|version|Nem|A nézet megjelenítéséhez használt platform verziója.|
|diagramok|Igen|A metrikák oldal diagramok tömbje.|

### <a name="chart"></a>Diagram

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|DisplayName|Igen|A diagram megjelenített címe|
|chartType|Nem|A diagramhoz használandó vizualizáció. Alapértelmezés szerint egy vonalas diagramot használ. Támogatott diagramok típusai: `Bar, Line, Area, Scatter`.|
|metrics|Igen|A diagramon nyomtatandó mérőszámok tömbje. Ha többet szeretne megtudni a Azure Portal által támogatott mérőszámokról, tekintse meg [a támogatott mérőszámokat Azure monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|név|Igen|A metrika neve.|
|aggregationType|Igen|A metrikához használandó összesítési típus. Támogatott összesítési típusok: `none, sum, min, max, avg, unique, percentile, count`|
|névtér|Nem|A helyes metrikák szolgáltatójának meghatározásakor használandó további információk.|
|resourceTagFilter|Nem|Az erőforrás-címkék tömbje (`or` Szóval lesz elválasztva), amelynél megjelennek a metrikák. Az erőforrástípus-szűrőre vonatkozik.|
|resourceType|Igen|Az az erőforrástípus, amelynek mérőszámait meg szeretné jeleníteni.|

![Metrikák](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Egyéni erőforrások

`"kind": "CustomResources"`

Több ilyen típusú nézetet is meghatározhat. Az egyes nézetek a **mainTemplate. JSON**fájlban meghatározott egyéni szolgáltató **egyedi** egyéni erőforrástípust jelölik. Az egyéni szolgáltatók bevezetését az [Azure Custom Providers előzetes](custom-providers-overview.md)verziójának áttekintése című témakörben tekintheti meg.

Ebben a nézetben elvégezheti az egyéni erőforrástípus GET, PUT, DELETE és POST műveleteit. A POST művelet lehet globális egyéni művelet vagy egyéni művelet az egyéni erőforrástípus környezetében.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|DisplayName|Igen|A nézet megjelenített címe A címnek **egyedinek** kell lennie a **viewDefinition. JSON**fájl minden egyes CustomResources-nézetében.|
|version|Nem|A nézet megjelenítéséhez használt platform verziója.|
|resourceType|Igen|Az egyéni erőforrástípus. **Egyedi** egyéni erőforrás-típusnak kell lennie az egyéni szolgáltatónál.|
|Ikon|Nem|A nézet ikonja A [JSON-sémában](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)definiált ikonok listája.|
|createUIDefinition|Nem|Felhasználói felületi definíciós séma létrehozása az egyéni erőforrás létrehozása parancshoz. A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md) használatába|
|parancsok|Nem|A CustomResources nézet további eszköztár gombjainak tömbje: [parancsok](#commands).|
|Oszlopok|Nem|Az egyéni erőforrás oszlopainak tömbje. Ha nincs megadva, a rendszer alapértelmezés szerint a `name` oszlopot jeleníti meg. Az oszlopnak `"key"` és `"displayName"`nak kell lennie. A kulcs mezőben adja meg a nézetben megjelenítendő tulajdonság kulcsát. Ha beágyazott, a pontot elválasztó karakterként használja, például `"key": "name"` vagy `"key": "properties.property1"`. A megjelenítendő név mezőben adja meg a nézetben megjelenítendő tulajdonság megjelenítendő nevét. `"optional"` tulajdonságot is megadhat. Ha igaz értékre van állítva, a rendszer alapértelmezés szerint elrejti az oszlopot a nézetben.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Parancsok

A parancsok a lapon megjelenő további eszköztár-gombok tömbje. Minden parancs a **mainTemplate. JSON**fájlban meghatározott Azure-beli egyéni szolgáltató utáni műveletet jelöli. Az egyéni szolgáltatók bevezetését az [Azure egyéni szolgáltatók áttekintése](custom-providers-overview.md)című témakörben tekintheti meg.

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|DisplayName|Igen|A parancsgomb megjelenített neve.|
|elérési útja|Igen|Az egyéni szolgáltató műveleti neve. A műveletet a **mainTemplate. JSON**fájlban kell definiálni.|
|Ikon|Nem|A parancsgomb ikonja A [JSON-sémában](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)definiált ikonok listája.|
|createUIDefinition|Nem|Felhasználói felületi definíciós séma létrehozása a parancshoz. A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.|

## <a name="looking-for-help"></a>Segítség keresése

Ha kérdése van a Azure Managed Applicationsával kapcsolatban, próbálja meg megkérdezni a [stack overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). Előfordulhat, hogy egy hasonló kérdést már megtettek és megválaszoltak, ezért először A feladás előtt érdemes megnézni. A címke `azure-managedapps` hozzáadásával gyorsan választ kaphat!

## <a name="next-steps"></a>Következő lépések

- A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](overview.md) cikk ismerteti.
- Az egyéni szolgáltatók bevezetését az [Azure egyéni szolgáltatók áttekintése](custom-providers-overview.md)című témakörben tekintheti meg.
- Azure-beli felügyelt alkalmazás Azure-beli egyéni szolgáltatókkal való létrehozásával kapcsolatban lásd [: oktatóanyag: felügyelt alkalmazás létrehozása egyéni szolgáltatói műveletekkel és erőforrás-típusokkal](tutorial-create-managed-app-with-custom-provider.md)
