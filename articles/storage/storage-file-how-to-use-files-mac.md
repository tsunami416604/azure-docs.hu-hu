---
title: "Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren | Microsoft Docs"
description: "Itt megismerheti, hogyan csatlakoztathat Azure-fájlmegosztást az SMB protokoll segítségével macOS rendszeren."
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
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 8b1c9cbe88b7a78b3ef6e73c9da6d0a4c647f413
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---

# <a name="mount-azure-file-share-over-smb-with-macos"></a>Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren
Az [Azure File Storage](storage-dotnet-how-to-use-files.md) egy Microsoft-szolgáltatás, amely lehetővé teszi a hálózati fájlmegosztások Azure-ban történő létrehozását és használatát az iparági szabványok használatával. Az Azure-fájlmegosztások a macOS rendszer Sierra (10.12) és El Capitan (10.11) verzióra csatlakoztathatók. Ez a cikk kettő különböző módszert mutat be az Azure-fájlmegosztások csatlakoztatására macOS rendszeren: a Finder felhasználói felület és a Terminál használatával.

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
    ![A macOS arc ikonja](media/storage-file-how-to-use-files-mac/mount-via-finder-1.png)

2. **Válassza a „Go” (Ugrás) menü „Connect to Server” (Kapcsolódás a kiszolgálóhoz) lehetőségét**: Használja az [előfeltételekben](#preq) található UNC elérési útvonalat, alakítsa át az útvonal elején található dupla fordított perjelet (`\\`) az alábbira: `smb://`, és minden további fordított perjelet (`\`) perjelre (`/`). A hivatkozásnak az alábbi módon kell kinéznie: ![A „Connect to Server” (Kapcsolódás a kiszolgálóhoz) párbeszédablak](./media/storage-file-how-to-use-files-mac/mount-via-finder-2.png)

3. **Használja a megosztási nevet és a tárfiók kulcsát, amikor a rendszer felkéri a felhasználónév és jelszó megadására**: Ha a „Connect” (Kapcsolódás) lehetőségre kattint a „Connect to Server” (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen, a rendszer felkéri a felhasználónév és jelszó megadására (a rendszer automatikusan kitölti ezt a macOS-felhasználónevével). Lehetősége van arra, hogy hozzáadja a megosztási nevet/tárfiók-kulcsot a macOS-kulcskarikához.

4. **Használja az Azure-fájlmegosztást igény szerint**: Miután behelyettesítette a megosztási nevet és a tárfiók-kulcsot a felhasználónevére és jelszavára, a rendszer csatlakoztatja a megosztást. Ezt ugyanúgy használhatja, mint egy helyi mappát/fájlmegosztást, beleértve azt is, hogy fájlokat húzhat a fájlmegosztásba:

    ![Pillanatfelvétel egy csatlakoztatott Azure-fájlmegosztásról](./media/storage-file-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Azure-fájlmegosztás csatlakoztatása a Terminál segítségével
1. Cserélje le a `<storage-account-name>` kifejezést a tárfiókja nevére. Adja meg a tárfiók kulcsát, amikor a rendszer felkéri erre. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Használja az Azure-fájlmegosztást igény szerint**: Az Azure-fájlmegosztás az előző parancs által megadott csatlakoztatási ponton lesz csatlakoztatva.  

    ![Pillanatfelvétel a csatlakoztatott Azure-fájlmegosztásról](./media/storage-file-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure File Storage-ról.

* [Apple-támogatási cikk – Kapcsolódás a Mac fájlmegosztás funkciójának segítségével](https://support.apple.com/HT204445)
* [Gyakori kérdések](storage-files-faq.md)
* [hibaelhárítással](storage-troubleshoot-file-connection-problems.md)
