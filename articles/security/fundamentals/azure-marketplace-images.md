---
title: Biztonsági javaslatok az Azure Marketplace-lemezképekhez | Microsoft Docs
description: Ez a cikk a piacon található rendszerképekre vonatkozó ajánlásokat tartalmaz
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80548656"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Biztonsági javaslatok az Azure Marketplace-rendszerképekhez

A rendszerképnek meg kell felelnie ezeknek a biztonsági konfigurációs javaslatoknak. Ez segít megőrizni a partneri megoldások képeinek magas szintű biztonságát az Azure piactéren.

Az elküldés előtt mindig futtasson biztonsági sebezhetőségi észlelést a rendszerképen. Ha a saját közzétett rendszerképében biztonsági rést derít fel, akkor a biztonsági rést és annak kijavítását időben tájékoztatnia kell ügyfeleiről.

## <a name="open-source-based-images"></a>Nyílt forráskódú lemezképek

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategória**                                                 | **Jelölőnégyzet**                                                                                                                                                                                                                                                                              |
| Biztonság                                                     | Telepítse az összes legújabb biztonsági javítást a Linux-disztribúcióhoz.                                                                                                                                                                                                              |
| Biztonság                                                     | Az iparági irányelvek követésével biztonságossá teheti a virtuálisgép-rendszerképet az adott Linux-disztribúcióhoz.                                                                                                                                                                                     |
| Biztonság                                                     | Korlátozza a támadási felületet úgy, hogy a minimális helyigényt csak a szükséges Windows Server-szerepkörökkel, szolgáltatásokkal, szolgáltatásokkal és hálózati portokkal tartja.                                                                                                                                               |
| Biztonság                                                     | A forráskód és a létrejövő virtuálisgép-rendszerkép vizsgálata a kártevő szoftverhez.                                                                                                                                                                                                                                   |
| Biztonság                                                     | A VHD-rendszerkép csak olyan szükséges zárolt fiókokat tartalmaz, amelyek nem rendelkeznek az interaktív bejelentkezést lehetővé tevő alapértelmezett jelszóval. nincs hátsó ajtó.                                                                                                                                           |
| Biztonság                                                     | A tűzfalszabályok letiltása, ha az alkalmazás nem támaszkodik rájuk, például egy tűzfal készülékre.                                                                                                                                                                             |
| Biztonság                                                     | Távolítsa el az összes bizalmas információt a VHD-rendszerképből, például az SSH-kulcsokat, az ismert fájlokat, a naplófájlokat és a szükségtelen tanúsítványokat.                                                                                                                                       |
| Biztonság                                                     | Kerülje az LVM használatát.                                                                                                                                                                                                                                            |
| Biztonság                                                     | Adja meg a szükséges kódtárak legújabb verzióit: </br> -OpenSSL 1.0 vagy újabb verzió </br> -Python 2,5 vagy újabb (Python 2.6 + ajánlott) </br> -Python pyasn1-csomag, ha még nincs telepítve </br> -d. OpenSSL v 1,0 vagy újabb                                                                |
| Biztonság                                                     | A bash/shell History bejegyzéseinek törlése.                                                                                                                                                                                                                                             |
| Hálózatkezelés                                                   | Adja meg az SSH-kiszolgálót alapértelmezetten. Állítsa be az SSH-t életben az sshd-konfigurációba a következő beállítással: ClientAliveInterval 180.                                                                                                                                                        |
| Hálózatkezelés                                                   | Távolítsa el az egyéni hálózati konfigurációt a rendszerképből. Törölje a resolv. conf fájlt: `rm /etc/resolv.conf` .                                                                                                                                                                                |
| Üzembe helyezés                                                   | Telepítse a legújabb Azure Linux-ügynököt.</br> -Telepítés az RPM vagy a deb csomag használatával.  </br> – A manuális telepítési folyamat is használható, de a telepítő csomagok ajánlottak és előnyben részesítettek. </br> – Ha manuálisan telepíti az ügynököt a GitHub-tárházból, először másolja `waagent` `/usr/sbin` és futtassa a fájlt (root néven): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Az ügynök konfigurációs fájlját a rendszer a következő helyen helyezi el: `/etc/waagent.conf` . |
| Üzembe helyezés                                                   | Győződjön meg arról, hogy az Azure-támogatás szükség esetén a soros konzol kimenetével biztosíthatja partnereit, és megfelelő időkorlátot biztosít a Felhőbeli tároló operációsrendszer-lemez csatlakoztatásához. Adja hozzá a következő paramétereket a rendszerkép kernel rendszerindítási sorához: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` . |
| Üzembe helyezés                                                   | Nincs lapozófájl-partíció az operációsrendszer-lemezen. A felcserélés a Linux-ügynök által a helyi erőforrás lemezén történő létrehozáshoz kérhető le.         |
| Üzembe helyezés                                                   | Hozzon létre egyetlen legfelső szintű partíciót az operációsrendszer-lemez számára.      |
| Üzembe helyezés                                                   | 64 bites operációs rendszer csak.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-alapú rendszerképek

|||
|-------------| -------------------------|
| **Kategória**                                                     | **Jelölőnégyzet**                                                                                                                                                                |
| Biztonság                                                         | Használjon biztonságos operációsrendszer-alapképet. A Windows Serveren alapuló lemezképek forrásához használt virtuális merevlemeznek a Microsoft Azure által biztosított Windows Server operációsrendszer-lemezképből kell származnia. |
| Biztonság                                                         | Telepítse a legújabb biztonsági frissítéseket.                                                                                                                                     |
| Biztonság                                                         | Az alkalmazások nem lehetnek olyan korlátozott felhasználónevek, mint a rendszergazda, a gyökér vagy a rendszergazda.                                                                |
| Biztonság                                                         | Az operációs rendszer merevlemez-meghajtóinak és az adatmerevlemezek BitLocker meghajtótitkosítás engedélyezése.                                                             |
| Biztonság                                                         | Korlátozza a támadási felületet úgy, hogy minimális helyigényt biztosít, csak a szükséges Windows Server-szerepköröket, szolgáltatásokat, szolgáltatásokat és hálózati portokat engedélyezte.                         |
| Biztonság                                                         | A forráskód és a létrejövő virtuálisgép-rendszerkép vizsgálata a kártevő szoftverhez.                                                                                                                     |
| Biztonság                                                         | A Windows Server-lemezképek biztonsági frissítésének beállítása az automatikus frissítéshez.                                                                                                                |
| Biztonság                                                         | A VHD-rendszerkép csak olyan szükséges zárolt fiókokat tartalmaz, amelyek nem rendelkeznek az interaktív bejelentkezést lehetővé tevő alapértelmezett jelszóval. nincs hátsó ajtó.                             |
| Biztonság                                                         | A tűzfalszabályok letiltása, ha az alkalmazás nem támaszkodik rájuk, például egy tűzfal készülékre.                                                               |
| Biztonság                                                         | Távolítsa el az összes bizalmas információt a VHD-rendszerképből, beleértve a GAZDAGÉPek fájljait, a naplófájlokat és a szükségtelen tanúsítványokat.                                              |
| Üzembe helyezés                                                       | 64 bites operációs rendszer csak.                            |

Ha a szervezet nem rendelkezik rendszerképekkel az Azure Marketplace-en, érdemes megfontolnia a Windows-és Linux-lemezképek konfigurációjának ellenőrzését ezen javaslatok alapján.

## <a name="contacting-customers"></a>Kapcsolatfelvétel az ügyfelekkel

Ügyfelek és kapcsolattartási e-mailek azonosítása:

1.  Cloud Partner Portal a bal oldali vasúton **válassza az**eredmények elemet.
2.  A **megrendelések és használat** lapon a **kezdési dátum** és a **Befejezés dátuma** mezők használatával kérdezheti le a szükséges dátumtartományt a használaton belül. Ez azt mutatja, hogy mely Azure-előfizetések lettek felhasználva az ajánlathoz napi rendszerességgel. Exportálja ezeket az adatfájlokat. 
3.  Hasonlóképpen, az **ügyfél** lapon kérdezheti le és exportálhatja az ügyfél alapjait.
4.  A szükséges ügyfél-információk megkereséséhez a 2. lépésben szereplő előfizetés-azonosítót kell megegyeznie a 3. lépésben szereplő előfizetés-AZONOSÍTÓval.
