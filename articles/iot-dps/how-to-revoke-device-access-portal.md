---
title: Hogyan disenroll Azure IoT Hub eszköz kiépítése szolgáltatáshoz eszközről
description: Hogyan disenroll az eszköz Azure IoT Hub eszköz kiépítése szolgáltatáshoz a kiosztás megakadályozása
author: bryanla
ms.author: bryanla
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 4b6c948765575c92c962999fe394ffaf1a0d22f3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628844"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Hogyan disenroll Azure IoT Hub eszköz kiépítése szolgáltatáshoz eszközről

Magas-profil rendszerek például az IoT-megoldások elengedhetetlen a megfelelő felügyeleti eszköz hitelesítő adatokat. Az ilyen rendszerek esetén ajánlott eljárás, visszavonhatja a hozzáférést az eszközök hogyan egyértelmű terv hogy mikor megadhatják hitelesítő adataikat, hogy egy közös hozzáférésű jogosultságkódokat (SAS) jogkivonatot, vagy egy X.509 tanúsítvány utaló jeleket. 

Az eszköz kiépítése szolgáltatáshoz a regisztrációs lehetővé teszi, hogy egy eszköz [automatikus kiosztású](concepts-auto-provisioning.md). A kiépített eszköz egy, az IoT-központot, lehetővé téve az első fogadására regisztrált [eszköz iker](~/articles/iot-hub/iot-hub-devguide-device-twins.md) állapotát és telemetriai adatokat reporting megkezdéséhez. Ez a cikk ismerteti a kiépítési szolgáltatáspéldány, meggátolja, hogy újra létre a jövőben eszközről disenroll.

> [!NOTE] 
> Vegye figyelembe az újrapróbálkozási házirendet az eszközről, amelyen meg visszavonni a hozzáférést. Például egy eszköz, amely rendelkezik egy végtelen újrapróbálkozási házirendje előfordulhat, hogy folyamatosan próbálja meg regisztrálni az üzembe helyezési szolgáltatással. Ilyen esetben a szolgáltatás-erőforrásokat használ fel, és valószínűleg hatással van a teljesítményre.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Az egyes regisztrációs bejegyzés használatával blacklist eszközök

Egyes regisztrációkat egyetlen eszközt vonatkoznak, és X.509-tanúsítványokat vagy SAS-tokenje (a valós vagy virtuális TPM) is használhatja, igazoló mechanizmusként. (Használó eszközök SAS-tokenje módon csak egy egyedi igénylés révén az igazolás mechanizmus létesíthetők.) A tiltólistára az egyes tagság tartalmazó eszköz, letiltása, vagy a regisztrációs bejegyzés törlése. 

Ideiglenes tiltólistára az eszköz a regisztrációs bejegyzés letiltásával: 

1. Jelentkezzen be az Azure portál, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájához válassza ki a létesítési szolgáltatás, amelyet az eszköz tiltólistára kell helyezni.
3. A létesítési szolgáltatás, válassza ki **regisztrációkat kezelése**, majd válassza ki a **egyedi regisztrációkat** lapon.
4. Válassza ki a regisztrációs bejegyzés az eszköz, amelyet tiltólistára kell helyezni. 
5. Görgessen a lap aljára, és válassza **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**.  

   [![Tiltsa le az egyes regisztrációs bejegyzés a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png#lightbox)  

A véglegesen tiltólistára az eszköz a regisztrációs bejegyzés törlésével:

1. Jelentkezzen be az Azure portál, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájához válassza ki a létesítési szolgáltatás, amelyet az eszköz tiltólistára kell helyezni.
3. A létesítési szolgáltatás, válassza ki **regisztrációkat kezelése**, majd válassza ki a **egyedi regisztrációkat** lapon.
4. Válassza ki a regisztrációs bejegyzés az eszköz tiltólistára kell helyezni kívánt jelölőnégyzetét. 
5. Válassza ki **törlése** az ablakot, és válassza a felső **Igen** annak ellenőrzéséhez, hogy szeretné-e távolítsa el a regisztrációt. 

   ![A portál egyes regisztrációs bejegyzés törlése](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
Az eljárás befejezése után meg kell jelennie a bejegyzés az egyes regisztrációkat listájából eltávolítva.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Egy X.509 köztes vagy a legfelső szintű Hitelesítésszolgáltatói tanúsítvány tiltólistára egy beléptetési csoport használatával

X.509-tanúsítványokat általában egy megbízhatósági láncot vannak rendezve. Egy tanúsítványt egy lánc bármely szakaszában biztonságának sérülése esetén, ha bármilyen okból megszakad. A tanúsítvány a kell eszköz kiépítése szolgáltatáshoz megakadályozása, hogy a tanúsítványt bármely lánc létesítési eszközök után feketelistára teszi. X.509-tanúsítványokat és azok használata az üzembe helyezési szolgáltatással kapcsolatos további információkért lásd: [X.509-tanúsítványokat](./concepts-security.md#x509-certificates). 

Egy regisztrációs csoport egy bejegyzést az eszközöket, amelyek egy közös igazolás mechanizmus az X.509 tanúsítvány aláírásával ugyanaz a köztes rendelkezik, illetve a legfelső szintű hitelesítésszolgáltató. A beléptetési csoport bevitele konfigurálva van a köztes társított X.509 tanúsítvánnyal vagy legfelső szintű hitelesítésszolgáltató. A bejegyzés is bármely konfigurációs értékeket, például a kettős állapot és az IoT hub kapcsolat, a tanúsítványlánc megosztott eszközök a tanúsítvánnyal van konfigurálva. A tanúsítvány tiltólistára, hogy tiltsa le, vagy a regisztráció csoport törlése.

Ideiglenes tiltólistára a tanúsítvány beléptetési csoportjának letiltásával: 

1. Jelentkezzen be az Azure portál, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájához válassza ki a létesítési szolgáltatás tiltólistára a aláíró tanúsítványt a kívánt.
3. Válassza ki a létesítési szolgáltatás **regisztrációkat kezelése**, majd válassza ki a **beléptetési csoportok** lapon.
4. Válassza ki a regisztrációs tiltólistára kell helyezni kívánt tanúsítvány használatával.
5. Válassza ki **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**.  

   ![Tiltsa le a regisztrációs csoport bejegyzés a portálon](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
A véglegesen tiltólistára a tanúsítvány beléptetési csoportjának törlésével:

1. Jelentkezzen be az Azure portál, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájához válassza ki a létesítési szolgáltatás, amelyet az eszköz tiltólistára kell helyezni.
3. Válassza ki a létesítési szolgáltatás **regisztrációkat kezelése**, majd válassza ki a **beléptetési csoportok** lapon.
4. Válassza ki a regisztrációs csoport tiltólistára kell helyezni kívánt tanúsítvány melletti jelölőnégyzetet. 
5. Válassza ki **törlése** az ablakot, és válassza a felső **Igen** annak ellenőrzéséhez, hogy szeretné-e a beléptetési csoport eltávolítása. 

   ![A portál regisztrációs csoport bejegyzés törlése](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Az eljárás befejezése után meg kell jelennie a bejegyzés eltávolítja a beléptetési csoportok listája.  

> [!NOTE]
> Ha töröl egy beléptetési csoport tanúsítványt, eszközökre, amelyeken a tanúsítványt a tanúsítványlánc továbbra is lehet történő regisztrálását, ha egy engedélyezett beléptetési csoportot a legfelső szintű tanúsítvány vagy egy másik köztes tanúsítvány tanúsítványukat feljebb lánc létezik-e.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Blacklist konkrét eszközökhöz regisztrációs-csoportban

Eszközök, amelyek megvalósítják az X.509 tanúsítvány mechanizmus az eszköz tanúsítványlánc és a titkos kulcs segítségével hitelesíteni. Amikor egy eszköz csatlakozik, és hitelesíti az eszköz kiépítése szolgáltatáshoz, a szolgáltatás először megkeresi az egyes tagság, amely megfelel az eszköz hitelesítő adatokat. A szolgáltatás majd annak meghatározásához, hogy az eszköz kiépítése beléptetési csoportok keres. Ha a szolgáltatás egy letiltott egyes regisztrációt az adott eszköz talál, megakadályozza, hogy az eszköz csatlakozzon. Akkor is, ha egy közbenső engedélyezett beléptetési csoportot vagy egy legfelső szintű Hitelesítésszolgáltatói tanúsítványlánc az eszköz már létezik a szolgáltatás megakadályozza, hogy a kapcsolat. 

Az egyes eszköz beléptetési csoportban tiltólistára, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portál, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájához jelölje ki a létesítési szolgáltatás, amely tartalmazza a beléptetési csoport, az eszköz tiltólistára kell helyezni kívánt.
3. A létesítési szolgáltatás, válassza ki **regisztrációkat kezelése**, majd válassza ki a **egyedi regisztrációkat** lapon.
4. Válassza ki a **Hozzáadás** gombra az oldal tetején. 
5. Válassza ki **X.509** igazoló mechanizmusként használható az eszköz, és feltöltése az eszköz tanúsítványát. Ez az az aláírt végfelhasználói tanúsítvány telepítve az eszközön. Az eszköz tanúsítványainak létrehozásához szükséges hitelesítési használja azt.
6. A **IoT-központ Eszközazonosító**, adjon meg az Azonosítót az eszközt. 
7. Válassza ki **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**. 

    [![Használata le van tiltva, az egyes regisztrációs bejegyzés letiltani az eszközt a csoport regisztrációjukat, a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

A regisztráció sikeres létrehozásakor megjelennie az eszköz jelenik meg a **egyedi regisztrációkat** fülre.

## <a name="next-steps"></a>További lépések

Disenrollment is a nagyobb megszüntetési folyamatot részét képezi. Egy eszköz megszüntetés disenrollment a szolgáltatás kiépítését, és tartalmaz az IoT-központ deregistering. A teljes folyamattal kapcsolatos további tudnivalókért lásd: [hogyan korábban automatikus-kiépített eszközök kiosztásának megszüntetése](how-to-unprovision-devices.md) 

