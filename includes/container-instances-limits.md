---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 055fbc652d0d72925cccfae40efff8b8eef2a8c2
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904477"
---
| Erőforrás | Alapértelmezett korlát |
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
