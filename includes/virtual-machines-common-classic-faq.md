---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 37dc96cf965181c5acba74449c684a08035e37b2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735563"
---
Ez a cikk néhány, a felhasználók által a klasszikus üzemi modellel létrehozott Azure-beli virtuális gépekkel kapcsolatban gyakran feltett kérdéssel foglalkozik.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>Át tudom telepíteni a klasszikus üzemi modellben létrehozott virtuális gépeket az új Resource Manager-modellbe?
Igen. A migrálással kapcsolatos utasításokért lásd:

* [Áttelepítés a klasszikusból Resource Manager-alapú környezetbe az Azure PowerShell használatával](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Áttelepítés a klasszikusból Resource Manager-alapú környezetbe az Azure CLI használatával](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. Futtathatja a Windows Server legújabb verzióit, valamint különböző Linux-disztribúciókat. Támogatási részletek:

• Windows rendszerű virtuális gépek – [Microsoft kiszolgálószoftveres támogatás az Azure Virtual Machines szolgáltatáshoz](https://go.microsoft.com/fwlink/p/?LinkId=393550)

• Linux rendszerű virtuális gépek – [Linux az Azure által támogatott disztribúciókon](https://go.microsoft.com/fwlink/p/?LinkId=393551)

A Windows-alapú rendszerképeknél fejlesztési és tesztelési feladatokhoz a Windows 7 és a Windows 8.1 bizonyos verziói elérhetők az MSDN Azure előfizetői előnyöket és az MSDN fejlesztés/tesztelés – használatalapú fizetést választó előfizetők számára. Részletekért, többek között az utasításokért és korlátozásokért tekintse meg az [MSDN-előfizetők számára elérhető Windows-rendszerképeket](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) ismertető cikket.

## <a name="why-are-affinity-groups-being-deprecated"></a>Miért avultak el az affinitáscsoportok?
Az affinitáscsoport egy örökölt fogalom egy ügyfél felhőszolgáltatási környezeteinek és tárfiókjainak az Azure-on belüli földrajzi csoportosítására. Eredetileg a virtuális gépek közötti hálózati teljesítmény növelésére szolgáltak a korai Azure-hálózati kialakításokban. Támogatták a virtuális hálózatok (VNetek) eredeti kiadását is, amelyek egy régión belül a hardverek egy kisebb készletére voltak korlátozva.

A régión belüli aktuális Azure-hálózat úgy van megtervezve, hogy az affinitáscsoportokra többé nincs szükség. A virtuális hálózatok szintén regionális hatókörben vannak megtervezve, így nem szükséges affinitáscsoport egy virtuális hálózat használatához. Ezeknek a fejlesztéseknek köszönhetően már nem ajánljuk ügyfeleink számára az affinitáscsoportok használatát, mivel ezek néhány esetben felesleges korlátozásokkal járhatnak. Az affinitáscsoportok használata feleslegesen társítja a virtuális gépeket egy adott hardverhez, ami korlátozza az elérhető virtuálisgép-méretek kiválasztását. Ez kapacitással kapcsolatos hibákhoz is vezethet új virtuális gépek hozzáadásakor, ha az affinitáscsoporttal társított adott hardver megközelítette a maximális kapacitását.

Az affinitáscsoport-funkciók már elavultnak számítanak az Azure Resource Manager-alapú üzemi modellben és az Azure Portalon. A klasszikus Azure Portal esetében kivezetjük az affinitáscsoportok létrehozásának és az affinitáscsoporthoz rögzített tároló-erőforrások létrehozásának támogatását. Az affinitáscsoportot használó meglévő felhőszolgáltatásokat nem kell módosítania. Azonban ne használjon affinitáscsoportokat új felhőszolgáltatásokhoz, kivéve, ha egy Azure-terméktámogatási szakértő ezt javasolja.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Minden adatlemez akár 1 TB méretű is lehet. A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az Azure-tárfiók tárhelyet biztosít az operációsrendszer-lemez és bármely adatlemez számára. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Milyen típusú virtuális merevlemezeket használhatok?
Az Azure csak a rögzített méretű, VHD formátumú virtuális merevlemezek használatát támogatja. Ha rendelkezik olyan VHDX-szel, amelyet szeretne használni az Azure-ban, először konvertálnia kell azt a Hyper-V Manager vagy a [convert-VHD](https://go.microsoft.com/fwlink/p/?LinkId=393656) parancsmag használatával. Miután ezt megtette, használja az [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) parancsmagot (Szolgáltatásfelügyelet módban) a VHD egy Azure-ban található tárfiókba való feltöltésére, így azt használhatja a virtuális gépekkel.

* A Linuxra vonatkozó utasításokért tekintse meg a [Linux operációs rendszert tartalmazó virtuális merevlemez létrehozását és feltöltését](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) ismertető témakört.

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Ezek a virtuális gépek ugyanazok, mint a Hyper-V-beli virtuális gépek?
Több szempontból hasonlítanak az 1. generációs Hyper-V-beli virtuális gépekre, de nem teljesen azonosak. Mindkét típus virtuális hardvert biztosít és a VHD formátumú virtuális merevlemezekkel kompatibilis. Ez azt jelenti, hogy áthelyezheti őket a Hyper-V és az Azure között. Az alábbi három fő különbség azonban sokszor meglepetésként éri a Hyper-V-felhasználókat:

* Az Azure nem biztosít konzolos hozzáférést a virtuális gépekhez. A virtuális géphez nem lehet hozzáférni az indítás befejezése előtt.
* Az Azure-beli virtuális gépek a legtöbb [méretben](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) csak 1 virtuális hálózati adapterrel rendelkeznek, ami azt jelenti, hogy csak 1 külső IP-címmel rendelkezhetnek. (Az A8 és A9 méretűek – konkrét körülmények esetén – egy második hálózati adaptert használnak a példányok közötti alkalmazáskommunikációhoz.)
* Az Azure-beli virtuális gépek nem támogatják a 2. generációs Hyper-V-beli virtuálisgép-funkciókat. A funkciókkal kapcsolatban lásd a [virtuális gépek műszaki adatait Hyper-V használata estén](https://technet.microsoft.com/library/dn592184.aspx) és a [2. generációs virtuális gépek áttekintését](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Használhatják ezek a virtuális gépek a meglévő, helyszíni hálózati infrastruktúrát?
A klasszikus üzemi modellben létrehozott virtuális gépek esetében használhatja az Azure Virtual Networköt a meglévő infrastruktúra kiterjesztésére. A módszer a fiókirodák létrehozásához hasonlít. Az Azure-ban létrehozhat és kezelhet virtuális magánhálózatokat (VPN-eket), valamint biztonságosan csatlakoztathatja ezeket egy helyszíni informatikai infrastruktúrához. További részleteket a [virtuális hálózatok áttekintésében](../articles/virtual-network/virtual-networks-overview.md) talál.

A virtuális gép létrehozásakor meg kell adnia azt a hálózatot, amelyhez a virtuális gép tartozni fog. Meglévő virtuális gépet nem csatlakoztathat egy virtuális hálózathoz. Ezt azonban megkerülheti úgy, hogy leválasztja a virtuális merevlemezt (VHD) a meglévő virtuális gépről, majd egy új, a kívánt hálózati konfigurációval rendelkező virtuális gép létrehozásához használja.

## <a name="how-can-i-access--my-virtual-machine"></a>Hogyan érhetem el a virtuális gépem?
Jelentkezzen be a virtuális géphez a távoli asztali kapcsolat egy Windows virtuális gép vagy egy Secure Shell (SSH) a Linux rendszerű virtuális gép távoli kapcsolatot létesíteni szükség. További útmutatásért lásd:

* [Bejelentkezés egy Windows Servert futtató virtuális gépre](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Legfeljebb 2 párhuzamos kapcsolat támogatott, kivéve, ha a kiszolgáló Távoli asztali szolgáltatások-munkamenetgazdaként van konfigurálva.  
* [Bejelentkezés egy Linuxot futtató virtuális gépre](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Alapértelmezés szerint az SSH legfeljebb 10 párhuzamos kapcsolatot tesz lehetővé. Ezt a számot a konfigurációs fájl szerkesztésével növelheti.

Ha problémákat tapasztal a Távoli asztallal vagy az SSH-val kapcsolatban, telepítse és használja a [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bővítményt a probléma kijavításához.

Windows rendszerű virtuális gépek esetén a további lehetőségek is a rendelkezésére állnak:

* Az Azure Portalon keresse meg a virtuális Gépet, majd kattintson a **távelérés visszaállítása** a parancssávon.
* Tekintse át a [távoli asztali kapcsolatok Windows-alapú virtuális gépeken történő hibaelhárításával](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) foglalkozó témakört.
* Használja a Windows PowerShell távoli eljáráshívást a virtuális géphez való csatlakozásra, vagy hozzon létre további végpontokat egyéb erőforrások számára a virtuális géphez való csatlakozáshoz. Részletekért lásd a [végpontok virtuális gépekhez történő beállítását](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.

Ha járatos a Hyper-V használatában, akkor előfordulhat, hogy egy, a VMConnecthez hasonló eszközt szeretne használni. Az Azure nem biztosít hasonló eszközt, mivel nem támogatja a virtuális gépek konzolos elérését.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>Használhatom az ideiglenes lemezt (Windows esetében a D: meghajtó, Linux esetében a /dev/sdb1) adatok tárolására?
Ne használja az ideiglenes lemezt (Windows esetében a D: meghajtó, Linux esetében /dev/sdb1) adatok tárolására. Ezek csak ideiglenes tárak, és ha így tesz, azt kockáztatja, hogy az adatok elvesznek, és nem lehet őket helyreállítani. Ez akkor történhet meg, ha a virtuális gépet egy másik gazdagépre helyezik át. A virtuális gép átméretezése, a gazdagép frissítése vagy a gazdagépen bekövetkezett hardverhiba lehet az oka egy virtuális gép áthelyezésének.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hogyan módosíthatom egy ideiglenes lemez meghajtóbetűjelét?
Windows rendszerű virtuális gépeken a meghajtó betűjelét a lapozófájl áthelyezésével és a meghajtóbetűjel újbóli hozzárendelésével módosíthatja, de győződjön meg arról, hogy a lépéseket adott sorrendben hajtja végre. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Hogyan frissíthető a vendég operációs rendszer?
A frissítés kifejezés általában azt jelenti, hogy az operációs rendszerének egy újabb kiadására tér át, miközben ugyanazt a hardvert használja tovább. Az Azure-beli virtuális gépeken az újabb kiadásra való váltás Linux és Windows rendszeren eltérő:

* Linuxos virtuális gépeken használja a disztribúciónak megfelelő csomagkezelő eszközöket és eljárásokat.
* Windowsos virtuális gépeken a kiszolgálót a Windows Server áttelepítési eszközökkel vagy hasonló eszközökkel migrálhatja. Ne kísérelje meg a vendég operációs rendszer frissítését, amíg a gép az Azure-ban található. Ez nem támogatott, mivel így fennállna a kockázat, hogy elveszíti a virtuális géphez való hozzáférést. Ha problémák lépnek fel a frissítés közben, előfordulhatna, hogy nem tud távoli asztali munkamenetet indítani, és nem tudná elhárítani a problémákat.

Általános adatok a Windows Server migrálásának eszközeiről és folyamatairól: [Szerepkörök és szolgáltatások áttelepítése Windows Serverre](https://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Mi az alapértelmezett felhasználónév és jelszó a virtuális gépen?
Az Azure által biztosított rendszerképek nem rendelkeznek előre konfigurált felhasználónévvel és jelszóval. Amikor hoz létre virtuális gépet egy ilyen rendszerképpel, szüksége, adjon meg egy felhasználónevet és jelszót, amely segítségével jelentkezzen be a virtuális gép.

Ha elfelejtette a felhasználónevet vagy jelszót, és telepítette a VM Agent szolgáltatást, a [VMAcess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bővítmény telepítésével és használatával megoldhatja a problémát.

További részletek:

* A Linux-lemezképekhez Ha használja az Azure Portalon, alapértelmezett felhasználónév "azureuser" van megadva, de ezt módosíthatja úgy a "Katalógusból" helyett "Gyors létrehozás" segítségével hozza létre a virtuális gépet is. A „Katalógusból” használatával ezenkívül eldöntheti, hogy a bejelentkezéshez jelszavat, SSH-kulcsot vagy mindkettőt kívánja használni. A felhasználói fiók egy nem kiemelt jogosultságú felhasználó, aki „sudo” hozzáféréssel rendelkezik a kiemelt jogosultságot igénylő parancsok futtatásához. A „root” fiók le van tiltva.
* Windows-rendszerképek esetén meg kell adnia egy felhasználónevet és jelszót a virtuális gép létrehozásakor. A rendszer hozzáadja a fiókot a Rendszergazdák csoporthoz.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Tud víruskeresőt futtatni az Azure a virtuális gépeimen?
Az Azure számos víruskereső megoldást kínál, azonban a kezelésük az Ön feladata. Például egy külön előfizetésre lehet szüksége egy kártevőirtó szoftverhez, és Önnek kell eldöntenie, hogy mikor futtat vizsgálatokat és telepít frissítéseket. A Microsoft Antimalware, a Symantec Endpoint Protection vagy a TrendMicro Deep Security Agent VM-bővítményével a windowsos virtuális gép létrehozásakor vagy később is biztosíthatja a víruskereső-támogatást. A Symantec és a TrendMicro bővítményeit ingyenes korlátozott idejű próbaverziós előfizetéssel vagy egy meglévő vállalati előfizetéssel használhatja. A Microsoft Antimalware ingyenes. Részletes információ:

* [A Symantec Endpoint Protection telepítése és konfigurálása Azure-beli virtuális gépen](https://go.microsoft.com/fwlink/p/?LinkId=404207)
* [A Trend Micro Deep Security telepítése és konfigurálása szolgáltatásként Azure-beli virtuális gépen](https://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Milyen biztonsági mentési és helyreállítási lehetőségek közül választhatok?
Az Azure Backup bizonyos régiókban elérhető előzetes verzióként. További részletek: [Azure-beli virtuális gépek biztonsági mentése](../articles/backup/backup-azure-arm-vms.md). Hivatalos partnereinktől egyéb megoldások is elérhetők. A jelenleg elérhető szolgáltatásokat keresse az Azure Marketplace-en.

Egy másik lehetőség a blobtároló pillanatfelvétel funkciójának használata. Ehhez le kell állítania a virtuális gépet, mielőtt bármilyen műveletet végez, amely blob-pillanatképeket használ. Ez menti a függőben lévő adatírásokat, és konzisztens állapotba helyezi a fájlrendszert.

## <a name="how-does-azure-charge-for-my-vm"></a>Hogyan számítja az Azure a virtuális gép díjszabását?
Az Azure óradíjas rendszerben számláz, a virtuális gép mérete és az operációs rendszer alapján. A megkezdett órák esetében az Azure percalapú elszámolást biztosít. Ha a virtuális gépet bizonyos előre telepített szoftvereket tartalmazó rendszerképből telepíti, további óránkénti szoftverdíjak merülhetnek fel. Az Azure külön számláz a virtuális gép operációs rendszerének és adatlemezeinek tárolásáért. Az ideiglenes lemezes tárolás ingyenes.

A futó és a leállított állapotú virtuális gépek után fizetnie kell, a Leállítva (Felszabadítva) állapotúak után azonban nem. Ha a virtuális gépet Leállítva (Felszabadítva) állapotba kívánja helyezni, tegye a következők egyikét:

* Állítsa le vagy törölje a virtuális gépet az Azure Portalon.
* Használja a Stop-AzureVM parancsmagot, amely az Azure PowerShell modulban érhető el.
* Használja a Szerepkör leállítása műveletet a szolgáltatásfelügyeleti REST API-n, és adja meg a StoppedDeallocated paramétert a PostShutdownAction elemhez.

További információ: [A Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Az Azure újraindítja a virtuális gépet karbantartás céljából?
Az Azure időnként újraindítja a virtuális gépét az Azure-adatközpontok rendszeres, tervezett karbantartási frissítéseinek keretében.

Nem tervezett karbantartási események előfordulhatnak, ha az Azure komoly hardveres problémát észlel, amely befolyásolja a virtuális gépét. Nem tervezett események esetén az Azure automatikusan migrálja a virtuális gépet egy kifogástalan állapotú gazdagépre, és újraindítja a virtuális gépet.

Bármely különálló virtuális gép esetén (amely azt jelenti, hogy a virtuális gép nem része egy rendelkezésre állási csoportnak) az Azure e-mailben értesíti az előfizetés szolgáltatás-rendszergazdáját legalább egy héttel a tervezett karbantartás előtt, hogy a virtuális gépek újraindulhatnak a frissítés során. Előfordulhat, hogy a virtuális gépeken futtatott alkalmazások működése leáll.

Is használhatja az Azure portal vagy Azure PowerShell-lel az újraindítási naplóinak megtekintése, ha az újraindítás tervezett karbantartás miatt történt. További részletek: [Virtuális gépek újraindítási naplóinak megtekintése](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Redundancia biztosításához helyezzen két vagy több hasonlóan konfigurált virtuális gépet ugyanabba a rendelkezésre állási csoportba. Ezzel biztosítja, hogy legalább egy virtuális gép elérhető maradjon a tervezett vagy nem tervezett karbantartás alatt. Az Azure garantálja a virtuális gépek bizonyos szintű rendelkezésre állását ebben a konfigurációban. Részletes információ: [Virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>További források
[Az Azure-beli virtuális gépek bemutatása](../articles/virtual-machines/virtual-machines-linux-about.md)

[Linux rendszerű virtuális gépek létrehozása és kezelése az Azure CLI-vel](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Windows rendszerű virtuális gépek létrehozása és felügyelete Azure PowerShell-lel ](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

