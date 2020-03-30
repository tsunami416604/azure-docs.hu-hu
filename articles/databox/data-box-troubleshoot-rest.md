---
title: Azure Data Box hibaelhárítás a REST-felület használatával kapcsolatban| Microsoft dokumentumok
description: Bemutatja, hogyan háríthatók el az Azure Data Boxban látható problémák, ha az adatok másolása a REST-felületen keresztül történik.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297130"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Az Azure Data Box Blob tárterülettel kapcsolatos problémák elhárítása

Ez a cikk részletesen ismerteti, hogyan háríthatja el azokat a problémákat, amelyek a Data Box Blob storage használatakor a Data Box REST-felületén keresztül adatok másolása kor jelenhetnek meg. Ezek a problémák akkor kerülnek felszínre, ha a Data Box Blob storage más alkalmazásokkal vagy ügyfélkódtárakkal, például az Azure Storage Explorer, az AzCopy vagy az Azure Storage-kódtár pythonhoz használatával.

## <a name="errors-seen-in-azure-storage-explorer"></a>Az Azure Storage Explorerben látható hibák

Ez a szakasz részletezi az Azure Storage Explorer data box blob tárterülettel való használata során felmerülő problémák at.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|Nem lehet beolvasni a gyermekerőforrásokat. Az egyik HTTP-fejléc értéke nem a megfelelő formátumú.|A **Szerkesztés** menüben válassza az **Azure Stack-ek célparancsait.** <br>Indítsa újra az Azure Storage Explorert.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Ellenőrizze, hogy a `<accountname>.blob.<serialnumber>.microsoftdatabox.com` végpont neve hozzá van-e adva a hosts fájlhoz ezen az útvonalon: <li>`C:\Windows\System32\drivers\etc\hosts`Windows rendszeren, vagy </li><li> `/etc/hosts`Linux alatt.</li>|
|Nem lehet beolvasni a gyermekerőforrásokat. <br>Részletek: önaláírt tanúsítvány |Importálja az eszköz TLS/SSL-tanúsítványát az Azure Storage Explorerbe: <li>Töltse le a tanúsítványt az Azure Portalról. További információt a [Tanúsítvány letöltése című](data-box-deploy-copy-data-via-rest.md#download-certificate)területen talál.</li><li>A **Szerkesztés** menüben válassza az **SSL-tanúsítványok,** majd **a Tanúsítványok importálása parancsot.**</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Az AzCopy for Windows programban látható hibák

Ez a szakasz az AzCopy for Windows data box blob tárolóval való használata során felmerülő problémák at részletezi.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|Úgy tűnik, hogy az AzCopy parancs egy percig lefagy, mielőtt megjelenne a hiba: <br>Nem sikerült felsorolni a könyvtári https://... A távoli név nem oldható fel.`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Ellenőrizze, hogy a `<accountname>.blob.<serialnumber>.microsoftdatabox.com` végpont neve hozzá van-e adva a hosts fájlhoz a következő helyen: `C:\Windows\System32\drivers\etc\hosts`.|
|Úgy tűnik, hogy az AzCopy parancs egy percig lefagy, mielőtt megjelenne a hiba: <br>Hiba a forráshely elemzésével. Az alapul szolgáló kapcsolat megszakadt: Nem lehetett megbízhatósági kapcsolatot létesíteni az SSL/TLS biztonságos csatornához.|Importálja az eszköz TLS/SSL tanúsítványát a rendszer tanúsítványtárolójába. További információt a [Tanúsítvány letöltése című](data-box-deploy-copy-data-via-rest.md#download-certificate)területen talál.|


## <a name="errors-seen-in-azcopy-for-linux"></a>Hibák látható AzCopy linuxos

Ez a szakasz részletezi az AzCopy for Linux data box blob tárolóval kapcsolatos problémák némelyikét.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|Úgy tűnik, hogy az AzCopy parancs 20 percig lefagy, mielőtt megjelenne a hiba: <br>Hiba történt a `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`forráshely elemzésével. Nincs ilyen eszköz vagy cím|Ellenőrizze, hogy a `<accountname>.blob.<serialnumber>.microsoftdatabox.com` végpont neve hozzá van-e adva a hosts fájlhoz a következő helyen: `/etc/hosts`.|
|Úgy tűnik, hogy az AzCopy parancs 20 percig lefagy, mielőtt megjelenne a hiba: <br>Hiba a forráshely elemzésével... Az SSL-kapcsolat nem jött létre.|Importálja az eszköz TLS/SSL tanúsítványát a rendszer tanúsítványtárolójába. További információt a [Tanúsítvány letöltése című](data-box-deploy-copy-data-via-rest.md#download-certificate)területen talál.|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Hibák láthatók az Azure Storage-függvénytár pythonhoz

Ez a szakasz részletezi a Data Box Disk üzembe helyezése során felmerülő legfontosabb problémákat, amikor Linux-ügyfélt használ az adatok másolásához.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|Az egyik HTTP-fejléc értéke nem a megfelelő formátumú. |A Data Box nem támogatja a Microsoft Azure Storage Library for Python telepített verzióját. Tekintse meg az Azure Data Box Blob tárolási követelményeit a támogatott verziókhoz.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|A Python futtatása előtt állítsa be a REQUESTS_CA_BUNDLE környezeti változót a Base64 kódolású TLS tanúsítványfájl elérési útvonalára [(lásd: A tanúsítvány letöltése).](data-box-deploy-copy-data-via-rest.md#download-certificate) <br>Példa:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Másik lehetőségként adja hozzá a tanúsítványt a rendszer tanúsítványtárolójához, majd állítsa be ezt a környezeti változót az adott tároló elérési útvonalára. <br> Például az Ubuntu rendszeren: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Gyakori hibák

Ezek a hibák nem jellemzőek egyetlen alkalmazásra sem.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|A kapcsolat túllépi az időtöltést. |Jelentkezzen be a Data Box eszközbe, és ellenőrizze, hogy fel van-e oldva. Minden alkalommal, amikor az eszköz újraindul, zárva marad, amíg valaki be nem jelentkezik.|

## <a name="next-steps"></a>További lépések

- További információ a [Data Box Blob tárolási rendszerkövetelményeiről.](data-box-system-requirements-rest.md)
