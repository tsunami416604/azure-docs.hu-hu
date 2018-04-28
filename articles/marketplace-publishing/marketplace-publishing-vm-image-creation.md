---
title: Hozzon létre egy virtuálisgép-lemezkép az Azure piactérről |} Microsoft Docs
description: Részletes útmutatást az Azure piactéren mások megvásárlásához a virtuális gép lemezképének létrehozásához.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: ea92275b26da4ac72f76b438f632bd1c048beb10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Útmutató a virtuálisgép-lemezkép létrehozása az Azure piactéren
Ez a cikk **2. lépés**, végigvezeti azokon a virtuális merevlemezeket (VHD), ha telepíti az Azure piactéren előkészítése. A virtuális merevlemezek a Termékváltozat alapját. A folyamat eltér attól függően, hogy meg van adva egy Linux- vagy Windows-alapú Termékváltozat. Ez a cikk mindkét forgatókönyvet ismertet. Ez a folyamat végrehajtható párhuzamosan [fióklétrehozás és a regisztrációs][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Ajánlatok és termékváltozatok megadása
Ebben a szakaszban megismerheti az ajánlatok és azok kapcsolódó termékváltozatok meghatározására.

Az ajánlat az összes termékváltozatának „szülője”. Több ajánlattal is rendelkezhet. Ajánlatait igényei szerint strukturálhatja. Az ajánlat átmeneti üzembe helyezése annak összes termékváltozatával együtt történik. A Termékváltozat azonosítók alaposan gondolja át, mert az URL-cím látható lesz:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Az Azure betekintő portál: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

Egy termékváltozata Virtuálisgép-lemezkép kereskedelmi nevét. Virtuálisgép-lemezképet tartalmazza egy operációs rendszert tároló lemezének és nulla vagy több adatlemezek. Lényegében a virtuális gép teljes tárprofiljáról van szó. Egy virtuális merevlemez lemezenként van szükség. Még akkor is üres adatlemez kell létrehozni virtuális Merevlemezt.

A használt operációs rendszertől függetlenül csak a minimális számú adatlemez szükséges a termékváltozathoz. Az ügyfelek nem távolítható el, lemezek, a telepítés során a kép részét képező, de mindig hozzáadhat lemezeket során vagy a telepítés utáni Ha szükségük van rá.

> [!IMPORTANT]
> **Ne változtassa meg egy új verzióban kép lemez számát.** Ha újra kell konfigurálnia a kép adatlemezek, adja meg egy új Termékváltozat. Egy új lemezkép verziója és a másik lemezt darabszámukból közzététele fog rendelkezik a lehetséges legfrissebb új központi telepítési új lemezkép verziója alapján ARM-sablonokat és az egyéb forgatókönyvek megoldások automatikus skálázást, az automatikus központi telepítése esetén.
>
>

### <a name="11-add-an-offer"></a>1.1 ajánlatot hozzáadása
1. Jelentkezzen be a [közzétételi Portáljára] [ link-pubportal] a értékesítő fiók használatával.
2. Válassza ki a **virtuális gépek** a közzétételi portál lapján. A kért bejegyzés mezőben adja meg az ajánlat nevét. Az ajánlat neve esetén általában a termék vagy szolgáltatás, amely azt tervezi, hogy az Azure piactéren el.
3. Kattintson a **Létrehozás** gombra.

### <a name="12-define-a-sku"></a>1.2 a Termékváltozat meghatározása
Miután hozzáadta az ajánlatot, adja meg, és azonosítja a termékváltozatok szüksége. Több ajánlatok akkor is, és minden ajánlat lehet több SKU rajta. Az ajánlat átmeneti üzembe helyezése annak összes termékváltozatával együtt történik.

1. **Adja hozzá a Termékváltozat.** A Termékváltozat szükséges egy azonosító, amely az URL-cím szerepel. Az azonosító a közzétételi profil belül egyedieknek kell lenniük, de nincs a más gyártók ütközés azonosítója nem veszélye.

   > [!NOTE]
   > Az ajánlat és SKU-azonosítók a Piactéri ajánlat URL-cím jelenik meg.
   >
   >
2. **Rövid szöveges ismertetése hozzáadása a termékváltozat.** Összegző leírások láthatók az ügyfél számára, ezért meg kell győződnie azok könnyen olvasható. Ez az információ nem kell csak a "Tesztelés való leküldés" szakasz zárolva lesz. addig szabadon szerkesztheti azt.
3. Ha Windows-alapú termékváltozatokat használ, kövesse a javasolt hivatkozásokat a Windows Server jóváhagyott verzióinak beszerzéséhez.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Hozzon létre egy Azure-kompatibilis VHD-t (Linux-alapú)
Ebben a szakaszban egy Linux-alapú Virtuálisgép-lemezkép létrehozása az Azure piactéren ajánlott eljárásai összpontosít. Részletes útmutató, tekintse meg az alábbi dokumentáció: [hozzon létre egy egyéni Linux Virtuálisgép-lemezkép](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Hozzon létre egy Azure-kompatibilis VHD-t (Windows-alapú)
Ez a szakasz a Windows Server meghatározásával az Azure piactéren SKU létrehozásához szükséges lépéseket összpontosít.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 győződjön meg arról, hogy a helyes alap virtuális merevlemezeket használ
A Virtuálisgép-lemezkép az operációs rendszer virtuális merevlemez alapján kell egy Azure-jóváhagyott alapjául szolgáló lemezképhez, amely tartalmazza a Windows Server vagy SQL Server.

A munka megkezdéséhez hozzon létre egy virtuális Gépet a következő lemezképek helyen a [Microsoft Azure-portálon][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1] [link-datactr-2008-r2])
* SQL Server 2014 ([vállalati][link-sql-2014-ent], [szabványos][link-sql-2014-std], [webes] [ link-sql-2014-web])
* Az SQL Server 2012 SP2 ([vállalati][link-sql-2012-ent], [szabványos][link-sql-2012-std], [webes] [ link-sql-2012-web])

A hivatkozások a közzétételi portálon is megtalálhatók az SKU (Termékváltozat) lapon.

> [!TIP]
> A jelenlegi Azure-portálon vagy a PowerShell használ, ha jóváhagyott közzétett 2014. szeptember 8 és újabb Windows Server-lemezképekben.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2-es verzióját a Windows-alapú virtuális gép létrehozása
A Microsoft Azure-portálról a virtuális Gépet egy jóváhagyott alapjául szolgáló lemezképhez, csupán néhány egyszerű lépésben alapján is létrehozhat. A következő részben áttekintjük a folyamat:

1. Az alapjául szolgáló lemezképhez lapon válassza ki a **virtuális gép létrehozása** az új irányíthatja [Microsoft Azure-portálon][link-azure-portal].

    ![rajz][img-acom-1]
2. Jelentkezzen be a portálra a Microsoft-fiók és jelszó használni kívánt Azure-előfizetéséhez.
3. Kövesse az utasításokat egy virtuális gép létrehozása az alapjául szolgáló lemezképhez, kijelölt használatával. Meg kell adnia a virtuális gép a gazdagép nevét (a számítógép nevét), felhasználói név (rendszergazda) és jelszót.

    ![rajz][img-portal-vm-create]
4. Adja meg a virtuális gép központi telepítéséhez:

    a.    Ha azt tervezi, a VHD-t a helyszíni kialakításához, a méret nem számít. Érdemes egy kisebb méretű virtuális gépet használni.

    b.    Amennyiben az Azure-ban kívánja fejleszteni a lemezképet, érdemes a kiválasztott lemezképhez javasolt virtuálisgép-méretek valamelyikét használni.

    c.    Díjszabási információkért tekintse meg a **ajánlott a tarifacsomagok** választó jelenik meg a portálon. Ez megjeleníti a kiadó által megadott három ajánlott méretet. (A jelen esetben a Microsoft a kiadó.)

    ![rajz][img-portal-vm-size]
5. Állítsa be a tulajdonságokat:

    a.    Gyors üzembe helyezés esetén hagyja bejelölve a tulajdonságok alapértelmezett értékeinek **opcionális konfigurációs** és **erőforráscsoport**.

    b.    A **Tárfiók**, opcionálisan válassza ki a tárfiók, amely az operációs rendszer virtuális Merevlemeze tárolni fogja.

    c.    A **erőforráscsoport**, opcionálisan válassza ki a logikai csoport a virtuális gép elhelyezéséhez.
6. Válassza ki a **hely** központi telepítéshez:

    a.    Ha azt tervezi, a VHD-t a helyszíni fejlesztéséhez, a hely nem számít, mert a rendszer feltölti a lemezképet az Azure-bA később.

    b.    Amennyiben az Azure-ban kívánja fejleszteni a lemezképet, javasolt kezdettől fogva valamelyik amerikai Microsoft Azure-régiót használni. Ez felgyorsítja a virtuális merevlemez másolása folyamatot, amely a Microsoft az Ön nevében hajt végre, amikor a hitelesítésszolgáltató kép.

    ![rajz][img-portal-vm-location]
7. Kattintson a **Create** (Létrehozás) gombra. A virtuális gép telepítése elindul. A sikeres üzembe helyezés percek kérdése, és ekkor megkezdheti a termékváltozatához tartozó lemezkép létrehozását.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 fejlesztése a VHD-t, a felhőben
Határozottan javasoljuk, hogy a VHD-t, a felhőben fejleszt Remote Desktop Protocol (RDP) használatával. RDP csatlakozni a felhasználói névvel és a kiépítés során megadott jelszót.

> [!IMPORTANT]
> **Ne használja a felügyelt lemezek.** A virtuális gép a VHD-fájlt a felhő fejlesztéséhez használt nem kell alapozni lemezeknek, mivel jelenleg nem támogatja azokat a képfájl létrehozásához.
> A virtuális gép létrehozása a választható funkciót módosíthatja az alapértelmezett lemezek kezelése.

> Ha a virtuális merevlemez fejleszt helyszíni (ez nem ajánlott), lásd: [egy virtuálisgép-lemezkép létrehozásához a helyszíni](marketplace-publishing-vm-image-creation-on-premise.md). A virtuális merevlemez letöltése nincs szükség a felhőben fejlesztői.
>
>

**Csatlakozás RDP segítségével a [Microsoft Azure-portálon][link-azure-portal]**

1. Válassza ki **minden szolgáltatás** > **virtuális gépek**.
2. A virtuális gépek panelje megnyílik. Győződjön meg arról, hogy szeretné csatlakoztatni a virtuális gép fut, és állítsa be azt a központilag telepített virtuális gépek listájáról.
3. Ekkor megnyílik egy panel, amely leírja a kiválasztott virtuális Géphez. A lap tetején kattintson **Connect**.
4. Adja meg a felhasználónevet és a kiépítés során megadott jelszót kéri.

**Csatlakozás RDP PowerShell használatával**

A helyi számítógép a távoli asztali fájl letöltéséhez használjon a [Get-AzureRemoteDesktopFile parancsmag][link-technet-2]. Ez a parancsmag használatához szükséges tudja a nevét, a szolgáltatás és a virtuális gép neve. Ha a virtuális gép hozott létre a [Microsoft Azure-portálon][link-azure-portal], ezeket az információkat a virtuális gép tulajdonságok található:

1. Válassza a Microsoft Azure portál **minden szolgáltatás** > **virtuális gépek**.
2. A virtuális gépek panelje megnyílik. Válassza ki a központilag telepített virtuális gép.
3. Ekkor megnyílik egy panel, amely leírja a kiválasztott virtuális Géphez.
4. Kattintson a **Tulajdonságok** elemre.
5. A tartománynév első része a szolgáltatásnév. Az állomásnév a virtuális gép neve.

    ![rajz][img-portal-vm-rdp]
6. A parancsmag az RDP-fájl letöltéséhez a rendszergazdának helyi asztali a létrehozott virtuális gépek a következőképpen történik.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

További információ a RDP-cikkben található az MSDN-en található [csatlakozás az Azure virtuális gép RDP-vagy SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**A virtuális gép konfigurálása, és hozzon létre a Termékváltozat**

Ha az operációs rendszer virtuális merevlemez letöltődik Hyper-v használja, és adja a virtuális gépek a Termékváltozat létrehozásának megkezdéséhez. Részletes utasítások a következő TechNet hivatkozás helyen találhatók: [Hyper-v telepítése és konfigurálása a virtuális gépek](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4, válassza ki a megfelelő virtuális merevlemez méretét
A Windows operációs rendszer virtuális Merevlemezt a Virtuálisgép-lemezkép 128 GB-os rögzített formátumú virtuális Merevlemezt, létre kell hozni.  

Ha a fizikai mérete 128 GB-nál kevesebb, a virtuális merevlemez ritka kell lennie. A megadott alap Windows és az SQL Server lemezképeket már megfelel a fenti követelményeknek, ezért nem módosítható a formátum vagy a méretét a VHD-fájl kapott.  

Az adatlemezek akkora, mint 1 TB-os lehet. Amikor eldönti, a lemez mérete, ne feledje, hogy az ügyfelek nem méretezhető virtuális merevlemezek kép központi telepítés alkalmával. Adatok lemez VHD rögzített formátumú virtuális Merevlemezt, létre kell hozni. Akkor is kell ritka. Az adatlemezek tartalmazhatnak adatokat, de lehetnek üresek is.

### <a name="35-install-the-latest-windows-patches"></a>3.5-ös verzióját telepítse a legújabb Windows javítások
Az alaplemezképek a közzétételük dátumáig kiadott javításokat tartalmazzák. Az operációs rendszer virtuális Merevlemeze közzététele előtt hozott létre, győződjön meg arról, hogy a Windows Update futtatása és a legújabb kritikus és a fontos biztonsági frissítések telepítve.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 szükség szerint további konfigurációval és ütemezéssel feladatok végrehajtása
Ha további konfigurációra van szükség, érdemes lehet módosítani végső a virtuális gép után üzembe helyezéséig indításkor futó ütemezett feladat:

* Bevált gyakorlat, hogy a feladat sikeres végrehajtása után törli magát.
* Konfiguráció nem igazolható a C vagy D meghajtó kívül, mert ezek csak két meghajtókat garantáltan mindig létezik. A C meghajtó az operációsrendszer-lemez, és a D meghajtón, az ideiglenes helyi lemez.

### <a name="37-generalize-the-image"></a>3.7 generalize a kép
Az Azure piactéren összes lemezkép újrafelhasználható általános módon kell lennie. Ez azt jelenti az operációs rendszer virtuális Merevlemeze általánosítva kell:

* A Windows, a lemezkép "Sysprep használatával létrehozott" lehet, és nem konfigurációk el kell végezni, amelyek nem támogatják a **sysprep** parancsot.
* A következő parancsot futtathatja a könyvtár % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Hogyan futtatni a Sysprep programot az operációs rendszer van megadva. lépésében a következő MSDN-cikk útmutatást: [az Azure-ba, a Windows Server VHD létrehozása és feltöltése](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. A virtuális merevlemezek egy virtuális gép telepítése
Után a virtuális merevlemezek (az általánosított virtuális merevlemez operációs rendszer és nulla vagy több adat lemez VHD-k) Azure-tárfiók feltöltött, mint egy felhasználó a Virtuálisgép-lemezkép regisztrálhatja őket. Majd tesztelheti ezt a lemezképet. Vegye figyelembe, hogy az operációs rendszer virtuális Merevlemeze általánosított, mert akkor közvetlenül a virtuális gép nem telepíthető a virtuális merevlemez URL-cím megadásával.

VM-rendszerképekkel kapcsolatos további információkért tekintse át a következő blogbejegyzésekben:

* [A Virtuálisgép-lemezkép](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Virtuális gép lemezképének PowerShell hogyan](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Az Azure VM-lemezképekkel kapcsolatban](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>A szükséges eszközök, PowerShell és az Azure CLI beállítása
* [A telepítő PowerShell hogyan](/powershell/azure/overview)
* [Hogyan lehet beállítani az Azure parancssori felület](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 a felhasználó a Virtuálisgép-lemezkép létrehozása
#### <a name="capture-vm"></a>Virtuális gép rögzítése
Kérjük, olvassa el a hivatkozások kapcsolatos útmutatás a virtuális gép API vagy PowerShell vagy az Azure parancssori felület használatával rögzítése az alábbiakban.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Kép generalize
Kérjük, olvassa el a hivatkozások kapcsolatos útmutatás a virtuális gép API vagy PowerShell vagy az Azure parancssori felület használatával rögzítése az alábbiakban.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 egy virtuális Gépet, a felhasználói VM-lemezkép központi telepítése
A virtuális gép egy felhasználó a Virtuálisgép-lemezkép központi telepítéséhez használja az aktuális [Azure-portálon](https://manage.windowsazure.com) vagy a PowerShell használatával.

**Telepítse a virtuális Gépet a jelenlegi Azure-portálon**

1. Ugrás a **új** > **számítási** > **virtuális gép** > **gyűjteményből**.

2. Nyissa meg a **képek**, majd válassza ki a Virtuálisgép-lemezkép, amelyből a virtuális gép üzembe helyezése:

   1. Figyelmesen elolvassa az melyik képfájlját választania, mert a **képek** operációsrendszer-lemezképek és a Virtuálisgép-rendszerképek listák megtekintése.
   2. Megnézi a lemezek számát is meghatározásához, hogy milyen típusú kép telepít, mert a legtöbb Virtuálisgép-lemezképek több lemez. Azonban továbbra is lehetséges, hogy csak egy egyetlen operációsrendszer-lemez, amely ezután rendelkező Virtuálisgép-lemezkép **lemezek számát** 1 értékre.

      ![rajz][img-manage-vm-select]
3. Hajtsa végre a virtuális gép létrehozása varázslót, és adja meg a virtuális gép neve, VM mérete, hely, felhasználónév és jelszó.

**A PowerShell egy virtuális gép üzembe helyezése**

Egy nagy méretű az imént létrehozott általánosított Virtuálisgép-lemezkép központi telepítéséhez használhatja a következő parancsmagokat.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> További segítségért tekintse meg az [Hibaelhárítás gyakori problémákat észlelt a virtuális merevlemez létrehozása közben].
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. A VM-lemezkép tanúsítvány beszerzése
A Virtuálisgép-lemezkép előkészítése az Azure piactér következő lépése, hogy hitelesített azt.

A folyamat egy különös hitelesítő eszközt futtató, az ellenőrzési eredmények feltöltése az Azure-tárolót a virtuális merevlemezek tároló, ajánlatot hozzáadása, a Termékváltozat meghatározása és küldése a VM-lemezkép a hitelesítésszolgáltató tartalmaz.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 eszköz letöltése és futtatása a hitelesítésszolgáltató tesztelése az Azure hitelesített
A hitelesítésszolgáltató eszköz futtatásával egy futó virtuális gépen, a felhasználó Virtuálisgép-lemezkép kiosztott annak biztosításához, hogy a Microsoft Azure-ral kompatibilis a Virtuálisgép-lemezkép. Az eszköz ellenőrzi, hogy a virtuális merevlemez előkészítésére vonatkozó irányelvek és követelmények betartását. Az eszköz eredménye egy kompatibilitási jelentést, amely során a kérelmező hitelesítésszolgáltató közzétételi Portal fel kell tölteni.

A hitelesítésszolgáltató eszköz a Windows és Linux virtuális gépek egyaránt használható. Windows-alapú virtuális gépek Powershellen keresztül csatlakozik, és csatlakozik a Linux virtuális gépek SSH.Net keresztül:

1. Először töltse le a hitelesítésszolgáltató eszköz, a [Microsoft letöltési hely][link-msft-download].
2. Nyissa meg a hitelesítő eszközt, és kattintson a **új Teszt indítása** gombra.
3. Az a **tesztelése információk** képernyőn, adjon meg egy nevet a teszt futtatásakor.
4. Adja meg, hogy Linux vagy Windows rendszerű virtuális gépről van-e szó. A választott lehetőségtől függően adja meg a további beállítási lehetőségeket.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**A hitelesítésszolgáltató eszköz csatlakozni Linux Virtuálisgép-lemezkép**
1. Válassza ki az SSH-hitelesítési módot: jelszó vagy kulcsfájl.
2. Ha a jelszó-alapú hitelesítést használ, adja meg a tartománynévrendszer (DNS) neve, a felhasználónév és a jelszót.
3. Ha kulcsfájl-hitelesítést használ, adja meg a DNS-nevét, a felhasználónév és a titkos kulcs helye.

   ![Linux virtuális gép lemezképének jelszó-hitelesítés][img-cert-vm-pswd-lnx]

   ![Linux virtuális gép lemezképének kulcsfájl hitelesítés][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**A hitelesítésszolgáltató eszköz csatlakozni egy Windows-alapú Virtuálisgép-lemezkép**
1. Adja meg a teljes méretű DNS-nevet (például MyVMName.Cloudapp.net).
2. Adja meg a felhasználónevet és jelszót.

   ![Jelszó-hitelesítés Windows Virtuálisgép-lemezkép][img-cert-vm-pswd-win]

Miután kiválasztotta a megfelelő beállításokat a Linux vagy a Windows-alapú Virtuálisgép-lemezkép számára, jelölje ki a **kapcsolat tesztelése** ellenőrizze, hogy SSH.Net vagy a PowerShell van-e érvényes kapcsolat tesztelési célokra. A kapcsolat létrejötte után válassza ki a **tovább** a teszt elindításához.

A teszt befejezése után megjelennek a tesztelt összetevőkre vonatkozó eredmények – Pass (Megfelelt), Fail (Sikertelen) vagy Warning (Figyelmeztetés).

![Linux virtuális gép lemezképének teszteseteinek][img-cert-vm-test-lnx]

![Windowsos VM-lemezkép teszteseteinek][img-cert-vm-test-win]

Ha a vizsgálat sikertelen, a lemezkép nem kell hitelesíteni. Ha ez történik, tekintse át a követelményeket, és a szükséges módosításokat.

Az automatizált vizsgálat után kell adnia további megadása a Virtuálisgép-lemezkép egy kérdőív képernyő keresztül.  Fejezze be a kérdéseket, majd válassza ki **következő**.

![Hitelesítésszolgáltató eszköz kérdőív][img-cert-vm-questionnaire]

![Hitelesítésszolgáltató eszköz kérdőív][img-cert-vm-questionnaire-2]

Miután elvégezte a kérdőív, további információkkal szolgál például a Linux virtuális gép SSH-információinak lemezkép és bármely sikertelen értékelés magyarázata. A teszteredmények és a naplófájlok a végrehajtott teszteseteinek mellett a kérdőív kérdésekre adott válaszai töltheti le. Az eredmények mentse a VHD-k ugyanabban a tárolóban.

![Hitelesítésszolgáltató vizsgálati eredményeket menteni][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 a Virtuálisgép-lemezképek a közös hozzáférésű jogosultságkódot URI lekérése
A közzététel során adja meg az egységes erőforrás-azonosítók (URI), amely a VHD-k a Termékváltozat létrehozott vezethet. Ezek a Microsoft számára szükségesek a virtuális merevlemezek eléréséhez a minősítési folyamat során. Ezért figyelembe kell egy közös hozzáférésű jogosultságkódot URI minden virtuális merevlemez létrehozásához. Ez a megadott URI kell megadni a **képek** a közzétételi Portáljára lapján.

A közös hozzáférésű jogosultságkódot létrehozni URI a következő követelményeknek kell megfelelniük:

Megjegyzés: az alábbi utasítások alapján csak a nem felügyelt lemezeket, amelyeket az egyetlen támogatott típusa alkalmazhatók.

* Közös hozzáférésű jogosultságkódot URI-azonosítók a VHD-k létrehozásakor listáját és az olvasási engedélyek megfelelőek. Ne adjon írási vagy törlési hozzáférést.
* Az időtartamot a hozzáférés legalább három (3) héten belül kell lennie, a közös hozzáférésű jogosultságkódot URI létrehozásakor.
* Hozzáférésekkel UTC szerinti idő, válassza ki az aktuális dátum előtt. Ha az aktuális dátum 2014. októberi 6 jelölje ki például 10/5/2014.

SAS URL-címet a virtuális merevlemez megosztása az Azure piactér többféle módon hozhatók létre.
Az alábbiakban a 3 javasolt eszközök:

1.  Azure Storage Explorer
2.  Microsoft Storage Explorer
3.  Azure CLI

**Az Azure Tártallózó (Windows-felhasználóknak javasolt)**

Azure Tártallózó használatával SAS URL-cím létrehozásának lépései a következők

1. Töltse le [az Azure Storage Explorer 6 Preview 3](https://azurestorageexplorer.codeplex.com/) a Codeplex webhelyen. Nyissa meg a [Azure Storage Explorer 6 Preview](https://azurestorageexplorer.codeplex.com/) kattintson **"Letölti"**.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Töltse le [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) és telepítése után kicsomagolás azt.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. A telepítés után nyissa meg az alkalmazást.
4. Kattintson a **fiók hozzáadása**.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Adja meg a tárfiók nevét, a tárfiók kulcsa és a tárolási végpontok tartomány. Ez az a storage-fiókot az Azure-előfizetéshez ahol megtartotta a VHD-t az Azure-portálon.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Miután Azure Tártallózó csatlakozik-e a megadott tárfiók, amely az összes olyan indul a tartalmazza a tárfiókon belül. Válassza ki a tárolóban, ahová másolta az operációs rendszer lemez VHD-fájlt (is adatlemezek, ha a forgatókönyvéhez alkalmazható).

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Miután kiválasztotta a blob-tároló, Azure Tártallózó elindul, megjeleníti a tárolóban lévő fájlokat. Válassza ki a képfájl (.vhd), amelyet a küldhető el.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  A .vhd fájl a tárolóban kiválasztását követően kattintson a **biztonsági** fülre.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  Az a **Blob tároló biztonsági** párbeszédpanel mezőben hagyja meg az alapértelmezett érték a a **hozzáférési szint** fülre, majd **megosztott hozzáférési aláírásokkal** fülre.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. A közös hozzáférésű jogosultságkód URI a .vhd-lemezkép létrehozásához az alábbi lépésekkel:

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Engedélyezett hozzáférési:** hozzáférésekkel UTC szerinti idő, válassza ki az aktuális dátum előtt. Ha az aktuális dátum 2014. októberi 6 jelölje ki például 10/5/2014.

    b. **Engedélyezett hozzáférési:** válassza ki a dátumot, amely legalább három hetet követően a **engedélyezett hozzáférési** dátum.

    c. **Engedélyezett műveletek:** válassza ki a **lista** és **olvasási** engedélyek.

    d. Ha a .vhd fájlt megfelelően választotta, akkor a fájl jelenik meg **eléréséhez a Blob nevének** .vhd kiterjesztéssel együtt.

    e. Kattintson a **aláírását létrehozó**.

    f. A **létrehozott megosztott hozzáférési aláírást URI-azonosítója ebben a tárolóban**, ellenőrizze a következő példának fenti:

       - Győződjön meg arról, hogy a lemezkép-fájl neve és **".vhd"** URI szerepelnek.
       - Az aláírás végén győződjön meg arról, hogy **"= rl"** jelenik meg. Ez azt jelenti, hogy olvasási és lista hozzáférés sikeresen biztosítja-e.
       - Az aláírás középső, győződjön meg arról, hogy **"sr = c"** jelenik meg. Ez azt jelenti, hogy rendelkezik-e hozzáférési tároló

11. Győződjön meg arról, hogy a létrehozott megosztott hozzáférési aláírást URI működik, kattintson a **böngészőben teszt**. A letöltési folyamatot addig kell elindítani.

12. Másolja a közös hozzáférésű jogosultságkódot URI. Ezt az URI-t kell beillesztenie a közzétételi portálon.

13. Ismételje meg a 6 – 10. minden virtuális merevlemez a Termékváltozat.

**A Microsoft Azure Tártallózó (Windows vagy MAC/Linux)**

SAS URL-cím használatával a Microsoft Azure Tártallózó létrehozásának lépései a következők

1.  Töltse le a Microsoft Azure Tártallózó űrlap [ http://storageexplorer.com/ ](http://storageexplorer.com/) webhelyet. Ugrás a [Microsoft Azure Tártallózó](http://storageexplorer.com/releasenotes.html) kattintson **"Töltse le a Windows"**.

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  A telepítés után nyissa meg az alkalmazást.

3.  Kattintson a **fiók hozzáadása**.

4.  A Microsoft Azure Tártallózó előfizetéshez jelentkezzen be a fiók konfigurálása

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Nyissa meg a tárfiók, és jelölje ki a tárolót

6.  Válassza ki **"Megosztás hozzáférési aláírás beolvasása..."** Kattintson a jobb gombbal a használatával a **tároló**

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Kezdési ideje, a lejárat időpontjának és az engedélyek frissítése a következő száma

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Kezdő időpontja:** hozzáférésekkel UTC szerinti idő, válassza ki az aktuális dátum előtt. Ha az aktuális dátum 2014. októberi 6 jelölje ki például 10/5/2014.

    b.  **Lejárati idő:** válassza ki a dátumot, amely legalább három hetet követően a **kezdete** dátum.

    c.  **Engedélyek:** válassza ki a **lista** és **olvasási** engedélyek

8.  Másolja a tároló közös hozzáférésű jogosultságkódot URI

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    Generált SAS URL-cím tároló szint és most létre kell azt hozzáadása a virtuális merevlemez nevét.

    Tároló szintű SAS URL-címének formátuma: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Helyezze be a virtuális merevlemez neve után következő SAS URL-címben a tároló neve `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Példa:

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd a VHD neveként, akkor VHD SAS URL-t `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Győződjön meg arról, hogy a lemezkép-fájl neve és **".vhd"** URI szerepelnek.
    - Az aláírás középső, győződjön meg arról, hogy **"sp = rl"** jelenik meg. Ez azt jelenti, hogy olvasási és lista hozzáférés sikeresen biztosítja-e.
    - Az aláírás középső, győződjön meg arról, hogy **"sr = c"** jelenik meg. Ez azt jelenti, hogy rendelkezik-e hozzáférési tároló

9.  Győződjön meg arról, hogy a létrehozott megosztott hozzáférési aláírást URI működik, hogy kipróbálja a böngészőben. A letöltési folyamatot addig kell elindítani

10. Másolja a közös hozzáférésű jogosultságkódot URI. Ezt az URI-t kell beillesztenie a közzétételi portálon.

11. A termékváltozatban található minden egyes virtuális merevlemeznél ismételje meg a fenti lépéseket.

**Az Azure CLI (nem Windows & folyamatos integráció ajánlott)**

Azure parancssori felület használatával SAS URL-cím létrehozásának lépései a következők

1.  Töltse le a Microsoft Azure CLI [Itt](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). A különböző hivatkozásait is megtalálhatja **[Windows](http://aka.ms/webpi-azure-cli)** és  **[MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Amennyiben a letöltés, telepítse a

3.  Hozzon létre egy PowerShell (vagy egyéb parancsfájl végrehajtható) a következő kóddal fájlt, és mentse helyileg

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Frissítse a következő paramétereket a fenti

    a. **`<StorageAccountName>`**: Adjon a tárfiók neve

    b. **`<Storage Account Key>`**: Adjon a tárfiók kulcsára

    c. **`<Permission Start Date>`**: Annak érdekében, hogy UTC szerinti idő, válassza ki az aktuális dátum előtt. Például, ha az aktuális dátum 25 2016 októberétől kezdve, majd értéket kell 10/25/2016. Azure CLI 2.0 (az parancs) használata esetén adja meg a dátum és az idő a kezdő és záró dátumát, például: 10-25-2016T00:00:00Z.

    d. **`<Permission End Date>`**: Adjon meg egy dátumot, amely legalább három hetet követően a **Kezdődátum**. Az érték legyen **11-02-2016**. Azure CLI 2.0 (az parancs) használata esetén adja meg a dátum és az idő a kezdő és záró dátumát, például: 11-02-2016T00:00:00Z.

    Az alábbiakban látható a példakódot megfelelő paraméterek frissítése után

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Powershell-szerkesztő megnyitása a "Futtatás mint" üzemmódban, majd nyissa meg a fájl a #3. lépésben. Használhatja a parancsprogram-szerkesztő, amely az operációs rendszeren érhető el.

5.  Futtassa a parancsfájlt, és azt biztosítja azokat az SAS URL-címet a tároló szintű hozzáférés

    Következő a rendszer az SAS-aláírás kimenetét, majd másolja a kijelölt része egy Jegyzettömb

    ![rajz](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Most tároló szint SAS URL-cím fog megjelenni, és hozzá kell adnia a VHD neveként azt.

    Tároló szintű SAS URL-cím #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Helyezze be a virtuális merevlemez neve után SAS URL-címben a tároló neve alább látható módon `https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Példa:

    TestRGVM201631920152.vhd a VHD neveként, akkor VHD SAS URL-t

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Győződjön meg arról, hogy a kép fájlnevét és a ".vhd" URI.
    -   Az aláírás középső, győződjön meg arról, hogy "sp = rl" jelenik meg. Ez azt jelenti, hogy olvasási és lista hozzáférés sikeresen biztosítja-e.
    -   Az aláírás középső, győződjön meg arról, hogy "sr = c" jelenik meg. Ez azt jelenti, hogy rendelkezik-e hozzáférési tároló

8.  Győződjön meg arról, hogy a létrehozott megosztott hozzáférési aláírást URI működik, hogy kipróbálja a böngészőben. A letöltési folyamatot addig kell elindítani

9.  Másolja a közös hozzáférésű jogosultságkódot URI. Ezt az URI-t kell beillesztenie a közzétételi portálon.

10. A termékváltozatban található minden egyes virtuális merevlemeznél ismételje meg a fenti lépéseket.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3, adja meg a virtuális gép lemezképére vonatkozó információ, és kérjen tanúsítási a közzétételi portálon
Miután létrehozta a ajánlatot és az SKU, írja be a Termékváltozat társított részletei:

1. Lépjen a [közzétételi Portáljára][link-pubportal], és jelentkezzen be a értékesítő fiókjával.
2. Válassza ki a **Virtuálisgép-rendszerképek** fülre.
3. A lap tetején felsorolt azonosító ténylegesen ajánlat azonosítóját, illetve nem a Termékváltozat-azonosító.
4. Töltse ki a tulajdonságok a **termékváltozatok** szakasz.
5. A **operációsrendszer-családot**, kattintson az operációs rendszer típusa az operációs rendszer virtuális merevlemez társítva.
6. Az a **operációs rendszer** mezőben, az operációs rendszert határozzák meg. Vegye figyelembe például az operációsrendszer-családot, a típus, a verziót és a frissítések formátumban. Példa: "Windows Server Datacenter 2014 R2."
7. Válassza ki a legfeljebb hat ajánlott virtuálisgép-méretek. Ezek a javaslatok beolvasása jelenik meg az ügyfél az árazás réteg panelen az Azure portálon, ha úgy döntenek, hogy a megvásárolt és a lemezkép központi telepítését. **Ezek a javaslatok csak. Az ügyfél el tudja válassza ki a lemezek, a lemezkép megadott üzenettovábbítást bármely Virtuálisgép-méretet.**
8. Adja meg a verzió. A verzió mező magában foglalja a termék-és a frissítések szemantikai verzió:
   * Verziók az űrlap X.Y.Z, ha X, Y és Z nem egész számnak kell lennie.
   * Különböző Termékváltozatai a lemezképek különböző fő- és alverzió verziója is van.
   * Egy SKU belül verziók csak kell lennie a növekményes változásokat, ami növeli a javítás verzió (a X.Y.Z Z).
9. Az a **az operációs rendszer virtuális merevlemez URL-cím** mezőbe írja be a virtuális merevlemez operációs rendszerhez létrehozott URI közös hozzáférésű jogosultságkóddal.
10. A Termékváltozat tartozó adatlemeznek esetén válassza ki a logikai egységszám (LUN), amelyet a adatlemez telepítése után csatlakoztatni szeretne.
11. Az a **LUN X virtuális merevlemez URL-cím** mezőben adja meg a közös hozzáférésű jogosultságkódot, az első adatok VHD URI létre.

    ![rajz](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Közös SAS URL-címet állít ki, és kijavítja a

|Probléma|Hibaüzenet|Javítás|Dokumentáció-hivatkozás|
|---|---|---|---|
|Hiba történt a másolása lemezképek – "?" nem található az SAS URL-címe|Hiba: A képek másolása. Nem sikerült letölteni a blob segítségével SAS URI-t.|Frissítés az SAS URL-cím használata ajánlott eszközök|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a Másolás lemezképek - "st" és "se" paraméterek nem az SAS URL-címe|Hiba: A képek másolása. Nem sikerült letölteni a blob segítségével SAS URI-t.|Az SAS URL-címet frissíteni a kezdő és befejező dátumok|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek - "sp = rl" nem SAS URL-címben másolása|Hiba: A képek másolása. Nem sikerült letölteni a blob SAS URI-jának használatával megadott|Az SAS URL-címet frissíteni beállítás "Read" & "lista engedélyekkel|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek - SAS URL-cím másolása szóközöket rendelkezik a virtuális merevlemez neve|Hiba: A képek másolása. Nem sikerült letölteni a blob segítségével SAS URI-t.|Frissítés az SAS URL-címet, szóközök nélkül|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek – SAS URL-engedélyezési hiba másolása|Hiba: A képek másolása. Töltse le a blob engedélyezési hiba miatt nem sikerült|Az SAS URL-cím újbóli létrehozása|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Hiba történt a lemezképek – SAS URL-címe "st" és "se" paraméterek másolása nem rendelkezik a megadott dátum időpont|Hiba: A képek másolása. Nem sikerült letölteni a blob helytelen SAS URL-cím miatt |SAS URL-cím indítása és a záró dátum paraméter ("st", "m") kell lennie a dátum idejű specifikációját, például a 11-02-2017T00:00:00Z, és nem csak a dátum vagy idő rövidített verziók. Úgy is során az ebben a forgatókönyvben az Azure CLI 2.0 (az parancs) használja. Ne adja meg a dátum idejű megadását, majd újra létrehozza az SAS URL-címet.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Következő lépés
Miután elkészült, az SKU-adatokkal, áthelyezheti előre a [Azure piactér marketing content útmutató][link-pushstaging]. Hogy a lépésben a közzétételi folyamat a marketing tartalmat, árképzési és más a szükséges információk biztosítása **3. lépés: a virtuális gép tesztelési kínálnak a tesztelési**, ahol tesztelni különböző használati eset forgatókönyvekben telepítése előtt a az Azure piactéren nyilvános látható és beszerzési biztosítson.  

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
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
