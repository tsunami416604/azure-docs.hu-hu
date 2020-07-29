---
title: Azure-beli virtuális gépek technikai eszközeinek létrehozása
description: Ismerje meg, hogyan hozhat létre és konfigurálhat technikai eszközöket az Azure Marketplace-hez készült virtuálisgép-(VM-) ajánlathoz.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/13/2020
ms.openlocfilehash: 69458a7a3f2555a301c1266addfe7c49cb111ef6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317756"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Azure-beli virtuális gépek technikai eszközeinek létrehozása

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat technikai eszközöket az Azure Marketplace-hez készült virtuálisgép-(VM-) ajánlathoz. A virtuális gépek két összetevőt tartalmaznak: az operációs rendszer virtuális merevlemezét (VHD) és opcionálisan kapcsolódó adatlemezek VHD-ket:

* **Operációs rendszer virtuális merevlemeze** – az ajánlatával üzembe helyezett operációs rendszert és megoldást tartalmazza. A virtuális merevlemez előkészítésének folyamata attól függően eltérő, hogy Linux-alapú, Windows-alapú vagy egyedi alapú virtuális gép-e.
* **Adatlemezek virtuális** merevlemezek – dedikált, állandó tárterület egy virtuális géphez. Ne használja az operációs rendszer VHD-jét (például a C: meghajtót) az állandó információk tárolásához.

A virtuálisgép-lemezképek egy operációsrendszer-lemezt és legfeljebb 16 adatlemezt tartalmaznak. Adatlemez esetén használjon egy VHD-t, még akkor is, ha a lemez üres.

> [!NOTE]
> A használt operációs rendszertől függetlenül csak a megoldáshoz szükséges adatlemezek minimális számát adja hozzá. Az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket az üzembe helyezéskor, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is.

> [!IMPORTANT]
> A tervekben szereplő összes virtuálisgép-rendszerképnek azonos számú adatlemezzel kell rendelkeznie.

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról. A megoldás tartományán kívül a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

* Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése
* [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
* Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage) és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking) együttműködésének ismerete
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
* A [JSON](https://www.json.org/) működésének ismerete

## <a name="suggested-tools--optional"></a>Javasolt eszközök – nem kötelező

A virtuális gépek és virtuális merevlemezek kezeléséhez a következő parancsfájl-környezetek egyikét érdemes használni:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Azure CLI](https://code.visualstudio.com/)

Emellett vegye fontolóra a következő eszközök hozzáadását a fejlesztői környezethez:

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Tekintse át a rendelkezésre álló eszközöket az [Azure fejlesztői eszközök](https://azure.microsoft.com/product-categories/developer-tools/) oldalon, és ha a Visual studiót használja, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.

## <a name="create-a-vm-image-using-an-approved-base"></a>Virtuálisgép-rendszerkép létrehozása jóváhagyott alap használatával

> [!NOTE]
> Ha a virtuális gép technikai eszközeit a saját telephelyén létrehozott rendszerképpel szeretné létrehozni, ugorjon a [saját rendszerkép használatával](#create-a-vm-using-your-own-image)hozzon létre egy virtuális gépet.

Ez a szakasz a jóváhagyott alapértékek használatának különböző szempontjait ismerteti, például a RDP protokoll (RDP) használatát, a virtuális gép méretének kiválasztását, a legújabb Windows-frissítések telepítését és a VHD-lemezkép általánosítását ismerteti.

Az alábbi fejezetek elsősorban a Windows-alapú virtuális merevlemezek esetében összpontosítanak. A Linux-alapú virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: az [Azure által támogatott disztribúciók Linux](../../virtual-machines/linux/endorsed-distros.md).

> [!WARNING]
> Kövesse a jelen témakör útmutatását, hogy az Azure-t használva hozzon létre egy virtuális gépet, amely egy előre konfigurált, támogatott operációs rendszert tartalmaz. Ha ez nem kompatibilis a megoldással, lehetséges, hogy a helyszíni virtuális gépet egy jóváhagyott operációs rendszer használatával hozza létre és konfigurálja. Ezután beállíthatja és felkészítheti a feltöltéshez a [Windows VHD vagy a VHDX előkészítése az Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md)-ba való feltöltéshez című témakörben leírtak szerint.

### <a name="select-an-approved-base"></a>Jóváhagyott alap kiválasztása

Válassza ki a Windows operációs rendszert vagy a Linuxot az alapjaként.

#### <a name="windows"></a>Windows

A Windows-alapú virtuálisgép-rendszerkép operációs rendszerének VHD-jét a Windows Servert vagy SQL Servert tartalmazó, Azure által jóváhagyott alaprendszerképen kell alapulnia. A kezdéshez hozzon létre egy virtuális gépet az alábbi rendszerképek egyikéről a Azure Portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 r2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, standard, web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, standard, web)

> [!NOTE]
> Ha a jelenlegi Azure Portal vagy Azure PowerShell használja, a Windows Server rendszerképeket a 2014. szeptember 8-án és később is jóváhagyjuk.

#### <a name="linux"></a>Linux

Az Azure számos jóváhagyott Linux-disztribúciót kínál. Az aktuális listán tekintse [meg az Azure által támogatott disztribúciókkal](../../virtual-machines/linux/endorsed-distros.md)foglalkozó Linux című témakört.

### <a name="create-vm-in-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portalban

Az alábbi lépéseket követve hozza létre az alapszintű VM-rendszerképet a [Azure Portalban](https://ms.portal.azure.com/):

1. Jelentkezzen be a [Azure Portalba](https://ms.portal.azure.com/) a virtuális gép ajánlatának közzétételéhez használni kívánt Azure-előfizetéshez társított Microsoft-fiókval.
2. Hozzon létre egy új erőforráscsoportot, és adja meg az **erőforráscsoport nevét**, az **előfizetést**és az **erőforráscsoport helyét**. Részletekért lásd: [erőforrások kezelése](../../azure-resource-manager/resource-group-portal.md).
3. Válassza a bal oldali **virtuális gépek** lehetőséget a virtuális gépek részletei lap megjelenítéséhez.
4. Válassza a **+ Hozzáadás** lehetőséget a **virtuális gép létrehozásának élményének**megnyitásához.
5. Válassza ki a lemezképet a legördülő listából, vagy kattintson az **összes nyilvános és privát rendszerkép tallózása** lehetőségre az összes elérhető virtuálisgép-lemezkép kereséséhez vagy tallózásához.
6. Válassza ki az alábbi javaslatok alapján telepítendő virtuális gép méretét:
    * Ha a helyszíni virtuális merevlemez fejlesztését tervezi, a méret nem számít. Érdemes lehet a kisebb virtuális gépek egyikét használni.
    * Ha azt tervezi, hogy az Azure-ban fejleszti a rendszerképet, érdemes lehet a kiválasztott rendszerképhez ajánlott virtuálisgép-méreteket használni.

7. A **lemezek** szakaszban bontsa ki a **speciális** szakaszt, és állítsa a **felügyelt lemezek használata** lehetőséget a **nem**értékre.
8. Adja meg a virtuális gép létrehozásához szükséges egyéb adatokat.
9. Válassza a **felülvizsgálat + létrehozás** lehetőséget a választási lehetőségek áttekintéséhez. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

Az Azure megkezdi a megadott virtuális gép üzembe helyezését. Az előrehaladás nyomon követéséhez válassza a bal oldali **Virtual Machines** fület. A létrehozása után az állapot **fut**értékre változik.

Ha nehézségekbe ütközik az új Azure-alapú virtuális merevlemez létrehozásakor, tekintse meg a [gyakori problémák a VHD létrehozásakor (gyakori kérdések)](common-issues-during-vhd-creation.md)című témakört.

### <a name="connect-to-your-azure-vm"></a>Kapcsolódás Azure-beli virtuális géphez

Ez a szakasz azt ismerteti, hogyan csatlakozhat az Azure-ban létrehozott virtuális géphez, és hogyan jelentkezhet be az alkalmazásba. A sikeres csatlakozást követően ugyanúgy dolgozhat a virtuális géppel, mintha helyileg bejelentkezett volna a gazdagép kiszolgálójára.

#### <a name="connect-to-a-windows-based-vm"></a>Kapcsolódás Windows-alapú virtuális géphez

Az Azure-ban üzemeltetett Windows-alapú virtuális géphez való kapcsolódáshoz használja a távoli asztali ügyfelet. A Windows legtöbb verziója natív módon támogatja a Remote Desktop Protocol (RDP) szolgáltatást. Más operációs rendszerek esetén további információkat találhat [Távoli asztal ügyfelek](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)ügyfeleiről.

Ez a cikk részletesen ismerteti, hogyan használható a beépített Windows RDP-támogatás a virtuális géphez való kapcsolódáshoz: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](../../virtual-machines/windows/connect-logon.md).

> [!TIP]
> A folyamat során biztonsági figyelmeztetéseket kaphat. Például: "az. rdp fájl ismeretlen közzétevőtől származik" vagy "a felhasználói hitelesítő adatok nem ellenőrizhetők." Ezeket a figyelmeztetéseket nyugodtan figyelmen kívül hagyhatja.

#### <a name="connect-to-a-linux-based-vm"></a>Kapcsolódás Linux-alapú virtuális géphez

Linux-alapú virtuális géphez való kapcsolódáshoz Secure Shell Protocol-(SSH-) ügyfélre van szükség. A következő lépések az ingyenes [Putty](https://www.ssh.com/ssh/putty/) SHH-terminált használják.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com/).
2. Keresse meg és válassza ki a **virtuális gépeket**.
3. Válassza ki azt a virtuális gépet, amelyhez csatlakozni szeretne.
4. Ha még nem fut, indítsa el a virtuális gépet.
5. Válassza ki a virtuális gép nevét az **áttekintő** oldal megnyitásához.
6. Jegyezze fel a virtuális gép nyilvános IP-címét és DNS-nevét (ha ezek az értékek nincsenek beállítva, [létre kell hoznia egy hálózati adaptert](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface))).
7. Nyissa meg a PuTTY alkalmazást.
8. A PuTTY konfigurációja párbeszédpanelen adja meg a virtuális gép IP-címét vagy DNS-nevét.

    :::image type="content" source="media/avm-putty.png" alt-text="A PuTTY-terminál beállításainak ábrázolása. ki van emelve az állomásnév vagy az IP-cím és a portok jelölőnégyzet.":::

9. Válassza a **Megnyitás** lehetőséget egy Putty terminál megnyitásához.
10. Ha a rendszer kéri, adja meg a linuxos virtuális gép fiókjának nevét és jelszavát.

Ha kapcsolódási problémák léptek fel, tekintse meg az SSH-ügyfél dokumentációját. Például a [10. fejezet: gyakori hibaüzenetek](https://www.ssh.com/ssh/putty/putty-manuals).

További részletekért, beleértve az asztal kiépített linuxos virtuális géphez való hozzáadásával kapcsolatos információkat: [Távoli asztal telepítése és konfigurálása egy linuxos virtuális géphez való kapcsolódáshoz az Azure-ban](../../virtual-machines/linux/use-remote-desktop.md).

## <a name="create-a-vm-using-your-own-image"></a>Virtuális gép létrehozása saját rendszerkép használatával

Ez a szakasz azt ismerteti, hogyan hozhat létre és helyezhet üzembe egy felhasználó által megadott virtuálisgép-lemezképet. Ezt úgy teheti meg, hogy az operációs rendszer és az adatlemez VHD-lemezképeit egy Azure által telepített virtuális merevlemezről (VHD) biztosítja.

> [!NOTE]
> Ha választható alaprendszerképet szeretne használni, kövesse a virtuálisgép- [rendszerkép létrehozása jóváhagyott alap használatával](#create-a-vm-image-using-an-approved-base)című témakör utasításait.

1. Töltse fel a lemezképeket az Azure Storage-fiókba.
2. Telepítse a VM-rendszerképet.
3. Rögzítse a virtuálisgép-lemezképet.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Lemezképek feltöltése egy Azure Storage-fiókba

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Töltse fel az általánosított operációs rendszer VHD-jét és az adatlemez virtuális merevlemezeit az Azure Storage-fiókjába.

### <a name="deploy-your-image"></a>A rendszerkép üzembe helyezése

Hozza létre a rendszerképet a Azure Portal vagy a Azure PowerShell használatával.

#### <a name="deploy-using-the-azure-portal"></a>Üzembe helyezés az Azure Portalon

1. A kezdőlapon válassza az **erőforrás létrehozása**lehetőséget, keressen rá a "sablon központi telepítése" kifejezésre, és válassza a **Létrehozás**lehetőséget.
2. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Az egyéni telepítési oldal szemléltetése.":::

3. Illessze be ezt a [JSON-sablont](../partner-center-portal/azure-vm-image-certification.md) a Szerkesztőbe, majd válassza a **Mentés**lehetőséget.
4. Adja meg a paraméterek értékeit az **Egyéni központi telepítési** tulajdonságlapon.

    | Paraméter | Leírás |
    | ------------ | ------------- |
    | Felhasználói Storage-fiók neve | A 2. cellából származó tartalom |
    | Felhasználói tároló tárolójának neve | A Storage-fiók neve, ahol az általánosított VHD található |
    | Nyilvános IP-cím DNS-neve | Nyilvános IP-cím DNS-neve. Adja meg a nyilvános IP-cím DNS-nevét a Azure Portal az ajánlat telepítése után. |
    | Rendszergazdai Felhasználónév | Rendszergazdai fiók felhasználóneve az új virtuális géphez |
    | Rendszergazdai jelszó | Rendszergazdai fiók jelszava az új virtuális géphez |
    | Operációs rendszer típusa | VM operációs rendszer: Windows vagy Linux |
    | Előfizetés azonosítója | A kiválasztott előfizetés azonosítója |
    | Hely | Az üzemelő példány földrajzi helye |
    | Virtuális gép mérete | [Azure-beli virtuális gép mérete](../../virtual-machines/windows/sizes.md), például Standard_A2 |
    | Nyilvános IP-cím neve | A nyilvános IP-cím neve |
    | Virtuális gép neve | Az új virtuális gép neve |
    | Virtual Network neve | A virtuális gép által használt virtuális hálózat neve |
    | Hálózati adapter neve | A virtuális hálózatot futtató hálózati kártya neve |
    | VHD URL-CÍME | OPERÁCIÓSRENDSZER-lemez virtuális merevlemezének teljes URL-címe |
    |  |  |

5. Az értékek megadása után válassza a **vásárlás**lehetőséget.

Az Azure elindítja az üzembe helyezést. Egy új virtuális gépet hoz létre a megadott nem felügyelt VHD-vel a megadott Storage-fiók elérési útján. A Azure Portal előrehaladását a portál bal oldalán található **Virtual Machines** kiválasztásával követheti nyomon. A virtuális gép létrehozása után az állapot az indítástól a futtatásig változik.

#### <a name="deploy-using-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>A virtuális gép rendszerképének rögzítése

Használja az alábbi utasításokat, amelyek megfelelnek az Ön megközelítésének:

* Azure PowerShell: nem [felügyelt virtuális gép rendszerképének létrehozása Azure-beli virtuális](../../virtual-machines/windows/capture-image-resource.md) gépről
* Azure CLI: [virtuális gép vagy VHD rendszerképének létrehozása](../../virtual-machines/linux/capture-image.md)
* API: [Virtual Machines – rögzítés](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>A virtuális gép konfigurálása

Ez a szakasz azt ismerteti, hogyan lehet méretezni, frissíteni és általánosítani egy Azure-beli virtuális gépet. Ezek a lépések szükségesek ahhoz, hogy előkészítse a virtuális gépet az Azure Marketplace-en való üzembe helyezéshez.

### <a name="sizing-the-vhds"></a>A VHD-k méretezése

Ha a virtuális gépek egyikét (és opcionálisan további szolgáltatásokat) előre konfigurálta, akkor már egy standard Azure-beli virtuálisgép-méretet választott ki. Az ajánlott módszer a megoldás elindítása előre konfigurált operációs rendszerrel. Ha azonban manuálisan telepít egy operációs rendszert, az elsődleges virtuális merevlemezt a virtuálisgép-rendszerképbe kell méreteznie:

* Windows esetén az operációs rendszer virtuális merevlemezét 127 – 128 GB-os rögzített formátumú VHD-ként kell létrehozni.
* Linux esetén ezt a virtuális merevlemezt 30 – 50 GB-os rögzített formátumú VHD-ként kell létrehozni.

Ha a fizikai méret kisebb, mint 127 – 128 GB, a VHD-nek bővíthetőnek kell lennie (ritka/dinamikus). A megadott alapszintű Windows-és SQL Server-lemezképek már megfelelnek ezeknek a követelményeknek, ezért ne módosítsa a VHD formátumát vagy méretét.

Az adatlemezek mérete akár 1 TB is lehet. A méret meghatározásakor ne feledje, hogy az ügyfelek nem tudják átméretezni a lemezképen belüli virtuális merevlemezeket az üzembe helyezéskor. Az adatlemezek virtuális merevlemezeit rögzített formátumú VHD-ként kell létrehozni. Emellett bővíthetők (ritka/dinamikus). Az adatlemezek kezdetben üresek vagy tartalmazhatnak adatfájlokat.

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

Az operációs rendszer virtuális gépei alapképeinek a közzétételük napjáig a legújabb frissítéseket kell tartalmazniuk. Mielőtt közzéteszi a létrehozott operációs rendszer virtuális merevlemezét, győződjön meg róla, hogy a legújabb biztonsági és karbantartási javításokkal frissíti az operációs rendszert és az összes telepített szolgáltatást.

A Windows Server esetében futtassa a **frissítések keresése** parancsot.

A Linux-disztribúciók esetében a frissítések általában egy parancssori eszközzel vagy egy grafikus segédprogrammal tölthetők le és telepíthetők. Például Ubuntu Linux biztosítja az [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) parancsot és az [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) eszközt az operációs rendszer frissítéséhez.

### <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

Az Azure Marketplace-en magas szintű biztonságot tarthat a megoldási lemezképek számára. A következő cikk a biztonsági konfigurációkról és eljárásokról nyújt útmutatást: az [Azure Marketplace-rendszerképekkel kapcsolatos biztonsági javaslatok](../../security/security-recommendations-azure-marketplace-images.md). A javaslatok némelyike a Linux-alapú rendszerképekre jellemző, de a legtöbb esetben a virtuálisgép-lemezképekre is érvényes.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

Ha további konfigurálásra van szükség, az indításkor futó ütemezett feladat használatával hajtsa végre a végső módosításokat a virtuális gépen a telepítés után. Vegye figyelembe az alábbi ajánlásokat is:

* Ha ez egy egyszeri futtatású feladat, a feladat a sikeres befejezést követően törli magát.
* A konfigurációk nem hivatkozhatnak a C vagy a D meghajtón kívüli meghajtókra, mert csak ez a két meghajtó garantáltan létezik (a C meghajtó az operációsrendszer-lemez, a D meghajtó pedig az ideiglenes helyi lemez).

A Linux testreszabásával kapcsolatos további információkért lásd: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](../../virtual-machines/extensions/features-linux.md).

## <a name="generalize-the-image"></a>A rendszerkép általánosítása

Az Azure Marketplace-en lévő összes lemezképet általános módon újrafelhasználhatónak kell lennie. Ennek eléréséhez általánosítva kell lennie az operációs rendszer virtuális merevlemezének, egy olyan műveletnek, amely eltávolítja az összes példány-specifikus azonosítót és a szoftver illesztőprogramjait egy virtuális gépről.

### <a name="windows"></a>Windows

A Windows operációsrendszer-lemezek általánosítva vannak a [Sysprep eszközzel](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Ha később frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a Sysprep programot.

> [!WARNING]
> Mivel a frissítések automatikusan futtathatók, a Sysprep futtatása után kapcsolja ki a virtuális gépet, amíg üzembe nem helyezi. Ez a Leállítás megakadályozza, hogy a későbbi frissítések az operációs rendszer vagy a telepített szolgáltatások példány-specifikus módosításait is elvégezzék. A Sysprep futtatásával kapcsolatos további információkért tekintse [meg a virtuális merevlemez általánosítása című témakör lépéseit](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

A következő folyamat általánosít egy Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre. Részletekért lásd: [virtuális gépek vagy VHD-k rendszerképének létrehozása](../../virtual-machines/linux/capture-image.md). Ha eléri a "virtuális gép létrehozása a rögzített lemezképből" szakaszt, akkor leállíthatja.

1. **Az Azure Linux-ügynök eltávolítása**

    1. Csatlakozhat a linuxos virtuális géphez egy SSH-ügyfél használatával.
    2. Az SSH ablakban adja meg a következő parancsot: `sudo waagent -deprovision+user` .
    3. A folytatáshoz írja be az **Y betűt** (a **-Force** paramétert hozzáadhatja az előző parancshoz, így elkerülhető a megerősítő lépés).
    d. A parancs befejezése után a **Kilépés** gombra kattintva zárja be az SSH-ügyfelet.

2. **Virtuális gép leállítása**

    1. A Azure Portal válassza ki az erőforráscsoportot (RG) és a virtuális gép lefoglalását.
    2. A VHD-fájl már általánosítva van, és létrehozhat egy új virtuális gépet a virtuális merevlemez használatával.

## <a name="next-steps"></a>További lépések

Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg a [gyakori problémák a VHD létrehozása során](common-issues-during-vhd-creation.md)című témakört.

Egyéb esetben:

* [Tanúsítsa a](get-sas-uri.md) virtuálisgép-rendszerképet, amely bemutatja, hogyan lehet tesztelni és elküldeni egy virtuálisgép-rendszerképet az Azure Marketplace minősítéshez, beleértve az *Azure Certified eszköz minősítési teszt eszközét* és annak használatát a virtuálisgép-rendszerkép hitelesítéséhez.
