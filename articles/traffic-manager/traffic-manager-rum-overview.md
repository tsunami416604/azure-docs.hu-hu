---
title: Valós felhasználói mérések az Azure Traffic Managerben
description: Ebben a bevezetőben megtudhatja, hogyan működnek az Azure Traffic Manager valós felhasználói mérései.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938429"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager – valós felhasználói mérések áttekintése

Amikor egy Traffic Manager-profilt állít be a teljesítmény-útválasztási módszer használatára, a szolgáltatás megvizsgálja, hogy a DNS-lekérdezési kérelmek honnan érkeznek, és útválasztási döntéseket hoz, hogy ezeket a kéréseket az Azure-régióba irányítsa, amely a legalacsonyabb késést biztosítja számukra. Ez úgy érhető el, hogy a Traffic Manager által a különböző végfelhasználói hálózatokhoz fenntartott hálózati késleltetési intelligencia felhasználásával valósítható meg.

A valós felhasználói mérések lehetővé teszik a hálózati késés méréseinek mérését az Azure-régiókba, a végfelhasználók által használt ügyfélalkalmazásokból, és a Traffic Manager figyelembe veszi ezeket az információkat az útválasztási döntések meghozatalakor is. Ha a Valós felhasználói mérések et választja, növelheti az útválasztás pontosságát azon hálózatokról érkező kérelmek esetében, ahol a végfelhasználók tartózkodnak. 

## <a name="how-real-user-measurements-work"></a>A valós felhasználói mérések működésének

A valós felhasználói mérések úgy működnek, hogy az ügyfélalkalmazások mérik a késést az Azure-régiókban, ahogy az a végfelhasználói hálózatokból látható, amelyekben használják őket. Ha például olyan weboldallal rendelkezik, amelyet a felhasználók különböző helyeken (például az észak-amerikai régiókban) elérnek, a Valós felhasználói mérések teljesítmény-útválasztási módszerrel lehetővé teheti számukra, hogy eljussanak a legjobb Azure-régióba, ahol a kiszolgáló alkalmazás házigazdája.

Úgy kezdődik, hogy beágyaz egy Azure-t biztosított JavaScript (egy egyedi kulcs van benne) a weboldalakat. Ha ez megtörtént, amikor egy felhasználó felkeresi a weblapot, a JavaScript lekérdezi a Traffic Managert, hogy információkat kapjon az általa mérnikívánt Azure-régiókról. A szolgáltatás végpontok készletét adja vissza a parancsfájlnak, amely ezután folyamatosan méri ezeket a régiókat azáltal, hogy egyetlen képpontképet tölt le az adott Azure-régiókban, és megjegyzi a kérés elküldésének időpontja és az első bájt fogadásának időpontja közötti késést. . Ezeket a méréseket ezután jelentik vissza a Traffic Manager szolgáltatásnak.

Idővel ez sokszor és számos hálózaton keresztül történik, ami ahhoz vezet, hogy a Traffic Manager pontosabb információkat kap a végfelhasználók at használó hálózatok késési jellemzőiről. Ez az információ kezd belemenni a Traffic Manager által hozott útválasztási döntésekbe. Ennek eredményeképpen a valós felhasználói mérések en alapuló döntések pontosságának növelése.

A Valós felhasználói mérések használatakor a Traffic Managernek küldött mérések száma alapján számlázunk. Az árképzésről további információt a [Traffic Manager díjszabási oldalán talál.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>Gyakori kérdések

* [Milyen előnyökkel jár a valós felhasználói mérések használata?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Használhatom a valós felhasználói méréseket nem Azure-régiókkal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Melyik útválasztási módszer élvezi a valós felhasználói mérések előnyeit?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Külön-külön kell engedélyeznem a valós felhasználói méréseket az egyes profilokban?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Hogyan kapcsolhatom ki a Valódi felhasználói méréseket az előfizetésemhez?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Használhatom a Valós felhasználói méréseket a weboldalaktól eltérő ügyfélalkalmazásokkal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Hány mérés történik minden alkalommal, amikor a Valós felhasználói mérések et engedélyezve weboldalam megjelenik?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Késleltetés van, mielőtt a Valós felhasználói mérések parancsfájl elindul na a weboldalamon?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Használhatom a valós felhasználói méréseket csak azokat az Azure-régiókat, amelyeket mérni szeretnék?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Korlátozhatom a mérések számát egy adott számra?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Megnézhetem az ügyfélalkalmazás által a valós felhasználói mérések részeként végzett méréseket?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Módosíthatom a Traffic Manager által biztosított mérési parancsfájlt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Lehetséges lesz-e, hogy mások is lássák a kulcsot, amelyet a valós felhasználói mérésekhez használok?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Mások is visszaélhetnek a RUM-kulcsommal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [A mérési JavaScriptet minden weboldalamra be kell helyeznem?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [A Forgalomkezelő azonosíthatja a végfelhasználókkal kapcsolatos információkat, ha valós felhasználói méréseket használok?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [A valós felhasználói méréseket mérő weblapnak a Traffic Managert kell használnia az útválasztáshoz?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Kell-e olyan szolgáltatást üzemeltetnem az Azure-régiókban, amelyet a valós felhasználói mérésekhez használhatok?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Növekedni fog az Azure sávszélesség-használata a valós felhasználói mérések használatakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan használhatja a [valós felhasználói méréseket weboldalakkal](traffic-manager-create-rum-web-pages.md)
- A [Traffic Manager működésének megismerése](traffic-manager-overview.md)
- További információ a [Mobilközpontról](https://docs.microsoft.com/mobile-center/)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- További információ a [Traffic Manager-profil létrehozásáról](traffic-manager-create-profile.md)

