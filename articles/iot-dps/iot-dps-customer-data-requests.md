---
title: Ügyféladat-kérési funkciók az Azure DPS-eszközökhöz
description: Az Azure Device Provisioning Service (DPS) személyes en kezelt eszközök esetében ez a cikk bemutatja a rendszergazdáknak, hogyan exportálhatják vagy törölhetik a személyes adatokat.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890653"
---
# <a name="summary-of-customer-data-request-features"></a>Az ügyféladat-igénylési funkciók összefoglalása

Az Azure IoT Hub-eszközkiépítési szolgáltatás egy REST API-alapú felhőszolgáltatás, amely a vállalati ügyfeleket célozza meg, amely lehetővé teszi az eszközök zökkenőmentes, automatikus, érintés nélküli kiépítését az Azure IoT Hubba az eszközön kezdődő és a felhővel végződő biztonsággal.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Az egyes eszközök höz egy bérlői rendszergazda rendel regisztrációs azonosítót és eszközazonosítót. Ezek az eszközökről származó és az ezekről az adatok ezekről az azonosítókról származnak. A Microsoft nem tart fenn adatokat, és nem fér hozzá olyan adatokhoz, amelyek lehetővé tennék ezen eszközök és az egyén közötti korrelációt.

Az Eszközkiépítési szolgáltatásban kezelt eszközök közül sok nem személyes eszköz, például irodai termosztát vagy gyári robot. Az ügyfelek azonban úgy tekinthetik, hogy egyes eszközök személyazonosításra alkalmasak, és saját belátásuk szerint fenntarthatják saját eszköz- vagy készletkövetési módszereiket, amelyek az eszközöket egyénekhez kötik. Az Eszközkiépítési szolgáltatás úgy kezeli és tárolja az eszközökhöz társított összes adatot, mintha azok személyes adatok lennének.

A bérlői rendszergazdák az Azure Portalon vagy a szolgáltatás REST API-iban is teljesíthetik az információkéréseket az eszközazonosítóhoz vagy regisztrációs azonosítóhoz társított adatok exportálásával vagy törlésével.

> [!NOTE]
> Az Azure IoT Hubban az eszközkiépítési szolgáltatáson keresztül kiépített eszközök további adatokat tárolnak az Azure IoT Hub szolgáltatásban. Tekintse meg az [Azure IoT Hub referenciadokumentációját](../iot-hub/iot-hub-customer-data-requests.md) egy adott eszköz teljes igényének teljesítéséhez.

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

Az Eszközkiépítési szolgáltatás tárolja a regisztrációkat és a regisztrációs rekordokat. A regisztrációk olyan eszközökre vonatkozó információkat tartalmaznak, amelyek kiépíthetők, és a regisztrációs rekordok azt mutatják, hogy mely eszközök mentek már keresztül a kiépítési folyamaton.

A bérlői rendszergazdák eltávolíthatják a regisztrációkat az Azure Portalról, és ez eltávolítja a kapcsolódó regisztrációs rekordokat is.

További információt az [Eszközregisztrációk kezelése című témakörben talál.](how-to-manage-enrollments.md)

A regisztrációk és a regisztrációs rekordok törlésére is lehetőség van a REST API-k használatával:

* Egyetlen eszköz regisztrációs adatainak törléséhez [használhatja az Eszközregisztráció – Törlés parancsot.](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment)
* Az eszközök egy csoportjának regisztrációs adatainak törléséhez [használhatja az Eszközregisztrációs csoport - Törlés parancsot.](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup)
* A kiépített eszközökkel kapcsolatos információk törléséhez [használhatja a Regisztrációs állapot - Regisztrációs állapot törlése lehetőséget.](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate)

## <a name="exporting-customer-data"></a>Vevői adatok exportálása

Az Eszközkiépítési szolgáltatás tárolja a regisztrációkat és a regisztrációs rekordokat. A regisztrációk olyan eszközökre vonatkozó információkat tartalmaznak, amelyek kiépíthetők, és a regisztrációs rekordok azt mutatják, hogy mely eszközök mentek már keresztül a kiépítési folyamaton.

A bérlői rendszergazdák megtekinthetik a regisztrációkat és a regisztrációs rekordokat az Azure Portalon keresztül, és exportálhatják őket másolással és beillesztéssel.

A regisztrációk kezeléséről az [eszközregisztrációk kezelése](how-to-manage-enrollments.md)című témakörben talál további információt.

A regisztrációk és a regisztrációs rekordok exportálási műveletei rest API-k használatával is végrehajthatók:

* Egyetlen eszköz regisztrációs adatainak exportálásához [használhatja az Eszközregisztráció – Beget parancsot.](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment)
* Az eszközök egy csoportjának regisztrációs adatainak exportálásához [használhatja az Eszközregisztrációs csoport - Get parancsot.](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup)
* A már kiépített eszközökkel kapcsolatos információk exportálásához [használhatja a Regisztrációs állapot - Regisztrációs állapot beszerezhető .](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate)

> [!NOTE]
> A Microsoft vállalati szolgáltatásainak használatakor a Microsoft létrehoz néhány információt, más néven a rendszer által létrehozott naplókat. Egyes eszközkiépítési szolgáltatás rendszer által létrehozott naplók nem érhetők el, vagy exportálható a bérlői rendszergazdák. Ezek a naplók a szolgáltatáson belül végzett tényszerű műveleteket és az egyes eszközökkel kapcsolatos diagnosztikai adatokat jelentik.

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

Az eszközkiépítési szolgáltatás API-inak [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps)teljes dokumentációja a helyen található.

Az Azure IoT Hub [ügyféladat-igénylési funkciói.](../iot-hub/iot-hub-customer-data-requests.md)