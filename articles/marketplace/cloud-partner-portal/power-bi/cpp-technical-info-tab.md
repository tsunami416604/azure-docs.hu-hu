---
title: Technikai információ a Power BI alkalmazás-ajánlat |} Az Azure Marketplace-en
description: A Power BI alkalmazás ajánlat technikai információ mezők konfigurálása a Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 15f4e2a76724a70c15411dea767cc9bc433e4d4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943215"
---
# <a name="power-bi-apps-technical-info-tab"></a>A Power BI alkalmazások technikai információ lap

Az a **új ajánlat** oldalon a **technikai információ** fülre, és adja meg a Power BI-telepítő csomag URL-CÍMÉT és egyéb információkat, hogy szeretne-e az új ajánlat ellenőrzése.  A kezdeti kiadás minden Power BI alkalmazás ingyenesek, és az appsource-ból letölthető. Emiatt a készletkezelési egységek (SKU) ajánlat típus nem lehet definiálni.

![A technikai információ lap](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Technikai információ mezők 

Az a **technikai információ** fülre, töltse ki a mezőket a következő táblázat ismerteti. Egy csillag (*) mező címke végén található, az azt jelenti, hogy a mező kitöltése kötelező.

|        Mező          |  Leírás                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Telepítő URL-címe\***     | A Power BI az URL-címet állít elő, amikor éles környezetbe való előléptetése és tegye közzé az alkalmazást.  További információkért lásd: [alkalmazások közzététele az irányítópultokat és jelentéseket a Power bi-ban](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Ellenőrzési utasítások**  |  Ha azt szeretné, adja hozzá a Microsoft ellenőrzési csoportja konfigurálása, csatlakozás és az alkalmazás teszteléséhez útmutatást (3000 karakter). Például a tipikus konfigurációs beállítások, fiókok, paraméter vagy egyéb információkat, amelyek a csatlakozás beállítás teszteléséhez használható. Ez az információ csak az ellenőrzési csoportja látható-e, és azt csak ellenőrzési célokat szolgál.  |
| **Ez az alkalmazás a program létrehoz egy Power BI-tartalomcsomag?** | Jelenleg a mező csak belső használatra szolgál. Ne módosítsa az alapértelmezett, **nem**. Ha módosítja a beállításokat, hogy **Igen**, meg tudta leállítani a közzétételi folyamat.  |  
|  |  |


## <a name="next-steps"></a>További lépések

Az a [kirakat részletek](./cpp-storefront-details-tab.md) lapra, adja meg az alkalmazás a marketing és jogi információit.

