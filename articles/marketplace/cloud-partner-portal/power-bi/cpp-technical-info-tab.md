---
title: Technikai információ a Power BI alkalmazást offer – Azure Marketplace-en |} A Microsoft Docs
description: A Power BI alkalmazás ajánlat technikai információ mezők konfigurálása a Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 71f226d2ba471a2ccbba3123015697da43d2cf80
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667065"
---
# <a name="power-bi-apps-technical-info-tab"></a>A Power BI alkalmazások technikai információ lap

A **technikai információ** lapján a **új ajánlat** lap, ahol meg kell a Power BI-telepítő csomag URL-CÍMÉT és az új ajánlat érvényesítéshez szükséges további adatok.  A kezdeti kiadás minden Power BI-alkalmazások ingyenes, letölthető az appsource-ból külön díj nélkül. Ennek eredményeképpen nem bármely leltári egységek (SKU) ajánlat típus definiálását.

![Technikai információ lap](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Technikai információ mezők 

Az a **technikai információ** lapon meg kell adnia a következő mezőket.  A hozzáfűzött csillag (*) a mező címke jelzi, hogy szükséges.

|        Mező          |  Leírás                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Telepítő URL-címe**     | Amikor éles környezetbe való előléptetése és az alkalmazás közzététele a Power BI által létrehozott címe.  Az URL-cím létrehozása a további információkért lásd: [service-alkalmazások közzététele a Power bi-ban](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Ellenőrzési utasítások**  |  A Microsoft ellenőrzési csoportja, amelyek segítik a konfigurálása, csatlakozás és az alkalmazás tesztelése nem kötelező szöveg útmutatást (max 3000 karakter) többek között: tipikus konfigurációs beállítások, tesztelni vagy a "Connect adatok" beállítás tesztelésére szolgáló paraméterek stb. Ez az információ csak akkor látható, az érvényesítési csapatának, és csak ellenőrzési célokat szolgál.  |
| **Ez az alkalmazás a program létrehoz egy Power BI-tartalomcsomag?** | Jelenleg ez az egy belső használt mezőt. Hagyja a beállítást az alapértelmezett értékére, `No`; ellenkező esetben a mezőben a módosítása `No` akadályozhatnák a közzététel.  |  
|  |  |


## <a name="next-steps"></a>További lépések

A következő [kirakat részletek](./cpp-storefront-details-tab.md) lapon, marketinges és jogi információkkal szolgálnak az alkalmazáshoz.

