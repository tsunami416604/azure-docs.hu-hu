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
    ms.date="02/22/2016" 
    ms.author="raynew"/>

#  Mi a Site Recovery?

Üdvözli Önt az Azure Site Recovery szolgáltatás! Kezdje ezzel a cikkel, hogy gyorsan áttekinthesse a Site Recovery szolgáltatás működését, illetve azt, hogyan járulhat hozzá a szolgáltatás üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiájának megvalósításához.

Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk mindkét modellre vonatkozik. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## Áttekintés

A szervezete BCDR-stratégiájának egyik fontos része, hogy tudja, hogyan kell fenntartani a vállalati munkaterheléseket és alkalmazásokat, illetve azok futtatását tervezett és nem tervezett leállások esetén.

A Site Recovery ennek a célnak a teljesülését a munkaterhelések és alkalmazások replikálásával, feladatátvételével és helyreállításával segíti, hogy elérhetők legyenek egy másodlagos helyen, ha az elsődleges hely leáll. 

## Miért előnyös a Site Recovery használata? 

A Site Recovery a következőkkel segítheti a vállalatát:

- **A BCDR-stratégia leegyszerűsítése** – A Site Recovery megkönnyíti több üzleti számítási feladat és alkalmazás replikációjának, feladatátvételének és helyreállításának egyetlen helyről történő kezelését. A Site Recovery koordinálja a replikációt és a feladatátvételt, de nem fér hozzá az alkalmazás adataihoz, és semmilyen arra vonatkozó információval nem rendelkezik.
- **Rugalmas replikáció biztosítása** – A Site Recovery segítségével replikálhatja a Hyper-V virtuális gépeken, a VMware virtuális gépeken és a Windows/Linux fizikai kiszolgálókon futó számítási feladatokat. 
- **Egyszerű feladatátvétel és helyreállítás** – A Site Recovery feladatátvételi teszteket biztosít a vészhelyreállítások részletezésének támogatásához anélkül, hogy ez hatással lenne az éles környezetekre. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel befejezése után visszaadhatja a feladatokat az elsődleges helyeknek. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását. 
- **Másodlagos adatközpont kiküszöbölése** – A replikálást egy másodlagos helyszíni helyre vagy az Azure-ba végezheti. Ha az Azure-t használj a vészhelyreállítás célhelyeként, elkerülheti a másodlagos hely fenntartásával járó költségeket és bonyodalmakat, és a replikált adatokat az Azure Storage fogja tárolni, az összes általa biztosított hibatűrés mellett.
- **Integrálás a meglévő BCDR-technológiákkal** – A Site Recovery együttműködik az alkalmazás más BCDR-funkcióival. A Site Recovery segítségével megvédheti például a vállalati számítási feladatok SQL Server háttéralkalmazását, ideértve az SQL Server AlwaysOn natív támogatását is a rendelkezésre állási csoportok feladatátvételének kezelésében. 

## Miket replikálhatok?

Itt található egy összegzés arról, hogy miket replikálhat a Site Recovery.

![Két helyszíni hely közötti replikálás](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLIKÁLÁS** | **REPLIKÁLÁS FORRÁSHELYE** | **REPLIKÁLÁS CÉLHELYE** | **CIKK**
---|---|---|---
VMware virtuális gépeken futó számítási feladatok | Helyszíni VMware-kiszolgáló | Azure Storage-tárterület | [Üzembe helyezés](site-recovery-vmware-to-azure-classic.md)
VMware virtuális gépeken futó számítási feladatok | Helyszíni VMware-kiszolgáló | Másodlagos VMware-hely | [Üzembe helyezés](site-recovery-vmware-to-vmware.md) 
Hyper-V virtuális gépeken futó számítási feladatok | Helyszíni Hyper-V gazdakiszolgáló a VMM-felhőben | Azure Storage-tárterület | [Üzembe helyezés](site-recovery-vmm-to-azure.md)
Hyper-V virtuális gépeken futó számítási feladatok | Helyszíni Hyper-V gazdakiszolgáló a VMM-felhőben | Másodlagos VMM-hely | [Üzembe helyezés](site-recovery-vmm-to-vmm.md)
Hyper-V virtuális gépeken futó számítási feladatok | Helyszíni Hyper-V gazdakiszolgáló a VMM-felhőben, SAN-tárral| VMM másodlagos hely, SAN-tárral | [Üzembe helyezés](site-recovery-vmm-san.md)
Hyper-V virtuális gépeken futó számítási feladatok | Helyszíni Hyper-V-hely (nincs VMM) | Azure Storage-tárterület | [Üzembe helyezés](site-recovery-hyper-v-site-to-azure.md)
Fizikai Windows/Linux-kiszolgálókon futó számítási feladatok | Helyszíni fizikai kiszolgáló | Azure Storage-tárterület | [Üzembe helyezés](site-recovery-vmware-to-azure-classic.md)
Fizikai Windows/Linux-kiszolgálókon futó számítási feladatok | Helyszíni fizikai kiszolgáló | Másodlagos adatközpont | [Üzembe helyezés](site-recovery-vmware-to-vmware.md) 


## Milyen számítási feladatokat tudok megvédeni?

A Site Recovery elősegíti az alkalmazásbarát BCDR-stratégia megvalósítását, így a számítási feladatok és az alkalmazások konzisztens módon futhatnak továbbra leállások esetén is. A Site Recovery a következőket biztosítja: 

- **Alkalmazáskonzisztens pillanatképek** – Egyetlen vagy N szintű alkalmazásokhoz alkalmazáskonzisztens pillanatképeket használó replikáció.
**Közel szinkron replikáció** – A replikáció gyakorisága Hyper-V esetén akár 30 másodperc is lehet, VMware esetén pedig folyamatos lehet a replikáció.
**Integráció az SQL Server AlwaysOn szolgáltatással** – A Site Recovery helyreállítási terveiben kezelheti a rendelkezésre állási csoportok feladatátvételét. 
- **Rugalmas helyreállítási tervek** – Külső parancsfájlokat, manuális műveleteket és Azure Automation-forgatókönyveket tartalmazó helyreállítási terveket hozhat létre és szabhat testre, amelyek segítségével egész alkalmazáscsoportokat állíthat helyre egyetlen kattintással.
- **Automatizálási könyvtár** – Az Azure Automation-könyvtár gazdag, éles használatra kész és alkalmazásspecifikus parancsfájlokat tartalmazó automatizálási könyvtár, amely letölthető, és beépíthető a Site Recovery szolgáltatásba. 
-**Egyszerű hálózatkezelés** – A Site Recovery és az Azure fejlett hálózatkezelési funkciói leegyszerűsítik az alkalmazáshálózati követelményeket, ideértve az IP-címek lefoglalását, a terheléselosztók konfigurálását, valamint az Azure Traffic Manager integrációját, amely hatékony hálózatváltást garantál.


## Következő lépések

- További információk: [Milyen számítási feladatokat tud védeni a Site Recovery?](site-recovery-workload.md)
- További információk a Site Recovery architektúrájáról: [Hogyan működik a Site Recovery?](site-recovery-components.md)
 



<!--HONumber=jun16_HO2-->


