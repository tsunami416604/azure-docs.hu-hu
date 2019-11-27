---
title: Határozza meg a közzététel lehetőséget az Azure Marketplace-en |} Az Azure
description: Ez a cikk ismerteti a megfelelőségi feltételeket, és szeretné ismerni az alkalmazások közzététele az Azure piactér közzétételi követelmények partnerek.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: ed25b92dc759f687e35a8424558453a830369a57
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548360"
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

A közzétételi lehetőség kiválasztása előtt fontos megérteni a Marketplace-megoldások,-alkalmazások és-szolgáltatások kirakatra vonatkozó támogathatósági követelményeit:

Az **Azure Marketplace** -alkalmazások olyan technikai jellegű "építőelemek", amelyek beépítettek vagy beépítettek az Azure-hoz, és az informatikai vagy fejlesztői közönség számára készültek. Az Azure Marketplace tanácsadási szolgáltatásokat ajánlatok, amelyek segítségével az ügyfelek – első lépések professzionális szolgáltatások vagy gyorsítsa fel az Azure használatát.

A **AppSource** -alkalmazások olyan üzletági megoldások, amelyek az Azure-ban vagy a következő beépített platformokon érhetők el: Dynamics 365, Office 365, Power bi vagy Power apps. Appsource-ban tanácsadási szolgáltatások professzionális szolgáltatások ajánlatok, amelyek segítségével az ügyfelek használatának első lépései, vagy gyorsítsa fel a Dynamics 365 és a Power bi-ban.


## <a name="understand-storefront-selection"></a>Kijelölés kirakat ismertetése

Minden kirakat egyedi ügyfél-követelményeket szolgál ki. Az ajánlat közzétételének helye az ajánlat típusa, a tranzakciós lehetőségek, a kategória és/vagy az iparági kiválasztás alapján határozható meg, hogy az adott célközönséget célozza meg. 

>[!Note]
>"Kereszthivatkozások" (csak SaaS-alkalmazásokhoz): Ha egy lista vagy próbaverziós ajánlat megfelel a technikai és az üzleti felhasználói közönség feltételeinek, az ajánlat mindkét kirakatban szerepel. További információ az alábbi közzétételi beállítások.

## <a name="choose-a-publishing-option"></a>A közzétételi lehetőség kiválasztása

A rendelkezésre álló közzétételi lehetőségek különböző ügyfél-összevonást biztosítanak, miközben hozzáférést biztosítanak a megosztási és [kereskedelmi Piactéri előnyökhöz](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits).  Vegye figyelembe a hívások-az-műveletet, amely a közzétételi lehetőség felel meg:

| **Közzétételi lehetőség**    | **Leírás**  |
| :------------------- | :-------------------|
| **Listáját** | Az alkalmazás vagy szolgáltatás egyszerű listája, amely lehetővé teszi, hogy a piactér felhasználói **kapcsolatba lépjenek** az ügyféllel a Kapcsolatfelvétel a hívással művelettel. |
| **Próbaverzió** | A piactér használatával javíthatja a felhasználhatóságot, és automatizálhatja a megoldás próbaverzióját, így a leendő felhasználók díjmentesen használhatják SaaS-, IaaS-vagy Microsoft-alkalmazási élményét a vásárlást megelőzően korlátozott ideig. A próbaverziós közzétételi lehetőséghez használt hívások közötti művelet a következő lehet: **ingyenes próbaverzió** vagy **tesztvezetés**. |
|**BYOL**  |A piactér használatával javíthatja a megoldást, és automatizálhatja a megoldás üzembe helyezését, és elvégezheti a pénzügyi tranzakciók külön történő elvégzését. A BYOL-ajánlatok típusai ideálisak a helyszíni és a felhőalapú Migrálás számára. A művelet meghívása **most már elérhető**.
| **Transact** | Az alkalmazás az Azure-ban fut, és erőforrásként közvetlenül az ügyfél Azure-előfizetésében is kiépíthető, ha az ügyfél a **Letöltés** meghívása műveletet választja. A szoftverlicenc-díjat opcionálisan megvásárolhatja és számlázhatja az ügyfél által választott fizetési eszköz és feltételek alapján, valamint dönthet úgy is, hogy az **ingyenes próbaverzióra** korlátozott időre szóló hozzáférést biztosít (csak az Azure Marketplace-en érhető el). |

>[!Note]
>A Transact-közzétételi lehetőség használatakor a fontos megérteni a díjszabás, Számlázás, számlázás és küldenie jóváhagyásra cége kifizetési szempontok ajánlat típusának kiválasztása és az ajánlat létrehozása előtt. További információért tekintse át a [Marketplace számlázási és kereskedelmi megfontolásokat ismertető cikket](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Következő lépések

*   A közzétételi lehetőség [kiválasztása után kiválaszthatja az](./publisher-guide-by-offer-type.md) ajánlat bemutatására használni kívánt ajánlat típusát.
*   Tekintse át a jogosult-e a kijelölés és a konfiguráció az Ön ajánlatát véglegesítéséhez ajánlat típushoz című által a közzétételi beállítások.
*   Tekintse át a közzétételi minták storefront-példák hogyan megoldását képez le egy ajánlat típusát és a konfiguráció alapján.



