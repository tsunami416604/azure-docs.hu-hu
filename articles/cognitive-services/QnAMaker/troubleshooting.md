---
title: Hibaelhárítás – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e98fd089ce8ec1285232840a40bb42ac5b81446
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795445"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker hibaelhárítása

A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>A QnAMaker szolgáltatás végpontjának beszerzése

A QnAMaker szolgáltatás végpontja hibakeresési célokra használható, amikor kapcsolatba lép a QnAMaker támogatási szolgálatával vagy a UserVoice. A végpont egy URL-cím ebben az űrlapban: https://your-resource-name.azurewebsites.net.
    
1. Lépjen a QnAMaker szolgáltatásra (erőforráscsoport) a [Azure Portal](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoport Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a QnA Maker erőforráshoz társított App Service. A nevek általában azonosak.

     ![QnAMaker kiválasztása App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. A végpont URL-címe az Áttekintés szakaszban érhető el

    ![QnAMaker végpont](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>A QnA Maker portál Súgó robotjának használata

A **QnA Maker segítséget nyújt** a QnA Maker portálon a segítségére. A Súgó robot minden weblapon elérhető. A robot a QnA Maker segítségével ad választ, és biztosítja a [ C# robot-keretrendszer kódjának projektjét](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) a robot számára, így gyorsan megkezdheti a saját válaszának használatát. 

![! [QnA Maker egy * * Súgó * * robotot biztosít a QnA Maker portálon, hogy segítsen.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>A Tudásbázis kezelése

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Véletlenül töröltem a QnA Maker egy részét, mit tegyek? 

Ne törölje a létrehozott Azure-szolgáltatásokat a QnA Maker erőforrással, például a Search vagy a Web App szolgáltatással együtt. Ezek a QnA Maker működéséhez szükségesek, ha törli az egyiket, QnA Maker nem fog megfelelően működni.

Az összes törlés végleges, beleértve a kérdés-és a választ, a fájlokat, az URL-eket, az egyéni kérdéseket és válaszokat, a tudásbázisokat vagy az Azure-erőforrásokat. A Tudásbázis bármely részének törlése előtt győződjön meg arról, hogy a tudásbázist a **Beállítások** lapról exportálja. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Miért nem/file a kérdés-válasz párokat az URL-ek (k)?

Lehetséges, hogy QnA Maker nem tud automatikusan kinyerni néhány kérdés-válasz (QnA) tartalmat az érvényes GYIK URL-címekről. Ilyen esetekben beillesztheti a QnA tartalmát egy. txt fájlba, és megtudhatja, hogy az eszköz képes-e a betöltésre. Másik lehetőségként a [QnA Maker portálon](https://qnamaker.ai)is hozzáadhat tartalmat a tudásbázishoz.

### <a name="how-large-a-knowledge-base-can-i-create"></a>Milyen méretű lehet a létrehozott tudásbázis?

A Tudásbázis mérete az QnA Maker szolgáltatás létrehozásakor választott Azure Search SKU-ból függ. További [részletekért olvassa el](./Tutorials/choosing-capacity-qnamaker-deployment.md) a következőt:.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Miért nem látok semmit a legördülő listából, amikor megpróbálok új tudásbázist létrehozni?

Még nem hozott létre QnA Maker szolgáltatásokat az Azure-ban. Ennek [megismeréséhez olvassa el](./How-To/set-up-qnamaker-service-azure.md) a következőt:.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Hogyan megoszthat tudásbázist másokkal?

A megosztás a QnA Maker szolgáltatás szintjén működik, azaz a szolgáltatásban található összes Tudásbázis meg lesz osztva. [Itt](./How-To/collaborate-knowledge-base.md) olvashat arról, hogyan lehet együttműködni a Tudásbázisban.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Megoszthat egy tudásbázist olyan közreműködővel, amely nem ugyanabban az HRE-bérlőben található, a Tudásbázis módosításához? 

A megosztás az Azure szerepköralapú hozzáférés-vezérlés (RBAC) alapján történik. Ha egy másik felhasználóval is megoszthat _bármilyen_ erőforrást az Azure-ban, megoszthatja QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Ha van App Service csomag 5 QnAMaker tudásbázissal. Az olvasási/írási jogosultság 5 különböző felhasználóhoz rendelhető, így mindegyikhez csak 1 QnAMaker-alapú Tudásbázis férhet hozzá?

Megoszthat egy teljes QnAMaker szolgáltatást, nem pedig egyedi tudásbázisokat.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hogyan változtathatom meg az alapértelmezett üzenetet, ha nem találok megfelelő egyezést?

Az alapértelmezett üzenet az App Service beállításainak részét képezi.
- Nyissa meg az App Service-erőforrást a Azure Portal

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **Beállítások** lehetőségre.

![qnamaker appservice beállításai](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **DefaultAnswer** -beállítás értékének módosítása
- Indítsa újra az App Service-t

![qnamaker appservice újraindítása](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Miért nem kapok kibontásban a SharePoint-hivatkozást?

További információ: [adatforrás helyei](./Concepts/data-sources-supported.md#data-source-locations) .

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>A Tudásbázisban létrehozott frissítések nem jelennek meg a közzététel során. Mi ennek az oka?

Minden szerkesztési műveletet, legyen szó a tábla frissítéséről, teszteléséről vagy beállításáról, a közzététel előtt menteni kell. Ügyeljen rá, hogy minden szerkesztési művelet után kattintson a **Mentés és a betanítás** gombra.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Támogatja a Tudásbázis a gazdag és a multimédiás adatok használatát?

A tudásbázis támogatja a Markdown-formátumot. Az URL-címek automatikus kibontása azonban korlátozott HTML-Markdown átalakítási képességgel rendelkezik. Ha teljes körű Markdown szeretne használni, a tartalmat közvetlenül a táblázatban módosíthatja, vagy feltölthet egy tudásbázist a gazdag tartalommal.

A multimédia, például a képek és a videók jelenleg nem támogatottak.

### <a name="does-qna-maker-support-non-english-languages"></a>Az angolon kívül támogat a QnA Maker más nyelveket is?

További információ a [támogatott nyelvekről](./Overview/languages-supported.md).

Ha több nyelven is vannak tartalmai, ügyeljen rá, hogy minden nyelvhez külön-külön szolgáltatást hozzon létre.

## <a name="manage-service"></a>Szolgáltatás kezelése

### <a name="when-should-i-restart-my-app-service"></a>Mikor kell újraindítani az App Service-t? 

Frissítse az App Service-t, ha a **felhasználói beállítások** [lap](https://www.qnamaker.ai/UserSettings) **Endpoint Keys (végponti kulcsok** ) táblájában a tudásbázishoz tartozó verzió értéke mellett a figyelmeztetés ikon található.

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Törölte a meglévő keresési szolgáltatást. Hogyan javíthatom ezt?

Ha töröl egy Azure Cognitive Search indexet, a művelet végleges, és az index nem állítható helyre. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Törölte a `testkb` indexet a keresési szolgáltatásban. Hogyan javíthatom ezt? 

A régi adatait nem lehet helyreállítani. Hozzon létre egy új QnA Maker-erőforrást, és hozza létre újra a tudásbázist.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Mikor kell frissíteni a végponti kulcsokat?

Frissítse a végpont kulcsait, ha azt gyanítja, hogy sérült a biztonsága.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Használhatom ugyanazt az Azure Cognitive Search-erőforrást a tudásbázisok számára több nyelv használatával?

Ha több nyelvet és több tudásbázist szeretne használni, a felhasználónak minden nyelvhez létre kell hoznia egy QnA Maker erőforrást. Ez egy külön Azure Search-szolgáltatást hoz létre a nyelv alapján. A különböző nyelvi tudásbázisok egyetlen Azure Search-szolgáltatásban való keverése a találatok teljesítményének romlásához vezet.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Hogyan változtathatom meg a QnA Maker által használt Azure Cognitive Search-erőforrás nevét?

Az Azure Cognitive Search erőforrás neve a QnA Maker-erőforrás neve, és néhány véletlenszerűen fűzött betűvel egészül ki. Így nehéz megkülönböztetni QnA Maker több keresési erőforrását. Hozzon létre egy külön keresési szolgáltatást (nevezze el a kívánt módon), és kapcsolódjon a QnA szolgáltatáshoz. A lépések hasonlóak az [Azure Search verziófrissítéséhez](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)szükséges lépésekhez.

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Ha a QnA Maker a `Runtime core is not initialized,` értéket adja vissza, Hogyan javíthatom?

Lehet, hogy az App Service lemezterülete megtelt. A lemezterület kijavításának lépései:

1. A [Azure Portal](https://portal.azure.com)válassza ki a QnA Maker app Service-t, majd állítsa le a szolgáltatást.
1. Noha még mindig az App Service-ben van, válassza a **fejlesztői eszközök**, majd a **speciális eszközök**, majd a **tovább**lehetőséget. Ekkor megnyílik egy új böngészőablak.
1. Válassza a **hibakeresési konzol**, majd a **cmd** parancsot egy parancssori eszköz megnyitásához. 
1. Navigáljon a _site/wwwroot/az adat/QnAMaker/_ könyvtárhoz.
1. Távolítsa el az összes olyan mappát, amelynek a neve `rd` karakterrel kezdődik. 

    Ne **törölje** a következőket:

    * KbIdToRankerMappings. txt fájl
    * EndpointSettings. JSON fájl
    * EndpointKeys mappa 

1. Indítsa el az App Service-t.
1. A tudásbázishoz férhet hozzá, hogy ellenőrizze, működik-e most. 


## <a name="integrate-with-other-services-including-bots"></a>Integrálás más szolgáltatásokkal, többek között a botokkal

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Szükséges a Bot Framework használata a QnA Maker használatához?

Nem, a [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) -t nem kell használnia a QnA Maker. QnA Maker azonban a [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)számos sablonjának egyikét kínálja. A bot Service lehetővé teszi a gyors intelligens robot-fejlesztést a Microsoft bot Framework segítségével, és egy kiszolgáló nélküli környezetben fut.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Hogyan hozhatok létre egy új robotot a QnA Maker használatával?

A [jelen](./Tutorials/create-qna-bot.md) dokumentáció utasításait követve hozza létre a robotot Azure bot Service használatával.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Hogyan használhat egy másik tudásbázist egy meglévő Azure bot Service-szel?

Az alábbi információkra van szüksége a Tudásbázisról:

* Tudásbázis-azonosító.
* A Tudásbázis közzétett végpontjának egyéni altartományának neve (`host`) a közzététel után a **Beállítások** lapon található.
* A Tudásbázis közzétett végponti kulcsa – a **Beállítások** lapon, a közzététel után. 

Ezekkel az információkkal a Azure Portal a robot app Service-t. A **Beállítások – > konfiguráció – > Alkalmazásbeállítások**területen módosítsa ezeket az értékeket.  

A Tudásbázis végponti kulcsának `QnAAuthkey` jelölése van az ABS szolgáltatásban. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Két vagy több ügyfélalkalmazás is megoszthat egy tudásbázist? 

Igen, a Tudásbázis tetszőleges számú ügyféltől kérdezhető le. Ha úgy tűnik, hogy a Tudásbázis válasza lassú vagy időtúllépést okoz, érdemes lehet frissíteni a tudásbázishoz társított app Service-szolgáltatási szintet.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hogyan ágyazhatom be a QnA Maker szolgáltatást a honlapomba?

Az alábbi lépéseket követve ágyazhatja be a QnA Maker szolgáltatást webes csevegéses vezérlőként a webhelyén:

1. A GYIK-robot létrehozásához kövesse az [itt](./Tutorials/create-qna-bot.md)található utasításokat.
2. Az [itt](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) leírt lépéseket követve engedélyezze a webes csevegést

## <a name="data-storage"></a>Adattárolás

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Milyen adattárolási és tárolási helye van? 

A QnA Maker szolgáltatás létrehozásakor egy Azure-régiót választott ki. A tudásbázisok és a naplófájlok ebben a régióban tárolódnak. 
