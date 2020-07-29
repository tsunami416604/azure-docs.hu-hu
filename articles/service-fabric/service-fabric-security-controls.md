---
title: Az Azure Service Fabric biztonsági vezérlői
description: Ismerje meg az Azure Service Fabric biztonsági vezérlőit. A beépített biztonsági vezérlők ellenőrzőlistáját tartalmazza.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645429"
---
# <a name="security-controls-for-azure-service-fabric"></a>Az Azure Service Fabric biztonsági vezérlői

Ez a cikk az Azure Service Fabric beépített biztonsági vezérlőket dokumentálja. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Yes |  |
| VNet-befecskendezés támogatása| Yes |  |
| Hálózati elkülönítés és tűzfalak támogatása| Yes | Hálózati biztonsági csoportok (NSG) használata. |
| Kényszerített bújtatás támogatása| Yes | Az Azure Networking kényszerített bújtatást biztosít. |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Yes | Az Azure monitoring támogatás és a harmadik féltől származó támogatás használata. |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes | A vezérlési sík műveletei a naplózási és jóváhagyási folyamatokon keresztül futnak. |
| Adatsíkok naplózása és naplózása| N.A. | Az ügyfél tulajdonosa a fürt.  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Hitelesítés| Yes | A hitelesítés Azure Active Directoryon keresztül történik. |
| Engedélyezés| Yes | Az identitás-és hozzáférés-kezelés (IAM) a SFRP-on keresztüli hívásokhoz. A közvetlenül a fürt végpontja felé irányuló hívások két szerepkört támogatnak: felhasználó és rendszergazda. Az ügyfél bármely szerepkörhöz hozzárendelheti az API-kat. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Yes | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Oszlop szintű titkosítás (Azure Data Services)| N.A. |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Yes |  |
| Titkosított API-hívások| Yes | Service Fabric API-hívások Azure Resource Manageron keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).