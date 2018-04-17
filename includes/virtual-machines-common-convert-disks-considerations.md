
* Az átalakításhoz újra kell indítani a virtuális gépet, ezért ütemezze a virtuális gépek migrálását egy meglévő karbantartási időszakra. 

* Az átalakítás nem vonható vissza. 

* Ne feledje, hogy bármely rendelkező felhasználók a [virtuális gép közreműködő](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör nem fogja tudni módosítani a Virtuálisgép-méretet (mivel azok sikerült az átalakítás előtti). Ennek az az oka felügyelt lemezzel rendelkező virtuális gépek a felhasználónak engedéllyel kell rendelkeznie a Microsoft.Compute/disks/write az OS lemezeken.

* Mindenképpen tesztelje az átalakítást. Migráljon egy tesztcélú virtuális gépet, mielőtt végrehajtja a migrálást éles környezetben.

* Az átalakítás közben felszabadítja a virtuális gépet. A virtuális gép új IP-címet kap, amikor elindul az átalakítás után. Szükség esetén [hozzárendelhet egy statikus IP-címet](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) a virtuális géphez.

* A virtuális gép által az átalakítás előtt használt eredeti virtuális merevlemezeket és a tárfiókot nem törli a rendszer. A futtatásuk továbbra is költségekkel jár. Annak érdekében, hogy ezekért az összetevőkért a továbbiakban ne fizessen, törölje az eredeti virtuálismerevlemez-blobokat, miután meggyőződött arról, hogy az átalakítás befejeződött.

* Nézze át az Azure Virtuálisgép-ügynök az átalakítási folyamat támogatásához szükséges minimális verzióját. Ellenőrizze és frissítse az ügynök verziója kapcsolatos információkért lásd: [VM-ügynökök az Azure-ban által támogatott minimális verzió](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)