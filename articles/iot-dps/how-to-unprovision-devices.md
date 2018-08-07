---
title: Hogyan kiépített Azure IoT Hub Device Provisioning Service-eszközöket megszüntetése |} A Microsoft Docs
description: Hogyan kiépített Azure IoT Hub Device Provisioning Service-eszközöket megszüntetése
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 3c6e2a9006d73d269422292dc959866d3f6d8a82
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522672"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Hogyan kell eszközöket, amelyek korábban automatikus – kiépített megszüntetése 

Előfordulhat, szükség megszüntetési eszközökre, amelyek korábban automatikus regisztrációt a Device Provisioning Service használatával. Például egy eszköz előfordulhat, hogy értékesített vagy áthelyezték egy másik IoT hubhoz, vagy lehet elveszett, ellopott, vagy más módon sérült. 

Általában egy eszköz megszüntetés két lépésből áll:

1. Az eszközt a kiépítési szolgáltatáshoz, hogy a jövőbeni automatikus kiépítés disenroll. Attól függően, hogy átmenetileg vagy véglegesen vonni a hozzáférést érdemes tiltsa le vagy törölje a regisztrációs bejegyzés. X.509-igazoláshoz használó eszközök esetében érdemes egy bejegyzést a hierarchiában, a meglévő regisztrációs csoportok letiltása/törlése.  
 
   - Egy eszköz disenroll kezelésével kapcsolatos információkért lásd: [hogyan eszközön az Azure IoT Hub Device Provisioning Service disenroll](how-to-revoke-device-access-portal.md).
   - Ismerje meg, hogyan disenroll egy eszközt a kiépítési szolgáltatás SDK-k programozott használatával, lásd: [eszközök beléptetésének kezelése szolgáltatási SDK-k az](how-to-manage-enrollments-sdks.md).

2. Az IoT hubról, hogy a jövőbeli kommunikáció és az adatátvitelt az eszköz regisztrációjának megszüntetése. Újra ideiglenesen letilthatja vagy véglegesen törli az eszköz bejegyzés az eszközidentitás-jegyzékben lévő az IoT hub, ahol kiosztott. Lásd: [letilthatja az eszközöket](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) megfelelő tájékozódhat. "Device Management / IoT-eszközök" az IoT Hub-erőforrás jelenik meg a [az Azure portal](https://portal.azure.com).

Megszünteti az eszköz pontos lépései attól függnek, az igazolási mechanizmus és a megfelelő regisztrációs bejegyzés a kiépítési szolgáltatáshoz. A következő szakaszok a regisztráció és a tanúsítvány típusától függően a folyamat áttekintése.

## <a name="individual-enrollments"></a>Egyéni regisztrációk
TPM-igazolást vagy X.509-igazoláshoz a levéltanúsítvány használó eszközök törlődnek, az egyéni regisztrációs bejegyzést. 

Egy eszköz, amely rendelkezik egy egyéni regisztráció megszüntetéséhez: 

1. Az eszköz regisztrációját az eszközkiépítési szolgáltatás disenroll:

   - Olyan eszközöknél, TPM-igazolást használjon az egyéni regisztrációs bejegyzés véglegesen vonni a hozzáférést az eszközt a kiépítési szolgáltatás törölje vagy tiltsa le a bejegyzés ideiglenesen vonni a hozzáférést. 
   - X.509-igazoláshoz használó eszközök esetében, vagy törölheti vagy letilthatja a-bejegyzést. Vegye figyelembe, azonban, ha törli egy eszköz X.509 használó egyéni regisztrációt, és engedélyezett regisztrációs csoportot abban, hogy az eszköz tanúsítványlánc, az eszköz mindig újból beléptethetik az aláíró tanúsítvány létezik. Az ilyen eszközök lehet letiltani a regisztrációs bejegyzés biztonságosabb. Ennek során ezért megakadályozza, hogy az eszköz regisztrációját adták, függetlenül attól, engedélyezett regisztrációs csoportot egy aláíró tanúsítványt megtalálható-e.

2. Tiltsa le vagy törölje az eszközt, az üzembe helyezett IoT hub identitásjegyzékében. 


## <a name="enrollment-groups"></a>Regisztrációs csoportok
Az X.509-igazoláshoz eszközök is bővítheti keresztül egy regisztrációs csoportnak. Regisztrációs csoportok vannak konfigurálva, egy aláíró tanúsítvánnyal, vagy egy köztes vagy legfelső szintű Hitelesítésszolgáltatói tanúsítvány, és a tanúsítvány a tanúsítványláncukban rendelkező eszközök kiépítési szolgáltatáshoz való hozzáférés szabályozása. Regisztrációs csoportok és az X.509-tanúsítványokat a kiépítési szolgáltatással kapcsolatos további tudnivalókért lásd: [X.509 tanúsítványok](concepts-security.md#x509-certificates). 

Szeretné látni, amely egy regisztrációs csoportnak keresztül lettek telepítve eszközök listáját, megtekintheti a regisztrációs csoport részletei. Ez az egy egyszerű módja annak, hogy melyik minden egyes eszköz lett kiépítve az IoT hub ismertetése. Az eszközlista megtekintése: 

1. Jelentkezzen be az Azure Portalon, és kattintson a **összes erőforrás** a bal oldali menüben.
2. Kattintson az erőforrások listájához a kiépítési szolgáltatásra.
3. Kattintson az eszközkiépítési szolgáltatás **regisztrációk kezelése**, majd **regisztrációs csoportok** fülre.
4. Kattintson a regisztrációs csoport a megnyitásához.

   ![Regisztrációscsoport-bejegyzést megtekintése a portálon](./media/how-to-unprovision-devices/view-enrollment-group.png)

A regisztrációs csoportok van két forgatókönyvet kell figyelembe venni:

- Megszünteti az összes eszközt, amely egy regisztrációs csoportnak keresztül lettek telepítve:
  1. Tiltsa le a regisztrációs csoport a tiltólistára aláíró tanúsítványát. 
  2. Tiltsa le vagy törölje egyes eszközök a megfelelő IoT hub eszközidentitás-jegyzék üzembe helyezett eszközök regisztrációs csoport használatával. 
  3. Miután letiltása, vagy minden eszköz törlését a megfelelő IoT hubon, szükség esetén törölheti a regisztrációs csoport. Ne feledje azonban, hogy a regisztrációs csoportot töröl, és egy aláíró tanúsítványt a tanúsítványlánc alakult engedélyezett regisztrációs csoportot egy vagy több olyan eszközt, ha azokat az eszközöket mindig újból beléptethetik. 

- Megszünteti a regisztrációs csoportot egy adott eszköz:
  1. Hozzon létre egy letiltott egyéni regisztrációt a levéltanúsítványának (eszköz). Ez visszavonja az eszközön a kiépítési szolgáltatáshoz való hozzáférés továbbra is lehetővé téve az egyéb a láncban a regisztrációs csoport aláíró tanúsítvánnyal rendelkező eszközök hozzáférését. Ne törölje az eszköz egyéni regisztrációjának letiltott. Így lehetővé teheti a regisztrációs csoport keresztül újból regisztrálni szeretné az eszközt. 
  2. Az IoT hubon az eszközön való üzembe helyezett kereséséhez használja a regisztrációs csoport üzembe helyezett eszközök listáját, és tiltsa le vagy törölje azt az adott hub identitásjegyzékében. 
  
  










