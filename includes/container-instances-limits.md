---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334820"
---
| Erőforrás | Korlát |
| --- | :--- |
| Standard SKU Container groups régiónként/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Dedikált SKU Container Group régiónként/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) | 0<sup>1</sup> |
| Tárolók száma tárolócsoportonként | 60 |
| Kötetek száma tárolócsoportonként | 20 |
| Portok száma IP-címenként | 5 |
| Tároló-példány naplójának mérete – futó példány | 4 MB |
| Tároló-példány naplójának mérete – leállított példány | 16 KB vagy 1 000 sor |
| Óránként létrehozott tárolók száma |300<sup>1</sup> |
| 5 percenként létrehozott tárolók száma | 100<sup>1</sup> |
| Óránként törölt tárolók száma | 300<sup>1</sup> |
| 5 percenként törölt tárolók száma | 100<sup>1</sup> |


<sup>1</sup> A határérték-növekedés igényléséhez hozzon létre egy [Azure-support Request][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
