---
title: Az Azure világítótoronyig és a Cloud Solution Provider program
description: Ha az erőforrás-kezelés az Azure-meghatalmazott, fontos fontos a biztonság és hozzáférés-vezérlés.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 09552c66d2dc841cff8b5cb52e26dc657568e08f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809959"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Az Azure világítótoronyig és a Cloud Solution Provider program

Ha Ön egy [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) partner, Azure-előfizetések segítségével a CSP program keretében az ügyfelek számára létrehozott elérheti a [felügyeletéhez más nevében (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funkciót. Ez a hozzáférés lehetővé teszi, hogy közvetlenül támogatja, konfigurálását és az ügyfelek előfizetéseinek kezelése.

A AOBO mechanizmus ügyfélkörnyezetekben teljes hozzáférést biztosít. Azure-erőforrás delegált felügyeleti együtt AOBO használatával javítja biztonsági azáltal, hogy csökkenteni a szükségtelen hozzáférés engedélyezése a felhasználók részletesebb engedélyek. 

## <a name="administer-on-behalf-of-aobo"></a>(AOBO) nevében felügyelete

A AOBO, minden felhasználó, aki a [Adminisztrátorügynökként](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) szerepkör a bérlő Azure-előfizetések létrehozása a CSP program AOBO hozzáférést kap. Minden olyan felhasználók, akik hozzáférhetnek a ügyfél sem az előfizetések a csoport tagjának kell lennie. AOBO nem teszi lehetővé a rugalmasságot, amelyek együttműködnek a különböző ügyfelek különböző csoportok létrehozásához, vagy ahhoz, hogy a csoportok és felhasználók különböző szerepkörökkel.

![Bérlő felügyeleti AOBO használatával](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Delegált Azure resource Managerrel

Az Azure-erőforrás-kezelés meghatalmazott, hozzárendelheti különböző csoportokhoz különböző ügyfelektől vagy szerepköröket, az alábbi ábrán látható módon. Mivel a felhasználók a megfelelő szintű hozzáférés delegált az Azure resource management szolgáltatáson keresztül lesz, csökkentheti a felhasználók száma, akik a felügyeleti ügynök szerepkörnek (és így a teljes AOBO hozzáféréssel rendelkezik). Ez nagyobb biztonságot nyújt, korlátozza az ügyfelek erőforrások felesleges elérését. Azt is, nagyobb rugalmasságot, ipari méretekben több ügyfelet kezel.

Előkészítés a következő leírt lépéseket követve olyan előfizetést, amelyet a CSP program keretében létrehozott [felvétele az Azure-előfizetéssel delegált erőforrás-kezelés](../how-to/onboard-customer.md). Minden olyan felhasználó, aki rendelkezik a felügyeleti ügynök szerepkör a bérlőben a bevezetési hajthat végre.

Vegye figyelembe, hogy a CSP program keretében létrehozott előfizetések, csak hozhat létre a szolgáltató bérlőjében a felügyeleti ügynök szerepkörrel rendelkező felhasználók támogatási kérelmeket. Delegált Azure-erőforrások kezelése során hozzáadott felhasználók nem tudják megnyitni a delegált erőforrások kapcsolatos támogatási kérelmeket ezeket az előfizetéseket a.

![Bérlő felügyeleti AOBO és az Azure használatával delegált erőforrás-kezelés](../media/csp-2.jpg)

## <a name="partner-admin-link"></a>Partner rendszergazdai hivatkozás

Az előkészített előfizetések nyomon követésére a hatás az ügyfélesetekből társíthatja a Microsoft Partner Network (MPN) Azonosítóját.

Ha Ön [egy felügyelt szolgáltatások ajánlat közzététele az Azure piactéren](../how-to/publish-managed-services-offers.md), az MPN-Azonosítóját a közzétevő profilhoz társítva, és az ajánlat automatikusan társítva lesz. Ez az ajánlat Azure erőforrásai által előállított bevétel lesz majd tulajdonítható a szervezet. Jelentési rendszerrel, például a Partnerközpont- vagy MPN partner a tesznek elérhetővé; ilyenek, Partner rendszergazdai hivatkozás (PAL) fog megjelenni.

Ha Ön [ügyfelek az Azure erőforrás-kezelés az Azure Resource Manager-sablonok használatával delegált](../how-to/onboard-customer.md), továbbra is társíthat az MPN-Azonosítóját az ügyfélesetekből gyakorolt a felismerés fogadásához, de kell Ehhez manuálisan. További információ: [Partnerazonosító csatolása az Azure-fiókokhoz](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). 

## <a name="next-steps"></a>További lépések

- Ismerje meg [több-bérlős felhasználói élmény](cross-tenant-management-experience.md).
- További információ a [Cloud Solution Provider program](https://docs.microsoft.com/partner-center/csp-overview).
