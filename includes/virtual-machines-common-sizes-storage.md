Tárolási optimalizált Virtuálisgép-méretek nagy átviteli sebesség és IO, és alkalmasak Big Data típusú adatok, az SQL és a NoSQL-adatbázisok. Ez a cikk tájékoztatást ad azokról a Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség minden méretéhez ennél a csoportosításnál száma. 

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.  

## <a name="ls-series"></a>Ls-sorozat

ACU: 180–240
 
| Méret          | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális ideiglenes tárolási teljesítmény: IOPS / MB/s | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 8    | 20,000 / 200   | 10,000 / 250        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 16   | 40,000 / 400   | 20,000 / 500       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 32   | 80,000 / 800   | 40,000 / 1,000       | 8 / 6,000 - 16,000 &#8224; | 
| Standard_L32s* | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 80,000 / 2,000     | 8 / 20,000 | 
 

Az Ls-sorozatba tartozó virtuális gépek lemezeinek maximális átviteli sebességét a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket. 

*A példány ügyfelenként külön dedikált hardveren üzemel.

