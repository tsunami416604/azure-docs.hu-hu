Kétféle tárfióktípus létezik:

### <a name="general-purpose-storage-accounts"></a>Általános célú tárfiókok
Egy általános célú tárfiók olyan Azure Storage-szolgáltatásokhoz biztosít hozzáférést egyetlen fiókban, mint a Tables, a Queues, a Files, a Blobs és az Azure virtuális gép lemezei. Ez a tárfióktípus két teljesítményszinttel rendelkezik:

* Egy standard tárolási teljesítményszinttel, amely a táblák, üzenetsorok, fájlok, blobok és Azure virtuálisgép-lemezek tárolását teszi lehetővé.
* Egy prémium szintű Storage teljesítményszinttel, amely jelenleg csak az Azure virtuális gépek lemezeit támogatja. A Premium Storage részletesebb áttekintéséért lásd: [Premium Storage: High-performance Storage for Azure Virtual Machine Workloads](../articles/virtual-machines/windows/premium-storage.md) (Premium Storage: Nagy teljesítményű tárterület az Azure virtuális gépek számítási feladataihoz).

### <a name="blob-storage-accounts"></a>Blob Storage-fiókok
A Blob Storage-fiók egy speciális tárfiók a strukturálatlan adatok blobként (objektumként) való tárolására az Azure Storage-ban. A Blob Storage-fiókok hasonlóak a meglévő általános célú tárfiókjaihoz, és a jelenlegi rendszereivel megegyező szintű tartósságot, rendelkezésre állást, méretezhetőséget és teljesítményt nyújtanak, beleértve a 100%-os API-konzisztenciát a blokkblobokhoz és a hozzáfűző blobokhoz. A csak blokkok és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát.

> [!NOTE]
> A Blob Storage-fiókok csak a blokkblobokat és a hozzáfűző blobokat támogatják, a lapblobokat nem.
> 
> 

A Blob Storage-fiókokban elérhető a **Hozzáférési szint** attribútum, amely a fiók létrehozásakor adható meg, később pedig szükség szerint módosítható. Kétféle hozzáférési szint adható meg az adathozzáférési minták alapján:

* Egy **Gyakori** hozzáférési szint, amely a tárfiókban tárolt objektumok gyakoribb elérésére utal. Az adatait így alacsonyabb hozzáférési költség mellett tárolhatja.
* Egy **Ritka** hozzáférési szint, amely a tárfiókban tárolt objektumok ritkább elérésére utal. Az adatait így alacsonyabb adattárolási költség mellett tárolhatja.

Ha változik az adatok használati módja, bármikor válthat a hozzáférési rétegek közt. A hozzáférési rétegek módosítása további díjakat vonhat maga után. További részletek: [Blob Storage-fiókok – Árak és számlázás](../articles/storage/common/storage-account-options.md#pricing-and-billing).

Blob Storage-fiókok további részletei: [Azure Blob Storage: Cool and Hot tiers](../articles/storage/blobs/storage-blob-storage-tiers.md) (Azure Blob Storage: Ritka és Gyakori hozzáférési szintek).

Ahhoz, hogy létrehozhasson egy tárfiókot, Azure-előfizetéssel kell rendelkeznie. Ez a csomag számos különböző Azure-szolgáltatáshoz biztosít hozzáférést. Az Azure használatát egy [ingyenes fiók](https://azure.microsoft.com/pricing/free-trial/) létrehozásával is elkezdheti. Ha úgy dönt, hogy szeretne előfizetési csomagot vásárolni, több [vásárlási lehetőség](https://azure.microsoft.com/pricing/purchase-options/) közül választhat. Ha Ön [MSDN-előfizető](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), ingyenes havi krediteket kap, amelyeket olyan Azure-szolgáltatásokhoz használhat, mint az Azure Storage. A mennyiségi díjszabásról lásd: [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

A tárfiók létrehozásával kapcsolatos részleteket lásd: [Create a storage account](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) (Tárfiók létrehozása). Akár 200 egyedi névvel ellátott tárfiókot is létrehozhat egyetlen előfizetéssel. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../articles/storage/common/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménykorlátai).

