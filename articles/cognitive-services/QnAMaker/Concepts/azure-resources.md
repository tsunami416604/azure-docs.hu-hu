---
title: Azure-erőforrások – QnA Maker
description: A QnA Maker több Azure-forrást használ, amelyek mindegyike más célt szolgál. Ha tisztában van azzal, hogyan használják őket külön-külön, megtervezheti és kiválaszthatja a megfelelő tarifacsomagot, vagy megtudhatja, hogy mikor kell módosítania a tarifacsomagot. Ha tisztában van azzal, hogyan használják őket együttesen, megtalálhatja és kijavíthatja a problémákat, amikor azok előfordulnak.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 581029d2372f7a2ef704dcf02f266b66440aa246
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873905"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-erőforrások a QnA Maker számára

A QnA Maker több Azure-forrást használ, amelyek mindegyike más célt szolgál. Ha tisztában van azzal, hogyan használják őket külön-külön, megtervezheti és kiválaszthatja a megfelelő tarifacsomagot, vagy megtudhatja, hogy mikor kell módosítania a tarifacsomagot. Ha tisztában van azzal, hogyan használják őket _együttesen,_ megtalálhatja és kijavíthatja a problémákat, amikor azok előfordulnak.

## <a name="resource-planning"></a>Erőforrás-tervezés

Amikor először fejleszt egy QnA Maker tudásbázist, a prototípus fázisban gyakori, hogy egyetlen QnA Maker erőforrással rendelkezik mind a teszteléshez, mind az éles környezethez.

Amikor a projekt fejlesztési szakaszába lép, érdemes a következőket figyelembe venni:

* hány nyelvet fog tartani a tudásbázisrendszer
* hány régióra van szüksége ahhoz, hogy tudásbázisa elérhető legyen a
* hány dokumentumot tart a rendszer az egyes tartományokban

Tervezze meg, hogy egyetlen QnA Maker erőforrás rendelkezik az összes olyan tudásbázissal, amelynek ugyanaz a nyelve, ugyanaz a régiója és ugyanaz a tulajdonostartomány-kombinációja.

## <a name="pricing-tier-considerations"></a>A tarifacsomagra vonatkozó szempontok

Általában három paramétert kell figyelembe vennie:

* **A szolgáltatásból szükséges átviteli igény:**
    * Válassza ki a megfelelő [alkalmazáscsomagot](https://azure.microsoft.com/pricing/details/app-service/plans/) az alkalmazásszolgáltatásigényeinek megfelelően. Az alkalmazás fel- vagy [leskálázható.](https://docs.microsoft.com/azure/app-service/manage-scale-up)
    * Ez az Azure **Cognitive Search** termékváltozatának kiválasztását is befolyásolhatja, itt további [részleteket](https://docs.microsoft.com/azure/search/search-sku-tier)talál. Emellett szükség lehet a cognitive Search [kapacitás](../../../search/search-capacity-planning.md) replikák beállítására.

* **A tudásbázisok mérete és száma:** Válassza ki a forgatókönyvhöz megfelelő [Azure keresési termékváltozatot.](https://azure.microsoft.com/pricing/details/search/) Általában a szükséges tudásbázisok számát a különböző tárgyú tartományok száma alapján kell eldöntenie. Egyszer tárgy tartomány (egy nyelv) kell egy tudásbázis.

    Az N-1 tudásbázisok egy adott rétegben tehetik közzé, ahol N a rétegben engedélyezett maximális indexek. Ellenőrizze a maximális méretet és a rétegenként engedélyezett dokumentumok számát is.

    Ha például a réteg 15 engedélyezett indexszel rendelkezik, 14 tudásbázist tehet közzé (közzétett tudásbázisonként 1 indexet). A tizenötödik index a szerzői és tesztelési tudásbázisok összes éhezése.

* **Dokumentumok száma forrásként:** A QnA Maker felügyeleti szolgáltatás ingyenes termékváltozata a portálon és az API-kon keresztül kezelhető dokumentumok számát 3-ra korlátozza (egyenként 1 MB mérettel). A szabványos termékváltozat nem korlátozza a kezelhető dokumentumok számát. További részletek [itt](https://aka.ms/qnamaker-pricing).

Az alábbi táblázat néhány magas szintű útmutatást ad.

|                        | QnA Maker kezelése | App Service | Azure Cognitive Search | Korlátozások                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Kísérletezés        | Ingyenes Termékváltozat             | Ingyenes szint   | Ingyenes szint    | Legfeljebb 2 KB-k közzététele, 50 MB méret  |
| Fejlesztői/tesztelési környezet   | Standard termékváltozat         | Megosztott      | Basic        | Legfeljebb 14 Gb-os közzététel, 2 GB méret    |
| Termelési környezet | Standard termékváltozat         | Basic       | Standard     | Közzététel Legfeljebb 49 KB-ig, 25 GB-os méret |

## <a name="recommended-settings"></a>Ajánlott beállítások

|QPS cél | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 példány   | S1, 1 példány    |
| 50         | S3, 10 példány       | S1, 12 példány         |
| 80         | S3, 10 példány      |  S3, 12 példány  |
| 100         | P3V2, 10 példány  | S3, 12 példány, 3 partíció   |
| 200 hogy 250         | P3V2, 20 példány | S3, 12 példány, 3 partíció    |

## <a name="when-to-change-a-pricing-tier"></a>Mikor kell módosítani egy tarifacsomagot?

|Frissítés|Ok|
|--|--|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker felügyeleti termékváltozat|Több QnA-párt vagy dokumentumforrást szeretne a tudásbázisában.|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service Termékváltozat és ellenőrizze a Cognitive Search réteget, és [hozzon létre kognitív keresési replikákat](../../../search/search-capacity-planning.md)|A tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazásból, például egy csevegőrobotot.|
|[Frissítés](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search szolgáltatás|Azt tervezi, hogy sok tudásbázist.|

Az [App Service azure-portálon való frissítésével](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)értesülhet a legújabb futásidejű frissítésekről.

## <a name="resource-naming-considerations"></a>Erőforrás-elnevezési szempontok

A QnA Maker erőforrás neve, `qna-westus-f0-b`például a , a többi erőforrás elnevezésére is használható.

Az Azure Portal létrehozása ablak lehetővé teszi, hogy hozzon létre egy QnA Maker erőforrást, és válassza ki a tarifacsomagok a többi erőforráshoz.

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról a QnA Maker erőforrás-létrehozása](../media/concept-azure-resource/create-blade.png)

Az erőforrások létrehozása után ugyanaz a nevük, kivéve a választható Application Insights erőforrást, amely a karaktereket a névre postzi.

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portal erőforráslistájáról](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Új erőforráscsoport létrehozása QnA Maker erőforrás létrehozásakor. Ez lehetővé teszi a QnA Maker erőforráshoz társított összes erőforrás megtekintését erőforráscsoport onként.

> [!TIP]
> Az erőforrás vagy az erőforráscsoport nevén belüli tarifacsomagok megjelöléséhez használjon elnevezési konvenciót. Ha hibaüzenetet kap egy új tudásbázis létrehozásából vagy új dokumentumok hozzáadásából, a Cognitive Search tarifacsomag-korlát ja a leggyakoribb probléma.

## <a name="resource-purposes"></a>Erőforrás céljai

A QnA Makerrel létrehozott minden Egyes Azure-erőforrásnak meghatározott célja van:

* QnA Maker erőforrás
* Kognitív keresés erőforrás
* App Service
* Alkalmazáscsomag-szolgáltatás
* Application Insights szolgáltatás


### <a name="cognitive-search-resource"></a>Kognitív keresés erőforrás

A [Cognitive Search](../../../search/index.yml) erőforrás a következőkre szolgál:

* A QnA párok tárolása
* Adja meg a QnA párok kezdeti rangsorát (#1) futásidőben

#### <a name="index-usage"></a>Index használat

Az erőforrás egy indexet tart meg, hogy tesztindexként működjön, és a fennmaradó indexek egy-egy közzétett tudásbázissal korrelálnak.

Egy 15 index tárolására szolgáló erőforrás 14 közzétett tudásbázist fog tárolni, és egy indexet használ az összes tudásbázis teszteléséhez. Ezt a tesztindexet a tudásbázis particionálja, így az interaktív tesztablakon keresztül végzett lekérdezés a tesztindexet fogja használni, de csak az adott tudásbázishoz társított partícióról adja vissza az eredményeket.

#### <a name="language-usage"></a>Nyelvhasználat

A QnA Maker erőforrásban létrehozott első tudásbázis a Cognitive Search erőforrás és az összes indexe _egyetlen_ nyelvkészletének meghatározására szolgál. A QnA Maker szolgáltatáshoz csak _egy nyelv állítható be._

### <a name="qna-maker-resource"></a>QnA Maker erőforrás

A QnA Maker erőforrás hozzáférést biztosít a szerzői és közzétételi API-khoz, valamint a QnA-párok természetes nyelvi feldolgozási (NLP) alapú második rangsorolási rétegéhez (ranker #2) futásidőben.

A második rangsorolás intelligens szűrőket alkalmaz, amelyek metaadatokat és nyomon követési utasításokat tartalmazhatnak.

#### <a name="qna-maker-resource-configuration-settings"></a>A QnA Maker erőforrás-konfigurációs beállításai

Amikor új tudásbázist hoz létre a [QnA Maker portálon,](https://qnamaker.ai)a **Nyelvi** beállítás az egyetlen olyan beállítás, amely erőforrásszinten van alkalmazva. Az erőforrás első tudásalapjának létrehozásakor válassza ki a nyelvet.

### <a name="app-service-and-app-service-plan"></a>App-szolgáltatás és alkalmazásszolgáltatási csomag

Az [alkalmazásszolgáltatást](../../../app-service/index.yml) az ügyfélalkalmazás használja a közzétett tudásbázisok eléréséhez a futásidejű végponton keresztül.

A közzétett tudásbázis lekérdezéséhez az összes közzétett tudásbázis ugyanazt az URL-végpontot használja, de adja meg a **tudásbázis-azonosítót** az útvonalon belül.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Az Application Insights](../../../azure-monitor/app/app-insights-overview.md) csevegési naplók és telemetriai adatok gyűjtésére szolgál. Tekintse át a szolgáltatással kapcsolatos információkat a gyakori [Kusto-lekérdezésekből.](../how-to/get-analytics-knowledge-base.md)

## <a name="share-services-with-qna-maker"></a>Szolgáltatások megosztása a QnA Makerrel

A QnA Maker számos Azure-erőforrást hoz létre. A felügyelet csökkentése és a költségmegosztás előnyeinek kihasználása érdekében az alábbi táblázat segítségével ismerje meg, mit oszthat meg és mit nem:

|Szolgáltatás|Megosztás|Ok|
|--|--|--|
|Cognitive Services|X|Nem lehetséges a tervezés|
|App Service-csomag|✔|Rögzített lemezterület egy App Service-csomag számára. Ha más alkalmazások, amelyek ugyanazt az App Service-csomagot használják, jelentős lemezterületet használnak, a QnAMaker App Service-példány problémákba ütközik.|
|App Service|X|Nem lehetséges a tervezés|
|Application Insights|✔|Megosztható|
|Keresési szolgáltatás|✔|1. `testkb` a QnAMaker szolgáltatás fenntartott neve; mások nem használhatják.<br>2. A Név `synonym-map` szerint a Szinonimatérkép a QnAMaker szolgáltatás számára van fenntartva.<br>3. A közzétett tudásbázisok számát a Keresési szolgáltatásszint korlátozza. Ha vannak ingyenes indexek állnak rendelkezésre, más szolgáltatások is használhatják őket.|

### <a name="using-a-single-cognitive-search-service"></a>Egyetlen Cognitive Search szolgáltatás használata

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, akkor létrejön egy keresési szolgáltatás, amely a QnA Maker szolgáltatáshoz kapcsolódik. Ezen erőforrások létrehozása után frissítheti az App Service-beállítást egy korábban meglévő keresési szolgáltatás használatához, és eltávolíthatja az imént létrehozottat.

Ismerje [meg, hogyan konfigurálhatja a](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker-t egy másik Cognitive Service-erőforrás használatára, mint a QnA Maker erőforrás-létrehozási folyamat részeként létrehozott erőforrás.

## <a name="management-service-region"></a>Felügyeleti szolgáltatási régió

A QnA Maker felügyeleti szolgáltatása csak a QnA Maker portálhoz és a kezdeti adatfeldolgozáshoz használatos. Ez a szolgáltatás csak az **USA nyugati régiójában** érhető el. Ebben az USA nyugati közép-amerikai szolgáltatásában nem tárolunk ügyféladatokat.

## <a name="keys-in-qna-maker"></a>Kulcsok a QnA Makerben

A QnA Maker szolgáltatás kétféle kulcs: **szerzői kulcsok** és **lekérdezési végpont kulcsok** az app szolgáltatásban üzemeltetett futásidejű használt.

Ha az **előfizetési kulcsot**keresi, [a terminológia megváltozott.](#subscription-keys)

Használja ezeket a kulcsokat, ha api-kon keresztül kér a szolgáltatáshoz.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

|Név|Hely|Cél|
|--|--|--|
|Szerzői kulcs|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Ezek a kulcsok a [QnA Maker felügyeleti szolgáltatás API-inak](https://go.microsoft.com/fwlink/?linkid=2092179)elérésére szolgálnak. Ezek az API-k lehetővé teszik a kérdések és válaszok szerkesztését a tudásbázisban, és közzéteheti tudásbázisát. Ezek a kulcsok akkor jönnek létre, amikor új QnA Maker szolgáltatást hoz létre.<br><br>Keresse meg ezeket a kulcsokat a **Cognitive Services** erőforrás a **Kulcsok** lapon.|
|Végpont lekérdezési kulcsa|[QnA Maker portál](https://www.qnamaker.ai)|Ezek a kulcsok a közzétett tudásbázis-végpont lekérdezésére szolgálnak, hogy választ kapjanak egy felhasználói kérdésre. Ezt a lekérdezési végpontot általában a csevegőrobotban vagy a QnA Maker szolgáltatáshoz csatlakozó ügyfélalkalmazás-kódban használja. Ezek a kulcsok a QnA Maker tudásbázisának közzétételekor jönnek létre.<br><br>Keresse meg ezeket a kulcsokat a **Szolgáltatás beállításai** lapon. Keresse meg ezt az oldalt a felhasználó menüjéből a legördülő menü jobb felső részén.|

### <a name="subscription-keys"></a>Előfizetői azonosítók

A szerzői és lekérdezési végpontkulcs kifejezések helyesbítő kifejezések. Az előző időszak **az előfizetési kulcs**volt. Ha az előfizetési kulcsokra vonatkozó egyéb dokumentációt lát, ezek egyenértékűek a (futásidőben használt) végpontkulcsok szerzői és lekérdezésével.

Tudnia kell, hogy mi a kulcs elérése, tudásbázis-kezelés vagy tudásbázis lekérdezése, tudnia kell, hogy melyik kulcsot kell megtalálnia.

## <a name="next-steps"></a>További lépések

* Ismerje meg a QnA Maker [tudásbázisát](knowledge-base.md)
* A [tudásbázis életciklusának megismerése](development-lifecycle-knowledge-base.md)
* A szolgáltatás- és tudásbázis [korlátainak áttekintése](../limits.md)

