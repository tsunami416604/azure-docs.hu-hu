---
title: Automatizálhatja a StorSimple fileshare Vészhelyreállítás az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerteti a lépéseket és a egy vész-helyreállítási megoldás StorSimple a Microsoft Azure storage-ban üzemeltetett fájlmegosztások létrehozásának ajánlott eljárásai.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: vidarmsft
ms.openlocfilehash: d6b8341f16cca29fe5bedca34749f47053a14ebb
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666938"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatizált vész-helyreállítási megoldás StorSimple található fájlmegosztások az Azure Site Recovery használatával
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple hibrid felhőalapú tárolási megoldás, amely kiküszöböli a strukturálatlan adatok gyakran társított fájlmegosztások a. A StorSimple felhőalapú tárolás kiterjesztése, a helyszíni megoldást, és automatikusan rétegek adatok között a helyszíni és felhőbeli tárolására használja. Integrált adatvédelmet biztosít, helyi és felhőalapú pillanatfelvételek, szükségtelenné teszi a tárolási infrastruktúrák sprawling.

[Az Azure Site Recovery](../site-recovery/site-recovery-overview.md) egy Azure-alapú szolgáltatás, amely a vész-helyreállítási lehetőségeket kínál replikálása, feladatátvétele és helyreállítási virtuális gépek replikálásával. Az Azure Site Recovery replikációs technológiái révén folyamatosan replikálni, védheti meg és zökkenőmentesen átveheti a virtuális gépek és alkalmazások privát és nyilvános vagy a szolgáltatott felhők számos támogat.

Az Azure Site Recovery virtuálisgép-replikációt és a StorSimple felhőalapú pillanatképkezelési funkciókat használ, védheti meg a fájl teljes kiszolgálói környezet. Egy bekövetkező szolgáltatáskimaradás esetén egyetlen kattintással használhatja ahhoz, hogy a fájlmegosztások online az Azure-ban mindössze néhány perc múlva.

Ez a dokumentum részletesen bemutatja, hogyan hozzon létre egy vész-helyreállítási megoldást a fájlmegosztások, a StorSimple-tárolás, tárolt és elvégezheti a tervezett vagy nem tervezett és indított feladatátvételi tesztek ugyanúgy egy helyreállítási terv használatával. Lényegében bemutatja, hogyan módosíthatja a helyreállítási terv az Azure Site Recovery-tárolók engedélyezése a StorSimple feladatátvételek során vészhelyreállítási forgatókönyveket. Ismerteti továbbá által támogatott konfigurációk és előfeltételek. Jelen dokumentum céljából feltételezzük, hogy ismeri az Azure Site Recovery-és StorSimple alapjait.

## <a name="supported-azure-site-recovery-deployment-options"></a>Támogatott az Azure Site Recovery üzembe helyezési beállítások
Ügyfelek üzembe helyezése a fájlkiszolgálók, fizikai kiszolgálóként vagy Hyper-V vagy VMware virtuális gépek (VM), és ezután fájlmegosztásokat hozhat létre StorSimple tárolóból faragottnak kötetekről. Az Azure Site Recovery szolgáltatás védi a fizikai és virtuális üzemelő példányok az Azure-bA vagy egy másodlagos helyre. Ez a dokumentum a Vészhelyreállítási megoldást az Azure virtuális gép a Hyper-v környezetben üzemeltetett fájlkiszolgálók esetében a helyreállítási helyen, és a StorSimple-storage-fájlmegosztásokkal részleteit ismerteti. Más olyan forgatókönyvekben, ahol a fájlkiszolgáló virtuális gép VMware virtuális gép vagy fizikai gép hasonló módon implementálható.

## <a name="prerequisites"></a>Előfeltételek
Azure Site Recovery-fájlmegosztások a StorSimple-storage-ban üzemeltetett használó egykattintásos vészhelyreállítási megoldást Végrehajtási előfeltételei a következők:

   - A helyszíni virtuális gép a Hyper-V vagy VMware vagy fizikai gép üzemeltethetők a Windows Server 2012 R2-fájl server
   - Az Azure StorSimple manager regisztrálva a StorSimple eszköz helyi tárterület
   - A StorSimple felhőalapú készülék létrehozása az Azure StorSimple Manager. A berendezés leállított állapotban tartani is.
   - A StorSimple tárolóeszközt konfigurált fürtköteten fájlmegosztások
   - [Az Azure Site Recovery services-tároló](../site-recovery/site-recovery-vmm-to-vmm.md) egy adott Microsoft Azure-előfizetés létrehozása

Ezenkívül, ha az Azure helyreállítási webhelyként, futtassa a [Azure Virtual Machine Readiness Assessment eszköz](http://azure.microsoft.com/downloads/vm-readiness-assessment/) szolgáltatások biztosításához, hogy kompatibilis az Azure virtuális gépek és az Azure Site Recovery-beli virtuális gépeken.

Késés (amely magasabb költségeket eredményezhet) problémák, győződjön meg arról, hogy hoz létre a StorSimple felhőalapú készülék, az automation-fiók és a storage elkerülése érdekében fiók(ok) ugyanabban a régióban.

## <a name="enable-dr-for-storsimple-file-shares"></a>A StorSimple-fájlmegosztások DR engedélyezése
Egyes összetevői által a helyszíni környezetben kell lenniük ahhoz, hogy teljes replikálását és helyreállítását. Ez a szakasz azt ismerteti, hogyan lehet:
    
   - Az Active Directory és DNS-replikálás beállítása (nem kötelező)
   - Az Azure Site Recovery segítségével a fájlkiszolgáló virtuális gép védelmének engedélyezése
   - A StorSimple-kötetek védelmének engedélyezése
   - A hálózat konfigurálása

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Az Active Directory és DNS-replikálás beállítása (nem kötelező)
Ha meg szeretné védeni a gépek Active Directory és DNS futnak, így elérhetők a DR-helyen, kell explicit módon védeni őket (úgy, hogy a fájlkiszolgálók érhetők el a hitelesítést a feladatátvételt követően). Az ügyfél a helyszíni környezet összetettsége alapján két ajánlott lehetőség van.

#### <a name="option-1"></a>1. lehetőséget
Ha az ügyfél rendelkezik egy kis számú alkalmazást, a teljes egyetlen tartományvezérlő a helyszíni hely és lesz majd a domain controller gép replikálása egy másodlagos Azure Site Recovery replikációs használatát javasoljuk a teljes helyre feladatátvétele a webhely (Ez a megfelelő hely – hely és a hely – Azure).

#### <a name="option-2"></a>2. lehetőséget
Ha az ügyfél nagy számú az alkalmazások, Active Directory-erdő fut, és néhány alkalmazások egyszerre lesz sikertelen, akkor azt javasoljuk, hogy a DR webhelyen további tartományvezérlő beállításának (vagy egy másodlagos helyre vagy az Azure-ban).

Tekintse meg [automatizált Vészhelyreállítási megoldást az Active Directory és DNS az Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) utasításokat, ha egy tartományvezérlőt a DR webhelyen elérhetővé. Ez a dokumentum a többi feltételezzük egy tartományvezérlő nem érhető el a DR-helyen.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Az Azure Site Recovery segítségével a fájlkiszolgáló virtuális gép védelmének engedélyezése
Ebben a lépésben elő kell készíteni a helyszíni fájl-kiszolgálói környezet, hozzon létre és készítse elő az Azure Site Recovery-tároló és fájlt a virtuális gép védelmének engedélyezéséhez.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>A helyszíni fájl-kiszolgálói környezet előkészítése
1. Állítsa be a **felhasználói fiókok felügyelete** való **soha nem kérek értesítést**. Ez azért szükséges, hogy az iSCSI-tárolók az Azure Site Recovery a feladatátvételt követően csatlakozni az Azure automation parancsfájlokat használhat.
   
   1. Nyomja le a Windows billentyű + Q, és keressen rá a **UAC**.  
   1. Válassza ki **módosítása felhasználói fiókok felügyelete beállításainak**.  
   1. A sáv húzza felfelé haladva történik **soha nem kérek értesítést**.  
   1. Kattintson a **OK** majd **Igen** amikor a rendszer kéri.  
   
      ![Felhasználói fiókok beállításai](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Virtuálisgép-ügynök telepítése minden egyes, a fájlkiszolgáló virtuális gépeket. Ez azért szükséges, hogy az Azure automation parancsfájlok futtathatók a sikertelen a virtuális gépen.
   
   1. [Töltse le az ügynököt](http://aka.ms/vmagentwin) való `C:\\Users\\<username>\\Downloads`.
   1. Nyissa meg a Windows Powershellt (Futtatás rendszergazdaként) rendszergazdai módban, és adja meg, keresse meg a letöltési helyre a következő parancsot:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > A fájlnév verziójától függően változhatnak.
      
1. Kattintson a **Tovább** gombra.
1. Fogadja el a **feltételeket a szerződés** majd **tovább**.
1. Kattintson a **Befejezés** gombra.
1. Elfogyott a tárterület StorSimple faragottnak köteteket használó fájlmegosztásokat hozhat létre. További információkért lásd: [kötetek kezelése a StorSimple Manager szolgáltatás használatával](storsimple-manage-volumes.md).
   
   1. A helyszíni virtuális gépeken, nyomja le a Windows billentyű + Q, és keressen rá a **iSCSI**.
   1. Válassza ki **iSCSI-kezdeményező**.
   1. Válassza ki a **konfigurációs** fület, és másolja a kezdeményező neve.
   1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
   1. Válassza ki a **StorSimple** lapot, majd a StorSimple Manager szolgáltatás, amely tartalmazza a fizikai eszköztől.
   1. Hozzon létre kötettároló(k), és hozzon létre (ek). (Ezek a kötetek vonatkoznak a fájlmegosztáso a fájlkiszolgáló virtuális gépeket). Másolja a kezdeményező neve, és a hozzáférés-vezérlési rekordok egy megfelelő nevet adjon a kötetek létrehozásakor.
   1. Válassza ki a **konfigurálása** lapon és a jegyezze fel az eszköz IP-címét.
   1. A helyszíni virtuális gépek, nyissa meg a **iSCSI-kezdeményező** újra és írja be a IP-cím a gyors csatlakozás szakaszban. Kattintson a **gyors csatlakozás** (az eszköz Ezzel létrejön a kapcsolat).
   1. Nyissa meg az Azure Portalon, és válassza a **kötetek és eszközök** fülre. Kattintson a **automatikus konfigurálása**. Meg kell jelennie a kötetet, amelyet létrehozott.
   1. A portálon, válassza ki a **eszközök** lapot, majd **új virtuális gép létrehozásához.** (A virtuális eszköz alkalmazva lesznek, ha feladatátvétel történik). Az új virtuális eszköz további költségek elkerülése érdekében offline állapotban is kell tartani. Offline állapotba a virtuális eszköz, nyissa meg a **virtuális gépek** szakaszt a portálon, és állítsa le.
   1. Lépjen vissza a helyszíni virtuális gépeket, és nyissa meg a Lemezkezelés (válassza ki, és nyomja le a Windows billentyű + X **Lemezkezelés**).
   1. Láthatja, hogy néhány további lemezek (attól függően, a létrehozott kötetek száma). Kattintson a jobb gombbal a először egy select **lemez inicializálása**, és válassza ki **OK**. Kattintson a jobb gombbal a **Unallocated** szakaszban jelölje be **új egyszerű kötet**, rendeljen hozzá meghajtóbetűjelet, és fejezze be a varázslót.
   1. Minden lemez esetében ismételje meg a l. A most már megtekintheti az összes lemez **Ez a gép** a Windows Intézőben.
   1. A fájl- és tárolási szolgáltatások szerepkör használatával hozzon létre fájlmegosztást ezeken a köteteken.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Hozhat létre és készítse elő az Azure Site Recovery-tároló
Tekintse meg a [Azure Site Recovery dokumentációja](../site-recovery/site-recovery-hyper-v-site-to-azure.md) Ismerkedés az Azure Site Recovery a fájlkiszolgáló virtuális gép védelmének megkezdése előtt.

#### <a name="to-enable-protection"></a>Védelem engedélyezése
1. Az iSCSI cél(ok) bontsa a kapcsolatot a helyszíni virtuális gépek Azure Site Recovery segítségével védeni kívánt:
   
   1. Nyomja le a Windows billentyű + Q, és keressen rá a **iSCSI**.
   1. Válassza ki **állítsa be az iSCSI-kezdeményező**.
   1. Válassza le a korábban csatlakoztatott StorSimple-eszköz. Másik megoldásként kikapcsolhatja a fájlkiszolgáló néhány percet a védelem engedélyezésekor.
      
   > [!NOTE]
   > Ennek hatására a fájlmegosztások átmenetileg nem érhető el.
   
1. [Virtuális gép védelmének engedélyezéséhez](../site-recovery/site-recovery-hyper-v-site-to-azure.md) a fájlkiszolgáló virtuális gép az Azure Site Recovery portálról.
1. Amikor megkezdődik a kezdeti szinkronizálás, újra a cél újra. Nyissa meg az iSCSI-kezdeményező, jelölje be a StorSimple-eszközt, majd kattintson a **Connect**.
1. Ha a szinkronizálás befejeződött, és a virtuális gép állapota **védett**, válassza ki a virtuális Gépet, jelölje be a **konfigurálása** lapra, és ennek megfelelően frissítse a virtuális gép a hálózat (Ez az a hálózat, amely a sikertelen keresztül Virtuális gép egy része lesz). Ha a hálózat nem jelenik meg, az azt jelenti, hogy a szinkronizálás továbbra is történik.

### <a name="enable-protection-of-storsimple-volumes"></a>A StorSimple-kötetek védelmének engedélyezése
Ha nincs kiválasztva a **ehhez a kötethez alapértelmezett biztonsági mentés engedélyezése** első számú megoldás a StorSimple-köteteket, ugorjon a **biztonsági mentési szabályzatok** a StorSimple Manager szolgáltatásra, és a megfelelő biztonsági mentési szabályzat létrehozása a köteteket. Azt javasoljuk, hogy a helyreállításipont-célkitűzés (RPO), amely, tekintse meg az alkalmazás szeretné beállítani a biztonsági mentések gyakoriságát.

### <a name="configure-the-network"></a>A hálózat konfigurálása
A fájlkiszolgáló virtuális gép, hálózati beállítások konfigurálása az Azure Site Recoveryben, hogy a Virtuálisgép-hálózatok a feladatátvételt követően a megfelelő DR-hálózathoz van csatlakoztatva.

Kiválaszthatja, hogy a virtuális Géphez a **replikált elemek** lap segítségével konfigurálhatja a hálózati beállításokat, az alábbi ábrán látható módon.

![Számítás és hálózat](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv hozhat létre fájlmegosztást a feladatátvételi folyamat automatizálása ASR-ben. Egy bekövetkező szolgáltatáskimaradás esetén szakaszhoz a fájlmegosztások egyetlen kattintással mindössze néhány perc múlva. Ahhoz, hogy ezt az automatizálást, szüksége lesz egy Azure automation-fiókot.

#### <a name="to-create-an-automation-account"></a>Az Automation-fiók létrehozása
1. Nyissa meg az Azure Portalt &gt; **Automation** szakaszban.
1. Kattintson a **+ Hozzáadás** gombra, ekkor megnyílik a panel alatt.
   
   ![Automation-fiók hozzáadása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Név – adjon meg egy új automation-fiók
   - Előfizetés – válasszon előfizetést
   - Resource group - új/válasszon egy meglévő erőforráscsoport létrehozása
   - Hely - hely, és tárolja az ugyanazon földrajzi vagy régióban, amelyben a StorSimple felhőalapú készülék és a Storage-fiókok létrejöttek.
   - Azure-beli futtató fiók létrehozása – Select **Igen** lehetőséget.
   
1. Nyissa meg az Automation-fiókot, kattintson a **Runbookok** &gt; **Tallózás a katalógusban** a szükséges runbookokat importálni az automation-fiókot.
1. Adja hozzá az alábbi runbookok felderítésével **vész-helyreállítási** címke a katalógusban:
   
   - Teszt feladatátvétel (TFO) után a StorSimple-kötetek karbantartása
   - Feladatátvétel a StorSimple-kötettároló
   - Kötetek a StorSimple-eszköz csatlakoztatása a feladatátvétel után
   - Távolítsa el az egyéni szkriptek futtatására szolgáló bővítmény az Azure virtuális Gépen
   - Indítsa el a StorSimple virtuális készülék
   
      ![Tallózás a katalógusban](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Válassza ki a runbook az automation-fiókot az összes parancsfájl közzététele, és kattintson a **szerkesztése** &gt; **közzététel** , majd **Igen** , az ellenőrző üzenetet. Elvégezte a lépést a **Runbookok** lap jelenik meg a következő:
   
   ![Runbookok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Az automation-fiókban kattintson **változók** &gt; **változó hozzáadása** , és adja hozzá a következő változókat. Kiválaszthatja, hogy ezek az eszközök titkosításához. Ezeket a változókat a helyreállítási terv megadott. Ha a helyreállítási terv, amely a következő lépésben létrehozza név TestPlan, majd a változók kell TestPlan StorSimRegKey, TestPlan-AzureSubscriptionName, és így tovább.

   - **BaseUrl**: Resource Manager URL-címét az Azure-felhőben. Első használatával **Get-AzureRmEnvironment |} Select-Object Name, ResourceManagerUrl** parancsmagot.
   - *RecoveryPlanName* **- ResourceGroupName**: Resource Manager-csoport, amely a StorSimple-erőforrás.
   - *RecoveryPlanName* **- ManagerName**: A StorSimple-erőforrás, amely a StorSimple-eszköz rendelkezik.
   - *RecoveryPlanName* **- eszköznév**: A StorSimple-eszköz feladatátvételt kell végrehajtani.
   - *RecoveryPlanName* **- DeviceIpAddress**: az eszköz IP-cím (Ez található a **eszközök** lapon a StorSimple-Eszközkezelő szakasz &gt; **beállításai** &gt; **Hálózati** &gt; **DNS-beállítások** csoport).
   - *RecoveryPlanName* **- VolumeContainers**: egy vesszővel tagolt karakterláncot kíván végrehajtani, több mint; például az eszköz kötettárolóinak: volcon1, volcon2, volcon3.
   - *RecoveryPlanName* **- TargetDeviceName**: A StorSimple felhőalapú készülék, amelyre a tárolók vannak feladatátvételt kell végrehajtani.
   - *RecoveryPlanName* **- TargetDeviceIpAddress**: az eszköznek az IP-címét (Ez található a **virtuális gép** szakasz &gt; **beállítások** csoport &gt; **hálózatkezelés** lap).
   - *RecoveryPlanName* **- StorageAccountName**: A tárfiók nevét, amely a parancsfájl (melynek futtathatók a feladatátvételen átesett virtuális gép) tárolhatja. Ez lehet bármely storage-fiók, amely ideiglenesen tárolja a parancsfájl lemezterületet rendelkezik.
   - *RecoveryPlanName* **- StorageAccountKey**: a fenti tárfiók hozzáférési kulcsára.
   - *RecoveryPlanName* **- VMGUIDS**: esetén a virtuális gép védelmét, az Azure Site Recovery rendel minden virtuális gép egy egyedi azonosítója, amely részletezi a feladatátvételen átesett virtuális gép. A VMGUID beszerzéséhez válassza ki a **Recovery Services** fülre, és **védett elem** &gt; **védelmi csoportok** &gt;  **Gépek** &gt; **tulajdonságok**. Ha több virtuális gépet, majd a GUID hozzáadása vesszővel elválasztva karakterláncként.

    Például, ha a helyreállítási terv neve nem fileServerpredayRP majd a **változók**, **kapcsolatok** és **tanúsítványok** lapon meg kell jelennie a következő hozzáadása után az eszközök.

      ![Objektumok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Töltse fel a StorSimple 8000 sorozat Runbook-modulhoz az Automation-fiókban. Használja az alábbi lépéseket egy modul hozzáadása:
   
   1. Nyissa meg a powershellt, hozzon létre egy új mappát, és lépjen a mappába.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Töltse le a nuget parancssori felület ugyanabba a mappába, az 1. lépés alatt.
      Nuget.exe különböző verziói elérhetők a [nuget letölti](https://www.nuget.org/downloads). Egyes letöltési hivatkozást közvetlenül egy .exe-fájlra mutat ezért ügyeljen arra, hogy kattintson a jobb gombbal, és mentse a fájlt a számítógép helyett a böngészőben történő futtatásával.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. A függő SDK letöltése
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Hozzon létre egy Azure Automation-Runbook modul a StorSimple 8000 sorozat felügyelethez. Használja az alábbi parancsokat egy automatizálási a modul zip-fájl létrehozásához.
         
      ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importálja az Azure Automation modul zip-fájlja (Microsoft.Azure.Management.StorSimple8000Series.zip) fenti létrehozott. Ez az Automation-fiók kiválasztásával teheti meg, kattintson a **modulok** megosztott erőforrások, majd kattintson a **modul hozzáadása**.
   
   A StorSimple 8000 sorozat modul importálása után a **modulok** lapon meg kell jelennie a következő:
   
      ![Modulok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Nyissa meg a **Recovery Services** szakaszt, és válassza ki a korábban létrehozott Azure Site Recovery-tárból.
1. Válassza ki a **helyreállítási tervek (Site Recovery)** parancsát **kezelés** csoportot, és a egy új helyreállítási terv létrehozása a következő:
   
   - Kattintson a **+ helyreállítás terv** gombra, ekkor megnyílik a panel alatt.
      
      ![Helyreállítási terv létrehozása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Adja meg a helyreállítási terv neve, és válassza ki a forrás, cél és üzembe helyezési modell értékeket.
   
   - Válassza ki a virtuális gépeket a védelmi csoportból, amely tartalmazza a helyreállítási tervben szereplő, és kattintson a kívánt **OK** gombra.
   
   - Válassza ki a korábban létrehozott helyreállítási tervet, kattintson **Testreszabás** gombra kattintva nyissa meg a helyreállítási terv testreszabási nézetet.
   
   - Kattintson a jobb gombbal **minden csoport leállítása** kattintson **előzetes művelet hozzáadása**.
   
   - Megnyitja Insert művelet panel, adjon meg egy nevet, válassza ki **elsődleges oldal** hol beállítás futtatni, válassza ki Automation-fiók (amely hozzáadta a runbookok), majd válassza ki a beállítás a **feladatátvétel – a StorSimple-Kötettároló**  runbook.
   
   - Kattintson a jobb gombbal **csoport 1: Start** kattintson **adja hozzá a védett elemek** lehetőséget, majd válassza ki a helyreállítási tervet, majd kattintson a védendő virtuális gépeit **Ok** gombra. Nem kötelező, ha már van kijelölve a virtuális gépeket.
   
   - Kattintson a jobb gombbal **csoport 1: Start** kattintson **művelet közzététele** lehetőséget, majd adja hozzá az alábbi parancsfájlok:  
      
      - Runbook indítása – a StorSimple-virtuális-készülék  
      - Több mint – a StorSimple-kötettároló runbook sikertelen  
      - Runbook csatlakoztatási kötetek-után – feladatátvétel  
      - Eltávolítás –--szkriptbővítmény runbook  
        
   - Manuális művelet után a fenti 4 szkriptek hozzáadása az egyazon **1. csoport: utólagos lépések** szakaszban. Ez a művelet nem a pont, ahol ellenőrizheti, hogy minden helyesen működik. Ez a művelet csak feladatátvételi teszt részeként hozzá kell adnia (tehát csak a kiválasztott a **feladatátvételi teszt** jelölőnégyzet).
    
   - A manuális műveletet után adja hozzá a **karbantartása** használja ugyanazt az eljárást, a más runbookokat is használ. **Mentés** a helyreállítási tervben.
    
   > [!NOTE]
   > Feladatátvételi teszt futtatásakor Ellenőrizze minden, a manuális műveletet lépéseknél, mert a StorSimple-köteteket, amelyek korábban lett klónozta a céleszközön törlődik a karbantartás részeként a manuális művelet befejeződése után.
       
      ![Recoery terv](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Feladatátvételi teszt végrehajtása
Tekintse meg a [Active Directory-DR-megoldásként](../site-recovery/site-recovery-active-directory.md) útmutatója szempontok adott Active Directory feladatátvételi teszt során. A helyszíni telepítés ne zavarják minden, a teszt feladatátvétel esetén. A StorSimple-köteteket, amelyek a helyszíni virtuális Géphez csatolt voltak a StorSimple felhőalapú készülék az Azure-ban, a rendszer klónozza. Egy virtuális Gépet tesztelési célokra van kerülnek sorra az Azure-ban, és a klónozott kötetek vannak csatolva a virtuális Gépet.

#### <a name="to-perform-the-test-failover"></a>A feladatátvételi teszt végrehajtásához
1. Az Azure Portalon válassza ki a Site Recovery-tárból.
1. Kattintson a helyreállítási terv létrehozása a fájlkiszolgáló virtuális gép számára.
1. Kattintson a **feladatátvételi teszt**.
1. Válassza ki az Azure virtuális hálózat, amelyhez az Azure virtuális gépek csatlakozik a feladatátvételt követően.
   
   ![Feladatátvétel indítása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Kattintson a virtuális Gépre, és nyissa meg a tulajdonságait, vagy a követheti a folyamat állapotát a **teszt feladatátvételi feladatot** a tároló neve &gt; **feladatok** &gt; **Site Recovery-feladatok**.
1. A feladatátvétel befejezése után is kell látni a replika Azure machine jelennek meg az Azure Portalon &gt; **virtuális gépek**. Az ellenőrzések elvégzése.
1. Az ellenőrzés után kattintson a **ellenőrzések teljes**. Ezzel eltávolítja a StorSimple-kötetek és a StorSimple felhőalapú készülék leállítása.
1. Amikor elkészült, kattintson a **feladatátvételi teszt utáni karbantartás** elemre a helyreállítási terven. Kiegészítő jegyezheti fel és mentheti a feladatátvételi tesztet megfigyeléseket. Ez a művelet törli a feladatátvételi teszt során létrehozott virtuális gépet.

## <a name="perform-a-planned-failover"></a>Tervezett feladatátvétel végrehajtása
   Egy tervezett feladatátvétel során a helyszíni fájlkiszolgáló virtuális gép szabályosan leállítja és a kötetek a StorSimple eszközön a biztonsági mentési pillanatkép készül a felhő. A StorSimple-köteteket a virtuális eszközön feladatátvétel, a replika virtuális gép állapotba kerül az Azure-ban, és a virtuális géphez csatolt kötetek.

#### <a name="to-perform-a-planned-failover"></a>Tervezett feladatátvétel végrehajtásához
1. Az Azure Portalon válassza ki a **a Recovery services** tároló &gt; **helyreállítási tervek (Site Recovery)** &gt; **recoveryplan_name** létrehozott a a fájlkiszolgáló virtuális Gépet.
1. A helyreállítási terv panelen kattintson a **további** &gt; **tervezett feladatátvételt**.  

   ![Helyreállítási terv](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Az a **tervezett feladatátvétel megerősítése** panelen válassza ki a forrás és a célhelyek és cél kiválasztása hálózati, majd kattintson a pipa ikonra a feladatátvételi folyamat elindításához ✓.
1. A replika virtuális gépek létrehozása után is a függőben lévő állapotba. Kattintson a **véglegesítési** véglegesíti a feladatátvételt.
1. Replikáció befejezése után a virtuális gépek indítása a másodlagos helyen.

## <a name="perform-a-failover"></a>Feladatátvétel végrehajtása
Egy nem tervezett feladatátvétel során a StorSimple-köteteket a virtuális eszközön feladatátvétel, a replika virtuális gép az Azure-ban kerül, és a virtuális géphez csatolt kötetek.

#### <a name="to-perform-a-failover"></a>A feladatátvétel végrehajtásához
1. Az Azure Portalon válassza ki a **a Recovery services** tároló &gt; **helyreállítási tervek (Site Recovery)** &gt; **recoveryplan_name** létrehozott a a fájlkiszolgáló virtuális Gépet.
1. A helyreállítási terv panelen kattintson a **további** &gt; **feladatátvételi**.  
1. Az a **feladatátvétel megerősítése** panelen válassza ki a forrás és cél helye.
1. Válassza ki **virtuális gépek leállítása és a legfrissebb adatok szinkronizálása** megadásához, hogy a Site Recovery próbálja a védett virtuális gépet leállítja, majd szinkronizálja az adatokat, így az adatok a legújabb verzióra fog a feladatátvételt.
1. A feladatátvétel után a virtuális gépek vannak függőben lévő állapotba. Kattintson a **véglegesítési** véglegesíti a feladatátvételt.


## <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása
A feladat-visszavétel során a StorSimple-kötettároló feladatátvétel történt vissza a fizikai eszköz az után egy biztonsági mentés készül.

#### <a name="to-perform-a-failback"></a>A feladat-visszavétel végrehajtásához
1. Az Azure Portalon válassza ki a **a Recovery services** tároló &gt; **helyreállítási tervek (Site Recovery)** &gt; **recoveryplan_name** létrehozott a a fájlkiszolgáló virtuális Gépet.
1. A helyreállítási terv panelen kattintson a **további** &gt; **tervezett feladatátvétel**.  
1. Válassza ki a forrás- és helyek, válassza ki a megfelelő adatszinkronizálási és a virtuális gép létrehozási lehetőségek.
1. Kattintson a **OK** gombra kattintva indítsa el a feladat-visszavételi folyamat.
   
   ![Indítsa el a feladat-visszavétel](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Ajánlott eljárások
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitás megtervezése és a készültségi értékelése
#### <a name="hyper-v-site"></a>Hyper-V-hely
Használja a [felhasználói Capacity planner eszközt](http://www.microsoft.com/download/details.aspx?id=39057) a kiszolgáló, tárolási és hálózati infrastruktúra, a Hyper-V replika környezetének megtervezéséhez.

#### <a name="azure"></a>Azure
Futtathatja a [Azure Virtual Machine Readiness Assessment eszköz](http://azure.microsoft.com/downloads/vm-readiness-assessment/) annak érdekében, hogy azok kompatibilis az Azure virtuális gépek és az Azure Site Recovery Services-beli virtuális gépeken. A Readiness Assessment eszközt ellenőrzi a Virtuálisgép-konfigurációk, és figyelmezteti, ha a konfigurációk a következők nem kompatibilis az Azure-ral. Például kapcsolatos figyelmeztetés Ha a C: meghajtó 127 GB-nál nagyobb.

Kapacitástervezés épül fel, ha legalább két fontos folyamatok:

   - Hozzárendelés a helyszíni Hyper-V virtuális gépek Azure-beli Virtuálisgép-méretek (például A6, a7-es, a8-as és a9-es).
   - A szükséges internetes sávszélesség meghatározásához.

## <a name="limitations"></a>Korlátozások
   - Jelenleg csak 1 StorSimple-eszköz feladatátvétele (az egyetlen StorSimple Cloud Appliance). Egy fájlkiszolgálón, amely több StorSimple eszközt a jelen forgatókönyv még nem támogatott.
   - Ha egy virtuális gép védelmének engedélyezése során hibaüzenetet kap, ellenőrizze, hogy le van választva az iSCSI-tárolók.
   - Egy biztonsági mentési szabályzatok kötettárolók között átfedés miatt csoportban minden kötettároló fog a feladatátvételt együtt.
   - A kiválasztott kötettároló összes kötet fogja végrehajtani a feladatátvételt.
   - Köteteket, amelyek akár több mint 64 TB-ig hozzáadása nem tudja végrehajtani a feladatátvételt, mert egyetlen StorSimple Cloud Appliance maximális kapacitása 64 TB.
   - Ha a tervezett/nem tervezett feladatátvétel sikertelen lesz, és a virtuális gépek jönnek létre az Azure-ban, majd akkor ne törölje a virtuális gépeket. Ehelyett hajtsa végre a feladat-visszavételhez. Ha törli a virtuális gépek ezután a helyszíni virtuális gépek nem kapcsolható be újra.
   - A feladatátvétel után Ha Ön nem láthatja a köteteket, nyissa meg a virtuális gépek, nyissa meg a Lemezkezelést, ellenőrizze újra a lemezeket, és majd kapcsolásuk.
   - Bizonyos esetekben a meghajtó betűjelei a DR-helyen, mint a betűk helyszíni eltérő lehet. Ha ez történik, szüksége lesz a feladatátvétel befejezése után manuálisan hárítsa el a problémát.
   - Feladatátvételi feladat időtúllépése: A StorSimple-parancsfájl időtúllépést okoz, ha a kötettároló feladatátvétele parancsfájl (jelenleg 120 perc) az Azure Site Recovery előállítónként több időt vesz igénybe.
   - Biztonsági mentési feladat időtúllépése: A StorSimple-parancsfájl időtúllépés történik, ha a kötetek biztonsági mentése szkript (jelenleg 120 perc) az Azure Site Recovery előállítónként több időt vesz igénybe.
   
   > [!IMPORTANT]
   > A biztonsági mentés manuális futtatása az Azure Portalról, és futtassa újra a helyreállítási tervben.
   
   - Klónozza a feladat időtúllépése: A StorSimple-parancsfájl időtúllépés történik, ha a kötetek klónozása időigényesebb, mint az Azure Site Recovery korlát / script (jelenleg 120 perc).
   - Szinkronizálási hiba ideje: A StorSimple-szkriptek hibák meg arról, hogy a biztonsági mentés sikertelen volt-e annak ellenére, hogy a biztonsági mentés sikeres, a portálon. Ennek egyik lehetséges oka lehet, hogy a StorSimple-készülék időt szinkronizálva az időzóna szerint az aktuális időt tartalmazó lehet.
   
   > [!IMPORTANT]
   > Az aktuális idejét az időzóna készülék idő szinkronizálása.
   
   - Készülék feladatátvételi hiba: A StorSimple-parancsfájl meghiúsulhat, ha nincs egy készülék feladatátvételt, ha a helyreállítási terv fut-e.
   
   > [!IMPORTANT]
   > Futtassa újra a helyreállítási tervet, a készülék feladatátvétel befejezése után.


## <a name="summary"></a>Összegzés
Azure Site Recovery használatával létrehozhat egy fájlkiszolgáló virtuális gép teljes automatizált Vészhelyreállítási terv a StorSimple-storage-ban üzemeltetett fájlmegosztások kellene. Bárhonnan másodpercen belül is kezdeményezhető a feladatátvétel esetén a megszakítás és letöltheti az alkalmazás néhány perc alatt ütembe helyezheti.
