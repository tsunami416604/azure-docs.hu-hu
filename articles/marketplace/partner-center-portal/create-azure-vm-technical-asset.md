---
title: Azure-beli virtuális gépek technikai eszközeinek létrehozása
description: Ismerje meg, hogyan hozhat létre és konfigurálhat technikai eszközöket az Azure Marketplace-hez készült virtuálisgép-(VM-) ajánlathoz.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 07c8de2a9d94b51f7183829466bd68d56e19efba
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646810"
---
# <a name="create-azure-virtual-machine-technical-assets"></a>Azure-beli virtuális gépek technikai eszközeinek létrehozása

A virtuális gép (VM) lemezképének az Azure Marketplace-en való közzétételekor az Azure-csapat ellenőrzi a virtuálisgép-lemezképet, hogy biztosítsa a rendszerindítást, a biztonságot és az Azure-kompatibilitást. Ha a kiváló minőségű tesztek bármelyike meghibásodik, a közzététel sikertelen lesz, és a rendszer hibaüzenetet tartalmaz, és a lehetséges [helyesbítési lépéseket](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)tartalmazza.

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat technikai eszközöket az Azure Marketplace-hez készült virtuálisgép-(VM-) ajánlathoz. A virtuális gépek két összetevőt tartalmaznak: az operációs rendszer virtuális merevlemezét (VHD) és opcionálisan kapcsolódó adatlemezek VHD-ket:

1. **Operációs rendszer virtuális merevlemeze** – az ajánlatával üzembe helyezett operációs rendszert és megoldást tartalmazza. A VHD előkészítési folyamata attól függően eltérő, hogy Linux-alapú, Windows-alapú vagy egyéni virtuális gép-e.

2. **Adatlemez VHD** -k – dedikált, állandó tárterület egy virtuális géphez. Ne használja az operációs rendszer VHD-jét (például a C: meghajtót) az állandó információk tárolásához.

A virtuálisgép-lemezképek egy operációsrendszer-lemezt és legfeljebb 16 adatlemezt tartalmaznak. Adatlemez esetén használjon egy VHD-t, még akkor is, ha a lemez üres.

> [!NOTE]
> A használt operációs rendszertől függetlenül csak a megoldáshoz szükséges adatlemezek minimális számát adja hozzá. Az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket az üzembe helyezéskor, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is.

> [!IMPORTANT]
> A tervekben szereplő összes virtuálisgép-rendszerképnek azonos számú adatlemezzel kell rendelkeznie.

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról. A megoldás tartományán kívül a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

- Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése
- [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
- Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage)és az [Azure hálózatkezelésének](https://azure.microsoft.com/services/?filter=networking) gyakorlati ismerete
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
- A [JSON](https://www.json.org/) működésének ismerete

### <a name="optional-suggested-tools"></a>Választható javasolt eszközök

A virtuális gépek és virtuális merevlemezek kezeléséhez a következő parancsfájl-környezetek egyikét érdemes használni:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

Emellett vegye fontolóra a következő eszközök hozzáadását a fejlesztői környezethez:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Virtuálisgép-rendszerkép létrehozása jóváhagyott alap használatával

Ha a virtuális gép technikai eszközeit a saját telephelyén létrehozott rendszerképpel szeretné létrehozni, tekintse meg a virtuálisgép- [rendszerkép létrehozása az alábbi jóváhagyott alap használatával](#create-a-vm-image-using-an-approved-base) című szakaszt.

Ez a szakasz a jóváhagyott alapértékek használatának különböző szempontjait ismerteti, például a RDP protokoll (RDP) használatát, a virtuális gép méretének kiválasztását, a legújabb Windows-frissítések telepítését és a VHD-lemezkép általánosítását ismerteti.

A cikk útmutatását követve az Azure-t használva hozzon létre egy virtuális gépet, amely egy előre konfigurált, támogatott operációs rendszert tartalmaz. Ha ez nem kompatibilis a megoldással, lehetséges, hogy a helyszíni virtuális gépet egy jóváhagyott operációs rendszer használatával hozza létre és konfigurálja. Ezután beállíthatja és felkészítheti a feltöltéshez a [Windows VHD vagy a VHDX előkészítése az Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)-ba való feltöltéshez című témakörben leírtak szerint.

### <a name="select-an-approved-base-image"></a>Jóváhagyott alaprendszerkép kiválasztása

Válassza ki a Windows operációs rendszert vagy a Linuxot az alapjaként.

A virtuálisgép-rendszerkép operációs rendszerének VHD-jét egy olyan, az Azure által jóváhagyott alaprendszerképen kell alapulnia, amely Windows Servert vagy SQL Server tartalmaz.

Ha olyan kérést küld, amely a rendszerkép frissítéssel való újbóli közzétételét kéri, akkor előfordulhat, hogy a részleges ellenőrzés tesztelési esete meghiúsul. Ebben az esetben a rendszerkép nem lesz jóváhagyva.

Ez a hiba akkor fordul elő, ha egy másik közzétevőhöz tartozó alaprendszerképet használ, és frissítette a rendszerképet. Ebben az esetben nem engedélyezheti a rendszerkép közzétételét.

A probléma megoldásához kérje le legújabb rendszerképét az Azure Marketplace-ről, és végezze el a lemezkép módosítását. A következő témakörben megtekintheti a jóváhagyott alapképeket, ahol megkeresheti a lemezképet:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

Az Azure számos jóváhagyott Linux-disztribúciót kínál. Az aktuális listán tekintse [meg az Azure által támogatott disztribúciókkal](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)foglalkozó Linux című témakört.

### <a name="create-vm-on-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portal

Az alábbi lépéseket követve hozza létre az alapszintű VM-rendszerképet a [Azure Portal](https://ms.portal.azure.com/):

1. Jelentkezzen be a [Azure Portalba](https://ms.portal.azure.com/) a virtuális gép ajánlatának közzétételéhez használni kívánt Azure-előfizetéshez társított Microsoft-fiókval.
2. Hozzon létre egy új erőforráscsoportot, és adja meg az **erőforráscsoport nevét**, az **előfizetést**és az **erőforráscsoport helyét**. Részletekért lásd: [erőforrások kezelése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Egy erőforráscsoport létrehozásának kezdetét jeleníti meg.":::

3. A bal oldali navigációs panelen válassza a **virtuális gépek** lehetőséget a virtuális gépek részletei lap megjelenítéséhez.
4. Válassza a **+ Hozzáadás** lehetőséget a **virtuális gép létrehozásának élményének**megnyitásához.
5. Válassza ki a lemezképet a legördülő listából, vagy válassza az **összes nyilvános és privát rendszerkép tallózása** lehetőséget a rendelkezésre álló virtuálisgép-lemezképek kereséséhez vagy tallózásához. Példa:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Egy minta virtuálisgép-rendszerképet jelenít meg.":::

6. Válassza ki az alábbi javaslatok alapján telepítendő virtuális gép méretét:
    1. Ha a helyszíni virtuális merevlemez fejlesztését tervezi, a méret nem számít. Érdemes lehet a kisebb virtuális gépek egyikét használni.
    2. Ha azt tervezi, hogy az Azure-ban fejleszti a rendszerképet, érdemes lehet a kiválasztott rendszerképhez ajánlott virtuálisgép-méreteket használni.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="A virtuális gép méretének kiválasztását mutatja.":::

7. A **lemezek** szakaszban bontsa ki a **speciális** szakaszt, és állítsa a **felügyelt lemezek használata** lehetőséget a **nem**értékre.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="A felügyelt lemezek használatára vonatkozó beállítást mutatja.":::

8. Adja meg a virtuális gép létrehozásához szükséges egyéb adatokat.
9. Válassza a **felülvizsgálat + létrehozás** lehetőséget a választási lehetőségek áttekintéséhez. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

Az Azure megkezdi a megadott virtuális gép üzembe helyezését. Az előrehaladás nyomon követéséhez válassza a bal oldali **Virtual Machines** fület. A létrehozása után az állapot **fut**értékre változik.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Hozzon létre egy 2. generációs virtuális gépet a Azure Portal

Hozzon létre egy 2. generációs (Gen2) virtuális gépet Azure Portal.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.
2. Válassza az **Erőforrás létrehozása** lehetőséget.
3. Válassza az **összes** megjelenítése lehetőséget a bal oldalon található Azure Marketplace-en.
4. Válasszon olyan rendszerképet, amely támogatja a Gen2.
5. Kattintson a **Létrehozás** gombra.
6. A **speciális** lapon, a **virtuális gép létrehozása** szakaszban válassza a 2. **generációs** lehetőséget.
7. Az **alapvető beállítások** lap **példány részletei**területén válassza a **méret** elemet, és nyissa meg a **virtuális gép méretének kiválasztása panelt** .
8. Válassza ki a [támogatott 2. generációs virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) és méret javasolt méretét.
9. A virtuális gép létrehozásának befejezéséhez folytassa a [Azure Portal létrehozás folyamatán](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) .

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Megjeleníti a virtuális gép generációjának kiválasztására szolgáló lehetőséget.":::

## <a name="connect-to-your-azure-vm"></a>Kapcsolódás Azure-beli virtuális géphez

Ez a szakasz azt ismerteti, hogyan csatlakozhat és jelentkezhet be az Azure-on létrehozott virtuális gépre. A sikeres csatlakozást követően ugyanúgy dolgozhat a virtuális géppel, mintha helyileg bejelentkezett volna a gazdagép kiszolgálójára.

### <a name="connect-to-a-windows-based-vm"></a>Kapcsolódás Windows-alapú virtuális géphez

Az Azure-ban üzemeltetett Windows-alapú virtuális géphez való kapcsolódáshoz használja a távoli asztali ügyfelet. A Windows legtöbb verziója natív módon támogatja a Remote Desktop Protocol (RDP) szolgáltatást. Más operációs rendszerek esetén további információkat találhat [Távoli asztal ügyfelek](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)ügyfeleiről.

Ez a cikk részletesen ismerteti, hogyan használható a beépített Windows RDP-támogatás a virtuális géphez való kapcsolódáshoz: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> A folyamat során biztonsági figyelmeztetéseket kaphat. Például: "az. rdp fájl ismeretlen közzétevőtől származik" vagy "a felhasználói hitelesítő adatok nem ellenőrizhetők." Ezeket a figyelmeztetéseket nyugodtan figyelmen kívül hagyhatja.

### <a name="connect-to-a-linux-based-vm"></a>Kapcsolódás Linux-alapú virtuális géphez

Linux-alapú virtuális géphez való kapcsolódáshoz Secure Shell Protocol-(SSH-) ügyfélre van szükség. A következő lépések az ingyenes [Putty](https://www.ssh.com/ssh/putty/) SHH-terminált használják.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com/).
2. Keresse meg és válassza ki a virtuális gépeket.
3. Válassza ki azt a virtuális gépet, amelyhez csatlakozni szeretne.
4. Ha még nem fut, indítsa el a virtuális gépet.
5. Válassza ki a virtuális gép nevét az áttekintő oldal megnyitásához.
6. Jegyezze fel a virtuális gép nyilvános IP-címét és DNS-nevét (ha ezek az értékek nincsenek beállítva, [létre kell hoznia egy hálózati adaptert](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).
7. Nyissa meg a PuTTY alkalmazást.
8. A PuTTY konfigurációja párbeszédpanelen adja meg a virtuális gép IP-címét vagy DNS-nevét.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Bemutatja a PuTTY-terminál beállításait, kiemelve az állomásnév és a port mezőket.":::

9. Válassza a **Megnyitás** lehetőséget egy Putty terminál megnyitásához.
10. Ha a rendszer kéri, adja meg a linuxos virtuális gép fiókjának nevét és jelszavát.

## <a name="configure-the-virtual-machine"></a>A virtuális gép konfigurálása

Ez a szakasz azt ismerteti, hogyan lehet méretezni, frissíteni és általánosítani egy Azure-beli virtuális gépet. Ezek a lépések szükségesek ahhoz, hogy előkészítse a virtuális gépet az Azure Marketplace-en való üzembe helyezéshez.

### <a name="sizing-the-vhds"></a>A VHD-k méretezése

Az operációs rendszer lemezének méretére vonatkozó korlátozások az alábbi szabályok. Ha bármilyen kérelmet küld be, győződjön meg arról, hogy az operációsrendszer-lemez mérete a Linux vagy a Windows korlátozásán belül van.

| Operációs rendszer | Ajánlott VHD-méret |
| --- | --- |
| Linux | 30 – 1023 GB |
| Windows | 30 – 250 GB |

Mivel a virtuális gépek engedélyezik az alapul szolgáló operációs rendszer elérését, gondoskodjon arról, hogy a VHD-méret elég nagy legyen a VHD-hez. Mivel a lemezek állásidő nélkül nem bővíthetők, a lemez mérete 30 és 50 GB között kell, hogy legyen &nbsp; .

| VHD-méret | Tényleges foglalt méret | Megoldás |
| --- | --- | --- |
| >500 TB | n.a. | Kivétel jóváhagyásához forduljon a támogatási csoporthoz. |
| 250-500 TB | A blob méretétől eltérő 200 GB > | Kivétel jóváhagyásához forduljon a támogatási csoporthoz. |

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

Az operációs rendszer virtuális gépei alapképeinek a közzétételük napjáig a legújabb frissítéseket kell tartalmazniuk. Mielőtt közzéteszi a létrehozott operációs rendszer virtuális merevlemezét, győződjön meg róla, hogy a legújabb biztonsági és karbantartási javításokkal frissíti az operációs rendszert és az összes telepített szolgáltatást.

- A Windows Server esetében futtassa a frissítések keresése parancsot.
- A Linux-disztribúciók esetében a frissítések általában egy parancssori eszközzel vagy egy grafikus segédprogrammal tölthetők le és telepíthetők. Például Ubuntu Linux biztosítja az [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) parancsot és az [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) eszközt az operációs rendszer frissítéséhez.

#### <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

Az Azure Marketplace-en magas szintű biztonságot tarthat a megoldási lemezképek számára. A biztonsági konfigurációk és eljárások Ellenőrzőlista: [biztonsági javaslatok az Azure Marketplace-lemezképekhez](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). A javaslatok némelyike a Linux-alapú rendszerképekre jellemző, de a legtöbb esetben a virtuálisgép-lemezképekre is érvényes.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

Ha további konfigurációra van szüksége, az indításkor futó ütemezett feladat használatával hajtsa végre a végső módosításokat a virtuális gépen a telepítés után. Ügyeljen a következőkre is:

- Ha ez egy egyszeri futtatású feladat, a feladat a sikeres befejezést követően törli magát.
- A konfigurációk nem hivatkozhatnak a C vagy a D meghajtón kívüli meghajtókra, mert csak ez a két meghajtó garantáltan létezik (a C meghajtó az operációsrendszer-lemez, a D meghajtó pedig az ideiglenes helyi lemez).

A Linux testreszabásával kapcsolatos további információkért lásd: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>A rendszerkép általánosítása

Az Azure Marketplace-en lévő összes lemezképet általános módon újrafelhasználhatónak kell lennie. Ennek eléréséhez általánosítva kell lennie az operációs rendszer virtuális merevlemezének, egy olyan műveletnek, amely eltávolítja az összes példány-specifikus azonosítót és a szoftver illesztőprogramjait egy virtuális gépről.

### <a name="for-windows"></a>Windows esetén

A Windows operációsrendszer-lemezek általánosítva vannak a [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) eszközzel. Ha később frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a Sysprep programot.

> [!WARNING]
> A Sysprep futtatása után kapcsolja ki a virtuális gépet az üzembe helyezésig, mert a frissítések automatikusan futtathatók. Ez a Leállítás megakadályozza, hogy a későbbi frissítések az operációs rendszer vagy a telepített szolgáltatások példány-specifikus módosításait is elvégezzék. A Sysprep futtatásával kapcsolatos további információkért tekintse [meg a virtuális merevlemez általánosítása című témakör lépéseit](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Linux esetén

A következő folyamat általánosít egy Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre. Részletekért lásd: [virtuális gépek vagy VHD-k rendszerképének létrehozása](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Ha eléri a "virtuális gép létrehozása a rögzített lemezképből" szakaszt, akkor leállíthatja.

1. Az Azure Linux-ügynök eltávolítása

    1. Kapcsolódás Linux rendszerű virtuális géphez SSH-ügyfél használatával
    2. Az SSH ablakban adja meg a következő parancsot: `sudo waagent –deprovision+user` .
    3. A folytatáshoz írja be az Y betűt (a-Force paramétert hozzáadhatja az előző parancshoz, így elkerülhető a megerősítő lépés).
    4. A parancs befejezése után a Kilépés gombra kattintva zárja be az SSH-ügyfelet.

2. Virtuális gép leállítása

    1. A Azure Portal válassza ki az erőforráscsoportot (RG) és a virtuális gép lefoglalását.
    2. A VHD-fájl már általánosítva van, és létrehozhat egy új virtuális gépet a virtuális merevlemez használatával.

## <a name="next-steps"></a>Következő lépések

- Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg a [gyakori problémák a VHD létrehozása során](common-issues-during-vhd-creation.md)című témakört.
- Ha nem, a [VHD-ről üzembe helyezett virtuális gép (VM)](azure-vm-image-certification.md) elmagyarázza, hogyan kell tesztelni és elküldeni az Azure Marketplace minősítéshez tartozó virtuálisgép-lemezképet, beleértve az Azure Certified eszköz minősítési teszt eszközének beszerzését és a virtuálisgép-lemezképek hitelesítésének módját.
