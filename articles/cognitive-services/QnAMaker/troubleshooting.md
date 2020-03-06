---
title: Hibaelhárítás – QnA Maker
description: A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.
ms.topic: troubleshooting
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 4596c16a5d7c9053bf0e27af476c66fe8fa9ed35
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296099"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker hibaelhárítása

A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

<details>
<summary><b>A QnAMaker szolgáltatás végpontjának beszerzése</b></summary>

**Válasz**: a QnAMaker szolgáltatás végpontja hibakeresési célokra használható, amikor felveszi a kapcsolatot a QnAMaker támogatási szolgálatával vagy a UserVoice. A végpont egy URL-cím ebben az űrlapban: https://your-resource-name.azurewebsites.net.

1. Lépjen a QnAMaker szolgáltatásra (erőforráscsoport) a [Azure Portal](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoportot az Azure Portalon](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a QnA Maker erőforráshoz társított App Service. A nevek általában azonosak.

     ![Válassza ki a QnAMaker App Service-ben](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. A végpont URL-címe az Áttekintés szakaszban érhető el

    ![QnAMaker végpont](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>A Tudásbázis kezelése

<details>
<summary><b>Véletlenül töröltem a QnA Maker egy részét, mit tegyek?</b></summary>

**Válasz**: ne törölje a létrehozott Azure-szolgáltatásokat a QnA Maker erőforrással, például a Search vagy a Web App szolgáltatással együtt. Ezek a QnA Maker működéséhez szükségesek, ha törli az egyiket, QnA Maker nem fog megfelelően működni.

Összes törlése végleges, beleértve a kérdés és válasz párt, fájlok, URL-címek, egyéni kérdéseket és válaszokat, tudásbázisok vagy Azure-erőforrások. A Tudásbázis bármely részének törlése előtt győződjön meg arról, hogy a tudásbázist a **Beállítások** lapról exportálja.

</details>

<details>
<summary><b>Miért nem/file a kérdés-válasz párokat az URL-ek (k)?</b></summary>

**Válasz**: lehetséges, hogy QnA Maker nem tud automatikusan kinyerni néhány kérdés-válasz (QnA) tartalmat az érvényes GYIK URL-címekről. Ezekben az esetekben egy .txt fájlban illessze be a szövegét, és tekintse meg, ha az eszköz betöltheti azt. Másik lehetőségként a [QnA Maker portálon](https://qnamaker.ai)is hozzáadhat tartalmat a tudásbázishoz.

</details>

<details>
<summary><b>Mekkora lehet a Tudásbázis létrehozása?</b></summary>

**Válasz**: a Tudásbázis mérete az QnA Maker szolgáltatás létrehozásakor választott Azure Search SKU-ból függ. További [részletekért olvassa el](./Tutorials/choosing-capacity-qnamaker-deployment.md) a következőt:.

</details>

<details>
<summary><b>Miért nem látok semmit a legördülő listából, amikor megpróbálok új tudásbázist létrehozni?</b></summary>

**Válasz**: még nem hozott létre QnA Maker szolgáltatásokat az Azure-ban. Ennek [megismeréséhez olvassa el](./How-To/set-up-qnamaker-service-azure.md) a következőt:.

</details>

<details>
<summary><b>Hogyan megoszthat tudásbázist másokkal?</b></summary>

**Válasz**: a megosztás a QnA Maker szolgáltatás szintjén működik, azaz a szolgáltatásban található összes Tudásbázis meg lesz osztva. [Itt](./How-To/collaborate-knowledge-base.md) olvashat arról, hogyan lehet együttműködni a Tudásbázisban.

</details>

<details>
<summary><b>Megoszthat egy tudásbázist olyan közreműködővel, amely nem ugyanabban az HRE-bérlőben található, a Tudásbázis módosításához?</b></summary>

**Válasz**: a megosztás az Azure szerepköralapú hozzáférés-vezérlés (RBAC) alapján történik. Ha egy másik felhasználóval is megoszthat _bármilyen_ erőforrást az Azure-ban, megoszthatja QnA Maker.

</details>

<details>
<summary><b>Ha van App Service csomag 5 QnAMaker tudásbázissal. Az olvasási/írási jogosultság 5 különböző felhasználóhoz rendelhető, így mindegyikhez csak 1 QnAMaker-alapú Tudásbázis férhet hozzá?</b></summary>

**Válasz**: megoszthat egy teljes QnAMaker szolgáltatást, nem pedig egyedi tudásbázisokat.

</details>

<details>
<summary><b>Hogyan változtathatom meg az alapértelmezett üzenetet, ha nem találok megfelelő egyezést?</b></summary>

**Válasz**: az alapértelmezett üzenet az App Service-ben található beállítások részét képezi.
- Nyissa meg az Azure Portalon, az App service erőforrás

![az App Service qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **Beállítások** lehetőségre.

![qnamaker az App Service-beállítások](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **DefaultAnswer** -beállítás értékének módosítása
- Indítsa újra az App service

![qnamaker az App Service-újraindítás](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Miért nem kapok kibontásban a SharePoint-hivatkozást?</b></summary>

**Válasz**: további információért tekintse meg az [adatforrás helyét](./Concepts/knowledge-base.md#data-source-locations) ismertető témakört.

</details>

<details>
<summary><b>A Tudásbázisban létrehozott frissítések nem jelennek meg a közzététel során. miért ne?</b></summary>

**Válasz**: minden szerkesztési műveletet, legyen szó a tábla frissítéséről, teszteléséről vagy beállításáról, a közzététel előtt menteni kell. Ügyeljen rá, hogy minden szerkesztési művelet után kattintson a **Mentés és a betanítás** gombra.

</details>

<details>
<summary><b>Támogatja a Tudásbázis a gazdag és a multimédiás adatok használatát?</b></summary>

**Válasz**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Multimédiás Automatikus kibontás fájlok és URL-címek esetén

* URL-címek – korlátozott HTML-Markdown átalakítási képesség.
* Fájlok – nem támogatott

#### <a name="answer-text-in-markdown"></a>Válasz szövege a Markdown
Ha a QnA készletek szerepelnek a Tudásbázisban, szerkesztheti a válasz Markdown szövegét, hogy a nyilvános URL-címekről elérhető adathordozóra mutató hivatkozásokat is tartalmazzon.


</details>

<details>
<summary><b>A QnA Maker támogatja a nem angol nyelvű nyelveket?</b></summary>

**Válasz**: Tekintse meg a [támogatott nyelvek](./Overview/languages-supported.md)további részleteit.

Ha több nyelven elérhető tartalmait, mindenképpen hozzon létre egy külön szolgáltatás, az egyes nyelvekhez.

</details>

## <a name="manage-service"></a>Szolgáltatás kezelése

<details>
<summary><b>Mikor kell újraindítani az App Service-t?</b></summary>

**Válasz**: frissítse az App Service-t, ha a **felhasználói beállítások** [lap](https://www.qnamaker.ai/UserSettings) **Endpoint Keys (végponti kulcsok** ) táblájában a tudásbázishoz tartozó verzió értéke melletti figyelmeztető ikon található.

</details>

<details>
<summary><b>Törölte a meglévő keresési szolgáltatást. Hogyan javíthatom ezt?</b></summary>

**Válasz**: Ha töröl egy Azure Cognitive Search indexet, a művelet végleges, és az index nem állítható helyre.

</details>

<details>
<summary><b>Törölte a `testkb` indexet a keresési szolgáltatásban. Hogyan javíthatom ezt?</b></summary>

**Válasz**: a régi adatai nem állíthatók helyre. Hozzon létre egy új QnA Maker-erőforrást, és hozza létre újra a tudásbázist.

</details>

<details>
<summary><b>Mikor kell frissíteni a végponti kulcsokat?</b></summary>

**Válasz**: frissítse a végpont kulcsait, ha azt gyanítja, hogy sérült a biztonsága.

</details>

<details>
<summary><b>Használhatom ugyanazt az Azure Cognitive Search-erőforrást a tudásbázisok számára több nyelv használatával?</b></summary>

**Válasz**: Ha több nyelvet és több tudásbázist szeretne használni, a felhasználónak minden nyelvhez létre kell hoznia egy QnA Maker erőforrást. Ez egy külön Azure Search-szolgáltatást hoz létre a nyelv alapján. Ettől eltérő nyelv tudásbázisok egy egyetlen az Azure search szolgáltatásban keverése csökkentett teljesítményű, az eredmények relevancia eredményez.

</details>

<details>
<summary><b>Hogyan változtathatom meg a QnA Maker által használt Azure Cognitive Search-erőforrás nevét?</b></summary>

**Válasz**: az Azure Cognitive Search erőforrás neve a QnA Maker-erőforrás neve, és néhány véletlenszerűen fűzött betűvel rendelkezik. Így nehéz különbséget tenni a QnA Maker több keresési erőforrások között. Hozzon létre egy külön keresési szolgáltatást (nevezze el a kívánt módon), és kapcsolódjon a QnA szolgáltatáshoz. A lépések hasonlóak az [Azure Search verziófrissítéséhez](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)szükséges lépésekhez.

</details>

<details>
<summary><b>Ha a QnA Maker visszaadja `Runtime core is not initialized,` Hogyan javíthatom?</b></summary>

**Válasz**: lehet, hogy az App Service lemezterülete megtelt. A lemezterület kijavításának lépései:

1. A [Azure Portal](https://portal.azure.com)válassza ki a QnA Maker app Service-t, majd állítsa le a szolgáltatást.
1. Noha még mindig az App Service-ben van, válassza a **fejlesztői eszközök**, majd a **speciális eszközök**, majd a **tovább**lehetőséget. Ekkor megnyílik egy új böngészőablak.
1. Válassza a **hibakeresési konzol**, majd a **cmd** parancsot egy parancssori eszköz megnyitásához.
1. Navigáljon a _site/wwwroot/az adat/QnAMaker/_ könyvtárhoz.
1. Távolítsa el az összes olyan mappát, amelynek a neve `rd`vel kezdődik.

    Ne **törölje** a következőket:

    * KbIdToRankerMappings. txt fájl
    * EndpointSettings. JSON fájl
    * EndpointKeys mappa

1. Indítsa el az App Service-t.
1. A tudásbázishoz férhet hozzá, hogy ellenőrizze, működik-e most.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integráció más szolgáltatásokkal, beleértve a robotok

<details>
<summary><b>Szükség van a bot Framework használatára a QnA Maker használatához?</b></summary>

**Válasz**: nem, a [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) -et nem kell használnia QnA Maker. QnA Maker azonban a [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)számos sablonjának egyikét kínálja. Bot Service lehetővé teszi, hogy a gyors intelligens robotfejlesztést keresztül a Microsoft Bot Framework, és hogy fut egy kiszolgáló nélküli környezetben.

</details>

<details>
<summary><b>Hogyan hozhatok létre egy új robotot a QnA Maker használatával?</b></summary>

**Válasz**: kövesse az [ebben](./Quickstarts/create-publish-knowledge-base.md) a dokumentációban található utasításokat a robot Azure bot Service való létrehozásához.

</details>

<details>
<summary><b>Hogyan használhat egy másik tudásbázist egy meglévő Azure bot Service-szel?</b></summary>

**Válasz**: az alábbi információkra van szüksége a Tudásbázisban:

* Tudásbázis-azonosító.
* A Tudásbázis közzétett végpontjának egyéni altartományának neve (`host`) a közzététel után a **Beállítások** lapon található.
* A Tudásbázis közzétett végponti kulcsa – a **Beállítások** lapon, a közzététel után.

Ezekkel az információkkal a Azure Portal a robot app Service-t. A **Beállítások – > konfiguráció – > Alkalmazásbeállítások**területen módosítsa ezeket az értékeket.

A Tudásbázis végponti kulcsának címkéje `QnAAuthkey` az ABS szolgáltatásban.

</details>

<details>
<summary><b>Két vagy több ügyfélalkalmazás is megoszthat egy tudásbázist?</b></summary>

**Válasz**: igen, a Tudásbázis tetszőleges számú ügyféltől kérdezhető le. Ha úgy tűnik, hogy a Tudásbázis válasza lassú vagy időtúllépést okoz, érdemes lehet frissíteni a tudásbázishoz társított app Service-szolgáltatási szintet.

</details>

<details>
<summary><b>Hogyan beágyazni a QnA Maker szolgáltatást a saját webhelyemen?</b></summary>

**Válasz**: kövesse az alábbi lépéseket a QnA Maker szolgáltatás webes csevegéses vezérlőként való beágyazásához a webhelyén:

1. A GYIK-robot létrehozásához kövesse az [itt](./Quickstarts/create-publish-knowledge-base.md)található utasításokat.
2. Az [itt](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) leírt lépéseket követve engedélyezze a webes csevegést

</details>

## <a name="data-storage"></a>Adattárolás

<details>
<summary><b>Milyen adattárolási és tárolási helye van?</b></summary>

**Válasz**:

A QnA Maker szolgáltatás létrehozásakor kiválasztott egy Azure-régióban. A tudásbázisok és a naplófájlok ebben a régióban vannak tárolva.

</details>