## <a name="deployment-considerations"></a>Telepítési szempontok

* N sorozatú virtuális gépek rendelkezésre állását, lásd: [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/en-us/regions/services/).

* N sorozatú virtuális gépek csak a Resource Manager üzembe helyezési modellel is telepíthető.

* N sorozatú virtuális gépek nem egyezik a támogatják a lemezek Azure tárolási típusát. NC és portok HV virtuális gépek csak támogatja a virtuális gépek lemezei, amelyekről által a szabványos lemez tárolási (HDD). NCv2 ND és NCv3 (előzetes) virtuális gépek csak támogató virtuális gép lemeze, amely a prémium szintű lemez tárolási (SSD) által támogatott.

* Ha azt szeretné, csak néhány N sorozatú virtuális gépek telepítéséhez, fontolja meg a használatalapú előfizetés vagy egyéb beszerzési lehetőségek. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* Szükség lehet a magok kvótája (régiónként) az Azure-előfizetése, és a hálózati vezérlő által, NCv2, ND vagy portok HV magok külön kvóta növeléséhez. A kvóta növelését [nyissa meg az online támogatás ügyfélkérés](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. Alapértelmezett korlátokat eltérhetnek attól függően, hogy az előfizetés kategóriát.

* Egy virtuális gép lemezképe N sorozatú virtuális gépeken telepítheti a [Azure adatok tudományos virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md). Az adatok tudományos virtuális gép előtelepíti, és számos népszerű adattudomány és eszközök tanulási mély konfigurálja. Azt is előtelepíti NVIDIA Tesla GPU-illesztőprogramokat.





