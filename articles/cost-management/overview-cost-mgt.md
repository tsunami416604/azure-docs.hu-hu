---
title: Az Azure Cost Management áttekintése | Microsoft Docs
description: Az Azure Cost Management egy költségkezelő megoldás, amely segítséget nyújt az Azure-költségek figyeléséhez és szabályozásához, valamint optimalizálja az erőforrások használatát.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: a90ef531cedb5e4c32a8f0af8b6cca86a93fb39a
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997124"
---
# <a name="what-is-azure-cost-management"></a>Mi az Azure Cost Management?

A költségkezelés az a folyamat, amely során hatékonyan megtervezi és szabályozza az üzleti költségeket. A költségkezelési feladatokat általában pénzügyi, felügyeleti és az alkalmazásokat kezelő csapatok végzik. Az Azure Cost Management a Microsoft nagyvállalati szerződés (EA) segítségével segít a szervezeteknek a tervezésben, figyelembe véve a költségeket. Továbbá támogatást nyújt a költségek hatékony elemzésében, és a felhőköltségek optimalizálásának kezdő lépéseiben. Ha több információra van szüksége arról, hogy szervezetileg hogyan közelíthető meg a költségkezelés, tekintse meg a következő cikket: [Az Azure Cost Management ajánlott eljárásai](cost-mgt-best-practices.md).

Habár kapcsolódik hozzá, a számlázás eltér a költségkezeléstől. A számlázás a számlák kiállításának folyamata az ügyfelek részére árucikkekről vagy szolgáltatásokról, valamint a kereskedelmi kapcsolat kezelése.  A számlázási feladatokat általában a beszerzés és a pénzügyi csapatok végzik.

A Cost Management rámutat szervezete költség- és a felhasználási mintáira, bővített analitikával. Az Azure Cost Management jelentései tartalmazzák az Azure-költségeket, a fenntartott példányokat és az Azure Hybrid Benefit-felhasználást. A jelentések együttesen kimutatják a belső és külső használati költségeket, valamint az Azure Marketplace-díjakat. Az egyéb díjak, mint például a foglalások, támogatási díjak és az adók, még nem látszanak a jelentésekben. A jelentések segítenek értelmezni a kiadásokat és az erőforrások felhasználtságát, továbbá könnyebben fellelhet esetleges rendellenességeket kiadásaiban. Ezen felül prediktív elemzések is elérhetők. A Cost Management Azure-beli felügyeleti csoportok, költségvetések és javaslatok használatával egyértelműen megmutatja, hogyan vannak rendszerezve költségei, és hogyan csökkenthetné azokat.

Az Azure Portal vagy pedig a különféle API-k használatával automatizálhatja az adatexportálást, hogy integrálhassa a költségadatokat külső rendszerekbe és folyamatokba. Emellett lehetősége van a számlázási adatok automatikus exportálására és jelentések ütemezésére is.

## <a name="plan-and-control-expenses"></a>Költségtervezés és -irányítás

A Cost Management többek közt a következő módokon segíthet a költségirányítással, valamint előre tervezni bizonyos költségekkel: költségelemzés, költségvetések, javaslatok és költségelemzési adatok exportálása.

A költségelemzés használatával megvizsgálhatja és elemezheti szervezete költségeit. Megtekintheti szervezete összesített költségeit, hogy jobban átláthassa, hogy mely területeken vannak elhatárolt költségek, valamint felismerheti a kiadási trendeket. Továbbá láthatja az idők során felhalmozott költségeket, így a költségvetéshez viszonyítva készíthet havi, negyedéves vagy akár éves költségtrendbecsléseket is.

A költségvetések használatával egyszerűbben kalkulálhat előre, valamint biztosíthatja a pénzügyi átláthatóságot szervezetén belül. Segítségükkel elkerülheti a költségküszöbök vagy korlátok átlépését. Abban is hasznára lehetnek, hogy másokat értesíthessen kiadásaikról a proaktív költségkezelés érdekében. Továbbá kimutatják, hogyan alakulnak kiadásai az idő során.

A javaslatok a kihasználatlan vagy alacsony kihasználtságú erőforrások kimutatásával szemléltetik, hogyan optimalizálhatja és fejlesztheti a költséghatékonyságot. Emellett alacsonyabb költségű lehetőségeket is ajánlanak. Ha a javaslatok megfogadásával változtat erőforrásai kihasználásán, pénzt takaríthat meg. Hogy hozzákezdhessen, első lépésként érdemes átnéznie a költségoptimalizálási javaslatokat. Így tájékozódhat arról, hogy melyek a potenciálisan kevésbé hatékonyan kihasznált erőforrásai. Ezt követően az egyik javaslat alapján egy költséghatékonyabb módot választ az Azure-erőforrásai felhasználásához. Ezután csak jóvá kell hagynia a műveletet, hogy a módosítás biztosan sikeresen végbe menjen.

Ha külső rendszereket használ a költségadatokhoz való hozzáféréshez vagy áttekintésükhöz, az Azure-ból könnyedén kiexportálhatja az adatokat. Ezen felül beállíthat egy napi rendszerességű ütemezett exportot CSV-formátumban, az adatfájlokat pedig eltárolhatja az Azure Storage-ban. Ezt követően máris hozzáférhet az adatokhoz a külső rendszeren keresztül is.

## <a name="consider-cloudyn"></a>Fontolja meg a Cloudyn használatát

A [Cloudyn](overview.md) egy, a Cost Managementhez kapcsolódó Azure-szolgáltatás. A Cloudyn használatával nyomon követheti Azure-erőforrásai felhőhasználatát és költségeit. Ezenkívül támogatja az egyéb felhőszolgáltatókat, beleértve az AWS-t és a Google-t. A könnyen értelmezhető irányítópult-jelentések segítenek a költséglefoglalásban és a költséghelyi visszacsatolásban/elszámolásban. A Cost Management jelenleg nem támogatja költséghelyi visszacsatolásos/elszámolásos, valamint az egyéb felhőszolgáltatókat. Azonban a Cloudyn _igen_. A Cost Management jelenleg kizárólag Azure EA-fiókokat támogat. Habár a Cost Management nem támogatja az egyéni használatalapú fizetéses vagy a Microsoft Cloud szolgáltatói fiókokat, a Cloudyn igen. Ha az említett fiókok valamelyikével rendelkezik, a Cloudyn segítségével azokkal is lehetősége van költségeit kezelni.

## <a name="additional-azure-tools"></a>További Azure-eszközök

Az Azure más olyan eszközökkel is rendelkezik, amelyeket az Azure Cost Management szolgáltatáskészlete nem tartalmaz. Azonban fontos szerepük van a költségkezelés folyamatában. Az eszközökkel kapcsolatos további tudnivalókért lásd az alábbi hivatkozásokat.

- [Azure Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) – segítségével becslést végezhet kezdeti felhőköltségeiről.
- [Azure Migrate](../migrate/migrate-overview.md) – felmérheti adatközpontja jelenlegi számítási feladatait, így betekintést nyerhet abba, hogy mit várjon el egy Azure helyettesítő megoldástól.
- [Azure Advisor](../advisor/advisor-overview.md) - azonosíthatja használaton kívüli virtuális gépeit, és javaslatokat kaphat Azure fenntartott példányok vásárlásával kapcsolatban.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) – használja az aktuális helyszíni Windows Server- vagy SQL Server-licenceit az Azure-beli virtuális gépeken a költségek csökkentése érdekében.


## <a name="next-steps"></a>További lépések

Most, hogy már megismerkedett a Cost Managementtel, a következő lépés a szolgáltatás igénybe vétele.

- Használja az Azure Cost Managementet [költségeinek elemzésére](quick-acm-cost-analysis.md).
- További információért megtekintheti az [Azure Cost Management ajánlott eljárásait](cost-mgt-best-practices.md).
