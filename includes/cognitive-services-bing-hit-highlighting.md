Bing támogatja kiemelve, amely jelöli meg a lekérdezési kifejezések találati aránya (vagy egyéb feltételeket, hogy a Bing talál megfelelő) az egyes a kérdésekre adott válaszokat a megjelenítési karakterláncot. Például egy weboldal `name`, `displayUrl`, és `snippet` mezők előfordulhat, hogy jelölje meg a lekérdezési kifejezések.

Alapértelmezés szerint a Bing nem tartalmazza a jelölők a megjelenített karakterláncok kiemelve. A jelölők tartalmazza, tartalmazza a `textDecorations` lekérdezésparaméter a kérelmét, és állítsa az értékét **igaz**. Bing a lekérdezési kifejezések E000 és E001 Unicode karakterek használata a elején és végén lévő kifejezés szabadként jelöli meg. Például ha a lekérdezés kifejezés induló utazó Dinghy és a mező szerepel vagy kifejezés, kifejezés szimpla találati kijelölő karakterek az alábbi példában látható módon:  
  
![Elérte a kiemelve](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

A karakterlánc megjelenítése a felhasználói felületen, előtt a Unicode-karaktereket volna cserélje karakterek, amelyek a megjelenítési formátuma. Például ha a szöveg HTML formátumban, előfordulhat, hogy kijelöl lekérdezési kifejezés tartományvezérlőkkel történő lecserélésével E000 a < b\> és a E001 < /b\>. Ha nem kívánja alkalmazni a formázás, távolítsa el a jelölők a karakterláncot. 

Bing Unicode-karaktereket vagy a HTML-címkék használata a jelölők lehetőséget biztosít. Adja meg, melyik jelölők használni, adja meg a `textFormat` lekérdezési paraméter. Jelölje meg a tartalom Unicode-karakteres, állítsa `textFormat` Raw (alapértelmezett), és jelölje meg a tartalmat a HTML-címkéket, állítsa be `textFormat` HTML-re. 
  
Ha `textDecorations` van **igaz**, a Bing tartalmazhatják a következő jelölők a megjelenített karakterláncok válaszok. Ha nincs HTML egyenértékű, a HTML-tábla cella mező üres.

|Unicode|HTML|Leírás
|-|-|-
|U + E000|\<b >|A lekérdezési kifejezésre (találatok kiemelése) kezdetét jelöli
|U + E001|\</b >|A lekérdezés kifejezés végét jelöli
|U + E002|\<i >|Dőlt betűvel tartalom kezdetét jelöli 
|U + E003|\</i >|Dőlt betűvel tartalom végét jelöli
|U + E004|\<br / >|Sortörés jelöli meg
|U + E005||A telefonszám kezdetét jelöli
|U + E006||A telefonszám végét jelöli
|U + E007||Cím kezdetét jelöli
|U + E008||Egy címet végét jelöli
|U + E009|\&nbsp;|Nem törhető szóközzel jelöli meg
|U + E00C|\<erős >|A félkövér betűvel írott tartalom kezdetét jelöli
|U + E00D|\</ strong >|A félkövér betűvel írott tartalom végét jelöli
|U + E00E||Tartalom, amelynek háttér kell lennie a környező háttér világosabb kezdetét jelöli
|U + E00F||Tartalom, amelynek háttér kell lennie a környező háttér világosabb végét jelöli
|U + E010||Tartalom, amelynek háttér kell lennie a környező háttér sötétebbek kezdetét jelöli
|U + E011||Tartalom, amelynek háttér kell lennie a környező háttér sötétebbek végét jelöli
|U + E012|\<del >|Át kell húzni tartalom kezdetét jelöli
|U + E013|\</ del >|Át kell húzni tartalom végét jelöli
|U + E016|\<Sub >|Alsó tartalom kezdetét jelöli
|U + E017|\</ sub >|Alsó tartalom végét jelöli
|U + E018|\<sup >|Felső tartalom kezdetét jelöli
|U + E019|\</ sup >|Felső tartalom végét jelöli

Az alábbi példa mutatja egy `Computation` választ, amely tartalmazza az alsó jelölők log(2) lekérdezés időszakra. A `expression` mezőben a jelölők csak akkor, ha `textDecoration` van **igaz**.

![számítási jelölők](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

A kérelem nem kért díszítés, ha a kifejezés log10(2) lesz. 
  
