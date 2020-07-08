---
title: valós felhasználómérés az Azure-ban Traffic Manager
description: Ebben az oktatóanyagban megismerheti, hogyan működik az Azure Traffic Manager valós felhasználómérés.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76938429"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager – valós felhasználói mérések áttekintése

Ha Traffic Manager-profilt állít be a teljesítmény-útválasztási módszer használatára, a szolgáltatás a DNS-lekérdezési kérelmek helyét keresi, és lehetővé teszi, hogy az útválasztási döntések a legalacsonyabb késést biztosító Azure-régióba irányítsák ezeket a kérelmezőket. Ezt úgy érheti el, ha a hálózati késési intelligenciát használja, Traffic Manager a különböző végfelhasználói hálózatokat.

A valós felhasználómérés lehetővé teszi a hálózati késések mérését az Azure-régiókra, a végfelhasználók által használt ügyfélalkalmazásoktól, és az útválasztási döntések meghozatala során Traffic Manager figyelembe venni ezeket az információkat is. A valós felhasználómérés használatának kiválasztásával növelheti az Útválasztás pontosságát azoktól a hálózatokból érkező kérések esetében, amelyekben a végfelhasználók találhatók. 

## <a name="how-real-user-measurements-work"></a>A valós felhasználómérés működése

Valós felhasználómérés úgy működik, hogy az ügyfélalkalmazások az Azure-régiók késését mérik, mivel azok a végfelhasználói hálózatokban láthatók, amelyeken a használatban vannak. Ha például egy olyan weboldalt használ, amelyet a felhasználók különböző helyszíneken (például az észak-amerikai régiókban) keresztül férnek hozzá, akkor a valós felhasználómérés a teljesítmény-útválasztási módszerrel érheti el azokat a legjobb Azure-régióba, ahol a kiszolgálói alkalmazás fut.

Ez egy, az Azure által biztosított JavaScript (egy egyedi kulccsal) beágyazásával kezdődik a weblapokon. Miután a felhasználó meglátogatja a weboldalt, a JavaScript-lekérdezések Traffic Manager, hogy információt kapjanak az Azure-régiókról, amelyeknek mérhetőnek kell lenniük. A szolgáltatás végpontok számát adja vissza a parancsfájlnak, amely ezután az Azure-régiókban üzemeltetett egyetlen képpontos rendszerkép letöltésével és a kérés elküldésének időpontja és az első bájt fogadásának időpontja közötti késéssel méri le ezeket a régiókat. A rendszer ezután visszaküldi ezeket a mértékeket a Traffic Manager szolgáltatásnak.

Ez az idő múlásával többször is megtörténik, és számos hálózatban Traffic Manager, ami pontosabb információt kap a végfelhasználók által használt hálózatok késési jellemzőiről. Ezek az információk megkezdik a Traffic Manager által végzett útválasztási döntések beszerzését. Ennek eredményeképpen az eljuttatott valós felhasználómérés alapján nagyobb pontosságot eredményez ezekben a döntésekben.

Valós felhasználómérés használatakor a számlázás a Traffic Managerra eljuttatott mérések száma alapján történik. A díjszabással kapcsolatos további információkért látogasson el a [Traffic Manager díjszabási oldalára](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Gyakori kérdések

* [Milyen előnyökkel jár a valós felhasználómérés használata?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Használhatok valós felhasználómérést nem Azure-régiókkal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Melyik útválasztási módszer előnyös a valós felhasználómérés?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Külön kell engedélyeznie valós felhasználómérés az egyes profilokat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Az előfizetéshez tartozó valós felhasználómérés kikapcsolása Hogyan](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Használhatok a weblapokon kívül más ügyfélalkalmazások valós felhasználómérés is?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Hány mérést végeznek minden alkalommal, amikor a valós felhasználómérés engedélyezve weboldala jelenik meg?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Van-e késés a weblapon valós felhasználómérés szkript futtatása előtt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Használhatom valós felhasználómérés csak a mérni kívánt Azure-régiókkal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Korlátozható a megadott számú mérések száma?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Megtekinthetem az ügyfélalkalmazás által a valós felhasználómérés részeként végrehajtott méréseket?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Módosíthatom Traffic Manager által biztosított mérési parancsfájlt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Mások is láthatják, hogy milyen kulcsot használok a valós felhasználómérés?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Mások is kihasználhatják a RUM-kulcsot?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Be kell-e állítani a mérési JavaScriptet az összes weblapon?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [A végfelhasználók számára azonosíthatók a Traffic Manager, ha valós felhasználómérés használok?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [A weblap mérési valós felhasználómérés az útválasztáshoz Traffic Manager kell használnia?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Szükség van-e az Azure-régiókban üzemeltetett szolgáltatások üzemeltetésére az valós felhasználómérés-vel való használatra?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Növekedni fog az Azure sávszélesség-használatom a valós felhasználómérés használatakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan használhatja [a valós felhasználómérést weblapok](traffic-manager-create-rum-web-pages.md) használatával
- Tudnivalók a [Traffic Manager működéséről](traffic-manager-overview.md)
- További információ a [Mobile Centerről](https://docs.microsoft.com/mobile-center/)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](traffic-manager-create-profile.md)

