---
title: Dynamics 365 for Customer Engagement technikai információ lap – az Azure Marketplace-en |} A Microsoft Docs
description: Hogyan adja meg a technikai információkat egy Dynamics 365 Customer Engagement-alkalmazás az appsource-ban Marketplace-en.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
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
ms.date: 01/02/2019
ms.author: pbutlerm
ms.openlocfilehash: 214abd64232130dd3fd5fdde510f7545732ac82e
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082942"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 Customer Engagement technikai információ lap

A **technikai információ** lapján a **új ajánlat** lap lehetővé teszi, hogy adja meg a Dynamics 365 Customer Engagement-alkalmazáshoz, beleértve a CRM-csomag- és marketing embléma eszközök részletes adatait.  Ezen a lapon négy részből áll: **Informace aplikace**, **CRM csomag**, **CRM csomag rendelkezésre állási**, és **Marketing-összetevők**. A hozzáfűzött csillag (*) a mező neve jelzi, hogy szükséges. 


## <a name="application-info-section"></a>Alkalmazás adatai szakasz

Ebben a szakaszban a Dynamics 365 alkalmazással kapcsolatos részletezze lesz.

![Alkalmazás adatai szakaszában az technikai információ lap](./media/dynce-technical-info-tab1.png)

A következő táblázat ismerteti ezeket a mezőket.

|      Mező                    |    Leírás                  |
|    ---------                  |  ---------------                |
|   Alap licencelési modell          |  Licencelési modell meghatározza, hogy hogyan ügyfelek rendelt-e az alkalmazás a Dynamics 365 felügyeleti központban. **Erőforrás** licencelési példányalapú, mivel a **felhasználói** vannak licencek rendelve egy bérlőt egy.  |
|  Kimenő S2S és CRM-Store hozzáférés biztonságossá tétele |  Lehetővé teszi a CRM biztonságos Store vagy a kimenő hozzáférést kiszolgálók (S2S) konfigurációját. *A szolgáltatás használatához a minősítési fázis során a Dynamics 365-csapattól speciális szempontok.* A Microsoft felveszi Önnel a kapcsolatot további lépések végrehajtása támogatja ezt a szolgáltatást.  |
| Fizessen elő a CRM-életciklus-események | Integráció a Dynamics 365-életciklus-események kell adnia egy dedikált szolgáltatás, amely egy speciális szerződések van regisztrálva. *A szolgáltatás használatához a minősítési fázis során a Dynamics 365-csapattól speciális szempontok.* Ezen képesség támogatásához a teljes további lépéseket, értesítjük.  |
| Alkalmazás-konfigurációs URL-címe | Lehetővé teszi, hogy a felhasználót, hogy az alkalmazás konfigurálása a weblap URL-címe |
| Vonatkozó Dynamics 365-termék  | Válassza ki a Dynamics 365-termékek, amelyre ez az ajánlat vonatkozik. Ez az ajánlat az appsource-ban a kijelölt termékek alatt fog megjelenni.  |
| Marketing-csak módosítása         | Ezt a beállítást Igen azt jelzi, hogy csak marketing leíró végzett módosítások a már meglévő ajánlatra.  Ezek a változások lehetővé teszik a felülvizsgálat és üzembe helyezési fázisok megkerülésére vonatkozó ajánlatot.  |
|  |  |


## <a name="crm-package-section"></a>CRM-csomag szakasz

Ebben a szakaszban az appsource-ban alkalmazáscsomag-fájl kapcsolatos részletezze lesz.  Ez az információ a Dynamics 365 érvényesítési és minősítési csapatok használják.

![CRM-csomag szakaszában az technikai információ lap](./media/dynce-technical-info-tab2.png)

A következő táblázat ismerteti ezeket a mezőket.

|      Mező                    |    Leírás                  |
|    ---------                  |  ---------------                |
|  A csomag fájlneve     |  A fájlnév a csomag (.zip).  Ez a név *nem* nyilvános és a Dynamics 365 hitelesítő csapata által belsőleg használandó.  |
|  URL-cím                          |  Azure Storage-fiókot, amely tartalmazza a feltöltött alkalmazáscsomag-fájl URL-címe. Az URL-címet tartalmaznia kell egy írásvédett SAS-kulcs, hogy a csomag az ellenőrzéshez csomópontmetrikák csapatunk.  |
| Egynél több crm-csomag     | Válassza ki a csak igen, ha támogatja a különböző verzióinak különböző csomagokkal crm.  Minden verzió lesz a megfelelő csomag fájl munkamegosztást külön-külön kell létrehoznia.  |
| A forgatókönyv és a használati eset eszköz   | Lehetővé teszi a egy működési specifikáció szerinti dokumentum, a Dynamics 365-ellenőrzési csoportja általi használatra, az alkalmazás feltöltésének.  Ez specifikációja előnyben részesített formátuma a [E2E felhasználói forgatókönyv sablon](http://download.microsoft.com/download/5/1/8/51812AC9-BCD8-489F-937C-5D439C494EC1/E2E%20User%20Scenario%20Template.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>CRM-csomag rendelkezésre állási szakasz

Ebben a szakaszban válassza ki, mely földrajzi régiók az alkalmazás-ügyfelek számára elérhető lesz.  A következő szuverén régiók telepítését *speciális engedélyek és az érvényesítés megkövetelése* a minősítési folyamat során: [A Németországi](https://docs.microsoft.com/azure/germany/), [Egyesült Államok kormányának Felhője](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), és tipp.


## <a name="marketing-artifacts-section"></a>Marketing-összetevők szakasz

Ez a szakasz megköveteli, hogy egy alkalmazás emblémája, mely a használható, amelyek az AppSource-piactéren a csomag feltöltése.  A emblémakép PNG formátumú legyen, és legfeljebb 255 x 115 mérete képpont lehet.


## <a name="next-steps"></a>További lépések

Azt javasoljuk, hogy az alkalmazás egy bemutatási kínál; Ehhez hajtsa végre a [Test Drive lap](./cpp-testdrive-tab.md)
