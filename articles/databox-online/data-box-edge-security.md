---
title: Azure Data Box Edge biztonság | Microsoft Docs
description: Ismerteti azokat a biztonsági és adatvédelmi funkciókat, amelyek megvédik Azure Data Box Edge eszközét, szolgáltatását és adatait a helyszínen és a felhőben.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "69970888"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Az Azure Data Box Edge biztonsági és adatvédelmi szolgáltatásai

A biztonság jelentős jelentőséggel bír az új technológiák bevezetésekor, különösen akkor, ha a technológiát bizalmas vagy tulajdonosi adatokkal használják. Azure Data Box Edge segítségével biztosíthatja, hogy csak a felhatalmazott entitások tudják megtekinteni, módosítani vagy törölni az adatait.

Ez a cikk azokat a Data Box Edge biztonsági funkciókat ismerteti, amelyek segítenek a megoldás egyes összetevőinek és a bennük tárolt adatainak a védelmében.

A Azure Data Box Edge négy fő összetevőből áll, amelyek egymással együttműködnek:

- **Az Azure-ban üzemeltetett Data Box Edge szolgáltatás**. Az eszköz megrendelésének létrehozásához használt felügyeleti erőforrás, konfigurálja az eszközt, majd kövesse nyomon a befejezési sorrendet.
- **Data Box Edge eszköz**. Az Ön által szállított továbbítási eszköz, amely a helyszíni adatok Azure-ba való importálására használható.
- **Az eszközhöz csatlakoztatott ügyfelek/gazdagépek**. Az infrastruktúra azon ügyfelei, amelyek a Data Box Edge eszközhöz csatlakoznak, és a védeni kívánt adatait tartalmazzák.
- **Felhőbeli tárolás**. A hely az Azure Cloud platformon, ahol az adatgyűjtés történik. Ez a hely általában a létrehozott Data Box Edge erőforráshoz csatolt Storage-fiók.

## <a name="data-box-edge-service-protection"></a>Data Box Edge szolgáltatás védelme

A Data Box Edge szolgáltatás az Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás az eszköz konfigurálására és kezelésére szolgál.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Data Box Edge eszköz védelme

A Data Box Edge eszköz egy helyszíni eszköz, amely megkönnyíti az adatok helyi feldolgozását, majd az Azure-ba történő küldését. Az eszköz:

- Aktiválási kulcsra van szükség a Data Box Edge szolgáltatás eléréséhez.
- Az eszköz jelszavai mindig védelmet biztosítanak.
- Egy zárolt eszköz. Az eszköz BMC és BIOS jelszavas védelemmel van ellátva. A BIOS-t korlátozott felhasználói hozzáférés védi.
- A biztonságos rendszerindítás engedélyezve van.
- A Windows Defender Device Guard eszközt futtatja. Az Eszközkezelő lehetővé teszi, hogy csak a kód-integritási házirendekben meghatározott megbízható alkalmazásokat futtasson.

### <a name="protect-the-device-via-activation-key"></a>Az eszköz védetté aktiválása az aktiválási kulcs használatával

Csak egy engedélyezett Data Box Edge eszköz csatlakozhat az Azure-előfizetésében létrehozott Data Box Edge szolgáltatáshoz. Az eszköz engedélyezéséhez aktiválnia kell egy aktiválási kulcsot az eszköz Data Box Edge szolgáltatással való aktiválásához.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

További információ: [aktiválási kulcs beszerzése](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Az eszköz jelszavas védelemmel való ellátása

A jelszavak biztosítják, hogy csak a jogosult felhasználók férjenek hozzá az adataihoz. Data Box Edge az eszközök zárolt állapotban vannak.

A következőket teheti:

- Kapcsolódjon az eszköz helyi webes FELÜLETéhez egy böngészőben, majd adjon meg egy jelszót az eszközre való bejelentkezéshez.
- Távolról kapcsolódhat az eszköz PowerShell-felületéhez HTTP-n keresztül. A távfelügyelet alapértelmezés szerint be van kapcsolva. Ezután megadhatja az eszköz jelszavát az eszközre való bejelentkezéshez. További információ: [távoli kapcsolódás a Data Box Edge eszközhöz](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- A [jelszó megváltoztatásához](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)használja a helyi webes felhasználói felületet. Ha megváltoztatja a jelszót, ügyeljen arra, hogy értesítse az összes távelérési felhasználót, hogy ne legyenek a bejelentkezéssel kapcsolatos problémák.

## <a name="protect-your-data"></a>Adatok védelme

Ez a szakasz azokat a Data Box Edge biztonsági szolgáltatásokat ismerteti, amelyek a továbbítást és a tárolt információkat védik.

### <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- A BitLocker XTS-AES 256 bites titkosítás a helyi adatvédelemre szolgál.


### <a name="protect-data-in-flight"></a>A repülésben tárolt adatvédelem

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Az adatvédelem a Storage-fiókok használatával

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgassa el és [szinkronizálja rendszeresen a Storage-fiók kulcsait](data-box-edge-manage-shares.md#sync-storage-keys) , hogy megvédje a Storage-fiókját a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box Edge szolgáltatás a következő helyzetekben gyűjt személyes adatokat:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A megosztásokat elérő vagy törölhető felhasználók listájának megtekintéséhez kövesse a [megosztások kezelése a Data Box Edgeban](data-box-edge-manage-shares.md)című témakör lépéseit.

További információkért tekintse át a Microsoft adatvédelmi szabályzatát a [megbízhatósági központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[A Data Box Edge eszköz üzembe helyezése](data-box-edge-deploy-prep.md)
