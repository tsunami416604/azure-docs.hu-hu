---
title: Vásárlói adatok kérése funkciók az Azure IoT Central |} A Microsoft Docs
description: Vásárlói adatok kérése az Azure IoT Central funkciók
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 2952008ca788a620f2b558d5997aeebd59196b7a
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314568"
---
# <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása

Az Azure IoT Central egy teljes körűen felügyelt eszközök internetes hálózata (IoT) szoftver--szolgáltatásként megoldás, amely megkönnyíti a csatlakoztatása, figyelése, és a nagy mennyiségű IoT-eszközök kezeléséhez, részletesebben elemezheti az IoT-adatok létrehozása és megalapozott.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Vásárlói adatok azonosítása

Azonosítsa azokat a felhasználókat és a szerepkörök hozzárendelése az Azure Active Directory-objektumazonosítók használhatók. Az Azure IoT Central portálon megjelenik felhasználói e-mail-címeket a szerepkör-hozzárendeléseket, de csak az Azure Active Directory-Objektumazonosító van tárolva, az e-mail-cím dinamikusan kérdezhető le az Azure Active Directoryból. Az Azure IoT Central rendszergazdák megtekintése, exportálása és alkalmazás felhasználók törlése az Azure IoT Central alkalmazáshoz felhasználói Adminisztráció szakaszában.

Az alkalmazásból e-mail-címek konfigurálhatók érkező riasztások fogadásához. E-mail-címeket ebben az esetben belül az IoT-központ tárolódnak, és az alkalmazáson belüli fiók felügyeleti oldalról kell felügyelni.

Eszközökre vonatkozó Microsoft tart fenn semmilyen adatot, és nem fér hozzá, amely lehetővé teszi az eszközt, hogy a felhasználó megfelelési adatok. Számos az Azure IoT Central a felügyelt eszközök, amelyek személyes eszközök, például egy Eladóautomata vagy a kávét készítő nem. Az ügyfelek is, azonban figyelembe kell venni néhány eszközt személyes azonosításra alkalmas lehet, és saját belátása szerint a saját eszköz vagy a leltár nyomon követése a rendszerek, amelyek elősegítsék eszközök egyéni felhasználók számára fenntarthatják. Az Azure IoT Central kezeli, és, mintha a személyes adatok eszközökhöz társított minden adatot tárol.

A Microsoft enterprise szolgáltatást használ, a Microsoft néhány információt, a rendszer által létrehozott naplók néven hoz létre. Ezek a naplók műveletek előzményrekordjait a szolgáltatás és az egyes eszközökkel kapcsolatos diagnosztikai adatok képezik, és nem kapcsolódó felhasználói tevékenység. Az Azure IoT Central rendszer által létrehozott naplók nincsenek elérhető vagy nem exportálható, alkalmazás-rendszergazdák által.

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése

Felhasználói adatok törlésének lehetőségére is csak nyugtázását az IoT-központ adminisztrációs oldal. Alkalmazás-rendszergazdák is válassza ki a törölni, és válassza ki a felhasználó **törlése** rekord törléséhez az alkalmazás jobb felső sarokban. Alkalmazás-rendszergazdák is eltávolíthatja az egyes fiókok, amelyek már nem a szóban forgó alkalmazás társítva.

A felhasználó törlését követően további riasztások sem rendszer e-mailben a őket. Azonban e-mail-címükkel kell külön-külön távolítható minden beállított értesítés.

További információkért lásd: [konfigurálása az eszközre vonatkozó szabályokat és műveleteket](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása

Az adatok exportálásának lehetőségét csak nyugtázását az IoT-központ adminisztrációs oldal. Adatai, beleértve a hozzárendelt szerepkörök kiválasztott, másolt és alkalmazás-rendszergazda által beillesztett.

## <a name="links-to-additional-documentation"></a>További dokumentációkra mutató hivatkozások

További információ a fiókadminisztrátori, beleértve a szerepkör-definíciók: [az alkalmazás felügyelete](howto-administer.md).
