---
title: Felügyeleti megoldás létrehozása az Azure-ban | Microsoft dokumentumok
description: A felügyeleti megoldások olyan azure-beli csomagkezelési forgatókönyveket tartalmaznak, amelyeket az ügyfelek hozzáadhatnak a Log Analytics-munkaterületükhöz.  Ez a cikk részletesen ismerteti, hogyan hozhat létre felügyeleti megoldásokat a saját környezetében, vagy elérhetővé kell tenni az ügyfelek számára.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663215"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Felügyeleti megoldás tervezése és létrehozása az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció felügyeleti megoldások létrehozásához az Azure-ban, amelyek jelenleg előzetes verzióban. Az alábbiakban ismertetett sémák változhatnak.

[A felügyeleti megoldások]( solutions.md) olyan csomagkezelési forgatókönyveket biztosítanak, amelyeket az ügyfelek hozzáadhatnak a Log Analytics-munkaterületükhöz.  Ez a cikk a leggyakoribb követelményeknek megfelelő felügyeleti megoldás tervezésének és létrehozásának alapvető folyamatát mutatja be.  Ha még nem új az épület felügyeleti megoldások, akkor használhatja ezt a folyamatot, mint a kiindulási pont, majd kihasználja a fogalmak bonyolultabb megoldások, mint a követelmények fejlődnek.

## <a name="what-is-a-management-solution"></a>Mi a felügyeleti megoldás?

A felügyeleti megoldások olyan Azure-erőforrásokat tartalmaznak, amelyek együttműködnek egy adott felügyeleti forgatókönyv elérése érdekében.  [Erőforrás-kezelési sablonokként vannak megvalósítva,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) amelyek a megoldás telepítésekor a tartalmazott erőforrások telepítésének és konfigurálásának részleteit tartalmazzák.

Az alapvető stratégia a felügyeleti megoldás indítása az Azure-környezetben az egyes összetevők létrehozásával.  Ha már megfelelően működik a funkció, elkezdheti a [kezelési megoldásfájlba]( solutions-solution-file.md)csomagolását. 


## <a name="design-your-solution"></a>Tervezze meg a megoldást
A felügyeleti megoldás leggyakoribb mintája az alábbi ábrán látható.  A minta különböző összetevőit az alábbiakban tárgyaljuk.

![Felügyeleti megoldás – áttekintés](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Adatforrások
A megoldás tervezésének első lépése a Log Analytics-tárházból szükséges adatok meghatározása.  Ezeket az adatokat [adatforrás](../../azure-monitor/platform/agent-data-sources.md) vagy [más megoldás]( solutions.md)gyűjtheti, vagy a megoldásnak biztosítania kell a gyűjtési folyamatot.

A [Log Analytics adatforrásaiban](../../azure-monitor/platform/agent-data-sources.md)ismertetett adatok számos módon gyűjthetők.  Ez magában foglalja a Windows eseménynaplóban szereplő vagy a Syslog által létrehozott eseményeket a Windows és Linux ügyfelek teljesítményszámlálói mellett.  Az Azure Monitor által gyűjtött Azure-erőforrásokból is gyűjthet adatokat.  

Ha olyan adatokra van szüksége, amelyek nem érhetők el a rendelkezésre álló adatforrások bármelyikén keresztül, akkor használhatja a [HTTP-adatgyűjtő API-t,](../../azure-monitor/platform/data-collector-api.md) amely lehetővé teszi, hogy adatokat írjon a Log Analytics-tárházba bármely olyan ügyfélről, amely meghívhat egy REST API-t.  A felügyeleti megoldásban az egyéni adatgyűjtés leggyakoribb módja egy runbook létrehozása az [Azure Automationben,](../../automation/automation-runbook-types.md) amely összegyűjti a szükséges adatokat az Azure-ból vagy külső erőforrásokból, és az adatgyűjtő API-t használja a tárházba való íráshoz.  

### <a name="log-searches"></a>Keresésnaplózás
[A naplókeresések](../../azure-monitor/log-query/log-query-overview.md) a Log Analytics-tárházban lévő adatok kinyerésére és elemzésére szolgálnak.  Ezeket a nézetek és riasztások is használják, amellett, hogy lehetővé teszi a felhasználó számára, hogy ad hoc elemzést az adatok a tárházban.  

Meg kell határoznia azokat a lekérdezéseket, amelyek ről úgy gondolja, hogy hasznosak lesznek a felhasználó számára, még akkor is, ha azokat nem használja egyetlen nézet vagy riasztás sem.  Ezek mentett keresésekként lesznek elérhetők a portálon, és az egyéni nézetben a [Lekérdezések listájában](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) is szerepelhetnek.

### <a name="alerts"></a>Riasztások
[A Log Analytics riasztásai](../../azure-monitor/platform/alerts-overview.md) azonosítják a problémákat a tárházban lévő adatokra vonatkozó [naplókeresésekkel.](#log-searches)  Vagy értesítik a felhasználót, vagy automatikusan futtatnak egy műveletet válaszként. Azonosítsa az alkalmazás különböző riasztási feltételeit, és a megoldásfájlban adja meg a megfelelő riasztási szabályokat.

Ha a probléma potenciálisan kijavítható egy automatikus folyamat, majd általában hozzon létre egy runbook az Azure Automation ben a javítás végrehajtásához.  A legtöbb Azure-szolgáltatások kezelhető [parancsmagokkal,](/powershell/azure/overview) amelyek a runbook lenne kihasználni az ilyen funkciók végrehajtásához.

Ha a megoldás külső funkciókat igényel egy riasztásra adott válaszként, akkor használhatja a [webhook-választ.](../../azure-monitor/platform/alerts-metric.md)  Ez lehetővé teszi, hogy hívja a külső webszolgáltatás küldött adatokat a riasztásból.

### <a name="views"></a>Nézetek
A Log Analytics nézetek a Log Analytics-tárházból származó adatok megjelenítésére szolgálnak.  Minden megoldás általában egyetlen nézetet tartalmaz, amely a felhasználó fő irányítópultján megjelenő [csempével](../../azure-monitor/platform/view-designer-tiles.md) jelenik meg.  A nézet tetszőleges számú [vizualizációs részt](../../azure-monitor/platform/view-designer-parts.md) tartalmazhat, hogy az összegyűjtött adatok különböző vizualizációit biztosítsa a felhasználónak.

[Egyéni nézeteket hozhat létre a Nézettervező vel,](../../azure-monitor/platform/view-designer.md) amelyet később exportálhat a megoldásfájlba való felvételhez.  


## <a name="create-solution-file"></a>Megoldásfájl létrehozása
Miután konfigurálta és tesztelte a megoldás részét alkotó összetevőket, [létrehozhatja a megoldásfájlt.]( solutions-solution-file.md)  A megoldás-összetevőket egy [Erőforrás-kezelő sablonban](../../azure-resource-manager/templates/template-syntax.md) fogja megvalósítani, amely a fájl többi erőforrásával kapcsolatban álló [megoldás-erőforrást]( solutions-solution-file.md#solution-resource) tartalmaz.  


## <a name="test-your-solution"></a>Tesztelje a megoldást
A megoldás fejlesztése közben telepítenie és tesztelnie kell azt a munkaterületen.  Ezt az [Erőforrás-kezelő sablonjainak teszteléséhez és telepítéséhez](../../azure-resource-manager/templates/deploy-powershell.md)rendelkezésre álló módszerek bármelyikével teheti meg.

## <a name="publish-your-solution"></a>A megoldás közzététele
Miután kitöltötte és tesztelte a megoldást, elérhetővé teheti azt az ügyfelek számára a következő forrásokon keresztül.

- **Azure gyorsindítási sablonok**.  [Az Azure Quickstart-sablonok](https://azure.microsoft.com/resources/templates/) a Közösség által a GitHubon keresztül hozzájárult Erőforrás-kezelő sablonok készlete.  A megoldást elérhetővé teheti a [hozzájárulási útmutatóban](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)található információk követésével.
- **Az Azure Piactér**.  Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) lehetővé teszi, hogy a megoldás terjesztésére és értékesítésére más fejlesztők, független szoftverszállítók és informatikai szakemberek számára.  Megtudhatja, hogyan teheti közzé a megoldást az Azure Piactéren [a Hogyan tehet közzé és kezelhet egy ajánlatot az Azure Marketplace-en.](../../marketplace/marketplace-publishers-guide.md)



## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [hozhat létre megoldásfájlt]( solutions-solution-file.md) a felügyeleti megoldáshoz.
* Ismerje meg az [Azure Resource Manager-sablonok szerzői részleteit.](../../azure-resource-manager/templates/template-syntax.md)
* Az [Azure gyorsindítási sablonjaiban](https://azure.microsoft.com/documentation/templates) különböző Erőforrás-kezelősablonok mintáit keresheti.
