---
title: "Azure-fájlmegosztás csatlakoztatása és a megosztás elérése Windows rendszeren | Microsoft Docs"
description: "Azure-fájlmegosztás csatlakoztatása és a megosztás elérése Windows rendszeren."
services: storage
documentationcenter: na
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
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: e911e787cd1e29b2bbeaa648869c50245f2dd9ba
ms.contentlocale: hu-hu
ms.lasthandoff: 07/22/2017

---

# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Azure-fájlmegosztás csatlakoztatása és a megosztás elérése Windows rendszeren
Az [Azure-fájlmegosztás](storage-dotnet-how-to-use-files.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások Windows és Windows Server rendszeren csatlakoztathatók. Ez a cikk három különböző módszert mutat be az Azure-fájlmegosztások csatlakoztatására Windows rendszeren: a Fájlkezelő felhasználói felület, a PowerShell és a parancssor használatával. 

Ha egy Azure-fájlmegosztást az üzemeltető Azure-régión kívül kíván csatlakoztatni, például a helyszínen vagy más Azure-régióban, az operációs rendszernek támogatnia kell az SMB 3.0-s verziót. 

Az Azure-fájlmegosztás az operációs rendszer verziójától függően egy helyszíni vagy Azure-beli virtuális gépen lévő Windows-gépen csatlakoztatható. Az alábbi tábla a következőket mutatja be: 

| Windows-verzió        | SMB-verzió |Azure-beli virtuális gépen csatlakoztatható|Helyszínen csatlakoztatható|
|------------------------|-------------|---------------------|---------------------|
| Windows 7              | SMB 2.1     | Igen                 | Nem                  |
| Windows Server 2008 R2 | SMB 2.1     | Igen                 | Nem                  |
| Windows 8              | SMB 3.0     | Igen                 | Igen                 |
| Windows Server 2012    | SMB 3.0     | Igen                 | Igen                 |
| Windows Server 2012 R2 | SMB 3.0     | Igen                 | Igen                 |
| Windows 10             | SMB 3.0     | Igen                 | Igen                 |

> [!Note]  
> Javasoljuk, hogy mindig a Windows-verziójához legutóbb kiadott frissítést használja.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Az Azure-fájlmegosztások Windowson történő csatlakoztatásának előfeltételei 
* **Tárfiók neve**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz a tárfiók nevére.

* **Tárfiók kulcsa**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz az elsődleges (vagy másodlagos) tárkulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, hogy a 445-ös port nyitva van**: Az Azure File Storage SMB protokollt használ. Az SMB a 445-ös TCP-porton keresztül kommunikál – ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös TCP-portot az ügyfél gépéről.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Az Azure-fájlmegosztás csatlakoztatása a Fájlkezelővel
> [!Note]  
> Vegye figyelembe, hogy a következő példák a Windows 10-re vonatkoznak, és a régebbi kiadásokban eltérhetnek. 

1. **Nyissa meg a Fájlkezelőt**: Ezt a Start menüből vagy a Win+E billentyűkombináció lenyomásával teheti meg.

2. **Keresse meg az Ez a gép elemet az ablak bal oldalán. Ez módosítja a szalagon elérhető menüket. A Számítógép menüben válassza a Hálózati meghajtó csatlakoztatása**  elemet.
    
    ![A Hálózati meghajtó csatlakoztatása legördülő menü képernyőképe](media/storage-file-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Másolja az Azure Portal Csatlakozás ablaktábláján található UNC-útvonalat**: Az információ megkereséséről [itt](storage-file-how-to-use-files-portal.md#connect-to-file-share) talál részletes információt.

    ![Az UNC-útvonal az Azure File Storage Csatlakozás oldaláról](media/storage-file-how-to-use-files-windows/portal_netuse_connect.png)

4. **Válassza ki a meghajtó betűjelét, és írja be az UNC-útvonalat.** 
    
    ![A Hálózati meghajtó csatlakoztatása párbeszédpanel képernyőképe](media/storage-file-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Felhasználónévként használja a tárfiók `Azure\` előtaggal kiegészített nevét, jelszóként pedig egy tárfiókkulcsot.**
    
    ![A hálózati hitelesítő adatok párbeszédpanelének képernyőképe](media/storage-file-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Használja az Azure-fájlmegosztást igény szerint**.
    
    ![Az Azure-fájlmegosztás most már csatlakoztatva van](media/storage-file-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Amikor készen áll az Azure-fájlmegosztás leválasztására, kattintson a jobb gombbal a megosztás bejegyzésére a Fájlkezelő Hálózati helyek területén, és válassza a Leválasztás parancsot**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Az Azure-fájlmegosztás csatlakoztatása a PowerShell-lel
1. **Az alábbi parancs használatával csatlakoztathatja az Azure-fájlmegosztást**. Ne feledje el kicserélni az alábbi adatokat a megfelelő értékekre: `<storage-account-name>`, `<share-name>`, `<storage-account-key>` és `<desired-drive-letter>`.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Használja az Azure-fájlmegosztást igény szerint**.

3. **Ha végzett, válassza le az Azure-fájlmegosztást az alábbi parancs használatával**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> A `New-PSDrive` paranccsal és a `-Persist` paraméterrel teheti láthatóvá az Azure-fájlmegosztást az operációs rendszer többi részének, amíg csatlakoztatva van.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Az Azure-fájlmegosztás csatlakoztatása a parancssorral
1. **Az alábbi parancs használatával csatlakoztathatja az Azure-fájlmegosztást**. Ne feledje el kicserélni az alábbi adatokat a megfelelő értékekre: `<storage-account-name>`, `<share-name>`, `<storage-account-key>` és `<desired-drive-letter>`.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Használja az Azure-fájlmegosztást igény szerint**.

3. **Ha végzett, válassza le az Azure-fájlmegosztást az alábbi parancs használatával**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> A hitelesítő adatok Windowsban való megőrzésével úgy is konfigurálhatja az Azure-fájlmegosztást, hogy automatikusan újracsatlakozzon újraindítás esetén. Az alábbi parancs használatával őrizheti meg a hitelesítő adatokat:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure File Storage-ról.

* [Gyakori kérdések](storage-files-faq.md)
* [hibaelhárítással](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>Elméleti cikkek és videók
* [Azure File Storage: zökkenőmentes felhőalapú SMB fájlrendszer Windows és Linux rendszerekhez](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Az Azure File Storage használata Linuxszal](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-file-storage"></a>Azure File Storage-eszköztámogatás
* [Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)](storage-powershell-guide-full.md)
* [How to use AzCopy with Microsoft Azure Storage (Az AzCopy használata a Microsoft Azure Storage szolgáltatással)](storage-use-azcopy.md)
* [Using the Azure CLI with Azure Storage (Az Azure parancssori felülete és az Azure Storage együttes használata)](storage-azure-cli.md#create-and-manage-file-shares)
* [Az Azure File Storage-problémák hibaelhárítása](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="blog-posts"></a>Blogbejegyzések
* [Azure File storage is now generally available (Mostantól általánosan elérhető az Azure File Storage)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Az Azure File Storage ismertetése](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Adatok áttelepítése Azure File szolgáltatásba ](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referencia
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia a fájlszolgáltatás REST API-jához](http://msdn.microsoft.com/library/azure/dn167006.aspx)

