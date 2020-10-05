---
title: Azure Lab Services – egyéni rendszerkép feltöltése a közös képtárba
description: Ismerteti, hogyan tölthetők fel egyéni rendszerképek a megosztott képgyűjteménybe. Az egyetemi informatikai részlegek különösen hasznosnak találják a lemezképek importálását.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 5b69ba21787c07547edfbfd5254eaf34315cfadd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712398"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Egyéni rendszerkép feltöltése a megosztott képtárba

A megosztott képkatalógus elérhető a saját egyéni rendszerképeinek importálásához a Azure Lab Services Labs-beli létrehozásához. Az egyetemi informatikai részlegek a következő okok miatt különösen hasznosnak tekintik a lemezképek importálását: 

* Nem kell manuálisan létrehozni képeket a labor sablonjának virtuális gépe használatával.
* A más eszközök, például a SCCM, az Endpoint Manager stb. használatával létrehozott képeket is feltölthet.

Ez a cikk azokat a lépéseket ismerteti, amelyekkel Egyéni rendszerképek helyezhetők el és használhatók Azure Lab Servicesban. 

> [!IMPORTANT]
> Ha fizikai labor-környezetből az az Labs-be helyezi át a lemezképeket, át kell alakítania a appropriatly. Ne egyszerűen használja fel a meglévő rendszerképeket a fizikai laborból. <br/>Részletekért lásd: [Áthelyezés fizikai laborból Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) blogbejegyzésbe.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Egyéni rendszerkép létrehozása fizikai labor-környezetből

A következő lépések bemutatják, hogyan importálhat egy olyan egyéni rendszerképet, amely egy fizikai tesztkörnyezet-környezetből indul el. A rendszer Ezután létrehoz egy virtuális merevlemezt a környezetből, és az Azure-ban importálja a megosztott rendszerkép-katalógusba, hogy Azure Lab Serviceson belül is használható legyen.

Számos lehetőség létezik a virtuális merevlemez fizikai tesztkörnyezet környezetből való létrehozásához. A következő lépések bemutatják, hogyan hozhat létre virtuális merevlemezt egy Windows Hyper-V rendszerű virtuális gépről:

1. Kezdje egy olyan Hyper-V virtuális géppel, amely a rendszerképből létrehozott fizikai tesztkörnyezet környezetében található.
    1. A virtuális gépet 1. generációs virtuális géppel kell létrehozni.
    1. A virtuális gépnek rögzített méretű lemeznek kell lennie. Ebben az ablakban is megadhatja a lemez méretét. A lemez mérete nem haladhatja meg a 128 GB-ot.<br/>    
    Azure Lab Services nem támogatja a lemezes mérettel rendelkező rendszerképeket > 128 GB-ot. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Virtuális merevlemez összekötése":::   
    1. A virtuális gépet a szokásos módon ábrázolhatja.
1. [Kapcsolódjon a virtuális géphez, és készítse elő az Azure-hoz](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).
    1. [Windows-konfigurációk beállítása az Azure-hoz](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [A virtuális gép kapcsolatának biztosítása érdekében a minimálisan szükséges Windows-szolgáltatásokat ellenőrizze](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [A távoli asztal beállításjegyzék-beállításainak frissítése](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [A Windows tűzfal szabályainak konfigurálása](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. Windows-frissítések telepítése
    1. [Telepítse az Azure virtuálisgép-ügynököt és a további konfigurációkat az itt látható módon](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    A fenti lépésekkel egy speciális rendszerkép jön létre. Általánosított rendszerkép létrehozásakor a [Sysprep eszközt](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep)is futtatnia kell. <br/>
        Hozzon létre egy speciális rendszerképet, ha szeretné karbantartani a rendszerképben található szoftverek által igényelt felhasználói könyvtárat (amely tartalmazhat fájlokat, felhasználói fiók adatait stb.).
1. Mivel a **Hyper-V** alapértelmezés szerint létrehoz egy **VHDX** -fájlt, át kell alakítania egy VHD-fájlba.
    1. Navigáljon a **Hyper-V kezelője**  ->  **művelet**  ->  **lemez szerkesztése**elemre.
    1. Itt lehetősége lesz **átalakítani** a lemezt egy VHDX a virtuális merevlemezre
    1. A lemez méretének kibontására tett kísérlet során ügyeljen arra, hogy ne lépje túl a 128 GB-ot.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Virtuális merevlemez összekötése" lapját. Ahogy korábban említettük, a méretnek nem > 128 GB-nak kell lennie.
1. Készítsen pillanatképet a felügyelt lemezről.
    Ezt megteheti a PowerShellből, a Azure Portal használatával vagy a Storage Exploreron belül a [pillanatkép létrehozása a portál vagy a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)című témakörben leírtak szerint.
1. A megosztott képtárban hozzon létre egy rendszerkép-definíciót és-verziót:
    1. [Rendszerkép-definíció létrehozása](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition)
    1. Azt is meg kell adnia, hogy speciális/általánosított képet hoz-e létre.
1. Hozza létre a labort Azure Lab Services, és válassza ki az egyéni rendszerképet a megosztott rendszerkép-katalógusból.

    Ha az operációs rendszer telepítése után kibontotta a lemezt az eredeti Hyper-V virtuális gépen, akkor a Windows C meghajtóját is ki kell terjeszteni a nem lefoglalt lemezterület használatára. Ehhez jelentkezzen be a sablon virtuális gépre a labor létrehozása után, majd kövesse az [alapszintű Kötet kiterjesztése](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume)című részhez hasonló lépéseket. Ezt a felhasználói felületen és a PowerShell használatával is elvégezheti.

## <a name="next-steps"></a>Következő lépések

* [Megosztott képgyűjtemény – áttekintés](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [A megosztott képgyűjtemény használata](how-to-use-shared-image-gallery.md)
