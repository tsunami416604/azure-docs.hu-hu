---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67179809"
---
[Az Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) egy vállalati [integrációs](https://azure.microsoft.com/product-categories/integration/) üzenetközvetítő. A szolgáltatásbusz kétféle kommunikációt támogat: várólistákat és témaköröket. 

A várólisták támogatják az alkalmazások közötti aszinkron kommunikációt. Az alkalmazás üzeneteket küld egy várólistába, amely tárolja az üzeneteket. A fogadó alkalmazás ezután csatlakozik a várólistából érkező üzenetekhez, és beolvassa azokat.

A témakörök támogatják a közzététel-előfizetési mintát, amely lehetővé teszi az üzenetlétrehozó és az üzenetküldő(k) közötti egy-a-többhöz kapcsolatot.