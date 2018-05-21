---
title: Felhasználói adatok kérése Azure IoT központi szolgáltatásai
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: iot-central
ms.openlocfilehash: 0e348ca9ca85da7d4734a57afac4e5bb27217eae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="summary-of-customer-data-request-features"></a>Felhasználói adatok kérelem szolgáltatásainak összefoglalása

Az Azure IoT központi egy olyan teljes körűen felügyelt az eszközök internetes hálózatát (IoT) szoftver,--szolgáltatás megoldás, amellyel könnyedén csatlakozás figyelése, és léptékű az IoT-eszközök kezelése, hozzon létre mélyebben elemezheti az IoT-adatokból, és tájékoztatni művelet végrehajtása.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Felhasználói adatok azonosítása

Azonosítsa azokat a felhasználókat, és szerepkörök hozzárendelése az Azure Active Directory-objektumazonosítók használt. Az Azure IoT központi portál megjeleníti felhasználó e-mail címéhez tartozó szerepkör-hozzárendelések, de csak az Azure Active Directory-Objektumazonosító tárolja, az e-mail cím dinamikusan le kell kérdezni az Azure Active Directoryból. Az Azure IoT központi rendszergazdák megtekintheti, exportálása, és törli az Azure IoT központi alkalmazások felhasználói Adminisztráció szakaszában, az alkalmazás felhasználóinak.

Az alkalmazásból e-mail címek beállítható úgy, hogy a riasztásokat fogadó. Ebben az esetben e-mail címek IoT központi belül tárolják, és az alkalmazáson belüli fiók felügyeleti oldalról kell kezelni.

Eszközök, vonatkozó Microsoft tart fenn semmilyen adatot, és nem fér hozzá, amely lehetővé teszi az eszköz felhasználói korrelációs adatok. Az Azure IoT központban kezelt eszközök közül sok nem személyes eszközök, például egy Eladóautomata vagy a kávét készítő. Az ügyfelek is, azonban vegye figyelembe a személyes azonosításra alkalmas lehet néhány eszközt, és saját belátása szerint a saját eszköz vagy a készlet egyéni eszközök összekötését rendszerek követési fenntarthatja. Az Azure IoT központi kezeli, és tárolja a személyes adatok, mintha eszközök kapcsolódó összes adat.

A Microsoft vállalati szolgáltatást használ, a Microsoft néhány információt, a rendszer naplókat néven hoz létre. Ezek a naplók a szolgáltatás és az egyes eszközökkel kapcsolatos diagnosztikai adatok végzett tényleges műveletek alkotják, és nem kapcsolódik a felhasználói tevékenység. Az Azure IoT központi rendszer által létrehozott naplók nem elérhetők, és alkalmazás-rendszergazdák által exportálható.

## <a name="deleting-customer-data"></a>Felhasználói adatok törlése

A felhasználói adatok törlése csak biztosítja az IoT központi felügyelet lapján. Alkalmazás-rendszergazdák kiválaszthatja a felhasználó törlődik, és kattintson a **törlése** a rekord törlése az alkalmazás jobb felső sarokban. Alkalmazás-rendszergazdák is eltávolíthatja, amelyek már nem az adott alkalmazáshoz kapcsolódó egyedi fiókokat.

A felhasználó törlését követően további riasztások sem rendszer e-mailben a őket. Azonban az e-mail cím egyedileg távolítsa el minden egyes konfigurált riasztás alapján.

További információkért lásd: [szabályok és a műveletek az eszköz konfigurálása](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Felhasználói adatok exportálása

Az adatok exportálása csak biztosítja az IoT központi felügyelet lapján. Ügyféladatok és hozzárendelt szerepkörök választható ki, másolja, és egy alkalmazás-rendszergazda által beillesztett.

## <a name="links-to-additional-documentation"></a>További dokumentációkra mutató hivatkozások

Fiók felügyeleti, beleértve a szerepkör-definíciók, további információ: [az alkalmazás felügyelete](howto-administer.md).
