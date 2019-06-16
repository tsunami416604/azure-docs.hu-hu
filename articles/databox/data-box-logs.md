---
title: Nyomon követheti, és az Azure Data Box, az Azure Data Box nehéz események naplózása |} A Microsoft Docs
description: Ismerteti, hogyan lehet nyomon követése és naplózása az Azure Data Box és az Azure Data Box nehéz rendelését különböző szakaszaiban.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495809"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Tracking és az eseménynaplózás az Azure Data Box-és az Azure Data Box (nagy erőforrásigényű)

A Data Box vagy a Data Box nehéz sorrendet halad át az alábbi lépéseket: order, állítsa be, másolja, adja vissza, az Azure-bA adatfeltöltési és győződjön meg arról, és az adatok törlését. Megfelelő a sorrendben, tegye meg több a rendelés való hozzáférésnek, az események naplózása, nyomon követheti a rendelés, és a különböző előállított naplók értelmezése.

Az alábbi táblázat a Data Box vagy a Data Box nehéz rendelés lépéseket, és nyomon követése és naplózása a rendelés során minden lépés használható eszközök összegzését jeleníti meg.

| Data Box rendelés fázis       | Eszköz nyomon követése és naplózása                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Rendelés létrehozása               | [Állítsa be a hozzáférés-vezérlés a sorrendben, RBAC-n keresztül](#set-up-access-control-on-the-order)                                                    |
| A rendelés feldolgozása            | [Nyomon követheti a rendelés](#track-the-order) keresztül <ul><li> Azure Portal </li><li> A szállítási címhez tartozó szolgáltató webhely </li><li>E-mail-értesítések</ul> |
| Eszköz beállítása              | Eszköz hitelesítő adatai hozzáférést bejelentkezett [tevékenységeket tartalmazó naplók](#query-activity-logs-during-setup)                                              |
| Adatok másolása eszköz        | [Nézet *error.xml* fájlok](#view-error-log-during-data-copy) az adatok másolása                                                             |
| A szállítás előkészítése            | [A AJ fájlokat](#inspect-bom-during-prepare-to-ship) vagy az eszközön lévő fájlok                                      |
| Adatok feltöltése az Azure-bA       | [Felülvizsgálat *copylogs* ](#review-copy-log-during-upload-to-azure) adatok során hibák feltöltése az Azure-adatközpont                         |
| Adatok törlését az eszközről   | [Felügyeleti lánc naplók lánc megtekintéséhez](#get-chain-of-custody-logs-after-data-erasure) többek között a naplók és a rendelési előzmények                                                   |

Ez a cikk ismerteti részletesen ismertetjük a különböző mechanizmusokat vagy nyomon követése és naplózása a Data Box vagy a Data Box nehéz sorrendben elérhető eszközöket. Ebben a cikkben található információk, Data Box mind a Data Box nehéz vonatkozik. Az ezt követő szakaszokban Data Box mutató hivatkozásokat is érvényesek a Data Box (nagy erőforrásigényű).

## <a name="set-up-access-control-on-the-order"></a>Ahhoz, a hozzáférés-vezérlés beállítása

Szabályozhatja, hogy ki férhet hozzá a rendeléséhez a sorrend létrehozásakor. Szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök beállítása a különböző hatókörök szabályozhatja a hozzáférést a Data Box-rendelését. Az RBAC szerepkör határozza meg a hozzáférés – olvasási és írási, olvasási, olvasási és írási műveletek egy részét.

A két lehetséges szerepkör, amely az Azure Data Box szolgáltatás adható meg a következők:

- **Box Adatolvasó** – egy rendelés (ek) a csak olvasási hozzáféréssel rendelkezik, a hatókör szerint. Csak akkor megtekintheti a rendelés részleteit. Ezeket nem lehet hozzáférni a storage-fiókokhoz kapcsolódó összes többi részletet, vagy a rendelés részleteit, például cím szerkesztése és így tovább.
- **Data Box Közreműködője** -adatok átviteléhez a megadott tárfiókhoz rendelés csak hozhat létre *Ha írási hozzáférést a tárfiókhoz már rendelkeznek*. Nem rendelkeznek a tárfiókhoz való hozzáférést, ha azok még nem hozható létre másolhat adatokat a fiókot a Data Box-rendelését. Ez a szerepkör nem határoz meg minden olyan Storage-fiók kapcsolódó tárfiókok és az engedélyek nem biztosít hozzáférést.  

A hozzáférés korlátozása egy rendelést, a következőket teheti:

- Rendelje hozzá a szerepkört egy rendelés szintjén. A felhasználó csak rendelkezik ilyen engedéllyel, a szerepkörök számára, hogy adott Data Box-rendelés csak, és semmi más szerint.
- Az erőforráscsoport szintjén szerepkör hozzárendelése, a felhasználó rendelkezik hozzáféréssel az erőforráscsoporton belül Data Box-rendelés.

Javasolt az RBAC használatát további információkért lásd: [ajánlott eljárások az RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>A megrendelés nyomon követése

Nyomon követheti a rendelést, az Azure Portalon keresztül, és a szállítási szolgáltatója webhelyen keresztül. Az alábbi módszerek a rendszer a bármikor a Data Box-rendelését nyomon követéséhez:

- A rendelés nyomon, amikor az eszköz van az Azure-adatközpontban vagy a helyszínen, nyissa meg a **Data Box-rendelését > Áttekintés** az Azure Portalon.

    ![Rendelés állapota és nyomon követése nem megtekintése](media/data-box-logs/overview-view-status-1.png)

- Nyomon követheti sorrendben, amikor az eszköz az átvitel során, lépjen a regionális szolgáltató webhelyén, például UPS webhely velünk a KAPCSOLATOT. Adja meg az ahhoz tartozó követési szám.
- Data Box is küld értesítő e-mailek, bármikor a rendelés állapota módosul az e-maileket a sorrend létrehozásakor megadott alapján. Minden Data Box-állapot listáját lásd: [megtekintheti a rendelés állapota](data-box-portal-admin.md#view-order-status). A társított értesítési beállítások módosításához tekintse meg a [értesítés adatainak szerkesztése](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Lekérdezés Tevékenységnaplók a telepítés során

- A Data Box érkezik, a helyszíni zárolt állapotban. Az eszköz hitelesítő adatait az Azure Portalon elérhető használhatja a rendeléséhez.  

    Ha a Data Box be van állítva, szükség lehet tudni, akik az összes elérhető, az eszköz hitelesítő adatait. A döntse el, hogy ki fért a **hitelesítő adatai** panelen lekérdezheti a Tevékenységnaplókban.  Minden művelet, amely magában foglalja a elérése **. eszköz részletei > hitelesítő adatok** panel be legyen jelentkezve a tevékenységnaplókat, `ListCredentials` művelet.

    ![Tevékenységnaplók lekérdezése](media/data-box-logs/query-activity-log-1.png)

- Minden egyes jelentkezzen be a Data Box naplózott valós idejű. Azonban ez az információ érhető el csak a [Auditnaplók](#audit-logs) a rendelés sikeres végrehajtása után.

## <a name="view-error-log-during-data-copy"></a>Adatok másolása során hiba napló megtekintése

Során az adatok Data Box vagy a Data Box nehéz való másolását egy hiba történt a fájl generál, ha az adatok másolását a merül fel.

### <a name="errorxml-file"></a>Error.xml fájl

Győződjön meg arról, hogy a másolási feladatokat hiba nélkül befejeződött. Ha a másolási folyamat közben hibák, töltse le a naplók a **csatlakozás és másolás** lapot.

- Ha egy fájlt, amely nem 512 bájt igazítva a Data Box egy felügyelt lemez mappájába másolja, a fájl feltöltve nem lapblob, az előkészítési tárfiókból. Látni fogja a naplókban hiba. Távolítsa el a fájlt, és másolja át a fájlt, amely pedig 512 bájt igazítva.
- Ha vhdx-fájlt, vagy dinamikus virtuális merevlemez, vagy egy különbséglemez VHD-t (ezek a fájlok nem támogatottak) másolja, látni fogja a naplókban hiba.

Íme egy példa, a *error.xml* a különböző hibákat, amikor a felügyelt lemezek másolása.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Íme egy példa, a *error.xml* a különböző hibákat lapblobok másolásakor.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Íme egy példa, a *error.xml* a különböző hibákat történő másolásakor a blokkblobok használatát támogatják.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Íme egy példa, a *error.xml* a különböző hibákat az Azure Files másolásakor.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

A fenti esetek mindegyikében javítsa ki a hibákat, mielőtt továbblép a következő lépéssel. Az adatok másolása a Data Boxba SMB vagy NFS protokoll használatával során kapott hibáival kapcsolatos további információkért lépjen [Data Box – hibaelhárítás és a Data Box nehéz problémák](data-box-troubleshoot.md). Információk a Data Box REST-en keresztül az adatok másolása során kapott, Ugrás [hibaelhárítása Data Box Blob tárolási problémák](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Vizsgálja meg a AJ szállításra való előkészítése során

Során készítse elő szállításra, az anyagok anyagjegyzék (AJ) vagy a jegyzékfájl létrehozása néven fájlok listája.

- Ez a fájl segítségével ellenőrizze a tényleges nevét és a Data Box átmásolt fájlok száma.
- Ez a fájl segítségével ellenőrizze a fájl tényleges mérete alapján.
- Ellenőrizze, hogy a *crc64* felel meg egy nem nulla értékű karakterlánc. <!--A null value for crc64 indicates that there was a reparse point error)-->

További információk a a során kapott előkészítése szállításra, ugorjon a [Data Box – hibaelhárítás és a Data Box nehéz problémák](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Anyagjegyzék vagy manifest fájl

Az Anyagjegyzék vagy a jegyzékfájlt a Data Box-eszközre másolt fájlok listáját tartalmazza. A AJ fájl rendelkezik a fájl nevéből és a megfelelő méret, valamint az ellenőrzőösszeg. A blokkblobok, lapblobok és Azure fájlok másolása a REST API-kon keresztül, valamint a felügyelt lemezek a Másolás a Data Box egy külön AJ-fájl jön létre. A szállításra való előkészítés alatt letöltheti a AJ fájlokat a helyi webes felületén az eszközön.

Ezeket a fájlokat is találhatók a Data Box-eszközre, és töltenek fel a társított storage-fiókot az Azure-adatközpontban.

### <a name="bom-file-format"></a>AJ fájlformátum

Anyagjegyzék vagy manifest fájl általános formátuma a következő:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Itt látható egy minta jegyzékfájl jönnek létre, ha az adatokat a rendszer átmásolta a block blob megosztásra a Data Box.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Az Anyagjegyzék vagy jegyzékfájlok is másolja az Azure storage-fiókhoz. Használhatja az Anyagjegyzék vagy ellenőrizze, hogy az Azure-bA feltöltött fájlok egyeznek-e az adatokat, hogy a rendszer átmásolta a Data Box-fájlok jegyzékfájl.

## <a name="review-copy-log-during-upload-to-azure"></a>Tekintse át a másolási naplót az Azure-ba való feltöltésekor

Az Azure-bA az adatfeltöltés során egy *copylog* jön létre.

### <a name="copylog"></a>Copylog

A Data Box szolgáltatás hoz létre minden egyes rendelés feldolgozása, *copylog* társított storage-fiókban. A *copylog* feltöltött fájlok teljes száma és a Data Box másolása az Azure storage-fiókot, amely hibát eredményező ki során az adatok fájlok száma.

Egy ciklikus redundancia ellenőrzése (CRC) számítási történik a feltöltés során, az Azure-bA. Az adatok másolását, és összehasonlítja az adatok feltöltése után CRCs. CRC eltérés azt jelzi, hogy a megfelelő fájlokat nem sikerült feltölteni.

Alapértelmezés szerint a naplók copylog nevű tárolóba kerülnek. A naplókat tárolja az alábbi elnevezési szabályt követik:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

A copylog elérési útját is megjelenik a **áttekintése** a portál panelén.

![Az Áttekintés panelen befejezésekor copylog elérési útja](media/data-box-logs/copy-log-path-1.png)

Az alábbi mintában Ez a cikk ismerteti egy copylog fájl általános formátumát a Data Box feltöltése sikeresen befejeződött:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Azure-bA feltöltendő hibákkal is elvégezheti.

![Az Áttekintés panelen, amikor hibákkal fejeződött be copylog elérési útja](media/data-box-logs/copy-log-path-2.png)

Íme egy példa, egy copylog, ahol a feltöltés hibákkal fejeződött be:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Adatok a törlés után lánc felügyeleti lánc naplók lekérése

Miután az adatokat a Data Box-lemezek a 800-88 NIST SP 1 változat irányelvek alapján való törlése, a lánc felügyeleti lánc naplók érhetők el. Ezeket a naplókat a naplók és a rendelési előzményeit tartalmazza. Az Anyagjegyzék vagy jegyzékfájlok is másolja a vizsgálati naplók.

### <a name="audit-logs"></a>Naplók

Auditnaplók bekapcsolási információt tartalmaznak, és ha az Azure-beli adatközpont-en kívül tartalmazó fájlmegosztás elérését a Data Box vagy a Data Box (nagy erőforrásigényű). Ezek a naplók a következő helyen találhatók: `storage-account/azuredatabox-chainofcustodylogs`

Íme egy példa a Data Box naplók:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Rendelés előzményeinek letöltése

Rendelési előzmények az Azure Portalon érhető el. Ha a rendelés befejeződik, és az eszköz (adatok törlését lemezeiről) a törlés befejeződött, majd nyissa meg az eszközt ahhoz, és keresse meg **rendelés részletei**. ** Töltse le a rendelési előzmények** lehetőség érhető el. További információkért lásd: [töltse le a rendelési előzmények](data-box-portal-admin.md#download-order-history).

Ha a rendelési előzmények görgetve láthatja:

- A szolgáltató nyomkövetési adatokat az eszközhöz.
- Az események *SecureErase* tevékenység. Ezek az események felel meg a lemezen lévő adatok törlése.
- Data Box log hivatkozásokat. Az elérési utakat a *auditnaplók*, *copylogs*, és *AJ* fájlok jelennek meg.

Íme egy példa a rendelési előzmények napló az Azure Portalról:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [a Data Box és a Data Box gyakori hibáinak elhárítása](data-box-troubleshoot.md).
