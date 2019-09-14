---
title: Határozza meg a közzététel lehetőséget az Azure Marketplace-en |} Az Azure
description: Ez a cikk ismerteti a megfelelőségi feltételeket, és szeretné ismerni az alkalmazások közzététele az Azure piactér közzétételi követelmények partnerek.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 49723a80bb198e8151c7e85a90453183068c9d2b
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962820"
---
# <a name="determine-your-publishing-option"></a>A közzétételi lehetőség meghatározása
Az Ön által választott az ajánlat közzétételi lehetőség közvetlenül kapcsolódik a jogosultsági követelményeknek és a piactér GTM előnyeit. Még ennél is fontosabb, hogy a közzétételi lehetőség és az ajánlat típusa határozza meg, hogy a felhasználók hogyan fognak működni a Piactéri ajánlatával.

Az ajánlat konfigurálásához meg kell ismernie a következő fő Piactéri fogalmakat: a közzétételi lehetőségek, az ajánlatok típusai és a konfiguráció, valamint a hívások közötti művelet, amely szabályozza, hogy az ajánlat hogyan és hol jelenik meg a piactéren.

![](./media/marketplace-publishers-guide/storefronts_options_table.png)


Ez a cikk azt ismerteti...
<ul><li>    A megoldás megfelelő megjelennie meghatározása </ul></li>
<ul><li>    Milyen közzétételi beállítások és a hívások-művelet minden kirakat érhető el </ul></li>
<ul><li>    Amely ajánlat elérhető minden közzétételi lehetőség </ul></li>


## <a name="selecting-a-storefront-publishing-option-and-offer-type-for-your-solution"></a>Egy kirakat kiválasztásával, a közzététel lehetőséget, és a megoldás az ajánlattípus

A közzététel lehetőséget választja, mielőtt fontos megérteni a jogosultsági kirakat követelményeit a marketplace-megoldásokat, alkalmazások és szolgáltatások:

Az **Azure Marketplace** -alkalmazások olyan technikai jellegű "építőelemek", amelyek beépítettek vagy beépítettek az Azure-hoz, és az informatikai vagy fejlesztői közönség számára készültek. Az Azure Marketplace tanácsadási szolgáltatásokat ajánlatok, amelyek segítségével az ügyfelek – első lépések professzionális szolgáltatások vagy gyorsítsa fel az Azure használatát.

A **AppSource** -alkalmazások olyan üzletági megoldások, amelyek az Azure-ban vagy a következő beépített eszközökön érhetők el: Dynamics 365, Office 365, Power BI vagy Power apps. Appsource-ban tanácsadási szolgáltatások professzionális szolgáltatások ajánlatok, amelyek segítségével az ügyfelek használatának első lépései, vagy gyorsítsa fel a Dynamics 365 és a Power bi-ban.


## <a name="understand-storefront-selection"></a>Kijelölés kirakat ismertetése

Az ajánlat megadására, az Azure Marketplace-re és/vagy AppSource vonatkozó kirakatot a rendszer automatikusan meghatározza az ajánlat részletei és célközönsége, valamint az ajánlat létrehozásakor kiválasztott kategóriák és iparágak alapján. 

>[!Note]
>"Cross-lista" (az csak SaaS-alkalmazások): Ha egy próba-alapú ajánlat vagy lista megfelel a feltételeknek mind a technikai és üzleti felhasználó közönségnek, két kirakattípus jelenik meg az ajánlat. További információ az alábbi közzétételi beállítások.

## <a name="choose-a-publishing-option"></a>A közzétételi lehetőség kiválasztása

A rendelkezésre álló közzétételi lehetőségek különböző ügyfél-összevonást biztosítanak, miközben hozzáférést biztosítanak a megosztási és [kereskedelmi Piactéri előnyökhöz](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits).  Vegye figyelembe a hívások-az-műveletet, amely a közzétételi lehetőség felel meg:

| **Közzétételi lehetőség**    | **Leírás**  |
| :------------------- | :-------------------|
| **List** | Egyszerű listája az alkalmazás vagy szolgáltatás, amely lehetővé teszi a marketplace-felhasználót, hogy el az eszközt az ügyfél-n keresztül kapcsolódni a **kapcsolatfelvételi** hívás-művelet. |
| **Próbaverzió** | A piactér használatával javíthatja a felhasználhatóságot, és automatizálhatja a megoldás próbaverzióját, így a leendő felhasználók díjmentesen használhatják SaaS-, IaaS-vagy Microsoft-alkalmazási élményét a vásárlást megelőzően korlátozott ideig. A próba-közzétételi lehetőséghez használt hívások közötti művelet a következő lehet: **Ingyenes próbaverzió** vagy **tesztelési meghajtó**. |
|**BYOL**  |A piactér használatával javíthatja a megoldást, és automatizálhatja a megoldás üzembe helyezését, és elvégezheti a pénzügyi tranzakciók külön történő elvégzését. A BYOL-ajánlatok típusai ideálisak a helyszíni és a felhőalapú Migrálás számára. A művelet meghívása **most már elérhető**.
| **Transact** | Az alkalmazás az Azure-ban fut, és erőforrásként közvetlenül az ügyfél Azure-előfizetésében is kiépíthető, ha az ügyfél a **Letöltés** meghívása műveletet választja. A szoftverlicencdíjakat igény szerint is vásárolt és keresztül az ügyfél választott fizetési eszközt és kifejezések díjszabása, és Ön kiválaszthatja, időkorlátos hozzáférést nyújtani a **a szoftver ingyenes próbaverziója** (csak érhető el az Azure-hoz Marketplace-en.) |

>[!Note]
>A Transact-közzétételi lehetőség használatakor a fontos megérteni a díjszabás, Számlázás, számlázás és küldenie jóváhagyásra cége kifizetési szempontok ajánlat típusának kiválasztása és az ajánlat létrehozása előtt. Tekintse át a [Piactéri számlázási és kereskedelmi szempontok a cikk további](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>További lépések

*   Ha közzétételi lehetőség használata mellett dönt, hogy készen áll [válassza ki](./publisher-guide-by-offer-type.md) kulcsindexet felkínálandó ajánlat.
*   Tekintse át a jogosult-e a kijelölés és a konfiguráció az Ön ajánlatát véglegesítéséhez ajánlat típushoz című által a közzétételi beállítások.
*   Tekintse át a közzétételi minták storefront-példák hogyan megoldását képez le egy ajánlat típusát és a konfiguráció alapján.



