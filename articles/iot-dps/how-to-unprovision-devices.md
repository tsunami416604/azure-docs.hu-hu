---
title: Útmutató az Azure IoT Hub eszköz kiépítése szolgáltatáshoz kiépített eszközök kiosztásának megszüntetése |} Microsoft Docs
description: Mobileszközök deprovision kiépített az Azure IoT Hub eszköz kiépítése szolgáltatáshoz
author: bryanla
ms.author: bryanla
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 56d9c4c057e226f6cd93551380436cdc52d79169
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630357"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Hogyan korábban automatikus-kiépített eszközök kiosztásának megszüntetése 

Előfordulhat, szükség deprovision eszközök korábban automatikus kiosztású az eszköz kiépítése szolgáltatáson keresztül. Például egy eszköz tulajdonjogát vagy áthelyezése egy másik IoT-központ a, vagy lehet elveszett, ellopott, vagy más módon sérült. 

Általában egy eszköz megszüntetés két lépésből áll:

1. Az eszköz a jövőbeli automatikus-kiosztás megakadályozása, a létesítési szolgáltatás disenroll. Attól függően, hogy átmenetileg vagy véglegesen vonni a hozzáférést érdemes lehet tiltsa le vagy regisztrációs bejegyzés törlése. Az X.509 tanúsítvány használó eszközt érdemes lehet egy bejegyzést a hierarchiában, a meglévő regisztrációs csoportok letiltása/törlése.  
 
   - Az eszköz disenroll, lásd: [hogyan Azure IoT Hub eszköz kiépítése szolgáltatáshoz eszközről disenroll](how-to-revoke-device-access-portal.md).
   - Programozott módon, a létesítési szolgáltatás SDK-k egyik eszköz disenroll lásd: [eszközbeléptetésnél a szolgáltatás SDK-k kezelése](how-to-manage-enrollments-sdks.md).

2. Az IoT Hub jövőbeli kommunikáció és az adatátvitelt megelőzése érdekében az eszköz regisztrációjának törléséhez. Ebben az esetben ideiglenesen letilthatja vagy véglegesen törli az eszköz bejegyzést az identitásjegyzékhez az IoT Hub, ahol lett kiépítve a. Lásd: [eszközök letiltása](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) megfelelő tájékozódhat. "Eszköz felügyeleti / IoT-eszközök" az IoT-központ erőforrás című a [Azure-portálon](https://portal.azure.com).

A pontos lépései kiosztásának megszüntetése eszköz a tanúsítvány mechanizmus és a vonatkozó regisztrációs bejegyzés az üzembe helyezési szolgáltatással függ. A következő szakaszokban a folyamat, a regisztráció és a tanúsítvány típusa alapján áttekintése.

## <a name="individual-enrollments"></a>Egyes regisztrációkat
A levél tanúsítvánnyal TPM tanúsítványt vagy X.509 tanúsítvány használó eszközök törlődnek, az egyes regisztrációs bejegyzés. 

Egy eszköz kiosztásának megszüntetése, amely rendelkezik az egyes tagság: 

1. Az eszköz üzembe helyezési szolgáltatás disenroll:

   - A TPM-igazolási használó eszközök az egyes regisztrációs bejegyzés véglegesen visszavonni a hozzáférést az eszköz a létesítési szolgáltatás törlése, vagy tiltsa le a ideiglenesen visszavonni a hozzáférést a bejegyzést. 
   - Az X.509 tanúsítvány használó eszközt vagy törölheti vagy tiltsa le a bejegyzést. Vegye figyelembe, azonban, ha törli egy eszköz által használt X.509 egyedi igénylésére és egy engedélyezett beléptetési csoport létezik-e egy aláíró tanúsítványt abban, hogy az eszköz tanúsítványlánc, az eszköz mindig újból beléptethetik. Az ilyen eszközök hogy biztonságosabb lehet letiltani a regisztrációs bejegyzés. Ez megakadályozza az eszköz a beléptetés, függetlenül attól, hogy egy engedélyezett beléptetési csoport létezik-e aláíró tanúsítványt.

2. Tiltsa le, vagy törli a identitás beállításkulcsot, amely még lett beállítva, az IoT-központ az eszközt. 


## <a name="enrollment-groups"></a>Beléptetési csoportok
X.509 tanúsítvány, az eszközök kiépíthető egy beléptetési csoporton keresztül. Beléptetési csoportok be van állítva egy aláíró tanúsítványt, vagy egy köztes legfelső szintű Hitelesítésszolgáltatói tanúsítványt, illetve a üzembe helyezési szolgáltatással, hogy a tanúsítványt a tanúsítványlánc eszközökhöz való hozzáférést. Beléptetési csoportok és X.509-tanúsítványokat az üzembe helyezési szolgáltatással kapcsolatos további információkért lásd: [X.509-tanúsítványokat](concepts-security.md#x509-certificates). 

Az eszközről, amelyen keresztül a beléptetési csoport lettek kiosztva listájának megtekintéséhez, megtekintheti a beléptetési csoport részletes adatait. Ez egyszerű módja tudni, hogy mely minden eszköz van kiépítve az IoT-központ. Az eszközlista megtekintése: 

1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüben.
2. Kattintson az erőforrások listájához a létesítési szolgáltatás.
3. A létesítési szolgáltatás kattintson **regisztrációkat kezelése**, majd jelölje be **beléptetési csoportok** lapon.
4. Kattintson a regisztráció csoportra való megnyitásához.

   ![Beléptetési csoport bejegyzéseik megtekintését a portálon](./media/how-to-unprovision-devices/view-enrollment-group.png)

A beléptetési csoportok esetén két esetben kell figyelembe venni:

- A kiosztásának megszüntetése összes egy beléptetési csoporton keresztül kiépített eszközt:
  1. Tiltsa le a regisztrációs csoport aláíró tanúsítványát tiltólistára kell helyezni. 
  2. A beléptetési csoport kiépített eszközök listája használatával tiltsa le, illetve törölhet minden eszköz a megfelelő IoT hub az identitásjegyzékhez. 
  3. Után letiltása, vagy minden eszköz eltávolítása a saját IoT-központok, a beléptetési csoport opcionálisan törölheti. Ne feledje azonban, hogy a beléptetési csoport és egy engedélyezett beléptetési csoport egy vagy több olyan eszközt a magasabb a tanúsítványlánc aláíró tanúsítványt, az eszközök mindig újból beléptethetik. 

- A beléptetési csoportból egyetlen eszközt kiosztásának megszüntetése:
  1. Hozzon létre egy letiltott egyes regisztrációja a levél (eszközök) tanúsítványát. Ez visszavonja az eszköznek a létesítési szolgáltatás ugyanakkor továbbra is lehetővé teszi a hozzáférést a beléptetési csoport aláíró tanúsítványt a láncban lévő más eszközök. Ne törölje a letiltott egyes regisztrációt az adott eszköz. Így lehetővé teheti az eszköz újítsa meg a beléptetési csoporton keresztül. 
  2. Használja a beléptetési csoport kiépített eszközök listáját az IoT hub, amely az eszköz számára lett kiépítve található, és tiltsa le, vagy törölni az adott központi identitásjegyzékhez. 
  
  










