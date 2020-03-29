---
title: A nézetdefiníció áttekintése
description: Ismerteti az Azure felügyelt alkalmazások nézetdefiníciólétrehozásának koncepcióját.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: d0c60f5738bf634f9d43d6d4f0d78c1239b7ff3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650694"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Definíciós összetevő megtekintése az Azure felügyelt alkalmazásaiban

A nézetdefiníció egy választható összetevő az Azure felügyelt alkalmazásokban. Lehetővé teszi az áttekintő lap testreszabását, és további nézeteket, például metrikákat és egyéni erőforrásokat adhat hozzá.

Ez a cikk áttekintést nyújt a nézetdefiníciós összetevőről és annak képességeiről.

## <a name="view-definition-artifact"></a>Meghatározás megtekintése összetevő

A nézetdefiníciós összetevőnek **viewDefinition.json** névre kell esnie, és ugyanazon a szinten kell elhelyezni, mint a **createUiDefinition.json** és a **mainTemplate.json** a .zip csomagban, amely felügyelt alkalmazásdefiníciót hoz létre. A .zip csomag létrehozásáról és a felügyelt alkalmazásdefiníció közzétételéről az [Azure felügyelt alkalmazásdefiníció közzététele című](publish-managed-app-definition-quickstart.md) témakörben olvashat.

## <a name="view-definition-schema"></a>Definícióséma megtekintése

A **viewDefinition.json** fájl csak `views` egy legfelső szintű tulajdonsággal rendelkezik, amely nézetek tömbje. Minden nézet a felügyelt alkalmazás felhasználói felületén külön menüpontként jelenik meg a tartalomjegyzékben. Minden nézethez `kind` van egy tulajdonság, amely beállítja a nézet típusát. A következő értékek egyikére kell beállítani: [Áttekintés](#overview), [Metrikák](#metrics), [CustomResources](#custom-resources), [Társítások](#associations). További információ: a [viewDefinition.json aktuális JSON-sémája.](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)

Minta JSON a megtekintés definíciójához:

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
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Áttekintés

`"kind": "Overview"`

Amikor ezt a nézetet a **viewDefinition.json alkalmazásban**adja meg, felülírja a felügyelt alkalmazás alapértelmezett áttekintése lapját.

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

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|header|Nem|Az áttekintő lap fejléce.|
|leírás|Nem|A felügyelt alkalmazás leírása.|
|Parancsok|Nem|Az áttekintő lap további eszköztárgombjainak tömbje, lásd a [parancsokat.](#commands)|

![Áttekintés](./media/view-definition/overview.png)

## <a name="metrics"></a>Mérőszámok

`"kind": "Metrics"`

A metrikák nézet lehetővé teszi, hogy adatokat gyűjtsön és összesítsen a felügyelt alkalmazás-erőforrásokból az [Azure Monitor metrikákban.](../../azure-monitor/platform/data-platform-metrics.md)

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

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Nem|A nézet megjelenített címe.|
|version|Nem|A platform nak a nézet megjelenítéséhez használt verziója.|
|Charts|Igen|A mérőszámok oldal diagramjainak tömbje.|

### <a name="chart"></a>Diagram

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A diagram megjelenített címe.|
|diagramtípus|Nem|A diagramhoz használandó vizualizáció. Alapértelmezés szerint vonaldiagramot használ. Támogatott diagramtípusok: `Bar, Line, Area, Scatter`.|
|metrics|Igen|A diagramon ábrázolandó mérőszámok tömbje. Ha többet szeretne megtudni az Azure Portalon támogatott metrikákról, olvassa [el a Támogatott metrikák az Azure Monitorhasználatával című témakört.](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|név|Igen|A mérőszám neve.|
|aggregationType (összesítéstípusa)|Igen|A metrikához használandó összesítési típus. Támogatott összesítési típusok:`none, sum, min, max, avg, unique, percentile, count`|
|névtér|Nem|További információk a megfelelő metrikaszolgáltató meghatározásához.|
|resourceTagFilter|Nem|Az erőforráscímkék tömbje (szóval `or` lesz elválasztva), amelyhez metrikák jelennek meg. Az erőforrástípus-szűrő re.|
|resourceType|Igen|Az az erőforrástípus, amelyhez a metrikák megjelennének.|

![Mérőszámok](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Egyéni erőforrások

`"kind": "CustomResources"`

Az ilyen típusú nézeteket több nézetet is definiálhat. Minden nézet **egyedi** egyéni erőforrástípust jelöl a **mainTemplate.json**nyelven megadott egyéni szolgáltatótól. Az egyéni szolgáltatók bemutatása az [Azure Egyéni szolgáltatók előzetes verzió – áttekintés című témakörben található.](../custom-providers/overview.md)

Ebben a nézetben GET, PUT, DELETE és POST műveleteket hajthat végre az egyéni erőforrástípushoz. A POST-műveletek lehetnek globális egyéni műveletek vagy egyéni műveletek az egyéni erőforrástípus környezetében.

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

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A nézet megjelenített címe. A címnek **egyedinek** kell lennie a **viewDefinition.json minden**CustomResources nézetéhez.|
|version|Nem|A platform nak a nézet megjelenítéséhez használt verziója.|
|resourceType|Igen|Az egyéni erőforrástípus. Az egyéni szolgáltató **egyedi** egyéni erőforrástípusának kell lennie.|
|Ikon|Nem|A nézet ikonja. A példaikonok listája a [JSON-sémában](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)van definiálva.|
|createUIDefinition|Nem|Felhasználói felületdefiníciós séma létrehozása egyéni erőforrás-létrehozási parancshoz. A felhasználói felületdefiníciók létrehozásának bemutatása: [A CreateUiDefinition](create-uidefinition-overview.md) használata című témakörben|
|Parancsok|Nem|A CustomResources nézet további eszköztárgombjainak tömbje, lásd a [parancsokat.](#commands)|
|oszlopok|Nem|Az egyéni erőforrás oszlopainak tömbje. Ha nincs megadva, az `name` oszlop alapértelmezés szerint megjelenik. Az oszlopnak `"key"` `"displayName"`rendelkeznie kell a és a . Kulcs esetén adja meg a tulajdonság nézetben megjelenítendő kulcsát. Ha egymásba van ágyazva, használja a `"key": "name"` `"key": "properties.property1"`dot határolójelként, például vagy a . A megjelenítendő névhez adja meg a nézetben megjelenítendő tulajdonság megjelenítendő nevét. A szálláshelyet `"optional"` is meg adhat. Ha értéke igaz, az oszlop alapértelmezés szerint el van rejtve egy nézetben.|

![Egyéni erőforrások](./media/view-definition/customresources.png)

## <a name="commands"></a>Parancsok

A parancsok további eszköztárgombok tömbje, amelyek a lapon jelennek meg. Minden parancs egy POST műveletet jelöl az Azure egyéni szolgáltatójától, amelyet a **mainTemplate.json**nyelven határozmeg. Az egyéni szolgáltatók bemutatása az [Azure egyéni szolgáltatók áttekintése című témakörben található.](../custom-providers/overview.md)

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

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A parancsgomb megjelenített neve.|
|path|Igen|Az egyéni szolgáltatóművelet neve. A műveletet a **mainTemplate.json nyelven**kell definiálni.|
|Ikon|Nem|A parancsgomb ikonja. A példaikonok listája a [JSON-sémában](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)van definiálva.|
|createUIDefinition|Nem|Felhasználói felületdefiníciós séma létrehozása parancshoz. A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.|

## <a name="associations"></a>Egyesületek

`"kind": "Associations"`

Az ilyen típusú nézeteket több nézetet is definiálhat. Ez a nézet lehetővé teszi, hogy a meglévő erőforrásokat a felügyelt alkalmazáshoz kapcsolja a **mainTemplate.json**fájlban megadott egyéni szolgáltatón keresztül. Az egyéni szolgáltatók bemutatása az [Azure Egyéni szolgáltatók előzetes verzió – áttekintés című témakörben található.](../custom-providers/overview.md)

Ebben a nézetben a meglévő Azure-erőforrásokat a `targetResourceType`alapján bővítheti ki. Ha egy erőforrás van kiválasztva, létrehoz egy bevezetési kérelmet a **nyilvános** egyéni szolgáltató, amely alkalmazhat egy mellékhatást az erőforrásra. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A nézet megjelenített címe. A címnek **egyedinek** kell lennie a **viewDefinition.json nézetben**lévő egyes Társítások nézetekhez.|
|version|Nem|A platform nak a nézet megjelenítéséhez használt verziója.|
|targetResourceType|Igen|A célerőforrás típusa. Ez az az erőforrástípus, amely megjelenik az erőforrás-bevezetéshez.|
|createUIDefinition|Nem|Felhasználói felületdefiníciós séma létrehozása társítási erőforrás létrehozásához parancs. A felhasználói felületdefiníciók létrehozásának bemutatása: [A CreateUiDefinition](create-uidefinition-overview.md) használata című témakörben|

## <a name="looking-for-help"></a>Segítségre van szüksége

Ha kérdése van az Azure felügyelt alkalmazásokkal kapcsolatban, próbálja meg feltenni a [Stack Overflow-t.](https://stackoverflow.com/questions/tagged/azure-managedapps) Lehet, hogy egy hasonló kérdést már feltettek és megválaszoltak, ezért először ellenőrizze a feladás előtt. Add hozzá `azure-managedapps` a címkét, hogy gyors választ kapj!

## <a name="next-steps"></a>További lépések

- A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](overview.md) cikk ismerteti.
- Az egyéni szolgáltatók bemutatása az [Azure egyéni szolgáltatók áttekintése című témakörben található.](../custom-providers/overview.md)
- Az Azure felügyelt alkalmazás Azure egyéni szolgáltatókkal való létrehozásáról [az Oktatóanyag: Felügyelt alkalmazás létrehozása egyéni szolgáltatóműveletekkel és erőforrástípusokkal című](tutorial-create-managed-app-with-custom-provider.md) témakörben található.
