| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az előfizető számára régiónként rendelkezésre álló Batch-fiókok | 3 |50 |
| Magok száma (a Batch szolgáltatás mód) a Batch-fiók dedikált<sup>1</sup> | 20 | N/A<sup>2</sup> |
| Alacsony prioritású magok száma (a Batch szolgáltatás mód) a Batch-fiókhoz<sup>3</sup> | 50 | N/A<sup>4</sup> |
| Aktív feladatok és a feladatok ütemezésének<sup>5</sup> / Batch-fiókhoz. | 20 | 5000<sup>6</sup> |
| Készletek száma Batch-fiókonként | 20 | 2500 |

<sup>1</sup> látható dedikált core kvóták vannak, csak az alkalmazáskészlet foglalási üzemmóddal fiókok **a Batch szolgáltatás**. A fiókok móddal **felhasználói előfizetési**, core kvóták alapuló Virtuálisgép-magok kvótájának regionális szinten, vagy a virtuális gép termékcsalád az előfizetésében.

<sup>2</sup> Batch-fiók dedikált magok száma növelhető, de nincs megadva a maximális számát. Lépjen kapcsolatba az Azure támogatási növekedése lehetőségeket tárgyalja.

<sup>3</sup> alacsony prioritású core kvóták látható vannak, csak az alkalmazáskészlet foglalási üzemmóddal fiókok **a Batch szolgáltatás**. Alacsony prioritású magok nem érhetők el, az alkalmazáskészlet foglalási üzemmóddal fiókok állítsa **felhasználói előfizetési**.

<sup>4</sup> Batch-fiók alacsony prioritású magok száma növelhető, de nincs megadva a maximális számát. Lépjen kapcsolatba az Azure támogatási növekedése lehetőségeket tárgyalja.

<sup>5</sup> befejezett feladatok és a feladatok ütemezésének nem korlátozottak.

<sup>6</sup> forduljon Azure támogatási szolgálatához, ha azt szeretné, hogy a korlátozás megnövelésére meghaladja ezt a határt.
