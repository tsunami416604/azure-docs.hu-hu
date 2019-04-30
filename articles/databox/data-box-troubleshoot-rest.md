---
title: Az Azure Data Box hibaelhárítása a REST-felület használatával |} A Microsoft Docs
description: Ha a REST-felületen keresztül az adatok másolása az Azure Data Box látható problémák elhárításához ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: ee2820d78e95924e09a0219753f87d6910c0e736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782956"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Az Azure Data Box Blob storage-kapcsolatos problémák elhárítása

Ez a cikk részletes információk hibáinak elhárítása a jelenhet meg a Data Box Blob storage használata a Data Box a REST-felületen keresztül másolhat adatokat. Ezen problémák felület használata esetén Data Box Blob storage más alkalmazások vagy klienskódtárak, például az Azure Storage Explorer, az AzCopy vagy az Azure Storage Pythonhoz készült.

## <a name="errors-seen-in-azure-storage-explorer"></a>Az Azure Storage Explorerben látható hibák

Ez a szakasz részletesen szembesülnek, amikor az Azure Storage Explorer használatával a Data Box Blob storage-problémák.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|Nem sikerült beolvasni a gyermekszintű erőforrása. Az egyik a HTTP-fejléceket az érték nem szerepel a megfelelő formátumban.|Az a **szerkesztése** menüjében válassza **cél Azure Stack API-k**. <br>Indítsa újra az Azure Storage Explorerben.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Ellenőrizze, hogy a végpont neve `<accountname>.blob.<serialnumber>.microsoftdatabox.com` adnak hozzá a hosts fájl a elérési úton: <li>`C:\Windows\System32\drivers\etc\hosts` a Windows vagy </li><li> `/etc/hosts` linuxon.</li>|
|Nem sikerült beolvasni a gyermekszintű erőforrása. <br>Részletei: önaláírt tanúsítvány |Az SSL-tanúsítványt az eszköz importálása az Azure Storage Explorerben: <li>Töltse le a tanúsítványt az Azure Portalról. További információért ugorjon [töltse le a tanúsítványt](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Az a **szerkesztése** menüjében válassza **SSL-tanúsítványok** majd **tanúsítványok importálása**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Az AzCopy for Windows látható hibák

Ez a szakasz részletesen megmérkőzött AzCopy for Windows használata a Data Box Blob storage-problémák.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|AzCopy-parancs egy percet, mielőtt megjeleníti ezt a hibát leállásához jelenik meg: <br>Directory https:// számbavétele sikertelen volt... A távoli név nem oldható fel. `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Ellenőrizze, hogy a végpont neve `<accountname>.blob.<serialnumber>.microsoftdatabox.com` adnak hozzá a gazdagépek fájlban a következő: `C:\Windows\System32\drivers\etc\hosts`.|
|AzCopy-parancs egy percet, mielőtt megjeleníti ezt a hibát leállásához jelenik meg: <br>Hiba történt a forrás helye elemzése. A kapcsolat lezárva: Nem sikerült létrehozni a megbízhatósági kapcsolatot az SSL/TLS biztonságos csatorna.|Az SSL-tanúsítványt az eszköz importálja a rendszer tanúsítványtárolójába. További információért ugorjon [töltse le a tanúsítványt](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Az AzCopy látható a Linux-hibák

Ez a szakasz részletesen megmérkőzött az AzCopy használata a Linux Data Box Blob storage-problémák.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|AzCopy-parancs 20 percig, mielőtt megjeleníti ezt a hibát leállásához jelenik meg: <br>Hiba történt a forrás helye `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Nincs ilyen eszköz vagy a cím|Ellenőrizze, hogy a végpont neve `<accountname>.blob.<serialnumber>.microsoftdatabox.com` adnak hozzá a gazdagépek fájlban a következő: `/etc/hosts`.|
|AzCopy-parancs 20 percig, mielőtt megjeleníti ezt a hibát leállásához jelenik meg: <br>Hiba történt a forrás helye elemzése... Nem sikerült létrehozni az SSL-kapcsolatot.|Az SSL-tanúsítványt az eszköz importálja a rendszer tanúsítványtárolójába. További információért ugorjon [töltse le a tanúsítványt](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Pythonhoz készült Azure Storage-kódtárat látható hibák

Ebben a szakaszban egy Linux-ügyfél használata az adatok másolása a Data Box-lemezek üzembe helyezése során szembesülnek kapcsolatos leggyakoribb hibák némelyike részletezi.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|Az egyik a HTTP-fejléceket az érték nem szerepel a megfelelő formátumban. |A Microsoft Azure Storage-kódtára a Pythonhoz telepített verziója nem támogatott a Data Box. Tekintse meg az Azure Data Box Blob storage követelményeit a támogatott verziók.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Mielőtt futtatná a Python, a Base64-kódolású SSL-tanúsítvány fájl elérési útja a REQUESTS_CA_BUNDLE környezeti változó beállítása (lásd: hogyan [töltse le a tanúsítványt]()). <br>Példa:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Azt is megteheti hozzáadta a tanúsítványt a rendszer tanúsítványtárolójába, és majd állítsa be ezt a környezeti változót az elérési úthoz, ezt a tárolót. <br> Például az Ubuntu rendszeren: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Gyakori hibák

A hibák miatt nem minden alkalmazáshoz.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|A kapcsolat időtúllépés miatt megszakadt. |Jelentkezzen be a Data Box-eszközre, és ellenőrizze, hogy nem oldják a zárolást. Amikor az eszköz-újraindítások, azt zárolt marad, amíg valaki bejelentkezik.|

## <a name="next-steps"></a>További lépések

- További információ a [Data Box Blob storage rendszerkövetelmények](data-box-system-requirements-rest.md).
