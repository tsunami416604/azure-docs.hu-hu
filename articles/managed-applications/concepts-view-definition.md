---
title: Definíció megtekintése az Azure Managed Applications áttekintése |} A Microsoft Docs
description: Ismerteti az Azure által felügyelt alkalmazások nézet definíciójának létrehozása fogalmát.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478744"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Az Azure Managed Applications nézet definíciójának összetevő

Definíció megtekintése az Azure Managed Applications egy választható összetevő. Lehetővé teszi az Áttekintés oldal testreszabható, és adja hozzá például a mérőszámok és egyéni további nézeteket.

A cikk ismerteti a nézet definícióját összetevő áttekintése és annak képességeit.

## <a name="view-definition-artifact"></a>Meghatározás megtekintése összetevő

A nézet definícióját lehívandó összetevő neve legyen **viewDefinition.json** és ugyanazon a szinten **createUiDefinition.json** és **mainTemplate.json** a .zip a a csomag, amely létrehoz egy felügyelt alkalmazás definícióját. A .zip csomag létrehozásához, és a egy felügyelt alkalmazás definíciójának közzététele kapcsolatban lásd: [egy Azure által felügyelt alkalmazás definíciójának közzététele](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Nézet – definíciós séma

A **viewDefinition.json** fájl rendelkezik csak egyetlen legfelső szintű `views` tulajdonság, amely nézetek tömbje. Minden egyes nézet egy külön menüpont a tábla tartalmát, a felügyelt alkalmazás felhasználói felületén látható. Mindegyik nézetről tartalmaz egy `kind` tulajdonságot, amely beállítja a nézet a típusát. A következő értékek egyikére kell beállítani: [Áttekintés](#overview), [metrikák](#metrics), [CustomResources](#custom-resources). További információkért tekintse meg a jelenlegi [viewDefinition.json JSON-séma](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Minta JSON-definíció megtekintése:

```json
{
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
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
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
    ]
}

```

## <a name="overview"></a>Áttekintés

`"kind": "Overview"`

Ha megadta a nézet **viewDefinition.json**, ez a beállítás felülbírálja az alapértelmezett Áttekintés lapján található a felügyelt alkalmazás.

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
|header|Nem|Az Áttekintés oldal fejlécében.|
|description|Nem|A felügyelt alkalmazás leírása.|
|Parancsok|Nem|Az Áttekintés oldal további gombok tömbjének lásd [parancsok](#commands).|

## <a name="metrics"></a>Mérőszámok

`"kind": "Metrics"`

A metrikák nézet lehetővé teszi begyűjtése és összesítése az adatokat a felügyelt alkalmazás a erőforrásaiból [Azure Monitor-metrikák](../azure-monitor/platform/data-platform-metrics.md).

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
|displayName|Nem|A nézetben megjelenített címe.|
|version|Nem|A platform, a nézet megjelenítéséhez használt verziója.|
|Diagramok|Igen|A metrikák lap diagramok tömbje.|

### <a name="chart"></a>Diagram

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|displayName|Igen|A diagramon megjelenített címe.|
|chartType|Nem|Ezen a diagramon használni a vizualizációt. Alapértelmezés szerint használ egy vonaldiagramot. Diagram típusa támogatott: `Bar, Line, Area, Scatter`.|
|metrics|Igen|A mérőszámok jeleníti meg a diagram tömbje. Az Azure Portalon támogatott mérőszámok kapcsolatos további információkért lásd: [az Azure monitorban támogatott mérőszámok](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Tulajdonság|Szükséges|Leírás|
|---------|---------|---------|
|name|Igen|A metrika neve.|
|aggregationType|Igen|Ez a metrika használandó összesítés típusa. Támogatott az összesítés típusa: `none, sum, min, max, avg, unique, percentile, count`|
|névtér|Nem|További információk a megfelelő mérőszámokat szolgáltató meghatározása során használandó.|
|resourceTagFilter|Nem|Az erőforrás-címkék tömb (elválasztva kell beírni `or` word) melyik metrikákhoz jelenik meg. Érvényes Erőforrásszűrő típusa fölött.|
|resourceType|Igen|Az erőforrás típusa, amelynek metrikák jelenik meg.|

## <a name="custom-resources"></a>Egyéni erőforrások

`"kind": "CustomResources"`

Megadhatja, hogy ilyen típusú több nézetet. Minden egyes nézet jelöli egy **egyedi** egyéni erőforrás írja be az egyéni szolgáltató a meghatározott **mainTemplate.json**. Egyéni szolgáltatók bemutatása, lásd: [Azure egyéni szolgáltatók előzetes áttekintése](custom-providers-overview.md).

Ebben a nézetben végezhet GET, PUT, törlése és közzététele a műveletek az egyéni erőforrás típusát. POST műveletek globális egyéni műveletek és a egy környezetben egyéni műveletek az egyéni erőforrás típusa lehet.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
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
|displayName|Igen|A nézetben megjelenített címe. A cím legyen **egyedi** az egyes CustomResources nézet a **viewDefinition.json**.|
|version|Nem|A platform, a nézet megjelenítéséhez használt verziója.|
|resourceType|Igen|Az egyéni erőforrás típusa. Kell lennie egy **egyedi** egyéni erőforrás típusa, az egyéni szolgáltató.|
|createUIDefinition|Nem|UI-definíció létrehozása a séma létrehozása egyéni erőforrás parancs. Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md)|
|Parancsok|Nem|Lásd a CustomResources nézet, kiegészítő gombok tömbjének [parancsok](#commands).|
|Oszlopok|Nem|Az egyéni erőforrás oszlopok tömbje. Ha nincs definiálva a `name` oszlop alapértelmezés szerint megjelenik. Rendelkeznie kell az oszlop `"key"` és `"displayName"`. Kulcs adja meg a nézetben megjelenítendő tulajdonság kulcsát. Ha egymásba, dot elválasztó alapján, mint például használja `"key": "name"` vagy `"key": "properties.property1"`. Megjelenített név adja meg a nézetben megjelenítendő tulajdonság megjelenített neve. Meg lehet adni egy `"optional"` tulajdonság. Ha igaz értékű, az oszlop rejtett nézetben alapértelmezés szerint.|

## <a name="commands"></a>Parancsok

Parancsok az oldalon megjelenített további eszköztárgombok tömbjét. Minden parancs jelöli az Azure egyéni szolgáltató meghatározott egy POST művelet **mainTemplate.json**. Egyéni szolgáltatók bemutatása, lásd: [Azure egyéni szolgáltatók áttekintése](custom-providers-overview.md).

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
|displayName|Igen|A parancs gombjára megjelenített neve.|
|elérési út|Igen|Az egyéni szolgáltató művelet neve. A műveletet meg kell határozni az **mainTemplate.json**.|
|Ikon|Nem|A parancs gombjára ikon. Támogatott ikonok listájából meghatározott [JSON-sémájában](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nem|Hozzon létre felhasználói felület szolgáltatásdefiníciós sémában parancsot. Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).|

## <a name="next-steps"></a>További lépések

- A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](overview.md) cikk ismerteti.
- Egyéni szolgáltatók bemutatása, lásd: [Azure egyéni szolgáltatók áttekintése](custom-providers-overview.md).
