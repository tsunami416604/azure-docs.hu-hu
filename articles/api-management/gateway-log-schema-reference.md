---
title: Hivatkozás – Azure API Management erőforrás-napló
description: Séma-referenciák az Azure API Management GatewayLogs erőforrás-naplójához
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 064ac21e01239f7be3ddca9e48089ce880c6af58
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380388"
---
# <a name="reference-api-management-resource-log-schema"></a>Hivatkozás: API Management erőforrás-napló sémája

Ez a cikk az Azure API Management GatewayLogs erőforrás-naplójának sémájának hivatkozását tartalmazza. A naplóbejegyzések a [legfelső szintű közös sémában](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema)található mezőket is tartalmazzák.

A API Management erőforrás-napló gyűjtésének engedélyezéséhez tekintse meg a [közzétett API-k figyelése](api-management-howto-use-azure-monitor.md#resource-logs)című témakört.

## <a name="gatewaylogs-schema"></a>GatewayLogs séma

Az egyes API-kérelmeket a következő tulajdonságok naplózzák.

| Tulajdonság  | Típus | Description |
| ------------- | ------------- | ------------- |
| method | sztring | A bejövő kérelem HTTP-metódusa |
| url | sztring | A bejövő kérelem URL-címe |
| responseCode | egész szám | Az ügyfélnek küldött HTTP-válasz állapotkódja |
| responseSize | egész szám | A kérelem feldolgozása során az ügyfélnek küldött bájtok száma | 
| cache | sztring | API Management gyorsítótár részvételének állapota a kérelmek feldolgozásakor (találat, hiányzik, nincs) | 
| apiId | sztring | Az aktuális kérelem API-entitásazonosítója | 
| operationId | sztring | Az aktuális kérelem műveleti entitásának azonosítója | 
| clientProtocol | sztring | A bejövő kérelem HTTP-protokolljának verziója |
| clientTime | egész szám | A teljes ügyfél I/O-ra fordított ezredmásodpercek száma (csatlakozás, küldés és bájtok fogadása) | 
| apiRevision | sztring | Az aktuális kérelem API-verziója | 
| clientTlsVersion| sztring | Az ügyfél-küldő kérelem által használt TLS-verzió |
| lastError | object | Sikertelen kérelem esetén az utolsó kérelem feldolgozási hibájának részletei | 
| backendMethod | sztring | A háttérrendszernek küldött kérelem HTTP-metódusa |
| backendUrl | sztring | A háttérrendszernek küldött kérelem URL-címe |
| backendResponseCode | egész szám | A háttérrendszertől érkezett HTTP-válasz kódja |
| backedProtocol | sztring | A háttérrendszernek küldött kérelem HTTP-protokolljának verziója |
| backendTime | egész szám | A háttérbeli IO-ra (csatlakozás, küldés és a bájtok fogadása) fordított ezredmásodpercek száma | 


## <a name="next-steps"></a>Következő lépések

* További információ a API Management API-k monitorozásáról: a [közzétett API-k figyelése](api-management-howto-use-azure-monitor.md)
* További információ az [Azure-beli erőforrás-naplók általános és szolgáltatás-specifikus sémájáról](../azure-monitor/platform/resource-logs-schema.md)

