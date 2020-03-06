---
title: Azure-használati adatok lekérése az Azure Billing API-kkal | Microsoft Docs
description: Ismerkedjen meg az Azure Billing Usage és a RateCard API-val, amelyek betekintést nyújtanak az Azure-erőforrások használatába és trendjeibe.
author: tonguyen
ms.reviewer: mumami
tags: billing
ms.service: cost-management-billing
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 2765a1acca21081a696bb165b75a96e1a668aa40
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395843"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Az Azure Billing API-kkal szoftveres úton juthat az Azure-használatra vonatkozó megállapításokhoz.
Az Azure Billing API-kkal lekérhetők a használattal és erőforrásokkal kapcsolatos adatok az előnyben részesített adatelemző eszközökbe. Az Azure erőforrás-használati és RateCard API-k segítségével pontosan előrejelezheti és felügyelheti a költségeket. Az API-k erőforrás-szolgáltatóként vannak implementálva, és az Azure Resource Manager által közzétett API-k családjának részei.  

## <a name="azure-invoice-download-api-preview"></a>Azure Invoice Download API (előzetes verzió)
A [jóváhagyás befejezése után](manage-billing-access.md#opt-in) az [Invoice API](/rest/api/billing) előzetes verziójával letöltheti a számlákat. Többek között az alábbi funkciók érhetők el:

* **Azure-beli szerepköralapú hozzáférés-vezérlés** – hozzáférési szabályzatok konfigurálása az [Azure Portal](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) segítségével annak megadásához, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adataihoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
* **Dátum szerinti szűrés** – a `$filter` paraméterrel lekérheti az összes számlát fordított időrendi sorrendben, a számlázási időszak záró dátuma szerint.

> [!NOTE]
> Ez a funkció egyelőre az előzetes verzió első verziójaként érhető el, és előfordulhat, hogy olyan változásokon fog átesni, amelyek visszamenőlegesen nem kompatibilisek. Jelenleg nem érhető el minden előfizetési ajánlathoz (az EA, Felhőszolgáltató és AIO nem támogatott) és az Azure Germany rendszerében.

## <a name="azure-resource-usage-api-preview"></a>Azure Resource Usage API (előzetes verzió)
Az Azure [Resource Usage API](/previous-versions/azure/reference/mt219003(v=azure.100))-val megjelenítheti az Azure becsült használati adatait. Az API a következőket tartalmazza:

* **Azure-beli szerepköralapú hozzáférés-vezérlés** – hozzáférési szabályzatok konfigurálása az [Azure Portal](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) segítségével annak megadásához, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adataihoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
* **Óránkénti vagy napi összesítések** – a hívók meghatározhatják, hogy az Azure-használati adatokat óránkénti gyűjtőkben vagy napi gyűjtőkben szeretnék-e megkapni. Az alapértelmezett beállítás a napi.
* **Példány metaadatai (erőforrás-címkéket tartalmaz)** – az adatok példányszintű részletezése, például a teljes erőforrás URI-ja (/subscriptions/{előfizetési-azonosító}/..), az erőforráscsoport adatai és az erőforrás-címkék. Ezekkel a metaadatokkal determinisztikus és programozott módon oszthatja fel a használati adatokat a címkék alapján olyan használati esetekhez, mint például a szolgáltatások közötti díjszámítás.
* **Erőforrás-metaadatok** – az erőforrás részletei, például a mérőszám neve, a mérőszám kategóriája, a mérőszám alkategóriája, az egység és a régió lehetővé teszik a hívó számára, hogy jobban megértse a felhasználást. Arra is törekszünk, hogy az erőforrás-metaadatokat az Azure Portal, az Azure-használati CSV, a nagyvállalati szerződéses számlázási CSV és más, nyilvános felületek között összehangoljuk, így lehetővé téve az adatok a különböző felületek közötti egyeztetését.
* **Használati adatok különböző ajánlattípusokhoz** – a használati adatok olyan ajánlattípusokhoz érhetők el, mint a használatalapú fizetés, az MSDN, a pénzügyi keret, a pénzügyi kredit és a Nagyvállalati Szerződés, kivéve a [CSP](https://docs.microsoft.com/partner-center)-t.

## <a name="azure-resource-ratecard-api-preview"></a>Azure Resource RateCard API (előzetes verzió)
Az [Azure Resource RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)) használatával lekérheti az elérhető Azure-erőforrások listáját, illetve az egyes erőforrások becsült díjszabásával kapcsolatos információkat. Az API a következőket tartalmazza:

* **Azure-beli szerepköralapú hozzáférés-vezérlés** – a hozzáférési szabályzatok konfigurálása az [Azure Portalon](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) segítségével annak megadásához, hogy mely felhasználók vagy alkalmazások férhetnek hozzá a RateCard-adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót az olvasó, a tulajdonos vagy a közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetés használati adataihoz.
* **A használatalapú fizetéshez, MSDN-hez, pénzügyi kerethez és pénzügyi kredithez kapcsolódó ajánlatok támogatása (a Nagyvállalati Szerződés és a [CSP](https://docs.microsoft.com/partner-center) nem támogatott)** – ez az API az Azure ajánlatszintű díjszabásairól nyújt információt.  Az API hívójának át kell adnia az ajánlat adatait az erőforrás részleteinek és díjszabásának lekéréséhez. Jelenleg nem tudjuk megadni a Nagyvállalati Szerződés díjait, mert az ilyen ajánlatoknak regisztrációnként testreszabott díjszabásai vannak.

## <a name="scenarios"></a>Forgatókönyvek
Néhány forgatókönyv, amelyek a Usage- és RateCard API-k kombinációjával válnak lehetségessé:

* **Azure-költségek a hónap folyamán** – a Usage- és RateCard API-k kombinációjával jobb betekintést kaphat a hónap folyamán felmerülő felhőköltségekbe. Elemezheti az óránkénti és napi használatiadat-gyűjtőket és a díjbecsléseket.
* **Riasztások beállítása** – a Usage- és RateCard API-kkal megbecsülheti a felhőhasználatot és a díjakat, valamint beállíthat erőforrás-alapú vagy pénzügyi alapú riasztásokat.
* **Számla előrejelzése** – a becsült használati adatok és felhőköltés lekérése, valamint gépi tanulási algoritmusok alkalmazása a számlázási ciklus végén létrejövő számla előrejelzéséhez.
* **Felhasználás előtti költségelemzés** – a RateCard API-val előrejelezheti, hogy mekkora lenne a számla végösszege a várható használat alapján, amikor a számítási feladatokat az Azure-ba helyezi át. Ha más felhőkben vagy magánfelhőkben is rendelkezik meglévő számítási feladatokkal, a használatot az Azure díjszabásával is feltérképezheti, így jobb becslést kaphat az Azure-költségekről. Ez a becslés lehetővé teszi az ajánlat kimutatását, valamint a használatalapú fizetésen túli többi ajánlat közötti összehasonlítást, például a pénzügyi keret és a pénzügyi kredit esetében. Az API lehetővé teszi azt is, hogy régiónként is megtekintse a költségkülönbségeket, valamint hogy lehetőségelemzést végezzen a költségekről az üzembehelyezési döntések elősegítéséhez.
* **Lehetőségelemzés** -

  * Meghatározhatja, hogy a számítási feladatok egy másik régióban futtatva, vagy az Azure-erőforrás egy másik konfigurációjában futtatva lennének költséghatékonyabbak. Az Azure-erőforrások költségei a használt Azure-régiótól függően eltérőek lehetnek.
  * Azt is meghatározhatja, hogy egy másik Azure-ajánlat jobb díjszabást biztosít-e az Azure-erőforráshoz.


## <a name="next-steps"></a>Következő lépések
* A GitHubon elérhető kódminták megtekintése:
  * [Számlázási API kódmintája](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Használati API kódmintája](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API kódmintája](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* További információt az Azure Resource Managerről az [Azure Resource Manager áttekintésében](../../azure-resource-manager/management/overview.md) talál.
