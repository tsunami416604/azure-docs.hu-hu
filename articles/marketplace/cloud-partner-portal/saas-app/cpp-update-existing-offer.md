---
title: Azure SaaS-alkalmazás létező ajánlat frissítése |} Az Azure Marketplace-en
description: Hogyan frissíthető egy meglévő SaaS alkalmazás ajánlatot az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 8817c8aa7d5d589be5e03cdd04e3a505d7e2be21
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941667"
---
# <a name="update-an-existing-saas-application-offer"></a>Egy SaaS-alkalmazás létező ajánlat frissítése

Számos különböző típusú frissítések, amelyeket érdemes tennie az ajánlatra, már közzé van téve, és élő után. Az ajánlat új verziójának a módosítás kell menteni és újra közzé, hogy tükrözze a Marketplace-en. Ez a cikk végigvezeti a frissítése az SaaS-ajánlat a különböző aspektusait a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

Miért érdemes az ajánlatot, mint például frissítése több oka is van:

- Új verzió hozzáadása meglévő alkalmazáshoz.
- Frissít egy alkalmazást.
- Új funkciók hozzáadása egy alkalmazáshoz.
- A piactér metaadatokat az ajánlat frissítése.

Segítség ezeket a módosításokat, a portál biztosít a **összehasonlítása** és **előzmények** funkciókat.

## <a name="unpermitted-changes-to-a-saas-offer"></a>Egy SaaS-ajánlat engedett módosításai

Nincsenek olyan SaaS-ajánlat, amely nem módosítható, miután az Azure piactéren az ajánlat élő attribútumai. A következő beállítások nem módosíthatók:

- Ajánlat és az ajánlat közzétevő-azonosító
- Verzió címkék, például: `1.0.1`
- Számlázási és licencelési modell meglévő ajánlatok változik.

## <a name="common-update-operations"></a>Gyakori frissítési műveletek
 
A következő frissítési műveletek gyakoriak.

### <a name="update-offer-contacts"></a>Az ajánlat ügyfelek frissítése

A következő lépések segítségével frissítse a támogatási kapcsolatba lép az ajánlatban.

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg az ajánlat, amelyet szeretne frissíteni.
3. Nyissa meg a **névjegyek** fülre. Frissítse a névjegyeket.
4. Válassza ki **közzététel** a módosítások közzétételéhez a munkafolyamat elindításához.


### <a name="update-offer-marketplace-metadata"></a>Az ajánlat marketplace metaadatok frissítése

A következő lépések segítségével frissíteni az ajánlatot társított marketplace metaadatait. (Például: cég neve, emblémák, stb.)

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg az ajánlat, amelyet szeretne frissíteni.
3. Nyissa meg a **kirakat részletek** fülre. Útmutatásait a [ajánlat közzététele SaaS](./cpp-publish-offer.md) cikk metaadatok módosításokat.
4. Válassza ki **közzététel** a módosítások közzétételéhez a munkafolyamat elindításához.

## <a name="compare-feature"></a>A szolgáltatás összehasonlítása

Ha módosít egy közzétett ajánlatunk, a Compare-funkció segítségével végrehajtott módosítások naplózása. A következő képernyőfelvételen látható a közzétett ajánlatok összehasonlítása lehetőséget.

![Hasonlítsa össze segítségével megtekintheti ajánlat Cloud Partner Portalon változásai](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>Az összehasonlítás funkció használatához:

1. A szerkesztési folyamat bármely mozzanata válassza ki az ajánlatban összehasonlítása.
2. Tekintse meg a marketing eszközök és metaadatok verzióinak egymás mellett.

## <a name="publishing-history"></a>Közzétételi előzmények

Közzétételi végzett tevékenység előzményeit, jelölje ki a **előzmények** lap meg a bal oldali navigációs menü sáv a Cloud Partner portálra. Láthatja, hogy az Azure Marketplace-ajánlat teljes élettartama során végrehajtott időbélyegzővel műveletek.

![Tekintse meg Cloud Partner Portalon előzmények ajánlat](./media/saas-offer-history.png)

A naplózási Előzmények lapon használhatja egy adott ajánlat és a alkalmazni a szűrőket, például a közzétevő, ajánlat és esemény típusa (például OfferGoLiveRequested.) A naplózási előzmények csv-fájlként is letöltheti.


## <a name="next-steps"></a>További lépések

[SaaS-alkalmazásajánlat](./cpp-saas-offer.md)