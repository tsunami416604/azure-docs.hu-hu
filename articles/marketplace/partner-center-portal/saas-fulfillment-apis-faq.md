---
title: SaaS-teljesítési API-k – GYIK | Azure piactér
description: A SaaS-ajánlat ügyfelei által az Azure piactéren felfedezett és megvásárolt élmény.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275696"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS Fulfillment API-k – GYIK

Az Azure Marketplace integrációs követelményei az Azure-ügyfelek számára az SaaS-ajánlatra való előfizetéshez is fel vannak sorolva.

## <a name="discovery-experience"></a>Felderítési élmény

Az ajánlat közzétételekor az Azure-felhasználók felfedezhetik az Azure piactéren elérhető SaaS-ajánlatokat. Az ügyfelek a terméktípus (SaaS) alapján szűrhetik az ajánlatokat, és felfedezhetik azokat az SaaS-szolgáltatásokat, amelyekre érdeklik.

## <a name="purchase-experience"></a>Vásárlási élmény

Ha egy felhasználót érdekli egy adott SaaS-szolgáltatás, a felhasználó előfizethet az Azure Marketplace-ről.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Mit jelent az, ha egy Azure-felhasználó előfizet egy SaaS-ajánlatra az Azure Marketplace-en?

Ez azt jelenti, hogy a felhasználók megtekinthetik a SaaS szolgáltatáshoz társított használati feltételeket és adatvédelmi nyilatkozatot, és elfogadják, hogy az Ön által beállított számlázási feltételekkel megfizetik, az SaaS-ajánlat közzétevője, a Microsoft számláján. A felhasználók az Azure-ban meglévő fizetési profiljuk használatával fizethetnek az SaaS-szolgáltatás felhasználására.

Ez számos okból hasznos lehet. Az ügyfelek mostantól egy helyen felfedezhetik és előfizethetik Microsoft Cloud platformot megbízható forrásként, anélkül, hogy minden egyes ISV-szoftvert használni kellene. Emellett az ügyfelek a meglévő fizetési profilt is használhatják anélkül, hogy az egyes ISV-szoftvereket külön kell fizetniük.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>A felhasználó automatikusan felszámítja az ajánlat előfizetését?

A SaaS-ajánlatra való feliratkozáskor a felhasználó beleegyezett, hogy a Microsoft platformon keresztül fizet az SaaS-szolgáltatás fogyasztásának. A díjak azonban csak az ajánlat felhasználása után indulnak el. A felhasználónak meg kell adnia SaaS-ajánlatát, és meg kell erősítenie a fiók létrehozását az ajánlat megkezdése érdekében. Ezután értesíti a Microsoftot az ügyfél SaaS-előfizetés számlázásának megkezdéséről.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hogyan történik az értesítés, amikor egy felhasználó előfizet az SaaS-ajánlatra?

Az ajánlatra való feliratkozás után az Azure-felhasználó felderítheti és kezelheti az összes ajánlatát az Azure-ban. Alapértelmezés szerint az újonnan előfizetett SaaS-ajánlat állapota **"kiépítés, teljesítés folyamatban"**. Ebben az állapotban az Azure-felhasználót a rendszer a **"fiók konfigurálása"** művelettel fogja kérni, hogy megkeresse a Azure Portal a SaaS-előfizetés kezelési élményét.

Amikor a felhasználó a **"fiók konfigurálása"** gombra kattint, a rendszer átirányítja az SaaS szolgáltatás webhelyére. Az URL-címet, amelyen a közzétevő az ajánlat közzétételének időpontjában megjelenik, a közzétevő biztosítja. Ezt a lapot a közzétevő kezdőlapjának nevezzük. Az Azure-felhasználóknak be kell jelentkezniük a SaaS-kezdőlapra az Azure-ban meglévő HRE hitelesítő adataik alapján.

Ha az Azure-felhasználót átirányítják a kezdőlapra, a rendszer tokent ad hozzá a lekérdezési URL-címhez. Ez a jogkivonat rövid élettartamú, és 24 órás időtartamra érvényes. Ezt követően észlelhető a jogkivonat jelenléte, és meghívhatja a Microsoft API-ját, hogy a tokenhez több környezet legyen társítva.

![Ügyfél-előfizetési folyamat](media/saas-metering-service-integration-flow-a.png)

Tekintse meg a [SaaS-teljesítési API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) -dokumentumot, ahol további információkat talál az API-szerződéssel kapcsolatban a Transact-forgatókönyvek kezeléséhez az SaaS-ajánlat életciklusában.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Honnan tudhatja meg, hogy a felhasználó milyen SaaS-előfizetést kínál az Azure-ban?

Az `Resolve` API-ra adott válasz az SaaS-előfizetéshez kapcsolódó ajánlatot és megtervezési információkat tartalmazza.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hogyan változtatható meg az Azure-felhasználó az ehhez az Azure-előfizetéshez társított terv?

* Az Azure-felhasználó a SaaS-előfizetéshez kapcsolódó tervet közvetlenül a SaaS-élményben vagy a Microsoft platformon módosíthatja.

* A konverziókat a számlázási ciklusban bármikor megteheti. Minden konverziót el kell fogadnia, amely a rendszer tudomásul vétele után válik hatályossá.

* Az előre fizetett csomag (**havi** vagy **éves**) díjszabása Pro-rated. A konverzió időpontjában kibocsátott bármely túllépés a következő számlán lesz felszámítva. Az új túllépések az új csomag alapján lesznek kibocsátva.

>[!Note]
>Ha nem szeretné bizonyos konverziós útvonalakat támogatni, tiltsa le a visszalépéseket.

Az alábbi kódrészlet rögzíti a folyamatot, amikor egy Azure-ügyfél megváltoztatja az SaaS-élmény tervét:

![Ügyfél-díjcsomag változási folyamata](media/saas-metering-service-integration-flow-b.png)

Az alábbi kódrészlet rögzíti a folyamatot, amikor egy Azure-ügyfél megváltoztatja a Microsoft kirakatában található csomagot

![Ügyfél-kirakati terv változási folyamata](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hogyan lehet az Azure-felhasználó lemondani az Azure-előfizetéshez társított csomagról?

Az Azure-felhasználók közvetlenül a SaaS-élményben vagy a Microsoft platformon keresztül leiratkozhat a megvásárolt SaaS-ajánlatból. A felhasználó lemondása után a következő számlázási időszak után már nem számítunk fel díjat.

Az alábbi kódrészlet rögzíti a folyamatot, amikor egy Azure-ügyfél leiratkozik az SaaS-ajánlatra a SaaS-élményben:

![Az ügyfél lemond a SaaS-élményben](media/saas-metering-service-integration-flow-d.png)

Az alábbi kódrészlet rögzíti a folyamatot, amikor az Azure-felhasználó leiratkozik a Microsoft kirakatában:

![Ügyfél lemondása a Microsoft kirakatában](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>További lépések

- További információért lásd: [Marketplace-mérési szolgáltatás API](./marketplace-metering-service-apis.md) -k.
