---
title: Az Azure Service Fabric CLI - sfctl van |} Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl parancsok.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 11c7a10c6448248ffacee2b519f48b6ca7e4e188
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-is"></a>sfctl van
Lekérdezi és parancsainak elküldését az infrastruktúra-szolgáltatás.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    a parancs| Meghívja az adott infrastruktúra szolgáltatáspéldányon egy felügyeleti parancs.|
|    lekérdezés  | Meghívja az adott infrastruktúra szolgáltatáspéldányon írásvédett lekérdezést.|


## <a name="sfctl-is-command"></a>sfctl parancs
Meghívja az adott infrastruktúra szolgáltatáspéldányon egy felügyeleti parancs.

Fürtöknél az infrastruktúra-szolgáltatás, konfigurálva egy vagy több példányát Ez az API biztosítja az infrastruktúra-specifikus parancsokat küldhet az adott példányára. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni. .

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] parancsot| Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus.  : Alapértelmezés parancsot.|
| --szolgáltatás-azonosítója     | Az infrastruktúra-szolgáltatás identitásának. Az infrastruktúra-szolgáltatás nélkül teljes neve a "fabric:" URI-séma. Ez a paraméter csak az infrastruktúra-szolgáltatás fut egynél több példánnyal rendelkező fürtök kötelező.|
| – időtúllépés -t     | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-is-query"></a>sfctl Ez a lekérdezés
Meghívja az adott infrastruktúra szolgáltatáspéldányon írásvédett lekérdezést.

Fürtöknél az infrastruktúra-szolgáltatás, konfigurálva egy vagy több példányát Ez az API biztosítja az infrastruktúra-specifikus lekérdezéseket küldjön az infrastruktúra-szolgáltatás adott példányára. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] parancsot| Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus.  : Alapértelmezés lekérdezés.|
| --szolgáltatás-azonosítója     | Az infrastruktúra-szolgáltatás identitásának. Az infrastruktúra-szolgáltatás nélkül teljes neve a "fabric:" URI-séma. Ez a paraméter csak több mint egy infrastruktúra-szolgáltatás fut-példánnyal rendelkező fürtök szükség.|
| – időtúllépés -t     | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>Következő lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).