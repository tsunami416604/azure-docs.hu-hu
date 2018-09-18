---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f47146cef5a81476e76ee5bde3990fac1323148c
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740802"
---
Azure Storage, a storage-fiók használat alapján számlázzuk ki. A tárfiókban lévő összes objektum számlázása együtt, csoportosan történik. 

Tárolási költségek kiszámítása a következő tényezőktől: régió/hely, fióktípust, hozzáférési szint, tárolókapacitás, replikálási séma, tárolási tranzakciók és kimenő adatforgalom.

* **Régió** a földrajzi régióban, amelyben a fiók alapján hivatkozik.
* **Fiók típusa** használja a tárfiók típusára utal. 
* **Hozzáférési szint** hivatkozik az általános célú v2 és Blob storage-fiók a megadott adatok használati módja.
* Tárolási **kapacitás** vonatkozik, hogy tárfiókja mekkora mennyi áll használata az adatok tárolására.
* **Replikációs** határozza meg, hány példányban az adatok egy időben, és hol tárolja.
* **Tranzakciók** tekintse meg az összes olvasási és írási műveletek az Azure Storage.
* **Kimenő adatforgalom** bármilyen egy Azure-régión kívül átvitt adatok értendők. Amikor a tárfiókban lévő adatokat egy olyan alkalmazás használja, amely nem ugyanabban a régióban fut, a kimenő adatforgalom díjköteles. További információ az erőforráscsoportok segítségével adatait és szolgáltatások korlátozhatja a kimenő forgalom költségeit ugyanabban a régióban: [Mi az Azure-erőforráscsoport?](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group). 

Az [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lap részletes információkat biztosít a fióktípussal, a tárolási kapacitással, a replikálással és a tranzakciókkal kapcsolatban. Az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) tartalmazza a kimenő adatforgalommal kapcsolatos részletes díjszabási információkat. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) használatával megbecsülheti költségeit.

