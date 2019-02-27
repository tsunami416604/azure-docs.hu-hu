---
title: Az Azure AD Connect Health használata az AD FS kockázatos IP jelentés |} A Microsoft Docs
description: Az Azure AD Connect Health AD FS kockázatos IP jelentés ismerteti.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d1af8d7e10bd62819909c87c8e54fcbce6b7fe6
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890429"
---
# <a name="risky-ip-report-public-preview"></a>Kockázatos IP jelentés (nyilvános előzetes verzió)
Az AD FS-ügyfelek az interneten elérhetővé tehetnek jelszóalapú hitelesési végpontokat, és ezzel hitelesítési szolgáltatásokat biztosíthatnak a végfelhasználók számára olyan SaaS-alkalmazások eléréséhez, mint az Office 365. Ez lehetőséget ad kártékony elemek számára, hogy megkíséreljenek bejelentkezni az AD FS rendszerbe, és találgatással kiderítsék a végfelhasználó jelszavát az alkalmazás-erőforrások elérése érdekében. A Windows Server 2012 R2-n futó AD FS-től kezdve elérhető zárolási funkció az extranet-fiókokhoz az ilyen típusú támadások elkerülése érdekében. Ha korábbi verziót használ, erősen ajánlott AD FS rendszerét Windows Server 2016-ra frissíteni. <br />

Ezenkívül előfordulhat, hogy egy IP-címről többször próbálnak bejelentkezni több felhasználó fiókjába. Ilyen esetekben előfordulhat, hogy az egyes felhasználókat érintő kísérletek száma az AD FS fiókzárolási funkcióját aktiváló küszöbérték alatt marad. Az Azure AD Connect Health mostantól elérhető „Kockázatos IP jelentése” észleli az ilyen eseményeket, és értesíti azokról a rendszergazdákat. A jelentés előnyei a következők: 
- Észleli az IP-címeket, amelyek túllépik a sikertelen jelszóalapú belépési kísérletek küszöbértékét
- Támogatja a helytelen jelszó megadása vagy az extranet zárolási állapot miatti sikertelen bejelentkezéseket
- E-mailben értesíti a rendszergazdákat, ha ilyen eseményre kerül sor (az e-mail-beállítások testre szabhatók)
- A küszöbérték-beállítások testreszabhatóak, és összehangolhatók a vállalat biztonsági szabályzatával
- A jelentések letölthetők offline elemzéshez, és automatikusan integrálhatók más rendszerekbe

> [!NOTE]
> A jelentés használatához győződjön meg róla, hogy az AD FS-naplózás engedélyezve van. További információkért tekintse meg az [AD FS-naplózás engedélyezését](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Az előzetes verzió eléréséhez globális rendszergazda vagy [biztonsági olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) szerepkörű felhasználói engedély szükséges.  
> 

## <a name="what-is-in-the-report"></a>Mi az a jelentés?
A Kockázatos IP jelentés minden eleme összesített adatokat tartalmaz a sikertelen AD FS-bejelentkezési kísérletekről, amelyek túllépték a kijelölt küszöbértéket. Ez a következő adatokat tartalmazza: ![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report4a.png)

| Jelentéselem | Leírás |
| ------- | ----------- |
| Időbélyegző | Az észlelési időszak kezdetét jelző, az Azure Portal helyi ideje szerinti időbélyegző.<br /> Az összes napi eseményt UTC szerint éjfélkor állítja elő a rendszer. <br />Az óránkénti eseményeknél az időbélyeg az óra kezdetére van kerekítve. Az exportált fájlban a „firstAuditTimestamp” érték mutatja az első tevékenység kezdetét. |
| Eseményindító típusa | Az észlelési időszak típusa. Az összesítési eseményindítók óránkénti és a napi típusúak lehetnek. Ez hasznos lehet a nagy gyakoriságú találgatásos támadások észlelésére a lassú támadásokkal szemben, ahol a kísérletek eloszlanak egy adott napon belül. |
| IP-cím | Olyan kockázatos IP-cím, amelyről helytelen jelszót adtak meg, vagy amelyen extranet zárolást eredményező bejelentkezési tevékenység volt tapasztalható. Ez lehet IPv4- vagy IPv6-cím is. |
| Helytelen jelszó típusú hibák száma | Az IP-címről előforduló helytelen jelszó típusú hibák száma az észlelési időszakban. A helytelen jelszó típusú hiba többször is előfordulhat az egyes felhasználók esetében. Fontos: a lejárt jelszó miatti sikertelen bejelentkezések nem tartoznak ide. |
| Extranet zárolási hibák száma | Az IP-címről előforduló extranet zárolási hibák száma az észlelési időszakban. Extranet zárolási hibák többször is előfordulhatnak az egyes felhasználók esetében. Ez csak akkor jelenik meg, ha az AD FS-ben konfigurálta az extranet zárolást (a 2012 R2-es és újabb verziókban). <b>Megjegyzés:</b> Ha a jelszóalapú extranet bejelentkezés engedélyezve van, erősen ajánlott bekapcsolni ezt a funkciót. |
| Megkísérelt egyedi felhasználók | Azon egyedi felhasználói fiókok száma, amelyekbe megkíséreltek bejelentkezni az IP-címről az észlelési időszakban. Ez segíthet megkülönböztetni az egy és a több felhasználó elleni támadásokat.  |

Például az alábbi jelentésben 2018. 02. 28-án 18:00 és 19:00 között a <i>104.2XX.2XX.9</i> IP-címről helytelen jelszó típusú hiba nem volt, azonban 284 extranet zárolási hiba fordult elő. 14 egyedi felhasználó volt érintett. A tevékenység meghaladta az óránkénti jelentési küszöbértéket. 

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - A jelentés listájában kizárólag a küszöbértéket meghaladó tevékenységek jelennek meg. 
> - A jelentés legfeljebb az előző 30 napra vonatkozhat.
> - Ebben a figyelmeztető jelentésben nem jelennek meg az Exchange-IP-címek és a magánhálózati IP-címek, az exportálási listában azonban szerepelnek. 
>

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>A terheléselosztó IP-címek listájában
A terheléselosztó összesítési bejelentkezési tevékenységei meghiúsultak, és elérte a riasztási küszöbértéket. Ha terheléselosztói IP-címek jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának. Konfigurálja megfelelően a terheléselosztót a továbbított ügyfél IP-címének továbbítására. 

## <a name="download-risky-ip-report"></a>Kockázatos IP jelentés letöltése 
A **letöltési** funkcióval az összes kockázatos IP-cím listája exportálható a Connect Health portálról az elmúlt 30 napra vonatkozóan. Az exportált listában szerepel az egyes észlelési időszakokban előforduló összes sikertelen AD FS-bejelentkezési tevékenység, így az exportálás után testreszabhatja a szűrési feltételeket. A portálon kiemelt összesítések mellett az exportált lista IP-címekre lebontva további részleteket is tartalmaz a sikertelen bejelentkezési tevékenységekről:

|  Jelentéselem  |  Leírás  | 
| ------- | ----------- | 
| firstAuditTimestamp | A sikertelen tevékenységek kezdetének első időbélyegzője az észlelési időszakon belül.  | 
| lastAuditTimestamp | A sikertelen tevékenységek végének utolsó időbélyegzője az észlelési időszakon belül.  | 
| attemptCountThresholdIsExceeded | Annak a jelzője, ha az aktuális tevékenységek túllépték a figyelmeztetési küszöbértéket.  | 
| isWhitelistedIpAddress | Annak a jelzője, ha az IP-cím ki lett szűrve a figyelmeztetésekből és a jelentésekből. A magánhálózati IP-címek (<i>10.x.x.x, 172.x.x.x és 192.168.x.x</i>) és az Exchange-IP-címek szűrve vannak, és True értékűként vannak megjelölve. Ha magánhálózati IP-tartományok jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának.  | 

## <a name="configure-notification-settings"></a>Értesítési beállítások konfigurálása
A jelentés rendszergazdai elérhetőségi adatait az **értesítési beállításokban** lehet frissíteni. Alapértelmezés szerint a kockázatos IP-címekkel kapcsolatos e-mail-értesítés küldése ki van kapcsolva. Az értesítés bekapcsolásához át kell állítani az „E-mail-értesítés küldése, ha egy IP-cím túllépi a sikertelen tevékenységek figyelmeztetési küszöbértékét” beállítás kapcsológombját. A Connect Health általános értesítéseihez hasonlóan itt állíthatja be, hogy kik kapják meg az értesítési jelentést a kockázatos IP-címekről. A módosításról értesíthet minden globális rendszergazdát. 

## <a name="configure-threshold-settings"></a>Küszöbérték-beállítások konfigurálása
A figyelmeztetési küszöbérték küszöbérték-beállításokban módosítható. A rendszerben be van állítva egy alapértelmezett küszöbérték. A kockázatos IP-címek esetében négy küszöbérték-beállítási kategória közül választhat:

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report4d.png)

| Küszöbértékelem | Leírás |
| --- | --- |
| (Helytelen felhasználónév/jelszó + extranet zárolás) naponta  | A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha a helytelen jelszavak és az extranet zárolások **napi** száma meghaladja az értéket. |
| (Helytelen felhasználónév/jelszó + extranet zárolás) óránként | A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha a helytelen jelszavak és az extranet zárolások **óránkénti** száma meghaladja az értéket. |
| Extranet zárolások naponta | A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha az extranet zárolások **napi** száma meghaladja az értéket. |
| Extranet zárolások óránként| A küszöbérték-beállítás megadásával jelentés készül a tevékenységről, és figyelmeztető értesítés jön létre, ha az extranet zárolások **óránkénti** száma meghaladja az értéket. |

> [!NOTE]
> - A jelentési küszöbérték változása egy órával a beállítás módosítása után lép érvénybe. 
> - A korábban jelentett elemeket nem érinti a küszöbérték változása. 
> - Azt javasoljuk, hogy a környezetben előforduló események számának megfelelően módosítsa a küszöbértéket. 
>
>

## <a name="faq"></a>GYIK
**Miért jelennek meg magánhálózati IP-címtartományok a jelentésben?**  <br />
A magánhálózati IP-címek (<i>10.x.x.x, 172.x.x.x és 192.168.x.x</i>) és az Exchange-IP-címek szűrve vannak, és True értékűként vannak megjelölve az IP-címek engedélyezési listáján. Ha magánhálózati IP-tartományok jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának.

**Miért jelennek meg terheléselosztói IP-címek a jelentésben?**  <br />
Ha terheléselosztói IP-címek jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának. Konfigurálja megfelelően a terheléselosztót a továbbított ügyfél IP-címének továbbítására. 

**Mit tegyek blokkolja az IP-cím?**  <br />
Az azonosított kártevő IP-címeket érdemes hozzáadni a tűzfalhoz vagy blokkolni az Exchange-ben.   <br />

**Miért nem jelenik meg egyetlen elem sem ez a jelentés a?** <br />
- A sikertelen bejelentkezési tevékenységek nem érték el a beállított küszöbértéket.
- Ellenőrizze, hogy nincsen-e aktív „Az állapotszolgáltatás nem naprakész” figyelmeztetés az AD FS-kiszolgálók listáján.  Itt további információkat olvashat [a figyelmeztetés hibaelhárításáról](how-to-connect-health-data-freshness.md).
- Az AD FS-farmokon nincs engedélyezve a naplózás.

**Miért jelenik meg nem érhető el a jelentést?**  <br />
Globális rendszergazda vagy [biztonsági olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) szerepkörű felhasználói engedély szükséges. Hozzáférésért forduljon a globális rendszergazdához.


## <a name="next-steps"></a>További lépések
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
