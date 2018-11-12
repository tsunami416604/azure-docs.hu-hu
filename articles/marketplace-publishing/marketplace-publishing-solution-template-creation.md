---
title: Útmutató a megoldás a sablonok létrehozása a Marketplace-en |} A Microsoft Docs
description: Részletes útmutató bemutatja, hogyan hozhat létre, tanúsítása és a egy virtuális gépre kiterjedő kép Megoldássablon telepítése az Azure piactéren megvásárolt.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 4a72f11b55d1c315a9dce62de3e6d23c340baa51
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232861"
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Útmutató a megoldássablon létrehozásához az Azure Marketplace-en
1. lépés befejezése után [fióklétrehozás és a regisztrációs][link-acct-creation], hogy interaktív, egy Azure-kompatibilis megoldás sablon létrehozásakor [létrehozására vonatkozó technikai Előfeltételek egy a megoldássablon](marketplace-publishing-solution-template-creation-prerequisites.md). Most végigvezetjük meg létrehozni egy megoldássablon több virtuális gép a lépéseket a [közzétételi portál] [ link-pubportal] az Azure Marketplace-en.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>A megoldás sablon ajánlat létrehozása a közzétételi portálon
Lépjen a [ https://publish.windowsazure.com ](http://publish.windowsazure.com). Amikor első alkalommal való bejelentkezés a [közzétételi portál](https://publish.windowsazure.com/), használja a fiókot az értékesítői profilra a vállalat regisztrálva lett. Később mint a közzétételi portálon társadminisztrátorként bármely alkalmazott a vállalati is hozzáadhat.

### <a name="1-select-solution-templates"></a>1. Válassza ki a "Megoldássablonok"
  ![rajz][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Új megoldássablon létrehozásához
  ![rajz][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Start-topológiákkal
A megoldássablon az összes hozzá tartozó topológia „szülőeleme”. Egy ajánlat/megoldássablonban több topológiát is megadhat. Amikor egy ajánlatot a rendszer előkészítésre továbbít, az összes topológiájával együtt továbbítja. Adja meg az ajánlat az alábbi lépésekkel:     

* Olyan topológiák létrehozását: "Topológia azonosító" a általában a topológia nevét a megoldássablon. A topológia azonosítója az URL-címet használja a lent látható módon:

  Az Azure Marketplace-en: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Az Azure Portalon: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Adjon hozzá egy új verziót.

### <a name="4-get-your-topology-versions-certified"></a>4. Beszerzése a certified topológia verziók
Töltse fel, hogy a topológia adott verziójának kiépítése az összes szükséges fájlokat tartalmazó zip-fájlt. A zip-fájl a következőket kell tartalmaznia:

* *mainTemplate.json* és *createUiDefinition.json* fájlt a gyökérkönyvtárban.
* Minden hivatkozott sablonok és az összes szükséges szkriptek.

  > [!TIP]
  > Bár a megoldás sablon-topológiák létrehozásához, és azt minősítésre, az üzleti első dolgozhat a fejlesztők marketing és/vagy jogi részleg a cég a marketing és jogi tartalmat is dolgozhat.
  >
  >

## <a name="next-steps"></a>További lépések
Most, hogy a megoldássablon létrehozott és feltöltött zip-fájl, kérjük, kövesse az utasításokat a [Marketplace marketing content útmutató](marketplace-publishing-push-to-staging.md) , mielőtt leküldené az ajánlat átmeneti üzembe helyezéséhez. A cikkek közzététele piactér teljes körű megtekintéséhez keresse fel [első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md).

Akkor is hasznos lehet a kapcsolódó cikkek:

* Virtuálisgép-rendszerképek: [kapcsolatos virtuálisgép-lemezképek az Azure-ban](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* A Virtuálisgép-bővítmények: [Azure Virtuálisgép-bővítmények és szolgáltatások](../virtual-machines/extensions/features-windows.md)
* Az Azure Resource Manager: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md) és [egyszerű sablon példák](https://github.com/rjmax/ArmExamples)
* Storage-fiók szabályozza: [figyelése a Storage-fiók szabályozási](https://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) és [prémium szintű storage](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
