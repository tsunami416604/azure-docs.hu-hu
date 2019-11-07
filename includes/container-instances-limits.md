---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 3608eb6182f7806c0d4df8b96aa69faac68928f1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590834"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Tároló csoportok régiónként/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
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
