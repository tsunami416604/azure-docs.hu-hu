---
title: Ügyféladat-igénylési funkciók az Azure IoT Centralban | Microsoft dokumentumok
description: Ez a cikk az ügyféladatok azonosítását, törlését és exportálását ismerteti az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023752"
---
# <a name="summary-of-customer-data-request-features"></a>Az ügyféladat-igénylési funkciók összefoglalása

Az Azure IoT Central egy teljes körűen felügyelt eszközök internetes hálózata (IoT) szoftverszolgáltatásként megoldás, amely megkönnyíti az IoT-eszközök nagy méretekben történő csatlakoztatását, figyelését és kezelését, az IoT-adatok ból származó mély elemzéseket, és megalapozott lépéseket tesz.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Az ügyféladatok azonosítása

Az Azure Active Directory objektumazonosítók a felhasználók azonosítására és szerepkörök hozzárendelésére szolgálnak. Az Azure IoT Central portál megjeleníti a szerepkör-hozzárendelések felhasználói e-mail-címeket, de csak az Azure Active Directory Object-ID tárolja, az e-mail cím dinamikusan lekérdezett az Azure Active Directoryból. Az Azure IoT Központi rendszergazdák megtekinthetik, exportálhatják és törölhetik az alkalmazás-felhasználókat az Azure IoT Central alkalmazás felhasználói felügyeleti szakaszában.

Az alkalmazáson belül az e-mail címek beállíthatók riasztások fogadására. Ebben az esetben az e-mail-címek az IoT Central ban tárolódnak, és az alkalmazáson belüli fiókfelügyeleti lapról kell kezelni őket.

Az eszközök tekintetében a Microsoft nem tart fenn adatokat, és nem fér hozzá olyan adatokhoz, amelyek lehetővé teszik az eszköz és a felhasználói korreláció elérését. Az Azure IoT Centralban kezelt eszközök közül sok nem személyes eszköz, például egy automatából vagy kávéfőzőből. Az ügyfelek azonban úgy tekinthetik, hogy egyes eszközök személyazonosításra alkalmasak, és saját belátásuk szerint fenntarthatják saját eszköz- vagy készletkövető rendszereiket, amelyek az eszközöket egyénekhez kötik. Az Azure IoT Central úgy kezeli és tárolja az eszközökhöz társított összes adatot, mintha azok személyes adatok lennének.

A Microsoft vállalati szolgáltatásainak használatakor a Microsoft létrehoz néhány információt, más néven a rendszer által létrehozott naplókat. Ezek a naplók a szolgáltatáson belül végzett tényszerű műveleteket és az egyes eszközökkel kapcsolatos diagnosztikai adatokat jelentik, és nem kapcsolódnak a felhasználói tevékenységhez. Az Azure IoT Central rendszer által létrehozott naplók nem érhetők el, vagy exportálható az alkalmazás-rendszergazdák.

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A felhasználói adatok törlésének lehetősége csak az IoT Central adminisztrációs oldalán érhető el. Az alkalmazásgazdák kiválaszthatják a törölni kívánt felhasználót, és a bejegyzés törléséhez az alkalmazás jobb felső sarkában a **Törlés** lehetőséget választhatják. Az alkalmazás-rendszergazdák eltávolíthatják azokat a fiókokat is, amelyek már nincsenek társítva a kérdéses alkalmazáshoz.

A felhasználó törlése után a rendszer nem küld nekik további értesítéseket. Az e-mail címüket azonban egyenként el kell távolítani minden egyes konfigurált riasztásból.

## <a name="exporting-customer-data"></a>Vevői adatok exportálása

Az adatok exportálásának lehetősége csak az IoT Central adminisztrációs oldalán érhető el. Az ügyféladatokat, beleértve a hozzárendelt szerepköröket is, az alkalmazás rendszergazdája kijelölheti, másolhatja és beillesztheti.

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

A fiókfelügyeletről, beleértve a szerepkör-definíciókat is, az [alkalmazás felügyelete](howto-administer.md)című témakörben talál további információt.
