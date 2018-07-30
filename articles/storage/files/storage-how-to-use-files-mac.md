---
title: Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren | Microsoft Docs
description: Itt megismerheti, hogyan csatlakoztathat Azure-fájlmegosztást az SMB protokoll segítségével macOS rendszeren.
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
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: eaa59e0568a6fc6ac9c867c6f05b7bfb22b71055
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206511"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren
Az [Azure Files](storage-files-introduction.md) egy Microsoft-szolgáltatás, amely lehetővé teszi a hálózati fájlmegosztások Azure-ban történő létrehozását és használatát az iparági szabványok használatával. Az Azure-fájlmegosztások a macOS rendszer El Capitan 10.11-es vagy újabb verzióira csatlakoztathatók. Ez a cikk két különböző módszert mutat be az Azure-fájlmegosztások csatlakoztatására macOS rendszeren: a Finder felhasználói felület és a Terminál használatával.

> [!Note]  
> Az Azure-fájlmegosztás SMB protokollon keresztül történő csatlakoztatása előtt javasoljuk, hogy tiltsa le az SMB-csomagaláírást. Ha ezt nem teszi meg, az gyenge teljesítményt eredményezhet, amikor macOS rendszerről próbál meg hozzáférni az Azure-fájlmegosztáshoz. Az SMB-kapcsolat titkosítva lesz, tehát ez nem befolyásolja a kapcsolat biztonságát. A terminálból az alábbi parancsok segítségével tilthatja le az SMB-csomagaláírást, ahogyan azt ez [az SMB-csomagaláírás letiltásával foglalkozó Apple-támogatási cikk](https://support.apple.com/HT205926) is ismerteti:  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Az Azure-fájlmegosztások macOS rendszerre történő csatlakoztatásának előfeltételei
* **Tárfiók neve**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz a tárfiók nevére.

* **Tárfiók kulcsa**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz az elsődleges (vagy másodlagos) tárkulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, hogy a 445-ös port nyitva legyen**: Az SMB protokoll a 445-ös TCP porton keresztül kommunikál. Az ügyfélgépen (Mac) ellenőrizze, és győződjön meg arról, hogy a tűzfal nem blokkolja a 445-ös TCP portot.

## <a name="mount-an-azure-file-share-via-finder"></a>Azure-fájlmegosztás csatlakoztatása a Finder segítségével
1. **Nyissa meg a Findert**: A Finder alapértelmezés szerint meg van nyitva a macOS rendszereken, de biztosan megnyithatja, ha az aktuálisan kiválasztott alkalmazásban a „macOS arc ikonra” kattint a Dockban:  
    ![A macOS arc ikonja](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Válassza a „Go” (Ugrás) menü „Connect to Server” (Kapcsolódás a kiszolgálóhoz) lehetőségét**: Használja az [előfeltételekben](#preq) található UNC elérési útvonalat, alakítsa át az útvonal elején található dupla fordított perjelet (`\\`) az alábbira: `smb://`, és minden további fordított perjelet (`\`) perjelre (`/`). A hivatkozásnak az alábbi módon kell kinéznie: ![A „Connect to Server” (Kapcsolódás a kiszolgálóhoz) párbeszédablak](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Használja a tárfiók nevét és kulcsát, amikor a rendszer felkéri a felhasználónév és a jelszó megadására**: Ha a „Connect” (Kapcsolódás) elemre kattint a „Connect to Server” (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen, a rendszer felkéri a felhasználónév és a jelszó megadására (a rendszer automatikusan kitölti ezt a macOS-felhasználónevével). Lehetősége van arra, hogy hozzáadja a tárfiók nevét és kulcsát a macOS-kulcskarikához.

4. **Használja az Azure-fájlmegosztást igény szerint**: Miután behelyettesítette a megosztási nevet és a tárfiókkulcsot a felhasználónevére és jelszavára, a rendszer csatlakoztatja a megosztást. Ezt ugyanúgy használhatja, mint egy helyi mappát/fájlmegosztást, beleértve azt is, hogy fájlokat húzhat a fájlmegosztásba:

    ![Pillanatfelvétel egy csatlakoztatott Azure-fájlmegosztásról](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Azure-fájlmegosztás csatlakoztatása a Terminál segítségével
1. Cserélje le a `<storage-account-name>` kifejezést a tárfiókja nevére. Adja meg a tárfiók kulcsát, amikor a rendszer felkéri erre. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Használja az Azure-fájlmegosztást igény szerint**: Az Azure-fájlmegosztás az előző parancs által megadott csatlakoztatási ponton lesz csatlakoztatva.  

    ![Pillanatfelvétel a csatlakoztatott Azure-fájlmegosztásról](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [Apple-támogatási cikk – Kapcsolódás a Mac fájlmegosztás funkciójának segítségével](https://support.apple.com/HT204445)
* [GYIK](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)    
