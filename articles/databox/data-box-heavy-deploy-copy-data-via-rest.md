---
title: 'Oktatóanyag: Adatok másolása az Azure Data Box Blob storage-ba REST API-kon keresztül'
description: Megtudhatja, hogyan másolhat adatokat az Azure Data Box Heavy Blob storage-ba REST API-kon keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 9f3ba0a7e9f7cf72b0eade16679d980fe2207f98
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80297213"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Oktatóanyag: Adatok másolása az Azure Data Box Blob storage-ba REST API-kon keresztül  

Ez az oktatóanyag az Azure Data Box Blob storage-hoz való csatlakozás eljárásait ismerteti a REST API-kon keresztül *http-n* vagy *https-en*keresztül. A csatlakozás után az adatok Data Box Blob storage-ba másolásához szükséges lépéseket ismertetik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozás a Data Box Blob storage-hoz *http-n* vagy *https-en* keresztül
> * Adatok másolása a Data Box Heavyre

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejezte az [oktatóanyagot: Az Azure Data Box Heavy beállítása.](data-box-heavy-deploy-set-up.md)
2. Megkapta a Data Box Heavyt, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Áttekintette a [Data Box Blob storage rendszerkövetelményeit,](data-box-system-requirements-rest.md) és ismeri az API-k, Az SDK-k és az eszközök támogatott verzióit.
4. Olyan gazdaszámítógéphez férhet hozzá, amely rendelkezik azokkal az adatokkal, amelyeket át szeretne másolni a Data Box Heavy-ba. A gazdaszámítógépen:
    - Támogatott [operációs rendszer futtatása](data-box-system-requirements.md).
    - egy nagy sebességű hálózathoz kell csatlakoznia. A legnagyobb másolási sebesség érdekében két 40 GbE sebességű kapcsolat (csomópontonként egy) használható egyidejűleg. Ha nem rendelkezik 40 GbE sebességű kapcsolattal, javasoljuk, hogy legalább két 10 GbE sebességű kapcsolattal (csomópontonként eggyel) rendelkezzen. 
5. [Töltse le az AzCopy 7.1.0-t](https://aka.ms/azcopyforazurestack20170417) a gazdaszámítógépére. Az AzCopy használatával adatokat másolhat az Azure Data Box Blob storage-ba a gazdaszámítógépről.


## <a name="connect-via-http-or-https"></a>Csatlakozás http-n vagy https-en keresztül

A Data Box Blob storage-hoz *http* vagy *https*protokollon keresztül csatlakozhat.

- *A Https* a Data Box Blob storage-hoz való csatlakozás biztonságos és ajánlott módja.
- *A http* megbízható hálózatokon való csatlakozáskor használatos.

A csatlakozás lépései eltérőek, ha *http-n* vagy https-en keresztül csatlakozik a Data Box Blob *storage-hoz.*

## <a name="connect-via-http"></a>Csatlakozás http-n keresztül

A Data Box Blob storage REST API-khoz való csatlakozás *http-n* keresztül a következő lépéseket igényli:

- Az eszköz IP- és blobszolgáltatás-végpontjának hozzáadása a távoli állomáshoz
- Külső gyártótól származó szoftver konfigurálása és a kapcsolat ellenőrzése

A lépések mindegyikét a következő szakaszok ismertetik.

> [!IMPORTANT]
> A Data Box Heavy esetében meg kell ismételnie az összes csatlakozási utasítást a második csomóponthoz való csatlakozáshoz.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Eszköz IP-címének és blobszolgáltatás-végpontjának hozzáadása

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]



### <a name="configure-partner-software-and-verify-connection"></a>Partnerszoftver konfigurálása és a kapcsolat ellenőrzése

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Csatlakozás https-en keresztül

Az Azure Blob storage REST API-khttps-en keresztüli kapcsolata a következő lépéseket igényli:

- A tanúsítvány letöltése az Azure Portalról
- A tanúsítvány importálása az ügyfélen vagy a távoli állomáson
- Az eszköz IP- és blobszolgáltatás-végpontjának hozzáadása az ügyfélhez vagy a távoli állomáshoz
- Külső gyártótól származó szoftver konfigurálása és a kapcsolat ellenőrzése

A lépések mindegyikét a következő szakaszok ismertetik.

> [!IMPORTANT]
> A Data Box Heavy esetében meg kell ismételnie az összes csatlakozási utasítást a második csomóponthoz való csatlakozáshoz.

### <a name="download-certificate"></a>Tanúsítvány letöltése

Használja az Azure Portalon a tanúsítvány letöltéséhez.

1. Jelentkezzen be az Azure Portalra.
2. Nyissa meg a Data Box rendelést, és keresse meg **az Általános > Eszköz részletei**.
3. Az **Eszköz hitelesítő adatai**csoportban nyissa meg az **API-hozzáférést** az eszközhöz. Kattintson **a Letöltés gombra.** Ez a művelet letölti ** \<a rendelés nevét>.cer** tanúsítványfájlban. **Mentse** a fájlt. Ezt a tanúsítványt arra az ügyfélre vagy gazdaszámítógépre telepíti, amelyet az eszközhöz való csatlakozáshoz fog használni.

    ![Tanúsítvány letöltése az Azure Portalon](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Tanúsítvány importálása 

A Data Box Blob storage HTTPS-en keresztüli eléréséhez TLS/SSL-tanúsítvány szükséges az eszközhöz. A tanúsítvány ügyfélalkalmazás számára történő elérhetővé juttatásának módja alkalmazásról alkalmazásra, operációs rendszerekés disztribúciók között változik. Egyes alkalmazások hozzáférhetnek a tanúsítványhoz, miután importálták a rendszer tanúsítványtárolójába, míg más alkalmazások nem használják ezt a mechanizmust.

Az egyes alkalmazásokra vonatkozó konkrét információkat ebben a szakaszban említi. A többi alkalmazással kapcsolatos további információkért tanulmányozza az alkalmazás és a használt operációs rendszer dokumentációját.

Az alábbi lépésekkel `.cer` importálhatja a fájlt egy Windows vagy Linux ügyfél gyökértárolójába. Windows rendszeren a Windows PowerShell vagy a Windows Server felhasználói felülete segítségével importálhatja és telepítheti a tanúsítványt a rendszeren.

#### <a name="use-windows-powershell"></a>A Windows PowerShell használata

1. Windows PowerShell-munkamenet indítása rendszergazdaként.
2. A parancssorba írja be a következőt:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>A Windows Server felhasználói felületének használata

1.  Kattintson a `.cer` jobb gombbal a fájlra, és válassza **a Tanúsítvány telepítése parancsot.** Ez a művelet elindítja a Tanúsítványimportálás varázslót.
2.  Az **Üzlet helyéhez**válassza a **Helyi számítógép**lehetőséget, majd kattintson a **Tovább**gombra.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Válassza **az Összes tanúsítvány helye a következő tárolóban**lehetőséget, majd kattintson a **Tallózás gombra.** Nyissa meg a távoli állomás gyökértárolóját, majd kattintson a **Tovább**gombra.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Kattintson a **Befejezés** gombra. Megjelenik egy üzenet, amely arról tájékoztat, hogy az importálás sikeres volt.

    ![Tanúsítvány importálása a PowerShell használatával](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Linux rendszer használata

A tanúsítvány importálásának módja a felosztástól függően változik.

> [!IMPORTANT]
> A Data Box Heavy esetében meg kell ismételnie az összes csatlakozási utasítást a második csomóponthoz való csatlakozáshoz.

Több, mint például az Ubuntu `update-ca-certificates` és a Debian, használja a parancsot.  

- Nevezze át a Base64 kódolású tanúsítványfájlt `.crt` kiterjesztésre, `/usr/local/share/ca-certificates directory`majd másolja a ba.
- Futtassa a következő parancsot: `update-ca-certificates`.

Az RHEL, a Fedora és a `update-ca-trust` CentOS legújabb verziói használják a parancsot.

- Másolja a tanúsítványfájlt `/etc/pki/ca-trust/source/anchors` a könyvtárba.
- Futtassa az `update-ca-trust` parancsot.

A részletekért tekintse meg a terjesztésre vonatkozó dokumentációt.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Eszköz IP-címének és blobszolgáltatás-végpontjának hozzáadása 

Ugyanezeket a lépéseket követve adja hozzá az [eszköz IP-címét és a blobszolgáltatás végpontját *a http-n*keresztüli csatlakozáskor.](#add-device-ip-address-and-blob-service-endpoint)

### <a name="configure-partner-software-and-verify-connection"></a>Partnerszoftver konfigurálása és a kapcsolat ellenőrzése

Kövesse a [ *http-n*való csatlakozás során használt partnerszoftverek konfigurálása](#configure-partner-software-and-verify-connection)című lépéseket. Az egyetlen különbség az, hogy hagyja a *használata http opciót* bejelölve.

## <a name="copy-data-to-data-box-heavy"></a>Adatok másolása a Data Box Heavyre

Miután csatlakozott a Data Box Blob storage- hoz, a következő lépés az adatok másolása. Az adatok másolása előtt tekintse át a következő szempontokat:

-  Az adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az Azure storage és a [Data Box Heavy korlátokban](data-box-limits.md)leírt méretkorlátoknak.
- Ha a Data Box Heavy által feltöltött adatokat egyidejűleg más alkalmazások töltik fel a Data Box Heavy-on kívül, ez feltöltési feladathibákat és adatsérülést eredményezhet.

Ebben az oktatóanyagban az AzCopy adatok másolására szolgál a Data Box Blob storage-ba. Az Azure Storage Explorer (ha inkább egy GUI-alapú eszköz) vagy egy partner szoftver az adatok másolásához is használhatja.

A másolási eljárás a következő lépésekkel rendelkezik:

- Tároló létrehozása
- Mappa tartalmának feltöltése a Data Box Blob tárolóba
- Módosított fájlok feltöltése a Data Box Blob storage-ba


A lépések mindegyikét részletesen ismertetjük a következő szakaszokban.

> [!IMPORTANT]
> A Data Box Heavy esetében meg kell ismételnie az összes másolási utasítást az adatok második csomópontra másolásához.

### <a name="create-a-container"></a>Tároló létrehozása

Az első lépés egy tároló létrehozása, mert a blobok mindig egy tárolóba kerülnek feltöltve. A tárolók úgy rendszerezik a blobcsoportokat, mint a fájlokat a számítógép mappáiban. Az alábbi lépésekkel hozzon létre egy blob tárolót.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a tárfiókot, amelyen belül létre kívánja hozni a blob tárolót.
3. Kattintson a jobb gombbal a **Blob Containers elemre,** és a helyi menüben válassza a **Blob Container létrehozása parancsot.**

   ![Blob-tárolók környezetmenüjének létrehozása](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Egy szövegdoboz jelenik meg a **Blob Containers** mappa alatt. Adja meg a blobtároló nevét. Tekintse meg [a tároló létrehozása, és engedélyek beállítása](../storage/blobs/storage-quickstart-blobs-dotnet.md) a szabályok és korlátozások elnevezési blob tárolók.
5. Nyomja **meg az Enter,** ha kész a blob tároló létrehozásához, vagy **esc** megszakíthatja. A blob tároló sikeres létrehozása után a blob tárolók a kijelölt tárfiók **Blob Containers mappája** alatt jelenik meg.

   ![Blob-tároló létrehozva](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Mappa tartalmának feltöltése a Data Box Blob tárolóba

Az AzCopy segítségével töltse fel az összes fájlt egy mappában blob tároló Windows vagy Linux. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Cserélje `<key>` le a fiókkulcsot. A fiókkulcs beszerezéséhez az Azure Portalon nyissa meg a tárfiókot. Nyissa meg **a Beállítások > Access billentyűket,** jelöljön ki egy kulcsot, és illessze be az AzCopy parancsba.

Ha a célként megadott tároló nem létezik, az AzCopy létrehozza, majd feltölti a fájlt a tárolóba. Frissítse az adatkönyvtár forráselérési útját, és cserélje le `data-box-storage-account-name` a cél URL-címében az adatmezőhöz társított tárfiók nevét.

A megadott könyvtár tartalmának a Blob Storage-ba való rekurzív feltöltéséhez adja meg a `--recursive` (Linux) vagy az `/S` (Windows) beállítást. Ha az AzCopyt ezen beállítások egyikével futtatja, minden almappa és a bennük tárolt fájlok is feltöltődnek.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Módosított fájlok feltöltése a Data Box Blob storage-ba

Az AzCopy segítségével az utolsó módosítási idejük alapján tölthet fel fájlokat. Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Ha csak a frissített vagy új fájlokat szeretné feltölteni, adja hozzá az `--exclude-older` (Linux) vagy az `/XO` (Windows) paramétert az AzCopy-parancshoz.

Ha csak azokat az erőforrásokat szeretné átmásolni a forrásból, amelyek nem léteznek a célhelyen, adja meg az `--exclude-older` és az `--exclude-newer` (Linux), vagy az `/XO` és az `/XN` (Windows) paramétereket az AzCopy-parancsban. Az AzCopy az időbélyegek alapján csak a frissített adatokat tölti fel.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO

Ha a csatlakozási vagy másolási művelet során hibák lépnek fel, olvassa [el a Data Box Blob storage hibáinak elhárítása című témakört.](data-box-troubleshoot-rest.md)

A következő lépés az eszköz előkészítése a szállításra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozás a Data Box Blob storage-hoz *http-n* vagy *https-en* keresztül
> * Adatok másolása a Data Box Heavyre


Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box Heavy visszaküldése a Microsoftnak](./data-box-heavy-deploy-picked-up.md)
