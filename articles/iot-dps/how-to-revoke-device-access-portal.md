---
title: Hogyan lehet egy eszköz, az Azure IoT Hub Device Provisioning Service disenroll
description: Hogyan disenroll egy eszközt, hogy a telepítés az Azure IoT Hub Device Provisioning Service használatával
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: e66d896a7df48645dad39b5b978c4f7c2f8d8cb9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954551"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Hogyan lehet egy eszköz, az Azure IoT Hub Device Provisioning Service disenroll

Az eszköz hitelesítő adatai a megfelelő felügyeleti létfontosságú rendszerek nagy IoT-megoldások. A megfelelő törölje az eszközök hozzáférésének visszavonása az ilyen rendszerek ajánlott eljárás, ha a hitelesítő adataikat, akár egy közös hozzáférésű jogosultságkódok (SAS) tokent egy X.509 tanúsítvány utaló jeleket. 

Regisztráció a Device Provisioning Service lehetővé teszi, hogy egy eszköz [automatikus regisztrációt](concepts-auto-provisioning.md). Kiépített eszköz, amely regisztrálva van az IoT hubbal, lehetővé téve, hogy az első kap [ikereszköz](~/articles/iot-hub/iot-hub-devguide-device-twins.md) állapotát, és megkezdheti a telemetriai adatok jelentéskészítés. Ez a cikk ismerteti egy eszközt a kiépítési szolgáltatás-példány, meggátolja, hogy a jövőben újra üzembe a disenroll.

> [!NOTE] 
> Vegye figyelembe az újrapróbálkozási szabályzatot az eszközök, amelyek meg visszavonni a hozzáférést. Például egy végtelen újrapróbálkozási szabályzat rendelkező eszközök esetén előfordulhat, hogy folyamatosan próbál meg regisztrálni a kiépítési szolgáltatáshoz. Ez a helyzet szolgáltatási erőforrásokat használ fel, és valószínűleg befolyásolja a teljesítményt.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Az egyéni regisztrációs bejegyzést blacklist eszközök

Egyéni regisztrációk egy adott eszköz a alkalmazni, és X.509-tanúsítványokat vagy SAS-tokeneket (a valós vagy virtuális TPM) is használható, az igazolási mechanizmusként. (Eszközökön, azok igazolási mechanizmus bővítheti csak egyéni regisztrációt a SAS-tokeneket használó.) A tiltólistára egyéni regisztrációt egy eszközhöz, tiltsa le, vagy a regisztrációs bejegyzés törlése. 

Ideiglenes tiltólistára tiltsa le a regisztrációs bejegyzés az eszközön: 

1. Jelentkezzen be az Azure Portalon, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájában válassza ki a kiépítési szolgáltatás, amelyet az eszköz tiltólistára szeretne.
3. Válassza ki az eszközkiépítési szolgáltatás **beléptetések kezelése**, majd válassza ki a **egyéni regisztrációk** fülre.
4. Válassza ki a tiltólistára kívánt eszköz beléptetési bejegyzésében. 

    ![Válassza ki az egyéni regisztráció](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. A regisztrációs oldalon görgessen le, és válassza ki **letiltása** számára a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**.  

   ![Tiltsa le a portálon az egyéni regisztrációs bejegyzéssel](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

A végleges tiltólistára az eszköz a regisztrációs bejegyzés törlésével:

1. Jelentkezzen be az Azure Portalon, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájában válassza ki a kiépítési szolgáltatás, amelyet az eszköz tiltólistára szeretne.
3. Válassza ki az eszközkiépítési szolgáltatás **beléptetések kezelése**, majd válassza ki a **egyéni regisztrációk** fülre.
4. Válassza ki a tiltólistára kívánt az eszköz beléptetési bejegyzésében melletti jelölőnégyzetet. 
5. Válassza ki **törlése** az ablakot, és válassza ki a felső **Igen** annak ellenőrzéséhez, hogy el kívánja távolítani a regisztrációt. 

   ![A portál az egyéni regisztrációs bejegyzés törlése](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Az eljárás befejezése után megtekintheti a bejegyzés el lett távolítva, az egyéni regisztrációk listájából.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>A regisztrációs csoportot egy köztes X.509 vagy a legfelső szintű Hitelesítésszolgáltatói tanúsítványt tiltólistára

X.509-tanúsítványokat általában elrendezésére egy tanúsítvány megbízhatósági láncában. Egy tanúsítványt a lánc minden szakaszában biztonságának sérülése esetén, ha megszakad a bizalmi kapcsolat. A tanúsítvány megakadályozza, hogy Device Provisioning Service üzembe helyezési eszközök aktiválásához bármely láncot, amely tartalmazza ezt a tanúsítványt tiltólistán kell lennie. X.509-tanúsítványokat és azok hogyan használhatók a kiépítési szolgáltatással kapcsolatos további tudnivalókért lásd: [X.509 tanúsítványok](./concepts-security.md#x509-certificates). 

Egy regisztrációs csoportnak bejegyzés az eszközök, amelyek egy közös igazolási eljárás ugyanaz a köztes által aláírt X.509-tanúsítványokat, megosztás, vagy a legfelső szintű hitelesítésszolgáltató. A regisztrációscsoport-bejegyzést a köztes társított X.509-tanúsítvány van konfigurálva, vagy a legfelső szintű hitelesítésszolgáltató. A bejegyzés is bármely konfigurációs értékek, például az ikereszköz állapota és az IoT hub kapcsolati, a tanúsítványláncukban megosztott eszközök a tanúsítvánnyal van konfigurálva. A tiltólistára a tanúsítványt, vagy letilthatja vagy a regisztrációs csoport törlése.

Ideiglenes tiltólistára tiltsa le a regisztrációs csoport a tanúsítványt: 

1. Jelentkezzen be az Azure Portalon, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájában válassza ki a kiépítési szolgáltatás tiltólistára az aláíró tanúsítványt a kívánt.
3. Válassza ki az eszközkiépítési szolgáltatás **beléptetések kezelése**, majd válassza ki a **regisztrációs csoportok** fülre.
4. Válassza ki a regisztrációs csoport tiltólistára kívánt tanúsítvány használatával.
5. Válassza ki **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**.  

   ![Tiltsa le a regisztrációscsoport-bejegyzést a portálon](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
A végleges tiltólistára a tanúsítványt a regisztrációs csoport törlésével:

1. Jelentkezzen be az Azure Portalon, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájában válassza ki a kiépítési szolgáltatás, amelyet az eszköz tiltólistára szeretne.
3. Válassza ki az eszközkiépítési szolgáltatás **beléptetések kezelése**, majd válassza ki a **regisztrációs csoportok** fülre.
4. Válassza ki a regisztrációs csoport a tiltólistára kívánt tanúsítvány melletti jelölőnégyzetet. 
5. Válassza ki **törlése** az ablakot, és válassza ki a felső **Igen** annak ellenőrzéséhez, hogy el kívánja távolítani a regisztrációs csoport. 

   ![A portálon a regisztrációs csoport bejegyzés törlése](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Az eljárás befejezése után megtekintheti a bejegyzés eltávolítva a regisztrációs csoportok listája.  

> [!NOTE]
> A tanúsítvány regisztrációs csoportot töröl, a tanúsítványláncukban a tanúsítvánnyal rendelkező eszközök továbbra is lehet majd regisztrálni, ha a legfelső szintű tanúsítvány vagy egy másik köztes tanúsítványt a tanúsítvány alakult engedélyezett regisztrációs csoportot lánc létezik.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Blacklist adott eszközökre egy regisztrációs csoportnak

Az X.509-igazolási mechanizmus megvalósító eszközök tanúsítványlánc a eszközt és a titkos kulcs használatával hitelesíteni. Ha egy eszköz csatlakozik, és hitelesíti a Device Provisioning Service szolgáltatással, a szolgáltatás először megkeresi egyéni regisztrációt, amely megfelel az eszköz hitelesítő adatait. A szolgáltatás majd meghatározni, hogy az eszköz kiépítése a regisztrációs csoportok keres. Ha a szolgáltatás egy letiltva az eszköz egyéni regisztrációjának talál, kapcsolódás az eszköz megakadályozza. A szolgáltatás megakadályozza a kapcsolat, akkor is, ha létezik egy engedélyezett regisztrációs csoportot egy köztes vagy a legfelső szintű hitelesítésszolgáltató tanúsítványlánc a eszközt. 

A tiltólistára egy adott eszköz egy regisztrációs csoportnak, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon, és válassza **összes erőforrás** a bal oldali menüből.
2. Az erőforrások listájában válassza ki a kiépítési szolgáltatás, amely tartalmazza a regisztrációs csoport a tiltólistára kívánt eszköz.
3. Válassza ki az eszközkiépítési szolgáltatás **beléptetések kezelése**, majd válassza ki a **egyéni regisztrációk** fülre.
4. Válassza ki a **egyéni regisztráció hozzáadása** gombra az oldal tetején. 
5. Az a **regisztráció hozzáadása** lapon jelölje be **X.509** elemet az igazolási **mechanizmus** az eszközhöz.

    Töltse fel az eszköz tanúsítványt, és adja meg az eszköz azonosítója, az eszköz a tiltólistán lehet. A tanúsítvány használata az aláírt végfelhasználói tanúsítvány telepítve az eszközön. Az eszköz, a végfelhasználói önaláírt tanúsítványt használ.

    ![Állítsa be a Feketelistára tett eszköz tulajdonságai](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Alján görgessen a **regisztráció hozzáadása** lapon, és válassza **letiltása** a a **bejegyzés engedélyezése** váltson, és válassza ki **mentése**. 

    [![Használata le van tiltva az egyéni regisztrációs bejegyzés csoportregisztráció a portálon az eszköz letiltása](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

A regisztráció sikeresen létrehozásakor megjelenik a letiltott eszközök regisztrálásával visszalépve megtekintheti a **egyéni regisztrációk** fülre. 

## <a name="next-steps"></a>További lépések

Regisztráció megszüntetésének is a nagyobb megszüntetési folyamatot részét képezi. Megszüntetés eszköz tartalmaz, a létesítési szolgáltatásból regisztráció megszüntetésének, mind az IoT hubról megszüntetése. A teljes folyamat kapcsolatos további információkért lásd: [hogyan eszközöket, amelyek korábban automatikus – kiépített megszüntetése](how-to-unprovision-devices.md) 

