---
title: Az Azure Service Fabric biztonsági vezérlői
description: Ismerje meg az Azure Service Fabric biztonsági vezérlőit. Tartalmazza a beépített biztonsági vezérlők ellenőrzőlistáját.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645429"
---
# <a name="security-controls-for-azure-service-fabric"></a>Az Azure Service Fabric biztonsági vezérlői

Ez a cikk az Azure Service Fabric beépített biztonsági vezérlőket dokumentálja. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| Igen |  |
| A VNet injekciózás támogatása| Igen |  |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | Hálózati biztonsági csoportok (NSG) használata. |
| Kényszerített bújtatástámogatása| Igen | Az Azure-hálózatkényszerített bújtatás. |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Az Azure figyelési támogatás ának és a külső felek által nyújtott támogatásnak a használata. |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | Minden vezérlősík-művelet naplózási és jóváhagyási folyamatokon fut. |
| Adatsík naplózása és naplózása| N/A | Az ügyfél a fürt tulajdonosa.  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | A hitelesítés az Azure Active Directoryn keresztül történik. |
| Engedélyezés| Igen | Az SFRP-n keresztüli hívások identitás- és hozzáférés-kezelése (IAM). A fürtvégpontra irányuló közvetlen hívások két szerepkört támogatnak: felhasználó és rendszergazda. Az ügyfél leképezheti az API-kat bármelyik szerepkörhöz. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | Az ügyfél tulajdonában van a fürt és a virtuálisgép-méretezési készlet, amelyre a fürt épül. Az Azure lemeztitkosítás engedélyezhető a virtuálisgép-méretezési csoportban. |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél tulajdonában van a fürt és a virtuálisgép-méretezési készlet, amelyre a fürt épül. Az Azure lemeztitkosítás engedélyezhető a virtuálisgép-méretezési csoportban. |
| Oszlopszintű titkosítás (Azure Data Services)| N/A |  |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen |  |
| TITKOSÍTOTT API-hívások| Igen | A Service Fabric API-hívásai az Azure Resource Manageren keresztül történnek. Érvényes JSON-webtoken (JWT) szükséges. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)