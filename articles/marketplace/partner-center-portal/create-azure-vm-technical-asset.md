---
title: Az Azure Virtuálisgép technikai eszközeinek létrehozása
description: Ismerje meg, hogyan hozhat létre és konfigurálhat technikai eszközöket egy virtuális gép (VM) ajánlathoz az Azure Marketplace-hez.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 46c59e8b4e60fbe17887ea0fc375b6da758ebf50
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457399"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Az Azure Virtuálisgép technikai eszközeinek létrehozása

> [!IMPORTANT]
> Az Azure virtuálisgép-ajánlatok kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a Technikai eszközök létrehozása a [Cloud Partner Portal virtuálisgép-ajánlatához](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) található utasításokat az ajánlatok kezeléséhez.

Ez a cikk ismerteti, hogyan hozhat létre és konfigurálhat technikai eszközöket egy virtuális gép (VM) ajánlat az Azure Marketplace-en. A virtuális gép két összetevőt tartalmaz: az operációs rendszer virtuális merevlemezét (VHD) és a választható kapcsolódó adatlemezek Virtuális merevlemezeit:

* **Operációs rendszer VHD** – Tartalmazza az operációs rendszer és a megoldás, amely telepíti az ajánlatot. A virtuális merevlemez előkészítésének folyamata attól függően változik, hogy Linux-alapú, Windows-alapú vagy egyéni alapú virtuális gépről van-e szó.
* **Adatlemezek VHD-k** – dedikált, állandó tároló egy virtuális gép. Ne használja az operációs rendszer virtuális merevlemezét (például a C: meghajtót) állandó adatok tárolására.

A virtuális géplemez egy operációsrendszer-lemezt és legfeljebb 16 adatlemezt tartalmaz. Adatlemezenként egy Virtuális merevlemezt használjon, még akkor is, ha a lemez üres.

> [!NOTE]
> Függetlenül attól, hogy melyik operációs rendszert használja, csak a megoldáshoz szükséges minimális számú adatlemezt adja hozzá. Az ügyfelek nem tudják eltávolítani azokat a lemezeket, amelyek a lemezkép részét képezik a telepítés időpontjában, de a telepítés során vagy azt követően bármikor hozzáadhatnak lemezeket.

> [!IMPORTANT]
> A csomag minden virtuálisgép-lemezképének azonos számú adatlemezzel kell rendelkeznie.

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, létrehozása és tesztelése időt vesz igénybe, és mind az Azure platform, mind az ajánlat létrehozásához használt technológiák műszaki ismerete szükséges. A megoldástartománymellett a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

* Az [Azure-szolgáltatások](https://azure.microsoft.com/services/) alapvető ismerete
* [Azure-alkalmazások tervezése és megtervezése](https://azure.microsoft.com/solutions/architecture/)
* Az [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), az Azure [Storage](https://azure.microsoft.com/services/?filter=storage) és az [Azure Networking munkaismerete](https://azure.microsoft.com/services/?filter=networking)
* Az [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) munkaismerete
* A [JSON](https://www.json.org/) munkaismerete

## <a name="suggested-tools--optional"></a>Javasolt eszközök – nem kötelező

Fontolja meg az alábbi parancsfájlkezelési környezetek egyikének használatát a virtuális gépek és virtuális gépek kezeléséhez:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

Ezenkívül érdemes lehet hozzáadni a következő eszközöket a fejlesztői környezethez:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Bővítmény: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Kiterjesztés: [Szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Kiterjesztés: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Tekintse át a rendelkezésre álló eszközöket az [Azure Developer Tools](https://azure.microsoft.com/product-categories/developer-tools/) lapon, és ha a Visual Studio, a Visual Studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Virtuálisgép-lemezkép létrehozása jóváhagyott alap használatával

> [!NOTE]
> Ha a virtuális gép technikai eszközeit a saját telephelyén létrehozott lemezkép használatával szeretné létrehozni, nyissa meg [a Virtuális gép létrehozása saját lemezkép használatával](#create-a-vm-using-your-own-image)című területet.

Ez a szakasz a jóváhagyott alap használatának különböző aspektusait ismerteti, például a Távoli asztali protokoll (RDP) használatát, a virtuális gép méretének kiválasztását, a legújabb Windows-frissítések telepítését és a Virtuális merevlemez-lemezkép általánosítását.

A következő szakaszok elsősorban a Windows-alapú Virtuálisszolgáltatásokra összpontosítanak. A Linux-alapú virtuális gépek létrehozásáról további információt [az Azure által jóváhagyott Linux-disztribúciókcímű](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)témakörben talál.

> [!WARNING]
> Kövesse az ebben a témakörben található útmutatást az Azure használatához egy előre konfigurált, jóváhagyott operációs rendszert tartalmazó virtuális gép létrehozásához. Ha ez nem kompatibilis a megoldással, lehetséges, hogy hozzon létre és konfiguráljon egy helyszíni virtuális gép egy jóváhagyott operációs rendszer használatával. Ezután konfigurálhatja és előkészítheti a feltöltésre a [Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)című részben leírtak szerint.

### <a name="select-an-approved-base"></a>Jóváhagyott alap kiválasztása

Válassza ki a Windows operációs rendszert vagy a Linuxot alapként.

#### <a name="windows"></a>Windows

A Windows-alapú virtuálisgép-lemezkép operációs rendszer virtuális merevlemezének egy Azure által jóváhagyott alaplemezképen kell alapulnia, amely Windows Server vagy SQL Server rendszert tartalmaz. Először hozzon létre egy virtuális gép az alábbi képek az Azure Portalon:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (nagyvállalati, normál, webes)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (nagyvállalati, normál, webes)

> [!NOTE]
> Ha a jelenlegi Azure Portalon vagy az Azure PowerShellben használja, akkor a 2014.

#### <a name="linux"></a>Linux

Az Azure számos jóváhagyott Linux-disztribúciót kínál. Az aktuális listát az [Azure által jóváhagyott Linux-disztribúciókról](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)szóló témakörben található.

### <a name="create-vm-in-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portalon

Az alábbi lépésekkel hozd létre az alap virtuálisgép-lemezképet az [Azure Portalon:](https://ms.portal.azure.com/)

1. Jelentkezzen be az [Azure Portalon](https://ms.portal.azure.com/) a virtuális gépajánlat közzétételéhez használni kívánt Azure-előfizetéshez társított Microsoft-fiókkal.
2. Hozzon létre egy új erőforráscsoportot, és adja meg az **Erőforráscsoport nevét**, **Az Előfizetés**és **az Erőforráscsoport helyét.** További információt az [Erőforrások kezelése (Kezelés) témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)
3. Válassza a **virtuális gépek** a bal oldalon, hogy megjelenjen a virtuális gépek részleteit oldalon.
4. Válassza **a + Add lehetőséget** a **Virtuálisgép létrehozása élmény**megnyitásához.
5. Válassza ki a képet a legördülő listából, vagy kattintson az **Összes nyilvános és privát kép tallózása** elemre az összes elérhető virtuálisgép-lemezkép kereséséhez vagy tallózásához.
6. Válassza ki a virtuális gép méretét az alábbi javaslatok használatával:
    * Ha azt tervezi, hogy a virtuális merevlemez helyszíni fejlesztése, a méret nem számít. Fontolja meg a kisebb virtuális gépek használatát.
    * Ha azt tervezi, hogy a rendszerkép az Azure-ban, fontolja meg a kiválasztott rendszerkép ajánlott virtuálisgép-méretek egyikének használata.

7. A **Lemezek csoportban bontsa** ki a **Speciális** szakaszt, és állítsa a **Felügyelt lemezek használata** beállítást Nem **(Nem)** beállításra.
8. Adja meg a virtuális gép létrehozásához szükséges egyéb részleteket.
9. Válassza **a Véleményezés + létrehozás** lehetőséget a lehetőségek áttekintéséhez. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

Az Azure megkezdi a megadott virtuális gép kiépítését. A folyamat nyomon követhető a bal oldali **Virtuális gépek** fül kiválasztásával. Létrehozása után az állapot **Futás**állapotra változik.

Ha nehézségekbe ütközik az új Azure-alapú virtuális merevlemez létrehozása során, olvassa el [a Gyakori problémák a virtuális merevlemez létrehozása során (GYAKORI KÉRDÉSEK) című témakört.](https://aka.ms/VHDcreationIssues)

### <a name="connect-to-your-azure-vm"></a>Csatlakozás az Azure-beli virtuális géphez

Ez a szakasz bemutatja, hogyan csatlakozhat az Azure-ban létrehozott virtuális géphez, és hogyan jelentkezik be. Miután sikeresen csatlakozott, úgy dolgozhat a virtuális géptel, mintha helyileg jelentkezett volna be a gazdakiszolgálóra.

#### <a name="connect-to-a-windows-based-vm"></a>Csatlakozás Windows-alapú virtuális géphez

A távoli asztali ügyfél segítségével csatlakozzon az Azure-ban üzemeltetett Windows-alapú virtuális géphez. A Windows legtöbb verziója natív módon támogatja a távoli asztali protokollt (RDP). Más operációs rendszerek esetén az ügyfelekről további információt a [Távoli asztali ügyfelek](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)területen talál.

Ez a cikk bemutatja, hogyan használható a beépített Windows RDP-támogatás a virtuális géphez való csatlakozáshoz: [Csatlakozás és bejelentkezés Windows rendszerű Azure virtuális gépre.](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

> [!TIP]
> A folyamat során biztonsági figyelmeztetéseket kaphat. Például az "Az .rdp fájl ismeretlen közzétevőtől származik" vagy "A felhasználói hitelesítő adatok nem ellenőrizhetők" figyelmeztetések. Ezeket a figyelmeztetéseket nyugodtan figyelmen kívül hagyhatja.

#### <a name="connect-to-a-linux-based-vm"></a>Csatlakozás Linux-alapú virtuális géphez

Linux alapú virtuális géphez való csatlakozáshoz egy biztonságos rendszerhéj-protokoll (SSH) ügyfélre van szükség. A következő lépések az ingyenes [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminált használják.

1. Nyissa meg az [Azure Portalt.](https://ms.portal.azure.com/)
2. Keressen és válasszon **virtuális gépek**lehetőséget.
3. Válassza ki azt a virtuális gép, amelyhez csatlakozni szeretne.
4. Indítsa el a virtuális gép, ha még nem fut.
5. Válassza ki a virtuális gép nevét az **Áttekintés** lap megnyitásához.
6. Vegye figyelembe a virtuális gép nyilvános IP-címét és DNS-nevét (ha ezek az értékek nincsenek beállítva, létre kell [hoznia egy hálózati adaptert).](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)
7. Nyissa meg a PuTTY alkalmazást.
8. A PuTTY-konfiguráció párbeszédpanelen adja meg a virtuális gép IP-címét vagy DNS-nevét.

    :::image type="content" source="media/avm-putty.png" alt-text="A PuTTY terminál beállításait mutatja be. az állomásnév vagy IP-cím, valamint a Port mezők kiemelve jelennek meg.":::

9. A PuTTY terminál megnyitásához válassza a **Megnyitás** lehetőséget.
10. Amikor a rendszer kéri, adja meg a Linux virtuális gép fiók nevét és jelszavát.

Ha csatlakozási problémái vannak, olvassa el az SSH-ügyfél dokumentációját. Például [a 10.](https://www.ssh.com/ssh/putty/putty-manuals)

További részletekért, például arról, hogyan adhat hozzá asztalt egy kiépített Linux virtuális géphez, [olvassa el a Távoli asztal telepítése és konfigurálása linuxos virtuális géphez való csatlakozáshoz az Azure-ban című témakört.](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)

## <a name="create-a-vm-using-your-own-image"></a>Virtuális gép létrehozása saját lemezkép használatával

Ez a szakasz ismerteti, hogyan hozhat létre és helyezhet üzembe egy felhasználó által biztosított virtuális gép (VM) rendszerképet. Ehhez az operációs rendszer és az adatlemez VHD-lemezképeit egy Azure által telepített virtuális merevlemezről (VHD) biztosítja.

> [!NOTE]
> Ha tetszés szerint jóváhagyott alaplemezképet szeretne használni, kövesse a [Virtuálisgép-lemezkép létrehozása jóváhagyott alap használatával](#create-a-vm-image-using-an-approved-base)című útmutatóutasításait.

1. Töltse fel a képeket az Azure Storage-fiókba.
2. Telepítse a virtuális gép lemezképét.
3. Rögzítse a virtuális gép képét.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Képek feltöltése Azure-tárfiókba

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Töltse fel az általános operációs rendszer Virtuális merevlemezét és az adatlemez Virtuálismerevlemezeket az Azure tárfiókjába.

### <a name="deploy-your-image"></a>A lemezkép üzembe helyezése

Hozza létre a lemezképet az Azure Portalon vagy az Azure PowerShellhasználatával.

#### <a name="deploy-using-the-azure-portal"></a>Üzembe helyezés az Azure Portalon

1. A kezdőlapon válassza az **Erőforrás létrehozása**lehetőséget, keressen a "Sablon telepítése" kifejezésre, és válassza a **Létrehozás gombot.**
2. Válassza a Saját sablon létrehozása lehetőséget **a szerkesztőben.**

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Az Egyéni központi telepítés lap szemléltetése.":::

3. Illessze be ezt a [JSON-sablont](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) a szerkesztőbe, és válassza a **Mentés**lehetőséget.
4. Adja meg a megjelenített **egyéni központi telepítési** tulajdonságlapok paraméterértékeit.

    | Paraméter | Leírás |
    | ------------ | ------------- |
    | Felhasználói tárfiók neve | Tartalom a 2. |
    | Felhasználói tároló tárolójának neve | Tárfiók neve, ahol az általános virtuális merevlemez található |
    | Nyilvános IP DNS-neve | Nyilvános IP DNS-név. Adja meg a nyilvános IP-cím DNS-nevét az Azure Portalon az ajánlat üzembe helyezése után. |
    | Rendszergazdai felhasználónév | Rendszergazdai fiók felhasználóneve az új virtuális géphez |
    | Rendszergazdai jelszó | Rendszergazdai fiók jelszava az új virtuális géphez |
    | Operációs rendszer típusa | VM operációs rendszer: Windows vagy Linux |
    | Előfizetés azonosítója | A kijelölt előfizetés azonosítója |
    | Hely | A telepítés földrajzi helye |
    | Virtuális gép mérete | [Az Azure virtuális gép mérete,](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)például Standard_A2 |
    | Nyilvános IP-cím neve | A nyilvános IP-cím neve |
    | Virtuális gép neve | Az új virtuális gép neve |
    | Virtuális hálózat neve | A virtuális gép által használt virtuális hálózat neve |
    | Hálózati adapter neve | A virtuális hálózatot futtató hálózati kártya neve |
    | Virtuális merevlemez URL-címe | Az operációs rendszer lemezének virtuális merevlemezének teljes URL-címe |
    |  |  |

5. Miután megadta ezeket az értékeket, válassza **a Vásárlás**lehetőséget.

Az Azure megkezdi az üzembe helyezést. Új virtuális gép jön létre a megadott tárfiók elérési útján a megadott nem felügyelt virtuális merevlemezvel. Nyomon követheti a folyamatot az Azure Portalon a **virtuális gépek** kiválasztásával a portál bal oldalán. A virtuális gép létrehozásakor az állapot indításról futtatásra változik.

#### <a name="deploy-using-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>A virtuális gép képének rögzítése

Kövesse az alábbi utasításokat, amelyek megfelelnek a megközelítés:

* Azure PowerShell: [Nem felügyelt virtuálisgép-lemezkép létrehozása Azure virtuális gépről](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI: [Virtuális gép vagy virtuális merevlemez lemezképének létrehozása](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Virtuális gépek - Rögzítés](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>A virtuális gép konfigurálása

Ez a szakasz ismerteti, hogyan méretezheti, frissítheti és általánosíthatja az Azure virtuális gépek. Ezek a lépések szükségesek a virtuális gép azure piactéren való üzembe helyezéséhez.

### <a name="sizing-the-vhds"></a>A VHD-k méretezése

Ha kiválasztotta az operációs rendszerrel (és opcionálisan további szolgáltatásokkal) előre konfigurált virtuális gépek egyikét, akkor már kiválasztott egy szabványos Azure virtuális gépméretet. A megoldás indítása egy előre konfigurált operációs rendszer az ajánlott megközelítés. Ha azonban manuálisan telepít iszit, az elsődleges virtuális merevlemezt a virtuális gép lemezképében kell méreteznie:

* Windows esetén az operációs rendszer virtuális merevlemezét 127–128 GB-os rögzített formátumú virtuális merevlemezként kell létrehozni.
* Linux esetén ezt a virtuális merevlemezt 30–50 GB-os rögzített formátumú virtuális merevlemezként kell létrehozni.

Ha a fizikai méret kisebb, mint 127–128 GB, a virtuális merevlemeznek bővíthetőnek (ritka/dinamikus) kell lennie. A rendelkezésre álló alap Windows- és SQL Server-lemezképek már megfelelnek ezeknek a követelményeknek, ezért ne módosítsa a virtuális merevlemez formátumát vagy méretét.

Az adatlemezek akár 1 TB méretűek is lehetnek. A méret meghatározásakor ne feledje, hogy az ügyfelek nem méretezhetik át a virtuális központi gépeket egy lemezképen belül a telepítés időpontjában. A VHD-k adatlemezét rögzített formátumú VHD-ként kell létrehozni. Kibonthatónak (ritka/dinamikus) is kibonthatónak kell lenniük. Az adatlemezek kezdetben üresek vagy tartalmazhatnak adatokat.

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

Az operációs rendszer virtuális gépeinek alaplemezeinek tartalmazniuk kell a közzétett frissítéseket a közzétett dátumig. A létrehozott operációs rendszer virtuális merevlemezének közzététele előtt győződjön meg arról, hogy frissíti az operációs rendszert és az összes telepített szolgáltatást a legújabb biztonsági és karbantartási javításokkal.

Windows Server esetén futtassa a **Frissítések keresése** parancsot.

Linux disztribúciók esetén a frissítéseket általában parancssori eszközzel vagy grafikus segédprogrammal töltik le és telepítik. Például az Ubuntu Linux biztosítja az [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) parancsot és az [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) eszközt az operációs rendszer frissítéséhez.

### <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

Magas szintű biztonságot tarthat a megoldáslemezképeihez az Azure Marketplace-en. Az alábbi cikk a biztonsági konfigurációk és eljárások ellenőrző listáját tartalmazza: [Biztonsági javaslatok az Azure Marketplace-rendszerképekhez.](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images) Ezek a javaslatok közül néhány a Linux-alapú rendszerképek, de a legtöbb vonatkozik a virtuális gép rendszerkép.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfigurációs és ütemezett feladatok végrehajtása

Ha további konfigurációra van szükség, használjon egy ütemezett feladatot, amely indításkor fut, hogy a virtuális gép telepítése után végleges módosításokat hajtson végre. Is figyelembe kell venni a következő ajánlásokat:

* Ha egyszeri feladatról van szó, a sikeres befejezés után a feladatnak törölnie kell magát.
* A konfigurációk nem támaszkodhatnak a C vagy D meghajtón kívül más meghajtókra, mert csak ez a két meghajtó mindig garantáltan létezik (a C meghajtó az operációs rendszer lemeze, a D meghajtó pedig az ideiglenes helyi lemez).

A Linux-testreszabásokról a [Virtual machine extensions and features for Linux című](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)témakörben talál további információt.

## <a name="generalize-the-image"></a>A kép általánosítása

Az Azure Piactéren az összes rendszerképnek újrafelhasználhatónak kell lennie általános módon. Ennek elérése érdekében az operációs rendszer virtuális merevlemezének általánosnak kell lennie, egy olyan műveletnek, amely eltávolítja az összes példányspecifikus azonosítót és szoftver-illesztőprogramot a virtuális gépről.

### <a name="windows"></a>Windows

A Windows operációs rendszer lemezei általánosak a [sysprep eszközzel](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Ha ezt követően frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a sysprep programot.

> [!WARNING]
> Mivel a frissítések automatikusan futhatnak, a sysprep futtatása után kapcsolja ki a virtuális gép telepítését. Ez a leállás megakadályozza, hogy a későbbi frissítések példányspecifikus módosításokat hajtsanak végre az operációs rendszeren vagy a telepített szolgáltatásokon. A sysprep futtatásáról a [Virtuális merevlemez általánosításának lépései című témakörben](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)talál további információt.

### <a name="linux"></a>Linux

A következő folyamat általánosítja a Linux virtuális gép, és újratelepíti azt egy külön virtuális gép. További információt a [Virtuális gép vagy virtuális merevlemez lemezképének létrehozása című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image) Leállíthatja, amikor eléri a "Virtuális gép létrehozása a rögzített lemezképből" szakaszt.

1. **Az Azure Linux-ügynök eltávolítása**

    1. Csatlakozzon a Linux virtuális géphez egy SSH-ügyfél használatával.
    2. Az SSH ablakban írja be `sudo waagent -deprovision+user`a következő parancsot: .
    3. Írja be az **Y** parancsot a folytatáshoz (a megerősítési lépés elkerülése érdekében hozzáadhatja az előző parancshoz a **-force** paramétert).
    d. A parancs befejezése után írja be az **Exit** parancsot az SSH-ügyfél bezárásához.

2. **Virtuális gép leállítása**

    1. Az Azure Portalon válassza ki az erőforráscsoportot (RG) és a virtuális gép lefoglalása.
    2. A virtuális merevlemez most általános, és létrehozhat egy új virtuális gép ezzel a virtuális merevlemezhasználatával.

## <a name="next-steps"></a>További lépések

Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása, lásd: [Gyakori problémák a virtuális merevlemez létrehozása során.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)

Egyéb esetben:

* [A virtuális gép lemezképének hitelesítése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) elmagyarázza, hogyan tesztelheti és küldheti el a virtuális géplemezképét az Azure Marketplace-tanúsítványhoz, többek között azt, hogy hol szerezheti be az *Azure Certified tanúsítványteszteszközét,* és hogyan használhatja azt a virtuálisgép-lemezkép hitelesítéséhez.
