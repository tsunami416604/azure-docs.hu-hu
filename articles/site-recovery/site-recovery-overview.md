<properties
    pageTitle="Mi a Site Recovery? | Microsoft Azure" 
    description="Áttekintést nyújt az Azure Site Recovery szolgáltatásról, és ismerteti a szolgáltatás üzembe helyezését." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>


#  Mi a Site Recovery?

Üdvözli Önt az Azure Site Recovery szolgáltatás! Kezdje ezzel a cikkel, hogy gyorsan áttekinthesse a Site Recovery szolgáltatás működését, illetve azt, hogyan járulhat hozzá a szolgáltatás üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiájának megvalósításához.

## Áttekintés

A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. Fontos, hogy a BCDR stratégia gondoskodjon a vállalati adatok biztonságáról és helyreállíthatóságáról, és lehetővé tegye, hogy a számítási feladatok még vészhelyzet esetén se álljanak le. 

Az Azure Site Recovery szolgáltatása a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálásával segít a stratégia kidolgozásában. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. További információ: [Mi a Site Recovery?](site-recovery-overview.md)

## Site Recovery az Azure Portalon

Az Azure két különböző [üzemi modellel](../resource-manager-deployment-model.md) rendelkezik az erőforrások létrehozásához és használatához: az Azure Resource Manager-modellel és a klasszikus szolgáltatáskezelési modellel. Az Azure-ban két különböző portál érhető el: a [klasszikus Azure portál](https://manage.windowsazure.com/), amely a klasszikus üzembe helyezési modellt, és az [Azure Portal](https://portal.azure.com), amely mindkét modellt támogatja.

A Site Recovery a klasszikus portálon és az Azure Portalon egyaránt elérhető. A klasszikus Azure portálon a Site Recovery a klasszikus szolgáltatáskezelési modellel támogatható. Az Azure Portalon a klasszikus modellen és a Resource Manageren alapuló üzemelő példányok is támogatottak. [További információ](site-recovery-overview.md#site-recovery-in-the-azure-portal) az Azure Portalon történő üzembe helyezésről.

A cikk információi a klasszikus és az Azure Portalon üzemelő példányokra egyaránt vonatkoznak. Az esetleges különbségeket jelezzük.


## Miért előnyös a Site Recovery használata? 

A Site Recovery a következőkkel segítheti a vállalatát:

- **A BCDR-stratégia leegyszerűsítése** – A Site Recovery megkönnyíti több üzleti számítási feladat és alkalmazás replikációjának, feladatátvételének és helyreállításának egyetlen helyről történő kezelését. A Site Recovery koordinálja a replikációt és a feladatátvételt, de nem fér hozzá az alkalmazás adataihoz, és semmilyen arra vonatkozó információval nem rendelkezik.
- **Rugalmas replikáció biztosítása** – A Site Recovery segítségével replikálhatja a Hyper-V virtuális gépeken, a VMware virtuális gépeken és a Windows/Linux fizikai kiszolgálókon futó számítási feladatokat. 
- **Egyszerű feladatátvétel és helyreállítás** – A Site Recovery feladatátvételi teszteket biztosít a vészhelyreállítások részletezésének támogatásához anélkül, hogy ez hatással lenne az éles környezetekre. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel befejezése után visszaadhatja a feladatokat az elsődleges helyeknek. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását. 
- **Másodlagos adatközpont kiküszöbölése** – A replikálást egy másodlagos helyszíni helyre vagy az Azure-ba végezheti. Ha az Azure-t használj a vészhelyreállítás célhelyeként, elkerülheti a másodlagos hely fenntartásával járó költségeket és bonyodalmakat, és a replikált adatokat az Azure Storage fogja tárolni, az összes általa biztosított hibatűrés mellett.
- **Integrálás a meglévő BCDR-technológiákkal** – A Site Recovery együttműködik az alkalmazás más BCDR-funkcióival. A Site Recovery segítségével megvédheti például a vállalati számítási feladatok SQL Server háttéralkalmazását, ideértve az SQL Server AlwaysOn natív támogatását is a rendelkezésre állási csoportok feladatátvételének kezelésében. 

## Miket replikálhatok?

Itt található egy összefoglaló arról, hogy mit replikálhat a Site Recovery használatával.

![Két helyszíni hely közötti replikálás](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLIKÁLÁS** | **REPLIKÁLÁS FORRÁSA (HELYSZÍNI)** | **REPLIKÁLÁS CÉLHELYE** | **CIKK**
---|---|---|---
VMware virtuális gépek | VMware-kiszolgáló | Azure | [Részletek](site-recovery-vmware-to-azure-classic.md)
VMware virtuális gépek | VMware-kiszolgáló | Másodlagos VMware-hely | [Részletek](site-recovery-vmware-to-vmware.md) 
Hyper-V virtuális gépek | Hyper-V-gazdagépek a VMM-felhőben | Azure | [Részletek](site-recovery-vmm-to-azure.md) 
Hyper-V virtuális gépek | Hyper-V-gazdagépek a VMM-felhőben | Másodlagos VMM-hely | [Részletek](site-recovery-vmm-to-vmm.md)
Hyper-V virtuális gépek | Hyper-V-gazdagépek a VMM-felhőben, SAN-tárakkal| VMM másodlagos hely, SAN-tárral | [Részletek](site-recovery-vmm-san.md)
Hyper-V virtuális gépek | Hyper-V-gazdagép (VMM nélkül) | Azure | [Részletek](site-recovery-hyper-v-site-to-azure.md)
Fizikai Windows-/Linux-kiszolgálók | Fizikai kiszolgáló | Azure | [Részletek](site-recovery-vmware-to-azure-classic.md)
Fizikai Windows/Linux-kiszolgálókon futó számítási feladatok | Fizikai kiszolgáló | Másodlagos adatközpont | [Részletek](site-recovery-vmware-to-vmware.md) 


## Milyen számítási feladatokat tudok megvédeni?

A Site Recovery elősegíti az alkalmazásbarát BCDR-stratégia megvalósítását, így a számítási feladatok és az alkalmazások konzisztens módon futhatnak továbbra leállások esetén is. A Site Recovery a következőket biztosítja: 

- **Alkalmazáskonzisztens pillanatképek** – Egyetlen vagy N szintű alkalmazásokhoz alkalmazáskonzisztens pillanatképeket használó replikáció.
- **Közel szinkron replikáció** – A replikáció gyakorisága Hyper-V esetén akár 30 másodperc is lehet, VMware esetén pedig folyamatos lehet a replikáció.
- **Integráció az SQL Server AlwaysOn szolgáltatással** – A Site Recovery helyreállítási terveiben kezelheti a rendelkezésre állási csoportok feladatátvételét. 
- **Rugalmas helyreállítási tervek** – Külső parancsfájlokat, manuális műveleteket és Azure Automation-forgatókönyveket tartalmazó helyreállítási terveket hozhat létre és szabhat testre, amelyek segítségével egész alkalmazáscsoportokat állíthat helyre egyetlen kattintással.
- **Automatizálási könyvtár** – Az Azure Automation-könyvtár gazdag, éles használatra kész és alkalmazásspecifikus parancsfájlokat tartalmazó automatizálási könyvtár, amely letölthető, és beépíthető a Site Recovery szolgáltatásba.
- **Egyszerű hálózatkezelés** – A Site Recovery és az Azure fejlett hálózatkezelési funkciói leegyszerűsítik az alkalmazáshálózati követelményeket, ideértve az IP-címek lefoglalását, a terheléselosztók konfigurálását, valamint az Azure Traffic Manager integrációját, amely hatékony hálózatváltást garantál.


## Következő lépések

- További információk: [Milyen számítási feladatokat tud védeni a Site Recovery?](site-recovery-workload.md)
- További információk a Site Recovery architektúrájáról: [Hogyan működik a Site Recovery?](site-recovery-components.md)
 



<!--HONumber=Oct16_HO1-->


