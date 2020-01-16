---
title: A Azure Cost Management és a számlázás áttekintése | Microsoft Docs
description: A számlázási adminisztratív feladatok elvégzéséhez és a költségekhez való számlázási hozzáférés kezeléséhez Azure Cost Management és számlázási funkciókat használ. Emellett a szolgáltatás az Azure-kiadások figyelésére és szabályozására, valamint az Azure-erőforrások használatának optimalizálására is használható.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/20/2019
ms.topic: overview
ms.service: cost-management-billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: cadff1d83a8b47a540efe9b74ffaf6de171138b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987515"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Mi az Azure Cost Management és a számlázás?

Az Azure-termékekkel és-szolgáltatásokkal csak a ténylegesen használt funkciókért kell fizetnie. Az Azure-erőforrások létrehozásakor és használatakor az erőforrásokért kell fizetnie. A számlázási adminisztratív feladatok elvégzéséhez és a költségekhez való számlázási hozzáférés kezeléséhez Azure Cost Management és számlázási funkciókat használ. Emellett az Azure-kiadások figyelésére és szabályozására, valamint az Azure-erőforrások használatának optimalizálására szolgáló szolgáltatásai.

## <a name="understand-azure-billing"></a>Az Azure-számlázás ismertetése

Az Azure számlázási funkciói segítségével áttekintheti a számlázott költségeket, és kezelheti a számlázási adatokhoz való hozzáférést. A nagyobb szervezeteknél a beszerzések és a pénzügyi csapatok általában számlázási feladatokat végeznek.

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiókban kezelheti számláit, fizetéseit és nyomon követheti kiadásait. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Így előfordulhat, hogy egyéni Azure-előfizetéssel rendelkezik számlázási fiókkal. Emellett rendelkezhet hozzáféréssel a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Minden forgatókönyvhöz külön számlázási fiók szükséges.

### <a name="billing-accounts"></a>Számlázási fiókok

A Azure Portal jelenleg a következő típusú számlázási fiókokat támogatja:

- **Microsoft Online Services program**: a Microsoft Online Services programhoz tartozó egyéni számlázási fiók akkor jön létre, amikor az Azure-webhelyről regisztrál az Azure-ra. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Nagyvállalati szerződés**: létrejön egy nagyvállalati szerződés számlázási fiókja, amikor a szervezet aláír egy [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) az Azure használatára.

- **Microsoft**-szerződés: a Microsoft ügyfél-szerződéshez tartozó számlázási fiók akkor jön létre, ha a szervezet Microsoft-képviselővel működik együtt egy Microsoft-ügyfél-szerződés aláírására. Egyes régiókban, ha a felhasználó az Azure-webhelyen regisztrál egy [használatalapú fizetést használó fiókot](https://azure.microsoft.com/offers/ms-azr-0003p/), vagy frissíti [ingyenes Azure-fiókját](https://azure.microsoft.com/offers/ms-azr-0044p/), külön számlázási fiókot kaphat a Microsoft-ügyfélszerződéshez. További információkért tekintse meg [a Microsoft-ügyfélszerződés számlázási fiókjával kapcsolatos első lépéseket](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>A számlázási fiókok hatóköre
A hatókör a Számlázási fiók egyik csomópontja, amelyet a számlázás megtekintésére és kezelésére használhat. Itt kezelheti a számlázási adataikat, a befizetéseket, a számlákat és az általános fiókok felügyeletét.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók     | Egy vagy több Azure-előfizetés önálló tulajdonosát (fiókadminisztrátorát) jelöli. A fiókadminisztrátor jogosult elvégezni bizonyos számlázási feladatokat, például az előfizetések létrehozását, a számlák megtekintését vagy az előfizetések számlázásának módosítását.  |
|Előfizetés     |  Adott Azure-erőforrások csoportja. Az előfizetés hatókörében létrejön egy számla. Saját fizetési módokkal rendelkezik, amelyek a számlák kiegyenlítésére szolgálnak.|


#### <a name="enterprise-agreement"></a>Nagyvállalati szerződés

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók    | Egy Nagyvállalati Szerződésre való regisztrációt jelöl. A számla a Számlázási fiók hatókörében jön létre. Szervezeti egységek és beléptetési fiókok használatával strukturálva.  |
|Részleg     |  Regisztrált fiókok opcionális csoportja.      |
|Regisztrációs fiók     |  Egyetlen fióktulajdonost jelöl. Az Azure-előfizetések a beléptetési fiók hatókörében jönnek létre.  |


#### <a name="microsoft-customer-agreement"></a>Microsoft Ügyfélszerződés

|Hatókör  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Egy több Microsoft-terméket és -szolgáltatást magában foglaló ügyfélszerződést jelöl. A Számlázási fiók a számlázási profilok és a számla fejezetei alapján van strukturálva.   |
|Számlázási profil     |  Egy számlát és annak fizetési módjait jelöli. Ebben a hatókörben történik számlakiállítás. A számlázási profil több számla résszel is rendelkezhet.      |
|Számlázási szakasz     |   Egy költségcsoportot jelöl a számlán belül. Az előfizetések és egyéb vásárlások a számla szakasz hatóköréhez vannak társítva.    |


## <a name="understand-azure-cost-management"></a>A Azure Cost Management megismerése
A költségkezelés az a folyamat, amely során hatékonyan megtervezi és szabályozza az üzleti költségeket. A költségkezelési feladatokat általában pénzügyi, felügyeleti és az alkalmazásokat kezelő csapatok végzik. A Azure Cost Management + számlázási szolgáltatás segítségével a szervezetek a költségeket szem előtt tartva tervezik meg. Emellett hatékonyan elemezheti a költségeket, és lépéseket tehet a felhőalapú kiadások optimalizálása érdekében. Ha több információra van szüksége arról, hogy szervezetileg hogyan közelíthető meg a költségkezelés, tekintse meg a következő cikket: [Az Azure Cost Management ajánlott eljárásai](./costs/cost-mgt-best-practices.md).

Tekintse meg a [Azure Cost Management áttekintő videót](https://www.youtube.com/watch?v=el4yN5cHsJ0) , amely gyors áttekintést nyújt arról, hogy a Azure Cost Management hogyan segíthet pénzt megtakarítani az Azure-ban.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Habár kapcsolódik hozzá, a számlázás eltér a költségkezeléstől. A számlázás a számlák kiállításának folyamata az ügyfelek részére árucikkekről vagy szolgáltatásokról, valamint a kereskedelmi kapcsolat kezelése.

A Cost Management rámutat szervezete költség- és a felhasználási mintáira, bővített analitikával. A Cost Management jelentései az Azure-szolgáltatások és a harmadik féltől származó piactér-ajánlatok által felhasznált használaton alapuló költségeket mutatják be. A költségek az egyeztetett árakon és a foglalási és a Azure Hybrid Benefit kedvezmények alapján vehetők figyelembe. A jelentések együttesen kimutatják a belső és külső használati költségeket, valamint az Azure Marketplace-díjakat. Az egyéb díjak, mint például a foglalások, támogatási díjak és az adók, még nem látszanak a jelentésekben. A jelentések segítenek értelmezni a kiadásokat és az erőforrások felhasználtságát, továbbá könnyebben fellelhet esetleges rendellenességeket kiadásaiban. Ezen felül prediktív elemzések is elérhetők. A Cost Management Azure-beli felügyeleti csoportok, költségvetések és javaslatok használatával egyértelműen megmutatja, hogyan vannak rendszerezve költségei, és hogyan csökkenthetné azokat.

Az Azure Portal vagy pedig a különféle API-k használatával automatizálhatja az adatexportálást, hogy integrálhassa a költségadatokat külső rendszerekbe és folyamatokba. Emellett lehetősége van a számlázási adatok automatikus exportálására és jelentések ütemezésére is.

### <a name="plan-and-control-expenses"></a>Költségtervezés és -irányítás

A költségek megtervezésének és szabályozásának Cost Management módjai a következők: a Cost Analysis, a költségvetés, a javaslatok és a Cost Management-adatexportálás.

A költségelemzés használatával megvizsgálhatja és elemezheti szervezete költségeit. Megtekintheti szervezete összesített költségeit, hogy jobban átláthassa, hogy mely területeken vannak elhatárolt költségek, valamint felismerheti a kiadási trendeket. Továbbá láthatja az idők során felhalmozott költségeket, így a költségvetéshez viszonyítva készíthet havi, negyedéves vagy akár éves költségtrendbecsléseket is.

A költségvetések használatával egyszerűbben kalkulálhat előre, valamint biztosíthatja a pénzügyi átláthatóságot szervezetén belül. Segítségükkel elkerülheti a költségküszöbök vagy korlátok átlépését. Abban is hasznára lehetnek, hogy másokat értesíthessen kiadásaikról a proaktív költségkezelés érdekében. Továbbá kimutatják, hogyan alakulnak kiadásai az idő során.

A javaslatok a kihasználatlan vagy alacsony kihasználtságú erőforrások kimutatásával szemléltetik, hogyan optimalizálhatja és fejlesztheti a költséghatékonyságot. Emellett alacsonyabb költségű lehetőségeket is ajánlanak. Ha a javaslatok megfogadásával változtat erőforrásai kihasználásán, pénzt takaríthat meg. Hogy hozzákezdhessen, első lépésként érdemes átnéznie a költségoptimalizálási javaslatokat. Így tájékozódhat arról, hogy melyek a potenciálisan kevésbé hatékonyan kihasznált erőforrásai. Ezt követően az egyik javaslat alapján egy költséghatékonyabb módot választ az Azure-erőforrásai felhasználásához. Ezután csak jóvá kell hagynia a műveletet, hogy a módosítás biztosan sikeresen végbe menjen.

Ha külső rendszereket használ a költségadatokhoz való hozzáféréshez vagy áttekintésükhöz, az Azure-ból könnyedén kiexportálhatja az adatokat. Ezen felül beállíthat egy napi rendszerességű ütemezett exportot CSV-formátumban, az adatfájlokat pedig eltárolhatja az Azure Storage-ban. Ezt követően máris hozzáférhet az adatokhoz a külső rendszeren keresztül is.

### <a name="consider-cloudyn"></a>Fontolja meg a Cloudyn használatát

A [Cloudyn](./cloudyn/overview.md) egy, a Cost Managementhez kapcsolódó Azure-szolgáltatás. A Cloudyn használatával nyomon követheti Azure-erőforrásai felhőhasználatát és költségeit. Ezenkívül támogatja az egyéb felhőszolgáltatókat, beleértve az AWS-t és a Google-t. A könnyen értelmezhető irányítópult-jelentések segítenek a költséglefoglalásban és a költséghelyi visszacsatolásban/elszámolásban. A Cost Management jelenleg nem támogatja költséghelyi visszacsatolásos/elszámolásos, valamint az egyéb felhőszolgáltatókat. Azonban a Cloudyn _igen_. A Cost Management jelenleg nem támogatja Microsoft Cloud szolgáltatói (CSP) fiókok használatát, a Cloudyn azonban nem. Ha van CSP-fiókja, vagy ha a költséghelyi visszacsatolási/jóváírást szeretné használni, akkor a Cloudyn segítségével kezelheti a költségeit.

Tekintse meg a [Azure Cost Management és a Cloudyn videót, és](https://www.youtube.com/watch?v=PmwFWwSluh8) tekintse meg a javaslatokat, ha az üzleti igények alapján Azure Cost Management vagy Cloudyn használ.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>További Azure-eszközök

Az Azure más eszközökkel rendelkezik, amelyek nem részei a Azure Cost Management és a számlázási szolgáltatás készletének. Azonban fontos szerepet játszanak a Cost Management folyamatában. Az eszközökkel kapcsolatos további tudnivalókért lásd az alábbi hivatkozásokat.

- [Azure Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) – segítségével becslést végezhet kezdeti felhőköltségeiről.
- [Azure Migrate](../migrate/migrate-overview.md) – felmérheti adatközpontja jelenlegi számítási feladatait, így betekintést nyerhet abba, hogy mit várjon el egy Azure helyettesítő megoldástól.
- [Azure Advisor](../advisor/advisor-overview.md) - azonosíthatja használaton kívüli virtuális gépeit, és javaslatokat kaphat Azure fenntartott példányok vásárlásával kapcsolatban.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) – használja az aktuális helyszíni Windows Server- vagy SQL Server-licenceit az Azure-beli virtuális gépeken a költségek csökkentése érdekében.


## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a Cost Management és a számlázást, a következő lépés a szolgáltatás használatának megkezdése.

- Használja az Azure Cost Managementet [költségeinek elemzésére](./costs/quick-acm-cost-analysis.md).
- További információért megtekintheti az [Azure Cost Management ajánlott eljárásait](./costs/cost-mgt-best-practices.md).
