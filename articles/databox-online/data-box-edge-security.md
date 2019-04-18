---
title: Az Azure Data Box Edge biztonsági |} A Microsoft Docs
description: Ismerteti a biztonság és adatvédelem az Azure Data Box Edge eszköz-, szolgáltatás és a helyszíni és felhőbeli adatok védelmére.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682100"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Az Azure Data Box Edge biztonság és adatvédelem

Biztonsági esetén a fő szempont bevezetése új technológia, különösen akkor, ha a technológiát a bizalmas vagy szellemi tulajdont képező adatokat használatos. A Microsoft Azure Data Box peremhálózati megoldás segítségével, győződjön meg arról, hogy csak hitelesített entitások is megtekintése, módosítása vagy törlése az adatok.

Ez a cikk ismerteti a Data Box Edge biztonsági funkciók, amelyek mindegyike a megoldás-összetevőket és az azokon tárolt adatok védelme érdekében.

Az Azure Data Box peremhálózati megoldás egymással kommunikáló négy fő összetevőből áll:

- **Data Box Edge szolgáltatás Azure-ban üzemeltetett** – a felügyeleti erőforrás, amellyel az eszköz rendelés létrehozása, az eszköz konfigurálása és a rendelés nyomon követésével befejezését.
- **Data Box peremhálózati eszköz** – az átvitel eszköz, amely tartalmazza a szükséges, hogy a helyszíni adatok importálása az Azure-ba való.
- **Az eszközhöz csatlakoztatott ügyfelek /-gazdagépekre** – az ügyfelek az infrastruktúra, amely a Data Box peremhálózati eszköz csatlakozik, és adatokat tartalmaznak, amelyek kell védeni.
- **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik. Ez a hely általában a létrehozott Data Box Edge-erőforráshoz társított storage-fiók.

## <a name="data-box-edge-service-protection"></a>Box Edge szolgáltatás adatvédelem

A Data Box Edge szolgáltatás nem a Microsoft Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás konfigurálása és kezelése az eszköz segítségével.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Adatvédelem Box peremhálózati eszköz

A Data Box peremhálózati eszköz egy helyszíni eszköz, amely segít az adatok átalakítása a helyi feldolgozásra, és elküldi azt az Azure-ban. Az eszköz:

- Szüksége van egy aktiválási kulcsot a Data Box Edge szolgáltatás eléréséhez.
- Van minden alkalommal eszköz jelszóval védett.
- A zárolt eszköz van. Az eszköz BMC- és BIOS-ban, a BIOS-ban korlátozott felhasználói hozzáféréssel rendelkező jelszóval védett.
- A biztonságos rendszerindítás engedélyezve van.
- A Windows Defender Device Guard futtatja. A Device Guard lehetővé teszi, hogy csak az a kódintegritási házirendekben meghatározott megbízható alkalmazások futtathatók.

### <a name="protect-the-device-via-activation-key"></a>Az eszköz aktiválási kulccsal védelme

Csatlakozás a Data Box Edge szolgáltatást az Azure-előfizetésében létrehozott csak jogosult Data Box peremhálózati eszköz engedélyezett. Egy eszköz hitelesítéséhez, az aktiválási kulcs aktiválásához az eszköz a Data Box Edge szolgáltatással kell használnia.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

További információért ugorjon [aktiválási kulcs beszerzése](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Jelszó-n keresztül az eszköz védelme

Jelszavak győződjön meg arról, hogy az adatok csak a jogosult felhasználók számára is elérhető. Data Box peremhálózati eszközök rendszerindítás zárolt állapotban.

A következőket teheti:

- A helyi webes felhasználói felületen, az eszköz egy böngészőből csatlakozzon, és adja meg egy jelszót az eszköz bejelentkezik.
- Távoli csatlakozás az eszköz PowerShell-felületén az HTTP-n keresztül. Távfelügyelet alapértelmezés szerint be van kapcsolva. Majd adja meg, jelentkezzen be az eszköz az eszköz jelszavát. További információért ugorjon [távolról csatlakozhat a Data Box peremhálózati eszköz](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Használja a helyi webes felhasználói Felületét, hogy [módosítsa a jelszót](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Ha megváltoztatja a jelszót, mindenképpen az összes távelérési a felhasználók értesítése, hogy azok nem tapasztalnak egy bejelentkezési hiba.

## <a name="protect-the-data"></a>Az adatok védelme

Ez a szakasz ismerteti a Data Box Edge biztonsági funkciók, amelyek az átvitt adatokat és a tárolt adatok védelméhez.

### <a name="protect-data-at-rest"></a>Inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Továbbított adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Storage-fiókok keresztül adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgatás, majd [a tárfiókkulcsok szinkronizálása](data-box-edge-manage-shares.md#sync-storage-keys) rendszeresen annak érdekében, hogy a tárfiók nem érhető el a jogosulatlan felhasználók.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box Edge szolgáltatás adatokat gyűjt személyes adatokat a következő kulcs példányok:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A felhasználók férhetnek hozzá, vagy törli a megosztást listájának megtekintéséhez kövesse [a Data Box Edge-megosztások kezelése](data-box-edge-manage-shares.md).

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[A Data Box Edge-eszköz üzembe helyezése](data-box-edge-deploy-prep.md).
