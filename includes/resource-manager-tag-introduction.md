Az Azure-erőforrások címkézésével logikus elv mentén rendszerezheti azokat, kategóriák szerint. Minden címke egy kulcsból és egy értékből áll. Alkalmazhatja például a „Környezet” kulcsot és az „Éles” értéket az összes éles üzemben használt erőforrásra. E nélkül a címke nélkül nehézségekbe ütközhet, ha meg szeretné állapítani, hogy az adott erőforrás fejlesztési, tesztelési vagy éles üzemi célokat szolgál. A „Környezet” és az „Éles” azonban csak például szolgál. Olyan kulcsokat és értékeket adjon meg, amelyek az előfizetés rendszerezésének szempontjából célszerűek.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke kulcsával és értékével. A címkékkel olyan kapcsolódó erőforrásokat is lekérhet, amelyek más erőforráscsoportokban találhatók. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkével rendelkezhet. 
* A címke neve legfeljebb 512 karakter hosszúságú lehet.
* A címke értéke legfeljebb 256 karakter hosszúságú lehet. 
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások. 



<!--HONumber=Feb17_HO1-->


