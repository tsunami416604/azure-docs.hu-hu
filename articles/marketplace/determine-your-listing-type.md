---
title: A közzétételi lehetőség meghatározása a Microsoft kereskedelmi piactér kirakataiban
description: Ez a cikk ismerteti a jogosultsági feltételek és közzétételi követelmények a partnerek próbálják megérteni, hogyan tehet közzé alkalmazásokat a Microsoft AppSource és az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e91ca0334276dbe9118eb0e40d042f598e8b3c0f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415739"
---
# <a name="determine-your-publishing-option"></a>A közzétételi beállítások meghatározása

Az ajánlathoz kiválasztott közzétételi lehetőség közvetlenül kapcsolódik mind a jogosultsági követelményekhez, mind a kereskedelmi piac piacra vonatkozó GTM-előnyökhöz. Ennél is fontosabb, hogy a közzétételi lehetőség és az ajánlattípus kiválasztása határozza meg, hogy a felhasználók hogyan lépnek kapcsolatba a kereskedelmi piactér-ajánlattal.

Az ajánlat konfigurálásához meg kell értenie a következő legfontosabb kereskedelmi piactér-fogalmakat: a közzétételi lehetőségeket, az ajánlattípusokat és a konfigurációt, valamint a cselekvésre való felhívásokat, amelyek szabályozzák, hogy az ajánlat hogyan és hol jelenjen meg a kereskedelmi piactér kirakataiban.

Ebben a cikkben a következőkkel ismerkedhet meg:

- Hogyan állapítható meg a megoldásnak megfelelő kirakat?
- Az egyes kirakatokban elérhetők a közzétételi lehetőségek és a cselekvésre való felhívások
- Mely ajánlattípusok érhetők el az egyes közzétételi lehetőségekhez

## <a name="commercial-marketplace-publishing-options"></a>Kereskedelmi piactér közzétételi lehetőségei

Az alábbi táblázat a Microsoft AppSource és az Azure Marketplace ajánlattípusok közzétételi beállításait mutatja be.

|   | **Lista (kapcsolattartó)**  | **Lista (próbaverzió)**  | **Ingyenes** | **BYOL** | **Transact**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtuális gép** |  |  |  | Azure Piactér |  Azure Piactér |
| **Azure-alkalmazások (többvirtuális gép)** |  |  | Azure Piactér | Azure Piactér | Azure Piactér  |
| **Konténer képe** |  |  | Azure Piactér | Azure Piactér |   |
| **IoT Edge modul** |  |  | Azure Piactér | Azure Piactér |   |
| **Felügyelt szolgáltatások** |  |  |  | Azure Piactér |   |
| **Tanácsadói szolgáltatások** | Mindkét kirakat |  |  |  |   |
| **SaaS alkalmazás** | Mindkét kirakat | Mindkét kirakat | Mindkét kirakat |  | Mindkét kirakat* |
| **Microsoft 365 alkalmazás** | AppSource | AppSource |  |  | Alkalmazásforrás**  |
| **Dynamics 365 bővítmény** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; SaaS app Transact kínál a Microsoft AppSource jelenleg hitelkártya csak.

&#42;&#42; Microsoft 365 ajánlatok ingyenesen telepíthetők, és a SaaS-ajánlaton keresztül licencelési szolgáltatásként felhasználhatók. További információt az [Office 365-bővítmény monetizálása a Microsoft kereskedelmi piactéren keresztül című témakörben talál.](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)

## <a name="selecting-a-storefront"></a>Kirakat kiválasztása

Mielőtt kiválasztaná a közzétételi lehetőséget, fontos, hogy megértse a kereskedelmi piactéri megoldások, alkalmazások és szolgáltatások kirakati jogosultsági követelményeit. Minden kirakat egyedi ügyféligényeket szolgál ki, és meghatározott közönségeket céloz meg. Az ajánlat típusa, a tranzakciós képességek és a kategória vagy az iparág határozza meg, hogy hol tegye közzé az ajánlatot.

**A Microsoft AppSource** alkalmazások olyan üzletági megoldások, amelyek az Azure-ra vagy a beépített megoldásokra épülnek: Dynamics 365, Office 365, Power BI vagy Power Apps. Az AppSource tanácsadási szolgáltatások olyan professzionális szolgáltatások, amelyek segítenek az ügyfeleknek a Dynamics 365 és a Power BI használatának megkezdésében vagy használatának felgyorsításában.

**Az Azure Marketplace-alkalmazások** az Azure-ra vagy az Azure-ra épülő műszaki "építőelem" megoldások, amelyek et informatikai vagy fejlesztői közönségszámára szántak. Az Azure Marketplace tanácsadási szolgáltatásai olyan professzionális szolgáltatások, amelyek segítik az ügyfeleket az Azure használatának megkezdésében vagy használatának felgyorsításában.

>[!Note]
>Keresztlistázás (csak SaaS-alkalmazások esetén): ha egy lista vagy próbaalapú ajánlat megfelel mind a technikai, mind az üzleti felhasználói közönség feltételeinek, az ajánlat mindkét kirakatban megjelenik. További információ az alábbi közzétételi lehetőségekről.

## <a name="choose-a-publishing-option"></a>Közzétételi beállítás kiválasztása

A rendelkezésre álló közzétételi lehetőségek differenciált ügyfélelköteleződést biztosítanak, miközben hozzáférést biztosítanak az érdeklődőmegosztáshoz és a [kereskedelmi piachoz.](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits) Figyelje meg a közzétételi lehetőségnek megfelelő cselekvésre való felhívásokat:

| **Közzétételi beállítás**    | **Leírás**  |
| :------------------- | :-------------------|
| **Lista** | Az alkalmazás vagy szolgáltatás egyszerű listázása, amely lehetővé teszi, hogy egy kereskedelmi piactér-felhasználó kérje Önt, hogy kapcsolatba lépjen az ügyféllel a **Kapcsolatfelvételi** felhíváson keresztül. |
| **Próba** | A kereskedelmi piactér segítségével javíthatja a megoldás próbaélményének felderíthetőségét, és automatizálhatja a megoldás kipróbálási élményének kiépítését, lehetővé téve a leendő felhasználók számára, hogy a SaaS, az IaaS vagy a Microsoft alkalmazáson belüli élményét vásárlás előtt korlátozott ideig díjmentesen használhassák. A próbaverziós közzétételi lehetőséghez használt cselekvésre való felhívások az **ingyenes próbaverzió** vagy a **tesztvezetés.** |
| **BYOL** | A kereskedelmi piactér használatával javíthatja a felderíthetőséget és automatizálhatja a megoldás kiépítését, és külön végezheti el a pénzügyi tranzakciót. A BYOL ajánlattípusok ideálisak a helyszíni és a felhőalapú áttelepítéshez. A cselekvésre való felhívás **a Get it Now**.
| **Transact** | Transact ajánlatok értékesítik a kereskedelmi piacon. A Microsoft felelős a számlázásért és a beszedésért. A cselekvésre való felhívás **a Get it Now**.|

> [!Note]
> A Transact közzétételi lehetőség használatakor fontos, hogy az ajánlattípus kiválasztása és az ajánlat létrehozása előtt tisztában legyen az árképzési, számlázási, számlázási és kifizetési szempontokkal. További információért tekintse át a [Kereskedelmi piac kereskedelmi forgalom képességei című cikket.](./marketplace-commercial-transaction-capabilities-and-considerations.md)

## <a name="next-steps"></a>További lépések

- Miután úgy döntött, a közzétételi lehetőség, készen áll, hogy [válassza ki az ajánlat típusát,](./publisher-guide-by-offer-type.md) amely bemutatja az ajánlatot.
- Tekintse át a jogosultsági követelményeket a közzétételi lehetőségek ajánlattípus szerint szakaszban az ajánlat kiválasztásának és konfigurációjának véglegesítéséhez.
- Tekintse át a közzétételi minták at kirakat példákat, hogyan a megoldás leképezése egy ajánlat típusát és konfigurációját.
