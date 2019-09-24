---
title: A Azure Key Vault biztonsági javaslatai
description: Azure Key Vault biztonsági javaslatai. Az útmutató megvalósítása a megosztott felelősségi modellben leírtak szerint biztosítja a biztonsági kötelezettségek teljesítését
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/23/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 90965618b1d2cb126d56453d46a5bebc85319c0f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219640"
---
# <a name="security-recommendations-for-azure-key-vault"></a>A Azure Key Vault biztonsági javaslatai

Ez a cikk a Azure Key Vault biztonsági javaslatait tartalmazza. A javaslatok megvalósítása a közös felelősségi modellben leírtaknak megfelelően segíti a biztonsági kötelezettségek teljesítését. Ha többet szeretne megtudni arról, hogy a Microsoft hogyan teljesíti a szolgáltatói feladatokat, olvassa el a [megosztott felelősségek a felhőalapú számítástechnika](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)terén című témakört.

A cikkben szereplő ajánlások némelyikét a Azure Security Center automatikusan nyomon követheti. A Azure Security Center az Azure-beli erőforrások védelmének első védelmi vonala. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a megoldására.

- Azure Security Center javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok a Azure Security Centerban](../security-center/security-center-recommendations.md).
- További információ az Azure Security Centerről: [What is Azure Security Center?](../security-center/security-center-intro.md)

## <a name="recommendations"></a>Javaslatok

| Category | Ajánlás | Megjegyzések | Security Center |
|-|-|----|--|
| Adatvédelem |Soft delete engedélyezése | A [Soft delete](key-vault-ovw-soft-delete.md) lehetővé teszi a törölt tárolók és tároló objektumok helyreállítását |  - |
| Adatvédelem | A tár adatelérésének korlátozása  | Kövesse a legalacsonyabb jogosultsági szint elvét, és korlátozza, hogy a szervezet tagjai hozzáférhessenek a tár adataihoz |  - |
| Identitás- és hozzáférés-kezelés | A közreműködői hozzáféréssel rendelkező felhasználók számának korlátozása | Ha a felhasználó közreműködői engedélyekkel rendelkezik egy kulcstartó felügyeleti síkon, akkor a felhasználó egy Key Vault hozzáférési szabályzat beállításával hozzáférést biztosíthat az adatsíkon. Szigorúan meg kell határoznia, hogy kinek van közreműködői szerepköre a kulcstartóhoz való hozzáféréshez. Győződjön meg arról, hogy csak azok férhetnek hozzá és kezelhetik a tárolókat, akiknek szükségük van a hozzáférésre jogosult személyek számára. [A Key vaulthoz való biztonságos hozzáférést](key-vault-secure-your-key-vault.md) is elolvashatja | - |
| Figyelés | A Key Vault diagnosztikai naplóit engedélyezni kell | Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. | [Igen](../security-center/security-center-identity-access.md) |
| Figyelés | Annak korlátozása, hogy ki férhet hozzá az Azure Key Vault naplóihoz | [Key Vault naplók](key-vault-logging.md) a tárolón végrehajtott tevékenységekről, például a tárolók, kulcsok és titkos kódok létrehozásáról vagy törléséről, valamint a vizsgálat során felhasználható adatokról |  - |
| Hálózat |Hálózati expozíció korlátozása | A hálózati hozzáférést a tár elérését igénylő megoldások által használt virtuális hálózatokra kell korlátozni. Tekintse át a [Azure Key Vault virtuális hálózati szolgáltatás végpontjának](key-vault-overview-vnet-service-endpoints.md) információit | - |

## <a name="next-steps"></a>További lépések

Érdeklődjön az alkalmazás szolgáltatójánál, hogy vannak-e további biztonsági követelmények. A biztonságos alkalmazások fejlesztésével kapcsolatos további információkért lásd a [biztonságos fejlesztési dokumentációt](../security/fundamentals/abstract-develop-secure-apps.md).
