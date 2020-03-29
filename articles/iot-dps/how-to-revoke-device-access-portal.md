---
title: Az Azure IoT Hub-eszközkiépítési szolgáltatásból való leiratkozási eszköz
description: Eszköz leválasztása az Azure IoT Hub-eszközkiépítési szolgáltatáson (DPS) keresztültörténő kiépítés megakadályozására
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974921"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Eszköz leválasztása az Azure IoT Hub-eszközkiépítési szolgáltatásból

Az eszközhitelesítő adatok megfelelő kezelése kulcsfontosságú a nagy horderejű rendszerek, például az IoT-megoldások számára. Az ilyen rendszerek ajánlott eljárása, hogy egyértelmű tervvel rendelkeznek az eszközök hozzáférésének visszavonására vonatkozóan, ha a hitelesítő adataik – akár egy megosztott hozzáférésű aláírási jogkivonat, akár egy X.509-es tanúsítvány – sérülhetnek. 

Az eszközkiépítési szolgáltatásba való regisztráció lehetővé teszi az eszköz [automatikus kiépítését.](concepts-auto-provisioning.md) A kiépített eszköz az IoT Hub, amely lehetővé teszi, hogy megkapja a kezdeti [eszköz ikerállapotát,](~/articles/iot-hub/iot-hub-devguide-device-twins.md) és kezdje meg a telemetriai adatok jelentését. Ez a cikk ismerteti, hogyan lehet leválasztani egy eszközt a létesítési szolgáltatás példánya, megakadályozva, hogy a jövőben újra kiépítésre kerüljön.

> [!NOTE] 
> Vegye figyelembe az eszközök újrapróbálkozási házirendjeit, amelyekhez visszavonja a hozzáférést. Például egy eszköz, amely egy végtelen újrapróbálkozási szabályzat ot folyamatosan megpróbálhat regisztrálni a létesítési szolgáltatás. Ez a helyzet szolgáltatási erőforrásokat használ fel, és valószínűleg befolyásolja a teljesítményt.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Feketelista eszközök egyéni beléptetési bejegyzés használatával

Az egyéni regisztrációk egyetlen eszközre vonatkoznak, és igazolási mechanizmusként x.509-es tanúsítványokat vagy SAS-jogkivonatokat (valós vagy virtuális TPM-ben) használhatnak. (A SAS-jogkivonatokat tanúsítványmechanizmusként használó eszközök csak egyéni regisztrációval építhetők ki.) Ha egy egyéni regisztrációval rendelkező eszközt szeretne feketelistára tenni, letilthatja vagy törölheti a regisztrációs bejegyzést. 

Az eszköz ideiglenes feketelistázása a regisztrációs bejegyzés letiltásával: 

1. Jelentkezzen be az Azure Portalra, és válassza a bal oldali menü **Minden erőforrás** lehetőséget.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyből feketelistára szeretné tenni az eszközt.
3. A létesítési szolgáltatásban válassza a **Regisztrációk kezelése**lehetőséget, majd az **Egyéni beléptetések** lapot.
4. Válassza ki a feketelistára tenni kívánt eszköz regisztrációs bejegyzését. 

    ![Válassza ki az egyéni regisztrációt](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. A beléptetési lapon görgessen le az aljára, és válassza a **Letiltás lehetőséget** a **Bejegyzés engedélyezése** kapcsolóhoz, majd kattintson a **Mentés gombra.**  

   ![Egyéni beléptetési bejegyzés letiltása a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Az eszköz végleges feketelistázása a regisztrációs bejegyzés törlésével:

1. Jelentkezzen be az Azure Portalra, és válassza a bal oldali menü **Minden erőforrás** lehetőséget.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyből feketelistára szeretné tenni az eszközt.
3. A létesítési szolgáltatásban válassza a **Regisztrációk kezelése**lehetőséget, majd az **Egyéni beléptetések** lapot.
4. Jelölje be a feketelistára tenni kívánt eszköz regisztrációs bejegyzése melletti jelölőnégyzetet. 
5. Válassza a **Törlés** lehetőséget az ablak tetején, majd az **Igen** gombra a regisztráció eltávolításának megerősítéséhez. 

   ![Egyéni beléptetési bejegyzés törlése a portálon](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Az eljárás befejezése után látnia kell, hogy a bejegyzés törlődik az egyes regisztrációk listájáról.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>X.509 köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítvány feketelistára kerülése igénylési csoport használatával

Az X.509 tanúsítványok általában egy megbízhatósági tanúsítványláncba vannak rendezve. Ha egy tanúsítvány a lánc bármely szakaszában veszélybe kerül, a megbízhatóság megszakad. A tanúsítványnak feketelistára kell tenni, hogy megakadályozza, hogy az eszközkiépítési szolgáltatás az adott tanúsítványt tartalmazó lánc későbbi rétegében lévő eszközöket létesítse. Az X.509-tanúsítványokról és a kiépítési szolgáltatással való használatukról az [X.509 tanúsítványok című témakörben olvashat bővebben.](./concepts-security.md#x509-certificates) 

A regisztrációs csoport olyan eszközök bejegyzése, amelyek azonos köztes vagy legfelső szintű hitelesítésszolgáltató által aláírt X.509 tanúsítványok közös tanúsítvány-mechanizmusával rendelkeznek. A regisztrációs csoport bejegyzése a köztes vagy legfelső szintű hitelesítésszolgáltatóhoz társított X.509 tanúsítvánnyal van konfigurálva. A bejegyzés konfigurálva van minden olyan konfigurációs értékkel, például a kétállamos és az IoT hub-kapcsolattal, amelyeket az adott tanúsítvánnyal rendelkező eszközök megosztanak a tanúsítványláncban. A tanúsítvány feketelistára kerüléséhez letilthatja vagy törölheti a tanúsítvány igénylési csoportját.

A tanúsítvány ideiglenes feketelistázása a beléptetési csoport letiltásával: 

1. Jelentkezzen be az Azure Portalra, és válassza a bal oldali menü **Minden erőforrás** lehetőséget.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyből feketelistára szeretné tenni az aláíró tanúsítványt.
3. A létesítési szolgáltatásban válassza a **Regisztrációk kezelése**lehetőséget, majd a **Regisztrációs csoportok** lapot.
4. Jelölje ki a feketelistára kívánt tanúsítványt használó regisztrációs csoportot.
5. A Belépési kapcsoló **engedélyezése,** majd a **Mentés**lehetőséget, a **Letiltás** lehetőséget.  

   ![A regisztrációs csoport bejegyzésének letiltása a portálon](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
A tanúsítvány végleges feketelistázása a beléptetési csoport törlésével:

1. Jelentkezzen be az Azure Portalra, és válassza a bal oldali menü **Minden erőforrás** lehetőséget.
2. Az erőforrások listájában válassza ki azt a kiépítési szolgáltatást, amelyből feketelistára szeretné tenni az eszközt.
3. A létesítési szolgáltatásban válassza a **Regisztrációk kezelése**lehetőséget, majd a **Regisztrációs csoportok** lapot.
4. Jelölje be a feketelistára tenni kívánt tanúsítvány igénylési csoportja melletti jelölőnégyzetet. 
5. Válassza a **Törlés** lehetőséget az ablak tetején, majd az **Igen** gombra a beléptetési csoport eltávolításának megerősítéséhez. 

   ![Regisztrációs csoport bejegyzésének törlése a portálon](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Az eljárás befejezése után a bejegyzésnek el kell távolítania a regisztrációs csoportok listájáról.  

> [!NOTE]
> Ha töröl egy tanúsítvány igénylési csoportját, a tanúsítványláncban lévő tanúsítvánnyal rendelkező eszközök továbbra is regisztrálhatnak, ha a főtanúsítványhoz engedélyezett igénylési csoport vagy a tanúsítványban magasabban lévő másik köztes tanúsítvány regisztrálható. lánc létezik.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Adott eszközök feketelistája egy regisztrációs csoportban

Az X.509 tanúsítványmechanizmust megvalósító eszközök az eszköz tanúsítványláncát és személyes kulcsát használják a hitelesítéshez. Amikor egy eszköz csatlakozik, és hitelesíti az eszköz kiépítési szolgáltatás, a szolgáltatás először megkeresi az egyéni regisztráció, amely megfelel az eszköz hitelesítő adatait. A szolgáltatás ezután megkeresi a regisztrációs csoportokat, és megállapítja, hogy az eszköz kiépíthető-e. Ha a szolgáltatás letiltott egyéni regisztrációt talál az eszközhöz, megakadályozza az eszköz csatlakoztatását. A szolgáltatás akkor is megakadályozza a kapcsolatot, ha az eszköz tanúsítványláncában egy köztes vagy legfelső szintű hitelesítésszolgáltató hoz engedélyezett igénylési csoport létezik. 

Ha egy regisztrációs csoportban lévő eszközt szeretne feketelistára tenni, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra, és válassza a bal oldali menü **Minden erőforrás** lehetőséget.
2. Az erőforrások listájából válassza ki azt a létesítési szolgáltatást, amely a feketelistára tenni kívánt eszköz regisztrációs csoportját tartalmazza.
3. A létesítési szolgáltatásban válassza a **Regisztrációk kezelése**lehetőséget, majd az **Egyéni beléptetések** lapot.
4. A lista tetején válassza az **Egyéni beléptetés hozzáadása** gombot. 
5. A **Beiktatás hozzáadása** lapon válassza az **X.509** lehetőséget az eszköz igazolási **mechanizmusaként.**

    Töltse fel az eszköztanúsítványt, és adja meg a feketelistára kerülő eszköz eszközazonosítóját. A tanúsítványhoz használja az eszközre telepített aláírt végfelhasználói tanúsítványt. Az eszköz az aláírt végfelhasználói tanúsítványt használja a hitelesítéshez.

    ![A feketelistára tett eszköz eszköztulajdonságainak beállítása](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Görgessen a **Beiktatás hozzáadása** lap aljára, és válassza a **Letiltás lehetőséget** a **Bejegyzés engedélyezése** kapcsolón, majd kattintson a **Mentés gombra.** 

    [![A letiltott egyéni beléptetési bejegyzés használata az eszköz csoportregisztrációból való letiltásához a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Ha sikeresen létrehozta a regisztrációt, a letiltott eszközregisztrációk at az Egyéni regisztrációk lapon kell **látnia.** 

## <a name="next-steps"></a>További lépések

A disenrollment szintén része a nagyobb megszüntetési folyamatnak. Az eszköz megszüntetése magában foglalja a kiépítési szolgáltatásból való kihelyezést és az IoT hubról való regisztráció törlését. A teljes folyamatról a [Korábban automatikusan kiépített eszközök kiirtása című témakörben olvashat.](how-to-unprovision-devices.md) 

