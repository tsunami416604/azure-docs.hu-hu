---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c796ccf9466f83714b429f6551618529e5fef47c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165637"
---
<!--author=alkohli last changed: 03/17/16-->

## <a name="troubleshooting-update-failures"></a>A frissítéssel kapcsolatos hibák elhárítása
**Mi a teendő, ha megjelenik egy értesítés, hogy a frissítés előtti ellenőrzések sikertelenek voltak?**

Ha egy előzetes ellenőrzés sikertelen, tekintse meg az oldal alján lévő részletes értesítési sávot. Ez útmutatást nyújt arról, hogy miért volt sikertelen az előzetes ellenőrzés. A következő ábra egy olyan példányt mutat be, amelyen ilyen értesítés jelenik meg. Ebben az esetben a vezérlő állapot-ellenőrzése és a hardverösszetevő állapot-ellenőrzése hiúsult meg. A **Hardverállapot** szakaszban láthatja, hogy a **Vezérlő 0** és a **Vezérlő 1** összetevő is figyelmet igényel.

  ![Előzetes ellenőrzési hiba](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

Ellenőrizze, hogy mindkét vezérlő kifogástalan állapotú és online-e. Azt is ellenőrizze, hogy a StorSimple-eszköz összes hardverösszetevőjének kifogástalan-e az állapota a Karbantartás oldalon. Ezután próbálja meg telepíteni a frissítéseket. Ha nem tudja kijavítani a hardverösszetevő problémáit, a további lépésekért forduljon a Microsoft ügyfélszolgálatához.

**Mi a teendő, ha „A frissítéseket nem lehet telepíteni” hibaüzenetet kap, és a rendszer azt javasolja, hogy tekintse meg a frissítési hibaelhárítási útmutatót a hiba okának meghatározásához?**

Ennek egy valószínű oka lehet, hogy nem csatlakozik a Microsoft Update-kiszolgálókhoz. Ezt manuálisan kell ellenőrizni. Ha megszakad a kapcsolat a frissítési kiszolgálóval, a frissítési feladat meghiúsul. A kapcsolat ellenőrzéséhez futtassa a következő parancsmagot a StorSimple-eszköz Windows PowerShell-felületén:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Futtassa a parancsmagot mindkét vezérlőn.

Ha ellenőrizte, hogy a kapcsolat létezik, és továbbra is ezt a hibát látja, a további lépésekért forduljon a Microsoft ügyfélszolgálatához.

**Mi a teendő, ha frissítési hiba történik, amikor az eszközt a 4-es frissítésre frissíti, és mindkét vezérlő a 4-es frissítést futtatja?**

A 4-es frissítéstől kezdődően, ha mindkét vezérlő ugyanazt a szoftververziót futtatja és frissítési hiba fordul elő, a vezérlők nem lépnek helyreállítási módba. Ez a helyzet akkor fordulhat elő, ha az eszközszoftver gyorsjavítását (elsőrendű frissítés) mindkét vezérlőre sikeresen alkalmazza, de még további gyorsjavításokat (másod- és harmadrendű frissítéseket) kell alkalmazni. A 4-es frissítéstől kezdve a vezérlők csak akkor lépnek helyreállítási módba, ha a két vezérlőn eltérő szoftververzió fut. 

Ha frissítési hiba jelenik meg, amikor mindkét vezérlő a 4-es frissítést futtatja, javasoljuk, hogy várjon néhány percet, majd próbálkozzon újra a frissítéssel. Ha az ismételt próbálkozás nem sikerül, forduljon a Microsoft ügyfélszolgálatához.
