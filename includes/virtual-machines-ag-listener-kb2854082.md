Ezután ha olyan kiszolgálókat a fürt Windows Server 2008 R2 vagy Windows Server 2012 rendszert futtatnak, ellenőriznie kell, amely a gyorsjavítás [KB2854082](http://support.microsoft.com/kb/2854082) telepítve van a helyszíni kiszolgálók és az Azure virtuális gépeken, amelyek a fürt részei. A kiszolgáló vagy a virtuális Gépet, amely a fürt, de nem található a rendelkezésre állási csoport lehet ez a gyorsjavítás.

Töltse le a távoli asztali munkamenet az egyes fürtcsomópontokon, [KB2854082](http://support.microsoft.com/kb/2854082) egy helyi könyvtárba. Ezután telepítse a gyorsjavítást a fürt minden csomópontján egymás után. Ha a fürtcsomópont jelenleg fut a fürtszolgáltatás, a kiszolgáló újraindítása a gyorsjavítás telepítése végén.

> [!WARNING]
> A fürtszolgáltatás leállítása vagy a kiszolgáló újraindítása hatással van a kvórum a fürt és a rendelkezésre állási csoport állapotát, és előfordulhat, hogy a fürt offline állapotú. A fürt magas rendelkezésre állását fenntartásához telepítése során, győződjön meg arról, hogy:
> 
> * A fürt nem optimális kvórum állapotát. 
> * Mielőtt telepítené a gyorsjavítás bármely csomópontján, a fürt összes csomópontján online állapotban.
> * A gyorsjavítás telepítése a fürt többi csomópontjára, mielőtt futtatni a létrehozása után egy csomóponton, beleértve a teljes mértékben az a kiszolgáló újraindítása gyorsjavítás telepítésének engedélyezése.
> 
> 

