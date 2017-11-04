---
title: "A megoldássablon az a piactér létrehozását bemutató útmutatónak |} Microsoft Docs"
description: "Hozzon létre, hitelesíthet és központi telepítése virtuális Gépre kiterjedő kép megoldás sablonját részletes utasításokat vásárolja meg az Azure piactéren."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 675316b97e821a81ca4946d57a76d7bce978361b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Az útmutató megoldást sablon létrehozása az Azure piactéren
1. lépésben befejezése után [fióklétrehozás és a regisztrációs][link-acct-creation], azt interaktív, egy Azure-kompatibilis megoldás sablon létrehozásakor [műszaki Előfeltételek létrehozásához egy megoldás sablon](marketplace-publishing-solution-template-creation-prerequisites.md). Most végigvezetjük, a megoldás sablonok létrehozásának több virtuális gépek a lépéseket a [közzétételi Portáljára] [ link-pubportal] az Azure piactérről.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>A megoldás sablon ajánlat létrehozása a közzétételi portálon
Ugrás a [https://publish.windowsazure.com](http://publish.windowsazure.com). Amikor első alkalommal való bejelentkezés a [közzétételi Portáljára](https://publish.windowsazure.com/), használja a fiókot használja a vállalata értékesítő profil regisztrálták. Később a vállalat bármely alkalmazott is hozzáadhat a közzétételi portálon co-rendszergazdaként.

### <a name="1-select-solution-templates"></a>1. Válassza ki a "Solution templates"
  ![rajz][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Hozzon létre egy új megoldássablon
  ![rajz][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Indítsa el a topológiák
A megoldássablon az összes hozzá tartozó topológia „szülőeleme”. Egy ajánlat/megoldássablonban több topológiát is megadhat. Amikor egy ajánlatot a rendszer előkészítésre továbbít, az összes topológiájával együtt továbbítja. Adja meg az ajánlat az alábbi lépésekkel:     

* Az olyan topológiák létrehozását: "Topológia azonosítója" az általában a topológia a megoldás sablon neve. A topológia azonosítója az URL-címet használja a lent látható módon:

  Az Azure piactér: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure-portálon: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}
* Adjon hozzá egy új verziót.

### <a name="4-get-your-topology-versions-certified"></a>4. A hitelesített topológia verziók beolvasása
A topológia, hogy adott verziójának telepítéséhez az összes szükséges fájlokat tartalmazó zip-fájl feltöltése. A zip-fájl a következőket kell tartalmaznia:

* *mainTemplate.json* és *createUiDefinition.json* fájlt a gyökérkönyvtárban.
* Bármely kapcsolt sablonok és a parancsfájlok az összes szükséges.

  > [!TIP]
  > A fejlesztők a megoldás topológiák sablon létrehozásakor és rávenni a hitelesített, az üzleti munka közben marketing, és/vagy jogi részlegek a vállalat marketing és jogi-tartalma is működik.
  >
  >

## <a name="next-steps"></a>Következő lépések
Most, hogy a megoldás sablon létrehozása és a zip-fájl feltöltése, kérjük, kövesse az utasításokat a [piactér marketing content útmutató](marketplace-publishing-push-to-staging.md) átmeneti ajánlatot előtt. Piactér cikkek közzététele a teljes készletének megtekintéséhez keresse fel [első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md).

Is érdekelheti a kapcsolódó cikkekben:

* Virtuálisgép-rendszerképek: [kapcsolatos virtuálisgép-lemezképeket az Azure-ban](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Virtuálisgép-bővítmények: [Virtuálisgép-ügynök és Virtuálisgép-bővítmények áttekintése](https://msdn.microsoft.com/library/azure/dn832621.aspx) és [Azure Virtuálisgép-bővítmények és szolgáltatások](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Az Azure Resource Manager: [Azure Resource Manager sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md) és [egyszerű sablon példák](https://github.com/rjmax/ArmExamples)
* A tárfiók azelőtt gyorsítja fel: [tárolási fiók szabályozás figyelése](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) és [prémium szintű storage](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
