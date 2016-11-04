# Szerzői kalauz az Azure műszaki dokumentációjának fejlesztéséhez
Ez a GitHub-tárház (repository) a [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) címen üzemelő Azure Dokumentációs központban publikált műszaki dokumentáció forrása.

A tárház a műszaki dokumentációk fejlesztésének módját és menetét is ismerteti.  Ajánljuk figyelmébe a szerzői segédanyagok [tartalomjegyzékét](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Részvétel az Azure dokumentálásában
Örömmel vesszük az Azure dokumentációja iránti érdeklődését!

* [Közreműködési lehetőségek](#ways-to-contribute)
* [Etikai kódex](#code-of-conduct)
* [Az Azure-tartalmak fejlesztéséről](#about-your-contributions-to-azure-content)
* [A tárház szerkezete](#repository-organization)
* [A GitHub, a Git és a jelen tárház használata](#use-github-git-and-this-repository)
* [Tartalomformázás Markdown-szintaxissal](#how-to-use-markdown-to-format-your-topic)
* [Visszajelzések, megjegyzések és támogatás](./contributor-guide/feedback-and-comments.md)
* [További erőforrások](#more-resources)
* [Szerzői segédanyagok jegyzéke](./contributor-guide/contributor-guide-index.md) (új lapon nyílik meg)

## Közreműködési lehetőségek
Az [Azure dokumentációjának](http://azure.microsoft.com/documentation/) fejlesztésében többféle formában vehet részt:

* Bekapcsolódhat a [fórumokon](http://social.msdn.microsoft.com/Forums/windowsazure/home) zajló beszélgetésekbe.
* Hozzászólhat az egyes cikkekhez (a Disqus rendszerén keresztül).
* A GitHub felhasználói felületén keresztül egyszerűen részt vehet a műszaki cikkek fejlesztésében. Az egyes cikkeket a tárházban is megkeresheti, de a [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) portál oldalain látható GitHub-hivatkozásokon keresztül is eljuthat a szerkesztőfelületre.
* Ha alapvető változtatásokat szeretne végezni egy cikken, képeket szeretne feltölteni hozzá (vagy módosítaná a cikkben szereplő képeket), esetleg új cikket írna, ágaztassa el a tárházat. Ehhez telepítenie a Git Basht és a Markdown Padet, és meg kell ismerkednie néhány git-paranccsal.

## Etikai kódex
A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. Kérdéseire választ találhat az [etikai kódex gyakori kérdései között](https://opensource.microsoft.com/codeofconduct/faq/)Az itt meg nem válaszolt kérdéseit, illetve egyéb gondolatait örömmel várjuk az [opencode@microsoft.com](mailto:opencode@microsoft.com) címen.

## Az Azure-tartalmak fejlesztéséről
### Kisebb korrekciók
A dokumentációkban és példakódokban tett kisebb korrekciók és pontosítások az [Azure portál felhasználási feltételeinek](http://azure.microsoft.com/support/legal/website-terms-of-use/) hatálya alá esnek.

### Jelentősebb mértékű hozzájárulások
Ha nagyobb lélegzetvételű módosításokat végzett egy cikken vagy kódpéldán, a publikálási pull-kérelemre válaszul egy GitHub-hozzászólásban meg fogjuk kérni a tartalomszerzői licencszerződés (Contribution License Agreement; CLA) aláírására, hacsak nem tagja valamelyik alábbi csoportnak:

* Microsoft Open Technologies csoport
* Nem a Microsoft alkalmazásában álló szerzők

A nevéhez fűződő tartalmat csak a szerződés elfogadása után fogadhatjuk be.

Részletek: [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## A tárház szerkezete
Az azure-content tárház szerkezete azonos az [Azure.Microsoft.com](http://azure.microsoft.com) címen elérhető dokumentáció szerkezetével. A tárház két gyökérmappát tartalmaz:

### \articles
Az *\articles* mappa a dokumentáció cikkeit tartalmazza Markdown-fájlok formájában, *.md* kiterjesztéssel.

A gyökérmappában lévő cikkek a *http://azure.microsoft.com/documentation/articles/{cikk-neve-md-nélkül}/* címen jelennek meg élesben.

* **A cikkfájlok elnevezése:** lásd a [fájlelnevezési útmutatót](./contributor-guide/file-names-and-locations.md).

Az egyes szolgáltatásokhoz tartozó mappákban lévő cikkek a *http://azure.microsoft.com/documentation/articles/{szolgáltatás-mappája}/{cikk-neve-md-nélkül}/* címen jelennek meg élesben.

* **Media nevű almappák:** Az *\articles* mappában lévő *\media* mappában találhatók a főkönyvtárbeli cikkekhez tartozó médiafájlok – e mappán belül minden cikknek saját almappája van.  A szolgáltatásokhoz kapcsolódó mappák saját Media mappával rendelkeznek. Ezekben találhatók az adott szolgáltatáshoz tartozó cikkek médiafájljai. A médiafájlokat tartalmazó mappák neve azonos a vonatkozó cikkfájl nevével, leszámítva az *.md* kiterjesztést.

### \includes
A tárházban többször, több cikkben is felhasználható tartalomelemek is kialakíthatók. Ezeket tárolja ez a mappa. Részletek: [Műszaki tartalmakban használt saját bővítmények](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates
A mappa a cikkek standard Markdown-sablonjait tartalmazza.

### \contributor-guide
Itt találhatók a szerzői útmutatók.  

## A GitHub, a Git és a jelen tárház használata
Az [GitHub-beli tartalomfejlesztési eszközök telepítését és beállítását ismertető oldalon](./contributor-guide/tools-and-setup.md) részletesen tájékozódhat a közreműködés módjáról; megtudhatja, hogyan tehet meg kisebb módosításokat a GitHub felhasználói felületén; valamint hogy hogyan forkolhatja, és hogyan klónozhatja a tárházat a jelentősebb módosítások megtétele előtt.

Ha saját gépén szeretne dolgozni, és telepíti a GitBasht, az új helyi munkaág létrehozásáról, a módosítások megtételéről, illetve a módosult fájlok főágba történő visszavezetéséről [az új cikkek írásához és a cikkek szerkesztéséhez használandó Git-parancsokat ismertető oldalon](./contributor-guide/git-commands-for-master.md) olvashat.

### Ágak
Célszerű minden módosítási célra külön helyi ágat készíteni. Egy-egy ágnak csak egy-egy koncepciót vagy cikket kell felölelnie. Így hatékonyabbá tehető a fejlesztés, és az egyesítési ütközések száma is mérsékelhető.  Egy-egy ág tipikus terjedelme vagy célja:

* Új cikk írása (a kapcsolódó képekkel)
* Egy adott cikk helyesírási hibáinak javítása.
* Azonos formázási módosítás elvégzése nagyobb mennyiségű cikken (például a szerzői jogi lábjegyzet átformázása).

## Tartalomformázás Markdown-szintaxissal
A tárház cikkeit a Markdown-szintaxis GitHub-dialektusa szerint kell formázni.  Néhány segédanyag:

* [A Markdown alapjai](https://help.github.com/articles/markdown-basics/)
* [Nyomtatható Markdown-puska](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)
* Markdown-szerkesztőink listáját megtalálja az [eszközökkel és beállításokkal foglalkozó oldalon](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Cikkek metaadatai
A cikkek metaadatai fontosak az azure.microsoft.com egyes funkcióihoz – ilyen a szerzők és társszerzők megnevezése, a navigációs sávok működése, a cikkek kivonatának előállítása, a keresőoptimalizálás, illetve a tartalmak iránti érdeklődés mérésére szolgáló jelentési funkció. Szóval a metaadatok fontosak! Ajánljuk figyelmébe [a metaadatok helyes használatát ismertető cikket](./contributor-guide/article-metadata.md).

## További segédanyagok
[Szerzői kalauzunk oldalán](./contributor-guide/contributor-guide-index.md) mi.

<!--HONumber=Aug16_HO1-->


