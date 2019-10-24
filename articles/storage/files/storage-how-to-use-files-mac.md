---
title: Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren | Microsoft Docs
description: Itt megismerheti, hogyan csatlakoztathat Azure-fájlmegosztást az SMB protokoll segítségével macOS rendszeren.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bd696ce5a314b0c849256311d0629b917036ea2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699546"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások a macOS rendszer El Capitan 10.11-es vagy újabb verziójában elérhető, iparági szabványnak számító SMB 3 protokollal csatlakoztathatók. Ez a cikk két különböző módszert mutat be az Azure-fájlmegosztások macOS rendszeren történő csatlakoztatására: a Finder felhasználói felület és a Terminál használatát.

> [!Note]  
> Az Azure-fájlmegosztás SMB protokollon keresztül történő csatlakoztatása előtt javasoljuk, hogy tiltsa le az SMB-csomagaláírást. Ha ezt nem teszi meg, az gyenge teljesítményt eredményezhet, amikor macOS rendszerről próbál meg hozzáférni az Azure-fájlmegosztáshoz. Az SMB-kapcsolat titkosítva lesz, tehát ez nem befolyásolja a kapcsolat biztonságát. A terminálból az alábbi parancsok segítségével tilthatja le az SMB-csomagaláírást, ahogyan azt ez [az SMB-csomagaláírás letiltásával foglalkozó Apple-támogatási cikk](https://support.apple.com/HT205926) is ismerteti:  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Az Azure-fájlmegosztások macOS rendszerre történő csatlakoztatásának előfeltételei
* **Storage-fiók neve**: Azure-fájlmegosztás csatlakoztatásához szüksége lesz a Storage-fiók nevére.

* **Storage-fiók kulcsa**: Azure-fájlmegosztás csatlakoztatásához szüksége lesz az elsődleges (vagy másodlagos) tárolási kulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, hogy a 445-es port nyitva van**: Az SMB protokoll a 445-ös TCP porton keresztül kommunikál. Az ügyfélgépen (Mac) ellenőrizze, és győződjön meg arról, hogy a tűzfal nem blokkolja a 445-ös TCP portot.

## <a name="mount-an-azure-file-share-via-finder"></a>Azure-fájlmegosztás csatlakoztatása a Finder segítségével
1. A **Finder megnyitása**: A Finder alapértelmezés szerint meg van nyitva macOS rendszeren, de a Dockon a "macOS Face ikon" elemre kattintva biztos lehet benne, hogy a jelenleg kiválasztott alkalmazás.  
    ![A macOS arc ikonja](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Válassza a "kapcsolódás a kiszolgálóhoz" lehetőséget a "Go" menüben**: Az előfeltételekből származó UNC elérési út használatával alakítsa át a dupla fordított`\\`perjelet `smb://` ()`\`és az összes többi fordított perjelet ()`/`a perjel () továbbítására. A hivatkozásnak a következőhöz hasonlóan kell kinéznie: ![A "kapcsolódás a kiszolgálóhoz" párbeszédpanel](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Ha a rendszer a Felhasználónév és a jelszó megadását kéri, használja a Storage-fiók nevét és a Storage-fiók kulcsát**: Ha a "kapcsolódás a kiszolgálóhoz" párbeszédpanelen a "Csatlakozás" gombra kattint, a rendszer kérni fogja a felhasználónevet és a jelszót (ezt a rendszer automatikusan feltölti a macOS-felhasználónevével). Lehetősége van arra, hogy hozzáadja a tárfiók nevét és kulcsát a macOS-kulcskarikához.

4. **Használja az Azure-fájlmegosztást igény szerint**: A megosztási név és a Storage-fiók kulcsának a felhasználónévvel és a jelszóval való helyettesítése után a megosztás csatlakoztatva lesz. Ezt ugyanúgy használhatja, mint egy helyi mappát/fájlmegosztást, beleértve azt is, hogy fájlokat húzhat a fájlmegosztásba:

    ![Pillanatfelvétel egy csatlakoztatott Azure-fájlmegosztásról](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Azure-fájlmegosztás csatlakoztatása a Terminál segítségével
1. Cserélje `<storage-account-name>`le a nevet a Storage-fiók nevére. Adja meg a tárfiók kulcsát, amikor a rendszer felkéri erre. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Használja az Azure-fájlmegosztást igény szerint**: Az Azure-fájlmegosztás az előző parancs által megadott csatlakoztatási pontra lesz csatlakoztatva.  

    ![Pillanatfelvétel a csatlakoztatott Azure-fájlmegosztásról](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [Apple-támogatási cikk – Kapcsolódás a Mac fájlmegosztás funkciójának segítségével](https://support.apple.com/HT204445)
* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)    
