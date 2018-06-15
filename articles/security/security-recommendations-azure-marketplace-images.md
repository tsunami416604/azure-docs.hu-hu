---
title: Biztonsági javaslatok az Azure piactéren elérhető rendszerkép |} Microsoft Docs
description: Ez a cikk piacon rendszerképeket vonatkozó javaslatokkal szolgál.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
ms.locfileid: "23931134"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Az Azure piactéren elérhető rendszerkép biztonsági javaslatok

Azt javasoljuk, hogy az egyes megoldások megfelelnek-e a következő biztonsági javaslatok. Ez segít a partneri megoldás képek az Azure piactér biztonság magas szintű karbantartása.

Ezek a javaslatok is hasznos lehet az Azure piactéren lemezképek nem rendelkező szervezeteknek. Előfordulhat, hogy ellenőrizni kívánja a vállalati Windows és Linux-lemezkép konfigurációk szemben az alábbi táblázatokban található útmutatást.

## <a name="open-source-based-images"></a>Nyissa meg a forrás-alapú lemezképek

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategória**                                                 | **Jelölőnégyzet**                                                                                                                                                                                                                                                                              |
| Biztonság                                                     | A legújabb biztonsági javításokat a Linux terjesztési telepítve vannak.                                                                                                                                                                                                              |
| Biztonság                                                     | Iparági irányelveket a Virtuálisgép-lemezkép az adott Linux terjesztési biztonságos követték.                                                                                                                                                                                     |
| Biztonság                                                     | A támadási felület minimális erőforrásigényét tartása csak a szükséges Windows kiszolgálói szerepkörök, szolgáltatások, szolgáltatásaival és hálózati portok által korlátozza.                                                                                                                                               |
| Biztonság                                                     | Forráskód és az eredményül kapott Virtuálisgép-lemezkép kártevők vizsgálata.                                                                                                                                                                                                                                   |
| Biztonság                                                     | A Virtuálismerevlemez-kép csak tartalmazza a szükséges zárolt, alapértelmezett jelszót, amely lehetővé teszi az interaktív bejelentkezési; nem rendelkező fiókok Nincs összetevőkről.                                                                                                                                           |
| Biztonság                                                     | Tűzfalszabályok le vannak tiltva, kivéve, ha az alkalmazás funkcionálisan támaszkodik, például egy tűzfal készülék.                                                                                                                                                                             |
| Biztonság                                                     | Az összes bizalmas adatok el lettek távolítva a Virtuálismerevlemez-kép, például a hosts fájl, a naplófájlok és a szükségtelen tanúsítványok ismert teszt SSH-kulcsok.                                                                                                                                       |
| Biztonság                                                     | Javasoljuk, hogy LVM nem használni.                                                                                                                                                                                                                                            |
| Biztonság                                                     | Szükséges könyvtárak legújabb verzióját kell figyelembe venni: </br> -OpenSSL 1.0-s vagy újabb </br> -Python 2.5-ös vagy újabb (a Python 2.6 + erősen ajánlott) </br> -Python pyasn1 csomagot, ha még nincs telepítve </br> -d.OpenSSL v 1.0-s vagy újabb                                                                |
| Biztonság                                                     | Rendszerhéjakba/kérelemelőzmény-bejegyzésekkel törölni kell.                                                                                                                                                                                                                                             |
| Hálózat                                                   | Alapértelmezés szerint SSH-kiszolgálót kell tartalmaznia. SSH megtartása életben beállítása a következő kapcsolóval sshd config: ClientAliveInterval 180                                                                                                                                                        |
| Hálózat                                                   | Lemezkép nem tartalmazhat egyéni hálózati konfigurációt. Törölje a resolv.conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Környezet                                                   | Telepítse a legújabb Azure Linux ügynök </br> -Az ügynök a RPM vagy Deb csomaggal kell telepíteni.  </br> -Is használhatja a manuális telepítés folyamata, de a csomagok javasolt és ajánlott. </br> – Ha az ügynököt manuálisan telepíti a github-tárházban, másolnia a `waagent` fájlt `/usr/sbin` és futtató (root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Az ügynök konfigurációs fájl kerüljön `/etc/waagent.conf`.    |
| Környezet                                                   | Biztosítja, hogy az Azure támogatási szolgálatához a soros konzol kimenet, amikor szükséges, és adjon meg megfelelő időtúllépés OS lemez csatlakoztatása a felhőalapú tárolást partnereink képes biztosítani. Kép kell jelentek meg a következő paraméterek a Kernel rendszerindító sorba:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Környezet                                                   | Nincs az operációs rendszer lemezének swap partíció. A kért csere a helyi erőforrás lemez létrehozásához a Linux-ügynök.         |
| Környezet                                                   | Javasoljuk, hogy egyetlen partíció létrejön az operációsrendszer-lemezképet.      |
| Környezet                                                   | 64 bites operációs rendszer.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-alapú lemezképek

|||
|-------------| -------------------------|
| **Kategória**                                                     | **Jelölőnégyzet**                                                                                                                                                                |
| Biztonság                                                         | Használja a biztonságos az operációs rendszer alapjául szolgáló lemezképhez. A Windows Server-alapú képet forrásaként használt virtuális Merevlemezt a a Windows Server operációs rendszer lemezképeinek Microsoft Azure keresztül kell lennie. |
| Biztonság                                                         | Telepítse a legújabb frissítések.                                                                                                                                     |
| Biztonság                                                         | Alkalmazások függőség nem rendelkezhet a korlátozott felhasználói neveket, például rendszergazda, a legfelső szintű és a rendszergazda segítségét.                                                                |
| Biztonság                                                         | A BitLocker meghajtótitkosítás nem támogatott az operációs rendszer merevlemezén. A BitLocker adatlemezek használható.                                                            |
| Biztonság                                                         | A támadási felület minimális erőforrásigényét tartása csak a szükséges Windows kiszolgálói szerepkörök, szolgáltatások, szolgáltatások és engedélyezett hálózati portok által korlátozza.                         |
| Biztonság                                                         | Forráskód és az eredményül kapott Virtuálisgép-lemezkép kártevők vizsgálata.                                                                                                                     |
| Biztonság                                                         | Állítsa be a Windows Server lemezképeket biztonsági frissítése az automatikus frissítés.                                                                                                                |
| Biztonság                                                         | A Virtuálismerevlemez-kép csak tartalmazza a szükséges zárolt, alapértelmezett jelszót, amely lehetővé teszi az interaktív bejelentkezési; nem rendelkező fiókok Nincs összetevőkről.                             |
| Biztonság                                                         | Tűzfalszabályok le vannak tiltva, kivéve, ha az alkalmazás funkcionálisan támaszkodik, például egy tűzfal készülék.                                                               |
| Biztonság                                                         | Az összes bizalmas adatok el lettek távolítva a Virtuálismerevlemez-kép. Például a HOSTS fájl, a naplófájlok és a szükségtelen tanúsítványok kell eltávolítani.                                              |
| Környezet                                                       | 64 bites operációs rendszer.                            |
