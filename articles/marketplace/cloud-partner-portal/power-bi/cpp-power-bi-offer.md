---
title: A Power BI alkalmazást offer – Azure Marketplace-en |} A Microsoft Docs
description: Tudnivalók a Power BI alkalmazás közzététele a Microsoft AppSource piactérről.
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
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009716"
---
# <a name="power-bi-app-offer"></a>Power BI-alkalmazásajánlat

|              |                                |
|--------------|--------------------------------|
| Ez a cikk ismerteti a Power BI-alkalmazás közzététele a Microsoft [az AppSource piactérről](https://appsource.microsoft.com/).  A Power BI alkalmazás testre szabható Power BI-tartalmakat, beleértve az adatkészleteket, jelentéseket és irányítópultokat csomagok. Ezután az alkalmazás üzembe helyezése az appsource-ból más Power BI-bérlők, hajtsa végre, a korrekciók és a fejlesztő által engedélyezett testreszabások és csatlakoztassa a saját adataihoz. | ![A Power BI ikon](./media/powerbi-icon.png) |


Ez a cikk három fő részre oszlik:

-   [Előfeltételek](./cpp-prerequisites.md). Ajánlat létrehozása és a egy Power BI-alkalmazás közzététele a műszaki és üzleti igényeinek.
-   [Hozzon létre egy Power BI alkalmazás ajánlatot](./cpp-create-offer.md). Használatával a Power BI alkalmazás ajánlat bejegyzés létrehozása a [Cloud Partner Portalon](https://cloudpartner.azure.com).
-   [A Power BI alkalmazás-ajánlat közzététele](./cpp-publish-offer.md). Hogyan lehet elküldeni az appsource-on egy új ajánlat közzététele, és a egy létező ajánlat frissítése.


## <a name="publishing-steps"></a>Közzétételi lépéseket

A Power BI alkalmazás-ajánlat közzétételéhez magas szintű lépései a következők:

![Power BI alkalmazás ajánlat közzétételi lépéseket](media/publishing-steps.png)

Itt látható a Power BI-alkalmazás az ajánlat közzétételi folyamat:

1. A sablon-alkalmazás létrehozása a Power bi-ban. Ez a művelet létrehoz egy csomag telepítési URL-CÍMÉT, az ajánlat az elsődleges műszaki objektumot képviselő. Jelenleg is támogatják a vizsgálati csomag üzem előtti. További információkért lásd: [Mik azok a Power bi-ban sablon alkalmazások?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Adatokat gyűjthet, illetve az ajánlat olyan marketinganyagokat, beleértve a létrehozása: hivatalos név, leírás, emblémák, stb. 
3. Gyűjtése és a támogatási dokumentumok és az ajánlat jogi létrehozása: *használati feltételeket tartalmazó fájl*, *adatvédelmi szabályzat*, *támogatási házirend*, felhasználóknak szóló segítséget és így tovább.
4. Az ajánlat létrehozásához: használja a Cloud Partner Portalon konfigurálhatja az ajánlat az ajánlat leírása, marketing-anyagok, a jogi információk, a támogatási információkat és az adategység-specifikációi többek között az adatokat.  Miután az ajánlat teljes mértékben meg van adva, küldje el a közzétételre.
5. A Cloud Partner portálra a közzétételi folyamat figyelése  Ezzel a lépéssel az appsource-ban a bevezetési csapat teszteli, ellenőrzi és tanúsítja az alkalmazás. 
6. Miután az alkalmazás sikeresen megkapta a minősítést, tekintse át azt a tesztkörnyezetben, majd engedje el. 
7. A Power BI alkalmazást az appsource-ban (hogy "élő goes") szerepel.
8. A Power bi-ban az éles üzem előtti csomagot támogatják. További információkért lásd: [kezelheti az alkalmazás Szolgáltatássablon kiadása](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>További lépések

Mielőtt a Power BI alkalmazás ajánlat létrehozása, és tegye közzé az appsource-ban, meg kell felelnie a [követelmények](./cpp-prerequisites.md) egy Power BI alkalmazás-közzététel az appsource-ban.
