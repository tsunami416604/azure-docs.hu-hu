---
title: A StorSimple fájlmegosztás dr automatizálása az Azure Site Recovery szolgáltatással | Microsoft dokumentumok
description: A Microsoft Azure StorSimple storage-ban tárolt fájlmegosztások vész-helyreállítási megoldásának létrehozásához szükséges lépések és gyakorlati tanácsok ismertetése.
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
ms.author: alkohli
ms.openlocfilehash: 650798fdb884e6494990efb533335a1dd8b4d89f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875396"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatikus vész-helyreállítási megoldás az Azure Site Recovery használatával a StorSimple-en tárolt fájlmegosztásokhoz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple egy hibrid felhőalapú tárolási megoldás, amely a fájlmegosztásokkal gyakran társított strukturálatlan adatok összetettségével foglalkozik. A StorSimple a helyszíni megoldás bővítményeként használja a felhőalapú tárhelyet, és automatikusan rétegezi az adatokat a helyszíni és a felhőbeli tárhelyközött. Az integrált adatvédelem helyi és felhőbeli pillanatképekkel kiküszöböli a burjánzó tárolási infrastruktúrát.

[Az Azure Site Recovery](../site-recovery/site-recovery-overview.md) egy Azure-alapú szolgáltatás, amely vész-helyreállítási (DR) képességek et biztosít a replikáció, a feladatátvétel és a virtuális gépek helyreállítása vezénylésével. Az Azure Site Recovery számos replikációs technológiát támogat a virtuális gépek és alkalmazások konzisztens replikálásához, védelméhez és zökkenőmentes feladatátvételéhez a magán-/nyilvános vagy üzemeltetett felhőkszámára.

Az Azure Site Recovery, a virtuális gép replikációja és a StorSimple felhőbeli pillanatkép-képességek használatával megvédheti a teljes fájlkiszolgálói környezetet. Zavar esetén egyetlen kattintással néhány perc alatt online állapotba hozhatja a fájlmegosztásokat az Azure-ban.

Ez a dokumentum részletesen ismerteti, hogyan hozhat létre vész-helyreállítási megoldást a StorSimple-tárolón tárolt fájlmegosztásokhoz, és hogyan hajthat végre tervezett, nem tervezett és tesztfeladat-átvételi feladatokat egy kattintással a helyreállítási terv használatával. Lényegében azt mutatja be, hogyan módosíthatja a helyreállítási tervet az Azure Site Recovery-tárolóban a StorSimple feladatátvételek engedélyezéséhez a katasztrófa-forgatókönyvek során. Emellett ismerteti a támogatott konfigurációk és előfeltételek. Ez a dokumentum feltételezi, hogy ismeri az Azure Site Recovery és a StorSimple architektúrák alapjait.

## <a name="supported-azure-site-recovery-deployment-options"></a>Az Azure Site Recovery támogatott telepítési lehetőségei
Az ügyfelek telepíthetik a fájlkiszolgálókat a Hyper-V vagy vMware szolgáltatáson futó fizikai kiszolgálókként vagy virtuális gépekként, majd fájlmegosztásokat hozhatnak létre a StorSimple-tárolóból kifaragott kötetekből. Az Azure Site Recovery egy másodlagos vagy az Azure-beli fizikai és virtuális központi telepítéseket is képes megvédeni. Ez a dokumentum a Hyper-V-n üzemeltetett és a StorSimple-tárolóban lévő fájlmegosztásokkal rendelkező fájlkiszolgálói virtuális gép helyreállítási helyén az Azure-ral rendelkező VÉSZ-megoldás részleteit ismerteti. Más forgatókönyvek, amelyekben a fájlkiszolgáló virtuális gép egy VMware virtuális gép vagy egy fizikai gép is valósítható meg hasonlóan.

## <a name="prerequisites"></a>Előfeltételek
Egy egykattintásos vész-helyreállítási megoldás megvalósítása, amely az Azure Site Recovery-t használja a StorSimple storage-ban tárolt fájlmegosztásokhoz, a következő előfeltételei vannak:

   - Helyszíni Windows Server 2012 R2 Fájlkiszolgáló Hyper-V vagy VMware vagy fizikai gépen üzemeltetett virtuális gép
   - Az Azure StorSimple-kezelővel regisztrált StorSimple tárolóeszköz a helyszínen
   - StorSimple cloud appliance az Azure StorSimple menedzserben létrehozott. A készülék et le lehet állítani.
   - A StorSimple tárolóeszközön konfigurált köteteken tárolt fájlmegosztások
   - Microsoft Azure-előfizetésben létrehozott [Azure Site Recovery szolgáltatástároló](../site-recovery/site-recovery-vmm-to-vmm.md)

Emellett ha az Azure a helyreállítási hely, futtassa az [Azure virtuálisgép-készenléti felmérés eszköz](https://azure.microsoft.com/downloads/vm-readiness-assessment/) a virtuális gépeken annak érdekében, hogy azok kompatibilisek legyenek az Azure virtuális gépek és az Azure Site Recovery szolgáltatások.

A késési problémák elkerülése érdekében (ami magasabb költségeket eredményezhet), győződjön meg arról, hogy a StorSimple Cloud Appliance, az automatizálási fiók és a tárfiók(ok) létrehozása ugyanabban a régióban.

## <a name="enable-dr-for-storsimple-file-shares"></a>Dr engedélyezése StorSimple fájlmegosztásokesetén
A helyszíni környezet minden egyes összetevőjét védeni kell a teljes replikáció és helyreállítás engedélyezéséhez. Ez a szakasz a következőket ismerteti:
    
   - Az Active Directory és a DNS-replikáció beállítása (nem kötelező)
   - Az Azure Site Recovery használata a fájlkiszolgáló virtuális gépének védelméhez
   - StorSimple kötetek védelmének engedélyezése
   - A hálózat konfigurálása

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Az Active Directory és a DNS-replikáció beállítása (nem kötelező)
Ha meg szeretné védeni az Active Directoryt és a DNS-t futtató gépeket, hogy azok elérhetők legyenek a VÉSZ-helyen, akkor explicit módon kell védenie őket (hogy a fájlkiszolgálók a hitelesítéssel történő feladatátvétel után elérhetők legyenek). Az ügyfél helyszíni környezetének összetettsége alapján két ajánlott lehetőség áll rendelkezésre.

#### <a name="option-1"></a>1. lehetőség
Ha az ügyfél kis számú alkalmazással rendelkezik, egyetlen tartományvezérlővel a teljes helyszíni helyhez, és a teljes helyet fel fogja adni, akkor azt javasoljuk, hogy az Azure Site Recovery replikációjával replikálja a tartományvezérlő-gépet egy másodlagos (ez a helyről a helyekre és a helyek közötti használatra egyaránt érvényes).

#### <a name="option-2"></a>2. lehetőség
Ha az ügyfél nagy számú alkalmazással rendelkezik, egy Active Directory-erdőt futtat, és egyszerre néhány alkalmazást fog felborítani, akkor azt javasoljuk, hogy egy további tartományvezérlőt kell létrehozni a VÉSZ-helyen (másodlagos helyen vagy az Azure-ban).

Tekintse meg [az automatikus VÉSZ-megoldás az Active Directory és a DNS segítségével Az Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) utasításokat, amikor egy tartományvezérlő elérhetővé a VÉSZ-helyen. A dokumentum további részében feltételezzük, hogy egy tartományvezérlő érhető el a VÉSZ-helyen.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Az Azure Site Recovery használata a fájlkiszolgáló virtuális gépének védelméhez
Ehhez a lépéshez elő kell készítenie a helyszíni fájlkiszolgálói környezetet, létre kell hoznia és elő kell készítenie egy Azure Site Recovery-tárolót, és engedélyeznie kell a virtuális gép fájlvédelmét.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>A helyszíni fájlkiszolgálói környezet előkészítése
1. Állítsa a **Felhasználói fiókok felügyelete beállítását** **Soha ne értesítse**. Erre azért van szükség, hogy az Azure automation-parancsfájlok használatával csatlakoztathassa az iSCSI-tárolókat az Azure Site Recovery feladatátvétele után.
   
   1. Nyomja le a Windows +Q billentyűt, és keresse meg a **Felhasználói környezet et**.  
   1. Válassza **a Felhasználói fiókok vezérlésének beállításainak módosítása lehetőséget.**  
   1. Húzza a sávot az aljára a **Soha ne értesítse**felé.  
   1. Kattintson **az OK gombra,** majd a rákérdezéskor válassza az **Igen** lehetőséget.  
   
      ![Felhasználói fiókok vezérlésének beállításai](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Telepítse a virtuálisgép-ügynököt az egyes fájlkiszolgálói virtuális gépekre. Erre azért van szükség, hogy az Azure automation-parancsfájlok a feladatátvételi virtuális gépeken futtatható.
   
   1. [Töltse le az ügynököt](https://aka.ms/vmagentwin) a alkalmazásba. `C:\\Users\\<username>\\Downloads`
   1. Nyissa meg a Windows PowerShellt rendszergazdai módban (Futtatás rendszergazdaként), majd írja be a következő parancsot a letöltési helyre való navigáláshoz:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > A fájlnév a verziótól függően változhat.
      
1. Kattintson a **Tovább** gombra.
1. Fogadja el a **szerződési feltételeket,** majd kattintson a **Tovább**gombra.
1. Kattintson a **Befejezés** gombra.
1. Fájlmegosztások létrehozása a StorSimple-tárolóból kifaragott kötetek használatával. További információ: [A StorSimple Manager szolgáltatás használata kötetek kezeléséhez.](storsimple-manage-volumes.md)
   
   1. A helyszíni virtuális gépeken nyomja le a Windows +Q billentyűt, és keressen **az iSCSI**-re.
   1. Válassza az **iSCSI-kezdeményező lehetőséget**.
   1. Válassza a **Konfiguráció** lapot, és másolja a kezdeményező nevét.
   1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
   1. Válassza a **StorSimple** lapot, majd válassza ki a fizikai eszközt tartalmazó StorSimple-kezelő szolgáltatást.
   1. Hozzon létre kötettároló(ka)t, majd hozzon létre kötet(eke)t. (Ezek a kötetek a fájlkiszolgáló virtuális gépein lévő fájlmegosztás(ok)ra szolgálnak. Másolja a kezdeményező nevét, és adja meg a hozzáférés-vezérlési rekordok megfelelő nevét a kötetek létrehozásakor.
   1. Válassza a **Beállítás** lapot, és jegyezze fel az eszköz IP-címét.
   1. A helyszíni virtuális gépeken nyissa meg újra az **iSCSI-kezdeményezőt,** és írja be az IP-címet a Gyorscsatlakozás szakaszban. Kattintson a **Gyorscsatlakozás gombra** (az eszközt most csatlakoztatni kell).
   1. Nyissa meg az Azure **portalt,** és **Auto Configure**válassza a Kötetek és eszközök lapot. A létrehozott kötetnek meg kell jelennie.
   1. A portálon válassza az **Eszközök** fület, majd az **Új virtuális eszköz létrehozása lehetőséget.** (Ez a virtuális eszköz lesz használva, ha feladatátvétel történik). Ez az új virtuális eszköz offline állapotban tartható a többletköltségek elkerülése érdekében. A virtuális eszköz offline állapotba hozásához nyissa meg a **Virtuális gépek** szakaszt a portálon, és állítsa le.
   1. Lépjen vissza a helyszíni virtuális gépekre, és nyissa meg a Lemezkezelés segédprogramot (nyomja le a Windows + X billentyűkombinációt, és válassza a **Lemezkezelés**lehetőséget ).
   1. Észre fog venni néhány extra lemezt (a létrehozott kötetek számától függően). Kattintson a jobb gombbal az elsőre, válassza a **Lemez inicializálása**parancsot, majd az **OK**parancsot. Kattintson a jobb gombbal a **Nem lefoglalt** szakaszra, válassza az Új egyszerű **kötet parancsot,** rendeljen hozzá egy meghajtóbetűjelet, és fejezze be a varázslót.
   1. Ismételje meg az l lépést az összes lemezesetében. Most már láthatja az **összes** lemezt ezen a számítógépen a Windows Intézőben.
   1. A Fájl- és tárolási szolgáltatások szerepkör segítségével fájlmegosztásokat hozhat létre ezeken a köteteken.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Azure Site Recovery-tároló létrehozása és előkészítése
Tekintse meg az [Azure Site Recovery dokumentációt](../site-recovery/site-recovery-hyper-v-site-to-azure.md) az Azure Site Recovery használatával való ismerkedés a fájlkiszolgáló virtuális gépének védelme előtt.

#### <a name="to-enable-protection"></a>A védelem engedélyezése
1. Válassza le az iSCSI-tároló(ka)t az Azure Site Recovery szolgáltatással védeni kívánt helyszíni virtuális gépekről:
   
   1. Nyomja le a Windows + Q billentyűt, és keressen **iSCSI**.
   1. Válassza **az iSCSI-kezdeményező beállítása**lehetőséget.
   1. Válassza le a korábban csatlakoztatott StorSimple-eszközt. Azt is megteheti, hogy néhány percre kikapcsolja a fájlkiszolgálót, amikor engedélyezi a védelmet.
      
   > [!NOTE]
   > Ez azt eredményezi, hogy a fájlmegosztások átmenetileg nem lesznek elérhetők.
   
1. [Engedélyezze](../site-recovery/site-recovery-hyper-v-site-to-azure.md) a fájlkiszolgáló virtuális gépének virtuális gépének virtuális gép védelmét az Azure Site Recovery portálról.
1. Amikor a kezdeti szinkronizálás megkezdődik, újra csatlakoztathatja a célt. Nyissa meg az iSCSI-kezdeményezőt, jelölje ki a StorSimple eszközt, és kattintson a **Csatlakozás gombra.**
1. Amikor a szinkronizálás befejeződött, és a virtuális gép állapota **védett,** válassza a virtuális gép, válassza a **Konfigurálás** lapot, és ennek megfelelően frissítse a virtuális gép hálózatát (ez az a hálózat, amelynek a virtuális gép(ek) a feladatátvételi rendszer része lesz). Ha a hálózat nem jelenik meg, az azt jelenti, hogy a szinkronizálás továbbra is folyamatban van.

### <a name="enable-protection-of-storsimple-volumes"></a>StorSimple kötetek védelmének engedélyezése
Ha nem választotta ki a Kötet **alapértelmezett biztonsági mentésének engedélyezése** beállítást a StorSimple kötetekhez, nyissa meg a Biztonsági **mentési házirendeket** a StorSimple Manager szolgáltatásban, és hozzon létre egy megfelelő biztonsági mentési házirendet az összes kötethez. Azt javasoljuk, hogy állítsa be a biztonsági mentések gyakoriságát a helyreállítási pont cél (RPO), amely az alkalmazás hoz andó.

### <a name="configure-the-network"></a>A hálózat konfigurálása
A fájlkiszolgáló virtuális gép, konfigurálja a hálózati beállításokat az Azure Site Recovery úgy, hogy a virtuálisgép-hálózatok csatlakoznak a megfelelő DR-hálózat feladatátvétel után.

A virtuális gép kiválasztásával a **Replikált elemek** lapon konfigurálhatja a hálózati beállításokat, ahogy az az alábbi ábrán látható.

![Számítás és hálózat](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
Az asr-ben helyreállítási tervet hozhat létre a fájlmegosztások feladatátvételi folyamatának automatizálásához. Ha zavar lép fel, a fájlmegosztásokat néhány perc alatt egyetlen kattintással felhozhatja. Az automatizálás engedélyezéséhez szüksége lesz egy Azure-automatizálási fiókra.

#### <a name="to-create-an-automation-account"></a>Automatizálási fiók létrehozása
1. Nyissa meg az &gt; Azure Portal **Automation** szakaszt.
1. Kattintson **a + Hozzáadás** gombra, megnyílik a penge alatt.
   
   ![Automation-fiók hozzáadása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Név - Új automatizálási fiók megadása
   - Előfizetés – Előfizetés kiválasztása
   - Erőforráscsoport – Új erőforráscsoport létrehozása/meglévő erőforráscsoport kiválasztása
   - Hely – Válassza ki a helyet, tartsa ugyanabban a földrajzi/régióban, amelyben a StorSimple cloud appliance és a storage fiókok jöttek létre.
   - Hozzon létre Azure Run As fiókként – Válassza az **Igen** lehetőséget.
   
1. Nyissa meg az Automation-fiókot, és kattintson a **Runbooks** &gt; **Tallózás itára** elemre az összes szükséges runbook importálásához az automatizálási fiókba.
1. Adja hozzá a következő runbookokat a **vész-helyreállítási** címke megkeresésével a galériában:
   
   - StorSimple-kötetek karbantartása a tesztfeladat-átvétel (TFO) után
   - Feladatátvételst tartalmazó StorSimple kötettárolók
   - Kötetek csatlakoztatása StorSimple-eszközön feladatátvétel után
   - Egyéni parancsfájl-bővítmény eltávolítása az Azure VM-ben
   - StorSimple virtuális készülék indítása
   
      ![Galéria tallózása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Tegye közzé az összes parancsfájlt úgy, hogy kiválasztja a runbookot az automation-fiókban, majd kattintson a **Közzététel** **szerkesztése,** &gt; majd az **Igen** gombra az ellenőrző üzenetre. A lépés után a **Runbooks** lap a következőképpen jelenik meg:
   
   ![Runbookok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Az automatizálási fiókban kattintson a **Változók** &gt; **hozzáadása változóra,** és adja hozzá a következő változókat. Dönthet úgy, hogy titkosítja ezeket az eszközöket. Ezek a változók a helyreállítási tervre jellemzőek. Ha a helyreállítási terv, amely a következő lépésben létrehoz, neve TestPlan, majd a változók kell TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName, és így tovább.

   - **BaseUrl**: Az Azure-felhő erőforrás-kezelő url-címe. Get-AzEnvironment használata **| Select-Object Name, ResourceManagerUrl** parancsmag.
   - _RecoveryPlanName_**-ResourceGroupName**: A StorSimple erőforrást tartalmazó Erőforrás-kezelő csoport.
   - _RecoveryPlanName_**-ManagerName**: A StorSimple eszközzel rendelkező StorSimple erőforrás.
   - _RecoveryPlanName_**-DeviceName**: A storSimple eszköz, amelyet át kell venni.
   - _RecoveryPlanName_**-DeviceIpAddress**: Az eszköz IP-címe (ez a StorSimple Device &gt; Manager **szakasz** &gt; Beállítások **hálózati** &gt; **DNS-beállítások** csoportjának **Eszközök** lapján található).
   - _RecoveryPlanName_**-VolumeContainers**: Az eszközön található, vesszővel tagolt kötettárolók sorozata, amelyet át kell venni; például: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: Az a StorSimple felhőkezelő berendezés, amelyen a tárolókat feladatát kell venni.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: A céleszköz IP-címe (ez a &gt; **Virtuálisgép** csoport **Beállítások** &gt; **csoportjának hálózata** lapon található).
   - _RecoveryPlanName_**-StorageAccountName**: A tárfiók neve, amelyben a parancsfájl (amelynek futtatnia kell a feladatátvevő virtuális gép) lesz tárolva. Ez lehet bármilyen tárfiók, amely rendelkezik némi helyet a parancsfájl ideiglenes tárolására.
   - _RecoveryPlanName_**-StorageAccountKey**: A fenti tárfiók hozzáférési kulcsa.
   - _RecoveryPlanName_**-VMGUIDS**: Virtuális gép védelme esetén az Azure Site Recovery minden virtuális géphez egyedi azonosítót rendel, amely megadja a feladatátvételi virtuális gép részleteit. A VMGUID beszerzéséhez jelölje ki a **Helyreállítási szolgáltatások** lapot, és kattintson a **Védett elemvédelmi** &gt; **csoportok** &gt; **géptulajdonságai** &gt; **gombra.** Ha több virtuális gép, majd adja hozzá a GUID-ok, mint egy vesszővel tagolt karakterlánc.

     Ha például a helyreállítási terv neve fileServerpredayRP, akkor a **Változók**, **Kapcsolatok** és **tanúsítványok** lap az összes eszköz hozzáadása után a következőképpen jelenik meg.

      ![Objektumok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Töltsön fel StorSimple 8000 sorozatú Runbook modult az Automation-fiókba. Modul hozzáadásához használja az alábbi lépéseket:
   
   1. Nyissa meg a powershellt, hozzon létre egy új mappát, & módosítsa a könyvtárat a mappára.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Töltse le a nuget CLI-t ugyanabban a mappában az 1.
      A nuget.exe különböző verziói elérhetők a [nuget letöltéseken.](https://www.nuget.org/downloads) Minden letöltési hivatkozás közvetlenül egy .exe fájlra mutat, ezért a jobb gombbal kattintson a jobb gombbal, és mentse a fájlt a számítógépre, és ne futtassa a böngészőből.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. A függő SDK letöltése
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Hozzon létre egy Azure Automation Runbook modult a StorSimple 8000 sorozatú eszközkezeléshez. Az alábbi parancsokkal hozzon létre egy Automation modul zip fájlt.
         
      ```powershell
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
         
   1. Importálja a fenti lépésben létrehozott Azure Automation modul zip-fájlját (Microsoft.Azure.Management.StorSimple8000Series.zip). Ez az Automatizálási fiók kiválasztásával, a MEGOSZTOTT ERŐFORRÁSOK **csoportban** a Modulok elemre, majd **a Modul hozzáadása**gombra kattintva végezhető el.
   
   A StorSimple 8000 sorozat modul importálása után a **Modulok** lapnak a következőképpen kell megjelennie:
   
      ![Modulok](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Nyissa meg a **Helyreállítási szolgáltatások** szakaszt, és válassza ki a korábban létrehozott Azure Site Recovery-tárolót.
1. Válassza a **Helyreállítási tervek (Webhely-helyreállítási)** lehetőséget a **Kezelés** csoportban, és hozzon létre egy új helyreállítási tervet az alábbiak szerint:
   
   - Kattintson **a + Recover terv** gombra, megnyílik alatt panel.
      
      ![Helyreállítási terv létrehozása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Adja meg a helyreállítási terv nevét, válassza a Forrás, a Cél & Telepítési modell értékeit.
   
   - Válassza ki a helyreállítási tervbe felvenni kívánt védelmi csoportból származó virtuális gépeket, és kattintson az **OK** gombra.
   
   - Válassza a korábban létrehozott Helyreállítási terv lehetőséget, kattintson a **Testreszabás** gombra a Helyreállítási terv testreszabási nézetének megnyitásához.
   
   - Kattintson a jobb gombbal az **Összes csoport leállítására,** majd kattintson **az Előművelet hozzáadása parancsra.**
   
   - Megnyitja a Beszúrás műveletpanelt, adjon meg egy nevet, válassza **a** Hol való futtatás lehetőséget, válassza az Automation-fiókot (amelyben a runbookokat adta hozzá), majd válassza a **Feladatátvétel-StorSimple-Volume-Containers** runbook lehetőséget.
   
   - Kattintson a jobb gombbal az **1-es csoportra: Indítsa el** és kattintson a **Védett elemek hozzáadása** lehetőségre, majd válassza ki a védeni kívánt virtuális gépeket a helyreállítási tervben, és kattintson az **Ok** gombra. Nem kötelező, ha már ki van jelölve a virtuális gépek.
   
   - Kattintson a jobb gombbal az **1-es csoportra: Indítsa el** és kattintson a **Művelet közzélépések** lehetőségére, majd adja hozzá az alábbi parancsfájlokat:  
      
      - Start-StorSimple-Virtual-Appliance runbook  
      - Feladatátvételst-over-StorSimple-volume-containers runbook  
      - Kötetek csatlakoztatása feladatátvétel után runbook  
      - Eltávolítás-egyéni-script-bővítmény runbook  
        
   - Manuális művelet hozzáadása a fenti 4 parancsfájl után ugyanabban a **1.** Ez a művelet az a pont, ahol ellenőrizheti, hogy minden megfelelően működik-e. Ezt a műveletet csak a teszt feladatátvétel részeként kell hozzáadni (ezért csak a **Teszt feladatátvétel** jelölőnégyzetét jelölje be).
    
   - A manuális művelet után adja hozzá a **Cleanup** parancsfájlt a többi runbookhoz használt eljárással. **Mentse** a helyreállítási tervet.
    
   > [!NOTE]
   > A teszt feladatátvétel futtatásakor ellenőriznie kell mindent a manuális művelet lépés, mert a StorSimple kötetek, amelyek klónozott a céleszközön törlődik részeként a karbantartás után a manuális művelet befejeződött.
       
      ![Helyreállítási terv](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Teszt feladatátvétel végrehajtása
A teszt feladatátvétel során az [Active Directory DR-megoldástárs-útmutatójában](../site-recovery/site-recovery-active-directory.md) tájékozódhat. A helyszíni beállítás egyáltalán nem zavarja, ha a teszt feladatátvétel történik. A helyszíni virtuális géphez csatlakoztatott StorSimple-kötetek az Azure-beli StorSimple felhőalapú berendezéshez vannak klónozva. A virtuális gép tesztelési célokra az Azure-ban, és a klónozott kötetek kapcsolódnak a virtuális géphez.

#### <a name="to-perform-the-test-failover"></a>A teszt feladatátvételének végrehajtása
1. Az Azure Portalon válassza ki a site recovery-tároló.
1. Kattintson a fájlkiszolgáló virtuális gépéhez létrehozott helyreállítási tervre.
1. Kattintson **a Feladatátvétel tesztelése gombra.**
1. Válassza ki azt az Azure virtuális hálózatot, amelyhez az Azure-beli virtuális gépek a feladatátvétel után csatlakoznak.
   
   ![Feladatátvétel indítása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Nyomon követheti a folyamatot, ha a virtuális gépre kattint a tulajdonságaimegnyitásához, vagy a **Feladatátvételi feladat tesztje** a vault &gt; ban **Jobs** &gt; Site Recovery **feladatok.**
1. A feladatátvétel befejezése után is látnia kell a replika &gt; Azure-gép jelenik meg az Azure portal **virtuális gépek.** Elvégezheti az érvényesítést.
1. Az ellenőrzések befejezése után kattintson az **Érvényesítés befejezése gombra.** Ez eltávolítja a StorSimple kötetek és állítsa le a StorSimple cloud appliance.
1. Ha elkészült, kattintson **a Karbantartás teszt feladatátvétel** a helyreállítási terv. A Jegyzetek területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ezzel törli a tesztfeladat-átvétel során létrehozott virtuális gépet.

## <a name="perform-a-planned-failover"></a>Tervezett feladatátvétel végrehajtása
   A tervezett feladatátvétel során a helyszíni fájlkiszolgáló virtuális gép e-mail ben leáll, és a StorSimple-eszközön lévő kötetek felhőbeli biztonsági mentési pillanatképe készül. A StorSimple-kötetek feladatátvételt a virtuális eszköz, egy replika virtuális gép az Azure-ban, és a kötetek a virtuális géphez vannak csatolva.

#### <a name="to-perform-a-planned-failover"></a>Tervezett feladatátvétel végrehajtása
1. Az Azure Portalon válassza &gt; a Helyreállítási **szolgáltatások** tároló helyreállítási tervek **(Site Recovery)** &gt; **recoveryplan_name** létre a fájlkiszolgáló virtuális gép.
1. A Helyreállítási terv panelen kattintson a **További** &gt; **tervezett feladatátvétel gombra.**  

   ![Helyreállítási terv](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. A **Tervezett feladatátvétel megerősítése** panelen válassza ki a forrás- és célhelyeket, válassza ki a célhálózatot, és kattintson az ellenőrzés ikonra ✓ a feladatátvételi folyamat elindításához.
1. A replika virtuális gépei létrehozása után véglegesítésfüggő állapotban vannak. A feladatátvétel véglegesítéséhez kattintson a **Véglegesítés** gombra.
1. A replikáció befejezése után a virtuális gépek elindulnak a másodlagos helyen.

## <a name="perform-a-failover"></a>Feladatátvétel végrehajtása
Egy nem tervezett feladatátvétel során a StorSimple-kötetek feladatátvételt kap a virtuális eszközre, egy replika virtuális gép lesz az Azure-ban, és a kötetek a virtuális géphez vannak csatolva.

#### <a name="to-perform-a-failover"></a>Feladatátvétel végrehajtása
1. Az Azure Portalon válassza &gt; a Helyreállítási **szolgáltatások** tároló helyreállítási tervek **(Site Recovery)** &gt; **recoveryplan_name** létre a fájlkiszolgáló virtuális gép.
1. A Helyreállítási terv panelen kattintson a **További** &gt; **feladatátvétel gombra.**  
1. A **Feladatátvétel megerősítése** panelen válassza ki a forrás- és célhelyeket.
1. Válassza **a Virtuális gépek leállítása lehetőséget, és szinkronizálja a legújabb adatokat** annak megadásához, hogy a Site Recovery megpróbálja leállítani a védett virtuális gépet, és szinkronizálja az adatokat, hogy az adatok legújabb verziója legyen a feladatátvétel.
1. A feladatátvétel után a virtuális gépek véglegesítésfüggő állapotban vannak. A feladatátvétel véglegesítéséhez kattintson a **Véglegesítés** gombra.


## <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása
A feladat-visszavétel során a StorSimple kötettárolók feladatátvételt a fizikai eszközre, miután egy biztonsági mentést.

#### <a name="to-perform-a-failback"></a>Feladat-visszavétel végrehajtása
1. Az Azure Portalon válassza &gt; a Helyreállítási **szolgáltatások** tároló helyreállítási tervek **(Site Recovery)** &gt; **recoveryplan_name** létre a fájlkiszolgáló virtuális gép.
1. A Helyreállítási terv panelen kattintson a **További** &gt; **tervezett feladatátvétel gombra.**  
1. Válassza ki a forrás- és célhelyeket, válassza ki a megfelelő adatszinkronizálási és virtuális gép-létrehozási beállításokat.
1. A feladat-visszavételi folyamat elindításához kattintson az **OK** gombra.
   
   ![Feladat-visszavétel indítása](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Ajánlott eljárások
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitástervezés és készenléti értékelés
#### <a name="hyper-v-site"></a>Hyper-V-hely
A [Felhasználói kapacitástervező eszközzel](https://www.microsoft.com/download/details.aspx?id=39057) megtervezheti a kiszolgálót, a tárolót és a hálózati infrastruktúrát a Hyper-V replikakörnyezethez.

#### <a name="azure"></a>Azure
Az Azure [virtuálisgép-készenléti felmérés eszköz](https://azure.microsoft.com/downloads/vm-readiness-assessment/) virtuális gépeken futtatható annak érdekében, hogy azok kompatibilisek legyenek az Azure virtuális gépekkel és az Azure Site Recovery Services szolgáltatással. A készenléti értékelési eszköz ellenőrzi a virtuális gép konfigurációit, és figyelmeztet, ha a konfigurációk nem kompatibilisek az Azure-ral. Figyelmeztetést ad ki például, ha a C: meghajtó 127 GB-nál nagyobb.

A kapacitástervezés legalább két fontos folyamatból áll:

   - Helyszíni Hyper-V virtuális gépek leképezése az Azure virtuális gép méretei (például A6, A7, A8 és A9) azure-beli virtuális gép méreteihez.
   - A szükséges internetes sávszélesség meghatározása.

## <a name="limitations"></a>Korlátozások
- Jelenleg csak 1 StorSimple eszköz lehet feladatátvétel (egyetlen StorSimple cloud appliance). A forgatókönyv egy fájlkiszolgáló, amely több StorSimple eszközök még nem támogatott.
- Ha hibaüzenetet kap, miközben engedélyezi a virtuális gép védelmét, győződjön meg arról, hogy leválasztotta az iSCSI-tárolókat.
- A kötettárolók között átnyúló biztonsági mentési házirendek miatt csoportosított kötettárolók at a rendszer együtt veszi át a feladatát.
- A kiválasztott kötettárolókban lévő kötetek mindegyike feladatátvételt kap.
- A 64 TB-nál több kötetet nem lehet átvenni, mert egyetlen StorSimple cloud appliance maximális kapacitása 64 TB.
- Ha a tervezett/nem tervezett feladatátvétel sikertelen, és a virtuális gépek jönnek létre az Azure-ban, majd ne törölje a virtuális gépek. Ehelyett végezze el a feladat-visszavételt. Ha törli a virtuális gépeket, majd a helyszíni virtuális gépek nem kapcsolhatók be újra.
- Feladatátvétel után, ha nem látja a köteteket, nyissa meg a virtuális gépeket, nyissa meg a Lemezkezelés, vizsgálja át a lemezeket, majd hozza őket online állapotba.
- Bizonyos esetekben a DR-helyen lévő meghajtóbetűjelek eltérhetnek a helyszíni betűktől. Ebben az esetben a feladatátvétel befejezése után manuálisan kell kijavítania a problémát.
- Feladatátvételi feladat időtúllépése: A StorSimple-parancsfájl időtúllépés, ha a kötettárolók feladatátvétele több időt vesz igénybe, mint az Azure Site Recovery korlát parancsfájlonként (jelenleg 120 perc).
- Biztonsági mentési feladat időtúllépése: A StorSimple parancsfájl időtúllépés, ha a kötetek biztonsági mentése több időt vesz igénybe, mint az Azure Site Recovery korlát parancsfájlonként (jelenleg 120 perc).
   
  > [!IMPORTANT]
  > Futtassa a biztonsági mentést manuálisan az Azure Portalról, majd futtassa újra a helyreállítási tervet.
   
- Klónozási feladat időtúllépése: A StorSimple parancsfájl időtúllépés, ha a kötetek klónozása több időt vesz igénybe, mint az Azure Site Recovery korlát parancsfájlonként (jelenleg 120 perc).
- Időszinkronizálási hiba: A StorSimple parancsfájlok hibákat, mondván, hogy a biztonsági mentések sikertelenek voltak annak ellenére, hogy a biztonsági mentés sikeres a portálon. Ennek egyik lehetséges oka lehet, hogy a StorSimple készülék ideje nem lehet szinkronban az aktuális idő az időzónában.
   
  > [!IMPORTANT]
  > Szinkronizálja a készülék idejét az időzónában lévő aktuális idővel.
   
- A feladatátvételi hiba: A StorSimple parancsfájl sikertelen lehet, ha a helyreállítási terv futása kor egy berendezés feladatátvétele sikertelen lesz.
   
  > [!IMPORTANT]
  > Futtassa újra a helyreállítási tervet a feladatátvétel befejezése után.


## <a name="summary"></a>Összefoglalás
Az Azure Site Recovery használatával teljes automatikus vész-helyreállítási tervet hozhat létre a StorSimple-tárolón tárolt fájlmegosztásokkal rendelkező fájlkiszolgálói virtuális géphez. A feladatátvételt másodperceken belül kezdeményezheti bárhonnan, ha megszakad a megszakítás, és néhány perc alatt működésbe hozhatja az alkalmazást.
