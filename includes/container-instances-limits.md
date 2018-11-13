---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572232"
---
| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Tárolócsoportok száma [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Tárolók száma tárolócsoportonként | 60 |
| Kötetek száma tárolócsoportonként | 20 |
| Portok száma IP-címenként | 5 |
| Óránként létrehozott tárolók száma |300<sup>1</sup> |
| 5 percenként létrehozott tárolók száma | 100<sup>1</sup> |
| Óránként törölt tárolók száma | 300<sup>1</sup> |
| 5 percenként törölt tárolók száma | 100<sup>1</sup> |
| Több tároló tárolócsoportonként | Csak Linux<sup>2</sup> |
| Azure Files-kötetek | Csak Linux<sup>2</sup> |
| GitRepo-kötetek | Csak Linux<sup>2</sup> |
| Titkos kötetek | Csak Linux<sup>2</sup> |

<sup>1</sup> [Azure-támogatási kérés][azure-support] létrehozása a korlátozás emelésének kérvényezéséhez.<br />
<sup>2</sup> A funkció Windows-támogatása tervezés alatt áll.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
