---
title: "Által támogatott disztribúciók Linux |} Microsoft Docs"
description: "További információk a Linux az Azure-támogatott disztribúciókkal, többek között irányelvek, Ubuntu, CentOS, Oracle és SUSE."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
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
ms.openlocfilehash: 811769443e322af3a2981c58979040a1e33b06e9
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Az Azure által támogatott disztribúciók Linux
Partnerek adja meg az Azure piactéren Linux-lemezképeket. Már dolgozunk a különböző Linux Közösségek még több változatban is elkészíti a támogatott terjesztési listához adni. Időközben azokat a terjesztéseket, amelyek nem érhetők el a piactérről, az mindig helyezheti a saját Linux által útmutatása szerint [létrehozása és feltöltése, a Linux operációs rendszert tartalmazó virtuális merevlemez](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="supported-distributions-and-versions"></a>Támogatott disztribúcióiról, valamint a verziók
A következő táblázat a Linux terjesztéseket, a támogatott Azure-on. Tekintse meg [támogatja a Microsoft Azure Linux képek](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) részletesebb információ a Linux és az Azure-ban nyílt forráskódú technológiája támogatása.

A Hyper-V és Azure Linux integrációs szolgáltatások (LIS) illesztőprogramok legyenek, amely Microsoft közvetlenül hozzájárul a felsőbb rétegbeli Linux kernel kernel-modulok.  Alapértelmezés szerint a terjesztési kernel egyes LIS illesztőprogramok beépített. Régebbi azokat a terjesztéseket, amelyek Red Hat Enterprise (RHEL) – CentOS, külön letölthető / [Linux integrációs szolgáltatások verzió 4.1 a Hyper-V](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Lásd: [Linux kernel követelmények](create-upload-generic.md#linux-kernel-requirements) a LIS további tudnivalókat.

Az Azure Linux ügynök előre telepítve van az Azure piactéren elérhető rendszerkép és általában a telepítési csomag tárházból. Forráskód található [GitHub](https://github.com/azure/walinuxagent).

  
| Disztribúció | Verzió | Illesztőprogramok | Ügynök |
| --- | --- | --- | --- |
| CentOS |A centOS 6.3 + 7.0 + |CentOS 6.3: [LIS letöltése](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: kernel a |Csomag: A [tárház](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) "WALinuxAgent" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |A kernel |Forráskód: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 + |A kernel |Csomag: a "waagent" adattárban lévő <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |A kernel |Csomag: a "WALinuxAgent" adattárban lévő <br/>Forráskód: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 + 7.1 + |A kernel |Csomag: a "WALinuxAgent" adattárban lévő <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES SAP esetén<br>11 SP4<br>12 SP1 +|A kernel |Csomag:<p> a 11 [felhő: eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tárház<br>a "python-azure-ügynök" a "Nyilvános felhő" modulban 12<br/>Forráskód: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |Termékek 42.2 + openSUSE |A kernel |Csomag: A [felhő: eszközök](https://build.opensuse.org/project/show/Cloud:Tools) tárház "python-azure-ügynök" alatt <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |A kernel |Csomag: a "walinuxagent" adattárban lévő <br/>Forráskód: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  Ubuntu 12.04 támogatásához az Azure tekintse meg a [EOL értesítés](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Partnerek

### <a name="coreos"></a>CoreOS
[https://CoreOS.com/docs/running-CoreOS/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

A CoreOS webhelyéről:

*CoreOS készült biztonsági, konzisztencia- és megbízhatóságát. Helyett yum vagy apt csomagok telepítése, CoreOS Linux tárolók kezelésére használ a szolgáltatások, magasabb absztrakciós szinten. Egyetlen szolgáltatás kódot és az összes függősége olyan, amelyek egy vagy több CoreOS gépeken futtathatók tárolóban vannak csomagolva.*

### <a name="credativ"></a>Credativ
[http://www.credativ.Co.uk/credativ-blog/debian-Images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ egy független tanácsadás és a szolgáltatások munkahelyi, amely a fejlesztés és szakmai megoldások megvalósításához szabad szoftvernek a használatával. Vezető nyílt forráskódú szakemberei, mert a Credativ számos számítástechnikai osztály számára támogatási használó a nemzetközi felismerés rendelkezik. A Microsoft együtt Credativ jelenleg előkészítését végzi, megfelelő Debian képek Debian 8 (Jessie) és a Debian 7 (Wheezy) előtt. Mindkét lemezképek futtatásához Azure kifejezetten, és könnyen kezelhető a platformon keresztül. A hosszú távú karbantartási és az Azure-ba, a nyílt forrás támogatási erőforrások segítségével a Debian lemezképek frissítése Credativ is támogatja.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle stratégia, hogy a nyilvános és magánfelhőkhöz megoldások átfogó portfóliót kínálnak. A stratégia biztosít az ügyfelek hogyan Oracle-felhőkben Oracle szoftvereket és a többi felhőből telepíthet-e azokat a rugalmasságának és. Az Oracle és a Microsoft partneri kapcsolatának köszönhetően lehetőség van az Oracle-szoftverek üzembe helyezésére a Microsoft nyilvános és magánfelhőiben az Oracle támogatásával és minősítéseivel.  Oracle kötelezettségvállalás és Oracle nyilvános és magánfelhő-megoldások használatára irányuló befektetéséből nem változott.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

A világ vezető szolgáltató nyílt forráskódú megoldások, Red Hat segít több mint 90 % Fortune 500 vállalatok üzleti kihívások megoldásában, informatikai igazítása és üzleti stratégiát, és készítse elő a jövőben technológia. Red Hat ennek érdekében biztonságos megoldások egy megnyitott üzleti modelljéhez és egy elérhető, előre jelezhető előfizetés modellt biztosít.

### <a name="suse"></a>SUSE
[http://www.SUSE.com/SUSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server az Azure-on bevált platformot biztosít az felső szintű megbízhatóság és a felhőalapú informatika. SUSE tartozó sokoldalú Linux platformon zökkenőmentesen integrálható a Azure-felhőbe szolgáltatásait, hogy egy könnyen kezelhető felhőalapú környezetben. Több mint 9,200 hiteles alkalmazások SUSE Linux Enterprise Server több mint 1,800 független szoftverszállítóktól származó SUSE biztosítja, hogy az adatközpontban lévő futó támogatott munkaterhelések magabiztosan telepíthető az Azure-on.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Canonical termékgondozó csoportja és a közösségi Megnyitás irányítás meghajtó Ubuntu a sikeres ügyfél, kiszolgáló és a felhőalapú megoldások, mely tartalmazza a fogyasztók személyes felhőbeli szolgáltatások. Egy egységes, szabad platform Ubuntu, a felhőbe, telefonról kanonikus tartozó stratégiai családba tartozó összefüggő felületek biztosít a telefon, táblagép, televízió és asztali. A stratégiai teszi Ubuntu a fogyasztói Electronics döntéshozók és egyedi technologists között kedvenc nyilvánosfelhő-szolgáltatók különböző intézmények első választás.

A fejlesztők és mérnöki központok világszerte a Canonical felkereshetők a hardver döntéshozók, tartalomszolgáltatók és szoftverfejlesztők Ubuntu megoldások kerüljön a számítógépek, kiszolgálók és a kézi eszközök piaci egyedileg elhelyezve.
