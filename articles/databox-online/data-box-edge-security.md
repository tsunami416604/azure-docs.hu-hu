---
title: Az Azure Data Box Edge biztonsági |} A Microsoft Docs
description: A biztonsági és adatvédelmi szolgáltatásait ismerteti, amelyek védelme az Azure Data Box peremhálózati eszköz-, szolgáltatás, és a helyszíni és a felhőben.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756230"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Az Azure Data Box Edge biztonság és adatvédelem

Biztonsági esetén fő szempont, Ön bevezetése új technológia, különösen akkor, ha a technológiát a bizalmas vagy szellemi tulajdont képező adatokat használatos. Az Azure Data Box Edge segítségével, győződjön meg arról, hogy csak hitelesített entitások megtekintése, módosítása vagy törlése az adatok.

Ez a cikk ismerteti a Data Box Edge biztonsági funkciók, amelyek segítenek megvédeni a megoldás-összetevők és a bennük tárolt adatokat.

Az Azure Data Box Edge egymással kommunikáló négy fő összetevőből áll:

- **Box Edge-szolgáltatás, az Azure-ban üzemeltetett**. A felügyeleti erőforrás, amellyel az eszköz rendelés létrehozása konfigurálják az eszközt, és nyomon követésével a rendelés befejezését.
- **Data Box peremhálózati eszköz**. Az adatátviteli eszközt, amely tartalmazza a szükséges, hogy így a helyszíni adatok Azure-bA importálhatja.
- **Az eszközhöz csatlakoztatott ügyfelek /-gazdagépekre**. Az ügyfél az infrastruktúra, amely a Data Box peremhálózati eszköz csatlakozik, és adatokat tartalmaznak, amelyek kell védeni.
- **Felhőalapú tárolás**. A hely, az Azure-felhőplatformon adatok tárolására. Ez a hely általában az Ön által létrehozott Data Box Edge erőforrás társított tárfiókba.

## <a name="data-box-edge-service-protection"></a>Box Edge szolgáltatás adatvédelem

A Data Box Edge szolgáltatás felügyeleti szolgáltatása az Azure-ban üzemel. A szolgáltatás konfigurálása és kezelése az eszköz segítségével.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Adatvédelem Box peremhálózati eszköz

A Data Box peremhálózati eszköz egy helyszíni eszköz, amely segít az adatok átalakítása a helyi feldolgozásra, és elküldi azt az Azure-ban. Az eszköz:

- Szüksége van egy aktiválási kulcsot a Data Box Edge szolgáltatás eléréséhez.
- Van minden alkalommal eszköz jelszóval védett.
- A zárolt eszköz van. Az eszköz BMC- és BIOS-ban, jelszóval védett. A BIOS-ban korlátozott felhasználói hozzáférés védelmét.
- A biztonságos rendszerindítás engedélyezve van.
- A Windows Defender Device Guard futtatja. A Device Guard lehetővé teszi csak a megbízható alkalmazások futtathatók, amelyeket a kódintegritás szabályzatokat.

### <a name="protect-the-device-via-activation-key"></a>Az eszköz aktiválási kulccsal védelme

Csatlakozás a Data Box Edge szolgáltatást az Azure-előfizetésében létrehozott csak jogosult Data Box peremhálózati eszköz engedélyezett. Egy eszköz hitelesítéséhez, meg kell használható aktiválási kulcs aktiválásához az eszköz a Data Box Edge szolgáltatással.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

További információkért lásd: [aktiválási kulcs beszerzése](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Jelszó-n keresztül az eszköz védelme

Jelszavak győződjön meg arról, hogy csak a jogosult felhasználók is elérhetők az adatok. Data Box peremhálózati eszközök rendszerindítás zárolt állapotban.

A következőket teheti:

- A helyi webes felhasználói felületen, az eszköz egy böngészőből csatlakozzon, és adja meg, hogy jelentkezzen be az eszköz jelszó.
- Távoli csatlakozás az eszköz PowerShell-felületén az HTTP-n keresztül. Távfelügyelet alapértelmezés szerint be van kapcsolva. Az eszköz jelszavának jelentkezzen be az eszközt, majd adja meg. További információkért lásd: [távolról csatlakozhat a Data Box peremhálózati eszköz](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Használja a helyi webes felhasználói Felületét, hogy [módosítsa a jelszót](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Ha megváltoztatja a jelszót, mindenképpen az összes távelérési felhasználók értesítése, hogy ne kelljen bejelentkezésnél.

## <a name="protect-your-data"></a>Az adatok védelme

Ez a szakasz ismerteti a Data Box Edge biztonsági átvitel és a tárolt adatok védelme érdekében.

### <a name="protect-data-at-rest"></a>Inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Továbbított adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Storage-fiókok keresztül adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgatás, majd [a tárfiókkulcsok szinkronizálása](data-box-edge-manage-shares.md#sync-storage-keys) rendszeresen segítségével védelmet biztosíthatnak a storage-fiók legyen a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box Edge szolgáltatás adatokat gyűjt személyes adatokat a következő esetekben:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A felhasználók férhetnek hozzá, vagy törli a megosztást listájának megtekintéséhez kövesse [a Data Box Edge-megosztások kezelése](data-box-edge-manage-shares.md).

További információkért tekintse át a Microsoft adatvédelmi szabályzatának az a [biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[A Data Box Edge-eszköz üzembe helyezése](data-box-edge-deploy-prep.md)
