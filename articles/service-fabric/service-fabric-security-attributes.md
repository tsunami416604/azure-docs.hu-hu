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
ms.openlocfilehash: f6db40a35c289fa0870ac4c9e5e55b773c84f4f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679227"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Az Azure Service Fabric általános biztonsági attribútumok

Biztonsági integrálva van az Azure-szolgáltatások minden szempontját. Ez a cikk a gyakori biztonsági attribútumok az Azure Service Fabric beépített dokumentumok. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Az ügyfél a fürt és a fürt beépített beállítani a virtuálisgép-méretezési csoport tulajdonosa. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A Vnet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen |  |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Az ügyfél a fürt és a fürt beépített beállítani a virtuálisgép-méretezési csoport tulajdonosa. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | Service Fabric API-hívások Azure Resource Manageren keresztül történik. Egy érvényes JSON webes jogkivonat (JWT) megadása kötelező. |

## <a name="network-segmentation"></a>Hálózati Szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatásvégpont-támogatás| Igen |  |
| vNET-injektálási támogatás| Igen |  |
| Hálózatelkülönítés / tűzfalas támogatása| Igen | Hálózati biztonsági csoportok (NSG) használatával. |
| Kényszerített bújtatás támogatása | Igen | Azure-hálózatok biztosítják a kényszerített bújtatás. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Figyelés és külső támogatást az Azure használatával. |

## <a name="iam-support"></a>IAM-támogatás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hozzáférés-kezelés – hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Hozzáférés-kezelés – engedélyezés| Igen | Identitás és hozzáférés-kezelés (IAM) keresztül SFRP-hívásokhoz. Közvetlenül a fürt végpontja hívások két szerepkör támogatja: Felhasználó és rendszergazda. Az ügyfél leképezheti az API-k bármelyik szerepkörhöz. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti naplózás és a naplózási tervezése| Igen | Az összes vezérlési síkjával végzett műveletek haladjon végig a naplózási és jóváhagyási folyamatok. |
| Adatok naplózása és naplózási adatsík| – | Ügyfél a tulajdonosa a fürtöt.  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | A szolgáltatás konfigurációs, verzióval ellátott és üzembe helyezett üzembe az Azure használatával. A kód (alkalmazás- és futásidejű) rendszerverzióval ellátott Azure Build használatával.