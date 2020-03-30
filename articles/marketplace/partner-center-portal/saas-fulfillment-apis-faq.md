---
title: SaaS teljesítési API-k – GYIK | Azure Piactér
description: SaaS-ajánlat ügyfeleinek felderítési és vásárlási élményeit az Azure Piactéren.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275696"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS Fulfillment API-k – GYIK

Az Azure Marketplace-en támasztott integrációs követelmények, amelyek lehetővé teszik az Azure-ügyfelek számára, hogy előiratkozzanak a SaaS-ajánlatra, fel vannak sorolva.

## <a name="discovery-experience"></a>Felfedezési élmény

Az ajánlat közzététele után az Azure-felhasználók felfedezhetik a SaaS-ajánlatot az Azure Marketplace-en. Az ügyfelek a terméktípus (SaaS) alapján szűrhetik az ajánlatokat, és felfedezhetik az őket érdeklő SaaS-szolgáltatásokat.

## <a name="purchase-experience"></a>Vásárlási élmény

Ha egy felhasználó érdeklődik egy adott SaaS-szolgáltatás iránt, a felhasználó előfizethet rá az Azure Marketplace-ről.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Mit jelent az, hogy egy Azure-felhasználó előfizet egy SaaS-ajánlatra az Azure Marketplace-en?

Ez azt jelenti, hogy a felhasználó megtekintheti a SaaS-szolgáltatáshoz társított használati feltételeket és adatvédelmi nyilatkozatot, és beleegyezhet abba, hogy az Ön, a SaaS-ajánlat kiadója által a Microsoft számláján megadott számlázási feltételeknek megfelelően fizet érte. A felhasználók az Azure-ban meglévő fizetési profiljuk használatával fizethetnek a SaaS-szolgáltatás használatáért.

Ez előnyös a sok okból. Az ügyfelek most már felfedezhetik és előfizethetnek egy helyen a Microsoft Cloud Platform megbízható forrásként való használatával, anélkül, hogy minden általa használni kívánt független szoftverszoftvert ellenőriznének kellene. Emellett az ügyfelek használhatják meglévő fizetési profiljukat anélkül, hogy minden független független független szoftverszoftvert kifejezetten egymástól függetlenül kellene fizetniük.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Automatikusan felszámítja a felhasználó az ajánlatot?

A SaaS-ajánlatra való előfizetés során a felhasználó beleegyezett, hogy a Microsoft platformon keresztül fizet a SaaS-szolgáltatás felhasználásáért. A díjak azonban csak akkor kezdődnek, ha az ajánlat felhasználásra kerül. A felhasználónak meg kell mennie a SaaS-ajánlathoz, és meg kell erősítenie a fiók létrehozását az ajánlat fogyasztásának megkezdéséhez. Ezután értesíti a Microsoftot, hogy megkezdi az ügyfél SaaS-előfizetés számlázását.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hogyan kap értesítést, ha egy felhasználó előfizet a SaaS-ajánlatra?

Miután feliratkozott egy ajánlatra, az Azure-felhasználó felderítheti és kezelheti az összes ajánlatát az Azure-ban. Alapértelmezés szerint az újonnan jegyzett SaaS-ajánlat állapota **"Kiépítés, teljesítés függőben"**. Ebben az állapotban az Azure-felhasználó a **"Fiók konfigurálása"** műveletet kéri annak érdekében, hogy az Azure Portalon keresse meg a SaaS-előfizetés-felügyeleti élményüket.

Amikor a felhasználó a **"Fiók konfigurálása"** gombra kattint, a rendszer átirányítja őket a SaaS szolgáltatás webhelyére. Az URL-címet, ahová a megtérést, a közzétevő adja meg az ajánlat közzétételekor. Ezt a lapot a közzétevő céloldalának nevezzük. Az Azure-felhasználóknak képesnek kell lenniük a SaaS céloldalra való bejelentkezésre az Azure-ban meglévő AAD-hitelesítő adataik alapján.

Amikor az Azure-felhasználó átirányítja a céloldalra, egy jogkivonat ot ad hozzá a lekérdezés URL-címéhez. Ez a jogkivonat rövid életű, és 24 órás időtartamra érvényes. Ezután észlelheti a jogkivonat jelenlétét, és hívja meg a Microsoft API-t, hogy több környezetet kapjon a jogkivonathoz társítva.

![Ügyfél-előfizetési folyamat](media/saas-metering-service-integration-flow-a.png)

Tekintse meg a [SaaS teljesítési API-dokumentum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) további információt az API-szerződés kezelésére tranzakciós forgatókönyvek az életciklus egy SaaS-ajánlat.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Honnan tudja, hogy a SaaS-ajánlat, amelyre a felhasználó előfizet az Azure-ban?

Az API-ra `Resolve` adott válasz tartalmazza az ajánlat és a SaaS-előfizetéshez társított információk megtervezése.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hogyan módosíthatja az Azure-felhasználó az Azure-előfizetéshez társított csomagot?

* Az Azure-felhasználó módosíthatja a SaaS-előfizetéshez társított csomagot közvetlenül a SaaS-élményben, vagy a Microsoft platformon keresztül.

* A konverziók a számlázási ciklusban bármikor elvégezhetők. El kell ismernie minden olyan konverziót, amely az elismerés után lép hatályba.

* Az előre fizetett **(havi** vagy **éves)** díjszabás oka a ráta. Az átváltás időpontjáig kibocsátott túllépésa a következő számlán kerül felszámításra. Az új terv alapján új túlterhelések kerülnek kibocsátásra.

>[!Note]
>Letilthatja a visszaminősítéseket, ha nem kíván bizonyos konverziós útvonalakat támogatni.

Az alábbi sorrend rögzíti a folyamatot, amikor egy Azure-ügyfél módosítja a tervet a SaaS-élményben:

![Vevői terv módosítási folyamata](media/saas-metering-service-integration-flow-b.png)

Az alábbi sorrend rögzíti a folyamatot, amikor egy Azure-ügyfél módosítja a tervet a Microsoft kirakatában

![Vevői kirakatterv változási folyamata](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hogyan iratkozhat le az Azure-felhasználó az Azure-előfizetéshez társított csomagról?

Egy Azure-felhasználó leiratkozhat egy megvásárolt SaaS-ajánlatról közvetlenül a SaaS-élményben, vagy a Microsoft platformon keresztül. Miután a felhasználó leiratkozott, a következő számlázási ciklustól már nem kell fizetnie.

Az alábbi sorrend rögzíti a folyamatot, amikor egy Azure-ügyfél leiratkozik a SaaS-ajánlatra a SaaS-élményben:

![Az ügyfél leiratkozik a SaaS-élményben](media/saas-metering-service-integration-flow-d.png)

Az alábbi sorozat rögzíti a folyamatot, amikor az Azure-felhasználó leiratkozik a Microsoft kirakatában:

![Az ügyfél leiratkozik a Microsoft kirakatában](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>További lépések

- További információkért [tekintse meg a Marketplace-mérési szolgáltatás API-jait.](./marketplace-metering-service-apis.md)
