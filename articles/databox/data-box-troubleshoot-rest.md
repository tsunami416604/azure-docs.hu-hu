---
title: A REST-felület használatának hibaelhárítása Azure Data Box | Microsoft Docs
description: Ismerteti, hogyan lehet elhárítani a Azure Data Boxban észlelt problémákat, amikor az Adatmásolás a REST-felületen keresztül történik.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297130"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Azure Data Box blob Storage-hoz kapcsolódó problémák elhárítása

Ez a cikk részletesen ismerteti, hogyan lehet elhárítani a Data Box blob Storage-t a Data Box REST-felületén keresztül az adatok másolásához használt hibák elhárításával kapcsolatban. Ezek a problémák a Data Box blob Storage-t más alkalmazásokkal vagy ügyféloldali kódtárakkal, például a Azure Storage Explorer, a AzCopy vagy a Pythonhoz készült Azure Storage Library használatával használják.

## <a name="errors-seen-in-azure-storage-explorer"></a>A Azure Storage Explorerban észlelt hibák

Ez a szakasz a Azure Storage Explorer Data Box blob Storage-ban való használata során felmerülő problémákat ismerteti.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|Nem sikerült beolvasni a gyermek erőforrásokat. A HTTP-fejlécek egyikének értéke nem megfelelő formátumú.|A **Szerkesztés** menüben válassza a **cél Azure stack API**-k elemet. <br>Azure Storage Explorer újraindítása.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Győződjön meg arról, hogy `<accountname>.blob.<serialnumber>.microsoftdatabox.com` a végpont neve hozzá van adva a gazdagépek fájljához ezen az elérési úton: <li>`C:\Windows\System32\drivers\etc\hosts`Windows rendszeren vagy </li><li> `/etc/hosts`Linux rendszeren.</li>|
|Nem sikerült beolvasni a gyermek erőforrásokat. <br>Részletek: önaláírt tanúsítvány |Importálja az eszköz TLS/SSL-tanúsítványát Azure Storage Explorerba: <li>Töltse le a tanúsítványt a Azure Portal. További információ: [a tanúsítvány letöltése](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>A **Szerkesztés** menüben válassza az **SSL-tanúsítványok** lehetőséget, majd válassza a **tanúsítványok importálása**lehetőséget.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>A Windows AzCopy által észlelt hibák

Ez a szakasz részletesen ismerteti a AzCopy for Windows és a Data Box blob Storage használatával kapcsolatos problémákat.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|A AzCopy parancs úgy tűnik, hogy egy percen keresztül lefagy a hiba megjelenítése előtt: <br>A címtár https://enumerálása sikertelen... A távoli név nem oldható fel.`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Győződjön meg arról, hogy `<accountname>.blob.<serialnumber>.microsoftdatabox.com` a végpont neve hozzá van adva a Hosts fájlhoz a következő helyen: `C:\Windows\System32\drivers\etc\hosts`.|
|A AzCopy parancs úgy tűnik, hogy egy percen keresztül lefagy a hiba megjelenítése előtt: <br>Hiba történt a forrás helyének elemzésekor. Az alapul szolgáló kapcsolat bezárult: nem hozható létre megbízhatósági kapcsolat az SSL/TLS biztonságos csatorna számára.|Importálja az eszköz TLS/SSL-tanúsítványát a rendszer tanúsítványtárolóba. További információ: [a tanúsítvány letöltése](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>A Linux-AzCopy észlelt hibák

Ez a szakasz részletesen ismerteti a AzCopy for Linux és a Data Box blob Storage használatával kapcsolatos problémákat.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|A AzCopy parancs úgy tűnik, hogy 20 percen belül lefagy a hiba megjelenítése előtt: <br>Hiba történt a forrás helyének `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`elemzésekor. Nincs ilyen eszköz vagy címe|Győződjön meg arról, hogy `<accountname>.blob.<serialnumber>.microsoftdatabox.com` a végpont neve hozzá van adva a Hosts fájlhoz a következő helyen: `/etc/hosts`.|
|A AzCopy parancs úgy tűnik, hogy 20 percen belül lefagy a hiba megjelenítése előtt: <br>Hiba történt a forrás helyének elemzésekor... Nem lehet létrehozni az SSL-kapcsolatokat.|Importálja az eszköz TLS/SSL-tanúsítványát a rendszer tanúsítványtárolóba. További információ: [a tanúsítvány letöltése](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>A Pythonhoz készült Azure Storage-függvénytárban észlelt hibák

Ez a szakasz részletesen ismerteti a Data Box Disk üzembe helyezése során felmerülő leggyakoribb problémákat, amikor Linux-ügyfelet használ az adatok másolásához.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|A HTTP-fejlécek egyikének értéke nem megfelelő formátumú. |A Data Box nem támogatja a Python Microsoft Azure Storage könyvtárának telepített verzióját. Lásd: Azure Data Box blob Storage-követelmények a támogatott verziókhoz.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|A Python futtatása előtt állítsa a REQUESTS_CA_BUNDLE környezeti változót a Base64 kódolású TLS-tanúsítványfájl elérési útjára (lásd: [a tanúsítvány letöltése](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Például:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Másik lehetőségként vegye fel a tanúsítványt a rendszer tanúsítványtárolóba, majd állítsa be ezt a környezeti változót a tároló elérési útjára. <br> Például az Ubuntu rendszeren: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Gyakori hibák

Ezek a hibák nem kifejezetten egyetlen alkalmazásra vonatkoznak.

|Hibaüzenet  |Javasolt művelet |
|---------|---------|
|A kapcsolatok időtúllépést mutatnak. |Jelentkezzen be a Data Box eszközre, és győződjön meg arról, hogy a zárolása fel van oldva. Az eszköz minden újraindításakor zárolva marad, amíg valaki bejelentkezik.|

## <a name="next-steps"></a>További lépések

- A [blob Storage rendszerkövetelményeinek Data Box](data-box-system-requirements-rest.md)megismerése.
