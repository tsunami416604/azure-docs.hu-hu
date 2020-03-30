---
title: Az Azure Data Box, az Azure Data Box Heavy eseményeinek nyomon követése és naplózása| Microsoft dokumentumok
description: Bemutatja, hogyan követheti nyomon és naplózhatja az eseményeket az Azure Data Box és az Azure Data Box Heavy rendelés különböző szakaszaiban.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 72e1d3b0ad72b1e68b88eb0550cbe839ade9d929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260019"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Az Azure Data Box és az Azure Data Box Heavy nyomon követése és eseménynaplózása

A Data Box vagy a Data Box Heavy rendelés a következő lépéseken megy keresztül: rendelés, beállítás, adatmásolás, visszaküldés, feltöltés az Azure-ba és az ellenőrzés, valamint az adatok törléséje. A sorrend minden egyes lépésének megfelelően több műveletet is végrehajthat a rendeléshez való hozzáférés szabályozására, az események naplózására, a sorrend nyomon követésére és a létrehozott különböző naplók értelmezésére.

Az alábbi táblázat a Data Box vagy a Data Box Heavy rendelési lépéseit, valamint az egyes lépések során a rendelés nyomon követéséhez és naplózásához rendelkezésre álló eszközöket tartalmazza.

| Data Box rendelési szakasza       | A nyomon követésésre és auditálásra eszköz                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Rendelés létrehozása               | [Hozzáférés-vezérlés beállítása a rendelésen az RBAC-on keresztül](#set-up-access-control-on-the-order)                                                    |
| Feldolgozott rendelés            | [A rendelés nyomon követése a](#track-the-order) <ul><li> Azure portál </li><li> A szállítmányozó honlapja </li><li>E-mail-értesítések</ul> |
| Eszköz beállítása              | A [tevékenységnaplókba](#query-activity-logs-during-setup) bejelentkezett eszközhitelesítő adatok hoz való hozzáférés                                              |
| Adatok másolása az eszközre        | [ *Hiba.xml* fájlok megtekintése](#view-error-log-during-data-copy) adatmásoláshoz                                                             |
| A szállítás előkészítése            | [Az anyagjegyzékfájlok](#inspect-bom-during-prepare-to-ship) vagy az eszközön lévő jegyzékfájlok vizsgálata                                      |
| Adatok feltöltése az Azure-ba       | Az Azure adatközpontban való adatfeltöltés során előforduló hibák [másolási naplóinak áttekintése](#review-copy-log-during-upload-to-azure)                         |
| Adattörlés eszközről   | [Felügyeleti naplók láncának megtekintése,](#get-chain-of-custody-logs-after-data-erasure) beleértve a naplónaplókat és a rendelési előzményeket                |

Ez a cikk részletesen ismerteti a különböző mechanizmusok at vagy eszközöket a Data Box vagy a Data Box Heavy rendelés nyomon követéséhez és naplózásához. Az ebben a cikkben szereplő információk a Data Box és a Data Box Heavy mezőre egyaránt vonatkoznak. A következő szakaszokban a Data Box-ra való hivatkozások a Data Box Heavy-ra is vonatkoznak.

## <a name="set-up-access-control-on-the-order"></a>Hozzáférés-vezérlés beállítása a rendelésen

Beállíthatja, hogy a rendelés első létrehozásakor ki férhet hozzá a rendeléshez. Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök beállítása különböző hatókörökben az Adatmező-sorrendhöz való hozzáférés szabályozásához. Az RBAC szerepkör határozza meg a hozzáférés típusát – írás-olvasás, csak olvasható, írás-olvasás a műveletek egy részhalmazához.

Az Azure Data Box szolgáltatáshoz definiálható két szerepkör a következő:

- **Data Box Reader** – írásvédett hozzáféréssel rendelkezik a hatókör által meghatározott rendelés(ek)hez. Csak a rendelés részleteit tekinthetik meg. Nem férhetnek hozzá a tárfiókokhoz kapcsolódó egyéb részletekhez, és nem szerkeszthetik a rendelés részleteit, például a címet és így tovább.
- **Data Box Közreműködő** - csak akkor hozhat létre rendelést az adatok adott tárfiókba történő átviteléhez, *ha már rendelkeznek írási hozzáféréssel egy tárfiókhoz.* Ha nem férnek hozzá a tárfiókhoz, még egy Data Box-sorrendet sem hozhatnak létre az adatok a fiókba másolásához. Ez a szerepkör nem határozza meg a tárfiókkal kapcsolatos engedélyeket, és nem biztosít hozzáférést a tárfiókokhoz.  

A rendeléshez való hozzáférés korlátozásához a következőket teheti:

- Szerepkör hozzárendelése rendelési szinten. A felhasználó csak azadott Adatdoboz-sorrenddel való interakcióra jogosult szerepkörök által meghatározott engedélyekkel rendelkezik, és semmi más.
- Rendeljen hozzá egy szerepkört az erőforráscsoport szintjén, a felhasználó hozzáfér az erőforráscsoporton belüli összes Adatdoboz-rendeléshez.

A javasolt RBAC-használatról az [RBAC ajánlott eljárások](../role-based-access-control/overview.md#best-practice-for-using-rbac)című témakörben talál további információt.

## <a name="track-the-order"></a>A megrendelés nyomon követése

A rendelést az Azure Portalon és a szállítmányozó webhelyén keresztül követheti nyomon. A data box rendelés nyomon követésére bármikor a következő mechanizmusok állnak elő:

- Ha nyomon szeretné követni a sorrendet, ha az eszköz az Azure-adatközpontban vagy a helyszínen, nyissa meg a **Data Box rendelési > áttekintése az** Azure Portalon.

    ![Rendelésállapotának és nyomon követésének megtekintése](media/data-box-logs/overview-view-status-1.png)

- A megrendelés nyomon követéséhez, amíg az eszköz szállítás alatt van, keresse fel a regionális fuvarozó webhelyét, például a UPS egyesült államokbeli webhelyét. Adja meg a rendeléshez társított követési számot.
- A Data Box e-mail értesítéseket is küld, amikor a rendelés állapota a rendelés létrehozásakor megadott e-mailek alapján változik. Az adatdoboz-rendelések összes állapotának listáját a [Rendelésállapot megtekintése című](data-box-portal-admin.md#view-order-status)témakörben tekintheti meg. A rendeléshez társított értesítési beállítások módosításáról az [Értesítés részleteinek szerkesztése](data-box-portal-admin.md#edit-notification-details)című témakörben olvashat.

## <a name="query-activity-logs-during-setup"></a>Lekérdezési tevékenységnaplók a telepítés során

- A Data Box zárolt állapotban érkezik a helyiségbe. Használhatja az azure portalon elérhető eszköz hitelesítő adatait a rendeléshez.  

    Ha be van állítva egy adatdoboz, előfordulhat, hogy tudnia kell, hogy ki fért hozzá az eszköz hitelesítő adataihoz. Annak kiderítéséhez, hogy ki fért hozzá az **Eszköz hitelesítő adatok** panelhez, lekérdezheti a tevékenységnaplókat.  Minden olyan művelet, amely magában foglalja az **eszköz adatainak** elérését > hitelesítő adatok panelt, műveletként `ListCredentials` be van jelentkezve a tevékenységnaplókba.

    ![Tevékenységnaplók lekérdezése](media/data-box-logs/query-activity-log-1.png)

- Minden a Data Box-ba való bejelentkezés valós időben kerül naplózásra. Ez az információ azonban csak a rendelés sikeres befejezése után érhető el a [naplózási naplókban.](#audit-logs)

## <a name="view-error-log-during-data-copy"></a>Hibanapló megtekintése az adatmásolás során

Az adatok Data Box vagy Data Box Heavy fájlba másolása során hibafájl jön létre, ha bármilyen probléma merül fel a másolt adatokkal kapcsolatban.

### <a name="errorxml-file"></a>Error.xml fájl

Győződjön meg arról, hogy a másolási feladatok hiba nélkül befejeződtek. Ha a másolási folyamat során hibák lépnek fel, töltse le a naplókat a **Csatlakozás és másolás** lapról.

- Ha nem 512 bájtos fájlt másolt a Data Box egyik felügyelt lemezmappájához, a fájl nem kerül feltöltésre lapblobként az átmeneti tárfiókba. Hibaüzenet jelenik meg a naplókban. Távolítsa el a fájlt, és másolja az 512 bájtos fájligazítást.
- Ha egy VHDX-et, egy dinamikus virtuális merevlemezt vagy egy differencing virtuális merevlemezt másolt (ezek a fájlok nem támogatottak), hibaüzenet jelenik meg a naplókban.

Íme egy példa a *error.xml* fájlból a különböző hibákról a felügyelt lemezekre történő másolássorán.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Íme egy példa a *error.xml* a különböző hibák, amikor a lap blobok.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Íme egy példa a *error.xml* a különböző hibák másolásakor a blobok.

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

Íme egy példa a *error.xml* a különböző hibák, amikor az Azure Files másolása.

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

A fenti esetek mindegyikében oldja meg a hibákat, mielőtt továbblépne a következő lépésre. Az SMB vagy NFS protokollon keresztül a Data Box ba történő adatmásolás során kapott hibákról a [Data Box és a Data Box Heavy problémák elhárítása című témakörben](data-box-troubleshoot.md)talál további információt. A REST-en keresztül a Data Box ba történő adatmásolás során kapott hibákról az [Adatdobozblob-tárolási problémák elhárítása című témakörben](data-box-troubleshoot-rest.md)talál tájékoztatást.

## <a name="inspect-bom-during-prepare-to-ship"></a>Anyagjegyzék ellenőrzése a szállításelőkészítése során

A szállítás előkészítése során létrejön az anyagjegyzék vagy a jegyzékfájl néven ismert fájlok listája.

- Ezzel a fájllal ellenőrizheti a tényleges neveket és az adatdobozba másolt fájlok számát.
- Ezzel a fájllal ellenőrizheti a fájlok tényleges méretét.
- Ellenőrizze, hogy a *crc64* nem nulla karakterláncnak felel-e meg. <!--A null value for crc64 indicates that there was a reparse point error)-->

A szállítás előkészítése során kapott hibákról a Data Box és a [Data Box Heavy problémák elhárítása című témakörben](data-box-troubleshoot.md)talál további információt.

### <a name="bom-or-manifest-file"></a>Anyagjegyzék- vagy jegyzékfájl

Az anyagjegyzék- vagy jegyzékfájl tartalmazza a Data Box eszközre másolt összes fájl listáját. Az Anyagjegyzékfájl fájlnevekkel és a megfelelő méretekkel, valamint az ellenőrzőösszeggel rendelkezik. Egy külön anyagjegyzék-fájl jön létre a blokkblobok, lapblobok, Azure-fájlok, másolása a REST API-kon keresztül, és a másolás a felügyelt lemezek a Data Box. Az anyagjegyzékfájlokat az eszköz helyi webes felhasználói felületéről töltheti le a szállításelőkészítése során.

Ezek a fájlok is a Data Box-eszközön találhatók, és az Azure-adatközpont társított tárfiókjába kerülnek feltöltésre.

### <a name="bom-file-format"></a>Anyagjegyzék fájlformátuma

Az anyagjegyzék- vagy jegyzékfájl általános formátuma a következő:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Itt van egy példa egy jegyzékfájl ból létrehozott, amikor az adatok másolása a blokk blob megosztás a Data Box.

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

Az anyagjegyzék- vagy jegyzékfájlfájlok is átmásolva vannak az Azure storage-fiókba. Az anyagjegyzék- vagy jegyzékfájlok segítségével ellenőrizheti, hogy az Azure-ba feltöltött fájlok megegyeznek-e az adatdobozba másolt adatokkal.

## <a name="review-copy-log-during-upload-to-azure"></a>Másolatnapló áttekintése az Azure-ba való feltöltés során

Az Azure-ba való adatfeltöltés során létrejön egy másolási napló.

### <a name="copy-log"></a>Napló másolása

A Data Box szolgáltatás minden egyes feldolgozott rendeléshez létrehozza a másolási naplót a társított tárfiókban. A másolási napló a feltöltött fájlok teljes számát és a Data Boxból az Azure storage-fiókba történő másolása során kinyomtatott fájlok számát tartalmaz.

A ciklikus redundancia-ellenőrzés (CRC) számítása az Azure-ba való feltöltés során történik. Az adatmásolatból és az adatok feltöltésének összehasonlítása után a CRC-k. A CRC-eltérés azt jelzi, hogy a megfelelő fájlok feltöltése sikertelen volt.

Alapértelmezés szerint a naplók egy tároló `copylog`nevű tárolóba kerülnek. A naplók tárolása a következő elnevezési konvencióval van tárolva:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

A másolási napló elérési útja is megjelenik a portál **áttekintése** panelen.

![A napló áttekinthető panelen való másolásának elérési útja, ha elkészült](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>A feltöltés sikeresen befejeződött 

Az alábbi minta a sikeresen befejeződött adatdoboz-feltöltés idomainak általános formátumát ismerteti:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>A feltöltés hibákkal fejeződött be 

Az Azure-ba való feltöltés hibákkal is kiegészíthető.

![A napló áttekinthető panelen való másolásának elérési útja, ha hibákkal van eltöltve](media/data-box-logs/copy-log-path-2.png)

Íme egy példa egy másolatnaplóra, ahol a feltöltés hibákkal fejeződött be:

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
### <a name="upload-completed-with-warnings"></a>A feltöltés figyelmeztetésekkel fejeződött be

Az Azure-ba való feltöltés figyelmeztetésekkel egészül ki, ha az adatok olyan tároló-/blob-/fájlnevekkel vannak ellátva, amelyek nem feleltek meg az Azure elnevezési konvencióinak, és a neveket módosították az adatok Azure-ba való feltöltéséhez.

![A napló áttekinthető panelen való másolásának elérési útja, ha azok figyelmeztetésekkel vannak kitöltve](media/data-box-logs/copy-log-path-3.png)

Íme egy példa egy másolati naplóra, ahol az Azure-névhasználati konvencióknak nem megfelelő tárolókat átnevezték az Azure-ba való adatfeltöltés során.

A tárolók új egyedi nevei formátumban vannak, `DataBox-GUID` és a tároló adatai az új átnevezett tárolóba kerülnek. A másolási napló a tároló régi és új tárolónevét adja meg.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Íme egy példa egy másolati naplóra, ahol a blobokat vagy a fájlokat, amelyek nem feleltek meg az Azure elnevezési konvencióknak, átnevezték az Azure-ba való adatfeltöltés során. Az új blob- vagy fájlnevek a tároló relatív elérési útjának SHA256 kivonatolására konvertálódnak, és a céltípus alapján kerülnek feltöltésre az elérési útra. A cél lehet blokk blobok, lapblobok vagy Az Azure Files.

A `copylog` megadott a régi és az új blob vagy fájl nevét és az elérési utat az Azure-ban.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>A felügyeleti lánc naplóinak beszereznie az adatok törlését követően

Miután az adatok a NIST SP 800-88 Revision 1 irányelveinek megfelelően törlődnek a Data Box lemezekről, a felügyeleti naplók láncolata elérhető. Ezek a naplók tartalmazzák a naplókat és a rendelési előzményeket. Az anyagjegyzék- vagy jegyzékfájlok at is másolja a naplóval.

### <a name="audit-logs"></a>Naplók

A naplónaplók információkat tartalmaznak adata box vagy a Data Box Heavy megosztásainak be- és eléréséről, ha az az Azure adatközponton kívül található. Ezek a naplók a következő helyen találhatók:`storage-account/azuredatabox-chainofcustodylogs`

Íme egy példa a napló egy adatmező:

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

A rendelési előzmények elérhetők az Azure Portalon. Ha a rendelés befejeződött, és az eszköz karbantartása (adattörlés a lemezekről) befejeződött, akkor lépjen a készülék megrendeléséhez, és keresse meg a **Rendelés részleteit**. Elérhető a **Rendelés előzményeinek letöltése** lehetőség. További információ: [A rendelési előzmények letöltése.](data-box-portal-admin.md#download-order-history)

Ha végiggörgeti a rendelési előzményeket, a következőket látja:

- Az eszköz mobilszolgáltatói követési adatai.
- Események *SecureErase* tevékenységgel. Ezek az események a lemezen lévő adatok törlésének felelnek meg.
- Adatmező naplólinkjei. A *naplónaplók,* *a másolási naplók*és az *anyagjegyzékfájlok* elérési útjai megjelennek.

Íme egy példa az Azure Portal rendelési előzmények naplójából:

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

- További információ a [Data Box és a Data Box Heavy hibáinak elhárításáról.](data-box-troubleshoot.md)
