---
title: A közzétételi lehetőség meghatározása – Microsoft kereskedelmi piactér
description: Ez a cikk az ajánlatok Microsoft AppSource és az Azure Marketplace-en való közzétételének támogathatósági feltételeit és követelményeit ismerteti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: 7acfd5e1a1b1c44a9a109bb2b8bc38c7fcc57593
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484867"
---
# <a name="determine-your-publishing-option"></a>A közzétételi beállítások meghatározása

Az ajánlathoz választott közzétételi lehetőség közvetlenül a támogathatósági követelményekkel és a kereskedelmi piactér GTM kapcsolatos előnyökkel is jár. Még ennél is fontosabb, hogy a közzétételi lehetőség és az ajánlat típusa határozza meg, hogy a felhasználók hogyan fognak működni a kereskedelmi Piactéri ajánlatával.

Az ajánlat konfigurálásához ismernie kell a legfontosabb kereskedelmi Piactéri fogalmakat: a közzétételi lehetőségeket, az ajánlat típusát és konfigurációját, valamint azokat a listaelemeket, amelyek meghatározzák, hogy az ajánlat hogyan és hol jelenik meg a kereskedelmi piactér online áruházakban.

Ebben a cikkben a következőkkel ismerkedhet meg:

- A megoldás megfelelő online áruházának meghatározása.
- Az egyes online áruházakban milyen közzétételi lehetőségek és listaelem-beállítások érhetők el.
- Az egyes közzétételi lehetőségeknél milyen típusú ajánlatok érhetők el.

## <a name="commercial-marketplace-publishing-options"></a>Kereskedelmi piactér közzétételi lehetőségei

A következő táblázat a Microsoft AppSource és az Azure piactéren elérhető ajánlatok közzétételi lehetőségeit mutatja be.

|   | **Lista (kapcsolat)**  | **Lista (próbaverzió)**  | **Ingyenes** | **BYOL** | **Tranzakciós**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtuális gép** |  |  |  | Azure Piactér |  Azure Piactér |
| **Azure-alkalmazások (több virtuális gép)** |  |  | Azure Piactér | Azure Piactér | Azure Piactér  |
| **Tároló képe** |  |  | Azure Piactér | Azure Piactér |   |
| **IoT Edge modul** |  |  | Azure Piactér | Azure Piactér |   |
| **Felügyelt szolgáltatások** |  |  |  | Azure Piactér |   |
| **Tanácsadói szolgáltatások** | Mindkét online áruház |  |  |  |   |
| **SaaS-alkalmazás** | Mindkét online áruház | Mindkét online áruház | Mindkét online áruház |  | Mindkét online áruház * |
| **Microsoft 365 alkalmazás** | AppSource | AppSource |  |  | AppSource * *  |
| **Dynamics 365 bővítmény** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; SaaS-alkalmazás Transact-ajánlatai Microsoft AppSource jelenleg csak bankkártyával rendelkeznek.

A &#42;&#42; Microsoft 365 ajánlatok díjmentesen telepíthetők, és az SaaS-ajánlaton keresztül licencelési szolgáltatásként is megadhatók. További információkért tekintse [meg az Office 365-bővítmények a Microsoft kereskedelmi piactéren keresztüli bevételeit ismertető részt](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-listing-option"></a>Listaelem választása

A rendelkezésre álló tőzsdei lehetőségek differenciált ügyfél-összevonást biztosítanak, miközben hozzáférést biztosít a vezető megosztási és [kereskedelmi Piactéri előnyökhöz](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Jegyezze fel a közzétételi lehetőségnek megfelelő listaelem-beállításokat:

| **Közzétételi lehetőség**    | **Leírás**  |
| :------------------- | :-------------------|
| **Lista** | Az alkalmazás vagy szolgáltatás egyszerű felsorolása, amely lehetővé teszi, hogy a kereskedelmi piactér felhasználója kapcsolatba lépjen Önnel az ügyféllel a **kapcsolattartási** adatok listázása lehetőség használatával. |
| **Próba** | A kereskedelmi piactéren javíthatja a felderíthetővé tételét, és automatizálhatja a megoldás próbaverziójának használatát, így a potenciális felhasználók a vásárlásuk előtt korlátozott ideig ingyenesen használhatják SaaS-, IaaS-vagy Microsoft-alkalmazásbeli élményét. A próbaverziós közzétételi lehetőséghez használt listázási beállítások az **ingyenes próbaverziós** vagy a **Test Drive**. |
| **BYOL** | A kereskedelmi piactéren javíthatja a felderíthetővé tételét, automatizálhatja a megoldás üzembe helyezését, és elvégezheti a pénzügyi tranzakciók külön történő elvégzését. A BYOL-ajánlatok típusai ideálisak a helyszíni és a felhőalapú Migrálás számára. A listázási lehetőség **most már elérhető**.
| **Tranzakciós** | A Transact-ajánlatokat a kereskedelmi Piactéren keresztül értékesítjük. A Microsoft felelős a számlázásért és a gyűjteményért. A listázási lehetőség **most már elérhető**.|

> [!Note]
> A Transact Publishing lehetőség használatakor fontos megérteni a díjszabást, a számlázást, a számlázást és a kifizetési szempontokat az ajánlat típusának kiválasztása és az ajánlat létrehozása előtt. További információért tekintse át a [kereskedelmi Piactéri Transact-képességeket](./marketplace-commercial-transaction-capabilities-and-considerations.md)ismertető cikket.

## <a name="selecting-an-online-store"></a>Online áruház kiválasztása

Minden online áruház az üzleti és informatikai megoldások különböző felhasználói igényeit szolgálja. Az ajánlat típusa, a Transact képességek és a kategória határozza meg az ajánlat közzétételének helyét. A kategóriákat és alkategóriákat a közzétett megoldás típusa alapján rendeli hozzá a rendszer az egyes online áruházakhoz:

**Microsoft AppSource** olyan üzleti megoldásokat kínál, mint például az iparági megoldások és a tanácsadási szolgáltatások, a Dynamics 365, a Microsoft 365 és a Power platform.

Az Azure **Marketplace** az Azure-hoz vagy az Azure-hoz készült informatikai megoldásokat kínál, valamint az ügyfelek Azure-beli használatát felgyorsító tanácsadói szolgáltatásokat.

Válassza ki azt a kategóriát és alkategóriát, amely a legjobban megfelel a megoldás típusának. Például egy webalkalmazási tűzfal olyan informatikai megoldás, amelyet közzé kell tenni az Azure Marketplace-en a biztonság kategóriában. A szerződéses felügyeleti alkalmazás olyan üzleti megoldás, amelyet közzé kell tenni az értékesítési kategória AppSource. Ha helytelen kategóriát vagy alkategóriát választ, előfordulhat, hogy az ajánlat nem megfelelő online áruházban van közzétéve.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Közzététel mindkét online áruházban (csak SaaS-ajánlatok esetén)

Az SaaS-ajánlatokat közzéteheti az Azure piactéren vagy a AppSource. Ha az SaaS-ajánlat *az IT* -megoldás (Azure Marketplace) és egy üzleti megoldás (AppSource) is, válasszon ki egy kategóriát és/vagy egy alkategóriát, amely az egyes online áruházakra vonatkozik. Az online áruházakban közzétett ajánlatoknak IT-megoldásként *és* üzleti megoldásként is rendelkezniük kell az értékkel.

> [!IMPORTANT]
> A [mért számlázással](partner-center-portal/saas-metered-billing.md) rendelkező SaaS-ajánlatok az Azure Marketplace-en és a Azure Portalon keresztül érhetők el. Az SaaS-ajánlatok csak privát csomagokkal érhetők el a Azure Portalon keresztül.

| Mért számlázás | Nyilvános csomag | Saját csomag | Elérhető itt: |
|---|---|---|---|
| Igen             | Igen         | Nem           | Azure Marketplace és Azure Portal |
| Igen             | Igen         | Igen          | Azure Marketplace és Azure Portal * |
| Igen             | Nem          | Igen          | Csak Azure Portal |
| Nem              | Nem          | Igen          | Csak Azure Portal |

&#42; az ajánlat privát csomagja csak a Azure Portal keresztül érhető el

Például a díjszabással ellátott számlázással és a privát csomaggal (nem nyilvános csomaggal) kapcsolatos ajánlatokat a Azure Portal ügyfelei vásárolják meg. További információ a [Microsoft kereskedelmi piactéren elérhető privát ajánlatokról](private-offers.md).

### <a name="categories"></a>Kategóriák

A kategóriák és alkategóriák a megoldás típusa alapján vannak leképezve minden online áruházhoz. Válassza ki a megoldáshoz legjobban illeszkedő kategóriákat és alkategóriákat. Az alábbiak közül választhat:

- Legalább egy és legfeljebb két kategória. Kiválaszthat egy elsődleges és egy másodlagos kategóriát is.
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem választja ki valamelyik alkategóriát, az ajánlat továbbra is felderíthető lesz a kiválasztott kategóriában.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Következő lépések

- A közzétételi lehetőség kiválasztása után tekintse át a [közzétételi útmutatót ajánlat típusa szerint](./publisher-guide-by-offer-type.md).
