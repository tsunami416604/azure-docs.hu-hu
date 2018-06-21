---
title: Bevezetés az Azure Files használatába | Microsoft Docs
description: Az Azure Files szolgáltatás áttekintése, amely lehetővé teszi hálózati fájlmegosztások létrehozását és használatát a felhőben az iparági szabvány SMB protokoll használatával.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: renash
ms.openlocfilehash: 83096e15b26b0edfd47df36fe368fd5c9c846f12
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737282"
---
# <a name="introduction-to-azure-files"></a>Bevezetés az Azure Files használatába
Az Azure Files teljes körűen felügyelt felhőbeli fájlmegosztást nyújt, amely az iparági szabványos [Server Message Block (SMB) protokollon](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) keresztül érhető el. Az Azure-fájlmegosztások párhuzamosan több felhőalapú vagy helyszíni Windows, Linux vagy macOS rendszerű üzemelő példány által is csatlakoztathatóak. Emellett az Azure-fájlmegosztások gyorsítótárazhatók Windows Servereken az Azure File Sync (előzetes verzió) használatával az adatok felhasználási helyéhez közeli gyors elérés érdekében.

## <a name="videos"></a>Videók
| Az Azure File Sync bemutatása (2 perc) | Azure Files Sync-kel (Ignite 2017) (85 perc)  |
|-|-|
| [![A Bevezetés az Azure File Sync használatába című videó képernyőképe – kattintson a lejátszáshoz!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Az Azure Files Sync-kel bemutató képernyőképe – kattintson a lejátszáshoz!](./media/storage-files-introduction/azure-files-ignite-2017-video.png)](https://www.youtube.com/watch?v=r26jWDGF_rg) |

## <a name="why-azure-files-is-useful"></a>Miért hasznos az Azure Files?
Az Azure-fájlmegosztások az alábbiakra használhatók:

* **Helyszíni fájlkiszolgálók lecserélése vagy kiegészítése**:  
    Az Azure Files használatával teljes mértékben lecserélheti vagy kiegészítheti a hagyományos helyszíni fájlkiszolgálókat vagy NAS-eszközöket. Az Azure-fájlmegosztások közvetlenül csatlakoztathatók a népszerű operációs rendszerekhez (például Windows, macOS és Linux) a földrajzi helyüktől függetlenül. Az Azure-fájlmegosztások az Azure File Sync használatával replikálhatók helyszíni vagy felhőalapú Windows Serverekre, az adatok a használat helyéhez közeli nagy teljesítményű és elosztott gyorsítótárazása érdekében.

* **Alkalmazások „átemelése”**:  
    Az Azure Files segítségével könnyedén „átemelhetők” azok az alkalmazások a felhőbe, amelyekhez fájlmegosztás szükséges a fájlalkalmazások és a felhasználók adatainak tárolásához. Az Azure Files az alkalmazást és az adatokat egyaránt az Azure-ba áthelyező „klasszikus”, és az adatokat az Azure Filesba áthelyező és az alkalmazást továbbra is a helyszíni környezetben futtató „hibrid” átemelési forgatókönyvet is támogatja. 

* **A felhőalapú fejlesztés egyszerűsítése**:  
    Az Azure Files emellett számos különféle módon segítheti az új felhőalapú fejlesztési projektek egyszerűbbé tételét. Például:
    * **Megosztott alkalmazásbeállítások**:  
        Az elosztott alkalmazások egyik jellegzetessége, hogy egy központi helyen található a konfigurációs fájljuk, ahol számos alkalmazáspéldány hozzáférhet. Az alkalmazáspéldányok a konfigurációjukat a Fájl REST API-val tudják betölteni, és a felhasználók az SMB-megosztások helyi csatlakoztatásával érhetik el őket igény szerint.

    * **Diagnosztika megosztása**:  
        Az Azure-fájlmegosztások kényelmes helyet biztosítanak a felhőalkalmazások számára a naplók, a mérőszámok és az összeomlási memóriaképek írásához. Az alkalmazáspéldányok a Fájl REST API-val írhatják a naplókat, a fejlesztők pedig a fájlmegosztás a helyi gépükön való csatlakoztatásával érhetik el őket. Ez nagy rugalmasságot biztosít, mivel a fejlesztők anélkül használhatják ki a felhő előnyeit, hogy le kellene mondaniuk az általuk használt és kedvelt eszközökről.

    * **Fejlesztés/Tesztelés/Hibakeresés**:  
        Amikor a fejlesztők vagy rendszergazdák a felhőben végeznek munkálatokat a virtuális gépen, gyakran van szükségük ugyanazokra az eszközökre vagy segédprogramokra. Az ilyen segédprogramok és eszközök az egyes virtuális gépekre másolása időigényes feladat. Az Azure-fájlmegosztások a virtuális gépekre történő helyi csatlakoztatásával a fejlesztők és a rendszergazdák gyorsan hozzáférhetnek eszközeikhez és segédprogramjaikhoz másolás nélkül.

## <a name="key-benefits"></a>Főbb előnyök
* **Megosztott hozzáférés**. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, ezért zökkenőmentesen lecserélheti helyszíni fájlmegosztásait Azure-fájlmegosztásokra, és nem kell aggódnia az alkalmazások kompatibilitása miatt. A fájlrendszerek több gépen, alkalmazásban/példányban történő megosztásának lehetősége az Azure Files egyik jelentős előnye a megoszthatóságot igénylő alkalmazások esetében. 
* **Teljes mértékű felügyelet**. Az Azure-fájlmegosztások anélkül is létrehozhatók, hogy felügyelnie kellene a hardvereket vagy az operációs rendszert. Ez azt jelenti, hogy nincs szükség a kiszolgáló operációs rendszerének kritikus fontosságú frissítésekkel történő javítására vagy a hibás merevlemezek cseréjére.
* **Szkripthasználat és eszköztámogatás**. A PowerShell-parancsmagok és az Azure CLI használatával az Azure-fájlmegosztásokat az Azure-alkalmazások felügyeletének részeként hozhatja létre, csatlakoztathatja és kezelheti. Az Azure-fájlmegosztásokat az Azure Portalról és az Azure Storage Explorerrel hozhatja létre és kezelheti. 
* **Rugalmasság**. Az Azure Files szolgáltatást a kezdetektől úgy hozták létre, hogy folyamatosan rendelkezésre álljon. Ha lecseréli helyszíni fájlmegosztásait az Azure Filesra, akkor többé nem kell helyi áramkimaradásokkal vagy hálózati hibákkal megküzdenie. 
* **Már ismerős programozhatóság**. Az Azure-ban futó alkalmazások a [fájlrendszer adatátviteli API-jain](https://msdn.microsoft.com/library/system.io.file.aspx) keresztül férhetnek hozzá a megosztás adataihoz. A fejlesztők épp ezért meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. A rendszer átviteli API-jai mellett az [Azure Storage ügyfélkódtárait](https://msdn.microsoft.com/library/azure/dn261237.aspx) vagy az [Azure Storage REST API-ját](/rest/api/storageservices/file-service-rest-api) is használhatja.

## <a name="next-steps"></a>További lépések
* [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)
* [Csatlakoztatás Windows rendszeren](storage-how-to-use-files-windows.md)
* [Csatlakoztatás Linux rendszeren](storage-how-to-use-files-linux.md)
* [Csatlakoztatás macOS rendszeren](storage-how-to-use-files-mac.md)
