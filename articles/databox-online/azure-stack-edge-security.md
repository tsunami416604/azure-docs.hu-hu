---
title: Azure Stack Edge-biztonság | Microsoft Docs
description: Ismerteti azokat a biztonsági és adatvédelmi funkciókat, amelyek védik a Azure Stack peremhálózati eszközét, szolgáltatását és adatait a helyszínen és a felhőben.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570069"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack peremhálózati biztonság és adatvédelem

A biztonság jelentős jelentőséggel bír az új technológiák bevezetésekor, különösen akkor, ha a technológiát bizalmas vagy tulajdonosi adatokkal használják. Azure Stack Edge segítségével biztosíthatja, hogy csak a felhatalmazott entitások tudják megtekinteni, módosítani vagy törölni az adatait.

Ez a cikk azokat a Azure Stack peremhálózati biztonsági funkciókat ismerteti, amelyek segítenek a megoldás egyes összetevőinek és a bennük tárolt adatainak a védelmében.

Az Azure Stack Edge négy fő összetevőből áll, amelyek egymással együttműködnek:

- **Az Azure-ban üzemeltetett Azure stack Edge szolgáltatás**. Az eszköz megrendelésének létrehozásához használt felügyeleti erőforrás, konfigurálja az eszközt, majd kövesse nyomon a befejezési sorrendet.
- **Azure stack Edge-eszköz**. Az Ön által szállított továbbítási eszköz, amely a helyszíni adatok Azure-ba való importálására használható.
- **Az eszközhöz csatlakoztatott ügyfelek/gazdagépek**. Az infrastruktúra azon ügyfelei, amelyek az Azure Stack Edge-eszközhöz csatlakoznak, és a védeni kívánt adatait tartalmazzák.
- **Felhőbeli tárolás**. A hely az Azure Cloud platformon, ahol az adatgyűjtés történik. Ez a hely általában a létrehozott Azure Stack peremhálózati erőforráshoz csatolt Storage-fiók.

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge szolgáltatás védelme

Az Azure Stack Edge szolgáltatás az Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás az eszköz konfigurálására és kezelésére szolgál.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack Edge-eszköz védelme

Az Azure Stack Edge-eszköz egy helyszíni eszköz, amely segít az adatok helyi feldolgozásában, majd az Azure-ba küldésében. Az eszköz:

- Aktiválási kulcsra van szüksége az Azure Stack Edge szolgáltatás eléréséhez.
- Az eszköz jelszavai mindig védelmet biztosítanak.
- Egy zárolt eszköz. Az eszköz BMC és BIOS jelszavas védelemmel van ellátva. A BIOS-t korlátozott felhasználói hozzáférés védi.
- A biztonságos rendszerindítás engedélyezve van.
- A Windows Defender Device Guard eszközt futtatja. Az Eszközkezelő lehetővé teszi, hogy csak a kód-integritási házirendekben meghatározott megbízható alkalmazásokat futtasson.

### <a name="protect-the-device-via-activation-key"></a>Az eszköz védetté aktiválása az aktiválási kulcs használatával

Csak egy engedélyezett Azure Stack peremhálózati eszköz csatlakozhat az Azure-előfizetésében létrehozott Azure Stack Edge szolgáltatáshoz. Az eszköz engedélyezéséhez aktiválnia kell az eszközt az Azure Stack Edge szolgáltatással.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

További információ: [aktiválási kulcs beszerzése](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Az eszköz jelszavas védelemmel való ellátása

A jelszavak biztosítják, hogy csak a jogosult felhasználók férjenek hozzá az adataihoz. Azure Stack peremhálózati eszközök zárolt állapotban indulnak.

A következőket teheti:

- Kapcsolódjon az eszköz helyi webes FELÜLETéhez egy böngészőben, majd adjon meg egy jelszót az eszközre való bejelentkezéshez.
- Távolról kapcsolódhat az eszköz PowerShell-felületéhez HTTP-n keresztül. A távfelügyelet alapértelmezés szerint be van kapcsolva. Ezután megadhatja az eszköz jelszavát az eszközre való bejelentkezéshez. További információ: [távoli kapcsolódás az Azure stack Edge-eszközhöz](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- A [jelszó megváltoztatásához](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)használja a helyi webes felhasználói felületet. Ha megváltoztatja a jelszót, ügyeljen arra, hogy értesítse az összes távelérési felhasználót, hogy ne legyenek a bejelentkezéssel kapcsolatos problémák.

## <a name="protect-your-data"></a>Adatok védelme

Ez a szakasz azokat a Azure Stack peremhálózati biztonsági funkciókat ismerteti, amelyek a továbbítás és a tárolt adatforgalom védelmére használhatók.

### <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- A BitLocker XTS-AES 256 bites titkosítás a helyi adatvédelemre szolgál.


### <a name="protect-data-in-flight"></a>A repülésben tárolt adatvédelem

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Az adatvédelem a Storage-fiókok használatával

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgassa el és [szinkronizálja rendszeresen a Storage-fiók kulcsait](azure-stack-edge-manage-shares.md#sync-storage-keys) , hogy megvédje a Storage-fiókját a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

Az Azure Stack Edge szolgáltatás a következő helyzetekben gyűjt személyes adatokat:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A megosztásokat elérő vagy törlő felhasználók listájának megtekintéséhez kövesse a [megosztások kezelése az Azure stack Edge](azure-stack-edge-manage-shares.md)-ben című témakör lépéseit.

További információkért tekintse át a Microsoft adatvédelmi szabályzatát a [megbízhatósági központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[Az Azure Stack Edge-eszköz üzembe helyezése](azure-stack-edge-deploy-prep.md)
