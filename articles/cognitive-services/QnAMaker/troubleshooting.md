---
title: Hibaelhárítás - QnA Maker
description: A QnA Maker szolgáltatással kapcsolatos leggyakoribb kérdések kurátori listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: 7847e21dbcf07f669d6802fffdd1e43623a72340
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804350"
---
# <a name="troubleshooting-for-qna-maker"></a>A QnA Maker hibáinak elhárítása

A QnA Maker szolgáltatással kapcsolatos leggyakoribb kérdések kurátori listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Előrejelzések kezelése

<details>
<summary><b>Hogyan javíthatom a lekérdezési előrejelzések átviteli teljesítményét?</b></summary>

**Válasz:** Átviteli teljesítmény problémák azt jelzik, hogy az alkalmazásszolgáltatás és a cognitive Search kell skálázás. Fontolja meg egy replika hozzáadása a cognitive Search teljesítményének javítása érdekében.

További információ [a tarifacsomagokról.](Concepts/azure-resources.md)
</details>

<details>
<summary><b>A QnAMaker szolgáltatás végpontjának beolvasása</b></summary>

**Válasz:** A QnAMaker szolgáltatás végpontja hibakereséshez hasznos, amikor kapcsolatba lép a QnAMaker támogatásával vagy a UserVoice-val. A végpont egy URL-cím `https://your-resource-name.azurewebsites.net`ezen a képernyőn: .

1. A QnAMaker szolgáltatás (erőforráscsoport) megnyitva az [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure erőforráscsoport az Azure Portalon](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a QnA Maker erőforráshoz társított App Service-t. Általában a nevek ugyanazok.

     ![Válassza a QnAMaker appservice lehetőséget](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. A végpont URL-címe az Áttekintés szakaszban érhető el

    ![QnAMaker végpont](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>A tudásbázis kezelése

<details>
<summary><b>Véletlenül töröltem a QnA Maker egy részét, mit tegyek?</b></summary>

**Válasz:** Ne törölje a QnA Maker erőforrással, például a Keresés vagy a Web App használatával együtt létrehozott Azure-szolgáltatások egyikét sem. Ezek szükségesek a QnA Maker működéséhez, ha töröl egyet, a QnA Maker nem fog megfelelően működni.

Minden törlés állandó, beleértve a kérdés-válasz párokat, fájlokat, URL-eket, egyéni kérdéseket és válaszokat, tudásbázisokat vagy Azure-erőforrásokat. Mielőtt a tudásbázis bármely **Settings** részének törlése, győződjön meg arról, hogy a tudásbázis bármely részéből exportálja a tudásbázist.

</details>

<details>
<summary><b>Miért nem bontja ki az URL-címem/fájlja(i) a kérdés-válasz párokat?</b></summary>

**Válasz:** Lehetséges, hogy a QnA Maker nem tud automatikusan kinyerni néhány kérdés-válasz (QnA) tartalmat az érvényes GYIK URL-ekből. Ilyen esetekben beillesztheti a QnA-tartalmat egy .txt fájlba, és megnézheti, hogy az eszköz be tudja-e adni. A [QnA Maker portálon](https://qnamaker.ai)keresztül szerkesztői módon is hozzáadhat tartalmat a tudásbázisához.

</details>

<details>
<summary><b>Mekkora tudásbázist hozhatok létre?</b></summary>

**Válasz:** A tudásbázis mérete az Azure-keresés termékváltozatátantól függ, amelyet a QnA Maker szolgáltatás létrehozásakor választ. Olvassa el [itt](./Tutorials/choosing-capacity-qnamaker-deployment.md) a további részleteket.

</details>

<details>
<summary><b>Miért nem látok semmit a legördülő menüben, amikor új tudásbázist próbálok létrehozni?</b></summary>

**Válasz:** Még nem hozott létre QnA Maker-szolgáltatásokat az Azure-ban. Olvassa el [itt,](./How-To/set-up-qnamaker-service-azure.md) hogy megtanulják, hogyan kell csinálni.

</details>

<details>
<summary><b>Hogyan oszthatok meg tudásbázist másokkal?</b></summary>

**Válasz:** A megosztás a QnA Maker szolgáltatás szintjén működik, azaz a szolgáltatás összes tudásbázisa meg lesz osztva. Itt [here](./How-To/collaborate-knowledge-base.md) olvashatja, hogyan működhet együtt egy tudásbázison.

</details>

<details>
<summary><b>Meg tud osztani egy tudásbázist egy olyan közreműködővel, amely nem ugyanabban az AAD-bérlőben található, hogy módosítsa a tudásbázist?</b></summary>

**Válasz:** A megosztás az Azure szerepköralapú hozzáférés-vezérlésen (RBAC) alapul. Ha az Azure-ban _bármely_ erőforrást megoszthat egy másik felhasználóval, megoszthatja a QnA Makert is.

</details>

<details>
<summary><b>Ha 5 QnAMaker tudásbázissal rendelkező App Service-csomaggal rendelkezik. Hozzá tud rendelni olvasási/írási jogokat 5 különböző felhasználóhoz, így mindegyikük csak 1 QnAMaker tudásbázishoz férhet hozzá?</b></summary>

**Válasz:** A teljes QnAMaker szolgáltatást megoszthatja, nem pedig az egyéni tudásbázisokat.

</details>

<details>
<summary><b>Hogyan módosíthatom az alapértelmezett üzenetet, ha nem található megfelelő egyezés?</b></summary>

**Válasz:** Az alapértelmezett üzenet az alkalmazásszolgáltatás beállításainak része.
- Nyissa meg az App Service-erőforrást az Azure Portalon

![qnamaker alkalmazásszolgáltatás](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **Beállítások** lehetőségre

![qnamaker appservice beállításai](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **A DefaultAnswer** beállítás értékének módosítása
- Az alkalmazásszolgáltatás újraindítása

![qnamaker appservice újraindítás](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Miért nem bontja ki a SharePoint-hivatkozást?</b></summary>

**Válasz**: További információ: [Adatforrás-helyek.](./Concepts/knowledge-base.md#data-source-locations)

</details>

<details>
<summary><b>A tudásbázisomon végzett frissítések nem jelennek meg a közzétételkor. miért ne?</b></summary>

**Válasz:** Minden szerkesztési műveletet, legyen az táblafrissítés, teszt vagy beállítás, a közzététel előtt menteni kell. Minden szerkesztési művelet után kattintson a **Mentés és betanítás** gombra.

</details>

<details>
<summary><b>Támogatja a tudásbázis a gazdag adatokat vagy a multimédiás tartalmakat?</b></summary>

**Válasz**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Multimédiás automatikus kinyerés fájlokhoz és URL-címekhez

* URL-címek – korlátozott HTML-to-Markdown konverziós képesség.
* Fájlok - nem támogatottak

#### <a name="answer-text-in-markdown"></a>Szöveg megválaszolása markdownban
Miután a QnA-párok a tudásbázisban vannak, szerkesztheti a válasz markdown szövegét, hogy az tartalmazza a nyilvános URL-ekből elérhető médiahivatkozásokat.


</details>

<details>
<summary><b>Támogatja a QnA Maker a nem angol nyelvű nyelveket?</b></summary>

**Válasz**: További részletek a [támogatott nyelvekről](./Overview/languages-supported.md).

Ha több nyelvről származó tartalommal rendelkezik, ügyeljen arra, hogy minden nyelvhez külön szolgáltatást hozzon létre.

</details>

## <a name="manage-service"></a>Szolgáltatás kezelése

<details>
<summary><b>Mikor kell újraindítani az alkalmazásszolgáltatást?</b></summary>

**Válasz:** Frissítse az alkalmazásszolgáltatást, ha a figyelmeztetés ikon a tudásbázis verzióértéke mellett található a **Felhasználói beállítások** [lap](https://www.qnamaker.ai/UserSettings) **Végpontok billentyűk** táblázatában.

</details>

<details>
<summary><b>Töröltem a meglévő keresési szolgáltatásomat. Hogyan tudom ezt helyrehozni?</b></summary>

**Válasz:** Ha töröl egy Azure Cognitive Search indexet, a művelet végleges, és az index nem állítható helyre.

</details>

<details>
<summary><b>Töröltem `testkb` az indexemet a keresési szolgáltatásban. Hogyan tudom ezt helyrehozni?</b></summary>

**Válasz:** A régi adatok nem lehet vissza. Hozzon létre egy új QnA Maker erőforrást, és hozza létre újra a tudásbázist.

</details>

<details>
<summary><b>Mikor kell frissíteni a végpontkulcsokat?</b></summary>

**Válasz:** Frissítse a végpontkulcsokat, ha azt gyanítja, hogy azok biztonsága sérült.

</details>

<details>
<summary><b>Használhatom ugyanazt az Azure Cognitive Search erőforrást több nyelvet használó tudásbázisokhoz?</b></summary>

**Válasz:** Több nyelv és több tudásbázis használatához a felhasználónak minden nyelvhez létre kell hoznia egy QnA Maker erőforrást. Ez nyelvenként külön Azure-keresési szolgáltatást hoz létre. A különböző nyelvi tudásbázisok egyetlen Azure-keresési szolgáltatásban való keverése az eredmények csökkent relevanciáját eredményezi.

</details>

<details>
<summary><b>Hogyan módosíthatom a QnA Maker által használt Azure Cognitive Search erőforrás nevét?</b></summary>

**Válasz:** Az Azure Cognitive Search erőforrás neve a QnA Maker erőforrás neve néhány véletlenszerű betűkkel a végén. Ez megnehezíti a QnA Maker több keresési erőforrásának megkülönböztetését. Hozzon létre egy külön keresési szolgáltatást (elnevezése, ahogy szeretné), és csatlakoztassa a QnA szolgáltatáshoz. A lépések hasonlóak az [Azure-keresés frissítéséhez](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)szükséges lépésekhez.

</details>

<details>
<summary><b>Amikor a QnA Maker visszatér, `Runtime core is not initialized,` hogyan tudom megjavítani?</b></summary>

**Válasz:** Lehet, hogy az alkalmazásszolgáltatás lemezterülete megtelt. A lemezterület javításának lépései:

1. Az [Azure Portalon](https://portal.azure.com)válassza ki a QnA Maker appszolgáltatás, majd állítsa le a szolgáltatást.
1. Amíg továbbra is az App service,válassza **fejlesztési eszközök**, majd **a Speciális eszközök**, majd **menj**. Ez megnyit egy új böngészőablakot.
1. A parancssori eszköz megnyitásához válassza **a Debug konzol**lehetőséget, majd a **CMD** lehetőséget.
1. Keresse meg a _webhelyet/wwwroot/Data/QnAMaker/_ könyvtárat.
1. Távolítsa el azokat a `rd`mappákat, amelyek nek a neve kezdődik .

    **Ne törölje** a következőket:

    * KbIdToRankerMappings.txt fájl
    * EndpointSettings.json fájl
    * EndpointKeys mappa

1. Indítsa el az App szolgáltatást.
1. A tudásbázishoz való hozzáféréssel ellenőrizheti, hogy most már működik-e.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integrálható más szolgáltatásokkal, beleértve a botokat

<details>
<summary><b>A QnA Maker használatához botkeretet kell használnom?</b></summary>

**Válasz:** Nem, nem kell használni a [Bot framework](https://github.com/Microsoft/botbuilder-dotnet) qna maker. A QnA Maker azonban az [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)számos sablonja közül egyet kínál. A Bot Service lehetővé teszi a gyors intelligens robotfejlesztést a Microsoft Bot Framework-en keresztül, és kiszolgáló nélküli környezetben fut.

</details>

<details>
<summary><b>Hogyan hozhatok létre új robotot a QnA Maker segítségével?</b></summary>

**Válasz:** Kövesse az [ebben](./Quickstarts/create-publish-knowledge-base.md) a dokumentációban található utasításokat a robot az Azure Bot Service használatával létrehozásához.

</details>

<details>
<summary><b>Hogyan használhatok egy másik tudásbázist egy meglévő Azure-robotszolgáltatással?</b></summary>

**Válasz:** A tudásbázissal kapcsolatban a következő információkkal kell rendelkeznie:

* Tudásbázis-azonosító.
* A tudásbázis közzétett végpontegyéni altartományneve, `host`más néven a közzététel után megtalálható a **Beállítások** lapon.
* A tudásbázis közzétett végpontkulcsa a közzététel után megtalálható a **Beállítások** lapon.

Ezekkel az információkkal nyissa meg a robot alkalmazásszolgáltatását az Azure Portalon. A **Beállítások -> Konfiguráció -> Alkalmazás beállításai csoportban**módosítsa ezeket az értékeket.

A tudásbázis végpontkulcsa az `QnAAuthkey` ABS szolgáltatásban van címkézve.

</details>

<details>
<summary><b>Két vagy több ügyfélalkalmazás osztozhat tudásbázison?</b></summary>

**Válasz:** Igen, a tudásbázis tetszőleges számú ügyféltől kérdezhető le. Ha a tudásbázis válasza lassúnak vagy időoutnak tűnik, fontolja meg a tudásbázishoz társított alkalmazásszolgáltatás szolgáltatási szintjének frissítését.

</details>

<details>
<summary><b>Hogyan ágyazhatom be a QnA Maker szolgáltatást a webhelyembe?</b></summary>

**Válasz**: Kövesse az alábbi lépéseket a QnA Maker szolgáltatás webchat-vezérlőként való beágyazásához a webhelyén:

1. Hozza létre a GYIK bot az alábbi utasításokat követve [itt](./Quickstarts/create-publish-knowledge-base.md).
2. Az internetes csevegés engedélyezése az [itt](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) leírt lépések végrehajtásával

</details>

## <a name="data-storage"></a>Adattárolás

<details>
<summary><b>Milyen adatokat tárolnak, és hol tárolják őket?</b></summary>

**Válasz**:

A QnA Maker szolgáltatás létrehozásakor kiválasztott egy Azure-régiót. A tudásbázisok és a naplófájlok ebben a régióban tárolódnak.

</details>