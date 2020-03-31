---
title: Az Azure IoT Hub-eszközkiépítési szolgáltatással kiépített eszközök kiirtása
description: Az Azure IoT Hub-eszközkiépítési szolgáltatással (DPS) kiépített eszközök megszüntetése
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 8a3677ba285f5b02407ca3d176979bf6c016ef9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974836"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>A korábban automatikusan kiépített eszközök kiirtása 

Előfordulhat, hogy szükségesnek találja a korábban az eszközkiépítési szolgáltatáson keresztül automatikusan kiépített eszközök kiépítésének eltörlését. Például egy eszköz lehet értékesíteni, vagy áthelyezni egy másik IoT hub, vagy lehet, hogy elveszett, ellopták, vagy más módon veszélybe. 

Az eszköz kiirtása általában két lépésből áll:

1. Az eszköz leiratkozása a létesítési szolgáltatásból, a jövőbeli automatikus kiépítés megakadályozása érdekében. Attól függően, hogy ideiglenesen vagy véglegesen vissza kívánja-e vonni a hozzáférést, előfordulhat, hogy le szeretne tiltani vagy törölni szeretne egy regisztrációs bejegyzést. Az X.509-es tanúsítványt használó eszközök esetében célszerű lehet letiltani/törölni egy bejegyzést a meglévő regisztrációs csoportok hierarchiájában.  
 
   - Az eszközök leiratkozásáról a [Hogyan lehet leválasztani egy eszközt az Azure IoT Hub-eszközkiépítési szolgáltatásból.](how-to-revoke-device-access-portal.md)
   - Ha tudni szeretné, hogyan lehet programozott módon leíratni egy eszközt a létesítési szolgáltatás egyik SDK-jával, olvassa el [az Eszközregisztrációk kezelése szolgáltatással SDK-kkal című témakört.](how-to-manage-enrollments-sdks.md)

2. Törölje az eszközt az IoT Hubról, hogy megakadályozza a jövőbeli kommunikációt és az adatátvitelt. Ismét ideiglenesen letilthatja vagy véglegesen törölheti az eszköz bejegyzését az IoT Hub identitás-beállításjegyzékében, ahol ki van építve. További információ a letiltásról az [Eszközök letiltása.](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) Tekintse meg az "Eszközkezelés / IoT-eszközök" az IoT Hub-erőforrás, az [Azure Portalon.](https://portal.azure.com)

Az eszköz kiirtásának pontos lépései a tanúsítványmechanizmusától és a létesítési szolgáltatással való megfelelő beléptetési bejegyzéstől függenek. A következő szakaszok áttekintést nyújtanak a folyamatról a regisztráció és a tanúsítvány típusa alapján.

## <a name="individual-enrollments"></a>Egyéni regisztrációk
A TPM-igazolást vagy Az X.509-es tanúsítványt levéltanúsítvánnyal rendelkező eszközöket egy egyéni beléptetési bejegyzésen keresztül kell kiépíteni. 

Egyéni regisztrációval rendelkező eszköz kiirtásának kitörlése: 

1. Az eszköz leiratkozása a létesítési szolgáltatásból:

   - A TPM-tanúsítványt használó eszközök esetében törölje az egyéni beléptetési bejegyzést, hogy véglegesen visszavonja az eszköz hozzáférését a létesítési szolgáltatáshoz, vagy tiltsa le a bejegyzést a hozzáférés ideiglenes visszavonásához. 
   - Az X.509-es tanúsítványt használó eszközök esetében törölheti vagy letilthatja a bejegyzést. Ne feledje azonban, hogy ha töröl egy egyéni regisztrációt egy x.509-et használó eszközhöz, és létezik egy aláírt tanúsítvány az eszköz tanúsítványláncában, az eszköz újra regisztrálhat. Az ilyen eszközök esetében biztonságosabb lehet a regisztrációs bejegyzés letiltása. Ezzel megakadályozza az eszköz újbóli regisztrálását, függetlenül attól, hogy létezik-e engedélyezett regisztrációs csoport az egyik aláíró tanúsítványához.

2. Tiltsa le vagy törölje az eszközt az IoT hub identitásjegyzékében, amelyhez ki van építve. 


## <a name="enrollment-groups"></a>Beléptetési csoportok
Az X.509-es tanúsítványsegítségével az eszközök egy regisztrációs csoporton keresztül is kiépíthetők. A regisztrációs csoportok egy aláíró tanúsítvánnyal, köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal vannak konfigurálva, és szabályozzák a tanúsítványláncukban lévő tanúsítvánnyal rendelkező eszközök létesítési szolgáltatásához való hozzáférést. A regisztrációs csoportokról és az X.509-es tanúsítványokról a kiépítési szolgáltatással kapcsolatban az [X.509 tanúsítványok című témakörben olvashat bővebben.](concepts-security.md#x509-certificates) 

A regisztrációs csoporton keresztül kiépített eszközök listájának megtekintéséhez megtekintheti a regisztrációs csoport adatait. Ez egy egyszerű módja annak megértéséhez, hogy az egyes eszközök melyik IoT hubhoz vannak kiépítve. Az eszközlista megtekintése: 

1. Jelentkezzen be az Azure Portalra, és kattintson a bal oldali menü **Minden erőforrás** parancsára.
2. Kattintson a kiépítési szolgáltatás az erőforrások listájában.
3. A létesítési szolgáltatásban kattintson az **Igénylések kezelése gombra,** majd válassza **a Regisztrációs csoportok** lapot.
4. A megnyitáshoz kattintson a regisztrációs csoportra.

   ![Regisztrációs csoport bejegyzésének megtekintése a portálon](./media/how-to-unprovision-devices/view-enrollment-group.png)

A beléptetési csoportok esetében két forgatókönyvet kell figyelembe venni:

- A regisztrációs csoporton keresztül kiépített összes eszköz kibontása:
  1. Tiltsa le a regisztrációs csoportot az aláíró tanúsítvány feketelistára. 
  2. Az adott regisztrációs csoport hoz tartozó kiépített eszközök listájával letilthatja vagy törölheti az egyes eszközöket a megfelelő IoT hub identitás-beállításjegyzékéből. 
  3. Miután letiltotta vagy törölte az összes eszközt a megfelelő IoT-központokból, szükség esetén törölheti a regisztrációs csoportot. Ne feledje azonban, hogy ha törli a regisztrációs csoportot, és egy aláírási tanúsítványhoz egy vagy több eszköz tanúsítványláncában van egy engedélyezett regisztrációs csoport, ezek az eszközök újra regisztrálhatnak. 

- Egyetlen eszköz kibontása egy regisztrációs csoportból:
  1. Hozzon létre egy letiltott egyéni regisztrációt a levél (eszköz) tanúsítványához. Ez visszavonja az adott eszköz létesítési szolgáltatásához való hozzáférést, miközben továbbra is engedélyezi a hozzáférést más olyan eszközök számára, amelyek láncában a regisztrációs csoport aláíró tanúsítványa található. Ne törölje az eszköz letiltott egyéni regisztrációját. Ezzel lehetővé teszi az eszköz számára, hogy újra regisztráljon a regisztrációs csoporton keresztül. 
  2. Az adott regisztrációs csoport létesített eszközeinek listájával keresse meg azt az IoT-központot, amelybe az eszközt kiépítették, és tiltsa le vagy törölje azt az adott hub identitás-beállításjegyzékéből. 
  
  










