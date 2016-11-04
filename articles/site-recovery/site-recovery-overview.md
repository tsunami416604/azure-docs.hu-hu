---
title: Mi a Site Recovery? | Microsoft Docs
description: Áttekintést nyújt az Azure Site Recovery szolgáltatásról, és összefoglalja az üzembehelyezési forgatókönyveket.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2016
ms.author: raynew

---
# <a name="what-is-site-recovery?"></a>Mi a Site Recovery?
Üdvözli Önt az Azure Site Recovery szolgáltatás! Ez a cikk gyors áttekintést nyújt a Site Recovery szolgáltatás működéséről, illetve arról, hogyan hasznosíthatja az üzletmentében.

Szervezetének szüksége van egy üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok biztonságban és üzemben maradni a tervezett és nem tervezett leállások során, illetve hogy miként lehet a lehető gyorsabban visszaállni a normál működésre. Az Azure Site Recovery szolgáltatása segít ezen stratégia kidolgozásában.

A Site Recovery a helyszíni fizikai kiszolgálókon és virtuális gépeken futó számítási feladatok replikálását koordinálja. Kiszolgálókat és virtuális gépeket replikálhat egy elsődleges adatközpontból a felhőbe (Azure) vagy egy másodlagos adatközpontba. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok elérhetők maradnak, és nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat.

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery az Azure Portalon
Az Azure két különböző [üzemi modellel](../resource-manager-deployment-model.md) rendelkezik az erőforrások létrehozásához és használatához: az Azure Resource Manager-modellel és a klasszikus szolgáltatáskezelési modellel. Az Azure-ban két különböző portál érhető el: a [klasszikus Azure portál](https://manage.windowsazure.com/) és az [Azure Portal](https://portal.azure.com). Az előbbi a klasszikus üzemi modellt, az utóbbi pedig a klasszikus és a Resource Manager-modellt is támogatja.

* A Site Recovery a klasszikus portálon és az Azure Portalon egyaránt elérhető.
* A klasszikus Azure portálon a Site Recovery a klasszikus szolgáltatáskezelési modellel támogatható.
* Az Azure Portalon a klasszikus modellen és a Resource Manageren alapuló üzemelő példányok is támogatottak. 

A cikk információi a klasszikus és az Azure Portalon üzemelő példányokra egyaránt vonatkoznak. Az esetleges különbségeket jelezzük.

## <a name="why-deploy-site-recovery?"></a>Miért érdemes üzembe helyezni a Site Recoveryt?
A Site Recovery a következőkkel segítheti a vállalatát:

* **A üzletmenet-folytonosság és vészhelyreállítás leegyszerűsítése** – Egyetlen helyen kezelheti több számítási feladat replikálását, feladatátvételét és helyreállítását az Azure Portalon. A Site Recovery koordinálja a replikációt és a feladatátvételt, de nem fér hozzá az alkalmazás adataihoz, és semmilyen arra vonatkozó információval nem rendelkezik.
* **Rugalmas replikáció biztosítása** – A Site Recovery segítségével replikálhatja a támogatott Hyper-V-alapú virtuális gépeken, a VMware-alapú virtuális gépeken és a Windows-/Linux-alapú fizikai kiszolgálókon futó számítási feladatokat.
* **Egyszerű replikációs teszt végrehajtása** – A Site Recovery feladatátvételi tesztekkel támogatja a vészhelyreállítási próbákat anélkül, hogy ez hatással lenne az éles környezetekre.
* **Feladatátvétel és helyreállítás** – Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel befejezése után visszaadhatja a feladatokat az elsődleges helyeknek. A Site Recovery olyan helyreállítási terveket biztosít, amelyek szkripteket és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Másodlagos adatközpont kiküszöbölése** – A számítási feladatokat egy másodlagos helyszín helyett az Azure-ba replikálhatja. Ezáltal kiküszöbölhetők egy másodlagos adatközpont fenntartásával járó bonyodalmak és költségek. A replikált adatokat az Azure Storage fogja tárolni, és az ezzel járó rugalmasságot is biztosítja. Feladatátvétel esetén a rendszer virtuális gépeket hoz létre a replikált adatokkal.
* **Integrálás a meglévő BCDR-technológiákkal** – A Site Recovery integrálható más BCDR-funkciókkal. A Site Recovery segítségével megvédheti például a vállalati számítási feladatok SQL Server-háttérrendszerét, ideértve az SQL Server AlwaysOn natív támogatását is a rendelkezésre állási csoportok feladatátvételének kezelésében.

## <a name="what-can-i-replicate?"></a>Miket replikálhatok?
Itt található egy összefoglaló arról, hogy mit replikálhat a Site Recovery használatával.

![Két helyszíni hely közötti replikálás](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLIKÁLÁS** | **REPLIKÁLÁS CÉLHELYE** |
| --- | --- |
| Helyszíni VMware virtuális gépeken futó számítási feladatok |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Másodlagos hely](site-recovery-vmware-to-vmware.md) |
| VMM-felhőkben felügyelt helyszíni Hyper-V virtuális gépeken futó számítási feladatok |[Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Másodlagos hely](site-recovery-vmm-to-vmm.md) |
| VMM-felhőkben felügyelt helyszíni Hyper-V virtuális gépeken futó számítási feladat SAN-tárral |[Másodlagos hely](site-recovery-vmm-san.md) |
| Helyszíni Hyper-V virtuális gépeken futó számítási feladatok VMM nélkül |[Azure](site-recovery-hyper-v-site-to-azure.md) |
| Helyszíni fizikai Windows-/Linux-kiszolgálókon futó számítási feladatok |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Másodlagos hely](site-recovery-vmware-to-vmware.md) |

## <a name="what-workloads-can-i-protect?"></a>Milyen számítási feladatokat tudok megvédeni?
A Site Recovery lehetővé teszi az alkalmazásbarát BCDR-stratégia megvalósítását, így a számítási feladatok és az alkalmazások konzisztens módon futhatnak tovább leállások esetén is. A Site Recovery a következőket biztosítja:

* **Alkalmazáskonzisztens pillanatképek** – A gépek alkalmazáskonzisztens pillanatképek használatával végzik a replikálást az egy- vagy többszintű alkalmazások esetében. Az alkalmazáskonzisztens pillanatképek a lemez adatai mellett a memóriában lévő összes adatot és az összes folyamatban lévő tranzakciót is rögzítik.
* **Közel szinkron replikáció** – A Site Recovery által biztosított replikáció gyakorisága Hyper-V esetén akár 30 másodperc is lehet, VMware esetén pedig folyamatos lehet a replikáció.
* **Rugalmas helyreállítási tervek** – Külső szkripteket és manuális műveleteket tartalmazó helyreállítási terveket hozhat létre és szabhat testre. Az Azure Automation-forgatókönyvekkel való integráció segítségével egész alkalmazáscsoportokat állíthat helyre egyetlen kattintással.
* **Integráció az SQL Server AlwaysOn szolgáltatással** – A Site Recovery helyreállítási terveiben kezelheti a rendelkezésre állási csoportok feladatátvételét.
* **Automatizálási könyvtár** – Az Azure Automation-könyvtár gazdag, éles használatra kész és alkalmazásspecifikus parancsfájlokat tartalmazó automatizálási könyvtár, amely letölthető, és beépíthető a Site Recovery szolgáltatásba.
* **Egyszerű hálózatkezelés** – A Site Recovery és az Azure fejlett hálózatkezelési funkciói leegyszerűsítik az alkalmazáshálózati követelményeket, ideértve az IP-címek lefoglalását, a terheléselosztók konfigurálását, valamint az Azure Traffic Manager integrációját, ami hatékony hálózatváltást garantál.

## <a name="next-steps"></a>Következő lépések
* További információk: [Milyen számítási feladatokat tud védeni a Site Recovery?](site-recovery-workload.md)
* További információk a Site Recovery architektúrájáról: [Hogyan működik a Site Recovery?](site-recovery-components.md)

<!--HONumber=Oct16_HO3-->


