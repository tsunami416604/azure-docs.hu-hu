---
title: Az Azure Data Box Gateway biztonsági |} A Microsoft Docs
description: A biztonsági és adatvédelmi szolgáltatásait ismerteti, amelyek védelme az Azure Data Box átjáró virtuális eszköz-, szolgáltatás, és, a helyszíni és a felhőben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754314"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Az Azure Data Box Gateway biztonsági és adatvédelmi

Biztonsági esetén fő szempont, Ön bevezetése új technológia, különösen akkor, ha a technológiát a bizalmas vagy szellemi tulajdont képező adatokat használatos. Az Azure Data Box-átjáró segítségével, győződjön meg arról, hogy csak hitelesített entitások megtekintése, módosítása vagy törlése az adatok.

Ez a cikk ismerteti az Azure Data Box Gateway biztonsági funkciók, amelyek segítenek megvédeni a megoldás-összetevők és a bennük tárolt adatokat.

A Data Box Gateway megoldást, amelyek az egymással interakcióban négy fő összetevőből áll:

- **Box-Gateway-szolgáltatás, az Azure-ban üzemeltetett**. A felügyeleti erőforrás, amellyel az eszköz rendelés létrehozása konfigurálják az eszközt, és nyomon követésével a rendelés befejezését.
- **Data Box-átjáróeszköz**. A virtuális eszköz, amely a hipervizor az Ön által megadott rendszer üzembe helyezi. A virtuális eszköz szolgál a helyszíni adatok importálása az Azure-bA.
- **Az eszközhöz csatlakoztatott ügyfelek /-gazdagépekre**. Az ügyfél az infrastruktúra, amely a Data Box átjáróeszköz csatlakozik, és adatokat tartalmaznak, amelyek kell védeni.
- **Felhőalapú tárolás**. A hely, az Azure-felhőplatformon adatok tárolására. Ez a hely általában az Ön által létrehozott Data Box-Gateway-erőforráshoz társított storage-fiókot.


## <a name="data-box-gateway-service-protection"></a>Box-átjáró szolgáltatás adatvédelem

A Data Box-átjáró szolgáltatás felügyeleti szolgáltatása az Azure-ban üzemel. A szolgáltatás konfigurálása és kezelése az eszköz segítségével.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box-átjáró elleni eszközvédelem

A Data Box átjáróeszköz, amely ki van építve a hipervizor az Ön által a helyszíni rendszer a virtuális eszköz. Az eszköz segít az adatok küldése az Azure-bA. Az eszköz:

- Szüksége van egy aktiválási kulcsot a Data Box Edge/Data Box Gateway szolgáltatás eléréséhez.
- Van minden alkalommal eszköz jelszóval védett.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Az eszköz aktiválási kulccsal védelme

Csatlakozás a Data Box-Gateway-szolgáltatás, amely az Azure-előfizetésben hoz létre csak egy engedéllyel rendelkező Data Box Gateway eszköz engedélyezett. Egy eszköz hitelesítéséhez, kell használható aktiválási kulcs aktiválásához az eszköz a Data Box Gateway szolgáltatással.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

További információkért lásd: [aktiválási kulcs beszerzése](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Jelszó-n keresztül az eszköz védelme

Jelszavak győződjön meg arról, hogy csak a jogosult felhasználók is elérhetők az adatok. Data Box-átjáró eszközök rendszerindítás zárolt állapotban.

A következőket teheti:

- A helyi webes felhasználói felületen, az eszköz egy böngészőből csatlakozzon, és adja meg, hogy jelentkezzen be az eszköz jelszó.
- Távoli csatlakozás az az eszköz PowerShell-felületünkön HTTP protokollon keresztül. Távfelügyelet alapértelmezés szerint be van kapcsolva. Az eszköz jelszavának jelentkezzen be az eszközt, majd adja meg. További információkért lásd: [távolról csatlakozhat a Data Box átjáróeszköz](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Használja a helyi webes felhasználói Felületét, hogy [módosítsa a jelszót](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Ha megváltoztatja a jelszót, mindenképpen az összes távelérési felhasználók értesítése, hogy ne kelljen bejelentkezésnél.


## <a name="protect-your-data"></a>Az adatok védelme

Ez a szakasz ismerteti a Data Box Gateway biztonsági átvitel és a tárolt adatok védelme érdekében.

### <a name="protect-data-at-rest"></a>Inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Továbbított adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Storage-fiókok keresztül adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgatás, majd [a tárfiókkulcsok szinkronizálása](data-box-gateway-manage-shares.md#sync-storage-keys) rendszeresen segítségével védelmet biztosíthatnak a storage-fiók legyen a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box-átjáró szolgáltatás adatokat gyűjt személyes adatokat a következő esetekben:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A felhasználók férhetnek hozzá, vagy törli a megosztást listájának megtekintéséhez kövesse [a Box átjáró-megosztások kezelése](data-box-gateway-manage-shares.md).

További információkért tekintse át a Microsoft adatvédelmi szabályzatának az a [biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[A Data Box-Gateway-eszköz üzembe helyezése](data-box-gateway-deploy-prep.md)
