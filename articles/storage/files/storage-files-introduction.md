---
title: "Bevezetés az Azure File Storage használatába | Microsoft Docs"
description: "Bevezetés a Microsoft Cloud szolgáltatásban hálózati fájlmegosztásokat biztosító Azure File Storage használatába."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 498af5cffb76e026c9a87127cab238f0f23b668a
ms.contentlocale: hu-hu
ms.lasthandoff: 08/21/2017

---

# <a name="introduction-to-azure-file-storage"></a>Bevezetés az Azure File Storage használatába

Az Azure File Storage egy felhőalapú hálózati fájlmegosztást kínáló, az iparági szabvány [Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) protokollt és a [Common Internet File System (CIFS)](https://technet.microsoft.com/library/cc939973.aspx) fájlrendszert használó szolgáltatás. Az Azure-fájlmegosztások párhuzamosan csatlakoztathatók az Azure Virtual Machines és a Windows, macOS vagy Linux rendszerek helyszíni üzemelő példányai által. Az általános célú tárfiókok Azure File Storage, Azure Blob Storage és Azure Queue Storage tárolókhoz biztosítanak hozzáférést.

## <a name="videos"></a>Videók
| Az Azure File Storage ismertetése (27 perc) | Az Azure File Storage oktatóanyaga (5 perc)  |
|-|-|
| [![Az Azure File Storage ismertetése című videó képernyőképe – kattintson a lejátszáshoz!](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Az Azure File Storage oktatóanyaga című videó képernyőképe – kattintson a lejátszáshoz!](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Miért hasznos az Azure File Storage?

Az Azure File Storage segítségével lecserélheti a helyszínen vagy a felhőben tárolt Windows Server, Linux vagy NAS-alapú fájlkiszolgálókat egy operációs rendszert nem igénylő felhőalapú fájlmegosztásra. Az Azure File Storage az alábbi előnyöket biztosítja:

* **Megosztott hozzáférés** Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, ezért zökkenőmentesen lecserélheti helyszíni fájlmegosztásait Azure-fájlmegosztásokra, és nem kell aggódnia az alkalmazások kompatibilitása miatt. A fájlmegosztások több gépről, alkalmazásból/példányból való elérésének lehetősége az Azure File Storage egyik jelentős előnye.

* **Teljes körűen felügyelt** Az Azure-fájlmegosztások anélkül is létrehozhatók, hogy felügyelnie kellene a hardvereket vagy az operációs rendszert. Ez azt jelenti, hogy nincs szükség a kiszolgáló operációs rendszerének kritikus fontosságú frissítésekkel történő javítására vagy a hibás merevlemezek cseréjére.

* **Szkripthasználat és eszköztámogatás** A PowerShell-parancsmagok és az Azure parancssori felület (CLI) használatával az Azure File-megosztásokat az Azure-alkalmazások felügyeletének részeként hozhatja létre, csatlakoztathatja és kezelheti. Azure-fájlmegosztásokat az [Azure Portal](https://portal.azure.com) és az [Azure Storage Explorer](https://storageexplorer.com) használatával hozhat létre és kezelhet. 

* **Rugalmasság** Az Azure File Storage szolgáltatást a kezdetektől úgy hozták létre, hogy mindig rendelkezésre álljon. Ha lecseréli helyszíni fájlmegosztásait az Azure File Storage szolgáltatásra, akkor többé nem kell helyi áramkimaradásokkal vagy hálózati hibákkal megküzdenie. 

* **Ismerős programozhatóság** Az Azure-ban futó alkalmazások [a fájlrendszer adatátviteli API-jain](https://msdn.microsoft.com/library/system.io.file.aspx) keresztül férhetnek hozzá a megosztás adataihoz. A fejlesztők épp ezért meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. A rendszer átviteli API-jai mellett az Azure Storage bármelyik ügyfélkódtárát (például a [.NET-hez](/dotnet/api/overview/azure/storage?view=azure-dotnet) elérhetőt) vagy az [Azure Storage REST API-ját](/rest/api/storageservices/file-service-rest-api) is használhatja.

Az Azure-fájlmegosztások az alábbiakra használhatók:

* **Helyszíni fájlkiszolgálók lecserélése** Az Azure File Storage használatával teljes mértékben lecserélheti a hagyományos helyszíni fájlkiszolgálókon vagy NAS-eszközökön található fájlmegosztásokat. Az Azure-fájlmegosztások könnyedén csatlakoztathatók a népszerű operációs rendszerekre (például Windows, macOS és Linux), a földrajzi helyüktől függetlenül.

* **Alkalmazások „átemelése”**

    Az Azure File Storage segítségével könnyedén „átemelhetők” az alkalmazások a helyszíni fájlmegosztásokat használó felhőbe, így egyszerűen megoszthatók az adatok az alkalmazás összetevői között. Ennek megvalósítása érdekében minden virtuális gép csatlakozik a fájlmegosztáshoz, és ezután ugyanúgy képes a fájlok olvasására és írására, mint a helyszíni fájlmegosztások esetében.

* **A felhőalapú fejlesztés egyszerűsítése**
    
    Az Azure File Storage számos különböző módon segítheti az új felhőalapú fejlesztési projektek egyszerűbbé tételét.
    
    * **Megosztott alkalmazásbeállítások**
    
        Az elosztott alkalmazások egyik jellegzetessége, hogy egy központi helyen található a konfigurációs fájljuk, ahol a számos különböző virtuális gép hozzáférhet. Az ilyen konfigurációs fájlok mostantól az Azure-fájlmegosztásokban is tárolhatók, és az összes alkalmazáspéldány által olvashatók. Ezek a beállítások a REST-felületen keresztül is kezelhetők, ami globális hozzáférést tesz lehetővé a konfigurációs fájlokhoz.

    * **Diagnosztika megosztása**
    
        Az Azure-fájlmegosztások a diagnosztikai fájlok (például naplók, mérőszámok és összeomlási memóriaképek) mentésére is használhatók. Az, hogy ezek a fájlmegosztások az SMB protokollon és a REST-felületen keresztül is elérhetők, lehetővé teszi az alkalmazások számára, hogy különféle elemzési eszközöket hozzanak létre vagy használjanak fel a diagnosztikai adatok feldolgozásához és elemzéséhez.

    * **Fejlesztés/Tesztelés/Hibakeresés**

        Amikor a fejlesztők vagy rendszergazdák a felhőben végeznek munkálatokat a virtuális gépen, gyakran van szükségük ugyanazokra az eszközökre vagy segédprogramokra. Az ilyen segédprogramok telepítése és kiosztása az egyes virtuális gépekre, ahol éppen szükség van rájuk, sok időt vehet igénybe. Az Azure File Storage szolgáltatással a fejlesztők vagy rendszergazdák a leggyakrabban használt eszközeiket tárolhatják egy fájlmegosztásban, amely könnyedén csatlakoztatható bármely virtuális gépről.
        
## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure-fájlmegosztások kezelése sokkal egyszerűbb, mint a helyszíni fájlmegosztások esetében. Az alábbi ábra az Azure File Storage kezelési szerkezetét ábrázolja:

![Fájlstruktúra](./media/storage-files-introduction/files-concepts.png)

* **Storage-fiók** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Méretezhetőségi és teljesítménycélok).

* **Megosztás** A File Storage-megosztás egy SMB-fájlmegosztás az Azure-ban. Minden könyvtárnak és fájlnak egy szülőmegosztásban kell létrejönnie. Egy fiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a fájlmegosztás maximális 5 TB-os kapacitásáig.

* **Könyvtár** Egy választható könyvtár-hierarchia.

* **Fájl** A megosztásban található fájl. Egy fájl akár 1 TB méretű is lehet.

* **URL-formátum** A fájlok az alábbi URL-formátummal érhetők el:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```

## <a name="next-steps"></a>Következő lépések

* [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)
* [Csatlakoztatás Windows rendszeren](storage-how-to-use-files-windows.md)
* [Csatlakoztatás Linux rendszeren](storage-how-to-use-files-linux.md)
* [Csatlakoztatás macOS rendszeren](storage-how-to-use-files-mac.md)
* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)   
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)   

<!-- Rena I would remove any articles from here that are more than a year old. - Robin-->
### <a name="conceptual-articles-and-videos"></a>Elméleti cikkek és videók
* [Azure File Storage: zökkenőmentes felhőalapú SMB fájlrendszer Windows és Linux rendszerekhez](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Azure File Storage-eszköztámogatás
* [How to use AzCopy with Microsoft Azure Storage (Az AzCopy használata a Microsoft Azure Storage szolgáltatással)](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Using the Azure CLI with Azure Storage (Az Azure parancssori felülete és az Azure Storage együttes használata)](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)

### <a name="blog-posts"></a>Blogbejegyzések
* [Azure File storage is now generally available (Mostantól általánosan elérhető az Azure File Storage)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Az Azure File Storage ismertetése](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Adatok áttelepítése Azure File szolgáltatásba ](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referencia
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia a fájlszolgáltatás REST API-jához](http://msdn.microsoft.com/library/azure/dn167006.aspx)

