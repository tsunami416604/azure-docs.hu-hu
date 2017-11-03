---
title: "Azure számlázás API-k |} Microsoft Docs"
description: "Tudnivalók Azure számlázási használati és RateCard API-k, amely biztosítja a trendeket és az Azure erőforrás-felhasználás."
services: 
documentationcenter: 
author: BryanLa
manager: tonguyen
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/9/2017
ms.author: mobandyo;bryanla
ms.openlocfilehash: 26217d6f4e14166a89fbb561cb12d0af78ae6f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Azure számlázás API-k segítségével az Azure használatának programozott módon webhelynaplókat
Azure számlázás API-k segítségével lekéréses használati és erőforrás adatokat be a kívánt adatok elemzésére szolgáló eszközöket. Az Azure erőforrás-használat és RateCard API-k segítségével pontosan előre jelezni, és a költségek kezelésére. Az API-kat használják, mint a egy erőforrás-szolgáltató és az Azure Resource Manager által közzétett API-kban család része.  

## <a name="azure-invoice-download-api-preview"></a>Az Azure számla letöltési API (előzetes verzió)
Egyszer a [részt lett teljes](billing-manage-access.md#opt-in), előzetes verzióját használja, a letöltési számlák [számla API](/rest/api/billing). A szolgáltatások a következők:

* **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) adhatja meg, mely felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez.
* **Dátum szerinti szűrés** -használatát a `$filter` paraméter használatával beolvassa a számlák fordított időrendben a számla időszak vége dátuma. 

> [!NOTE]
> Ez a szolgáltatás első előzetes verziójában, és függvényében visszafelé nem kompatibilisek egymással módosítások lehetnek. Jelenleg nincs elérhető egyes előfizetési ajánlatok (EA, CSP, nem támogatott AIO) és a Németországi Azure.

## <a name="azure-resource-usage-api-preview"></a>Az Azure erőforrás-használat API (előzetes verzió)
Használja az Azure [erőforrás-használati API](https://msdn.microsoft.com/library/azure/mt219003) a becsült Azure felhasználási adatok eléréséhez. Az API-t tartalmazza:

* **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) adhatja meg, mely felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez.
* **Óránkénti vagy a napi aggregáció** - hívóknak adhat meg, hogy azok szeretné-e az Azure használati adatokat óránkénti időszakok vagy napi időszakok. Az alapértelmezett érték a napi.
* **(Erőforrás-címkét tartalmaz) példány metaadatok** – például a teljesen minősített erőforrás uri példányszintű részletek beszerzése (/subscriptions/ {előfizetés-azonosító} /..), a erőforrásadatok csoport és az erőforráscímkék. A metaadatok segítségével deterministically és szoftveresen használati lefoglalni a címkék alapján a használati esetek, például határokon díjszabási.
* **Erőforrás-metaadatok** -erőforrás részletek, például a mérési nevét, a mérési kategória, a mérő az alkategória, az egység és a régió biztosítják a hívó szeretné jobban megismerni mi felhasznált. Erőforrás-metaadatok terminológia igazítása az Azure-portálon keresztül is dolgozunk Azure használati CSV, számlázási CSV, és más nyilvánosan elérhető lép adatok összefüggéseket elemek között, így EA.
* **A különböző típusú használati** – használati adatok érhető el – használatalapú fizetés, MSDN, pénzügyi kötelezettségvállalást, pénzügyi kreditet és EA, például típusú kivéve [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Azure-erőforrás RateCard API (előzetes verzió)
Használja a [Azure Resource RateCard API](https://msdn.microsoft.com/library/azure/mt219005) elérhető Azure-erőforrások és az egyes becsült díjszabási információkat listájának lekérdezése. Az API-t tartalmazza:

* **Szerepköralapú hozzáférés-vezérlés az Azure** -a hozzáférési házirendek konfigurálásához a [Azure-portálon](https://portal.azure.com) vagy [Azure PowerShell-parancsmagok](/powershell/azure/overview) adhatja meg, mely felhasználók vagy alkalmazások ingyenesen juthatnak az RateCard adatokat. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása az olvasó, a tulajdonos vagy a közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez.
* **Használatalapú fizetés, az MSDN, a pénzügyi kötelezettségvállalást a és a pénzügyi kreditet ajánlatok támogatása (EA és [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) nem támogatott)** -Ez az API felület Azure ajánlat szintű arány információkat nyújt.  Ez az API felület védőfalkezelőbe meg kell felelnie az ajánlat információkat az erőforrás részletek és sebességét. A rendszer jelenleg nem tudja EA díjszabás adja meg, mert EA ajánlatok testreszabott beléptetési mértékek. 

## <a name="scenarios"></a>Forgatókönyvek
Az alábbiakban néhány lehetséges a használati és a RateCard API-k végrehajtott forgatókönyv:

* **A hónap során töltött Azure** - a használati kombinációját használja, és a hónap során töltött RateCard API-k, a felhő jobb betekintést eléréséhez. A használati és kell fizetni becslések óránkénti és napi gyűjtők elemezheti.
* **Riasztások beállítása** – a használati és a RateCard API-k segítségével becsült felhő használat és díjak és erőforrás-alapú vagy pénzügyi-alapú értesítések beállítása.
* **Számlázási előrejelzése** – Get a becsült felhasználás és a felhő kiadásokat, és gépi tanulási algoritmusok előre jelezni, mi a számlázási lenne az elszámolási időszak végén alkalmazni.
* **Elemzés előtti fogyasztás** – a RateCard API használatával megjósolható, hogy mekkora a számlázási okozna a várható használati a munkaterhelések az Azure-bA helyezi át. Ha meglévő alkalmazások más felhők vagy magánfelhőkben, az az Azure-ral használatának is leképezheti Azure jobb becslése beolvasandó díjszabás televíziózással töltenek. Ez a becslés lehetőséget nyújt az ajánlatot, és hasonlítsa össze és ezzel szemben a más típusú túl használatalapú fizetés, például a pénzügyi kötelezettségvállalást a és a pénzügyi kreditet közötti forgáspont. Az API-t is lehetővé teszi az költség különbségek régiónként lehetővé teszi, és lehetővé teszi egy lehetőségelemzések költség elemzés annak érdekében, hogy a telepítési döntések meghozatalában.
* **Elemzési** -
  
  * Azt is meghatározhatja, hogy egy másik régióban, vagy egy másik Azure-erőforrás-konfigurációban alkalmazásokat és szolgáltatásokat futtathatnak költséghatékonyabb. Azure-erőforrás költségek a használata az Azure-régió változhatnak.
  * Azt is meghatározhatja, hogy ha egy másik Azure-ajánlat típusa a nagyobb mértékben nyújt egy Azure-erőforrás.
  
## <a name="partner-solutions"></a>Partneri megoldások
[Felhő Cruiser és a Microsoft Azure számlázási API-integráció](billing-usage-rate-card-partner-solution-cloudcruiser.md) ismerteti, hogyan [felhő Cruiser Express Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) közvetlenül a Windows Azure Pack (WAP) portálról működik. Kezelheti a Microsoft Azure magán- vagy kihelyezett nyilvános felhő működési és pénzügyi aspektusainak zökkenőmentesen egyetlen felhasználói felületről.   

## <a name="next-steps"></a>Következő lépések
* Tekintse meg a mintakódjainak megtekintése a Githubon:
  * [Számla API kódminta](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Használati API kódminta](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API kódminta](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Az Azure Resource Managerrel kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md). 

* További információk az eszközkészlet a szükséges segítségnyújtáshoz felhő ismeretét kiadásokat, Gartner cikkben [piaci útmutató informatikai pénzügyi Management (ITFM) eszközök](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

