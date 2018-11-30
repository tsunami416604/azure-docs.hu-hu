---
title: Vásárlói adatok funkciók
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5dd027c886e8102e77ddefe93817daee0e1ec29b
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584151"
---
# <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása

Az Azure IoT Hub Device Provisioning Service szolgáltatás REST API-alapú vállalati ügyfelek célzott, amely lehetővé teszi a zökkenőmentes, automatizált beavatkozás nélküli kiépítése Azure IoT Hub-eszközök az eszközön kezdődő és a felhőben végződő biztonsággal.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Egyes eszközök vannak rendelve egy regisztrációs és eszköz-azonosító szerint egy Bérlői rendszergazda. Ezek az eszközök kapcsolatos adatok alapján azonosítóit a részletekben. A Microsoft semmilyen adatot megőrzi, és nem fér hozzá, amelyek lehetővé teszik, hogy az eszközök egyéni korrelációs adatokat.

Számos a Device Provisioning Service-ben kezelt eszközök, amelyek nem személyes eszközök, például az office thermostat vagy a gyári robot. Az ügyfelek is, azonban figyelembe kell venni néhány eszközt személyes azonosításra alkalmas lehet, és saját belátása szerint a saját eszköz vagy a leltár nyomon követése módszerek elősegítsék eszközök egyéni felhasználók számára fenntarthatják. Device Provisioning Service kezeli, és, mintha a személyes adatok eszközökhöz társított minden adatot tárol.

A bérlői rendszergazdák segítségével az Azure Portalon vagy a szolgáltatás REST API-k információk kérések teljesítését exportálásával vagy Eszközazonosító vagy regisztrációs azonosító tartozó adatok törlése

> [!NOTE]
> Az Azure IoT Hub Device Provisioning Service használatával kiépített eszközök további, az Azure IoT Hub szolgáltatásban tárolt adatok rendelkezik. Tekintse meg a [Azure IoT Hub dokumentációja](../iot-hub/iot-hub-customer-data-requests.md) megtelt a kérelmek, az adott eszköz befejezéséhez.

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése

Device Provisioning Service-regisztrációk és a regisztrációs rekord tárolja. Regisztrációk információkat tartalmaznak arról, hogy mely építhető ki eszközök és regisztrációs rekordok jelenítik meg, amely az eszközök már elvégezte a kiépítési folyamat.

A bérlői rendszergazdák regisztrációk távolíthatja el az Azure Portalról, és ezzel eltávolítja a kapcsolódó regisztrációs rekordokat is.

További információkért lásd: [eszközök beléptetésének kezelése](how-to-manage-enrollments.md).

Lehetőség arra is regisztrációk és regisztrációs bejegyzések REST API-k használatával a törlési műveletek végrehajtásához:

* Regisztrációs információk egy adott eszköz törléséhez használhatja [Eszközregisztráció - Törlés](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Regisztrációs információk az eszközök egy csoportját törléséhez használhatja [eszköz regisztrációs csoport - törlési](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Kiépített eszközökkel kapcsolatos információk törléséhez használhatja [regisztrációs állapot - törlési regisztrációs állapot](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása

Device Provisioning Service-regisztrációk és a regisztrációs rekord tárolja. Regisztrációk információkat tartalmaznak arról, hogy mely építhető ki eszközök és regisztrációs rekordok jelenítik meg, amely az eszközök már elvégezte a kiépítési folyamat.

A bérlői rendszergazdák regisztrációk és regisztrációs rekordok az Azure portal segítségével megtekintheti és exportálásához használja a másolás és beillesztés.

Regisztrációk kezeléséről további információkért lásd: [eszközök beléptetésének kezelése](how-to-manage-enrollments.md).

Lehetőség arra is regisztrációk és a REST API-k használatával regisztrációs rekordok exportálása műveleteinek elvégzéséhez:

* Egy adott eszköz beléptetési információit exportálásához használja [Eszközregisztráció – Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Szeretné exportálni a regisztrációs információk az eszközök egy csoportját, használhatja a [eszköz regisztrációs csoport - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Már kiépített eszközökkel kapcsolatos információk exportálásához használja [regisztrációs állapot - Get-regisztrációs állapot](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> A Microsoft enterprise szolgáltatást használ, a Microsoft néhány információt, a rendszer által létrehozott naplók néven hoz létre. Egyes Device Provisioning Service-rendszer által létrehozott naplókban nincsenek elérhető vagy nem exportálható a bérlői rendszergazdák. Ezek a naplók a műveletek előzményrekordjait a szolgáltatás és az egyes eszközökkel kapcsolatos diagnosztikai adatok képezik.

## <a name="links-to-additional-documentation"></a>További dokumentációkra mutató hivatkozások

A Device Provisioning Service API-k teljes dokumentációt a következő helyen található [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Az Azure IoT Hub [vásárlói adatok kérése funkciók](../iot-hub/iot-hub-customer-data-requests.md).