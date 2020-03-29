---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851628"
---
Az Azure Storage-ért a tárfiók használata alapján számlázunk. A tárfiókban lévő összes objektum számlázása együtt, csoportosan történik. 

A tárolási költségeket a következő tényezők alapján számítják ki: 

* **A régió** arra a földrajzi régióra utal, amelyben a fiók alapul.
* **A fióktípus** a használt tárfiók típusára vonatkozik. 
* **Access tier** hivatkozik az általános célú v2 vagy Blob storage-fiók megadott adathasználati mintát.
* A tárolási **kapacitás** azt a következőket teszi, hogy a tárfiók lekötése mennyit használ az adatok tárolásához.
* **A replikáció** határozza meg, hogy az adatok hány példánya és mely helyen van megtartva.
* **A tranzakciók** az Azure Storage összes olvasási és írási műveletére vonatkoznak.
* **Az adatok kimenő forgalom** az Azure-régióból átvitt adatokat. Ha a tárfiókban lévő adatokhoz olyan alkalmazás érhető el, amely nem ugyanabban a régióban fut, az adatforgalomért díjat számítunk fel. Az erőforráscsoportok használatával csoportosíthatja az adatokat és szolgáltatásokat ugyanabban a régióban a kimenő forgalom díjainak korlátozása érdekében, [lásd: Mi az Azure-erőforráscsoport?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) 

Az [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lap részletes információkat biztosít a fióktípussal, a tárolási kapacitással, a replikálással és a tranzakciókkal kapcsolatban. Az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) tartalmazza a kimenő adatforgalommal kapcsolatos részletes díjszabási információkat. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) használatával megbecsülheti költségeit.

