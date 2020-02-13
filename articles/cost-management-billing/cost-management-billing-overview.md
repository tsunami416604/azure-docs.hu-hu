---
title: Az Azure Cost Management and Billing áttekintése | Microsoft Docs
description: Az Azure Cost Management and Billing szolgáltatásaival a számlázáshoz kapcsolódó adminisztrációs feladatokat hajthat végre, és kezelheti a költségekhez való számlázási célú hozzáférést. Használhatja az Azure-kiadások monitorozására és szabályozására, valamint az erőforrások használatának optimalizálására szolgáló szolgáltatásait is.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.custom: ''
ms.openlocfilehash: 1c6f03663a7487e814272c6296f6e7862c65c806
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769890"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Mi az az Azure Cost Management and Billing?

Az Azure-termékek és -szolgáltatások esetén a fizetés használat alapján történik. Amikor Azure-erőforrásokat hoz létre és használ, díjat kell fizetnie az erőforrásokért. Az Azure Cost Management and Billing szolgáltatásaival a számlázáshoz kapcsolódó adminisztrációs feladatokat hajthat végre, és kezelheti a költségekhez való számlázási célú hozzáférést. Használhatja az Azure-kiadások monitorozására és szabályozására, valamint az erőforrások használatának optimalizálására szolgáló szolgáltatásait is.

## <a name="understand-azure-billing"></a>Az Azure Billing ismertetése

Az Azure Billing szolgáltatásaival áttekintheti a kiszámlázott költségeket, és kezelheti a számlázási információkhoz való hozzáférést. Nagyobb szervezetekben általában a beszerzési és pénzügyi csapatok végzik a számlázási feladatokat.

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiókban kezelheti számláit, fizetéseit és nyomon követheti kiadásait. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Lehetséges tehát, hogy egyéni Azure-előfizetéssel és számlázási fiókkal rendelkezik. Emellett rendelkezhet hozzáféréssel a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Mindegyik forgatókönyvhöz egy külön számlázási fiók tartozik.

### <a name="billing-accounts"></a>Számlázási fiókok

Az Azure Portal jelenleg a következő típusú számlázási fiókokat támogatja:

- **Microsoft Online Services Program**: A Microsoft Online Services Program egyéni számlázási fiókjai akkor jönnek létre, amikor az Azure webhelyén keresztül regisztrál az Azure-ba. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Nagyvállalati Szerződés**: Nagyvállalati Szerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet [Nagyvállalati Szerződést (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) köt az Azure használatára.

- **Microsoft-ügyfélszerződés**: A Microsoft-ügyfélszerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet a Microsoft képviselőjével együttműködve Microsoft-ügyfélszerződést köt. Egyes régiókban, ha a felhasználó az Azure-webhelyen regisztrál egy [használatalapú fizetést használó fiókot](https://azure.microsoft.com/offers/ms-azr-0003p/), vagy frissíti [ingyenes Azure-fiókját](https://azure.microsoft.com/offers/ms-azr-0044p/), külön számlázási fiókot kaphat a Microsoft-ügyfélszerződéshez. További információkért tekintse meg [a Microsoft-ügyfélszerződés számlázási fiókjával kapcsolatos első lépéseket](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>A számlázási fiókok hatóköre
A hatókör egy csomópont a számlázási fiókban. Ennek használatával lehet megtekinteni és kezelni a számlázást. Itt lehet kezelni a számlázási adatokat, a fizetéseket, a számlákat, és itt történik az általános fiókkezelés.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók     | Egy vagy több Azure-előfizetés önálló tulajdonosát (fiókadminisztrátorát) jelöli. A fiókadminisztrátor jogosult elvégezni bizonyos számlázási feladatokat, például az előfizetések létrehozását, a számlák megtekintését vagy az előfizetések számlázásának módosítását.  |
|Előfizetés     |  Adott Azure-erőforrások csoportja. A számla létrehozása az előfizetés hatókörben történik. Saját fizetési módokkal rendelkezik, amelyek a számlák kiegyenlítésére szolgálnak.|


#### <a name="enterprise-agreement"></a>Nagyvállalati Szerződés

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók    | Egy Nagyvállalati Szerződésre való regisztrációt jelöl. A számla létrehozása a számlázási fiók hatókörben történik. A struktúráját a bevont részlegek és regisztrált fiókok határozzák meg.  |
|Részleg     |  Regisztrált fiókok opcionális csoportja.      |
|Regisztrációs fiók     |  Egyetlen fióktulajdonost jelöl. Az Azure-előfizetések létrehozása a regisztrációs fiók hatókörben történik.  |


#### <a name="microsoft-customer-agreement"></a>Microsoft-ügyfélszerződés

|Hatókör  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Egy több Microsoft-terméket és -szolgáltatást magában foglaló ügyfélszerződést jelöl. A számlázási fiók struktúráját a számlázási profilok és a számlázási szakaszok határozzák meg.   |
|Számlázási profil     |  Egy számlát és annak fizetési módjait jelöli. Ebben a hatókörben történik számlakiállítás. A számlázási profil több számlázási szakaszt is magában foglalhat.      |
|Számlázási szakasz     |   Egy költségcsoportot jelöl a számlán belül. Az előfizetések és más vásárlások a számlázási szakasz hatókörhöz vannak társítva.    |


## <a name="understand-azure-cost-management"></a>Az Azure Cost Management ismertetése
A költségkezelés az a folyamat, amely során hatékonyan megtervezi és szabályozza az üzleti költségeket. A költségkezelési feladatokat általában pénzügyi, felügyeleti és az alkalmazásokat kezelő csapatok végzik. Az Azure Cost Management + Billing segítségével a szervezetek költségtudatosan tervezhetnek. Továbbá támogatást nyújt a költségek hatékony elemzéséhez, és a felhőköltségek optimalizálásának kezdő lépéseihez. Ha több információra van szüksége arról, hogy szervezetileg hogyan közelíthető meg a költségkezelés, tekintse meg a következő cikket: [Az Azure Cost Management ajánlott eljárásai](./costs/cost-mgt-best-practices.md).

Az [Azure Cost Management áttekintését ismertető videó](https://www.youtube.com/watch?v=el4yN5cHsJ0) megtekintésével gyors képet alkothat arról, hogyan segíthet az Azure Cost Management az Azure-ban felmerülő költségek csökkentésében.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Habár kapcsolódik hozzá, a számlázás eltér a költségkezeléstől. A számlázás a számlák kiállításának folyamata az ügyfelek részére árucikkekről vagy szolgáltatásokról, valamint a kereskedelmi kapcsolat kezelése.

A Cost Management rámutat szervezete költség- és a felhasználási mintáira, bővített analitikával. A Cost Management jelentései az Azure-szolgáltatások és a külső Marketplace-ajánlatok használatán alapuló költségeket jelenítik meg. A költségek a megegyezés szerinti árakon alapulnak, figyelembe véve a foglalási és Azure Hybrid Benefit-kedvezményeket. A jelentések együttesen kimutatják a belső és külső használati költségeket, valamint az Azure Marketplace-díjakat. Az egyéb díjak, mint például a foglalások, támogatási díjak és az adók, még nem látszanak a jelentésekben. A jelentések segítenek értelmezni a kiadásokat és az erőforrások felhasználtságát, továbbá könnyebben fellelhet esetleges rendellenességeket kiadásaiban. Ezen felül prediktív elemzések is elérhetők. A Cost Management Azure-beli felügyeleti csoportok, költségvetések és javaslatok használatával egyértelműen megmutatja, hogyan vannak rendszerezve költségei, és hogyan csökkenthetné azokat.

Az Azure Portal vagy pedig a különféle API-k használatával automatizálhatja az adatexportálást, hogy integrálhassa a költségadatokat külső rendszerekbe és folyamatokba. Emellett lehetősége van a számlázási adatok automatikus exportálására és jelentések ütemezésére is.

### <a name="plan-and-control-expenses"></a>Költségtervezés és -irányítás

A Cost Management a következő módokon segít a költségek tervezésében és szabályozásában: Költségelemzés, költségvetések, javaslatok és a Cost Management adatainak exportálása.

A költségelemzés használatával megvizsgálhatja és elemezheti szervezete költségeit. Megtekintheti szervezete összesített költségeit, hogy jobban átláthassa, hogy mely területeken vannak elhatárolt költségek, valamint felismerheti a kiadási trendeket. Továbbá láthatja az idők során felhalmozott költségeket, így a költségvetéshez viszonyítva készíthet havi, negyedéves vagy akár éves költségtrendbecsléseket is.

A költségvetések használatával egyszerűbben kalkulálhat előre, valamint biztosíthatja a pénzügyi átláthatóságot szervezetén belül. Segítségükkel elkerülheti a költségküszöbök vagy korlátok átlépését. Abban is hasznára lehetnek, hogy másokat értesíthessen kiadásaikról a proaktív költségkezelés érdekében. Továbbá kimutatják, hogyan alakulnak kiadásai az idő során.

A javaslatok a kihasználatlan vagy alacsony kihasználtságú erőforrások kimutatásával szemléltetik, hogyan optimalizálhatja és fejlesztheti a költséghatékonyságot. Emellett alacsonyabb költségű lehetőségeket is ajánlanak. Ha a javaslatok megfogadásával változtat erőforrásai kihasználásán, pénzt takaríthat meg. Hogy hozzákezdhessen, első lépésként érdemes átnéznie a költségoptimalizálási javaslatokat. Így tájékozódhat arról, hogy melyek a potenciálisan kevésbé hatékonyan kihasznált erőforrásai. Ezt követően az egyik javaslat alapján egy költséghatékonyabb módot választ az Azure-erőforrásai felhasználásához. Ezután csak jóvá kell hagynia a műveletet, hogy a módosítás biztosan sikeresen végbe menjen.

Ha külső rendszereket használ a költségadatokhoz való hozzáféréshez vagy áttekintésükhöz, az Azure-ból könnyedén kiexportálhatja az adatokat. Ezen felül beállíthat egy napi rendszerességű ütemezett exportot CSV-formátumban, az adatfájlokat pedig eltárolhatja az Azure Storage-ban. Ezt követően máris hozzáférhet az adatokhoz a külső rendszeren keresztül is.

### <a name="consider-cloudyn"></a>Fontolja meg a Cloudyn használatát

A [Cloudyn](./cloudyn/overview.md) egy, a Cost Managementhez kapcsolódó Azure-szolgáltatás. A Cloudyn használatával nyomon követheti Azure-erőforrásai felhőhasználatát és költségeit. Ezenkívül támogatja az egyéb felhőszolgáltatókat, beleértve az AWS-t és a Google-t. A könnyen értelmezhető irányítópult-jelentések segítenek a költséglefoglalásban és a költséghelyi visszacsatolásban/elszámolásban. A Cost Management jelenleg nem támogatja költséghelyi visszacsatolásos/elszámolásos, valamint az egyéb felhőszolgáltatókat. Azonban a Cloudyn _igen_. A Cost Management jelenleg nem támogat Microsoft-felhőszolgáltatói (CSP-) fiókokat, a Cloudyn viszont igen. Ha CSP-fiókokkal rendelkezik, vagy a költséghelyi visszacsatolást/elszámolást szeretné használni, a Cloudynnel kezelheti a költségeit.

[Az Azure Cost Management és a Cloudyn videója](https://www.youtube.com/watch?v=PmwFWwSluh8) ajánlásokat mutat be arra vonatkozóan, hogy az üzleti igényeknek megfelelően mikor érdemes az Azure Cost Management vagy a Cloudyn mellett dönteni.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>További Azure-eszközök

Az Azure más olyan eszközökkel is rendelkezik, amelyek képezik az Azure Cost Management and Billing szolgáltatásainak a részét. Fontos szerepet játszanak azonban a költségkezelési folyamatban. Az eszközökkel kapcsolatos további tudnivalókért lásd az alábbi hivatkozásokat.

- [Azure Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) – segítségével becslést végezhet kezdeti felhőköltségeiről.
- [Azure Migrate](../migrate/migrate-overview.md) – felmérheti adatközpontja jelenlegi számítási feladatait, így betekintést nyerhet abba, hogy mit várjon el egy Azure helyettesítő megoldástól.
- [Azure Advisor](../advisor/advisor-overview.md) - azonosíthatja használaton kívüli virtuális gépeit, és javaslatokat kaphat Azure fenntartott példányok vásárlásával kapcsolatban.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) – használja az aktuális helyszíni Windows Server- vagy SQL Server-licenceit az Azure-beli virtuális gépeken a költségek csökkentése érdekében.


## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett a Cost Management and Billinggel, a következő lépés a szolgáltatás használatának a megkezdése.

- Használja az Azure Cost Managementet [költségeinek elemzésére](./costs/quick-acm-cost-analysis.md).
- További információért megtekintheti az [Azure Cost Management ajánlott eljárásait](./costs/cost-mgt-best-practices.md).
