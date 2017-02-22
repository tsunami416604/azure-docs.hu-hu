Az Azure virtuális gépek több adatlemez csatolását támogatták. Az optimális teljesítmény érdekében érdemes korlátozni a virtuális géphez csatlakoztatott nagy kihasználtságú lemezek számát a lehetséges szabályozás elkerülése érdekében. Ha nincs az összes nagy kihasználtságú lemez egyszerre használatban, a tárfiók több lemezt tud támogatni.

* **Azure Managed Disks esetén:** A felügyelt lemezek számának korlátja az előfizetés régiójától függ. Az alapértelmezett enyhe korlát 2000. A korlát növelésével kapcsolatban vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

    A felügyelt pillanatképeket és rendszerképeket a felügyelt lemezek korlátjához képest számítja a rendszer.

* **Standard szintű tárfiókok esetén:** A Standard szintű tárfiókok összesen legfeljebb 20 000 IOPS kérelemaránnyal rendelkeznek. A Standard szintű tárfiókokon az összes virtuálisgép-lemezen lévő teljes IOPS nem léphet túl ezen a korláton.
  
    Nagyjából kiszámíthatja az egyetlen Standard szintű tárfiók által támogatott nagy kihasználtságú lemezek számát a kérelemarány korlátja alapján. Az alapszintű csomagba tartozó virtuális gépek esetén például a nagy kihasználtságú lemezek maximális száma körülbelül 66 (20 000/300 IOPS lemezenként), a standard csomagba tartozó virtuális gépek esetén pedig körülbelül 40 (20 000/500 IOPS lemezenként) az alábbi táblázatban láthatók szerint. 
* **Prémium szintű tárfiókok esetén:** A prémium szintű tárfiókok összesen legfeljebb 50 Gbps átviteli sebességgel rendelkeznek. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.



<!--HONumber=Feb17_HO2-->


