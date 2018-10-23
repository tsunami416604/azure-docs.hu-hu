---
title: Által támogatott Linux-disztribúciók |} A Microsoft Docs
description: További információ a Linux az Azure által támogatott disztribúciók, például a Ubuntu, a CentOS, az Oracle és a SUSE vonatkozó irányelveket.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: szark
ms.openlocfilehash: 80a165c4533f555dce2d86f044f3caa328761123
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645747"
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Linux az Azure által támogatott disztribúciók
Partnerek az Azure Marketplace-en elérhető Linux-lemezképekhez adja meg. Jelenleg is dolgozunk, a különböző Linux közösségekkel, hogy még tovább bővíthesse a támogatott terjesztési listára. Addig is, amelyek nem érhetők el a piactérről disztribúciók, akkor is mindig saját Linux használata az alábbi az irányelveket, [létrehozása és feltöltése a Linux operációs rendszert tartalmazó virtuális merevlemez](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Támogatott disztribúciók és verziók
Az alábbi táblázat a Linux-disztribúciók és verziók által támogatott Azure-ban. Tekintse meg [támogatja a Microsoft Azure-ban a Linux-lemezképekhez](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) további részletes információ a Linux- és az Azure-ban nyílt forráskódú technológia támogatása.

A Linux Integration Services (LIS) illesztőprogramokat, a Hyper-V és az Azure olyan kernel modulok, amelyek a Microsoft közvetlenül hozzájárul a felsőbb szintű Linux-kernelt.  Alapértelmezés szerint a terjesztési kernel bizonyos LIS illesztőprogramok beépített. A Red Hat Enterprise (RHEL) rendszerű régebbi disztribúciók / CentOS, külön letölthető [Linux Integration Services verzió 4.2 a Hyper-V és az Azure](https://www.microsoft.com/en-us/download/details.aspx?id=55106). Lásd: [Linux-kernel követelmények](create-upload-generic.md#linux-kernel-requirements) LIS illesztőprogramokat további információt.

Az Azure Linux-ügynök már előre telepítve van az Azure Marketplace-rendszerképek, és a terjesztési csomag adattárból általában érhető el. Forráskód találhatók [GitHub](https://github.com/azure/walinuxagent).

  
| Disztribúció | Verzió | Illesztőprogramok | Ügynök |
| --- | --- | --- | --- |
| CentOS |A centOS 6.3 + 7.0 + |A centOS 6.3: [LIS letöltése](https://www.microsoft.com/en-us/download/details.aspx?id=55106)<p>CentOS 6.4 +: A kernel |Csomag: A [tárház](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) "WALinuxAgent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |A kernel |Forráskód: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 + |A kernel |Csomag: a "waagent" adattárban lévő <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |A kernel |Csomag: az adattár "WALinuxAgent" alatt <br/>Forráskód: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 + 7.1 + |A kernel |Csomag: az adattár "WALinuxAgent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES for SAP<br>11 SP4<br>12 SP1 +<br>15|A kernel |Csomag:<p> 11 [felhő: eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tárház<br>a 12 tartalmazza a "python-azure-ügynök" a "Nyilvános Felhőről" modul<br/>Forráskód: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE azt 42.2 + |A kernel |Csomag: A [felhő: eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tárház "python-azure-ügynök" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |A kernel |Csomag: az adattár "walinuxagent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  Ubuntu 12.04 támogatása az Azure-ban tekintse meg a [EOL értesítés](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Partnerek

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

A CoreOS webhelyéről:

*CoreOS biztonságot, a konzisztencia és a megbízhatóságot lett tervezve. Helyett a yum használatával vagy az apt-csomagok telepítése, a CoreOS Linux-tárolókat használja a magasabb absztrakciós szinten szolgáltatások kezeléséhez. Egy egyetlen szolgáltatást kóddal és annak összes függőségét, amely egy vagy több CoreOS rendszerű gépeken is futtatható olyan tárolóban vannak csomagolva.*

### <a name="credativ"></a>credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ egy független tanácsadási és a szolgáltató cég, amely a fejlesztési és szakmai megoldások megvalósítását ingyenes szoftver segítségével. Nyílt forráskódú vezető szakértőitől, mint a Credativ számos informatikai részlegek által használt támogatási a nemzetközi felismerése rendelkezik. A Microsoft együtt Credativ van jelenleg megfelelő Debian-rendszerképek előkészítéséről Debian 8 (Jessie) és a Debian 7 (Wheezy) előtt. Mindkét lemezképek kifejezetten tervezték, hogy az Azure-ban, és könnyen kezelhető a platform használatával. A hosszú távú karbantartási és a Debian rendszerképet az Azure a nyílt forrás támogatási erőforrások révén frissítése Credativ is támogatja.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle stratégia egy nyilvános és magánfelhőkben megoldásokat tartalmazó nyújtani. A stratégia révén ügyfelei választási lehetőséget és hogyan azokat üzembe Oracle-felhőben lévő Oracle-szoftverek és más felhők rugalmasságot. Az Oracle és a Microsoft partneri kapcsolatának köszönhetően lehetőség van az Oracle-szoftverek üzembe helyezésére a Microsoft nyilvános és magánfelhőiben az Oracle támogatásával és minősítéseivel.  Oracle kötelezettségvállalás és a beruházások Oracle nyilvános és magánfelhő-megoldások nem változik.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

A világ vezető szolgáltatója, nyílt forráskódú megoldások, a Red Hat segítségével több mint 90 %-át a Fortune 500-as cégek üzleti problémák megoldásában, igazítása informatikai és üzleti stratégiát, és készítse elő a technológia a jövő. Red Hat azáltal, hogy egy nyitott üzleti modell és a egy kedvező árú, kiszámítható előfizetésen keresztül olyan biztonságos megoldásokat azért teszi ezt.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server az Azure-ban az bevált platform, amely kiemelkedő megbízhatóságot és biztonságot felhő-számítástechnika biztosít. A SUSE sokoldalú Linux rendszeren zökkenőmentesen integrálható az Azure-felhőszolgáltatásokat, hogy egy könnyen kezelhető felhőbeli környezetre. A több mint 9,200 tanúsított alkalmazások több mint 1,800 független szoftvergyártók SUSE Linux Enterprise Server SUSE biztosítja, hogy az adatközpontban futó támogatott munkaterhelések magabiztosan telepíthető az Azure-ban.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical termékgondozó csoportja és a nyílt közösségi cégirányítási meghajtó Ubuntu a sikeres ügyfél, kiszolgáló és a felhő-számítástechnika, amely tartalmazza a fogyasztók számára személyes felhőszolgáltatások. Canonical vision telefonról a felhőbe, az Ubuntu, egységes, ingyenes platform összessége, egységes felületeket biztosít a telefonon, táblagépen, TV és asztali. A vision segítségével Ubuntu consumer Electronics a döntéshozók és kedvencként egyes technologists között nyilvánosfelhő-szolgáltatók különböző intézmények számára az első választás.

A fejlesztők és mérnöki adatközpontok a világ különböző pontjain Canonical kivételes helyzetéből adódóan a hardver döntéshozókat, tartalomszolgáltatók és szoftverfejlesztők Ubuntu megoldások használata a számítógépek, kiszolgálók és a kézi eszközök piacon.
