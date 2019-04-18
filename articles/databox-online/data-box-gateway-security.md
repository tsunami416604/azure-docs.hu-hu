---
title: Az Azure Data Box Gateway biztonsági |} A Microsoft Docs
description: Ismerteti a biztonság és adatvédelem az Azure Data Box átjáró virtuális eszköz, a szolgáltatás és a helyszíni és felhőbeli adatok védelmére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685900"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Az Azure Data Box Gateway biztonsági és adatvédelmi

Biztonsági esetén a fő szempont bevezetése új technológia, különösen akkor, ha a technológiát a bizalmas vagy szellemi tulajdont képező adatokat használatos. A Microsoft Azure Data Box Gateway megoldás segítségével, győződjön meg arról, hogy csak hitelesített entitások is megtekintése, módosítása vagy törlése az adatok.

Ez a cikk ismerteti az Azure Data Box Gateway biztonsági funkciók, amelyek mindegyike a megoldás-összetevőket és az azokon tárolt adatok védelme érdekében.

A Data Box Gateway megoldást, amelyek az egymással interakcióban négy fő összetevőből áll:

- **Data Box átjáró szolgáltatás az Azure-ban üzemeltetett** – a felügyeleti erőforrás, amellyel az eszköz rendelés létrehozása, az eszköz konfigurálása és a rendelés nyomon követésével befejezését.
- **Data Box-átjáróeszköz** – a virtuális eszköz, a rendszer a megadott, a hipervizorban lévő kiépített. A virtuális eszköz szolgál a helyszíni adatok importálása az Azure-bA.
- **Az eszközhöz csatlakoztatott ügyfelek /-gazdagépekre** – az ügyfelek az infrastruktúra, amely a Data Box átjáróeszköz csatlakozik, és adatokat tartalmaznak, amelyek kell védeni.
- **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik. Ez a hely általában a létrehozott Data Box Gateway-erőforráshoz társított storage-fiók.


## <a name="data-box-gateway-service-protection"></a>Box-átjáró szolgáltatás adatvédelem

A Data Box-átjáró szolgáltatás nem a Microsoft Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás konfigurálása és kezelése az eszköz segítségével.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box-átjáró elleni eszközvédelem

A Data Box-átjáró a hipervizor az Ön által a helyszíni rendszer kiépített virtuális eszközről. Az eszköz segít az adatok küldése az Azure-bA. Az eszköz:

- Szüksége van egy aktiválási kulcsot a Data Box Edge/Data Box Gateway szolgáltatás eléréséhez.
- Van minden alkalommal eszköz jelszóval védett.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Az eszköz aktiválási kulccsal védelme

Csatlakozás a Data Box-Gateway-szolgáltatás, amely az Azure-előfizetésében létrehozott csak egy engedéllyel rendelkező Data Box Gateway eszköz engedélyezett. Egy eszköz hitelesítéséhez, az aktiválási kulcs aktiválásához az eszköz a Data Box Gateway szolgáltatással kell használnia.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

További információért ugorjon [aktiválási kulcs beszerzése](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Jelszó-n keresztül az eszköz védelme

Jelszavak győződjön meg arról, hogy az adatok csak a jogosult felhasználók számára is elérhető. Data Box-átjáró eszközök rendszerindítás zárolt állapotban.

A következőket teheti:

- A helyi webes felhasználói felületen, az eszköz egy böngészőből csatlakozzon, és adja meg egy jelszót az eszköz bejelentkezik.
- Távoli csatlakozás az eszköz PowerShell-felületén az HTTP-n keresztül. Távfelügyelet alapértelmezés szerint be van kapcsolva. Majd adja meg, jelentkezzen be az eszköz az eszköz jelszavát. További információért ugorjon [távolról csatlakozhat a Data Box átjáróeszköz](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Használja a helyi webes felhasználói Felületét, hogy [módosítsa a jelszót](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Ha megváltoztatja a jelszót, mindenképpen az összes távelérési a felhasználók értesítése, hogy azok nem tapasztalnak egy bejelentkezési hiba.


## <a name="protect-the-data"></a>Az adatok védelme

Ez a szakasz ismerteti a Data Box Gateway biztonsági az átvitt adatokat és a tárolt adatok védelméhez.

### <a name="protect-data-at-rest"></a>Inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Továbbított adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Storage-fiókok keresztül adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgatás, majd [a tárfiókkulcsok szinkronizálása](data-box-gateway-manage-shares.md#sync-storage-keys) rendszeresen annak érdekében, hogy a tárfiók nem érhető el a jogosulatlan felhasználók.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box-átjáró szolgáltatás adatokat gyűjt személyes adatokat a következő kulcs példányok:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A felhasználók férhetnek hozzá, vagy törli a megosztást listájának megtekintéséhez kövesse [a Box átjáró-megosztások kezelése](data-box-gateway-manage-shares.md).

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[A Data Box-Gateway-eszköz üzembe helyezése](data-box-gateway-deploy-prep.md).
