## <a name="microsoft-open-source-code-of-conduct"></a>A Microsoft nyílt forrás etikai kódex

A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik.
További információ: a [kód a viselkedési szabályzat GYIK RÉSZÉT](https://opensource.microsoft.com/codeofconduct/faq/) , vagy forduljon [ opencode@microsoft.com ](mailto:opencode@microsoft.com) további kérdéseivel megjegyzések.

## <a name="contribute-to-azure-technical-documentation"></a>Hozzájárul az Azure technikai dokumentációja
Szívesen fogadjuk hozzájárulások (felhasználók, felhasználók, partnerek, MSFT alkalmazottak kívül core Azure termék egységek stb.) a Közösség vagy kiszolgálóról core Azure termék egységek dolgozó alkalmazottak számára. Hogyan járulhatnak attól függ, hogy kik tartoznak:

* **Közösségi - kisebb frissítéseken estek**: kisebb frissítéseken kívül a szív goodness legnagyobb mértékben járulnak hozzá, ha akkor is a cikk a tárházban található, vagy látogasson el a [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) , és kattintson a **Szerkesztése** , ez a cikk a Githubon forrás hivatkozásra. Ezt követően használja a GitHub felhasználói felületén a a frissítések. Vagy a tárházat elágazási Üdvözöljük, és az elágazáshoz a frissítések küldésére.

* **Közösségi - új cikkek**: Ha Ön része az Azure közösségi és szeretne új cikkeket, úgy, hogy, hogy új tartalom védelméhez a nyilvános munkahelyi és személyes tárház alkalmazott együttműködve kell.

* **Az alkalmazottak**: a műszaki író, a program manager, vagy az Azure-szolgáltatások, és azt a termékért felelős csoport a fejlesztői a feladat járulnak hozzá, vagy technikai cikkek hozhatnak létre, ha a titkos tárház (https://github.com/ kell használnia MicrosoftDocs/azure-docs-pr). Ha egy meglévő cikkben jelentős módosításokat végez, hozzá vagy módosíthat képek vagy legnagyobb mértékben járulnak hozzá egy új cikk szeretné ebben a tárházban forkolhatja, telepítse a Git bash eszközt, markdown-szerkesztő, és ismerje meg, néhány git-parancsokat. Lásd: [a belső közreműködői útmutató](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) további információt.


## <a name="about-your-contributions-to-azure-content"></a>Az Azure-tartalmak fejlesztéséről
### <a name="minor-corrections"></a>Kisebb korrekciók
Kisebb korrekciók és pontosítások meg dokumentációkban és példakódokban kódpéldák tartoznak a [docs.microsoft.com használati](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Jelentősebb mértékű hozzájárulások
Ha új vagy jelentős módosításai kódpéldán pull-kérelemre, küldünk Megjegyzés a Githubon kéri, hogy küldje el az online hozzájárulás licenc szerződés (CLA), ha nincs Microsoft alkalmazottja. A nevéhez fűződő tartalmat csak a szerződés elfogadása után fogadhatjuk be.

## <a name="tools-and-setup"></a>Eszközök és telepítés
Közösségi közreműködők a GitHub felhasználói felületén vagy oszthatja ketté a tárházban közre is. Látogasson el az alkalmazott [a belső közreműködői útmutató](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) hozzájárulnak a műszaki dokumentáció olvashat be.

## <a name="repository-organization"></a>A tárház szerkezete
A tartalom az azure-dokumentumok tárházban található dokumentáció https://docs.microsoft.com/azure következik. A tárház két gyökérmappát tartalmaz:

### <a name="articles"></a>\articles
Az *\articles* mappa a dokumentáció cikkeit tartalmazza Markdown-fájlok formájában, *.md* kiterjesztéssel. Cikkek általában Azure szolgáltatás szerint vannak csoportosítva.

A *\articles* mappa tartalmazza a *\media* mappa legfelső szintű gyökérkönyvtár cikkek médiafájljainak, amelyben az egyes cikkekhez tartozó képeket találhatók.  A szolgáltatásokhoz kapcsolódó mappák saját Media mappával rendelkeznek. Ezekben találhatók az adott szolgáltatáshoz tartozó cikkek médiafájljai. A médiafájlokat tartalmazó mappák neve azonos a vonatkozó cikkfájl nevével, leszámítva az *.md* kiterjesztést.

### <a name="includes"></a>\includes
A tárházban többször, több cikkben is felhasználható tartalomelemek is kialakíthatók. Ezeket tárolja ez a mappa. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Tartalomformázás Markdown-szintaxissal
A tárház cikkeit a Markdown-szintaxis GitHub-dialektusa szerint kell formázni.  Néhány segédanyag:

* [Markdown alapjai](https://help.github.com/articles/markdown-basics/)
* [Nyomtatható markdown-puska](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)


## <a name="labels"></a>Címkék
A nyilvános azure-dokumentumok tárház automatizált címkék vannak társítva kérelmeket, hogy segítséget nyújtson a lekérési kérelem munkafolyamat kezelése és érdekében, hogy tudja, mit jelenít meg a lekérési kérelmet lekéréses:

* Kapcsolódó hozzájárulás licencszerződés
  * CLA nem kötelező: A módosítás viszonylag csekély, és nem igényli, hogy egy CLA jelentkezik.
  * CLA szükséges: a módosítás következményeivel viszonylag nagy, és megköveteli, hogy egy CLA jelentkezik.
  * CLA által aláírt: A közreműködői aláírt a CLA, így a lekérési kérelem jóváhagyásra most már továbbléphet előre.
* Ahhoz, hogy küldött módosítása: A Szerző értesítést kapott arról, a függőben lévő lekérési kérelmet.
* készen áll-egyesítés: készen áll a felülvizsgálatra a lekérési kérelem felülvizsgálati csapat által.


