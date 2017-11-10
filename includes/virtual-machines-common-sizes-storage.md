Tárolási optimalizált Virtuálisgép-méretek nagy átviteli sebesség és IO, és alkalmasak Big Data típusú adatok, az SQL és a NoSQL-adatbázisok. Ez a cikk ismerteti a Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati teljesítményt minden méretéhez ennél a csoportosításnál száma. 

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.  

## <a name="ls-series"></a>Ls-sorozat

ACU: 180–240
 
| Méret          | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA / NA (0)          | 5,000 / 125                               | 2 / 4000       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | NA / NA (0)          | 10,000 / 250                              | 4 / 8000  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | NA / NA (0)          | 20,000 / 500                              | 8 / 6000 - 16000 &#8224; | 
| Standard_L32s* | 32 | 256  | 5,630 | 64             | NA / NA (0)          | 40,000 / 1,000                            | 8 / 20000 | 
 

Az Ls-sorozatba tartozó virtuális gépek lemezeinek maximális átviteli sebességét a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket. 

*A példány ügyfelenként külön dedikált hardveren üzemel.

