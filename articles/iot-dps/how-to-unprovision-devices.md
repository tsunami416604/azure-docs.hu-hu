---
title: "Hogyan leépíteni a következőt: eszközök regisztrálva az Azure IoT Hub eszköz kiépítése szolgáltatáshoz |} Microsoft Docs"
description: "Hogyan leépíteni a következőt: az Azure portálon a terjesztési pontok szolgáltatás által regisztrált eszközök"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 1d057a4df43cf25e6817672d198207d9a50e462e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Hogyan leépíteni a következőt: a létesítési szolgáltatás által regisztrált eszközök

Előfordulhat az szükséges, hogy az eszköz kiépítése szolgáltatáson keresztül lettek kiosztva leépíteni eszközökre. Például egy eszköz tulajdonjogát vagy áthelyezése egy másik IoT-központ a, vagy lehet elveszett, ellopott, vagy más módon sérült. 

Általában egy eszköz leépítése két lépésből áll:

1. Az eszköz a kiépítési szolgáltatáshoz való hozzáférés visszavonásához. Attól függően, hogy átmenetileg vagy véglegesen vonni a hozzáférést, illetve az X.509 tanúsítvány mechanizmus a hierarchiában, a meglévő regisztrációs csoportok esetén érdemes lehet tiltsa le vagy regisztrációs bejegyzés törlése. 
 
   - A portál használatával eszközök hozzáférésének visszavonása, lásd: [eszközök hozzáférésének visszavonása](how-to-revoke-device-access-portal.md).
   - Programozott módon, a létesítési szolgáltatás SDK-k egyik eszközök hozzáférésének visszavonása, lásd: [eszközbeléptetésnél a szolgáltatás SDK-k kezelése](how-to-manage-enrollments-sdks.md).

2. Tiltsa le, vagy törölheti az identitásjegyzékhez bejegyzést az eszközt az IoT Hub, ahol lett kiépítve. További tudnivalókért lásd: [eszköz Identitáskezelést](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) az Azure IoT Hub-dokumentációban. 

A pontos lépéseket leépíteni a következőt: egy eszközt az igazolás mechanizmus és a vonatkozó regisztrációs bejegyzés az üzembe helyezési szolgáltatással függ.

## <a name="individual-enrollments"></a>Egyes regisztrációkat
A levél tanúsítvánnyal TPM tanúsítványt vagy X.509 tanúsítvány használó eszközök törlődnek, az egyes regisztrációs bejegyzés. 

Egy eszköz, amely rendelkezik az egyes tagság leépíteni a következőt: 
1. TPM tanúsítványt használó eszközök esetében törölje az egyes regisztrációs bejegyzés véglegesen visszavonni a hozzáférést az eszköz a létesítési szolgáltatás, vagy tiltsa le a bejegyzés ideiglenesen visszavonni a hozzáférést. Az X.509 tanúsítvány használó eszközt vagy törölheti vagy tiltsa le a bejegyzést. Vegye figyelembe, azonban, hogy ha egy egyéni egy eszköz regisztrációjának törlése, amely használ X.509 tanúsítvány, és egy engedélyezett beléptetési csoport létezik-e egy aláíró tanúsítványt abban, hogy az eszköz tanúsítványlánc, az eszköz mindig újból beléptethetik. Az ilyen eszközök hogy biztonságosabb lehet letiltani a regisztrációs bejegyzés. Ez megakadályozza az eszköz a beléptetés, függetlenül attól, hogy egy engedélyezett beléptetési csoport létezik-e aláíró tanúsítványt.
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

- Az eszközöket, amelyek egy beléptetési csoporton keresztül lettek kiosztva mindegyikét leépíteni a következőt:
  1. Tiltsa le a regisztrációs csoport aláíró tanúsítványát tiltólistára kell helyezni. 
  2. A beléptetési csoport kiépített eszközök listája használatával tiltsa le, illetve törölhet minden eszköz a megfelelő IoT hub az identitásjegyzékhez. 
  3. Után letiltása, vagy minden eszköz eltávolítása a saját IoT-központok, a beléptetési csoport opcionálisan törölheti. Ne feledje azonban, hogy a beléptetési csoport és egy engedélyezett beléptetési csoport egy vagy több olyan eszközt a magasabb a tanúsítványlánc aláíró tanúsítványt, az eszközök mindig újból beléptethetik. 
- Beléptetési csoportból egyetlen eszközt leépíteni a következőt:
  1. Hozzon létre egy letiltott egyes regisztrációja a levél (eszközök) tanúsítványát. Ez visszavonja az eszköznek a létesítési szolgáltatás ugyanakkor továbbra is lehetővé teszi a hozzáférést a beléptetési csoport aláíró tanúsítványt a láncban lévő más eszközök. Ne törölje a letiltott egyes regisztrációt az adott eszköz. Így lehetővé teheti az eszköz újítsa meg a beléptetési csoporton keresztül. 
  2. Használja a beléptetési csoport kiépített eszközök listáját az IoT hub, amely az eszköz számára lett kiépítve található, és tiltsa le, vagy törölni az adott központi identitásjegyzékhez. 
  
  










