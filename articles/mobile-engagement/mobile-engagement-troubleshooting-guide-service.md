---
title: "Az Azure Mobile Engagement hibaelhárítási útmutatója - szolgáltatás"
description: "Hibaelhárítás az Azure Mobile Engagement útmutatók"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f13fd0540b783120014b3a8d4e41f78808c7fade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-service-issues"></a>A szolgáltatásokkal kapcsolatos problémákról hibaelhárítási útmutatója
A következőkben lehetséges problémák merülhetnek fel az Azure Mobile Engagement működésével.

## <a name="service-outages"></a>Szolgáltatás-kimaradások számát
### <a name="issue"></a>Probléma
* Az problémákat, amelyek jelenik meg, hogy az Azure Mobile Engagement szolgáltatás-kimaradások számát.

### <a name="causes"></a>Okok
* Problémák jelennek meg, hogy az Azure Mobile Engagement szolgáltatáskimaradások számos különböző probléma okozhatja:
  * Elkülönített problémák, amely eredetileg megjelenik a rendszerből, az összes Azure Mobile Engagement
  * Ismert problémák okozta a kiszolgáló valamilyen okból kimaradás (mindig nem jeleníti meg a kiszolgáló állapota):
  * Ütemezés késések célcsoportkezelést, jelvény a frissítéssel kapcsolatos problémák, hibák statisztika stop gyűjtése, leküldéses nem működik, API leáll, új alkalmazásokat, vagy a felhasználók nem hozható létre, DNS-hibák, és időtúllépést a felhasználói felület, az API-t vagy az alkalmazások az eszközön.
  * Függőség kimaradások cloud [Azure szolgáltatás állapota](http://status.azure.com/)
  * Leküldéses értesítési szolgáltatások (PNS) függőségi leállások esetén
  * Alkalmazás-áruház leállások esetén

1) Ellenőrizze, hogy van-e a problémát a rendszerből való tesztelheti a ugyanannak a függvénynek egy másik

* Az Azure Mobile Engagement integrált alkalmazás
* Vizsgálati eszköz
* Vizsgálati eszköz operációs rendszer verziója
* Kampány
* Rendszergazdai felhasználói fiókkal
* Böngésző (IE, Firefox Chrome, stb.)
* Computer

2) Ha a probléma csak hatással van a felhasználói felületén vagy a API tesztelése:

* Tesztelje az Azure Mobile Engagement felhasználói felületén, mind az Azure Mobile Engagement API ugyanannak a függvénynek.

3) Ha a probléma van a mobiltelefon hálózati teszteléséhez:

* Tesztelje a Wi-Fi keresztül, és a 3G mobiltelefon hálózaton keresztül csatlakozik az internethez csatlakozik.
* Győződjön meg arról, hogy a tűzfal nem blokkolja, az Azure Mobile Engagement IP-címek vagy portok.

4) Ha a probléma az eszközzel teszteléséhez:

* Ha az eszköz csatlakozni tudjanak Azure Mobile Engagement egy másik integrált Azure Mobile Engagement-alkalmazás tesztelése.
* Tesztelje, hogy eseményeket, a feladatok és az összeomlások hozhat létre, amely az Azure Mobile Engagement felhasználói felülete látható a telefonjáról. 
* Ha a leküldéses értesítéseket küldhet az Azure Mobile Engagement felhasználói felülete az eszközre, az eszköz azonosító alapján tesztelése 

5) Ha a probléma van az alkalmazás tesztelése:

* Telepítse, és tesztelje az alkalmazás az emulátor helyett egy fizikai eszközről:

6) Ha a probléma van az operációs rendszer frissítéseit is megoldásához SDK frissítést igénylő eszközök felhasználó tesztelése:

* Az operációs rendszer különböző verziói különböző eszközökön az alkalmazás teszteléséhez.
* Győződjön meg arról, hogy az SDK legújabb verzióját használja.

## <a name="connectivity-and-incorrect-information-issues"></a>Kapcsolat és a megfelelő információkat problémák
### <a name="issue"></a>Probléma
* A probléma jelentkezik be az Azure Mobile Engagement felhasználói felületén.
* Az Azure Mobile Engagement API kapcsolati hibák.
* Az eszköz API-n keresztül App Info címkék feltöltésével kapcsolatos problémák.
* A problémák naplók és exportált adatok letöltése az Azure Mobile engagement szolgáltatásból.
* Helytelen adatokat az Azure Mobile Engagement felhasználói felülete látható.
* Azure Mobile Engagement naplók látható tájékoztatás helytelen.

### <a name="causes"></a>Okok
* Győződjön meg arról, a felhasználói fiók rendelkezik megfelelő engedélyekkel a feladat végrehajtásához.
* Győződjön meg arról, hogy a probléma nem elkülönített egy számítógép vagy a helyi hálózaton.
* Győződjön meg arról, hogy az Azure Mobile Engagement-szolgáltatás nincs jelentett kimaradások esetén.
* Győződjön meg arról, hogy az App-Info címke fájlok hajtsa végre az összes szabályt:
  * Használja az UTF8 karakterkészletet (az ANSI karakterkészlet nem támogatott).
  * Használjon vesszőt ",", az elválasztó karaktert (megnyithat egy szolgáltatást a CSV elválasztó karaktert pontosvesszővel módosítására irányuló kérelem a kérelem "," például pontosvesszővel kell elválasztani őket egy másik karakterének ";").
  * Minden kisbetű használható logikai értékek a "true" és "false".
  * A maximális méret 35MB-nál kisebb fájlt használja.

