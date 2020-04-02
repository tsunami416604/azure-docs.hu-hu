---
title: Biztonsági javaslatok az Azure Marketplace-rendszerképekhez | Microsoft dokumentumok
description: Ez a cikk ajánlásokat tartalmaz a piacon található képekhez
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
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548656"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Biztonsági javaslatok az Azure Marketplace-lemezképekhez

A rendszerképnek meg kell felelnie ezeknek a biztonsági konfigurációs javaslatoknak. Ez segít fenntartani a magas szintű biztonságot a partnermegoldás-lemezképek számára az Azure Marketplace-en.

A beküldés előtt mindig futtasson biztonsági rést a lemezképen. Ha saját közzétett lemezképében biztonsági rést észlel, időben tájékoztatnia kell az ügyfeleket a biztonsági résről és annak javításáról.

## <a name="open-source-based-images"></a>Nyílt forráskódú képek

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategória**                                                 | **Jelölőnégyzet**                                                                                                                                                                                                                                                                              |
| Biztonság                                                     | Telepítse a legújabb biztonsági javításokat a Linux disztribúcióhoz.                                                                                                                                                                                                              |
| Biztonság                                                     | Kövesse az iparági irányelveket a virtuális gép rendszerképének biztonságossá tétele az adott Linux-disztribúcióhoz.                                                                                                                                                                                     |
| Biztonság                                                     | Korlátozza a támadási felületet azáltal, hogy minimális helyigényű, csak a szükséges Windows Server-szerepkörökkel, szolgáltatásokkal, szolgáltatásokkal és hálózati portokkal rendelkezik.                                                                                                                                               |
| Biztonság                                                     | A forráskód és az eredményül kapott virtuális géprendszer-rendszer rosszindulatú programok keresése.                                                                                                                                                                                                                                   |
| Biztonság                                                     | A virtuális merevlemez-lemezkép csak azokat a szükséges zárolt fiókokat tartalmazza, amelyek nem rendelkeznek olyan alapértelmezett jelszóval, amely lehetővé tenné az interaktív bejelentkezést; Nincs hátsó ajtó.                                                                                                                                           |
| Biztonság                                                     | Tiltsa le a tűzfalszabályokat, kivéve, ha az alkalmazás funkcionálisan támaszkodik rájuk, például egy tűzfalberendezésre.                                                                                                                                                                             |
| Biztonság                                                     | Távolítson el minden bizalmas információt a virtuális merevlemez-lemezképből, például tesztelje az SSH-kulcsokat, az ismert hosts fájlt, a naplófájlokat és a szükségtelen tanúsítványokat.                                                                                                                                       |
| Biztonság                                                     | Ne használja az LVM-et.                                                                                                                                                                                                                                            |
| Biztonság                                                     | Adja meg a szükséges könyvtárak legújabb verzióit: </br> - OpenSSL v1.0 vagy nagyobb </br> - Python 2.5 vagy újabb (Python 2.6+ erősen ajánlott) </br> - Python pyasn1 csomag, ha még nincs telepítve </br> - d.OpenSSL v 1.0 vagy nagyobb                                                                |
| Biztonság                                                     | Törölje a Bash/Shell előzménybejegyzéseit.                                                                                                                                                                                                                                             |
| Hálózat                                                   | Alapértelmezés szerint az SSH-kiszolgálót is tartalmazza. Állítsa sSH életben tartani, hogy sshd config a következő beállítással: ClientAliveInterval 180.                                                                                                                                                        |
| Hálózat                                                   | Távolítson el minden egyéni hálózati konfigurációt a lemezképből. A resolv.conf: `rm /etc/resolv.conf`fájl törlése.                                                                                                                                                                                |
| Környezet                                                   | Telepítse a legújabb Azure Linux-ügynök.</br> - Telepítse az RPM vagy Deb csomaggal.  </br> - Használhatja a kézi telepítési folyamatot is, de a telepítő csomagok ajánlottak és előnyben részesítendők. </br> - Ha az ügynököt manuálisan telepíti a GitHub-tárházból, először másolja a fájlt a `waagent` fájlba, `/usr/sbin` és fusson (gyökérként): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Az ügynök konfigurációs `/etc/waagent.conf`fájlja a rendszerre kerül. |
| Környezet                                                   | Győződjön meg arról, hogy az Azure-támogatás szükség esetén soros konzolkimenetet biztosít partnereiszámára, és megfelelő időbeli meghosszabbítást biztosít az operációs rendszer felhőalapú tárhelyről történő csatlakoztatásához. Adja hozzá a következő paramétereket a `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`kernel rendszerindító sorképéhez: . |
| Környezet                                                   | Nincs swap partíció az operációs rendszer lemezén. A felcserélést a Linux-ügynök kérheti a helyi erőforrás-lemezen való létrehozáshoz.         |
| Környezet                                                   | Hozzon létre egyetlen gyökérpartíciót az operációs rendszer lemezéhez.      |
| Környezet                                                   | Csak 64 bites operációs rendszer.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server alapú lemezképek

|||
|-------------| -------------------------|
| **Kategória**                                                     | **Jelölőnégyzet**                                                                                                                                                                |
| Biztonság                                                         | Használjon biztonságos operációsrendszer-alaplemezképet. A Windows Server rendszeren alapuló lemezképek forrásához használt virtuális merevlemeznek a Microsoft Azure-on keresztül biztosított Windows Server operációsrendszer-lemezképekből kell származnia. |
| Biztonság                                                         | Telepítse a legújabb biztonsági frissítéseket.                                                                                                                                     |
| Biztonság                                                         | Az alkalmazások nem függhetnek a korlátozott felhasználónevektől, például a rendszergazdától, a gyökértől vagy a rendszergazdától.                                                                |
| Biztonság                                                         | Engedélyezze a BitLocker meghajtótitkosítást az operációs rendszer merevlemezeihez és az adatmerevlemezekhez is.                                                             |
| Biztonság                                                         | Korlátozza a támadási felületet azáltal, hogy minimális helyigényű, csak a szükséges Windows Server-szerepkörökkel, szolgáltatásokkal, szolgáltatásokkal és hálózati portokkal rendelkezik.                         |
| Biztonság                                                         | A forráskód és az eredményül kapott virtuális géprendszer-rendszer rosszindulatú programok keresése.                                                                                                                     |
| Biztonság                                                         | Állítsa be a Windows Server rendszerképeinek biztonsági frissítését automatikus frissítésre.                                                                                                                |
| Biztonság                                                         | A virtuális merevlemez-lemezkép csak azokat a szükséges zárolt fiókokat tartalmazza, amelyek nem rendelkeznek olyan alapértelmezett jelszóval, amely lehetővé tenné az interaktív bejelentkezést; Nincs hátsó ajtó.                             |
| Biztonság                                                         | Tiltsa le a tűzfalszabályokat, kivéve, ha az alkalmazás funkcionálisan támaszkodik rájuk, például egy tűzfalberendezésre.                                                               |
| Biztonság                                                         | Távolítson el minden bizalmas információt a virtuális merevlemez-lemezképből, beleértve a HOSTS-fájlokat, a naplófájlokat és a szükségtelen tanúsítványokat.                                              |
| Környezet                                                       | Csak 64 bites operációs rendszer.                            |

Még akkor is, ha a szervezet nem rendelkezik lemezképek az Azure-piactéren, fontolja meg a Windows és a Linux rendszerkép konfigurációk alapján ezeket a javaslatokat.

## <a name="contacting-customers"></a>Ügyfelekkel való kapcsolatfelvétel

Az ügyfelek és kapcsolattartási e-mailjeik azonosítása:

1.  A Cloud Partner Portal bal oldali sínen válassza az **Insights**lehetőséget.
2.  A **Rendelések és a Kihasználat** lapon a **Kezdési dátum** és a **Záró dátum** mezővel lekérdezheti a szükséges dátumtartományon belüli felhasználást. Ez azt mutatja, hogy mely Azure-előfizetések használták az ajánlat napi rendszerességgel. Exportálja ezeket az adatokat. 
3.  Hasonlóképpen a **Vevő** lapon lekérdezheti és exportálhatja a vevőbázist.
4.  A szükséges ügyféladatok megkereséséhez a 2.
