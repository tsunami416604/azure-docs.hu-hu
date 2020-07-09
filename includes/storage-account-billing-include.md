---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74851628"
---
A Storage-fiók használata alapján számlázunk az Azure Storage-hoz. A tárfiókban lévő összes objektum számlázása együtt, csoportosan történik. 

A tárolási költségek kiszámítása a következő tényezők szerint történik: 

* A **régió** a fiók alapjául szolgáló földrajzi régióra utal.
* A **fióktípus** a használt Storage-fiók típusára utal. 
* A **hozzáférési réteg** az általános célú v2-vagy blob Storage-fiókhoz megadott adathasználati mintára vonatkozik.
* A tárolókapacitás arra utal, hogy mekkora **a Storage-** fiók kiosztása, amelyet az adattároláshoz használ.
* A **replikáció** meghatározza, hogy a rendszer egyszerre hány példányt tart fenn az adataiban, és milyen helyeken.
* A **tranzakciók** az Azure Storage-ba irányuló összes olvasási és írási műveletre vonatkoznak.
* A **kimenő adatforgalom** az Azure-régióból kivitt összes adatforgalomra vonatkozik. Ha egy olyan alkalmazás fér hozzá a Storage-fiókban lévő adataihoz, amely nem ugyanabban a régióban fut, akkor a kimenő adatforgalomért kell fizetnie. További információ arról, hogyan lehet erőforráscsoportok használatával csoportosítani az adatokat és szolgáltatásokat ugyanabban a régióban a kimenő forgalomra vonatkozó díjak csökkentése érdekében: [Mi az az Azure-erőforráscsoport?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Az [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lap részletes információkat biztosít a fióktípussal, a tárolási kapacitással, a replikálással és a tranzakciókkal kapcsolatban. Az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) tartalmazza a kimenő adatforgalommal kapcsolatos részletes díjszabási információkat. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) használatával megbecsülheti költségeit.

