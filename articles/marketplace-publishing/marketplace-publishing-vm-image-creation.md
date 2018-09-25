---
title: Virtuálisgép-rendszerképek létrehozása az Azure Marketplace-en |} A Microsoft Docs
description: A beszerzési mások számára az Azure piactéren virtuálisgép-lemezkép létrehozásáról részletes útmutatást.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ms.openlocfilehash: 893b0ee70f577d9240d577e76062eea36b704058
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989872"
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Útmutató a virtuálisgép-lemezkép létrehozása az Azure Marketplace-en
Ez a cikk **2. lépés**, a virtuális merevlemezek (VHD), amely központilag telepíti az Azure Marketplace-en való előkészítésének módját mutatja. A virtuális merevlemezek az alapítvány termékváltozat. A folyamat eltér attól függően, hogy egy Linux-alapú vagy Windows-alapú Termékváltozatot. Ez a cikk mindkét forgatókönyvvel foglalkozunk. Ez a folyamat végrehajtható párhuzamosan [fióklétrehozás és a regisztrációs][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Az ajánlatok és termékváltozatok meghatározása
Ebben a szakaszban megismerheti az ajánlatok és a kapcsolódó Termékváltozatuk.

Az ajánlat az összes termékváltozatának „szülője”. Több ajánlattal is rendelkezhet. Ajánlatait igényei szerint strukturálhatja. Az ajánlat átmeneti üzembe helyezése annak összes termékváltozatával együtt történik. Termékváltozat-azonosítókat használ, alaposan gondolja át, mert azok meg fognak jelenni az URL-cím:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Az Azure betekintő portálon: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

A Termékváltozat a Virtuálisgép-lemezkép kereskedelmi neve. Virtuálisgép-rendszerkép tartalmaz egy operációsrendszer-lemezt és nulla vagy több adatlemezeket. Lényegében a virtuális gép teljes tárprofiljáról van szó. Lemezenként egy virtuális merevlemez szükséges. Még az üres lemezekhez hozható létre egy virtuális Merevlemezt.

A használt operációs rendszertől függetlenül csak a minimális számú adatlemez szükséges a termékváltozathoz. Ügyfelek nem távolítható el a lemezeket, amelyek egy lemezkép részei, a telepítéshez, de mindig hozzáadhat lemezeket során, vagy üzembe helyezés után ha szükségük van rá.

> [!IMPORTANT]
> **Az új lemezkép verzióban lemezek száma nem változik.** A kép adatlemezek újra kell konfigurálnia, ha egy új Termékváltozat meghatározása. Közzététele egy másik lemez számokkal új lemezkép verziója lesz rendelkezik a lehetséges használhatatlanná tévő új központi telepítési alapján az új lemezkép-verzió azokban az esetekben az ARM-sablonokkal és az egyéb forgatókönyvek megoldásokat automatikus skálázást, az automatikus központi telepítései.
>
>

### <a name="11-add-an-offer"></a>1.1 ajánlat hozzáadása
1. Jelentkezzen be a [közzétételi portál] [ link-pubportal] értékesítői fiókjával.
2. Válassza ki a **virtuális gépek** lapján a közzétételi portálon. A beviteli mezőben adja meg az ajánlat nevét. Az ajánlat neve általában az a név, a termék vagy szolgáltatás, amely azt tervezi, hogy az Azure piactéren.
3. Kattintson a **Létrehozás** gombra.

### <a name="12-define-a-sku"></a>1.2 a Termékváltozat meghatározása
Miután hozzáadott egy ajánlatot, és azonosíthatja a termékváltozatok szüksége. Több ajánlattal is rendelkezhet, és mindegyik ajánlathoz tartozhatnak több termékváltozatok alatta. Az ajánlat átmeneti üzembe helyezése annak összes termékváltozatával együtt történik.

1. **Adjon hozzá egy Termékváltozatot.** Az SKU-azonosítója, az URL-címben használt igényel. Az azonosító a közzétételi profil egyedinek kell lennie, de nem áll fenn használt azonosítókkal való más kiadók ütközés.

   > [!NOTE]
   > Az ajánlat és a Termékváltozat-azonosítókat a piactéren az ajánlat URL-cím jelenik meg.
   >
   >
2. **Adja meg a Termékváltozat összefoglaló leírását.** Összefoglaló leírások láthatók az ügyfelek számára, ezért meg kell győződnie, azokat könnyen olvasható. Ezt az információt nem kell zárolni az a "Átmeneti üzembe" szakaszban. addig szabadon szerkesztheti azt.
3. Ha Windows-alapú termékváltozatokat használ, kövesse a javasolt hivatkozásokat a Windows Server jóváhagyott verzióinak beszerzéséhez.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Hozzon létre egy Azure-kompatibilis virtuális Merevlemezt (Linux-alapú)
Ebben a szakaszban egy Linux-alapú Virtuálisgép-rendszerképet hoz létre az Azure Marketplace-en az ajánlott eljárásait tárgyalja. Lépésenkénti útmutató, tekintse meg az alábbi dokumentációt: [Linux rendszerű virtuális gép egyéni rendszerképének létrehozása](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Hozzon létre egy Azure-kompatibilis virtuális Merevlemezt (Windows-alapú)
Ez a szakasz az Azure Marketplace-en Windows Serveren alapuló Termékváltozat létrehozásának lépéseit tárgyalja.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1. Győződjön meg arról, hogy használja a megfelelő alap VHD-k
A Virtuálisgép-lemezkép az operációs rendszer virtuális Merevlemeze kell lennie egy Azure által jóváhagyott alaplemezkép alapján, amely tartalmazza a Windows Server vagy SQL Server.

A kezdéshez egy virtuális gép létrehozása az alábbi lemezképek egyikéből helyén található a [Microsoft Azure-portálon][link-azure-portal]:

* A Windows Server ([2012 R2 Datacenter] [hivatkozás-datactr-2012-r2], [2012 Datacenter] [hivatkozás-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 
* SQL Server 2012 SP2 

A hivatkozások a közzétételi portálon is megtalálhatók az SKU (Termékváltozat) lapon.

> [!TIP]
> Az aktuális Azure-portálon vagy a PowerShell használatakor közzétett 2014. szeptember 8 és újabb Windows Server-rendszerképeket hagyja jóvá.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2-es verzióját a Windows-alapú virtuális gép létrehozása
A Microsoft Azure Portalon a virtuális gép néhány egyszerű lépésben valamelyik jóváhagyott alaplemezkép alapján hozhat létre. Az alábbiakban látható a folyamat áttekintését:

1. Az alaplemezkép lapján válassza **virtuális gép létrehozása** a rendszer az új [Microsoft Azure-portálon][link-azure-portal].

    ![rajz][img-acom-1]
2. Jelentkezzen be a portálra a Microsoft-fiók és a használni kívánt Azure-előfizetéshez tartozó jelszóval.
3. Kövesse az utasításokat a virtuális gép létrehozása a kiválasztott alaplemezkép használatával. Meg kell adnia a gazdagép neve (a számítógép nevét), a felhasználónevet (rendszergazdaként regisztrálva) és a jelszó a virtuális gép számára.

    ![rajz][img-portal-vm-create]
4. Válassza ki a méretet a virtuális gép üzembe helyezéséhez:

    a.    Ha azt tervezi, a VHD-t a helyszíni fejlesztés, a méret nem számít. Érdemes egy kisebb méretű virtuális gépet használni.

    b.    Amennyiben az Azure-ban kívánja fejleszteni a lemezképet, érdemes a kiválasztott lemezképhez javasolt virtuálisgép-méretek valamelyikét használni.

    c.    Díjszabási információkért tekintse meg a **javasolt tarifacsomagok** választó jelenik meg a portálon. Ez megjeleníti a kiadó által megadott három ajánlott méretet. (A jelen esetben a Microsoft a kiadó.)

    ![rajz][img-portal-vm-size]
5. Tulajdonságainak megadása:

    a.    Gyors üzembe helyezéshez, hagyja bejelölve az alapértelmezett értékeket (erőforráscsoport) részen **opcionális konfigurációs** és **erőforráscsoport**.

    b.    A **Tárfiók**, igény szerint válassza ki a tárfiókot, amely az operációs rendszer virtuális Merevlemeze tárolni fogja.

    c.    A **erőforráscsoport**, bejelölheti a logikai csoport a virtuális gép elhelyezéséhez.
6. Válassza ki a **hely** üzembe helyezéshez:

    a.    Ha azt tervezi, a VHD-t a helyszíni fejlesztés, a hely nem számít, mivel a rendszer feltölti a lemezképet az Azure-bA később.

    b.    Amennyiben az Azure-ban kívánja fejleszteni a lemezképet, javasolt kezdettől fogva valamelyik amerikai Microsoft Azure-régiót használni. Ez felgyorsítja a VHD másolási folyamat, amely a Microsoft az Ön nevében hajt végre, a lemezkép minősítésre való elküldésekor.

    ![rajz][img-portal-vm-location]
7. Kattintson a **Create** (Létrehozás) gombra. A virtuális gép elindul, és üzembe helyezése. A sikeres üzembe helyezés percek kérdése, és ekkor megkezdheti a termékváltozatához tartozó lemezkép létrehozását.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3-as fejlessze virtuális Lemezképét a felhőben
Határozottan javasoljuk a VHD-t a felhőben fejleszteni Remote Desktop Protocol (RDP) használatával. RDP csatlakozni a felhasználói névvel és üzembe helyezés során megadott jelszót.

> [!IMPORTANT]
> **Ne használja a felügyelt lemezeket.** A virtuális gépet a virtuális Merevlemezt a felhőbe való fejlesztéséhez használt nem, jelenleg nem támogatja azokat a lemezkép létrehozása felügyelt lemezeket kell alapulnia.
> A választható szolgáltatás módosíthatja az alapértelmezett, a lemezek a virtuális gép létrehozása felügyelt.

> Ha a virtuális merevlemez fejlesztése a helyszínen (amit nem javasolunk), lásd: [virtuálisgép-lemezkép létrehozása a helyszíni](marketplace-publishing-vm-image-creation-on-premise.md). Virtuális merevlemez letöltésére már nem szükséges, ha a felhőre fejleszt.
>
>

**Csatlakozás RDP használatával a [Microsoft Azure-portálon][link-azure-portal]**

1. Válassza ki **minden szolgáltatás** > **virtuális gépek**.
2. A virtuális gépek panel nyílik meg. Győződjön meg arról, hogy szeretné csatlakoztatni a virtuális gép fut-e, és az üzembe helyezett virtuális gépek listájából válassza.
3. Megnyílik egy panel, amely leírja a kiválasztott virtuális gép. A lap tetején kattintson **Connect**.
4. Adja meg a felhasználónevet és jelszót, amely üzembe helyezésekor megadott kéri.

**PowerShell-lel RDP-n keresztül csatlakozik**

Töltse le a távoli asztali fájl a helyi számítógépre, használja a [Get-AzureRemoteDesktopFile parancsmagot][link-technet-2]. Ez a parancsmag használatához kell ismernie a szolgáltatás és a virtuális gép nevét. Létrehozta a virtuális gépről a [Microsoft Azure-portálon][link-azure-portal], ezeket az információkat a virtuális gép tulajdonságok talál:

1. A Microsoft Azure Portalon válassza ki a **minden szolgáltatás** > **virtuális gépek**.
2. A virtuális gépek panel nyílik meg. Válassza ki a központilag telepített virtuális gép.
3. Megnyílik egy panel, amely leírja a kiválasztott virtuális gép.
4. Kattintson a **Tulajdonságok** elemre.
5. A tartománynév első része a szolgáltatásnév. Az állomásnév a virtuális gép neve.

    ![rajz][img-portal-vm-rdp]
6. Töltse le az RDP-fájlt a létrehozott virtuális Gépen a rendszergazda helyi számítógépére, a parancsmag a következőképpen történik.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

RDP további információt az MSDN webhelyen megtalálható a cikk [csatlakozhat az Azure virtuális Gépekhez RDP vagy SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Konfiguráljon egy virtuális Gépet, és saját Termékváltozat létrehozása**

Az operációs rendszer VHD letöltése után használja a Hyper-v, és konfiguráljon egy virtuális Gépet a Termékváltozat létrehozásának megkezdéséhez. Részletes lépéseket tekinthet meg a következő TechNet-hivatkozás: [telepítése Hyper-v és a virtuális gép konfigurálása](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 virtuális merevlemez megfelelő méretének kiválasztása
A Windows operációs rendszer virtuális Merevlemeze a Virtuálisgép-lemezkép egy 128 GB-os rögzített formátumú VHD-fájlként kell létrehoznia.  

Ha a fizikai méret kisebb, mint 128 GB, a virtuális merevlemez ritka fájlnak kell lennie. Biztosított alap Windows- és SQL Server rendszerképek megfelel ezeknek a követelményeknek, ezért ne módosítsa a formátumot vagy beszerzett virtuális merevlemez méretét.  

Az adatlemezek mérete akár 1 TB is lehet. A lemezméret eldöntésekor ne feledje, hogy ügyfelek nem tudják átméretezni a virtuális merevlemezek lemezképen belüli a telepítéshez. Az adatlemezek virtuális rögzített formátumú VHD-fájlként kell létrehoznia. Akkor is ritka fájlnak kell lennie. Az adatlemezek tartalmazhatnak adatokat, de lehetnek üresek is.

### <a name="35-install-the-latest-windows-patches"></a>3.5-ös verzióját telepítheti a legújabb Windows-javításokat
Az alaplemezképek a közzétételük dátumáig kiadott javításokat tartalmazzák. Az operációs rendszer virtuális Merevlemeze közzététele előtt hozott létre, győződjön meg arról, hogy Windows Update futtatása és, hogy a legújabb kritikus és a fontos biztonsági frissítések telepítve van-e.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6. hajtsa végre a további konfigurálást és ütemezzen feladatokat szükség szerint
Ha további konfigurációs van szüksége, fontolja meg végső módosításokat a virtuális gép után üzembe helyezéskor elvégzik rendszerindításkor futó ütemezett feladatot:

* Bevált gyakorlat, hogy a feladat sikeres végrehajtása után törli magát.
* Nincs konfiguráció meghajtók C, D meghajtóra támaszkodhat, mivel ez az a két meghajtó, amely mindig garantáltan létezik. A C meghajtó az operációsrendszer-lemez, és a D meghajtón, az átmeneti helyi lemezt.

### <a name="37-generalize-the-image"></a>3.7. a lemezkép általánossá tétele
Az Azure piactéren elérhető összes rendszerkép újrafelhasználható újrafelhasználhatónak kell lennie. Más szóval az operációs rendszer virtuális Merevlemeze általánossá kell tenni:

* A Windows, a lemezkép "Sysprep használatával előkészített" kell lennie, és semmilyen konfigurálást kell elvégezni, amelyek nem támogatják a **sysprep** parancsot.
* A következő parancsot a WINDIR%\System32\Sysprep könyvtárból futtathatja.

        sysprep.exe /generalize /oobe /shutdown

  Hogyan Sysprep az operációs rendszernek. lépés a következő MSDN-cikkben szereplő útmutatást: [létrehozása és feltöltése az Azure-ban Windows Server VHD](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Virtuális gép üzembe helyezése a VHD-ből
A virtuális merevlemezek (az általánosított operációs rendszer virtuális Merevlemeze és nulla vagy több adatlemezzel VHD-k) Azure storage-fiókkal való feltöltése után felhasználói Virtuálisgép-lemezképként regisztrálhatja őket. Ezután tesztelheti ezt a lemezképet. Vegye figyelembe, hogy az operációs rendszer virtuális Merevlemeze általánosítva van, mert azt közvetlenül a virtuális gép nem telepíthető azáltal, hogy a virtuális merevlemez URL-CÍMÉT.

Virtuálisgép-rendszerképek kapcsolatos további információkért tekintse át a következő blogbejegyzésekben:

* [Virtuálisgép-lemezkép](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Virtuális gép rendszerkép PowerShell hogyan](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Tudnivalók az Azure-beli Virtuálisgép-rendszerképek](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-classic-cli"></a>A szükséges eszközök, PowerShell és az Azure klasszikus parancssori felület beállítása
* [PowerShell-telepítés](/powershell/azure/overview)
* [Az Azure klasszikus parancssori felület beállítása](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 felhasználói Virtuálisgép-lemezkép létrehozása
#### <a name="capture-vm"></a>Virtuális gép rögzítése
Kérjük, olvassa el a virtuális gép API vagy PowerShell vagy az Azure CLI-vel rögzítése útmutatást az alábbiakban a hivatkozások.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Lemezkép általánossá tétele
Kérjük, olvassa el a virtuális gép API vagy PowerShell vagy az Azure CLI-vel rögzítése útmutatást az alábbiakban a hivatkozások.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 egy felhasználói Virtuálisgép-rendszerképet a virtuális gép telepítése
Virtuális gép felhasználói Virtuálisgép-lemezkép központi telepítéséhez használhatja az aktuális [az Azure portal](https://manage.windowsazure.com) vagy a PowerShell használatával.

**Virtuális gép üzembe helyezése a jelenlegi Azure Portalról**

1. Lépjen a **új** > **számítási** > **virtuális gép** > **katalógusból**.

2. Lépjen a **saját lemezképek**, majd válassza ki a Virtuálisgép-lemezképet, melyben szeretné üzembe helyezni egy virtuális Gépet:

   1. Figyeljen hogy melyik lemezképet választja, mert a **saját lemezképek** nézetben operációsrendszer-lemezképek és a Virtuálisgép-rendszerképek listája látható.
   2. Megnézzük, a lemezek számát is meghatározásához, hogy milyen típusú rendszerképet helyez üzembe, mivel a legtöbb Virtuálisgép-lemezkép egynél több lemezt tartalmaz. Azonban továbbra is lehetséges, hogy csak egy egyetlen operációsrendszer-lemez, amely már rendelkezni fog egy Virtuálisgép-lemezkép **lemezek száma** állítsa 1-re.

      ![rajz][img-manage-vm-select]
3. Kövesse a virtuális gép létrehozása varázsló, és adja meg a virtuális gép neve, virtuális gép méretét, helyét, felhasználónév és jelszó.

**Virtuális gép üzembe helyezése a PowerShell**

Nagy virtuális gép üzembe helyezése az imént létrehozott általánosított Virtuálisgép-rendszerképet, használhatja a következő parancsmagokat.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> További segítségért tekintse meg [hibaelhárítási gyakori problémák virtuális merevlemez létrehozása során észlelt].
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. A Virtuálisgép-lemezkép minősítés beszerzése
A következő lépése a Virtuálisgép-lemezkép előkészítése az Azure piactéren, hogy kell azt.

Ez a folyamat tartalmazza egy speciális minősítőeszköz fut, az ellenőrzés eredményeinek feltöltését a VHD-ket az Azure container, az ajánlat hozzáadását, a Termékváltozat meghatározását és elküldése a Virtuálisgép-lemezkép minősítésre.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1-es eszköz letöltése és futtatása a minősítési tesztelés az Azure Certified
A minősítőeszköz fut egy futó virtuális gépen, a felhasználói Virtuálisgép-lemezkép üzembe helyezett annak biztosításához, hogy a Virtuálisgép-lemezkép a Microsoft Azure-ral kompatibilis. Az eszköz ellenőrzi, hogy a virtuális merevlemez előkészítésére vonatkozó irányelvek és követelmények betartását. Az eszköz kimenete egy kompatibilitási jelentés, amelyet fel kell tölteni a kérelmező hitelesítésszolgáltató közben a közzétételi portálon.

A minősítőeszköz Windows és Linux rendszerű virtuális gépeken is használható. Windows-alapú virtuális gépek PowerShell-lel csatlakozik, és Linux rendszerű virtuális gépek SSH.Net segítségével kapcsolódik:

1. Először töltse le a minősítőeszközt a [Microsoft letöltési hely][link-msft-download].
2. Nyissa meg a minősítőeszközt, és kattintson a **új Teszt indítása** gombra.
3. Az a **információ tesztelése** képernyőn, adja meg a tesztfuttatás nevét.
4. Adja meg, hogy Linux vagy Windows rendszerű virtuális gépről van-e szó. A választott lehetőségtől függően adja meg a további beállítási lehetőségeket.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**A minősítőeszköz csatlakozhat egy Linux rendszerű Virtuálisgép-lemezkép**
1. Válassza ki az SSH-hitelesítési módot: jelszó vagy kulcsfájl.
2. Ha jelszóalapú hitelesítést használ, adja meg a tartománynévrendszer (DNS) nevét, a felhasználónév és a jelszót.
3. Ha kulcsfájl hitelesítést használ, adja meg a DNS-nevet, a felhasználónév és a titkos kulcs helyét.

   ![Linux virtuális gép lemezképének jelszavas hitelesítés][img-cert-vm-pswd-lnx]

   ![Linux virtuális gép lemezképének kulcsfájlalapú hitelesítés][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**A minősítőeszköz csatlakozhat Windows-alapú Virtuálisgép-lemezkép**
1. Adja meg a teljes virtuális gép DNS-nevet (például MyVMName.Cloudapp.net).
2. Adja meg a felhasználónevet és jelszót.

   ![Jelszavas hitelesítés Windows VM-lemezkép][img-cert-vm-pswd-win]

Miután kiválasztotta a megfelelő beállításokat a Linux vagy Windows-alapú virtuális gép lemezképének, válassza ki a **kapcsolat tesztelése** , hogy SSH.Net- vagy PowerShell legyen-e egy érvényes kapcsolati tesztelési célokra. A kapcsolat létrejötte után válassza ki a **tovább** a teszt elindításához.

A teszt befejezése után megjelennek a tesztelt összetevőkre vonatkozó eredmények – Pass (Megfelelt), Fail (Sikertelen) vagy Warning (Figyelmeztetés).

![A Linux rendszerű Virtuálisgép-lemezkép esetek][img-cert-vm-test-lnx]

![Windows virtuális gép lemezképének esetek][img-cert-vm-test-win]

Ha bármelyik teszt sikertelen, a lemezkép nem kell hitelesíteni. Ha ilyet tapasztal, tekintse át a követelményeket, és végezze el a szükséges módosításokat.

Az automatikus vizsgálatot követően rendszer további bemeneti biztosítsanak a Virtuálisgép-lemezkép használatával egy kérdőív képernyő.  Végezze el a kérdéseket, és válassza ki **tovább**.

![Minősítési eszköz kérdőív][img-cert-vm-questionnaire]

![Minősítési eszköz kérdőív][img-cert-vm-questionnaire-2]

Miután végrehajtotta a kérdőívet, a lemezkép és annak magyarázatát, bármely sikertelen értékelések megadhat további információk, például a Linux rendszerű virtuális gép SSH hozzáférési információk. Letöltheti a teszt eredményeit és a naplófájlok a végrehajtott teszt esetek mellett a válaszokat a kérdőívet. A-eredményeket menteni a VHD-ként ugyanabban a tárolóban.

![Mentse a minősítési terhelésiteszt-eredményei][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 a közös hozzáférésű jogosultságkód URI Azonosítójának lekérése a Virtuálisgép-rendszerképek
A közzétételi folyamat során adja meg a virtuális merevlemezhez a termékváltozathoz létrehozott minden egyes vezető egységes erőforrás-azonosítók (URI-k). Ezek a Microsoft számára szükségesek a virtuális merevlemezek eléréséhez a minősítési folyamat során. Ezért szeretne létrehozni egy közös hozzáférésű jogosultságkód URI-t minden egyes virtuális merevlemezhez. Ez az az URI-t kell megadni a **lemezképek** fülre a közzétételi portálon.

A közös hozzáférésű jogosultságkód URI létrehozott meg kell felelnie az alábbi követelményeknek:

Megjegyzés: az alábbi utasítások alapján csak a nem felügyelt lemezek, amelyek az egyetlen olyan altípus támogatott vonatkoznak.

* Közös hozzáférésű jogosultságkód URL-címének a VHD-k létrehozásakor a lista és olvasási engedélyekkel elegendőek. Ne adjon írási vagy törlési hozzáférést.
* A hozzáférés időtartama legalább három (3) héten belül kell lennie, a közös hozzáférésű jogosultságkód URI Azonosítójának létrehozásakor.
* A védelme érdekében UTC szerinti idő, válassza ki a nap, az aktuális dátum előtt. Jelölje be például, ha az aktuális dátum későbbi, 2014. október 6., 10/5/2014.

SAS URL-címet a virtuális merevlemez megosztása az Azure piactér többféle módon hozhatók létre.
Az alábbiakban a 3 javasolt eszközök:

1.  Azure Storage Explorer
2.  Microsoft Storage Explorer
3.  Azure CLI

**Az Azure Storage Explorer (Windows-felhasználók esetén ajánlott)**

Az alábbiakban a lépéseket az SAS URL-cím létrehozása az Azure Storage Explorer használatával

1. Töltse le [az Azure Storage Explorer 6 Preview 3](https://azurestorageexplorer.codeplex.com/) a Codeplex Tárházportálról. Lépjen a [Azure Storage Explorer 6 előzetes verziója](https://azurestorageexplorer.codeplex.com/) kattintson **"Letölti"**.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Töltse le [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) , és miután kicsomagolta, telepítse.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Azt követően, nyissa meg az alkalmazást.
4. Kattintson a **fiók hozzáadása**.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Adja meg a tárfiók neve, a tárfiók-kulcsot és a tárolási végpontok tartományát. Ez az a tárfiók Azure-előfizetésében ahol kell tárolni a VHD-t az Azure Portalon.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Miután az Azure Storage Explorer az adott storage-fiók össze van kapcsolva, amely az összes olyan indul a tartalmazza a tárfiókon belül. Válassza ki a tárolóban, ahová másolta az operációs rendszer lemez VHD-fájl (is az adatlemezeket, ha azok az adott forgatókönyvnek megfelelő).

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Miután kiválasztotta a blob-tároló, Azure Storage Explorer elindul, a tárolóban lévő fájlokat megjelenítő. Válassza ki a lemezképfájl (.vhd), amelyet be kell nyújtani.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Válassza ki a .vhd fájlt a tárolóban, kattintson a **biztonsági** fülre.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  Az a **Blob tároló biztonsági** párbeszédpanelen hagyja meg az alapértelmezett értékeket a a **hozzáférési szint** fülre, majd **közös hozzáférésű Jogosultságkódokat** fülre.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. A .vhd lemezképek közös hozzáférésű jogosultságkód URI létrehozásához az alábbi lépésekkel:

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Engedélyezett hozzáférési:** révén UTC szerinti idő, válassza ki a nap, az aktuális dátum előtt. Jelölje be például, ha az aktuális dátum későbbi, 2014. október 6., 10/5/2014.

    b. **Engedélyezett hozzáférési:** válassza ki a dátumot, amely legalább 3 hét után a **engedélyezett hozzáférési** dátum.

    c. **Engedélyezett műveletek:** válassza ki a **lista** és **olvasási** engedélyeket.

    d. Ha kiválasztotta a .vhd fájl megfelelően, majd a fájl jelenik meg **eléréséhez blobnév** .vhd kiterjesztéssel együtt.

    e. Kattintson a **aláírását létrehozó**.

    f. A **létrehozott megosztott hozzáférési Jogosultságkód URI-ját ebben a tárolóban**, ellenőrizze a következő, a kiemelt fenti:

       - Győződjön meg arról, hogy a lemezkép-fájl neve és **".vhd"** található az URI-t.
       - Az aláírás végén, ügyeljen arra, hogy **"= rl"** jelenik meg. Ez azt mutatja be, hogy olvasási és a lista hozzáférés sikeresen lett-e megadva.
       - Az aláírás közepén, ellenőrizze, hogy **"sr = c"** jelenik meg. Ez azt jelenti, hogy a tároló hozzáférési

11. Győződjön meg arról, hogy a létrehozott megosztott hozzáférési jogosultságkód URI működik, kattintson a **tesztelés böngészőben**. El kell indítaniuk a letöltési folyamatot.

12. Másolja a közös hozzáférésű jogosultságkód URI-t. Ezt az URI-t kell beillesztenie a közzétételi portálon.

13. Minden egyes virtuális Merevlemezhez a termékváltozat esetében ismételje meg a 6 – 10.

**A Microsoft Azure Storage Explorer (Windows/MAC/Linux)**

Az alábbiakban a lépéseket az SAS URL-cím létrehozása a Microsoft Azure Storage Explorer használatával

1.  Töltse le a Microsoft Azure Storage Explorer űrlap [ http://storageexplorer.com/ ](http://storageexplorer.com/) webhelyén. Lépjen a [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) kattintson **"Töltse le a Windows"**.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Azt követően, nyissa meg az alkalmazást.

3.  Kattintson a **fiók hozzáadása**.

4.  A Microsoft Azure Storage Explorer konfigurálásához jelentkezzen be a fiókjába az előfizetéshez

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Ugrás a storage-fiókot, és válassza ki a tárolót

6.  Válassza ki **"Get megosztás hozzáférésű jogosultságkód igénylése..."** a jobb gombbal a a **tároló**

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Kezdési idő, lejárati időpontot és engedélyek frissítése következő kiszolgálónként

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Kezdő időpont:** révén UTC szerinti idő, válassza ki a nap, az aktuális dátum előtt. Jelölje be például, ha az aktuális dátum későbbi, 2014. október 6., 10/5/2014.

    b.  **Lejárati idő:** válassza ki a dátumot, amely legalább 3 hét után a **kezdő időpont** dátum.

    c.  **Engedélyek:** válassza ki a **lista** és **olvasási** engedélyek

8.  Másolja a tároló közös hozzáférésű jogosultságkód URI

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    A létrehozott SAS URL-cím tároló szint és most hozzá kell adnunk a virtuális merevlemez neve szerepel.

    Tároló Fiókszintű SAS URL-cím formátuma: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Helyezze be a virtuális merevlemez neve után, az alábbi SAS URL-címet a tároló neve `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Példa:

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd a virtuális merevlemez nevét, majd a virtuális Merevlemezek SAS URL-cím `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Győződjön meg arról, hogy a lemezkép-fájl neve és **".vhd"** található az URI-t.
    - Az aláírás közepén, ellenőrizze, hogy **"sp = rl"** jelenik meg. Ez azt mutatja be, hogy olvasási és a lista hozzáférés sikeresen lett-e megadva.
    - Az aláírás közepén, ellenőrizze, hogy **"sr = c"** jelenik meg. Ez azt jelenti, hogy a tároló hozzáférési

9.  Győződjön meg arról, hogy a létrehozott megosztott hozzáférési jogosultságkód URI működik, a tesztelés a böngészőben. A letöltési folyamat azt kell kezdődnie

10. Másolja a közös hozzáférésű jogosultságkód URI-t. Ezt az URI-t kell beillesztenie a közzétételi portálon.

11. A termékváltozatban található minden egyes virtuális merevlemeznél ismételje meg a fenti lépéseket.

**Az Azure klasszikus parancssori felület (nem-Windows és a folyamatos integráció ajánlott)**

Az SAS URL-cím létrehozása a klasszikus Azure CLI-vel lépései a következők

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]

1.  Töltse le az Azure klasszikus parancssori felületét [Itt](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Különböző mutató hivatkozásokat is talál **[Windows](http://aka.ms/webpi-azure-cli)** és  **[MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Miután letöltötte, telepítse a

3.  Hozzon létre egy PowerShell-(vagy egyéb parancsfájl végrehajtható) a következő kód fájlt, és mentse helyileg

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Frissítse a következő paramétereket felett

    a. **`<StorageAccountName>`**: Adjon a tárfiók nevét

    b. **`<Storage Account Key>`**: Adjon a tárfiók kulcsa

    c. **`<Permission Start Date>`**: A védelme érdekében, UTC szerinti idő, válassza ki a nap, az aktuális dátum előtt. Például, ha az aktuális dátum későbbi, 2016. október 26., majd mesterpéldányaként 2016/10/25. Ha az Azure CLI 2.0-s vagy újabb verzióját használja, adja meg a dátum- és a kezdő és záró dátumát, például: 10-25 – 2016T00:00:00Z.

    d. **`<Permission End Date>`**: Adjon meg egy dátumot, amely legalább 3 hét után a **Kezdődátum**. Az érték lehet **11-02-2016**. Ha az Azure CLI 2.0-s vagy újabb verzióját használja, adja meg a dátum- és a kezdő és záró dátumát, például: 11-02-2016T00:00:00Z.

    Következő példakód a megfelelő paramétereinek frissítése után

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Nyissa meg a Powershell-szerkesztőt a "Futtatás mint rendszergazda" mód, és nyissa meg a fájlt a #3. lépésben. Használhat bármilyen parancsprogram-szerkesztő, amely a különböző operációs rendszerekhez érhető el.

5.  Futtassa a szkriptet, és azt biztosít Önnek a SAS URL-címet a tároló hozzáférési

    Következő a rendszer az SAS-aláírás kimenetét, és másolja a kijelölt részt egy Jegyzettömb

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Most már tároló szint SAS URL-címet kap, és adja hozzá a virtuális merevlemez nevét, a kell.

    Tároló szint SAS URL-címe #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Helyezze be a virtuális merevlemez neve után SAS URL-címet a tároló neve alább látható módon `https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Példa:

    TestRGVM201631920152.vhd a virtuális merevlemez nevét, majd a virtuális Merevlemezek SAS URL-cím

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Győződjön meg arról, hogy a bináris fájl neve és a ".vhd" az URI-t is.
    -   Az aláírás közepén, ellenőrizze, hogy "sp = rl" jelenik meg. Ez azt mutatja be, hogy olvasási és a lista hozzáférés sikeresen lett-e megadva.
    -   Az aláírás közepén, ellenőrizze, hogy "sr = c" jelenik meg. Ez azt jelenti, hogy a tároló hozzáférési

8.  Győződjön meg arról, hogy a létrehozott megosztott hozzáférési jogosultságkód URI működik, a tesztelés a böngészőben. A letöltési folyamat azt kell kezdődnie

9.  Másolja a közös hozzáférésű jogosultságkód URI-t. Ezt az URI-t kell beillesztenie a közzétételi portálon.

10. A termékváltozatban található minden egyes virtuális merevlemeznél ismételje meg a fenti lépéseket.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 a Virtuálisgép-lemezkép információt biztosítanak, és kérjen minősítést a közzétételi portálon
Az ajánlat és a Termékváltozat létrehozása után a Termékváltozathoz társított lemezkép adatait kell megadnia:

1. Nyissa meg a [közzétételi portál][link-pubportal], majd jelentkezzen be értékesítői fiókjával.
2. Válassza ki a **Virtuálisgép-rendszerképek** fülre.
3. Az azonosító, a lap tetején megjelenik, amely ténylegesen az ajánlat azonosítója, és nem a Termékváltozat azonosítója.
4. Adja meg a tulajdonságokat a **termékváltozatok** szakaszban.
5. A **operációs rendszer termékcsaládját**, kattintson az operációs rendszer típusa, az operációs rendszer virtuális Merevlemeze társított.
6. Az a **operációs rendszer** mezőbe operációs rendszert határozzák meg. Célszerű megadni az operációs rendszer termékcsaládját, típusát, verzióját és frissítéseket. Ilyen például, "A Windows Server Datacenter 2014 R2."
7. Válasszon legfeljebb hat javasolt Virtuálisgép-méretet. Ezek a javaslatok, amelyek az ügyfélnek a Tarifacsomag panelen az Azure Portalon a amikor megvásárlása és a rendszerkép üzembe helyezése mellett döntenek jelennek meg. **Ezek csak ajánlások. Az ügyfél el tudja válasszon, amely megfelelő a lemezképben megadott lemezek bármely Virtuálisgép-méretet.**
8. Adja meg a verziót. A verzió mező magában foglalja egy, a termék és a frissítések sémantická verze:
   * Verziók az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lenniük.
   * A lemezképek különböző termékváltozatban különböző fő- és alverzió verziók is rendelkezhet.
   * A Termékváltozat belül verziók csak kell lennie a növekményes változásokat, amely növelheti a javítást (X.Y.Z a – Z).
9. Az a **OS VHD URL-cím** mezőbe írja be a közös hozzáférésű jogosultságkód URI-t az operációs rendszer virtuális Merevlemeze számára létrehozott.
10. Ha ez a Termékváltozat hozzárendelt adatlemezek, válassza ki a logikaiegység-szám (LUN), amelyhez szeretné csatlakoztatni a telepítés után az adatlemezt.
11. Az a **LUN X VHD URL-cím** mezőbe írja be a közös hozzáférésű jogosultságkód URI létrehozása az első adatok VHD-t.

    ![rajz](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Gyakori problémák SAS URL-címe & javítások

|Probléma|Hibaüzenet|Javítás|Dokumentáció-hivatkozás|
|---|---|---|---|
|Hiba történt a Másolás-lemezkép - "?" nem található az SAS URL-címe|Hiba: A lemezképek másolása. Nem sikerült letölteni a blob használatával a megadott SAS URI-t.|Frissítés az SAS URL-cím használata ajánlott eszközök|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a Másolás lemezkép - "st" és "se" paraméterek nem az SAS URL-címe|Hiba: A lemezképek másolása. Nem sikerült letölteni a blob használatával a megadott SAS URI-t.|A kezdő és záró dátuma, az SAS URL-cím frissítése|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek – "sp = rl" nem az SAS URL-cím másolása|Hiba: A lemezképek másolása. Nem sikerült letölteni a blob SAS URI-t használó megadott|Beállítás "Olvasási" & "List engedélyeket a SAS URL-cím frissítése|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek - SAS URL-cím másolása térközt rendelkezik a virtuális merevlemez neve|Hiba: A lemezképek másolása. Nem sikerült letölteni a blob használatával a megadott SAS URI-t.|Frissítse a SAS URL-címét, szóközök nélkül|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek – SAS URL-engedélyezési hiba másolása|Hiba: A lemezképek másolása. Töltse le a blob engedélyezési hiba miatt nem sikerült|Az SAS URL-címet generálni|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek – az SAS URL-címe "st" és "se" paraméterek másolása nem rendelkezik teljes dátum-idő specifikáció|Hiba: A lemezképek másolása. Nem sikerült helytelen SAS URL-cím miatt blob letöltése |Teljes dátum-idő előírás 11 például SAS URL-cím indítása és a záró dátum paraméter ("st", "se") van szüksége-02-2017T00:00:00Z, és nem csak a dátum vagy akkor használhatja rövidített verzió alkalommal. Lehetséges, hogy ezt a forgatókönyvet az Azure CLI 2.0-s vagy újabb verzió. Mindenképpen adja meg a teljes dátum-idő specifikációinak, és az SAS URL-címet generálni.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Következő lépés
Miután elkészült, a Termékváltozat adatokkal, áthelyezheti előre a [Azure Marketplace-en marketing content útmutató][link-pushstaging]. Hogy a közzétételi folyamat lépésben biztosítanak a marketinganyagot, a díjszabást és a szükséges egyéb információkat előtti **3. lépés: a virtuális gép tesztelése az átmeneti állapotában is kínálnak**, tesztelni különböző használatieset-forgatókönyvek üzembe helyezése előtt, a az Azure piactér nyilvános átláthatósága és beszerzési kínálnak.  

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
