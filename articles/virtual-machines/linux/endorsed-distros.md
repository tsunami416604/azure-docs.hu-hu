---
title: Az Azure-ban támogatott Linux-disztribúciók
description: Ismerje meg az Azure által támogatott disztribúciók Linuxját, beleértve az Ubuntu, a CentOS, az Oracle és a SUSE útmutatásait.
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
ms.openlocfilehash: a2edc8d0116778e292f0e44761100d3c1e9212ff
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527387"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Támogatott Linux-disztribúciók az Azure-ban

A partnerek linuxos rendszerképeket biztosítanak az Azure piactéren. A Microsoft különböző Linux-közösségekkel működik együtt, hogy még több ízt adjon hozzá a támogatott terjesztési listához. A piactéren nem elérhető disztribúciók esetében a [Linux operációs rendszert tartalmazó virtuális merevlemez létrehozása és feltöltése](./create-upload-generic.md)című rész útmutatását követve mindig saját Linux-t hozhat létre.

## <a name="supported-distributions-and-versions"></a>Támogatott disztribúciók és verziók

A következő táblázat felsorolja az Azure-ban támogatott Linux-disztribúciókat és-verziókat. A Linux és a nyílt forráskódú technológiák Azure-beli támogatásával kapcsolatos részletesebb információkért tekintse meg a [Linux-rendszerképek támogatását Microsoft Azureban](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) .

A Hyper-V és az Azure-hoz készült Linux Integration Services (LIS) illesztőprogramok a Microsoft által közvetlenül a felsőbb rétegbeli linuxos kernelhez hozzájáruló kernel-modulok. Egyes LIS-illesztőprogramok alapértelmezés szerint a terjesztés rendszermagba vannak építve. A Red Hat Enterprise (RHEL)/CentOS alapuló régebbi disztribúciók külön letöltésként érhetők el a [Linux Integration Services 4,2-es verziójában a Hyper-V és az Azure számára](https://www.microsoft.com/download/details.aspx?id=55106). Az LIS-illesztőprogramokkal kapcsolatos további információkért tekintse meg a [Linux kernelre vonatkozó követelményeket](create-upload-generic.md#linux-kernel-requirements) .

Az Azure Linux-ügynök már előre telepítve van az Azure Marketplace-lemezképeken, és általában a terjesztési csomag adattárában érhető el. A forráskód megtalálható a [githubon](https://github.com/azure/walinuxagent).

| Disztribúció | Verzió | Illesztőprogramok | Ügynök |
| --- | --- | --- | --- |
| CentOS a szélhámos Wave szoftverrel |CentOS 6. x, 7. x, 8. x |CentOS 6,3: [lis Letöltés](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: a kernelben |Csomag [: a tárházban a "](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) WALinuxAgent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> A CoreOS mostantól a 2020. május 26-án [véget ért](https://coreos.com/os/eol/) . |Már nem érhető el | | |
| Debian by Credativ |8.x, 9.x |A kernelben |Csomag: a tárházban a "waagent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Flatcar-tároló linuxos Kinvolk szerint| Stable, Edge| | |
| Oracle-Oracle Linux |6.x, 7.x, 8.x |A kernelben |Csomag: a tárházban a "WALinuxAgent" alatt <br/>Forráskód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| A Red Hat Red Hat Enterprise Linux |6.x, 7.x, 8.x |A kernelben |Csomag: a tárházban a "WALinuxAgent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise a SUSE használatával |SLES/SLES for SAP 11. x, 12. x, 15. x <br/> [SUSE nyilvános Felhőbeli rendszerképek életciklusa](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |A kernelben |Csomag<p> 11 a [felhőben: eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tárháza<br>a "nyilvános felhő" modulban a "Python-Azure-Agent" alatt található 12.<br/>Forráskód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE – SUSE |openSUSE Leap 15.x |A kernelben |Csomag: a [felhőben: eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tárháza a "Python-Azure-Agent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu Canonical alapján |Ubuntu-kiszolgáló és Pro. 16. x, 18. x, 20. x<p>Az Ubuntu 12,04 és 14,04 kiterjesztett támogatásával kapcsolatos információk itt találhatók: [Ubuntu Extended Security karbantartás](https://www.ubuntu.com/esm). |A kernelben |Csomag: a tárházban a "walinuxagent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Rendszerkép frissítésének ritmusa

Az Azure-nak szüksége van arra, hogy a támogatott Linux-disztribúciók közzétevői rendszeresen frissítse lemezképeit az Azure Marketplace-en a legújabb javításokkal és biztonsági javításokkal, negyedévente vagy gyorsabb ütemben. Az Azure Marketplace-en frissített lemezképek automatikusan elérhetők az ügyfelek számára a lemezkép SKU új verzióiként. További információ a Linux-rendszerképek megkereséséről: Linux rendszerű [virtuális gépek rendszerképeinek megkeresése az Azure piactéren](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Azure által hangolt kernelek

Az Azure szorosan együttműködik a különböző támogatott Linux-disztribúciókkal az Azure piactéren közzétett lemezképek optimalizálásához. Ezen együttműködés egyik aspektusa az Azure platformra optimalizált, "hangolt" Linux-kernelek fejlesztése, amely a Linux-disztribúció teljes mértékben támogatott összetevőiként érhető el. Az Azure által hangolt kernelek új funkciókkal és teljesítménnyel kapcsolatos újdonságokkal rendelkeznek, és gyorsabb (általában negyedéves) ritmust tartalmaznak a terjesztésből elérhető alapértelmezett vagy általános kernelekhez képest.

A legtöbb esetben az Azure Marketplace-en az alapértelmezett lemezképekre előre telepített kerneleket talál, így az ügyfelek azonnal kihasználhatják ezeket az optimalizált kerneleket. Az Azure-ban hangolt kernelekről további információt az alábbi hivatkozásokon talál:

- [CentOS Azure által hangolt kernel – a CentOS virtualizációs SIG használatával érhető el](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian Cloud kernel – elérhető a Debian 10 és a Debian 9 "backports" rendszerképpel az Azure-ban](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure-ban hangolt kernel](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure által hangolt kernel](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)

## <a name="partners"></a>Partnerek

### <a name="coreos"></a>CoreOS

A CoreOS az [élettartam végéig](https://coreos.com/os/eol/) , 2020. május 26-án van ütemezve.
A Microsoft két (2) csatornát használ a CoreOS-felhasználók áttelepítéséhez.

- Flatcar Kinvolk szerint (lásd a "Flatcar Container Linux by Kinvolk" bejegyzést.)
- [Fedora Core operációs rendszer](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (az ügyfeleknek fel kell tölteniük a saját rendszerképét. Az [áttelepítési dokumentációt itt találja](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/).

### <a name="credativ"></a>Credativ

[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

A Credativ egy független tanácsadói és szolgáltatási cég, amely a professzionális megoldások fejlesztésére és megvalósítására specializálódott ingyenes szoftverek használatával. A vezető nyílt forráskódú szakemberek a Credativ nemzetközi elismeréssel rendelkeznek, és számos informatikai részleggel rendelkeznek, amelyek támogatják a támogatást. A Microsofttal együtt a Credativ jelenleg a megfelelő Debian-rendszerképeket készíti elő a Debian 8 (Jessie) és a Debian 7-es verziójában (zihálás). Mindkét rendszerkép kifejezetten az Azure-ban való futtatásra készült, és könnyen kezelhető a platformon keresztül. A Credativ az Azure-hoz készült Debian-rendszerképek hosszú távú karbantartását és frissítését is támogatja a nyílt forráskódú támogatási központjain keresztül.

### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Az Oracle stratégiája a nyilvános és privát felhőkre vonatkozó megoldások széles körét kínálja. A stratégia lehetővé teszi az ügyfelek számára, hogy megválasszák és rugalmasan használják Oracle-szoftvereket az Oracle-felhőkben és más felhőkben. Az Oracle a Microsofttal való együttműködése lehetővé teszi az ügyfeleknek, hogy Oracle-szoftvereket telepítsenek a Microsoft nyilvános és privát felhőkbe az Oracle minősítésének és támogatásának megbízhatóságával.  Az Oracle a nyilvános és a privát felhőalapú megoldások iránti elkötelezettsége és befektetése változatlan marad.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

A világ vezető nyílt forráskódú megoldásokat kínál, a Red hat a Fortune 500-vállalatok több mint 90%-át teszi lehetővé az üzleti problémák megoldását, az informatikai és üzleti stratégiák összehangolását, valamint a technológia jövőjének előkészítését. A Red Hat ezt egy nyitott üzleti modellen és egy megfizethető, kiszámítható előfizetési modellen keresztül biztosít biztonságos megoldásokkal.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

A SUSE Linux Enterprise Server az Azure-ban bevált platform, amely kiváló megbízhatóságot és biztonságot nyújt a felhőalapú számítástechnika számára. A SUSE sokoldalú linuxos platformja zökkenőmentesen integrálható az Azure Cloud Services szolgáltatással, hogy könnyen kezelhető felhőalapú környezetet nyújtson. Ha több mint 9 200 tanúsított alkalmazást használ több mint 1 800 független szoftvergyártótól a SUSE Linux Enterprise Server számára, a SUSE biztosítja, hogy az adatközpontban támogatott munkaterhelések biztonságosan üzembe helyezhetők az Azure-ban.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

A Canonical Engineering és a nyílt közösségi irányítási meghajtó Ubuntu sikere az ügyfél, a kiszolgáló és a felhőalapú számítástechnika terén, amely magában foglalja a személyes Cloud Services szolgáltatást a felhasználók számára. A Canonical víziója egy egységes, ingyenes, az Ubuntuban, a telefonról a felhőbe irányuló platform, amely koherens felületet biztosít a telefonhoz, a Tablethez, a TV-hez és az asztalhoz. Ez a jövőkép lehetővé teszi, hogy az Ubuntu a nyilvános felhőalapú szolgáltatók különböző intézményeinek, a fogyasztói elektronikai szakemberek és az egyéni technikusok körében a kedvenceket is megválasszák.

A világ fejlesztői és mérnöki központjai révén a Canonical egyedi pozíciót biztosít a hardvergyártók, a tartalomszolgáltatók és a szoftverfejlesztők számára, hogy Ubuntu-megoldásokat lehessen forgalmazni a számítógépek, kiszolgálók és kézi eszközök számára.
