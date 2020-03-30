---
title: Az Azure Service Bus Messaging biztonsági vezérlői
description: Az Azure Service Bus messaging kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903250"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Az Azure Service Bus Messaging biztonsági vezérlői

Ez a cikk az Azure Service Bus-üzenetek beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| A szolgáltatás végpontjának támogatása| Igen (csak prémium szint) | VNet-szolgáltatás végpontok csak [a Service Bus prémium szintű támogatottak.](service-bus-premium-messaging.md) |  |
| A VNet injekciózás támogatása| Nem | |  |
| Hálózati elkülönítés és tűzfaltámogatás| Igen (csak prémium szint) |  |  |
| Kényszerített bújtatástámogatása| Nem |  |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Az [Azure Monitor és a Riasztások](service-bus-metrics-azure-monitor.md)szolgáltatás támogatja. |  |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | Műveleti naplók állnak rendelkezésre.  | [Service Bus diagnosztikai naplók](service-bus-diagnostic-logs.md) |
| Adatsík naplózása és naplózása| Nem |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | Az [Azure Active Directory felügyelt szolgáltatásidentitásán](service-bus-managed-service-identity.md)keresztül kezelhető.| [A Service Bus hitelesítése és engedélyezése](service-bus-authentication-and-authorization.md). |
| Engedélyezés| Igen | Támogatja az [RBAC](authenticate-application.md) és SAS-jogkivonaton keresztüli engedélyezést. | [A Service Bus hitelesítése és engedélyezése](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok |  Igen a kiszolgálóoldali inaktív titkosítás esetén. |  |  |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen. | Az Azure KeyVault ügyfél által felügyelt kulcs segítségével titkosíthatja az adatokat a Service Bus névtér inaktív. | [Ügyfél által felügyelt kulcsok konfigurálása az Azure Service Bus inaktív adatainak titkosításához az Azure Portal használatával](configure-customer-managed-key.md)  |
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |   |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen | Támogatja a szabványos HTTPS/TLS mechanizmust. |   |
| TITKOSÍTOTT API-hívások| Igen | Az API-hívások az [Azure Resource Manageren](../azure-resource-manager/index.yml) és a HTTPS-en keresztül történnek. |   |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | Támogatja az erőforrás-szolgáltató verzióverzióját az [Azure Resource Manager API-n](/rest/api/resources/)keresztül.|   |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)
