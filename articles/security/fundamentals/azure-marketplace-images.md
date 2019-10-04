---
title: Biztonsági javaslatok az Azure Marketplace-lemezképekhez | Microsoft Docs
description: Ez a cikk a piacon található rendszerképekre vonatkozó ajánlásokat tartalmaz
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: 3d6b1ed2a3800058acba9fe5df2137811afed74a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300790"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Biztonsági javaslatok az Azure Marketplace-rendszerképekhez

Javasoljuk, hogy minden megoldás megfeleljen a következő biztonsági konfigurációs javaslatoknak. Ez segít megőrizni a partneri megoldások képeinek magas szintű biztonságát az Azure piactéren.

Ezek a javaslatok olyan szervezetek számára is hasznosak lehetnek, amelyek nem rendelkeznek rendszerképekkel az Azure piactéren. A következő táblázatokban található irányelvek alapján érdemes ellenőrizni a vállalat Windows-és Linux-rendszerképének konfigurációit:

## <a name="open-source-based-images"></a>Nyílt forráskódú lemezképek

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategória**                                                 | **Ellenőrizze**                                                                                                                                                                                                                                                                              |
| Biztonság                                                     | A Linux-disztribúció legújabb biztonsági javításai telepítve vannak.                                                                                                                                                                                                              |
| Biztonság                                                     | Az iparági irányelvek a virtuálisgép-rendszerkép biztonságossá tételéhez szükségesek az adott Linux-disztribúcióhoz.                                                                                                                                                                                     |
| Biztonság                                                     | Korlátozza a támadási felületet úgy, hogy a minimális helyigényt csak a szükséges Windows Server-szerepkörökkel, szolgáltatásokkal, szolgáltatásokkal és hálózati portokkal tartja.                                                                                                                                               |
| Biztonság                                                     | A forráskód és a létrejövő virtuálisgép-rendszerkép vizsgálata a kártevő szoftverhez.                                                                                                                                                                                                                                   |
| Biztonság                                                     | A VHD-rendszerkép csak a szükséges zárolt fiókokat tartalmazza, amelyek nem rendelkeznek az interaktív bejelentkezést lehetővé tevő alapértelmezett jelszavakkal. nincs hátsó ajtó.                                                                                                                                           |
| Biztonság                                                     | A tűzfalszabályok le vannak tiltva, kivéve, ha az alkalmazás működés közben nem támaszkodik rájuk, például egy tűzfal készülékre.                                                                                                                                                                             |
| Biztonság                                                     | A rendszer minden bizalmas információt eltávolítottan a VHD-rendszerképből, például az SSH-kulcsok, az ismert gazdagépek, a naplófájlok és a szükségtelen tanúsítványok számára.                                                                                                                                       |
| Biztonság                                                     | Azt javasoljuk, hogy az LVM ne legyen használatban.                                                                                                                                                                                                                                            |
| Biztonság                                                     | A szükséges kódtárak legújabb verzióit a következőket kell figyelembe venni: </br> -OpenSSL 1.0 vagy újabb verzió </br> -Python 2,5 vagy újabb (Python 2.6 + ajánlott) </br> -Python pyasn1-csomag, ha még nincs telepítve </br> -d. OpenSSL v 1,0 vagy újabb                                                                |
| Biztonság                                                     | A bash/shell History bejegyzéseket törölni kell                                                                                                                                                                                                                                             |
| Hálózat                                                   | Az SSH-kiszolgálót alapértelmezés szerint fel kell venni. Állítsa be az SSH-t életben az sshd-konfigurációba a következő beállítással: ClientAliveInterval 180                                                                                                                                                        |
| Hálózat                                                   | A rendszerkép nem tartalmazhat egyéni hálózati konfigurációt. Törölje a resolv. conf fájlt:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Környezet                                                   | A legújabb Azure Linux-ügynököt telepíteni kell </br> – Az ügynököt az RPM-vagy deb-csomag használatával kell telepíteni.  </br> – A manuális telepítési folyamat is használható, de a telepítő csomagok ajánlottak és előnyben részesítettek. </br> – Ha manuálisan telepíti az ügynököt a GitHub-tárházból, először `waagent` másolja `/usr/sbin` és futtassa a fájlt (root néven): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Az ügynök konfigurációs fájlját a rendszer `/etc/waagent.conf`a következő helyen helyezi el:.    |
| Környezet                                                   | Gondoskodik arról, hogy az Azure-támogatás szükség esetén a soros konzol kimenetével biztosítson partnereinknek, és megfelelő időtúllépést biztosítson az operációsrendszer-lemez csatlakoztatásához a felhőalapú tárolóból. A rendszerképnek hozzá kell adnia a következő paramétereket a kernel rendszerindítási sorához:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Környezet                                                   | Nincs lapozófájl-partíció az operációsrendszer-lemezen. A felcserélés a Linux-ügynök által a helyi erőforrás lemezén történő létrehozáshoz kérhető le.         |
| Környezet                                                   | Ajánlott egyetlen legfelső szintű partíciót létrehozni az operációsrendszer-lemezhez.      |
| Környezet                                                   | 64 bites operációs rendszer csak.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-alapú rendszerképek

|||
|-------------| -------------------------|
| **Kategória**                                                     | **Ellenőrizze**                                                                                                                                                                |
| Biztonság                                                         | Használjon biztonságos operációsrendszer-alapképet. A Windows Serveren alapuló lemezképek forrásához használt virtuális merevlemeznek a Microsoft Azure által biztosított Windows Server operációsrendszer-lemezképből kell származnia. |
| Biztonság                                                         | Telepítse a legújabb biztonsági frissítéseket.                                                                                                                                     |
| Biztonság                                                         | Az alkalmazásoknak nem lehet a korlátozott felhasználónevek, például a rendszergazda, a gyökér és a rendszergazda függősége.                                                                |
| Biztonság                                                         | BitLocker meghajtótitkosítás engedélyezve van az operációs rendszer merevlemez-meghajtója és az adatmerevlemez-meghajtók esetében is.                                                             |
| Biztonság                                                         | Korlátozza a támadási felületet úgy, hogy minimális helyigényt biztosít, csak a szükséges Windows Server-szerepköröket, szolgáltatásokat, szolgáltatásokat és hálózati portokat engedélyezte.                         |
| Biztonság                                                         | A forráskód és a létrejövő virtuálisgép-rendszerkép vizsgálata a kártevő szoftverhez.                                                                                                                     |
| Biztonság                                                         | A Windows Server-lemezképek biztonsági frissítésének beállítása az automatikus frissítéshez.                                                                                                                |
| Biztonság                                                         | A VHD-rendszerkép csak a szükséges zárolt fiókokat tartalmazza, amelyek nem rendelkeznek az interaktív bejelentkezést lehetővé tevő alapértelmezett jelszavakkal. nincs hátsó ajtó.                             |
| Biztonság                                                         | A tűzfalszabályok le vannak tiltva, kivéve, ha az alkalmazás működés közben nem támaszkodik rájuk, például egy tűzfal készülékre.                                                               |
| Biztonság                                                         | Minden bizalmas információ el lett távolítva a VHD-rendszerképből. Ilyenek például a fájlok, a naplófájlok és a szükségtelen tanúsítványok.                                              |
| Környezet                                                       | 64 bites operációs rendszer csak.                            |
