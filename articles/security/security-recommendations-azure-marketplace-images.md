---
title: Biztonsági javaslatok az Azure Marketplace-rendszerképek |} A Microsoft Docs
description: Ez a cikk tartalmazza a Marketplace-beli lemezképek vonatkozó javaslatokkal szolgál
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: 17372076f06cbaa833c437121a01f0dcaaf0e757
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243633"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Biztonsági javaslatok az Azure Marketplace-rendszerképek

Azt javasoljuk, hogy az egyes megoldások megfelel-e az alábbi biztonsági konfigurációs javaslatokat. Ez segít fenntartani a magas szintű biztonsági partneri megoldás rendszerképeket az Azure piactéren.

Ezekkel az ajánlásokkal is hasznos lehet a szervezet számára, amelyek nem rendelkeznek rendszerképek az Azure Marketplace-en. Érdemes ellenőrizni a vállalati Windows és Linux rendszerű kép konfigurációit az irányelveket, az alábbi táblázatban található:

## <a name="open-source-based-images"></a>Nyissa meg a forrás-alapú rendszerképekhez

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategória**                                                 | **Jelölőnégyzet**                                                                                                                                                                                                                                                                              |
| Biztonság                                                     | A legújabb biztonsági javításokat a Linux-disztribúció vannak telepítve.                                                                                                                                                                                                              |
| Biztonság                                                     | Iparági irányelveket a VM-lemezkép az adott Linux-disztribúció biztonságos történtek.                                                                                                                                                                                     |
| Biztonság                                                     | A támadási felület elérésének korlátozását azáltal, hogy csak a szükséges Windows Server-szerepkörök, szolgáltatások, szolgáltatások minimális erőforrás-igényű tárolja, és a hálózatkezelési portokat.                                                                                                                                               |
| Biztonság                                                     | Forráskód és a kártevők eredményül kapott Virtuálisgép-lemezkép beolvasása.                                                                                                                                                                                                                                   |
| Biztonság                                                     | A VHD-lemezképet csak tartalmazza a szükséges zárolt fiókok, amelyek nem rendelkeznek alapértelmezett jelszót, amely lehetővé teszi interaktív bejelentkezést. Nincs összetevőkről.                                                                                                                                           |
| Biztonság                                                     | Tűzfalszabályok le vannak tiltva, kivéve, ha az alkalmazás funkcionálisan támaszkodik, például a tűzfalat.                                                                                                                                                                             |
| Biztonság                                                     | Bizalmas adatok el lettek távolítva a VHD-rendszerképet, például az állomásleíró fájlhoz, a naplófájlok és a szükségtelen tanúsítványok ismert vizsgálati SSH-kulcsokat.                                                                                                                                       |
| Biztonság                                                     | Javasoljuk, hogy az LVM nem kell-e használatban.                                                                                                                                                                                                                                            |
| Biztonság                                                     | Szükséges kódtárak legújabb verzióit kell kiválasztani: </br> -OpenSSL 1.0-s verzió vagy újabb </br> -Python 2.5-ös vagy újabb (a Python 2.6 erősen ajánlott) </br> – Python pyasn1 csomag, ha még nem telepítette </br> -d.OpenSSL v 1.0-s vagy újabb                                                                |
| Biztonság                                                     | Bash-rendszerhéjból triggerelőzmény-bejegyzések törölni kell.                                                                                                                                                                                                                                             |
| Hálózat                                                   | SSH-kiszolgáló alapértelmezés szerint kell tartalmaznia. SSH életben tartási beállítása az sshd_config a következő beállítással: ClientAliveInterval 180.                                                                                                                                                        |
| Hálózat                                                   | Lemezkép nem tartalmazhat bármilyen egyéni hálózati beállítások. A resolv.conf törlése: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Környezet                                                   | Legújabb Azure Linux-ügynök telepítve kell lennie </br> – Az ügynököt telepíteni kell az RPM- vagy Deb-csomag használatával.  </br> – Is használhatja a manuális telepítési folyamatot, de a telepítőcsomagokat ajánlott, és előnyben részesített. </br> – Ha az ügynök manuális telepítése a GitHub-adattárból, először másolja a `waagent` fájlt `/usr/sbin` és futtató (root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Az ügynök konfigurációs fájlját van elhelyezve `/etc/waagent.conf`.    |
| Környezet                                                   | Biztosítja, hogy az Azure támogatási szolgálata megadhassa az partnereink a soros konzol kimenetét, ha szükséges, és elegendő időt biztosít az operációs rendszer lemez csatlakoztatása a felhőalapú tárból. Kép kell a következő paraméterek hozzáadta a rendszermag rendszerindítási sorához: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Környezet                                                   | Nincs operációsrendszer-lemez lapozó partíciót. Lapozófájl-kapacitás a Linux-ügynök igényelhet a helyi erőforrás lemez létrehozásához.         |
| Környezet                                                   | Javasoljuk, hogy egyetlen gyökérpartíciót a rendszerlemez számára létrejött-e.      |
| Környezet                                                   | 64 bites operációs rendszer.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>A Windows Server-alapú rendszerképekhez

|||
|-------------| -------------------------|
| **Kategória**                                                     | **Jelölőnégyzet**                                                                                                                                                                |
| Biztonság                                                         | Egy biztonságos alap operációsrendszer-lemezkép használata. A virtuális merevlemez alapján a Windows Server bármilyen kép forrását használja a Microsoft Azure-on keresztül biztosított Windows Server operációs rendszer rendszerképek kell lennie. |
| Biztonság                                                         | Telepítse a legújabb frissítések.                                                                                                                                     |
| Biztonság                                                         | Alkalmazások nem rendelkezhet egy függőségi korlátozott felhasználói nevek, például a rendszergazda, a legfelső szintű és a rendszergazdával.                                                                |
| Biztonság                                                         | A BitLocker meghajtótitkosítás nem támogatott az operációs rendszer merevlemezén. A BitLocker az adatlemezeket is használható.                                                            |
| Biztonság                                                         | A támadási felület elérésének korlátozását azáltal, hogy csak a szükséges Windows Server-szerepkörök, szolgáltatások, szolgáltatások minimális erőforrás-igényű tárolja, és a hálózati portok engedélyezve van.                         |
| Biztonság                                                         | Forráskód és a kártevők eredményül kapott Virtuálisgép-lemezkép beolvasása.                                                                                                                     |
| Biztonság                                                         | Állítsa be a Windows Server rendszerképek biztonsági frissítés automatikus frissítéséhez.                                                                                                                |
| Biztonság                                                         | A VHD-lemezképet csak tartalmazza a szükséges zárolt fiókok, amelyek nem rendelkeznek alapértelmezett jelszót, amely lehetővé teszi interaktív bejelentkezést. Nincs összetevőkről.                             |
| Biztonság                                                         | Tűzfalszabályok le vannak tiltva, kivéve, ha az alkalmazás funkcionálisan támaszkodik, például a tűzfalat.                                                               |
| Biztonság                                                         | Bizalmas adatok el lettek távolítva a VHD-lemezképet. Például a GAZDAGÉPEK fájl, a naplófájlok és a szükségtelen tanúsítványok kell eltávolítani.                                              |
| Környezet                                                       | 64 bites operációs rendszer.                            |
