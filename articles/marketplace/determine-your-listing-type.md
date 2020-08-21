---
title: A közzétételi lehetőség meghatározása – Microsoft kereskedelmi piactér
description: Ez a cikk az ajánlatok Microsoft AppSource és az Azure Marketplace-en való közzétételének támogathatósági feltételeit és követelményeit ismerteti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 4a27ace4e45d00a2fbb35b9e0e3e9b7c03383681
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705877"
---
# <a name="determine-your-publishing-option"></a>A közzétételi beállítások meghatározása

Az ajánlathoz választott közzétételi lehetőség közvetlenül a támogathatósági követelményekkel és a kereskedelmi piactér GTM kapcsolatos előnyökkel is jár. Még ennél is fontosabb, hogy a közzétételi lehetőség és az ajánlat típusa határozza meg, hogy a felhasználók hogyan fognak működni a kereskedelmi Piactéri ajánlatával.

Az ajánlat konfigurálásához meg kell ismernie a következő fő kereskedelmi Marketplace-fogalmakat: a közzétételi lehetőségek, az ajánlatok típusai és a konfiguráció, valamint a hívások közötti művelet, amely szabályozza, hogy az ajánlat hogyan és hol jelenik meg a kereskedelmi piactér online áruházakban.

Ebben a cikkben a következőkkel ismerkedhet meg:

- A megoldás megfelelő online áruházának meghatározása.
- A közzétételi lehetőségek és a meghívások közötti műveletek minden online áruházban elérhetők.
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
| **Lista** | Az alkalmazás vagy szolgáltatás egyszerű listája, amely lehetővé teszi, hogy a kereskedelmi piactér felhasználói **kapcsolatba lépjenek** az ügyféllel a Kapcsolatfelvétel a hívással művelettel. |
| **Próba** | A kereskedelmi piactéren javíthatja a felderíthetővé tételét, és automatizálhatja a megoldás próbaverziójának használatát, így a potenciális felhasználók a vásárlásuk előtt korlátozott ideig ingyenesen használhatják SaaS-, IaaS-vagy Microsoft-alkalmazásbeli élményét. A próba-közzétételi lehetőséghez használt hívások közötti művelet az **ingyenes próbaverzió** vagy a **tesztelési meghajtó**. |
| **BYOL** | A kereskedelmi piactéren javíthatja a felderíthetővé tételét, automatizálhatja a megoldás üzembe helyezését, és elvégezheti a pénzügyi tranzakciók külön történő elvégzését. A BYOL-ajánlatok típusai ideálisak a helyszíni és a felhőalapú Migrálás számára. A hívási művelet **most már elérhető**.
| **Tranzakciós** | A Transact-ajánlatokat a kereskedelmi Piactéren keresztül értékesítjük. A Microsoft felelős a számlázásért és a gyűjteményért. A hívási művelet **most már elérhető**.|

> [!Note]
> A Transact Publishing lehetőség használatakor fontos megérteni a díjszabást, a számlázást, a számlázást és a kifizetési szempontokat az ajánlat típusának kiválasztása és az ajánlat létrehozása előtt. További információért tekintse át a [kereskedelmi Piactéri Transact-képességeket](./marketplace-commercial-transaction-capabilities-and-considerations.md)ismertető cikket.

## <a name="selecting-an-online-store"></a>Online áruház kiválasztása

Minden online áruház egyedi vásárlói követelményeket és meghatározott célközönségeket céloz meg. Az ajánlat típusa, a Transact képességek és a kategória határozza meg az ajánlat közzétételének helyét. A kategóriák és alkategóriák az egyes online áruházakhoz a célközönség alapján vannak leképezve:

**Microsoft AppSource** a Dynamics 365, a Microsoft 365 és a Power platform üzleti vagy ipari megoldásait, valamint tanácsadói szolgáltatásokat keresnek.

Az Azure **Marketplace** olyan informatikai szakembereket és fejlesztőket céloz meg, akik az Azure-ra vagy az Azure-ra épülő megoldásokra, valamint az Azure használatát felgyorsító tanácsadó szolgáltatásokra

Válassza ki azt a kategóriát és alkategóriát, amely a legjobban igazodik a célközönséghez. A webalkalmazási tűzfalat például közzé kell tenni az Azure Marketplace-en a biztonsági kategória alatt, mivel a célközönség az informatikai szakemberek számára készült. Ehelyett közzé kell tenni egy AppSource alkalmazást az értékesítési kategória alatt, mivel a célközönség az üzleti felhasználók számára. Ha helytelen kategóriát vagy alkategóriát választ, előfordulhat, hogy az ajánlat nem megfelelő online áruházban van közzétéve.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Közzététel mindkét online áruházban (csak SaaS-ajánlatok esetén)

Az SaaS-ajánlatokat közzéteheti az Azure piactéren vagy a AppSource. Ha az SaaS-ajánlat a technikai közönség (Azure Marketplace) és egy üzleti közönség (AppSource *) számára készült* , válasszon ki egy kategóriát és/vagy egy alkategóriát az egyes online áruházakhoz. Az online áruházakban közzétett ajánlatoknak olyan értékkel kell rendelkezniük, amely az informatikai szakemberek *és* az üzleti felhasználók számára is kiterjeszthető.

> [!IMPORTANT]
> A mért számlázással rendelkező SaaS-ajánlatok az Azure Marketplace-en és a Azure Portalon keresztül érhetők el. Az SaaS-ajánlatok csak privát csomagokkal érhetők el a Azure Portalon keresztül.

| Mért számlázás | Nyilvános csomag | Saját csomag | Elérhető itt: |
|---|---|---|---|
| Igen             | Igen         | Nem           | Azure Marketplace és Azure Portal |
| Igen             | Igen         | Igen          | Azure Marketplace és Azure Portal * |
| Igen             | Nem          | Igen          | Csak Azure Portal |
| Nem              | Nem          | Igen          | Csak Azure Portal |

&#42; az ajánlat privát csomagja csak a Azure Portal keresztül érhető el

Például a díjszabással ellátott számlázással és a privát csomaggal (nem nyilvános csomaggal) kapcsolatos ajánlatokat a Azure Portal ügyfelei vásárolják meg. További információ a [Microsoft kereskedelmi piactéren elérhető privát ajánlatokról](private-offers.md).

### <a name="categories"></a>Kategóriák

A kategóriák és alkategóriák a célközönség alapján vannak leképezve az egyes online áruházakhoz. Válassza ki az ajánlathoz legjobban illeszkedő kategóriákat és alkategóriákat, valamint a kívánt célközönséget. Az alábbiak közül választhat:

- Legalább egy és legfeljebb két kategória. Lehetősége van elsődleges és másodlagos kategória kiválasztására.
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem választja ki valamelyik alkategóriát, az ajánlat továbbra is felderíthető lesz a kiválasztott kategóriában.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>További lépések

- A közzétételi lehetőség [kiválasztása után kiválaszthatja az](./publisher-guide-by-offer-type.md) ajánlat bemutatására használni kívánt ajánlat típusát.
- Az ajánlat kiválasztásának és konfigurálásának véglegesítéséhez tekintse át a támogathatósági követelményeket a közzétételi beállítások az ajánlat típusa szerint szakaszban.
- Tekintse át a közzétételi mintákat az online áruházban példákat arra, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.
