

Néhány adatbázis munkaterhelések, például az SQL Server- vagy Oracle igényelnek nagy memória, tárolási, és i/o-sávszélesség, de nem egy magas magok száma. Nincsenek processzorigényes adatbázis futó számos szolgáltatásnak. Az Azure bizonyos Virtuálisgép-méretek, ahol a virtuális gépek vCPU száma szoftverlicencelés, miközben fenntartja az ugyanahhoz a memóriához, a tároló és az i/o műveletek sávszélességétől költségeinek csökkentése érdekében is megkötése kínál.

A vCPU száma is korlátozható a fele vagy egy negyedévében az eredeti virtuális gép méretét. Ezek új Virtuálisgép-méretek rendelkezik, amely meghatározza a könnyebb azonosítása érdekében aktív Vcpu számát utótaggal.

Ha például az aktuális virtuális gép mérete 32 Vcpu, 448 GB RAM memóriával rendelkezik Standard_GS5, 64 lemezek (legfeljebb 256 TB), és a 80000 IOPs, vagy a 2 GB/s i/o-sávszélesség. Az új virtuális gép méretének Standard_GS5-16, és Standard_GS5-8 tartalmaz 16 és 8 active Vcpu, a memória, tárolási és i/o-sávszélesség Standard_GS5 specifikációk részeinek megőrzésével.

A licencelési díjakat számítjuk fel az SQL Server- vagy Oracle csak korlátozottan az új vCPU számát, és más termékek kell fizetnie az új vCPU száma alapján. Az eredmény egy 50-75 % növekedése a virtuális gép specifikációk aránya az aktív (számlázható) Vcpu. Ezek új Virtuálisgép-méretek, amelyek csak Azure-ra, így a nagyobb mértékű CPU-használatot leküldéses: a Licencelés (/-core) tört költség munkaterhelések érhető el. Jelenleg a számítási költség szempontjából, amely tartalmazza az operációs rendszer licencelési, mint az eredeti méret azonos egy marad. További információkért lásd: [több költséghatékony adatbázis munkaterhelés Azure virtuális gép mérete](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Név                | vCPU | Adatok           |
|---------------------|------|-----------------|
| Standard_M64-32ms   | 32   | Ugyanaz, mint M64ms   |
| Standard_M64-16ms   | 16   | Ugyanaz, mint M64ms   |
| Standard_M128-64ms  | 64   | Ugyanaz, mint M128ms  |
| Standard_M128-32ms  | 32   | Ugyanaz, mint M128ms  |
| Standard_E32-16_v3  | 16   | Ugyanaz, mint E32s_v3 |
| Standard_E32-8s_v3  | 8    | Ugyanaz, mint E32s_v3 |
| Standard_E64-32s_v3 | 32   | Ugyanaz, mint E64s_v3 |
| Standard_E64-16s_v3 | 16   | Ugyanaz, mint E64s_v3 |
| Standard_GS4-8      | 8    | Ugyanaz, mint GS4     |
| Standard_GS4-4      | 4    | Ugyanaz, mint GS4     |
| Standard_GS5 – 16     | 16   | Ugyanaz, mint GS5     |
| Standard_GS5-8      | 8    | Ugyanaz, mint GS5     |
| Standard_DS13-4_v2  | 4    | Ugyanaz, mint DS13_v2 |
| Standard_DS13-2_v2  | 2    | Ugyanaz, mint DS13_v2 |
| Standard_DS14-8_v2  | 8    | Ugyanaz, mint DS14_v2 |
| Standard_DS14-4_v2  | 4    | Ugyanaz, mint DS14_v2 |