---
title: Használati és a Felhőbeli szolgáltató Azure verem számlázási kezelése |} Microsoft Docs
description: Egy a lépésein végighaladva felhőszolgáltatóként Azure verem regisztrálása és az ügyfelek hozzáadása.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 21a52af4943004789b0a9bdbe4695ab1a603c046
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796699"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Használati és a Felhőbeli szolgáltató Azure verem számlázási kezelése 

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk végigvezeti Azure verem regisztrálása, a Felhőbeli szolgáltató (CSP) és az ügyfelek hozzáadása.

Egy CSP-hez, valószínűleg sok különböző vevő használata az Azure-verem. Mindegyik ügyfél egy CSP-előfizetéssel rendelkezik, az Azure-ban, és minden egyes felhasználó-előfizetéshez az Azure-veremből használati közvetlen kell.

Az alábbi ábrán látható a lépéseket, válassza ki a megosztott szolgáltatások fiók és a fiók azure-fiókot regisztrálni kell. Ez azért történik, amikor is bevezetésében end ügyfeleinek.

**Lépések végrehajtásával adja hozzá a használat nyomon követése, mint egy CSP-hez**

![A használati és a felügyeleti szolgáltatás felhőszolgáltatóként engedélyező folyamat.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>CSP vagy CSPSS előfizetés létrehozása

### <a name="cloud-service-provider-subscription-types"></a>Szolgáltató előfizetés felhőtípusok

Akkor válassza ki a megosztott szolgáltatások fiókkal, mellyel be Azure verem. Az előfizetések regisztrációja egy több-bérlős Azure verem használható a típusok a következők:

 - Felhőszolgáltató 
 - Partner megosztott szolgáltatások előfizetés 

#### <a name="csp-shared-services"></a>Kriptográfiai Szolgáltató megosztott szolgáltatások

Cloud Service Provider megosztott szolgáltatások (CSPSS) előfizetések az előnyben részesített választás az regisztrációs, amikor egy közvetlen CSP-hez vagy CSP terjesztő Azure verem működik.

A megosztott szolgáltatások bérlő CSPSS előfizetések tartoznak. Amikor regisztrál az Azure-verem, meg kell adnia a hitelesítő adatokat egy olyan fiók, amely az előfizetés tulajdonosa. Az Azure verem regisztrálásához használt fióknak különbözhet a központi telepítési; a használt rendszergazdai fiók a két tegye *nem* kell tartozniuk ugyanabban a tartományban. Ez azt jelenti előfordulhat, hogy központi telepítése, amelyek már használják a bérlőnek a használatával. Például akkor lehet, hogy használjon ContosoCSP.onmicrosoft.com, majd regisztrálja egy másik bérlőnek, például IURContosoCSP.onmicrosoft.com a használatával. Ne feledje, hogy jelentkezzen be ContosoCSP.onmicrosoft.com nap-ne Azure verem felügyeleti érhesse el kell. Amikor regisztrál Azure IURContosoCSP.onmicrosoft.com használatát, ha végre kell hajtani a regisztrációs műveletek.

Tekintse meg a következő előfizetés létrehozásával CSPSS előfizetések, valamint útmutatást a leírását [Azure Partner megosztott szolgáltatások hozzáadása](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Kriptográfiai Szolgáltató előfizetések

Felhőalapú szolgáltató (CSP) előfizetések a regisztráció során CSP viszonteladók előnyben részesített választás vagy az ügyfél egy záró Azure verem működik.

## <a name="register-azure-stack"></a>Az Azure verem regisztrálása

Azure verem regisztrálni, lásd: [Azure verem regisztrálása az Azure-előfizetéssel való](azure-stack-registration.md).

## <a name="add-end-customer"></a>Záró vevő hozzáadása

Azure-vermet, hogy ha egy új bérlőt erőforrást használ, a használati jelentve lesz a Felhőszolgáltató (CSP) előfizetésének konfigurálásához lásd: [adja hozzá a bérlő használati és számlázási Azure verem](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>A jobb oldali előfizetések díjat számítanak

Az Azure verem regisztrációs nevű szolgáltatást használja. A regisztráció az Azure, amely dokumentumokat mely Azure előfizetés(ek) segítségével egy adott Azure-verem díjat számítanak tárolt egy objektum. Ez a szakasz a regisztrációs fontosságát megoldást.

Regisztrációs Azure verem használatával:
 - Azure verem használati adatok továbbítására Azure kereskedelmi és kiszámlázni Azure-előfizetéssel.
 - Egy másik előfizetésben található Azure verem több-bérlős telepítésekben az egyes ügyfelek használati jelentést. Több vállalat kiszolgálása lehetővé teszi, hogy a különböző szervezetek támogatásához Azure verem azonos példányon lévő Azure verem.

Minden Azure verem van egy alapértelmezett előfizetés és sok bérlői előfizetések, igény szerint. Az alapértelmezett előfizetést Azure-előfizetéssel, amely fel van töltve, ha nincs bérlői vonatkozó előfizetése. Az első regisztráció kell lennie. A több-bérlős használatot jelentéskészítés működjön az előfizetés CSP vagy CSPSS előfizetés kell lennie.

Ezt követően a regisztrációs frissül Azure-előfizetés az egyes bérlők, amelyet szeretne Azure verem használható. Bérlői előfizetések a CSP típusúnak kell lennie, és az alapértelmezett előfizetés birtokló partnernek kell összesítő. Ez azt jelenti valaki más ügyfelek nem regisztrálható.

Amikor Azure verem globális Azure-bA továbbítja a használati adatokat, az Azure-szolgáltatás a regisztrációs tekint, és mindegyik bérlő használati leképezi a megfelelő bérlői előfizetéshez. Ha a bérlő nem regisztrált, adott kihasználtsága az alapértelmezett-előfizetéshez az Azure-verem példány, amelyből származik.

Mivel a bérlői előfizetések CSP előfizetések, a számlázási a rendszer elküldi a CSP-partner, és használati adatok nincs látható a végfelhasználók ügyfélnek.



## <a name="next-steps"></a>További lépések

 - A kriptográfiai Szolgáltató program kapcsolatos további információkért lásd: [Cloud Solution Provider programot](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból kapcsolatos további információkért lásd: [használati és számlázási Azure verem](azure-stack-billing-and-chargeback.md).
