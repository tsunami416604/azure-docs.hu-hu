---
title: Az Azure Service Fabric biztonsági attribútumai
description: Az Azure-Service Fabric értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443859"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Az Azure Service Fabric biztonsági attribútumai

Ez a cikk az Azure Service Fabric beépített biztonsági attribútumait dokumentálja. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)| Igen | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen |  |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | Service Fabric API-hívások Azure Resource Manageron keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges. |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Igen |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Hálózati biztonsági csoportok (NSG) használata. |
| Kényszerített bújtatás támogatása| Igen | Az Azure Networking kényszerített bújtatást biztosít. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Az Azure monitoring támogatás és a harmadik féltől származó támogatás használata. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Authorization| Igen | Az identitás-és hozzáférés-kezelés (IAM) a SFRP-on keresztüli hívásokhoz. A közvetlenül a fürt végpontja felé irányuló hívások két szerepkört támogatnak: Felhasználó és rendszergazda. Az ügyfél bármely szerepkörhöz hozzárendelheti az API-kat. |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A vezérlési sík műveletei a naplózási és jóváhagyási folyamatokon keresztül futnak. |
| Adatsíkok naplózása és naplózása| – | Az ügyfél tulajdonosa a fürt.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |