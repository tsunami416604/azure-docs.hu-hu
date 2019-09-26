---
title: Az Azure Service Fabric biztonsági vezérlői
description: Az Azure-Service Fabric kiértékelésére szolgáló biztonsági ellenőrzési ellenőrzőlista
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: d62c7848588c494c8190f0d429ce2d6641928b52
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886758"
---
# <a name="security-controls-for-azure-service-fabric"></a>Az Azure Service Fabric biztonsági vezérlői

Ez a cikk az Azure Service Fabric beépített biztonsági vezérlőket dokumentálja. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Igen |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Hálózati biztonsági csoportok (NSG) használata. |
| Kényszerített bújtatás támogatása| Igen | Az Azure Networking kényszerített bújtatást biztosít. |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Az Azure monitoring támogatás és a harmadik féltől származó támogatás használata. |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A vezérlési sík műveletei a naplózási és jóváhagyási folyamatokon keresztül futnak. |
| Adatsíkok naplózása és naplózása| – | Az ügyfél tulajdonosa a fürt.  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Authorization| Igen | Az identitás-és hozzáférés-kezelés (IAM) a SFRP-on keresztüli hívásokhoz. A közvetlenül a fürt végpontja felé irányuló hívások két szerepkört támogatnak: Felhasználó és rendszergazda. Az ügyfél bármely szerepkörhöz hozzárendelheti az API-kat. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen |  |
| Titkosított API-hívások| Igen | Service Fabric API-hívások Azure Resource Manageron keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).