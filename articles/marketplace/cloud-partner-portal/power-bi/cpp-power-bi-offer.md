---
title: Power BI appajánlat | Azure Piactér
description: Power BI-alkalmazás közzététele a Microsoft AppSource-piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: dc433fb2ee7888a20af16e01d76d4678cf12b01b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985575"
---
# <a name="power-bi-app-offer"></a>Power BI-alkalmazásajánlat

>[!Important]
>2020. április 13-tól megkezdjük a Power BI-alkalmazásajánlatok felügyeletének áthelyezését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Az áttelepített ajánlatok kezeléséhez kövesse a [Power BI-alkalmazások létrehozásának áttekintése](https://aka.ms/AzurePBIAppCreationOverview) című témakör utasításait.

|              |                                |
|--------------|--------------------------------|
| Ebből a cikkből megtudhatja, hogy miként tehet közzé Power BI-alkalmazást a Microsoft [AppSource-piactéren.](https://appsource.microsoft.com/)  A Power BI-alkalmazások testre szabható Power BI-tartalmakat, például adatkészleteket, jelentéseket és irányítópultokat tartalmaznak. Ezután telepítheti az alkalmazást más Power BI-bérlőkre az AppSource-on keresztül, elvégezheti a fejlesztő által engedélyezett módosításokat és testreszabásokat, és csatlakoztathatja a saját adataihoz. | ![Power BI ikon](./media/powerbi-icon.png) |


Ez a cikk három fő részből áll:

-   [Előfeltételek](./cpp-prerequisites.md). A Power BI-alkalmazásajánlat létrehozásához és közzétételéhez vonatkozó műszaki és üzleti követelmények.
-   [Power BI App-ajánlat létrehozása](./cpp-create-offer.md). Power BI-alkalmazásajánlat-bejegyzés létrehozása a [Cloud Partner Portal](https://cloudpartner.azure.com)használatával.
-   [Power BI-alkalmazásajánlat közzététele](./cpp-publish-offer.md). Új ajánlat küldése az AppSource-nak közzétételre, és hogyan frissíthető egy meglévő ajánlat.


## <a name="publishing-steps"></a>Közzétételi lépések

A Power BI-alkalmazásajánlat közzétételének magas szintű lépései:

![A Power BI App közzétételi lépésekkel](media/publishing-steps.png)

Íme a Power BI App ajánlatközzétételi folyamata:

1. Sablonalkalmazás létrehozása a Power BI-ban. Ez a művelet létrehoz egy csomag telepítési URL-címet, amely az ajánlat elsődleges technikai eszközét jelöli. Szintén ebben az időben, támogassák a vizsgálati csomag a gyártás előtti. További információt a [Miek a Power BI sablonalkalmazásai?](https://docs.microsoft.com/power-bi/service-template-apps-overview) 
2. Gyűjtse össze vagy hozza létre az ajánlat marketinganyagait, beleértve a következőket: hivatalos név, leírás, logók stb. 
3. Gyűjtse össze vagy hozza létre az ajánlat jogi és támogatási dokumentumait: *felhasználási feltételek,* *adatvédelmi szabályzat,* *támogatási politika,* felhasználói segítség stb.
4. Az ajánlat létrehozása: a Cloud Partner Portal segítségével konfigurálhatja az ajánlat részleteit, beleértve az ajánlat leírását, a marketinganyagokat, a jogi információkat, a támogatási információkat és az eszközspecifikációkat.  Az ajánlat teljes megadása után küldje el közzétételre.
5. Figyelje a közzétételi folyamatot a Cloud Partner Portalon.  Ebben a lépésben az AppSource bevezetési csapat teszteli, ellenőrzi és hitelesíti az alkalmazást. 
6. Az alkalmazás sikeres minősítése után tekintse át a tesztkörnyezetben, és engedje fel. 
7. A Power BI-alkalmazás szerepel az AppSource-ban (ez "élesben megy").
8. A Power BI-ban népszerűsítse az üzem előtti csomagot éles környezetben. További információt [a Sablonalkalmazás-kiadás kezelése](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)című témakörben talál.


## <a name="next-steps"></a>További lépések

Mielőtt létrehozna és közzétenné a Power BI App-ajánlatát az AppSource-ban, meg kell felelnie a Power BI-alkalmazás AppSource-ban való közzétételére [vonatkozó követelményeknek.](./cpp-prerequisites.md)
