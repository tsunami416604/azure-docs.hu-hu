---
title: Az Azure Data Box Edge biztonsága | Microsoft dokumentumok
description: Az Azure Data Box Edge-eszköz, a szolgáltatás és az adatok helyszíni és a felhőbeli védelmét szolgáló biztonsági és adatvédelmi funkciók ismertetése.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970888"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Az Azure Data Box Edge biztonsági és adatvédelmi szolgáltatásai

A biztonság komoly aggodalomra ad okot, ha új technológiát alkalmaz, különösen akkor, ha a technológiát bizalmas vagy védett adatokkal használják. Az Azure Data Box Edge segítségével biztosíthatja, hogy csak a jogosult entitások tekinthessék meg, módosíthassák vagy törölhetik az adatokat.

Ez a cikk a Data Box Edge biztonsági funkciókat ismerteti, amelyek segítenek megvédeni a megoldás egyes összetevőit és a bennük tárolt adatokat.

Az Azure Data Box Edge négy fő összetevőből áll, amelyek kölcsönhatásba lépnek egymással:

- **Data Box Edge szolgáltatás, amely az Azure-ban található.** Az eszközrendelés létrehozásához, az eszköz konfigurálásához, majd a sorrend befejezéséhez való nyomon követéséhez használt felügyeleti erőforrás.
- **Data Box Edge eszköz**. Az átvihető eszköz, amelyet leszállított Önnek, így importálhatja a helyszíni adatokat az Azure-ba.
- **Az eszközhöz csatlakoztatott ügyfelek/állomások.** Az infrastruktúra azon ügyfelei, amelyek a Data Box Edge eszközhöz csatlakoznak, és védett adatokat tartalmaznak.
- **Felhőalapú tárhely**. A hely az Azure felhőplatformján, ahol az adatok tárolása. Ez a hely általában a data box edge erőforráshoz létrehozott tárfiók.

## <a name="data-box-edge-service-protection"></a>Data Box Edge szolgáltatás védelem

A Data Box Edge szolgáltatás egy felügyeleti szolgáltatás, amely az Azure-ban üzemeltetett. A szolgáltatás az eszköz konfigurálására és kezelésére szolgál.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Data Box Edge eszközvédelem

A Data Box Edge eszköz egy helyszíni eszköz, amely segít átalakítani az adatokat helyi legkülönfélébben történő feldolgozásával, majd az Azure-ba való elküldésével. Az Ön készüléke:

- A Data Box Edge szolgáltatás eléréséhez aktiválási kulcsra van szükség.
- Mindig eszközjelszóval védett.
- Ez egy lezárt eszköz. A BMC és a BIOS eszköz jelszóval védett. A BIOS-t korlátozott felhasználói hozzáférés védi.
- Biztonságos rendszerindítás engedélyezve van.
- Futtatja a Windows Defender eszközvédőt. A Device Guard lehetővé teszi, hogy csak a kódintegritási házirendekben meghatározott megbízható alkalmazásokat futtasson.

### <a name="protect-the-device-via-activation-key"></a>Az eszköz védelme az aktiválási gombbal

Csak egy hivatalos Data Box Edge-eszköz csatlakozhat az Azure-előfizetésében létrehozott Data Box Edge szolgáltatáshoz. Egy eszköz engedélyezéséhez egy aktiváló kulcsot kell használnia az eszköz aktiválásához a Data Box Edge szolgáltatással.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

További információt az [Aktiválási kulcs bekapcsolása](data-box-edge-deploy-prep.md#get-the-activation-key)című témakörben talál.

### <a name="protect-the-device-via-password"></a>A készülék védelme jelszóval

A jelszavak biztosítják, hogy csak a jogosult felhasználók férhessenek hozzá az adatokhoz. A Data Box Edge-eszközök zárolt állapotban indulnak el.

A következőket teheti:

- Csatlakozzon az eszköz helyi webes felhasználói felületéhez egy böngészőn keresztül, majd adjon meg egy jelszót az eszközre való bejelentkezéshez.
- Távoli csatlakozás az eszközhöz PowerShell-kapcsolat HTTP-n keresztül. A távkezelés alapértelmezés szerint be van kapcsolva. Ezután megadhatja az eszköz jelszavát az eszközre való bejelentkezéshez. További információt a [Csatlakozás távolról a Data Box Edge eszközhöz című témakörben](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)talál.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- A jelszó módosításához használja a helyi webes felhasználói [felületet.](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access) Ha módosítja a jelszót, mindenképpen értesítse az összes távelérésű felhasználót, hogy ne legyen problémázva a bejelentkezéssel.

## <a name="protect-your-data"></a>Adatok védelme

Ez a szakasz a Data Box Edge biztonsági funkciókat ismerteti, amelyek védik a tranzit- és a tárolt adatokat.

### <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- A BitLocker XTS-AES 256 bites titkosítása a helyi adatok védelmére szolgál.


### <a name="protect-data-in-flight"></a>Adatok védelme repülés közben

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Adatok védelme tárfiókokon keresztül

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgassa el, majd rendszeresen [szinkronizálja a tárfiók kulcsait,](data-box-edge-manage-shares.md#sync-storage-keys) hogy megvédje a tárfiókot a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box Edge szolgáltatás a következő esetekben gyűjti a személyes adatokat:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A megosztáshoz hozzáférő vagy törölhető felhasználók listájának megtekintéséhez kövesse [a Megosztások kezelése a Data Box Edge-en](data-box-edge-manage-shares.md)című részt.

További információt a Microsoft adatvédelmi szabályzatában, az [Adatvédelmi központban talál.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>További lépések

[A Data Box Edge eszköz telepítése](data-box-edge-deploy-prep.md)
