---
title: "Eszköz-hozzáférés kezelése az Azure IoT Hub eszköz kiépítése szolgáltatáshoz |} Microsoft Docs"
description: "Eszköz-hozzáférés az Azure portálon a terjesztési pontok szolgáltatásbeli visszavonása"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Az üzembe helyezési szolgáltatás az Azure portálon eszköz elérését visszavonása

Magas-profil rendszerek például az IoT-megoldások elengedhetetlen a megfelelő felügyeleti eszköz hitelesítő adatokat. Az ilyen rendszerek esetén ajánlott eljárás, hogy egy tiszta terv mutatja be az eszközök azokban az esetekben, ahol megadhatják hitelesítő adataikat, hogy egy SAS-jogkivonat vagy egy X.509 tanúsítvány feltörhetik a hozzáférés visszavonásához. Ez a cikk ismerteti a kiépítési lépésnél eszközök hozzáférésének visszavonása.

További információt az IoT-központ az eszközök hozzáférésének visszavonása után az eszköz lett kiépítve. Lásd: [letiltása eszközök](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Vegye figyelembe az újrapróbálkozási házirendet az eszközről, amelyen meg visszavonni a hozzáférést. Például egy végtelen újrapróbálkozási házirendje rendelkező eszköz, előfordulhat, hogy folyamatosan próbálja meg regisztrálni az üzembe helyezési szolgáltatással, szolgáltatás-erőforrást kötött, és esetleg a teljesítményt befolyásoló.

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>Olyan egyedi regisztrációs bejegyzés blacklist eszközök

Egyes regisztrációkat egyetlen eszközt vonatkoznak, és előfordulhat, hogy használja X.509-tanúsítványokat vagy (a valós vagy virtuális TPM) SAS-tokenje igazoló mechanizmusként. (Használó eszközök SAS-tokenje, az igazolás mechanizmus csak egy egyedi igénylés révén létesíthetők.) A tiltólistára az egyes tagság tartalmazó eszköz, letiltása, vagy a regisztrációs bejegyzés törlése: 

- Az eszköz ideiglenesen tiltólistára, tiltsa le a regisztrációs bejegyzés. 

    1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüből.
    2. Kattintson a kívánt tiltólistára az eszköz regisztrációját az erőforrások listájához a létesítési szolgáltatás.
    3. A létesítési szolgáltatás kattintson **regisztrációkat kezelése**, majd válassza **egyedi regisztrációkat** lapon.
    4. Kattintson a regisztrációs bejegyzés tiltólistára Nyissa meg a kívánt eszközt. 
    5. Kattintson a **letiltása** beléptetési listaelemet alján kattintson a **mentése**.  

        ![Tiltsa le az egyes regisztrációs bejegyzés a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- Az eszköz véglegesen tiltólistára, törölheti a regisztrációs bejegyzés.

    1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüből.
    2. Kattintson a kívánt tiltólistára az eszköz regisztrációját az erőforrások listájához a létesítési szolgáltatás.
    3. A létesítési szolgáltatás kattintson **regisztrációkat kezelése**, majd válassza **egyedi regisztrációkat** lapon.
    4. A regisztrációs bejegyzés tiltólistára szeretné az eszköz melletti négyzetet. 
    5. Kattintson a **törlése** az ablak tetején, majd kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e távolítsa el a regisztrációt. 

        ![A portál egyes regisztrációs bejegyzés törlése](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. Ha a művelet befejeződött, a bejegyzés eltávolítja a listáról, az egyes regisztrációkat jelenik meg.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>Egy X.509 köztes vagy a legfelső szintű Hitelesítésszolgáltatói tanúsítvány beléptetési csoport használata tiltólistára

X.509-tanúsítványokat általában egy megbízhatósági láncot vannak rendezve. Egy tanúsítványt egy lánc bármely szakaszában biztonságának sérülése esetén, ha bármilyen okból megszakad, és a tanúsítvány feketelistára kell teszi a bármely lánc alatt hozta-e az eszköz kiépítése szolgáltatáshoz a tanúsítvány tartalmazó adatfolyam eszközöket megelőzése érdekében. X.509-tanúsítványokat és azok használata az üzembe helyezési szolgáltatással kapcsolatos további információkért lásd: [X.509-tanúsítványokat](./concepts-security.md#x509-certificates). 

Egy regisztrációs csoport egy bejegyzést az eszközöket, amelyek egy közös igazolás mechanizmus az X.509 tanúsítvány aláírásával ugyanaz a köztes rendelkezik, illetve a legfelső szintű hitelesítésszolgáltató. A beléptetési csoport bejegyzést a köztes vagy legfelső szintű hitelesítésszolgáltató, valamint bármely konfigurációs értékeket, például a kettős állapot és az IoT hub kapcsolat, a tanúsítvány tanúsítvány-eszközök által megosztott társított X.509-tanúsítvány van konfigurálva lánc. A tanúsítvány tiltólistára, hogy tiltsa le, vagy a beléptetési csoport törlése:

- A tanúsítvány ideiglenesen tiltólistára, tiltsa le a regisztrációs csoport. 

    1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüből.
    2. Kattintson a kívánt tiltólistára az aláíró tanúsítvány az erőforrások listájához a létesítési szolgáltatás.
    3. A létesítési szolgáltatás kattintson **regisztrációkat kezelése**, majd jelölje be **beléptetési csoportok** lapon.
    4. Kattintson a regisztráció csoportra való megnyitásához tiltólistára kell helyezni kívánt tanúsítvány.
    5. Kattintson a **csoport szerkesztése** , a bal felső a beléptetési csoport-bejegyzésben.
    6. A regisztrációs bejegyzés letiltásához válassza **letiltása** a a **bejegyzés engedélyezése** váltani, majd kattintson az **mentése**.  

        ![Tiltsa le a regisztrációs csoport bejegyzés a portálon](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- Végleg tiltólistára a tanúsítványt, törölheti a beléptetési csoport.

    1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüből.
    2. Kattintson a kívánt tiltólistára az eszköz regisztrációját az erőforrások listájához a létesítési szolgáltatás.
    3. A létesítési szolgáltatás kattintson **regisztrációkat kezelése**, majd jelölje be **beléptetési csoportok** lapon.
    4. A beléptetési csoport tiltólistára kell helyezni kívánt tanúsítvány melletti négyzetet. 
    5. Kattintson a **törlése** az ablak tetején, majd kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e a beléptetési csoport eltávolítása. 

        ![A portál regisztrációs csoport bejegyzés törlése](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. Ha a művelet befejeződött, a beléptetési csoportok listájának távolítva bejegyzés jelenik meg.  

> [!NOTE]
> Ha töröl egy beléptetési csoport tanúsítványt, a tanúsítványlánc lévő tanúsítvány eszközök továbbra is lehet regisztrálni, ha egy engedélyezett beléptetési csoportot a legfelső szintű tanúsítvány vagy egy másik köztes tanúsítvány tanúsítványukat feljebb lánc létezik-e.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Blacklist konkrét eszközökhöz regisztrációs-csoportban

Eszközök, amelyek megvalósítják az X.509 tanúsítvány mechanizmus az eszköz tanúsítványlánc és a titkos kulcs segítségével hitelesíteni. Ha egy eszköz csatlakozik, és ezzel hitelesíti a eszköz kiépítése szolgáltatáshoz, a szolgáltatás először megkeresi az egyes tagság megfelelő az eszköz hitelesítő adatok meghatározására, hogy az eszköz kiépítése beléptetési csoportok keresése előtt. Ha a szolgáltatás egy letiltott egyes regisztrációt az adott eszköz talál, megakadályozza, hogy az eszköz csatlakozni, akkor is, ha egy közbenső engedélyezett beléptetési csoportot vagy egy legfelső szintű Hitelesítésszolgáltatói tanúsítványlánc az eszköz már létezik. Az egyes eszköz beléptetési csoportban tiltólistára, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájához kattintson a létesítési szolgáltatás, amely tartalmazza a beléptetési csoport tiltólistára szeretné az eszköz.
3. A létesítési szolgáltatás kattintson **regisztrációkat kezelése**, majd válassza **egyedi regisztrációkat** lapon.
4. Kattintson a **Hozzáadás** gombra az oldal tetején. 
5. Válassza ki **X.509** a biztonsági mechanizmus a eszköz- és feltöltése az eszköz tanúsítványát. Ez az az aláírt végfelhasználói tanúsítvány telepítve az eszközön, így-hitelesítési tanúsítványok létrehozásához használ.
6. Adja meg a **IoT-központ Eszközazonosító** az eszközön. 
7. A regisztrációs bejegyzés letiltásához válassza **letiltása** a a **bejegyzés engedélyezése** váltani. 
8. Kattintson a **Save** (Mentés) gombra. A regisztráció sikeres létrehozása, a kell megjelennie az eszköz alatt jelennek meg a **egyedi regisztrációkat** lapon. 

    ![Tiltsa le az egyes regisztrációs bejegyzés a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




