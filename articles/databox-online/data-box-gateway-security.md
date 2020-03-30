---
title: Azure Data Box Gateway biztonsága | Microsoft dokumentumok
description: Az Azure Data Box Gateway virtuális eszközét, szolgáltatását és adatait a helyszínen és a felhőben védő biztonsági és adatvédelmi funkciók ismertetése.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900596"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Az Azure Data Box Gateway biztonsága és adatvédelem

A biztonság komoly aggodalomra ad okot, ha új technológiát alkalmaz, különösen akkor, ha a technológiát bizalmas vagy védett adatokkal használják. Az Azure Data Box Gateway segítségével biztosíthatja, hogy csak a jogosult entitások tekinthessék meg, módosíthassák vagy törölhetik az adatokat.

Ez a cikk ismerteti az Azure Data Box Gateway biztonsági funkciók, amelyek segítenek megvédeni az egyes megoldás-összetevők és a bennük tárolt adatok.

A Data Box Gateway megoldás négy fő összetevőből áll, amelyek kölcsönhatásba lépnek egymással:

- **Data Box Gateway szolgáltatás, üzemeltetett Azure.** Az eszközrendelés létrehozásához, az eszköz konfigurálásához, majd a sorrend befejezéséhez való nyomon követéséhez használt felügyeleti erőforrás.
- **Data Box Gateway eszköz**. A virtuális eszköz, amelyet az Ön által biztosított rendszer hipervizorában biztosít. Ez a virtuális eszköz a helyszíni adatok Azure-ba importálására szolgál.
- **Az eszközhöz csatlakoztatott ügyfelek/állomások.** Az infrastruktúra azon ügyfelei, amelyek csatlakoznak a Data Box Gateway eszközhöz, és védett adatokat tartalmaznak.
- **Felhőalapú tárhely**. A hely az Azure felhőplatformján, ahol az adatok tárolása. Ez a hely általában a létrehozott Data Box Gateway erőforráshoz kapcsolódó tárfiók.


## <a name="data-box-gateway-service-protection"></a>Data Box Gateway szolgáltatás védelem

A Data Box Gateway szolgáltatás egy felügyeleti szolgáltatás, amely az Azure-ban üzemelteti. A szolgáltatás az eszköz konfigurálására és kezelésére szolgál.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway eszközvédelem

A Data Box Gateway eszköz egy virtuális eszköz, amely az Ön által biztosított helyszíni rendszer hipervizorában van kiépítve. Az eszköz segít adatokat küldeni az Azure-ba. Az Ön készüléke:

- A Data Box Edge/Data Box Gateway szolgáltatás eléréséhez aktiválási kulcsra van szükség.
- Mindig eszközjelszóval védett.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Az eszköz védelme az aktiválási gombbal

Csak egy hivatalos Data Box Gateway eszköz csatlakozhat az Azure-előfizetésében létrehozott Data Box Gateway szolgáltatáshoz. Az eszköz engedélyezéséhez aktiváló kulcsot kell használnia az eszköz adatdobozos átjárószolgáltatással való aktiválásához.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

További információt az [Aktiválási kulcs bekapcsolása](data-box-gateway-deploy-prep.md#get-the-activation-key)című témakörben talál.

### <a name="protect-the-device-via-password"></a>A készülék védelme jelszóval

A jelszavak biztosítják, hogy csak a jogosult felhasználók férhessenek hozzá az adatokhoz. A Data Box Gateway-eszközök zárolt állapotban indulnak el.

A következőket teheti:

- Csatlakozzon az eszköz helyi webes felhasználói felületéhez egy böngészőn keresztül, majd adjon meg egy jelszót az eszközre való bejelentkezéshez.
- Távolról csatlakozhat az eszköz PowerShell-kapcsolatához HTTP-n keresztül. A távkezelés alapértelmezés szerint be van kapcsolva. Ezután megadhatja az eszköz jelszavát az eszközre való bejelentkezéshez. További információt a [Csatlakozás távolról a Data Box Gateway eszközhöz](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)című témakörben talál.

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- A jelszó módosításához használja a helyi webes felhasználói [felületet.](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access) Ha módosítja a jelszót, mindenképpen értesítse az összes távelérésű felhasználót, hogy ne legyen problémázva a bejelentkezéssel.


## <a name="protect-your-data"></a>Adatok védelme

Ez a szakasz a Data Box Gateway biztonsági szolgáltatásait ismerteti, amelyek az átvitel közbeni és a tárolt adatokat védik.

### <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Adatok védelme repülés közben

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Adatok védelme tárfiókokon keresztül

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgassa el, majd rendszeresen [szinkronizálja a tárfiók kulcsait,](data-box-gateway-manage-shares.md#sync-storage-keys) hogy megvédje a tárfiókot a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box Gateway szolgáltatás a következő esetekben gyűjti a személyes adatokat:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A megosztáshoz hozzáférő vagy törölhető felhasználók listájának megtekintéséhez kövesse a Megosztások kezelése az [Adatdoboz-átjárón](data-box-gateway-manage-shares.md)című részt.

További információt a Microsoft adatvédelmi szabályzatában, az [Adatvédelmi központban talál.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>További lépések

[A Data Box Gateway eszköz telepítése](data-box-gateway-deploy-prep.md)
