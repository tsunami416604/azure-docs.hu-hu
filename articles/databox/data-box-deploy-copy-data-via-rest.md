---
title: Adatok másolása az Azure Data Box Blob storage REST API-kon keresztül |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Data Box Blob storage REST API-kon keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 85c9eeb60027ba44fd5e65d056864aa76a6e720a
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900884"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Oktatóanyag: Adatok másolása az Azure Data Box Blob storage REST API-kon keresztül  

Ez az oktatóanyag leírja keresztül csatlakozni az Azure Data Box Blob storage REST API-kon keresztül eljárások *http* vagy *https*. A csatlakozás után az adatok másolása az adatok be Blob storage, és készítse elő szállításra, a Data Box szükséges lépéseket is ismerteti.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek
> * Data Box Blob storage-n keresztül kapcsolódni *http* vagy *https*
> * Adatok másolása a Data Boxra

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ön teljesítette a [oktatóanyag: Állítsa be az Azure Data Box](data-box-deploy-set-up.md).
2. A Data Box kapott, és a rendelés állapota a portálon **kézbesítések**.
3. Áttekintette a [Data Box Blob storage rendszerkövetelményei](data-box-system-requirements-rest.md) , és ismeri az API-k, SDK-k és eszközök támogatott verzióival.
4. Megismerte a gazdaszámítógép másolja át a Data Box kívánt adatokat a hozzáférést. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 10-GbE kapcsolatot nem érhető el, ha egy 1-GbE adatkapcsolat is használható, de a másolási sebességek érinti.
5. [Töltse le az AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) a gazdaszámítógépen. Adatok másolása az Azure Data Box Blob storage a gazdaszámítógépet az AzCopy fogja használni.


## <a name="connect-to-data-box-blob-storage"></a>Csatlakozás a Data Box blobtárolóba

Data Box Blob storage keresztül kapcsolódhat *http* vagy *https*. Általánosságban véve *https* csatlakozhat a Data Box Blob storage és ajánlott módja. *Http* használatos, amikor keresztül csatlakozó megbízható hálózatok. Attól függően, hogy kapcsolódik a Data Box Blob storage keresztül *http* vagy *https*, a lépések eltérő lehet.

## <a name="connect-via-http"></a>Kapcsolódás http-n keresztül

A Data Box Blob storage REST API-k kapcsolati keresztül *http* a következő lépéseket:

- Az eszköz IP-cím hozzáadása és a blob-szolgáltatásvégpont a távoli gazdagéphez
- Harmadik féltől származó szoftverek beállítása és a kapcsolat ellenőrzése

A következő szakaszokban ismertetett egyes lépéseket.

#### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Eszköz IP-cím hozzáadása és a blob-szolgáltatásvégpont a távoli gazdagéphez

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

#### <a name="configure-partner-software-and-verify-connection"></a>Partner szoftver konfigurálása és a kapcsolat ellenőrzése

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Https-kapcsolaton keresztül csatlakozni

Az Azure Blob storage REST API-k https-kapcsolaton keresztüli kapcsolat szükséges az alábbi lépéseket:

- Töltse le a tanúsítványt az Azure Portalról
- Készítse elő a számítógép távoli felügyeletére
- Az eszköz IP-cím hozzáadása és a blob-szolgáltatásvégpont a távoli gazdagéphez
- Harmadik féltől származó szoftverek beállítása és a kapcsolat ellenőrzése

A következő szakaszokban ismertetett egyes lépéseket.

### <a name="download-certificate"></a>Tanúsítvány letöltése

Az Azure portal használatával töltse le a tanúsítványt.

1. Jelentkezzen be az Azure Portalon.
2. Nyissa meg a Data Box-rendelés, és navigáljon a **általános > eszközadatok**.
3. A **hitelesítő adatai**, lépjen a **API-hozzáférés** eszközre. Kattintson a **Letöltés** gombra. Ez a művelet letölt egy  **<your order name>.cer** tanúsítványfájlt. **Mentés** ezt a fájlt. Ez a tanúsítvány a számítógépen az ügyfélszámítógépre vagy gazdagépre, amelyekkel fog csatlakozni az eszközhöz telepíti.

    ![Töltse le a tanúsítványt az Azure Portalon](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="prepare-the-host-for-remote-management"></a>Készítse elő a gazdagép távoli felügyeletére

Kövesse az alábbi lépéseket a Windows-ügyfél előkészítése a távoli kapcsolat által használt egy *https* munkamenetben:

- A .cer fájlt importálja a gyökérszintű tárolóban. az ügyfél vagy a távoli gazdagépen.
- Eszköz IP-cím hozzáadásához, és a blob-szolgáltatásvégpont az állomásleíró fájlhoz a Windows-ügyfélen.

A fenti eljárások leírását alább.

#### <a name="import-the-certificate-on-the-remote-host"></a>Importálja a tanúsítványt a távoli gazdagépen

Windows PowerShell vagy a Windows Server felhasználói felület segítségével importálhatja, és telepítse a tanúsítványt a gazdarendszer.

**A PowerShell használata**

1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként.
2. A parancssorba írja be a következőt:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

**A Windows Server felhasználói felületének használatával**

1.  Kattintson a jobb gombbal a .cer fájlt, és válassza ki **telepítése tanúsítvány**. A Tanúsítványimportáló varázsló elindul.
2.  A **hely Store**válassza **helyi gépen**, és kattintson a **tovább**.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Válassza ki **minden tanúsítvány tárolása ebben a tárolóban**, és kattintson a **Tallózás**. Keresse meg a gyökérszintű tárolóban. a távoli állomás, és kattintson a **tovább**.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Kattintson a **Befejezés** gombra. Megjelenik egy üzenet, amely tájékoztatja, hogy az importálás sikeres volt-e.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

### <a name="to-add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Eszköz IP-cím hozzáadása és a blob-szolgáltatásvégpont a távoli gazdagéphez

Az itt leírt lépések megegyeznek a keresztüli csatlakoztatása során használt *http*.

### <a name="configure-partner-software-to-establish-connection"></a>Partner szoftverhasználat-kapcsolat konfigurálása

Az itt leírt lépések megegyeznek a keresztüli csatlakoztatása során használt *http*. Az egyetlen különbség, hogy hagyja a *http-beállítással* nincs bejelölve.

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Miután csatlakozott a Data Box Blob Storage, a következő lépés az adatok másolása. Adatok másolása, előtt tekintse át az alábbiakat:

-  Adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az [Azure Storage és a Data Box korlátaival](data-box-limits.md) foglalkozó cikkben ismertetett méretkorlátoknak.
- Adatok feltöltése folyamatban van a Data Box, Data Box-en kívül más alkalmazás egyidejűleg fel, ha lehetséges, hogy emiatt feltöltési feladat hibák és az adatok sérülését.

Ebben az oktatóanyagban az AzCopy segítségével adatmásolás a Data Box blobtárolóba. Használhatja az Azure Storage Explorer (Ha inkább a GUI-alapú eszköz) vagy egy partneri szoftver az adatok másolásához.
A másolási eljárás rendelkezik az alábbi lépéseket:

- Tároló létrehozása
- Egy mappa tartalmának feltöltése a Data Box Blob storage
- Módosított fájlok feltöltése a Data Box Blob storage

Az egyes ezeket a lépéseket az alábbiakban részletesen ismertetjük.

### <a name="create-a-container"></a>Tároló létrehozása

Az első lépéseként hozzon létre egy tárolót, mert blobok minden esetben a rendszer feltölti egy tárolót. Tárolók a blobok csoportjait ugyanúgy rendszerezheti, ahogy mappákban lévő fájlokat a számítógépre. Kövesse az alábbi lépéseket egy blobtároló létrehozásához.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiókot, amelyen belül szeretné a blobtároló létrehozásához.
3. Kattintson a jobb gombbal **Blobtárolók**, és a helyi menüből válassza ki a **Blobtároló létrehozása**.

   ![Místní nabídka objektu blob tárolók létrehozása](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Szövegmező alatt megjelenik a **Blobtárolók** mappát. Adja meg a blobtároló nevét. Tekintse meg a [hozza létre a tárolót, valamint engedélyeket állíthat be](../storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) információ a blobtárolók elnevezésére vonatkozó szabályokat és korlátozásokat.
5. Nyomja meg **Enter** billentyűt a blobtároló létrehozásához vagy **Esc** megszakítja. Ha a blob-tároló sikeresen létrejött, megjelenik a **Blobtárolók** a kiválasztott tárfiókhoz tartozó mappában.

   ![Létrehozott BLOB-tárolóba](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Egy mappa tartalmának feltöltése a Data Box Blob storage

Az AzCopy segítségével egy mappában található összes fájl feltöltése a Blob storage Windows vagy Linux rendszeren. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Cserélje le `<key>` értékeket a fiók kulcsára. Az Azure Portalon a fiókkulcs lekéréséhez lépjen a tárfiókhoz. Lépjen a **beállítások > hozzáférési kulcsok**, válasszon ki egy kulcsot, és illessze be az AzCopy-parancsot.

Ha a célként megadott tároló nem létezik, az AzCopy létrehozza, majd feltölti a fájlt a tárolóba. A forrás elérési útjának frissítse az adatkönyvtár, és cserélje le `data-box-storage-account-name` a cél URL-CÍMÉT a tárfiók nevét a Data Box társított.

A megadott könyvtár tartalmának a Blob Storage-ba való rekurzív feltöltéséhez adja meg a `--recursive` (Linux) vagy az `/S` (Windows) beállítást. Ha az AzCopyt ezen beállítások egyikével futtatja, minden almappa és a bennük tárolt fájlok is feltöltődnek.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Módosított fájlok feltöltése a Data Box Blob storage

Az azcopyval a legutóbbi módosításuk ideje alapján fájlok feltöltéséhez. Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Ha csak a frissített vagy új fájlokat szeretné feltölteni, adja hozzá az `--exclude-older` (Linux) vagy az `/XO` (Windows) paramétert az AzCopy-parancshoz.

Ha csak azokat az erőforrásokat szeretné átmásolni a forrásból, amelyek nem léteznek a célhelyen, adja meg az `--exclude-older` és az `--exclude-newer` (Linux), vagy az `/XO` és az `/XN` (Windows) paramétereket az AzCopy-parancsban. Az AzCopy az időbélyegek alapján csak a frissített adatokat tölti fel.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO


Következő lépés, hogy az eszköz szállításra való előkészítése.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Data Box Blob storage-n keresztül kapcsolódni *http* vagy *https*
> * Adatok másolása a Data Boxra


Folytassa a következő oktatóanyaggal, megtudhatja, hogyan tehetnek a Data Box elküldje a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)