---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: dd5c8878ce71b49b3a25f5d14a00bfe4f49ee769
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333854"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Tárolócsoportok száma [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Tárolók száma tárolócsoportonként | 60 |
| Kötetek száma tárolócsoportonként | 20 |
| Portok száma IP-címenként | 5 |
| Tároló példány naplóméret - példányt futtató | 4 MB |
| Tároló példány naplóméret - leállított példány | 16 KB-os vagy 1000 sort |
| Óránként létrehozott tárolók száma |300<sup>1</sup> |
| 5 percenként létrehozott tárolók száma | 100<sup>1</sup> |
| Óránként törölt tárolók száma | 300<sup>1</sup> |
| 5 percenként törölt tárolók száma | 100<sup>1</sup> |


<sup>1</sup> [Azure-támogatási kérés][azure-support] létrehozása a korlátozás emelésének kérvényezéséhez.<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
