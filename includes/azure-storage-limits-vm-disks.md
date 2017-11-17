Az Azure virtuális gépek több adatlemez csatolását támogatták. Ez a cikk ismerteti a virtuális gép adatlemezek méretezhetőségi és Teljesítménycélok. Ezeken a célokon a segítségével meghatározhatja a számát és típusát, amelyekre szüksége van a teljesítmény és kapacitás követelményeinek megfelelően lemez. 

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozza a erősen túlterhelt lehetséges szabályozás elkerülése érdekében a virtuális géphez csatolt lemezek számát. A csatlakoztatott lemezek magas nem használhatók egyszerre, ha a virtuális gép lemezek nagyobb számú is támogatja.

* **Az Azure Managed lemezek:** a lemez felügyelt lemezek határa régiónként, valamint minden lemez típusa. A maximális száma, valamint az alapértelmezett határérték 10 000 felügyelt lemezek régiónként és lemez típusonkénti az előfizetéshez. Például létrehozhat legfeljebb 10 000 standard szintű felügyelt lemez és a is 10 000 prémium által kezelt lemezeken régióban, előfizetésenként.

    Felügyelt pillanatfelvételek és lemezképeket, csökkenti a felügyelt lemezek korlátot.

* **Standard szintű tárfiókok esetén:** A Standard szintű tárfiókok összesen legfeljebb 20 000 IOPS kérelemaránnyal rendelkeznek. A Standard szintű tárfiókokon az összes virtuálisgép-lemezen lévő teljes IOPS nem léphet túl ezen a korláton.
  
    Nagyjából kiszámíthatja az egyetlen Standard szintű tárfiók által támogatott nagy kihasználtságú lemezek számát a kérelemarány korlátja alapján. Például egy alapszintű rétegben VM, erősen túlterhelt lemezek maximális száma érték a 66 (20 000/300 iops-érték lemezenként), és a Standard szint virtuális gépek, akkor hamarosan 40 (20 000 500 iops-érték lemezenként). 

* **Prémium szintű tárfiókok esetén:** A prémium szintű tárfiókok összesen legfeljebb 50 Gbps átviteli sebességgel rendelkeznek. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

