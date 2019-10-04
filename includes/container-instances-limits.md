---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: f8821060b98ebfc954a6e59abad60350e6779b76
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67179484"
---
| Resource | Alapértelmezett korlát |
| --- | :--- |
| Tárolócsoportok száma [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
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
