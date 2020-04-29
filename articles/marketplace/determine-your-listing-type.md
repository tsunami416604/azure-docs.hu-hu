---
title: A közzétételi lehetőség meghatározása a Microsoft kereskedelmi piactér kirakatokban
description: Ez a cikk a jogosultsági feltételeket és a közzétételi követelményeket ismerteti azoknak a partnereknek, akik az alkalmazások Microsoft AppSource és az Azure Marketplace-en való közzétételét szeretnék megismerni.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e91ca0334276dbe9118eb0e40d042f598e8b3c0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415739"
---
# <a name="determine-your-publishing-option"></a>A közzétételi beállítások meghatározása

Az ajánlathoz választott közzétételi lehetőség közvetlenül a támogathatósági követelményekkel és a kereskedelmi piactér GTM kapcsolatos előnyökkel is jár. Még ennél is fontosabb, hogy a közzétételi lehetőség és az ajánlat típusa határozza meg, hogy a felhasználók hogyan fognak működni a kereskedelmi Piactéri ajánlatával.

Az ajánlat konfigurálásához meg kell ismernie a következő kulcsfontosságú kereskedelmi Marketplace-fogalmakat: a közzétételi lehetőségek, az ajánlatok típusai és a konfiguráció, valamint a hívások közötti művelet, amely szabályozza, hogy az ajánlat hogyan és hol jelenik meg a kereskedelmi piactéren.

Ebben a cikkben a következőkkel ismerkedhet meg:

- A megoldás megfelelő kirakatának meghatározása
- Az egyes kirakatokban elérhető közzétételi lehetőségek és hívások – művelet
- Az egyes közzétételi lehetőségekhez elérhető ajánlati típusok

## <a name="commercial-marketplace-publishing-options"></a>Kereskedelmi piactér közzétételi lehetőségei

A következő táblázat a Microsoft AppSource és az Azure piactéren elérhető ajánlatok közzétételi lehetőségeit mutatja be.

|   | **Lista (kapcsolat)**  | **Lista (próbaverzió)**  | **Ingyenes** | **BYOL** | **Transact**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtuális gép** |  |  |  | Azure Piactér |  Azure Piactér |
| **Azure-alkalmazások (több virtuális gép)** |  |  | Azure Piactér | Azure Piactér | Azure Piactér  |
| **Tároló képe** |  |  | Azure Piactér | Azure Piactér |   |
| **IoT Edge modul** |  |  | Azure Piactér | Azure Piactér |   |
| **Felügyelt szolgáltatások** |  |  |  | Azure Piactér |   |
| **Tanácsadói szolgáltatások** | Mindkét kirakat |  |  |  |   |
| **SaaS-alkalmazás** | Mindkét kirakat | Mindkét kirakat | Mindkét kirakat |  | Mindkét kirakat * |
| **Microsoft 365 alkalmazás** | AppSource | AppSource |  |  | AppSource * *  |
| **Dynamics 365 bővítmény** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; SaaS-alkalmazás Transact-ajánlatai Microsoft AppSource jelenleg csak bankkártyával rendelkeznek.

A &#42;&#42; Microsoft 365 ajánlatok díjmentesen telepíthetők, és az SaaS-ajánlaton keresztül licencelési szolgáltatásként is megadhatók. További információkért tekintse [meg az Office 365-bővítmények a Microsoft kereskedelmi piactéren keresztüli bevételeit ismertető részt](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="selecting-a-storefront"></a>Kirakat kiválasztása

A közzétételi lehetőség kiválasztása előtt fontos megérteni a kereskedelmi Marketplace-megoldások,-alkalmazások és-szolgáltatások kirakat-alkalmazhatósági követelményeit. Az egyes kirakatok egyedi ügyfelekre vonatkozó követelményeket és meghatározott célközönségeket céloznak meg. Az ajánlat típusa, a Transact képességek, a kategória vagy az iparág határozza meg, hogy hol tegye közzé ajánlatát.

**Microsoft AppSource** alkalmazások olyan üzletági megoldások, amelyek az Azure-ban vagy a következő beépített platformokon érhetők el: Dynamics 365, Office 365, Power bi vagy Power apps. A AppSource Consulting Services olyan Professional Services-ajánlatok, amelyek segítenek az ügyfeleknek a Dynamics 365 és Power BI használatának megkezdésében vagy felgyorsításában.

Az **Azure Marketplace** -alkalmazások olyan technikai jellegű "építőelemek", amelyek beépítettek vagy beépítettek az Azure-hoz, és az informatikai vagy fejlesztői közönség számára készültek. Az Azure Marketplace Consulting Services olyan professzionális szolgáltatásokat kínál, amelyek segítenek az ügyfeleknek az Azure használatának megkezdésében vagy felgyorsításában.

>[!Note]
>Kereszthivatkozások (csak SaaS-alkalmazásokhoz): Ha egy lista vagy próbaverziós ajánlat megfelel a technikai és az üzleti felhasználókra vonatkozó feltételnek, az ajánlat mindkét kirakatban szerepel. További információ az alábbi közzétételi lehetőségekről.

## <a name="choose-a-publishing-option"></a>Közzétételi lehetőség kiválasztása

A rendelkezésre álló közzétételi lehetőségek különböző ügyfél-összevonást biztosítanak, miközben hozzáférést biztosítanak a megosztási és [kereskedelmi Piactéri előnyökhöz](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Jegyezze fel a közzétételi lehetőségnek megfelelő meghívások közötti műveletet:

| **Közzétételi lehetőség**    | **Leírás**  |
| :------------------- | :-------------------|
| **Listáját** | Az alkalmazás vagy szolgáltatás egyszerű listája, amely lehetővé teszi, hogy a kereskedelmi piactér felhasználói **kapcsolatba lépjenek** az ügyféllel a Kapcsolatfelvétel a hívással művelettel. |
| **Próba** | A kereskedelmi piactéren javíthatja a felderíthetővé tételét, és automatizálhatja a megoldás próbaverziójának használatát, így a potenciális felhasználók a vásárlásuk előtt korlátozott ideig ingyenesen használhatják SaaS-, IaaS-vagy Microsoft-alkalmazásbeli élményét. A próba-közzétételi lehetőséghez használt hívások közötti művelet az **ingyenes próbaverzió** vagy a **tesztelési meghajtó**. |
| **BYOL** | A kereskedelmi piactéren javíthatja a felderíthetővé tételét, automatizálhatja a megoldás üzembe helyezését, és elvégezheti a pénzügyi tranzakciók külön történő elvégzését. A BYOL-ajánlatok típusai ideálisak a helyszíni és a felhőalapú Migrálás számára. A hívási művelet **most már elérhető**.
| **Transact** | A Transact-ajánlatokat a kereskedelmi Piactéren keresztül értékesítjük. A Microsoft felelős a számlázásért és a gyűjteményért. A hívási művelet **most már elérhető**.|

> [!Note]
> A Transact Publishing lehetőség használatakor fontos megérteni a díjszabást, a számlázást, a számlázást és a kifizetési szempontokat az ajánlat típusának kiválasztása és az ajánlat létrehozása előtt. További információért tekintse át a [kereskedelmi Piactéri Transact-képességeket](./marketplace-commercial-transaction-capabilities-and-considerations.md)ismertető cikket.

## <a name="next-steps"></a>További lépések

- A közzétételi lehetőség [kiválasztása után kiválaszthatja az](./publisher-guide-by-offer-type.md) ajánlat bemutatására használni kívánt ajánlat típusát.
- Az ajánlat kiválasztásának és konfigurálásának véglegesítéséhez tekintse át a támogathatósági követelményeket a közzétételi beállítások az ajánlat típusa szerint szakaszban.
- Tekintse át a közzétételi mintákat a kirakati példákkal, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.
