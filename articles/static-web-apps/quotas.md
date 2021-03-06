---
title: Kvóták az Azure statikus Web Apps előzetes verziójában
description: Tudnivalók az Azure statikus Web Apps előzetes verziójához kapcsolódó kvóták használatáról
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 915f8675ffda0d70347905d11c7d93975b9d4526
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132737"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Kvóták az Azure statikus Web Apps előzetes verziójában

Az Azure statikus Web Apps előzetes verziójának következő kvótái léteznek.

> [!IMPORTANT]
> Az Azure statikus Web Apps nyilvános előzetes verzióban érhető el, és nem éles használatra készült.

| Szolgáltatás                     | Ingyenes csomag        |
|-----------------------------|------------------|
| Belefoglalt sávszélesség          | 100 GB/hó |
| Túlhasználati sávszélesség           | Nem érhető el      |
| Alkalmazások/Azure-előfizetés | 10               |
| Alkalmazás mérete                    | 250 MB           |
| Éles üzem előtti környezetek | 1                |
| Egyéni tartományok              | 1                |
| Engedélyezés<br><br>Egyéni szerepkörökkel és útválasztási szabályokkal | Legfeljebb 25 végfelhasználó meghívott és hozzárendelt szerepkörök |
| Azure Functions             | Elérhető        |
| SLA                         | Nincsenek             |

## <a name="github-storage"></a>GitHub Storage

A GitHub-műveletek és-csomagok a GitHub Storage-t használják, amely a kvóták saját készletével rendelkezik. Amikor létrehoz egy Azure statikus Web Apps helyet, a GitHub az üzembe helyezés után is tárolja a webhely összetevőit.

További részletekért tekintse meg a következő forrásokat:

- [Műveletek tárhelyének kezelése](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [A GitHub-műveletek számlázása](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [A GitHub-műveletek költségkeretének kezelése](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Következő lépések

- [Áttekintés](overview.md)
