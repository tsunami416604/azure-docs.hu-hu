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
ms.openlocfilehash: ed7bfca6095dbb03042efd14456f34556f74a843
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179341"
---
Azure Storage, a storage-fiók használat alapján számlázzuk ki. A tárfiókban lévő összes objektum számlázása együtt, csoportosan történik. 

Tárolási költségek kiszámítása a következő tényezőktől: régió/hely, fióktípust, hozzáférési szint, tárolókapacitás, replikálási séma, tárolási tranzakciók és kimenő adatforgalom.

* **Régió** a földrajzi régióban, amelyben a fiók alapján hivatkozik.
* **Fiók típusa** használja a tárfiók típusára utal. 
* **Hozzáférési szint** hivatkozik az általános célú v2 és Blob storage-fiók a megadott adatok használati módja.
* Tárolási **kapacitás** vonatkozik, hogy tárfiókja mekkora mennyi áll használata az adatok tárolására.
* **Replikációs** határozza meg, hány példányban az adatok egy időben, és hol tárolja.
* **Tranzakciók** tekintse meg az összes olvasási és írási műveletek az Azure Storage.
* **Kimenő adatforgalom** bármilyen egy Azure-régión kívül átvitt adatok értendők. Amikor a tárfiókban lévő adatokat egy olyan alkalmazás használja, amely nem ugyanabban a régióban fut, a kimenő adatforgalom díjköteles. További információ az erőforráscsoportok segítségével adatait és szolgáltatások korlátozhatja a kimenő forgalom költségeit ugyanabban a régióban: [Mi az Azure-erőforráscsoport?](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group). 

Az [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lap részletes információkat biztosít a fióktípussal, a tárolási kapacitással, a replikálással és a tranzakciókkal kapcsolatban. Az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) tartalmazza a kimenő adatforgalommal kapcsolatos részletes díjszabási információkat. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) használatával megbecsülheti költségeit.

