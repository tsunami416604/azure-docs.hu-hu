---
title: Linux-disztribúciók az Azure-ban
description: Ismerje meg az Azure által támogatott disztribúciók Linux szolgáltatását, beleértve az Ubuntu, a CentOS, az Oracle és a SUSE irányelveit.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: aebb31c825725e8b748fe52bfdf74a79074d6a52
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460494"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Jóváhagyott Linux-disztribúciók az Azure-ban
A partnerek Linux-rendszerképeket biztosítanak az Azure Piactéren. Dolgozunk a különböző Linux közösségek hozzá még több ízek a támogatott distribution listán. Addig is, a disztribúciók, amelyek nem állnak rendelkezésre a Marketplace-en, akkor mindig hozza a saját Linux követve az iránymutatások [létrehozása és feltöltegy virtuális merevlemez, amely tartalmazza a Linux operációs rendszer](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Támogatott disztribúciók és verziók
Az alábbi táblázat felsorolja az Azure-ban támogatott Linux-disztribúciókat és -verziókat. A Linux és a nyílt forráskódú technológiák Azure-beli támogatásáról a [Microsoft Azure-ban a Linux-lemezképek támogatása](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) című témakörben talál részletesebb információt.

A Hyper-V és az Azure Linux integration services (LIS) illesztőprogramjai olyan kernelmodulok, amelyeket a Microsoft közvetlenül a upstream Linux kernelhez ad.  Egyes LIS-illesztőprogramok alapértelmezés szerint be vannak építve a disztribúció kernelébe. A Red Hat Enterprise (RHEL)/CentOS szolgáltatáson alapuló régebbi disztribúciók külön letöltésként érhetők el a [Linux Integration Services 4.2-es verziójában a Hyper-V és az Azure számára.](https://www.microsoft.com/download/details.aspx?id=55106) A [LIS-illesztőprogramokkal](create-upload-generic.md#linux-kernel-requirements) kapcsolatos további információkért tekintse meg a Linux kernel követelményeit.

Az Azure Linux-ügynök már előre telepítve van az Azure Marketplace-rendszerképeken, és általában elérhető a disztribúció csomagtárház. A forráskód megtalálható a [GitHubon.](https://github.com/azure/walinuxagent)


| Disztribúció | Verzió | Illesztőprogramok | Ügynök |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+, 8.0+ |CentOS 6.3: [LIS letöltés](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: A kernelben |Csomag: [Repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) alatt "WALinuxAgent" <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |A kernelben |Forráskód: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+, 9, 10 |A kernelben |Csomag: A repo alatt "waagent" <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |A kernelben |Csomag: Repo alatt "WALinuxAgent" <br/>Forráskód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+, 8.0+ |A kernelben |Csomag: Repo alatt "WALinuxAgent" <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES SAP-hoz<br>11 SP4<br>12 SP1+<br>15|A kernelben |Csomag:<p> 11-hez a [felhőben:Eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tártárja<br>a "Nyilvános felhő" modulban a "python-azure-agent" alatt található 12-hez<br/>Forráskód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |A kernelben |Csomag: A [felhőben:Eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tártára a "python-azure-agent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ ** <sup>1</sup>** |A kernelben |Csomag: Repo alatt "walinuxagent" <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Az Ubuntu 12.04 és 14.04 kiterjesztett támogatásáról itt olvashat: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Képfrissítési lépésszám
Az Azure megköveteli, hogy a jóváhagyott Linux-disztribúciók közzétevői rendszeresen frissítsék a lemezképeiket az Azure Piactéren a legújabb javításokkal és biztonsági javításokkal, negyedévente vagy gyorsabb ütemben. Az Azure Piactéren frissített lemezképek automatikusan elérhetők az ügyfelek számára egy lemezkép termékváltozatának új verziójaként. További információ a Linux-lemezképek megkereséséről: [Linuxos virtuálisgép-lemezképek keresése az Azure Marketplace-en.](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage)

### <a name="additional-links"></a>További hivatkozások
 - [SUSE nyilvános felhőkép életciklusa](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Azure-ra hangolt kernelek

Az Azure szorosan együttműködik a különböző jóváhagyott Linux-disztribúciókkal az Azure Marketplace-en közzétett lemezképek optimalizálása érdekében. Ennek az együttműködésnek az egyik aspektusa a "hangolt" Linux kernelek fejlesztése, amelyek az Azure platformra vannak optimalizálva, és a Linux-disztribúció teljes mértékben támogatott összetevőiként szolgálnak. Az Azure-tuned kernelek új funkciókat és teljesítménybeli fejlesztéseket tartalmaznak, és gyorsabb (általában negyedéves) ütemben, mint a disztribúcióból elérhető alapértelmezett vagy általános kernelek.

A legtöbb esetben ezeket a kerneleket előre telepítve fogja az Azure Piactéren, így az Azure-ügyfelek azonnal élvezhetik az optimalizált kernelek előnyeit. Ezekről az Azure-tunes kernelekről az alábbi hivatkozásokon talál további információt:

 - CentOS Azure-tuned Kernel - Elérhető a CentOS Virtualization SIG - [További információ](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud Kernel - Elérhető a Debian 10 és a Debian 9 "backports" képpel az Azure-ban - [További információ](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure-hangolt kernel - [További információ](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-hangolt kernel - [További információ](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partnerek

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

A CoreOS webhelyéről:

*A CoreOS-t biztonságra, konzisztenciára és megbízhatóságra tervezték. Ahelyett, hogy yum vagy apt segítségével telepítene csomagokat, a CoreOS Linux-tárolókat használ a szolgáltatások magasabb szintű absztrakciós kezeléséhez. Egyetlen szolgáltatás kódja és az összes függőség egy tárolóba van csomagolva, amely egy vagy több CoreOS-gépen futtatható.*

### <a name="credativ"></a>Credativ között
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

A Credativ egy független tanácsadó és szolgáltató vállalat, amely a professzionális megoldások fejlesztésére és végrehajtására specializálódott a szabad szoftver használatával. Vezető nyílt forráskódú szakemberként a Credativ nemzetközi elismeréssel rendelkezik számos informatikai részleggel, amelyek a támogatásukat használják. A Microsofttal együtt a Credativ jelenleg készíti elő a megfelelő Debian képeket a Debian 8 (Jessie) és a Debian számára 7 előtt (Wheezy). Mindkét lemezképek kifejezetten az Azure-ban való futtatásra tervezték, és könnyen kezelhetők a platformon keresztül. A Credativ a nyílt forráskódú támogatási központjain keresztül támogatja az Azure-hoz készült Debian-képek hosszú távú karbantartását és frissítését is.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Az Oracle stratégiája az, hogy széles körű megoldásokat kínál a köz- és magánfelhők számára. A stratégia választási lehetőséget és rugalmasságot biztosít az ügyfelek számára az Oracle-szoftverek Oracle-felhőkben és más felhőkben történő üzembe helyezésében. Az Oracle és a Microsoft együttműködése lehetővé teszi az ügyfelek számára, hogy oracle-szoftvereket telepítsenek a Microsoft nyilvános és privát felhőibe, az Oracle minősítésének és támogatásának megbízhatóságával.  Az Oracle elkötelezettsége és befektetése az Oracle nyilvános és magánfelhő-megoldásokba változatlan.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

A világ vezető nyílt forráskódú megoldásokat kínáló cége, a Red Hat a Fortune 500-as vállalatok több mint 90%-ának segít megoldani az üzleti kihívásokat, összehangolja informatikai és üzleti stratégiáját, és felkészül a technológia jövőjére. A Red Hat ezt úgy éri el, hogy biztonságos megoldásokat kínál egy nyitott üzleti modellen és egy megfizethető, kiszámítható előfizetési modellen keresztül.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

A SUSE Linux Enterprise Server az Azure-ban egy bevált platform, amely kiváló megbízhatóságot és biztonságot nyújt a felhőalapú számítástechnikához. A SUSE sokoldalú Linux platformja zökkenőmentesen integrálható az Azure felhőszolgáltatásaival, így könnyen kezelhető felhőalapú környezetet biztosít. Több mint 9200, a SUSE Linux Enterprise Server hez készült független szoftverszállító több mint 9200 tanúsított alkalmazásával a SUSE biztosítja, hogy az adatközpontban támogatott számítási feladatok magabiztosan telepíthetők legyenek az Azure-ban.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

A kanonikus tervezés és a nyílt közösségi irányítás az Ubuntu sikerét az ügyfél-, szerver- és felhőalapú számítástechnika terén, amely magában foglalja a személyes felhőszolgáltatásokat a fogyasztók számára. A Canonical elképzelése egy egységes, ingyenes platformról az Ubuntuban, a telefontól a felhőig, koherens interfészeket biztosít a telefonhoz, táblagéphez, TV-hez és asztali számítógéphez. Ez a jövőkép teszi ubuntu az első választás a különböző intézmények nyilvános felhő szolgáltatók a döntéshozók a fogyasztói elektronika és a kedvenc az egyes technológusok.

A fejlesztők és mérnöki központok a világ minden tájáról, A Canonical egyedülálló helyzetben van, hogy partnere a hardver döntéshozók, tartalomszolgáltatók, és a szoftverfejlesztők, hogy Ubuntu megoldások piacra PC-k, szerverek, és kézi eszközök.
