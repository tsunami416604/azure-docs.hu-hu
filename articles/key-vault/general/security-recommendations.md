---
title: Biztonsági javaslatok az Azure Key Vaulthoz
description: Az Azure Key Vault biztonsági javaslatai. Ezen útmutató végrehajtása segít önnek a közös felelősségi modellünkben leírt biztonsági kötelezettségek teljesítésében
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: b7bd85508851df7c7a664862b02141bf4d3bea1f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429849"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Biztonsági javaslatok az Azure Key Vaulthoz

Ez a cikk az Azure Key Vault biztonsági javaslatait tartalmazza. Ezeknek az ajánlásoknak a végrehajtása segít a közös felelősségi modellünkben leírt biztonsági kötelezettségek teljesítésében. Ha többet szeretne tudni arról, hogy a Microsoft mit tesz a szolgáltatói feladatok teljesítése érdekében, olvassa el [a Megosztott felelősségi körök a felhőalapú számítástechnikával](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)kapcsolatos című információt.

A cikkben szereplő javaslatok közül néhányat az Azure Security Center automatikusan figyelhet. Az Azure Security Center az első védelmi vonal az erőforrások védelmében az Azure-ban. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát a potenciális biztonsági rések azonosításához. Ezután ajánlásokat ad arra vonatkozóan, hogyan kell kezelni őket.

- Az Azure Security Center-javaslatokról az [Azure Security Center biztonsági javaslatai](../../security-center/security-center-recommendations.md)című témakörben talál további információt.
- Az Azure Security Centerről a [Mi az Azure Security Center?](../../security-center/security-center-intro.md)

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
|Helyreállítható törlés engedélyezése | [Helyreállítható törlés](overview-soft-delete.md)) lehetővé teszi a törölt tárolók és tárolóobjektumok helyreállítását |  - |
| A tárolóadatokhoz való hozzáférés korlátozása  | Kövesse a minimális jogosultság elvét, és korlátozza, hogy a szervezet tagjai mely tárolóadatokhoz férhetnek hozzá |  - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Közreműködői hozzáféréssel rendelkező felhasználók számának korlátozása | Ha egy felhasználó közreműködői engedélyekkel rendelkezik egy key vault felügyeleti síkhoz, a felhasználó hozzáférést biztosíthat magának az adatsíkhoz a Key Vault hozzáférési szabályzat beállításával. Szorosan szabályozhatja, hogy ki rendelkezik közreműködői szerepkör-hozzáféréssel a kulcstartókhoz. Győződjön meg arról, hogy csak azok férhetnek hozzá és kezelhetik a tárolókat, akiknek hozzáférésre van szükségük. Elolvashatja [biztonságos hozzáférést a key vault)](secure-your-key-vault.md) | - |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
 A Key Vaultban engedélyezni kell a diagnosztikai naplókat | Engedélyezze a naplókat, és őrizze meg őket egy évig. Ez lehetővé teszi, hogy újra tevékenységnyomvonalak at vizsgálati célokra, ha egy biztonsági incidens, vagy a hálózat veszélybe kerül. | [Igen](../../security-center/security-center-identity-access.md) |
| Annak korlátozása, hogy ki férhet hozzá az Azure Key Vault-naplókhoz | [Key Vault naplók](logging.md)) a trezorban végzett tevékenységekkel kapcsolatos információk mentése, például a tárolók, kulcsok, titkos kulcsok létrehozása vagy törlése, és a vizsgálat során felhasználhatók |  - |

## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
|Hálózati expozíció korlátozása | A hálózati hozzáférést a tárolóhoz való hozzáférést igénylő megoldások által használt virtuális hálózatokra kell korlátozni. Az [Azure Key Vault virtuális hálózati szolgáltatásának végpontjaira](overview-vnet-service-endpoints.md)vonatkozó információk áttekintése ) | - |

## <a name="next-steps"></a>További lépések

Érdeklődjön az alkalmazás szolgáltatójánál, hogy vannak-e további biztonsági követelmények. A biztonságos alkalmazások fejlesztéséről a Biztonságos fejlesztési dokumentáció című témakörben olvashat [bővebben.](../../security/fundamentals/abstract-develop-secure-apps.md)
