---
title: Az Azure SaaS-alkalmazás ajánlat csomagok |} A Microsoft Docs
description: Hozzon létre egy tervet a SaaS-alkalmazás ajánlat az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 0394fa759f4b6f6af59e075d9bc67668886d8075
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196987"
---
# <a name="saas-application-plans-tab"></a>SaaS-alkalmazás tervek lap

A csomagok lap használatával létrehozhat egy új csomagot. Legalább 1 csomag hozzá kell adni, ha az értékesítési keresztül a Microsoft lehetőséget használ az SaaS-alkalmazáshoz.

![Egy új csomag létrehozása](./media/saas-plans-new-plan.png)

## <a name="create-a-new-plan"></a>Egy új csomag létrehozása

Egy új csomag létrehozása:

1. A **csomagok**válassza **+ új csomag**
2. Az a **új csomag** előugró ablakban adjon meg egy **szolgáltatáscsomag-Azonosítóval**. A hossza legfeljebb 50 karakter hosszú lehet. Ez az azonosító csak kisbetűket, alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak, kell állnia. Ez az azonosító nem módosítható, miután az ajánlat közzétételének.
3. Válassza ki **OK** mentéséhez, a csomag azonosítóját.

   ![Új csomag csomag azonosítója](./media/saas-plans-plan-ID.png)

### <a name="to-configure-the-plan"></a>A terv konfigurálása:

1. A **csomag részletei**, adjon meg információt a következő mezőket:

   - Cím – adjon meg egy címet a csomag számára. A címe az legfeljebb 50 karakter hosszúságú lehet.
   - Leírás – adjon meg egy leírást. A leírás korlátozva, legfeljebb 500 karakter hosszúságú lehet.
   - Az egy privát tervet? -Ha a csomag csak felhasználók bizonyos csoportjaira számára elérhető, válassza ki a **Igen**.
   - Ország/régió rendelkezésre állása – a csomag legalább 1 ország vagy régió elérhetőnek kell lennie. Kattintson a **régiók kiválasztása**. Válassza ki az országot/régiót a **Válasszon országot/régiót rendelkezésre állási** listában, és válassza ki **OK**. 
   - Régi díjszabás – adja meg a költség, a USD havonta.

2. A **pénznem díjszabás egyszerűsített**, adja meg a következő információkat:

   - Számlázási időszak - havi díja alapértelmezés szerint ki van választva. Éves díjszabás is megadhatja.
   - A havi díjak – adja meg a havi díjak, amelynek meg kell egyeznie a régi díjszabás.

   >[!NOTE] 
   >Éves ár ad hozzá a számlázási időszak, ha meg fogja kérni a a **éves ár** évenként USD-ben.

3. Válassza ki **mentése** a terv konfigurálásának befejezéséhez.

   >[!NOTE] 
   >Az árképzési módosítások mentése után Ön is exportálási/importálási díjszabási adatokat.

![Konfigurálja a csomag részletei](./media/saas-plans-plan-details.png)

## <a name="next-steps"></a>További lépések

[Csatorna adatai lap](./cpp-channel-info-tab.md)

