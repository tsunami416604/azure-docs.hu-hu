---
title: A közzétételi lehetőség meghatározása az Azure Marketplace-en | Azure
description: Ez a cikk ismerteti a támogathatósági feltételeket és a közzétételi követelményekkel foglalkozó partnereket, amelyekkel megismerheti, hogyan tehet közzé alkalmazásokat az Azure piactéren.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 967d33cb7a9eb3ec922f9d85cfc2581b85bce537
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825910"
---
# <a name="determine-your-publishing-option"></a>A közzétételi lehetőség meghatározása
Az ajánlathoz választott közzétételi lehetőség közvetlenül a támogathatósági követelmények és a Piactéri GTM előnyeire vonatkozik. Még ennél is fontosabb, hogy a közzétételi lehetőség és az ajánlat típusa határozza meg, hogy a felhasználók hogyan fognak működni a Piactéri ajánlatával.

Az ajánlat konfigurálásához meg kell ismernie a következő fő Piactéri fogalmakat: a közzétételi lehetőségek, az ajánlatok típusai és a konfiguráció, valamint a hívások közötti művelet, amely szabályozza, hogy az ajánlat hogyan és hol jelenik meg a piactéren.

![](./media/marketplace-publishers-guide/storefronts_options_table.png)


Ebből a cikkből megtudhatja, hogy...
<ul><li>    A megoldás megfelelő kirakatának meghatározása </ul></li>
<ul><li>    Az egyes kirakatokban elérhető közzétételi lehetőségek és hívások – művelet </ul></li>
<ul><li>    Az egyes közzétételi lehetőségekhez elérhető ajánlati típusok </ul></li>


## <a name="selecting-a-storefront-publishing-option-and-offer-type-for-your-solution"></a>Kirakat, közzétételi lehetőség és ajánlat típusának kiválasztása a megoldáshoz

A közzétételi lehetőség kiválasztása előtt fontos megérteni a Marketplace-megoldások,-alkalmazások és-szolgáltatások kirakatra vonatkozó támogathatósági követelményeit:

Az **Azure Marketplace** -alkalmazások olyan technikai jellegű "építőelemek", amelyek beépítettek vagy beépítettek az Azure-hoz, és az informatikai vagy fejlesztői közönség számára készültek. Az Azure Marketplace Consulting Services olyan professzionális szolgáltatásokat kínál, amelyek segítenek az ügyfeleknek az Azure használatának megkezdésében vagy felgyorsításában.

A **AppSource** -alkalmazások olyan üzletági megoldások, amelyek az Azure-ban vagy a következő beépített platformokon érhetők el: Dynamics 365, Office 365, Power bi vagy Power apps. A AppSource Consulting Services olyan Professional Services-ajánlatok, amelyek segítenek az ügyfeleknek a Dynamics 365 és Power BI használatának megkezdésében vagy felgyorsításában.


## <a name="understand-storefront-selection"></a>A kirakat kijelölésének ismertetése

Az ajánlat megadására, az Azure Marketplace-re és/vagy AppSource vonatkozó kirakatot a rendszer automatikusan meghatározza az ajánlat részletei és célközönsége, valamint az ajánlat létrehozásakor kiválasztott kategóriák és iparágak alapján. 

>[!Note]
>"Kereszthivatkozások" (csak SaaS-alkalmazásokhoz): Ha egy lista vagy próbaverziós ajánlat megfelel a technikai és az üzleti felhasználói közönség feltételeinek, az ajánlat mindkét kirakatban szerepel. További információ az alábbi közzétételi lehetőségekről.

## <a name="choose-a-publishing-option"></a>Közzétételi lehetőség kiválasztása

A rendelkezésre álló közzétételi lehetőségek különböző ügyfél-összevonást biztosítanak, miközben hozzáférést biztosítanak a megosztási és [kereskedelmi Piactéri előnyökhöz](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits).  Jegyezze fel a közzétételi lehetőségnek megfelelő meghívások közötti műveletet:

| **Közzétételi lehetőség**    | **Leírás**  |
| :------------------- | :-------------------|
| **Listáját** | Az alkalmazás vagy szolgáltatás egyszerű listája, amely lehetővé teszi, hogy a piactér felhasználói **kapcsolatba lépjenek** az ügyféllel a Kapcsolatfelvétel a hívással művelettel. |
| **Próbaverzió** | A piactér használatával javíthatja a felhasználhatóságot, és automatizálhatja a megoldás próbaverzióját, így a leendő felhasználók díjmentesen használhatják SaaS-, IaaS-vagy Microsoft-alkalmazási élményét a vásárlást megelőzően korlátozott ideig. A próbaverziós közzétételi lehetőséghez használt hívások közötti művelet a következő lehet: **ingyenes próbaverzió** vagy **tesztvezetés**. |
|**BYOL**  |A piactér használatával javíthatja a megoldást, és automatizálhatja a megoldás üzembe helyezését, és elvégezheti a pénzügyi tranzakciók külön történő elvégzését. A BYOL-ajánlatok típusai ideálisak a helyszíni és a felhőalapú Migrálás számára. A művelet meghívása **most már elérhető**.
| **Transact** | Az alkalmazás az Azure-ban fut, és erőforrásként közvetlenül az ügyfél Azure-előfizetésében is kiépíthető, ha az ügyfél a **Letöltés** meghívása műveletet választja. A szoftverlicenc-díjat opcionálisan megvásárolhatja és számlázhatja az ügyfél által választott fizetési eszköz és feltételek alapján, valamint dönthet úgy is, hogy az **ingyenes próbaverzióra** korlátozott időre szóló hozzáférést biztosít (csak az Azure Marketplace-en érhető el). |

>[!Note]
>A Transact Publishing lehetőség használatakor fontos megérteni a díjszabást, a számlázást, a számlázást és a kifizetési szempontokat az ajánlat típusának kiválasztása és az ajánlat létrehozása előtt. További információért tekintse át a [Marketplace számlázási és kereskedelmi megfontolásokat ismertető cikket](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>További lépések

*   A közzétételi lehetőség [kiválasztása után kiválaszthatja az](./publisher-guide-by-offer-type.md) ajánlat bemutatására használni kívánt ajánlat típusát.
*   Az ajánlat kiválasztásának és konfigurálásának véglegesítéséhez tekintse át a támogathatósági követelményeket a közzétételi beállítások az ajánlat típusa szerint szakaszban.
*   Tekintse át a közzétételi mintákat a kirakati példákkal, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.



