Azure rendszeres időközönként megbízhatóságát, teljesítményét és a virtuális gépek állomás infrastruktúra biztonságát javító frissítés. Ezen frissítések közé javítását szoftverösszetevőket. az üzemeltetési környezetben (például az operációs rendszer, hipervizor, és a gazdagépen rendszerbe állított különböző ügynökök), hálózati összetevők frissítése hardver leszerelése. A legtöbb, a frissítések a futtatott virtuális gépek számára gyakorolt hatás nélkül kerül sor. Vannak azonban esetekben, amikor frissítések ütközés:

- A karbantartási nem igényel újraindítást, ha Azure helyben történő áttelepítés használatával a virtuális gép felfüggesztése, amíg a gazdagép frissül.

- Ha karbantartási újraindítást igényel, kap értesítést, ha a karbantartási tervezett. Ezekben az esetekben akkor lesz is kell adni egy olyan időkeretet, ahol megkezdheti a karbantartási saját magának, megfelelő egyszerre.

Ez a lap ismerteti, hogyan Microsoft Azure-ban mindkét karbantartási típusú. Nem tervezett események (hosszabb idejű) kapcsolatos további információkért lásd: a virtuális gépek rendelkezésre állásának kezelése [Windows] (.. / articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).

A virtuális gépen futó alkalmazások jövőbeli frissítések információt tud gyűjteni az Azure metaadat-szolgáltatás használatával [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) vagy [Linux] (.. / articles/virtual-machines/linux/instance-metadata-service.md).

"" Útmutatót a tervezett maintence kezelését, tekintse meg "Kezelési tervezett karbantartás értesítések" [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) vagy [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="in-place-vm-migration"></a>Helyszíni virtuális gép áttelepítés

Ha a frissítéseket egy teljes számítógép újraindítása nem szükséges, egy helyszíni élő áttelepítés szolgál. A frissítés során a virtuális gép fel van függesztve, körülbelül 30 másodpercig, RAM, a memória megőrzi, amíg az üzemeltetési környezetben alkalmazza, a szükséges frissítések és javítások. A virtuális gép majd folytatja a működését, és a rendszer automatikusan szinkronizálja az óra, a virtuális gép.

Virtuális gépek a rendelkezésre állási csoportokban a frissítés tartományai frissített egyszerre csak egy. Frissítési tartományok (UD) virtuális gépeinek szünetel, frissítése és majd folytatása előtt a következő UD a tervezett karbantartások helyezi át.

Egyes alkalmazások negatív hatással lehet az ilyen típusú frissítések. Valós idejű Eseményfeldolgozási, például a médiaadatfolyam vagy az átkódolás vagy a magas teljesítmény forgatókönyvek, hálózati végző alkalmazások nem úgy tervezték, hogy egy 30 másodperces szünet működését. <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>A rendszer újraindítását igénylik karbantartás

Ha virtuális gépeket újra kell indítani a tervezett karbantartáshoz, értesítés jelenik meg előre. Tervezett karbantartás két szakasza van: az önkiszolgáló ablakot, és egy ütemezett karbantartási időszaknál.

A **önkiszolgáló ablak** lehetővé teszi, hogy kezdeményezni a virtuális gépeken karbantartási. Ebben az időszakban lekérheti az állapotát tekintheti meg, és ellenőrizze az utolsó karbantartási kérelem eredménye összes virtuális Géphez.

Önkiszolgáló karbantartási indításakor a virtuális gép átkerül egy csomópontra, amely már frissítve van, és majd bekapcsolja azt vissza. A virtuális gép újraindul, mert az ideiglenes lemez, és dinamikus, virtuális hálózati interfészhez társított IP-címek frissülnek.

Önkiszolgáló karbantartási indítja el, és a folyamat során hiba történik, ha a művelet le van állítva, a virtuális gép nem frissül, és a tervezett karbantartások iterációs is a rendszer eltávolítja. A rendszer kapcsolatba lépni a később egy új ütemezéssel rendelkező, majd felkínálja egy új lehetőséget önkiszolgáló karbantartás. 

Az önkiszolgáló időszak elteltével a **ütemezett karbantartási időszaknál** kezdődik. Időablak során továbbra is kereshet a karbantartási időszak, de már nem lehet elindítani a karbantartási magát.

## <a name="availability-considerations-during-planned-maintenance"></a>Tervezett karbantartás során a rendelkezésre állási lehetőségekért 

Ha úgy dönt, hogy a tervezett karbantartások megvárni, néhány szempontot kell figyelembe venni a virtuális gépek közül a legmagasabb availabilty megőrzéséhez. 

### <a name="paired-regions"></a>Párhuzamos régiók

Minden Azure-régió, egy másik ugyanazon a földrajzi régióját párosított, együtt egy regionális pár élnek. Tervezett karbantartás közben Azure csak frissíti a virtuális gépek régió pár egy régiót. Például ha az USA északi középső régiójában található virtuális gépeket frissíti, az Azure nem fogja frissíteni az USA déli középső régiójában található virtuális gépeket ugyanabban az időben. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. Ismertetése régió párok működése segítségével jobban elosztása a virtuális gépek régiók. További információkért lásd: [az Azure-régió párok](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Rendelkezésre állási készletek és a méretezési csoportok

A munkaterhelés Azure virtuális gépeken való telepítésekor a virtuális gépek rendelkezésre állási készlet magas rendelkezésre állás, az alkalmazás belül is létrehozhat. Ez biztosítja, hogy szolgáltatáskimaradás vagy karbantartási események, legalább egy virtuális gép érhető el.

Egy rendelkezésre állási csoportot belül az egyes virtuális gépek legfeljebb 20 frissítési tartományok (UDs) vannak elosztva. Tervezett karbantartás közben csak egyetlen frissítési tartományi egy adott időpontban van hatással. Vegye figyelembe, hogy a frissítési tartományok befolyásolja sorrendjét nem feltétlenül fordulhat elő egymás után. 

Virtuálisgép-méretezési csoportok olyan Azure számítási erőforrás, amely lehetővé teszi, akkor helyezheti üzembe és felügyelheti az azonos virtuális gépek készletét, amely egyetlen erőforrásra. A méretezési automatikusan telepíti a frissítési tartományokon, például a virtuális gépek rendelkezésre állási csoportba. Csakúgy, mint a rendelkezésre állási csoportok esetében a méretezési csoportok csak egyetlen frissítési tartományi van hatással az adott időpontban.

A magas rendelkezésre állású virtuális gépek konfigurálásával kapcsolatos további információkért tekintse meg a virtuális gépek rendelkezésre állásának kezelése a [Windows](../articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).
