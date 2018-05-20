---
title: Felhasználói adatok kérelem szolgáltatások
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
ms.openlocfilehash: 9600d927ffefa8a211afcb3fe2bf7510aafc9fc0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="summary-of-customer-data-request-features"></a>Felhasználói adatok kérelem szolgáltatásainak összefoglalása

Az Azure IoT Hub eszköz kiépítése szolgáltatás REST API-alapú felhőszolgáltatás vállalati ügyfelek is, amely lehetővé teszi, hogy zökkenőmentes, automatikus nulla érintéssel létrehozását esetén az Azure IoT Hub eszköz, amely az eszközön és a felhő biztonsági.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Egyes eszközök a bérlői rendszergazda rendeli hozzá a regisztrációs és eszköz-azonosító. A és e-eszközökkel kapcsolatos adatok ezek azonosítók alapul. A Microsoft kezeli az adatokat, és nem fér hozzá, amely lehetővé tenné ezen eszközök legyen egy természetes személyhez korrelációs adatok.

Az Azure eszköz kiépítése szolgáltatáshoz a felügyelt eszközök közül sok nem személyes eszközök, például az office termosztát vagy a gyári robot. Az ügyfelek is, azonban vegye figyelembe a személyes azonosításra alkalmas lehet néhány eszközt, és saját belátása szerint a saját eszköz vagy a készlet nyomon követése a módszereket, amelyek egyéni eszközök összekötését fenntarthatja. Azure eszköz kiépítése szolgáltatáshoz kezeli, és tárolja a személyes adatok, mintha eszközök kapcsolódó összes adat.

A bérlői rendszergazdák használatával vagy az Azure-portálon, vagy a szolgáltatás REST API-k adatokat kérelmeinek teljesítéséhez exportálásával, illetve egy Eszközazonosító vagy a regisztrációs azonosítót. kapcsolatos adatok törlése

> [!NOTE]
> Az Azure IoT Hub keresztül Azure IoT Hub eszköz kiépítése szolgáltatáshoz kiépített eszközök tárolódnak az Azure IoT-központ szolgáltatás további adatokkal rendelkeznek. Tekintse meg a [Azure IoT Hub referenciadokumentációt](../iot-hub/iot-hub-customer-data-requests.md) ahhoz, hogy az adott eszköz teljes kérelem végrehajtása.

## <a name="deleting-customer-data"></a>Felhasználói adatok törlése

Az Azure IoT Hub eszköz kiépítése szolgáltatás történő regisztrációt és a regisztrációs bejegyzéseket tárolja. Regisztrációkat információkat tartalmaznak, amelyeknek engedélyezi úgy kell létrehozni, és regisztrációs rekordot mutatják, hogy mely eszközök már elvégezték az üzembe helyezési folyamat kell végrehajtaniuk.

A bérlői rendszergazdák távolíthatja el az Azure-portálról történő regisztrációt, és ezzel eltávolítja a kapcsolódó regisztrációt rekordokat is.

További információkért lásd: [kezelése az eszközök regisztrációját](how-to-manage-enrollments.md).

Lehetőség arra is történő regisztrációt és a regisztrációs REST API-k használatával törlési műveleteinek elvégzéséhez:

* Törli a regisztrációs adatokat egyetlen eszközhöz, használhatja a [az Eszközregisztráció - Törlés](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete).
* Regisztrációs adatai eszközök csoport törléséhez használja [beléptetési eszközcsoport - törlési](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete).
* Kiépített eszközökkel kapcsolatos információkat törléséhez használja [regisztrációs állapotát - törlés regisztrációs állapotát](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate).

## <a name="exporting-customer-data"></a>Felhasználói adatok exportálása

Az Azure IoT Hub eszköz kiépítése szolgáltatás történő regisztrációt és a regisztrációs bejegyzéseket tárolja. Regisztrációkat információkat tartalmaznak, amelyeknek engedélyezi úgy kell létrehozni, és regisztrációs rekordot mutatják, hogy mely eszközök már elvégezték az üzembe helyezési folyamat kell végrehajtaniuk.

A bérlői rendszergazdák történő regisztrációt és a regisztrációs bejegyzéseket az Azure portálon keresztül megtekintheti és exportálásához használja a másolási és beillesztési műveleteket.

Regisztráció kezeléséhez további információkért lásd: [kezelése az eszközök regisztrációját](how-to-manage-enrollments.md).

Lehetőség arra is történő regisztrációt és a REST API-k használatával rekordok exportálása műveleteinek elvégzéséhez:

* Regisztrálási információ egyetlen eszközhöz exportálásához használja [az Eszközregisztráció - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get).
* A csoport az eszközök regisztrálási információ exportálásához használja [beléptetési eszközcsoport - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get).
* Már kiépített eszközökkel kapcsolatos információkat exportálásához használja [regisztrációs állapotát - Get regisztrációs állapotát](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate).

> [!NOTE]
> A Microsoft vállalati szolgáltatást használ, a Microsoft néhány információt, a rendszer naplókat néven hoz létre. Bizonyos Azure IoT Hub eszköz kiépítése szolgáltatáshoz rendszer által létrehozott naplók nem elérhetők, és a bérlői rendszergazdák által exportálható. Ezek a naplók a szolgáltatás és az egyes eszközökkel kapcsolatos diagnosztikai adatok végzett tényleges műveletek alkotják.

## <a name="links-to-additional-documentation"></a>További dokumentációkra mutató hivatkozások

API-k itt található: Azure IoT Hub eszköz kiépítése szolgáltatáshoz dokumentáció teljes [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Az Azure IoT Hub [ügyféladatok kérelem szolgáltatások](../iot-hub/iot-hub-customer-data-requests.md).