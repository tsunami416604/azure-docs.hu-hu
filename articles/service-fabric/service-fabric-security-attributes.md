---
title: Az Azure Service Fabric általános biztonsági attribútumok
description: Az Azure Service Fabric értékelésére közös biztonsági attribútumok ellenőrzőlista
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003061"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Az Azure Service Fabric biztonsági attribútumok

Ez a cikk az Azure Service Fabric beépített biztonsági attribútumok dokumentumok. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Az ügyfél a fürt és a fürt beépített beállítani a virtuálisgép-méretezési csoport tulajdonosa. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen |  |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Az ügyfél a fürt és a fürt beépített beállítani a virtuálisgép-méretezési csoport tulajdonosa. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | Service Fabric API-hívások Azure Resource Manageren keresztül történik. Egy érvényes JSON webes jogkivonat (JWT) megadása kötelező. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen |  |
| VNet-injektálási támogatás| Igen |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Hálózati biztonsági csoportok (NSG) használatával. |
| Kényszerített bújtatás támogatása| Igen | Azure-hálózatok biztosítják a kényszerített bújtatás. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Figyelés és külső támogatást az Azure használatával. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Identitás és hozzáférés-kezelés (IAM) keresztül SFRP-hívásokhoz. Közvetlenül a fürt végpontja hívások két szerepkör támogatja: Felhasználó és rendszergazda. Az ügyfél leképezheti az API-k bármelyik szerepkörhöz. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Az összes vezérlési síkjával végzett műveletek haladjon végig a naplózási és jóváhagyási folyamatok. |
| Adatsík naplózása és naplózása| – | Ügyfél a tulajdonosa a fürtöt.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | |