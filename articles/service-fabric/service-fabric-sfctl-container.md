---
title: Az Azure Service Fabric CLI - sfctl tároló |} Microsoft Docs
description: A Service Fabric CLI sfctl tároló parancsok ismerteti.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764018"
---
# <a name="sfctl-container"></a>sfctl tároló
Tároló kapcsolatos parancsok futtatnak egy fürt csomópontja.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Invoke-api | Tároló REST API hívása. |
| naplók | Tároló naplók lekérése során. |

## <a name="sfctl-container-invoke-api"></a>invoke sfctl tároló-api
Tároló REST API hívása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Alkalmazás azonosítója. |
| --kódot--példány-csomagazonosító [szükséges] | Kód példány Csomagazonosítót "szolgáltatás kódot--csomaglista" lekérhetők. |
| --kódot--csomagnév [szükséges] | Csomag neve. |
| --tároló-api-uri-elérési út [szükséges] | Tároló REST API URI elérési útja, '{Azonosítójú}' használata helyett a tároló neve vagy azonosítója. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --jegyzékfájl-szolgáltatásnév [szükséges] | Szolgáltatás jegyzékfájljának nevét. |
| --tároló-api-szervezet | HTTP-kérés törzsében tároló REST API-t. |
| --tároló-api-tartalomtípus | A tároló REST API-t az alapértelmezett érték az "application/json" tartalomtípusa. |
| --tároló-api-http-művelet | HTTP-műveletet a tároló REST API-t alapértelmezés szerint használt érték a GET. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-container-logs"></a>sfctl tároló naplók
Tároló naplók lekérése során.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Alkalmazás azonosítója. |
| --kódot--példány-csomagazonosító [szükséges] | Kód példány Csomagazonosítót "szolgáltatás kódot--csomaglista" lekérhetők. |
| --kódot--csomagnév [szükséges] | Csomag neve. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --jegyzékfájl-szolgáltatásnév [szükséges] | Szolgáltatás jegyzékfájljának nevét. |
| – kiegészítő | A naplók végén csak vissza a napló sorok száma. Adja meg az egész szám vagy az összes kimeneti összes napló sort. Alapértelmezés szerint az összes. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="next-steps"></a>További lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).