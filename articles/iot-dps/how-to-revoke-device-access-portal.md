---
title: "Eszköz-hozzáférés kezelése Azure IoT Hub eszköz kiépítése szolgáltatáshoz |} Microsoft Docs"
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
ms.openlocfilehash: 12aebf3a56aa7469a765ab6fc67aa65b254db71a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>A létesítési szolgáltatás az Azure portálon eszköz hozzáférés megvonása

Magas-profil rendszerek például az IoT-megoldások elengedhetetlen a megfelelő felügyeleti eszköz hitelesítő adatokat. Az ilyen rendszerek esetén ajánlott eljárás, visszavonhatja a hozzáférést az eszközök hogyan egyértelmű terv hogy mikor megadhatják hitelesítő adataikat, hogy egy közös hozzáférésű jogosultságkódokat (SAS) jogkivonatot, vagy egy X.509 tanúsítvány utaló jeleket. Ez a cikk ismerteti a kiépítési lépésnél eszközök hozzáférésének visszavonása.

Az IoT-központ eszköz eléréséhez visszavonása után az eszköz van megadva, lásd: [eszközök letiltása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Vegye figyelembe az újrapróbálkozási házirendet az eszközről, amelyen meg visszavonni a hozzáférést. Például egy eszköz, amely rendelkezik egy végtelen újrapróbálkozási házirendje előfordulhat, hogy folyamatosan próbálja meg regisztrálni az üzembe helyezési szolgáltatással. Ilyen esetben a szolgáltatás-erőforrásokat használ fel, és valószínűleg hatással van a teljesítményre.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Az egyes regisztrációs bejegyzés használatával blacklist eszközök

Egyes regisztrációkat egyetlen eszközt vonatkoznak, és X.509-tanúsítványokat vagy SAS-tokenje (a valós vagy virtuális TPM) is használhatja, igazoló mechanizmusként. (Használó eszközök SAS-tokenje módon csak egy egyedi igénylés révén az igazolás mechanizmus létesíthetők.) A tiltólistára az egyes tagság tartalmazó eszköz, letiltása, vagy a regisztrációs bejegyzés törlése. 

Ideiglenes tiltólistára az eszköz a regisztrációs bejegyzés letiltásával: 

1. Jelentkezzen be az Azure portál, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájához válassza ki a létesítési szolgáltatás, amelyet az eszköz tiltólistára kell helyezni.
3. A létesítési szolgáltatás, válassza ki **regisztrációkat kezelése**, majd válassza ki a **egyedi regisztrációkat** lapon.
4. Válassza ki a regisztrációs bejegyzés az eszköz, amelyet tiltólistára kell helyezni. 
5. Válassza ki **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**.  

   ![Tiltsa le az egyes regisztrációs bejegyzés a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
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
4. Válassza ki a regisztrációs a tanúsítványt, amelyet tiltólistára kell helyezni.
5. Válassza ki a regisztrációs csoport-bejegyzésben **csoport szerkesztése**.
6. Válassza ki **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**.  

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
5. Válassza ki **X.509** a biztonsági mechanizmus az eszköz, és feltöltése az eszköz tanúsítványát. Ez az az aláírt végfelhasználói tanúsítvány telepítve az eszközön. Az eszköz tanúsítványainak létrehozásához szükséges hitelesítési használja azt.
6. A **IoT-központ Eszközazonosító**, adjon meg az Azonosítót az eszközt. 
7. Válassza ki **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**. 

   ![Tiltsa le az egyes regisztrációs bejegyzés a portálon](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

A regisztráció sikeres létrehozásakor megjelennie az eszköz jelenik meg a **egyedi regisztrációkat** fülre.


