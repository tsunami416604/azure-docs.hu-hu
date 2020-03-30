---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334820"
---
| Erőforrás | Korlát |
| --- | :--- |
| Standard sku tárolócsoportok régiónként [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Dedikált sku tárolócsoportok régiónként [és előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) | 0<sup>1.</sup> |
| Tárolók száma tárolócsoportonként | 60 |
| Kötetek száma tárolócsoportonként | 20 |
| Portok száma IP-címenként | 5 |
| Tárolópéldány naplójának mérete – futó példány | 4 MB |
| Tárolópéldány naplójának mérete – leállított példány | 16 KB vagy 1000 sor |
| Óránként létrehozott tárolók száma |300<sup>1</sup> |
| 5 percenként létrehozott tárolók száma | 100<sup>1</sup> |
| Óránként törölt tárolók száma | 300<sup>1</sup> |
| 5 percenként törölt tárolók száma | 100<sup>1</sup> |


<sup>1 1</sup> A korlát növelésének kéréséhez hozzon létre egy [Azure-támogatási kérelmet.][azure-support]<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
