---
title: Cloud Partner Portal API –hivatkozás | Azure Piactér
description: A használandó előfeltételek és a piactéri API-műveletek listája.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256416"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud Partner Portal API-referencia

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a dokumentumban felsorolt [CPP API-k módosításait,](#changes-to-cpp-apis-after-the-migration-to-partner-center) és győződjön meg arról, hogy a kód a Partnerközpontba való áttelepítés után is működik.

A Cloud Partner Portal REST API-k lehetővé teszik a számítási feladatok, ajánlatok és közzétevői profilok programozott lekérését és kezelését. Az API-k szerepköralapú hozzáférés-vezérlés (RBAC) használatával kényszerítik a megfelelő engedélyeket a feldolgozási idő alatt.

Ez a hivatkozás a Cloud Partner Portal REST API-k technikai részleteit tartalmazza. A dokumentumban szereplő hasznos teherminták csak tájékoztató jellegűek, és az új funkciók hozzáadásával változhatnak.

## <a name="prerequisites-and-considerations"></a>Előfeltételek és szempontok

Az API-k használata előtt tekintse át a következőket:

- [Az előfeltételek](./cloud-partner-portal-api-prerequisites.md) cikk megtudhatja, hogyan adhat hozzá egy egyszerű szolgáltatás a fiókjához, és egy Azure Active Directory (Azure AD) hozzáférési jogkivonatot a hitelesítéshez.
- Az API-k hívására rendelkezésre álló két [egyidejűség-ellenőrzési](./cloud-partner-portal-api-concurrency-control.md) stratégia.
- További [API-szempontok](./cloud-partner-portal-api-considerations.md), például verziószámozás és hibakezelés.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>A CPP API-k módosítása a Partnerközpontba való áttelepítés után

| **Api** | **Módosítások ismertetése** | **Hatás** |
| ------- | ---------------------- | ---------- |
| KÖZZÉTÉTEL, GoLive, Mégse | Az áttelepített ajánlatok esetében a válaszfejléc más formátumú lesz, de továbbra is ugyanúgy fog működni, és relatív elérési utat jelöli konkretizált a művelet állapotának beolvasásához. | Az ajánlathoz tartozó POST-kérelmek bármelyikének küldésekor a Hely fejléc az ajánlat áttelepítési állapotától függően két formátum valamelyikével fog rendelkezni:<ul><li>Nem áttelepített ajánlatok<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Áttelepített ajánlatok<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET művelet | Olyan ajánlattípusok esetén, amelyek korábban támogatták a válasz "értesítési-e-mail" mezőjét, ez a mező elavult, és az áttelepített ajánlatokhoz már nem kerül vissza. | Az áttelepített ajánlatok esetében a továbbiakban nem küldünk értesítéseket a kérelmekben megadott e-mailek listájára. Ehelyett az API-szolgáltatás igazodik az értesítési e-mail folyamathoz a Partner Központban az e-mailek küldéséhez. Pontosabban, értesítéseket küldünk az Eladó elérhetőségi adatait szakaszban beállított e-mail címre a Partner Center fiókbeállításaiban, hogy értesítsük Önt a művelet előrehaladásáról.<br><br>Kérjük, tekintse át a Partnerközpontban található [Fiókbeállítások](https://partner.microsoft.com/dashboard/account/management) Eladó elérhetőségi adatai szakaszában beállított e-mail címet, hogy biztosan a megfelelő e-mail ben legyen megadva az értesítésekhez.  |

## <a name="common-tasks"></a>Gyakori feladatok

Ez a hivatkozás a következő gyakori feladatok végrehajtásához szükséges API-kat részletezi.

### <a name="offers"></a>Ajánlatok

- [Az összes ajánlat lekérése](./cloud-partner-portal-api-retrieve-offers.md)
- [Adott ajánlat lekérése](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Ajánlat állapotának lekérése](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Ajánlat létrehozása](./cloud-partner-portal-api-creating-offer.md)
- [Ajánlat közzététele](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Műveletek

- [Műveletek lekérése](./cloud-partner-portal-api-retrieve-operations.md)
- [Műveletek megszakítása](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Alkalmazás közzététele

- [Élesítés](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Egyéb feladatok

- [A virtuális gépi ajánlatok díjszabásának beállítása](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Hibaelhárítás

- [Hitelesítési hibák hibaelhárítása](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
