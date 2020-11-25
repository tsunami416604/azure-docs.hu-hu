---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017676"
---
| Mechanizmus | Hatókör |Korlátok | Támogatott szintű engedély |
|---|---|---|---|
| Azure RBAC-vel | Storage-fiókok, tárolók. <br>Erőforrások Azure-beli szerepkör-hozzárendeléseinek összevetése az előfizetés vagy az erőforráscsoport szintjén. | 2000 Azure szerepkör-hozzárendelések egy előfizetésben | Azure-szerepkörök (beépített vagy egyéni) |
| ACL| Könyvtár, fájl |32 ACL-bejegyzés (gyakorlatilag 28 ACL-bejegyzés) fájlonként és könyvtárként. Hozzáférés és alapértelmezett ACL-ek mindegyike rendelkezik saját 32 ACL-bejegyzési korláttal. |ACL-engedély|
