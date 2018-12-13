---
title: Az Azure számlázási API-kat az Azure használatának |} A Microsoft Docs
description: Ismerje meg az Azure számlázási használat és RateCard API-k, rávilágítanak az Azure erőforrás-használat és a trendek használt.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: erikre
ms.custom: seodec18
ms.openlocfilehash: 9e3ad2327297e0403b279ad40db2186fcb55ae93
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080979"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Azure Billing API-k használatával programozott módon juthat az Azure-használat
Használat és a resource adatok be az előnyben részesített adatelemző eszközökkel Azure Billing API-k használatával. Az Azure erőforrás-használati és RateCard API-k segítségével pontosan előrejelezheti és felügyelheti a költségeket. Az API-k vannak megvalósítva egy erőforrás-szolgáltató és a termékcsalád az az Azure Resource Manager által elérhetővé tett API-k egy részét.  

> [!div class="nextstepaction"]
> [Segítsen az Azure számlázási dokumentumok fejlesztésében](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="azure-invoice-download-api-preview"></a>Az Azure Számlaletöltési API (előzetes verzió)
Egyszer a [jóváhagyás már befejeződött](billing-manage-access.md#opt-in), letölthető számlák előzetes verzióját [számla API](/rest/api/billing). A szolgáltatások a következők:

* **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) , adja meg, hogy mely felhasználók vagy alkalmazások hozzáférhet a az előfizetéshez tartozó használati adatok. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel.
* **Dátum szerinti szűrés** -használatát a `$filter` paraméter használatával beolvas minden számla fordított időrendben számla időszak vége dátuma szerint. 

> [!NOTE]
> Ez a szolgáltatás első előzetes verziója van, és lehet visszamenőlegesen nem kompatibilisek változhat. Jelenleg nem érhető el az egyes előfizetési ajánlatok (EA, CSP aio-ra nem támogatott.) és az Azure Germany.

## <a name="azure-resource-usage-api-preview"></a>Az Azure erőforrás-használati API (előzetes verzió)
Az Azure használata [erőforrás-használati API](https://msdn.microsoft.com/library/azure/mt219003) kívánt becsült Azure-használati adatait. Az API-t tartalmazza:

* **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) , adja meg, hogy mely felhasználók vagy alkalmazások hozzáférhet a az előfizetéshez tartozó használati adatok. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel.
* **Óránkénti vagy napi aggregáció** – Hívóit adhatja meg időtartamgyűjtők e szeretnék az Azure-használati adatokat óránkénti vagy napi időtartamgyűjtők. Az alapértelmezett érték a napi.
* **(Erőforrás-címkét tartalmaz) példány metaadatok** – példányszintű részleteket, például a teljesen minősített erőforrás-uri (/subscriptions/ {előfizetés azonosítója} /...), az erőforrás adatait, és az erőforráscímkék. A metaadatok segítségével determinisztikus és programozott módon használati lefoglalni a címkék alapján használati esetek hasonló eltérő díjszabási.
* **Az erőforrás metaadatának** -kra vonatkozó Mi feldolgozott adjon erőforrás részleteit, például a fogyasztásmérő neve, mérőszám kategóriája, mérőszám alkategória, egységek és régió a hívó. Erőforrás-metaadatok terminológia igazítása az Azure Portalon keresztül is dolgozunk az Azure használati CSV, a nagyvállalati szerződés számlázási CSV és egyéb nyilvános, lehetővé teszi, hogy adatainak korreláltatására élményt.
* **A különböző típusú használati** – használati adatok érhető el ajánlat esetében, mint használatalapú fizetés, MSDN, pénzügyi kötelezettségvállalást, a fizetésre használható kredit és nagyvállalati szerződéssel rendelkező, kivéve a [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Azure-erőforrás RateCard API (előzetes verzió)
Használja a [Azure Resource RateCard API](https://msdn.microsoft.com/library/azure/mt219005) elérhető Azure-erőforrások és az egyes becsült díjszabási információk lekéréséhez. Az API-t tartalmazza:

* **Szerepköralapú hozzáférés-vezérlés az Azure** – a hozzáférési szabályzatok konfigurálhatók az a [az Azure portal](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) , adja meg, hogy mely felhasználók vagy alkalmazások hozzáférhet a RateCard adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés az olvasó, a tulajdonos vagy a közreműködői szerepkört.
* **Használatalapú fizetés, MSDN, kötelezettségvállalás és pénzjóváírásos ajánlatok támogatása (nagyvállalati szerződéssel rendelkező és [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) nem támogatott)** – Ez az API az Azure-ajánlat szintű díjszabás információival.  Ez az API hívója erőforrás részleteit és a díjak az ajánlati információkat kell adja át. Sajnáljuk, jelenleg nem biztosít Nagyvállalati díjakat számoljuk fel, mert a nagyvállalati szerződésre vonatkozó ajánlatok testreszabott regisztrációs mértékek. 

## <a name="scenarios"></a>Forgatókönyvek
Néhány lehetséges a használat és a RateCard API-k együttes használatával végzett forgatókönyv:

* **Azure-költések kezelése a hónap során** – a használati kombinációját használja, és RateCard API-k segítségével megismerkedhet a felhő jobb csak a hónap során. Használat és a díj becsült óránkénti és napi gyűjtők elemezheti.
* **Riasztásokat állíthat be** – becsült felhőbeli fogyasztást és a költségek, és erőforrás-alapú vagy pénzügyi-alapú riasztásokat állíthat be a használat és a RateCard API-k használatával.
* **Számlázási előrejelzése** – Get a becsült használat és a felhőbeli költségek, és gépi tanulási algoritmusok előre jelezni, hogy mi a számla lenne az elszámolási időszakban végén a alkalmazni.
* **Elemzés előtti használat** – előrejelezheti, hogy mekkora a számla lenne a várható használat a számítási feladatok Azure-ba való áthelyezésekor a RateCard API-val. Ha rendelkezik meglévő számítási feladatokat, az egyéb felhőkben vagy privát felhők, a használat az Azure-ral is leképezheti díjak beolvasni egy jobb becslés az Azure-költségek. Ez a becslés teszi lehetővé a kimutatástáblába és következő elemek között a különböző típusú túl az utólagos elszámolású csomagok, például a pénzügyi kötelezettségvállalás és a fizetésre használható kredit az ajánlat, és hasonlítsa össze. Az API-t is költség különbségek régiónként megtekintéséhez lehetővé teszi, és lehetővé teszi, hogy a Lehetőségelemzési költségelemzés annak érdekében, hogy a telepítési döntések meghozatalában.
* **Lehetőségelemzés** -
  
  * Képes meghatározni, hogy azt helyett egy másik régióban található, vagy az Azure-erőforrás egy másik konfigurációs a számítási feladatok futtatásához. Az Azure erőforrás-használati díjak az Azure-régiót használ a megfelelően változhatnak.
  * Azt is meghatározhatja, hogy ha egy másik Azure-ajánlat típus jobb sebesség nyújt egy Azure-erőforrás.
  
## <a name="partner-solutions"></a>Partneri megoldások
A [Cloud Cruiser és a Microsoft Azure számlázási API integrációját](billing-usage-rate-card-partner-solution-cloudcruiser.md) leíró szakasz azt ismerteti, hogyan működik közvetlenül a [Cloud Cruiser Express for Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) a Windows Azure Pack (WAP) portálról. A Microsoft Azure privát vagy üzemeltetett nyilvános felhő működtetési és pénzügyi aspektusait is zökkenőmentesen kezelheti egyetlen felhasználói felületről.   

## <a name="next-steps"></a>További lépések
* Tekintse meg a Kódminták a Githubon:
  * [Számlázási API kódmintája](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Használati API kódmintája](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API kódmintája](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Az Azure Resource Managerrel kapcsolatos további tudnivalókért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md). 



