---
title: Azure-erőforrások – QnA Maker
description: QnA Maker számos Azure-forrást használ, amelyek mindegyike más célra szolgál. A használatuk módjának megismerése lehetővé teszi, hogy megtervezze és kiválassza a megfelelő árképzési szintet, vagy tudja, mikor kell módosítania az árképzési szintet. Annak megismerése, hogy a rendszer hogyan használja a kombinációkat, így megkeresheti és elháríthatja a problémákat, amikor azok bekövetkeznek.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 2b3a06122ce6123cd8edcedf5dfbf38c3c12218a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843498"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-erőforrások a QnA Makerhoz

QnA Maker számos Azure-forrást használ, amelyek mindegyike más célra szolgál. A használatuk módjának megismerése lehetővé teszi, hogy megtervezze és kiválassza a megfelelő árképzési szintet, vagy tudja, mikor kell módosítania az árképzési szintet. Annak megismerése, hogy a rendszer hogyan használja _a kombinációkat_ , így megkeresheti és elháríthatja a problémákat, amikor azok bekövetkeznek.

## <a name="resource-planning"></a>Erőforrás-tervezés

Amikor először fejleszt egy QnA Maker tudásbázist, a prototípus fázisban gyakori, hogy egyetlen QnA Maker erőforrással rendelkezzen mind a teszteléshez, mind a gyártáshoz.

A projekt fejlesztési szakaszába való áttéréskor érdemes megfontolnia a következőket:

* a Tudásbázis-rendszer által megtartott nyelvek számának száma
* hány régióban kell elérhetőnek lennie a Tudásbázisban/-ban
* a rendszer által megtartott összes tartomány dokumentumai

Tervezze meg, hogy egyetlen QnA Maker erőforrással rendelkezzen minden olyan tudásbázissal, amelynek a nyelve, ugyanazon a régiója és a tárgy tartományának kombinációja.

## <a name="pricing-tier-considerations"></a>Árképzési szintek szempontjai

Általában három paramétert kell figyelembe vennie:

* **A szolgáltatás által igényelt átviteli sebesség**: válassza ki a [megfelelő alkalmazáscsomag az App](https://azure.microsoft.com/pricing/details/app-service/plans/) Service-hez az igényei alapján. Az alkalmazás vertikális fel-és [leskálázásra](https://docs.microsoft.com/azure/app-service/manage-scale-up) is használható. Ez az Azure Cognitive Search SKU kiválasztását is befolyásolhatja, további részletek [itt](https://docs.microsoft.com/azure/search/search-sku-tier)találhatók.

* A **Tudásbázis mérete és száma**: válassza ki a megfelelő [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) -t a forgatókönyvhöz. Általában a különböző tulajdonosi tartományok száma alapján döntheti el, hogy hány tudásbázisra van szüksége. Ha a tárgy tartománya (egyetlen nyelv esetében) egy Tudásbázisban kell lennie.

    Az N-1 tudásbázist egy adott rétegben teheti közzé, ahol N a rétegben engedélyezett maximális indexek. Tekintse meg a maximális méretet és a másodpercenként engedélyezett dokumentumok számát is.

    Ha például a réteg 15 engedélyezett indextel rendelkezik, a közzétett Tudásbázisban 14 tudásbázist (1 indexet) tehet közzé. A tizenötödik index a szerzői műveletek és a tesztelés összes tudásbázisához használatos.

* **Dokumentumok száma forrásként**: a QnA Maker felügyeleti szolgáltatás ingyenes SKU-jának a Portálon és az API-kon keresztül kezelhető dokumentumok számát korlátozza (egyenként 1 MB méretűre). A standard SKU nem korlátozza a felügyelhető dokumentumok számát. További részletek [itt](https://aka.ms/qnamaker-pricing)találhatók.

A következő táblázat néhány magas szintű útmutatást nyújt.

|                        | QnA Maker-kezelés | App Service | Azure Cognitive Search | Korlátozások                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Kísérletezés        | Ingyenes SKU             | Ingyenes szint   | Ingyenes szint    | Legfeljebb 2 Tudásbázis közzététele, 50 MB méretű  |
| Fejlesztési/tesztelési környezet   | Standard termékváltozat         | Közös      | Basic        | Legfeljebb 14 Tudásbázis közzététele, 2 GB méretű méret    |
| Éles környezet | Standard termékváltozat         | Basic       | Standard     | Közzététel akár 49 Tudásbázis, 25 GB méretű |

## <a name="when-to-change-a-pricing-tier"></a>Mikor kell módosítani a díjszabási szintet

|Frissítés|Ok|
|--|--|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker felügyeleti SKU|Szeretne több QnA-készletet vagy dokumentumtípust használni a Tudásbázisban.|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU|A Tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazástól, például egy csevegési robottól.|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search szolgáltatás|Számos tudásbázist tervez.|

Szerezze be a legújabb futtatókörnyezeti frissítéseket a [app Service frissítésével a Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Erőforrás-elnevezési megfontolások

A QnA Maker erőforrás neve, például `qna-westus-f0-b`, a többi erőforrás elnevezésére is használatos.

A Azure Portal létrehozási ablakban létrehozhat egy QnA Maker-erőforrást, és kiválaszthatja a többi erőforrás díjszabási szintjeit.

> [!div class="mx-imgBorder"]
> ![képernyőkép a Azure Portal QnA Maker erőforrás-létrehozásról](../media/concept-azure-resource/create-blade.png)

Az erőforrások létrehozása után ugyanaz a neve, kivéve a nem kötelező Application Insights erőforrást, amely a postpends karaktert adja meg.

> [!div class="mx-imgBorder"]
> ![képernyőkép a Azure Portal-erőforrások listájáról](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Hozzon létre egy új erőforráscsoportot QnA Maker erőforrás létrehozásakor. Ez lehetővé teszi a QnA Maker erőforráshoz társított összes erőforrás megtekintését az erőforráscsoport keresésekor.

> [!TIP]
> Az erőforrás vagy az erőforráscsoport neve alapján megadhatja az árképzési szinteket a névadási konvenció alapján. Ha új Tudásbázis létrehozásával vagy új dokumentumok hozzáadásával kapcsolatos hibákat kap, akkor a Cognitive Search díjszabási szint korlátja gyakori probléma.

## <a name="resource-purposes"></a>Erőforrás-felhasználási célok

A QnA Makerrel létrehozott minden egyes Azure-erőforrásnak konkrét célja van:

* Erőforrás QnA Maker
* Erőforrás Cognitive Search
* App Service
* App Plan szolgáltatás
* Application Insights szolgáltatás


### <a name="cognitive-search-resource"></a>Erőforrás Cognitive Search

A [Cognitive Search](../../../search/index.yml) erőforrás a következőket használja:

* A QnA-készletek tárolása
* A QnA-készletek kezdeti rangsorának (ranker #1) megadása futásidőben

#### <a name="index-usage"></a>Indexelés használata

Az erőforrás megtartja az egyik indexet, hogy tesztelje az indexet, és a fennmaradó indexek egy közzétett tudásbázishoz tartozzanak.

A 15 indexek tárolására szolgáló erőforrás 14 közzétett tudásbázist tart fenn, és az összes Tudásbázis teszteléséhez egy indexet kell használni. Ezt a tesztelési indexet a Tudásbázis particionálja, így az interaktív tesztelési panelt használó lekérdezések a tesztelési indexet fogják használni, de csak az adott tudásbázishoz társított partíció eredményeit adják vissza.

#### <a name="language-usage"></a>Nyelvi használat

A QnA Maker erőforrásban létrehozott első Tudásbázis a Cognitive Search erőforráshoz és annak összes indexéhez beállított _egységes_ nyelv meghatározására szolgál. Egy QnA Maker szolgáltatáshoz csak _egy nyelvi beállítás_ tartozhat.

### <a name="qna-maker-resource"></a>Erőforrás QnA Maker

A QnA Maker erőforrás hozzáférést biztosít a szerzői és közzétételi API-khoz, valamint a természetes nyelvi feldolgozó (NLP) alapú második rangsorolási réteghez (ranker #2) a QnA-készletekhez futásidőben.

A második rangsor olyan intelligens szűrőket alkalmaz, amelyek metaadatokat és követő utasításokat is tartalmazhatnak.

### <a name="app-service-and-app-service-plan"></a>App Service-és app Service-csomag

Az [ügyfélalkalmazás a közzétett](../../../app-service/index.yml) tudásbázisokat a futásidejű végponton keresztül éri el.

A közzétett Tudásbázis lekérdezéséhez az összes közzétett Tudásbázis ugyanazt az URL-végpontot használja, de az útvonalon belül adja meg a **TUDÁSBÁZIS azonosítóját** .

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

A [Application Insights](../../../azure-monitor/app/app-insights-overview.md) a csevegési naplók és a telemetria összegyűjtésére szolgál. Tekintse át a szolgáltatással kapcsolatos általános [Kusto-lekérdezéseket](../how-to/get-analytics-knowledge-base.md) .

## <a name="share-services-with-qna-maker"></a>Szolgáltatások megosztása QnA Maker

QnA Maker több Azure-erőforrást hoz létre. A felügyelet és a költségmegosztás előnyeinek csökkentése érdekében az alábbi táblázat segítségével megismerheti, hogy mit tehet és nem oszthat meg:

|Szolgáltatás|Megosztás|Ok|
|--|--|--|
|Cognitive Services|X|Nem lehetséges a kialakítás|
|App Service-csomag|✔|App Service csomag számára lefoglalt rögzített lemezterület. Ha az azonos App Service-csomaggal rendelkező más alkalmazások jelentős lemezterületet használnak, akkor a QnAMaker App Service példánya problémákba ütközik.|
|App Service|X|Nem lehetséges a kialakítás|
|Application Insights|✔|Megosztható|
|Keresési szolgáltatás|✔|1. `testkb` a QnAMaker szolgáltatás számára fenntartott név; mások nem használhatják.<br>2. a QnAMaker szolgáltatás számára fenntartott `synonym-map` név szerinti szinonimák leképezése.<br>3. a közzétett tudásbázisok számát a Search szolgáltatási szintje korlátozza. Ha ingyenes indexek állnak rendelkezésre, más szolgáltatások is használhatják őket.|

### <a name="using-a-single-cognitive-search-service"></a>Egyetlen Cognitive Search szolgáltatás használata

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, akkor létrejön egy keresési szolgáltatás, amely a QnA Maker szolgáltatáshoz van csatolva. Az erőforrások létrehozása után frissítheti az App Service beállítást egy korábban meglévő keresési szolgáltatás használatára, és eltávolíthatja az imént létrehozott szolgáltatást.

Megtudhatja, [hogyan konfigurálhatja](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) a QnA Makert úgy, hogy az QnA Maker erőforrás-létrehozási folyamat részeként létrehozott egy másik kognitív szolgáltatási erőforrást használjon.

## <a name="management-service-region"></a>Felügyeleti szolgáltatási régió

QnA Maker felügyeleti szolgáltatása csak a QnA Maker portál és a kezdeti adatfeldolgozás esetében használatos. Ez a szolgáltatás csak az **USA nyugati** régiójában érhető el. Ebben a Nyugat-amerikai szolgáltatásban nem tárolunk ügyféladatokat.

## <a name="keys-in-qna-maker"></a>Kulcsok a QnA Makerban

A QnA Maker szolgáltatás két típusú kulccsal foglalkozik: az App Service-ben üzemeltetett futtatókörnyezettel használt kulcsok és **lekérdezési végponti kulcsok** **létrehozásával** .

Ha az **előfizetési kulcsot**keresi, [a terminológia módosult](#subscription-keys).

Ezeket a kulcsokat akkor használja, ha az API-kon keresztül kéri a szolgáltatást a szolgáltatásnak.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

|Név|Földrajzi egység|Rendeltetés|
|--|--|--|
|Szerzői kulcs|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Ezek a kulcsok a [QnA Maker Management szolgáltatás API-jai](https://go.microsoft.com/fwlink/?linkid=2092179)elérésére szolgálnak. Ezek az API-k lehetővé teszik a tudásbázisban található kérdések és válaszok szerkesztését, valamint a Tudásbázis közzétételét. Ezek a kulcsok akkor jönnek létre, amikor új QnA Maker szolgáltatást hoz létre.<br><br>Keresse meg ezeket a kulcsokat a **kulcsok** oldalon található **Cognitive Services** erőforrásban.|
|Lekérdezési végpont kulcsa|[QnA Maker portál](https://www.qnamaker.ai)|Ezekkel a kulcsokkal lekérdezheti a közzétett Tudásbázis-végpontot, hogy választ kapjon a felhasználó kérdéseire. Ezt a lekérdezési végpontot általában a csevegési robotjában vagy a QnA Maker szolgáltatáshoz csatlakozó ügyfélalkalmazás kódjában használja. Ezek a kulcsok akkor jönnek létre, amikor közzéteszi a QnA Maker tudásbázist.<br><br>Keresse meg ezeket a kulcsokat a **szolgáltatás beállításai** lapon. Keresse meg ezt a lapot a legördülő menüben található oldal jobb felső sarkában található felhasználó menüjében.|

### <a name="subscription-keys"></a>Előfizetési kulcsok

A létrehozási és a lekérdezési végpont kulcsa a helyes feltételek. Az előző kifejezés az **előfizetés kulcsa**. Ha az előfizetési kulcsokra hivatkozó egyéb dokumentáció jelenik meg, ezek egyenértékűek a létrehozási és lekérdezési végponti kulcsokkal (a futtatókörnyezetben használt).

Ismernie kell a kulcs elérését, a Tudásbázis felügyeletét vagy a Tudásbázis lekérdezését, hogy megtudja, melyik kulcsot kell megkeresnie.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a QnA Maker [Tudásbázisról](knowledge-base.md)
* A [Tudásbázis életciklusának](development-lifecycle-knowledge-base.md) megismerése
* A szolgáltatás és az Tudásbázis [korlátainak](../limits.md) áttekintése

