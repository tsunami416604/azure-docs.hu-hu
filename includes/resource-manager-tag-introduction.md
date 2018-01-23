Az Azure-erőforrások címkézésével logikus elv mentén rendszerezheti azokat, kategóriák szerint. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra. E nélkül a címke nélkül nehézségekbe ütközhet, ha meg szeretné állapítani, hogy az adott erőforrás fejlesztési, tesztelési vagy éles üzemi célokat szolgál. A „Környezet” és az „Éles” azonban csak például szolgál. Olyan neveket és értékeket adjon meg, amelyek az előfizetés rendszerezésének szempontjából célszerűek.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. A címkékkel olyan kapcsolódó erőforrásokat is lekérhet, amelyek más erőforráscsoportokban találhatók. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik. Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-karakterláncot a címke értékéhez. A JSON-karakterlánc sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Ez a cikk arra mutat be egy példát, hogyan rendelhető hozzá egy JSON-karakterlánc a címkéhez.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* Ezek a karakterek nem támogatottak:
  * `<`
  * `>`
  * `%`
  * `&`
  * `\\`
  * `?`
  * `/`
