---
title: Az Azure AD Connect Health használata az AD FS szolgáltatással | Microsoft Docs
description: Ez az Azure AD Connect Health oldal bemutatja, hogyan figyelheti helyszíni AD FS infrastruktúráját.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2018
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b17b4e8581daa5b19aaafd911765d843a9f3fe4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34055415"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Az AD FS monitorozása az Azure AD Connect Health használatával
Az alábbi dokumentáció az AD FS infrastruktúra Azure AD Connect Health használatával végzett figyelésére vonatkozik. Az Azure AD Connect (szinkronizálási szolgáltatás) az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md) című témakörben tekintheti meg. Az Active Directory tartományi szolgáltatások az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat pedig a [Using Azure AD Connect Health with AD DS](active-directory-aadconnect-health-adds.md) (Az Azure AD Connect Health használata az AD DS szolgáltatással) című témakörben találja.

## <a name="alerts-for-ad-fs"></a>AD FS-riasztások
Az aktív riasztások listáját az Azure AD Connect Health-riasztások szakaszban láthatja. Minden egyes riasztás tartalmazza a fontos információkat, a megoldás lépéseit, valamint a kapcsolódó dokumentációra mutató hivatkozásokat.

Egy aktív vagy egy megoldott riasztásra duplán kattintva egy új panel nyílik meg, amely további információkat, a riasztás megoldását segítő lépéseket és a fontosabb dokumentációkra mutató hivatkozásokat tartalmaz. A már megoldott riasztások esetében az előzményadatokat is megtekintheti.

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Használatelemzés az AD FS szolgáltatáshoz
Az Azure AD Connect Health Használatelemzés elemzi az összevonási kiszolgálók hitelesítési forgalmát. A használatelemzés mezőre duplán kattintva megnyílik az elemzés panel, amelyen számos metrika és csoportosítás jelenik meg.

> [!NOTE]
> A használatelemzés AD FS szolgáltatással való használatához győződjön meg róla, hogy az AD FS-naplózás engedélyezve van. További információkért tekintse meg az [AD FS-naplózás engedélyezését](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/report1.png)

További metrikák kiválasztásához adjon meg egy időtartományt, vagy a csoportosítás módosításához kattintson a jobb gombbal a használatelemzés diagramra, és válassza a Diagram szerkesztése lehetőséget. Ezt követően megadhatja az időtartományt, kiválaszthat egy másik metrikát, illetve módosíthatja a csoportosítást. A hitelesítési forgalom eloszlását különféle „metrikák” alapján tekintheti meg, és mindegyik metrikát csoportosíthatja a következő szakaszban ismertetett vonatkozó „csoportosítási szempont” paraméterek használatával:

**Metrika: Kérelmek száma összesen** – Az AD FS-kiszolgálók által feldolgozott kérelmek teljes száma.

|Csoportosítási szempont | Mit jelöl az adott csoportosítás, és miért hasznos? |
| --- | --- |
| Összes | Az összes AD FS-kiszolgáló által feldolgozott kérelmek teljes számát jeleníti meg.|
| Alkalmazás | Az összes kérelmet a célzott függő entitások alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, melyik alkalmazás hány százalékát fogadja a teljes forgalomnak. |
|  Kiszolgáló |Az összes kérelmet az azokat feldolgozó kiszolgálók alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni a teljes forgalom terheléseloszlását.
| Munkahelyi csatlakoztatás |Az összes kérelmet az alapján csoportosítja, hogy azok a munkahelyhez csatlakoztatott (ismert) eszközökről érkeznek-e. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy erőforrásaihoz hozzáférnek-e az identitás-infrastruktúra számára ismeretlen eszközökről származó kérelmek. |
|  Hitelesítési módszer | Az összes kérelmet a hitelesítés során alkalmazott hitelesítési módszer alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni a hitelesítéshez leggyakrabban használt hitelesítési módszereket. A lehetséges hitelesítési módszerek az alábbiak <ol> <li>Integrált Windows-hitelesítés (Windows)</li> <li>Űrlapalapú hitelesítés (Űrlapok)</li> <li>Egyszeri bejelentkezés (SSO)</li> <li>X509 tanúsítványalapú hitelesítés (Tanúsítvány)</li> <br>Ha az összevonási kiszolgálók a kérelmet SSO cookie-val kapják meg, a rendszer az adott kérelmet egyszeri bejelentkezésesnek (SSO) veszi. Ilyen esetekben, ha a cookie érvényes, a rendszer nem kér hitelesítő adatokat a felhasználótól, és zökkenőmentes hozzáférést biztosít az alkalmazáshoz. Ez a viselkedés gyakori, ha több, az összevonási kiszolgálók által védett jogcímfelhasználó alkalmazással rendelkezik. |
| Hálózati hely | Az összes kérelmet a felhasználó hálózati helye alapján csoportosítja. Ez lehet intranet vagy extranet. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy a forgalom hány százaléka érkezik az intranetről vagy az extranetről. |


**Metrika: Összes sikertelen kérelem** – Az összevonási szolgáltatás által feldolgozott sikertelen kérelmek teljes száma. (Ez a metrika csak az AD FS for Windows Server 2012 R2 szolgáltatás esetében elérhető)

|Csoportosítási szempont | Mit jelöl az adott csoportosítás, és miért hasznos? |
| --- | --- |
| Hibatípus | A hibák számát jeleníti meg előre meghatározott hibatípusok szerint. Ez a csoportosítás akkor hasznos, ha szeretné megismerni a gyakori hibatípusokat. <ul><li>Incorrect Username or Password (Helytelen felhasználónév vagy jelszó): A nem megfelelő felhasználónév vagy jelszó okozta hibák.</li> <li>„Extranet Lockout” (Extranet kizárás): A hiba oka, hogy a kérelem az extranetről kizárt felhasználótól érkezett </li><li> „Expired Password” (Lejárt jelszó): A hiba oka, hogy a felhasználó lejárt jelszóval jelentkezett be.</li><li>„Disabled Account” (Letiltott fiók): A hiba oka, hogy a felhasználó letiltott fiókkal jelentkezett be.</li><li>„Device Authentication” (Eszközhitelesítés): A hiba oka, hogy a felhasználó eszközhitelesítéssel való hitelesítése sikertelen volt.</li><li>„User Certificate Authentication” (Felhasználói tanúsítványos hitelesítés): A hiba oka, hogy a felhasználó hitelesítése érvénytelen tanúsítvány miatt sikertelen volt.</li><li>„MFA” (Többtényezős hitelesítés): A hiba oka, hogy a felhasználó többtényezős hitelesítéssel való hitelesítése sikertelen volt.</li><li>„Other Credential” (Egyéb hitelesítő adat): „Issuance Authorization” (Kiadásengedélyezés): Hitelesítési problémák okozta hibák.</li><li>„Issuance Delegation” (Kiadásdelegálás): Kiadásdelegálási problémák okozta hibák.</li><li>„Token Acceptance” (Jogkivonatok elfogadása): A hiba oka, hogy az AD FS elutasított egy külső identitásszolgáltatótól származó jogkivonatot.</li><li>„Protocol” (Protokoll): Protokollproblémák okozta hibák.</li><li>„Unknown” (Ismeretlen): Minden más. Bármely egyéb olyan hiba, amely nem illik a meghatározott kategóriákba.</li> |
| Kiszolgáló | A hibákat a kiszolgálók alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megismerni a hibák eloszlását a kiszolgálók között. Az egyenetlen eloszlás esetleg jelezheti azt, hogy valamelyik kiszolgáló meghibásodott. |
| Hálózati hely | A hibákat a kérelmek hálózati helye (intranet vagy extranet) alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megismerni a sikertelen kérelemtípusokat. |
|  Alkalmazás | A hibákat a célzott alkalmazás (jogcímfelhasználó alkalmazás) alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy melyik célzott alkalmazás kapja a legtöbb hibát. |

**Metrika : Felhasználók száma** – A hitelesítést aktívan az AD FS segítségével végző egyedi felhasználók száma

|Csoportosítási szempont | Mit jelöl az adott csoportosítás, és miért hasznos? |
| --- | --- |
|Összes |Ez a mérőszám az összevonási szolgáltatást egy adott időszeletben használó felhasználók átlagos darabszámát adja meg. A felhasználók nincsenek csoportosítva. <br>Az átlag a választott időszelet függvénye. |
| Alkalmazás |Az átlagos felhasználószámot a célzott alkalmazás (jogcímfelhasználó alkalmazás) alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, melyik alkalmazást hány felhasználó használja. |

## <a name="performance-monitoring-for-ad-fs"></a>Teljesítményfigyelés az AD FS szolgáltatáshoz
Az Azure AD Connect Health-teljesítményfigyelés a metrikákkal kapcsolatos figyelési információkat biztosít. A Figyelés mező kijelölésével megnyílik egy új panel, amely részletes információkat tartalmaz a metrikákra vonatkozóan.

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/perf1.png)

A panel tetején lévő Szűrővel kiszolgálónként szűrheti az adatokat, és megtekintheti az egyes különálló kiszolgálók metrikáit. A metrika módosításához a jobb gombbal kattintson a figyelési diagramra a figyelés panel alatt, és válassza a Diagram szerkesztése lehetőséget (vagy a Diagram szerkesztése gombot). A megnyíló új panelen a legördülő menüből kiválaszthatja a további metrikákat, és megadhat egy időtartományt a vonatkozó teljesítményadatok megtekintéséhez.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>A felhasználónevet/jelszót leggyakrabban sikertelenül megadó 50 felhasználó
A sikertelen hitelesítési kérelmek leggyakoribb oka az AD FS-kiszolgálókon, hogy a kérelem érvénytelen hitelesítő adatokkal, azaz rossz felhasználónévvel vagy jelszóval rendelkezik. Általában akkor történik meg, ha a felhasználók bonyolult jelszavakat használnak, elfelejtik vagy elgépelik a jelszavakat.

Azonban más okok miatt is előfordulhat, hogy az AD FS-kiszolgálóknak váratlan számú kérelmet kell kezelniük, például a következő esetekben: egy alkalmazás gyorsítótárazza a felhasználói hitelesítő adatokat, és a hitelesítő adatok lejárnak, vagy pedig egy rosszindulatú felhasználó jól ismert jelszavak sorozatával megpróbál bejelentkezni egy fiókba. Ez a két példa érvényes oka lehet annak, hogy a kérelmek száma hirtelen megnő.

Az AD FS szolgáltatáshoz elérhető Azure AD Connect Health jelentést készít a rossz felhasználónév/jelszó megadásával leggyakrabban sikertelenül bejelentkezni próbáló 50 felhasználóról. Ez a jelentés a farmokon lévő összes AD FS-kiszolgáló által létrehozott összes naplózási esemény feldolgozásával készíthető el.

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report1a.png)

A jelentésen belül könnyen elérhetőek az alábbi információk:

* A rossz felhasználónévvel/jelszóval rendelkező meghiúsult kérelmek teljes száma az elmúlt 30 napban
* A rossz felhasználónévvel/jelszóval sikertelenül bejelentkezni próbáló felhasználók átlagos száma naponta.

Erre a részre kattintva megjelenik a fő jelentéspanel, amelyen további részletek láthatóak. Ezen a panelen többek között egy trendekkel kapcsolatos információkat tartalmazó diagram is látható, amellyel meghatározható a rossz felhasználónévvel vagy jelszóval leadott kérelmek alapmetrikája. Megjeleníti továbbá az elmúlt héten sikertelen bejelentkezéssel leggyakrabban próbálkozó 50 felhasználó listáját. Az elmúlt heti 50 felhasználó megtekintésével azonosíthatóak a helytelen jelszóval végzett bejelentkezési kísérletekben jelentkező kiugró értékek.  

A diagramon az alábbi információk találhatóak meg:

* A rossz felhasználónév/jelszó miatt meghiúsult bejelentkezések teljes száma napi szinten.
* A meghiúsult bejelentkezéseket megkísérlő egyedi felhasználók teljes száma napi szinten.
* A legutóbbi kérelmet leadó ügyfél IP-címe

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report3a.png)

A jelentésben az alábbi információk találhatók:

| Jelentéselem | Leírás |
| --- | --- |
| Felhasználóazonosító |A használt felhasználóazonosítót mutatja. Ez a felhasználó által begépelt érték, ami bizonyos esetekben a rossz felhasználóazonosító. |
| Sikertelen próbálkozások |A sikertelen bejelentkezések teljes számát mutatja az adott felhasználóazonosítóhoz. A táblázat a sikertelen bejelentkezések mennyisége alapján van rendezve csökkenő sorrendben. |
| Utolsó sikertelen kísérlet |Az utolsó sikertelen kísérlet időpontjának időbélyegét mutatja. |
| Legutóbbi sikertelen kísérlet IP-címe |A legutóbbi sikertelen kérelemhez tartozó IP-címet jeleníti meg. Ha ebben az értékben egynél több IP-címet lát, lehet, hogy az érték magában foglalja a továbbított ügyfél IP-címét és a felhasználó utoljára megkísérelt kéréséhez tartozó IP-címet.  |

> [!NOTE]
> A jelentés 12 óránként automatikusan frissül az ez idő alatt gyűjtött új információkkal. Ezért az utolsó 12 órában gyűjtött bejelentkezési kísérletek esetleg még nem szerepelnek a jelentésben.
>
>

## <a name="risky-ip-report-public-preview"></a>Kockázatos IP jelentés (nyilvános előzetes verzió)
Az AD FS-ügyfelek az interneten elérhetővé tehetnek jelszóalapú hitelesési végpontokat, és ezzel hitelesítési szolgáltatásokat biztosíthatnak a végfelhasználók számára olyan SaaS-alkalmazások eléréséhez, mint az Office 365. Ez lehetőséget ad kártékony elemek számára, hogy megkíséreljenek bejelentkezni az AD FS rendszerbe, és találgatással kiderítsék a végfelhasználó jelszavát az alkalmazás-erőforrások elérése érdekében. A Windows Server 2012 R2-n futó AD FS-től kezdve elérhető zárolási funkció az extranet-fiókokhoz az ilyen típusú támadások elkerülése érdekében. Ha korábbi verziót használ, erősen ajánlott AD FS rendszerét Windows Server 2016-ra frissíteni. <br />
Ezenkívül előfordulhat, hogy egy IP-címről többször próbálnak bejelentkezni több felhasználó fiókjába. Ilyen esetekben előfordulhat, hogy az egyes felhasználókat érintő kísérletek száma az AD FS fiókzárolási funkcióját aktiváló küszöbérték alatt marad. Az Azure AD Connect Health mostantól elérhető „Kockázatos IP jelentése” észleli az ilyen eseményeket, és értesíti azokról a rendszergazdákat. A jelentés előnyei a következők: 
- Észleli az IP-címeket, amelyek túllépik a sikertelen jelszóalapú belépési kísérletek küszöbértékét
- Támogatja a helytelen jelszó megadása vagy az extranet zárolási állapot miatti sikertelen bejelentkezéseket
- E-mailben értesíti a rendszergazdákat, ha ilyen eseményre kerül sor (az e-mail-beállítások testre szabhatók)
- A küszöbérték-beállítások testreszabhatóak, és összehangolhatók a vállalat biztonsági szabályzatával
- A jelentések letölthetők offline elemzéshez, és automatikusan integrálhatók más rendszerekbe

> [!NOTE]
> A jelentés használatához győződjön meg róla, hogy az AD FS-naplózás engedélyezve van. További információkért tekintse meg az [AD FS-naplózás engedélyezését](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Az előzetes verzió eléréséhez globális rendszergazda vagy [biztonsági olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) szerepkörű felhasználói engedély szükséges.  
> 

### <a name="what-is-in-the-report"></a>A jelentés tartalma
A Kockázatos IP jelentés minden eleme összesített adatokat tartalmaz a sikertelen AD FS-bejelentkezési kísérletekről, amelyek túllépték a kijelölt küszöbértéket. A következő információkat tartalmazza: ![Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report4a.png)

| Jelentéselem | Leírás |
| ------- | ----------- |
| Időbélyegző | Az észlelési időszak kezdetét jelző, az Azure Portal helyi ideje szerinti időbélyegző.<br /> Az összes napi eseményt UTC szerint éjfélkor állítja elő a rendszer. <br />Az óránkénti eseményeknél az időbélyeg az óra kezdetére van kerekítve. Az exportált fájlban a „firstAuditTimestamp” érték mutatja az első tevékenység kezdetét. |
| Eseményindító típusa | Az észlelési időszak típusa. Az összesítési eseményindítók óránkénti és a napi típusúak lehetnek. Ez hasznos lehet a nagy gyakoriságú találgatásos támadások észlelésére a lassú támadásokkal szemben, ahol a kísérletek eloszlanak egy adott napon belül. |
| IP-cím | Olyan kockázatos IP-cím, amelyről helytelen jelszót adtak meg, vagy amelyen extranet zárolást eredményező bejelentkezési tevékenység volt tapasztalható. Ez lehet IPv4- vagy IPv6-cím is. |
| Helytelen jelszó típusú hibák száma | Az IP-címről előforduló helytelen jelszó típusú hibák száma az észlelési időszakban. A helytelen jelszó típusú hiba többször is előfordulhat az egyes felhasználók esetében. Fontos: a lejárt jelszó miatti sikertelen bejelentkezések nem tartoznak ide. |
| Extranet zárolási hibák száma | Az IP-címről előforduló extranet zárolási hibák száma az észlelési időszakban. Extranet zárolási hibák többször is előfordulhatnak az egyes felhasználók esetében. Ez csak akkor jelenik meg, ha az AD FS-ben konfigurálta az extranet zárolást (a 2012 R2-es és újabb verziókban). <b>Megjegyzés:</b> Ha a jelszóalapú extranet bejelentkezés engedélyezve van, erősen ajánlott bekapcsolni ezt a funkciót. |
| Megkísérelt egyedi felhasználók | Azon egyedi felhasználói fiókok száma, amelyekbe megkíséreltek bejelentkezni az IP-címről az észlelési időszakban. Ez segíthet megkülönböztetni az egy és a több felhasználó elleni támadásokat.  |

Például az alábbi jelentésben 2018. 02. 28-án 18:00 és 19:00 között a <i>104.2XX.2XX.9</i> IP-címről helytelen jelszó típusú hiba nem volt, azonban 284 extranet zárolási hiba fordult elő. 14 egyedi felhasználó volt érintett. A tevékenység meghaladta az óránkénti jelentési küszöbértéket. 

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report4b.png)

> [!NOTE]
> - A jelentés listájában kizárólag a küszöbértéket meghaladó tevékenységek jelennek meg. 
> - A jelentés legfeljebb az előző 30 napra vonatkozhat.
> - Ebben a figyelmeztető jelentésben nem jelennek meg az Exchange-IP-címek és a magánhálózati IP-címek, az exportálási listában azonban szerepelnek. 
>

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report4c.png)

### <a name="load-balancer-ip-addresses-in-the-list"></a>Load Balancer IP-címek a listában
A terheléselosztó összesítési bejelentkezési tevékenységei meghiúsultak, és elérte a riasztási küszöbértéket. Ha terheléselosztói IP-címek jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának. Konfigurálja megfelelően a terheléselosztót a továbbított ügyfél IP-címének továbbítására. 

### <a name="download-risky-ip-report"></a>Kockázatos IP jelentés letöltése 
A **letöltési** funkcióval az összes kockázatos IP-cím listája exportálható a Connect Health portálról az elmúlt 30 napra vonatkozóan. Az exportált listában szerepel az egyes észlelési időszakokban előforduló összes sikertelen AD FS-bejelentkezési tevékenység, így az exportálás után testreszabhatja a szűrési feltételeket. A portálon kiemelt összesítések mellett az exportált lista IP-címekre lebontva további részleteket is tartalmaz a sikertelen bejelentkezési tevékenységekről:

|  Jelentéselem  |  Leírás  | 
| ------- | ----------- | 
| firstAuditTimestamp | A sikertelen tevékenységek kezdetének első időbélyegzője az észlelési időszakon belül.  | 
| lastAuditTimestamp | A sikertelen tevékenységek végének utolsó időbélyegzője az észlelési időszakon belül.  | 
| attemptCountThresholdIsExceeded | Annak a jelzője, ha az aktuális tevékenységek túllépték a figyelmeztetési küszöbértéket.  | 
| isWhitelistedIpAddress | Annak a jelzője, ha az IP-cím ki lett szűrve a figyelmeztetésekből és a jelentésekből. A magánhálózati IP-címek (<i>10.x.x.x, 172.x.x.x és 192.168.x.x</i>) és az Exchange-IP-címek szűrve vannak, és True értékűként vannak megjelölve. Ha magánhálózati IP-tartományok jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának.  | 

### <a name="configure-notification-settings"></a>Értesítési beállítások konfigurálása
A jelentés rendszergazdai elérhetőségi adatait az **értesítési beállításokban** lehet frissíteni. Alapértelmezés szerint a kockázatos IP-címekkel kapcsolatos e-mail-értesítés küldése ki van kapcsolva. Az értesítés bekapcsolásához át kell állítani az „E-mail-értesítés küldése, ha egy IP-cím túllépi a sikertelen tevékenységek figyelmeztetési küszöbértékét” beállítás kapcsológombját. A Connect Health általános értesítéseihez hasonlóan itt állíthatja be, hogy kik kapják meg az értesítési jelentést a kockázatos IP-címekről. A módosításról értesíthet minden globális rendszergazdát. 

### <a name="configure-threshold-settings"></a>Küszöbérték-beállítások konfigurálása
A figyelmeztetési küszöbérték küszöbérték-beállításokban módosítható. A rendszerben be van állítva egy alapértelmezett küszöbérték. A kockázatos IP-címek esetében négy küszöbérték-beállítási kategória közül választhat:

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report4d.png)

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

### <a name="faq"></a>GYIK
1. Miért jelennek meg magánhálózati IP-címtartományok a jelentésben?  <br />
A magánhálózati IP-címek (<i>10.x.x.x, 172.x.x.x és 192.168.x.x</i>) és az Exchange-IP-címek szűrve vannak, és True értékűként vannak megjelölve az IP-címek engedélyezési listáján. Ha magánhálózati IP-tartományok jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának.

2. Miért jelennek meg terheléselosztói IP-címek a jelentésben?  <br />
Ha terheléselosztói IP-címek jelennek meg, nagyon valószínű, hogy a külső terheléselosztó nem küldi el az ügyfél IP-címét, amikor továbbítja a webalkalmazás proxykiszolgálójának. Konfigurálja megfelelően a terheléselosztót a továbbított ügyfél IP-címének továbbítására. 

3. Hogyan blokkolhatok IP-címeket?  <br />
Az azonosított kártevő IP-címeket érdemes hozzáadni a tűzfalhoz vagy blokkolni az Exchange-ben.   <br />

4. Miért nem jelenik meg egyetlen elem sem a jelentésben? <br />
   - A sikertelen bejelentkezési tevékenységek nem érték el a beállított küszöbértéket. 
   - Ellenőrizze, hogy nincsen-e aktív „Az állapotszolgáltatás nem naprakész” figyelmeztetés az AD FS-kiszolgálók listáján.  Itt további információkat olvashat [a figyelmeztetés hibaelhárításáról](active-directory-aadconnect-health-data-freshness.md).
   - Az AD FS-farmokon nincs engedélyezve a naplózás.
 
5. Miért nem látható hozzáférés a jelentéshez?  <br />
Globális rendszergazda vagy [biztonsági olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader) szerepkörű felhasználói engedély szükséges. Hozzáférésért forduljon a globális rendszergazdához.


## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Az Azure AD Connect Health műveletei](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – gyakori kérdések](active-directory-aadconnect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](active-directory-aadconnect-health-version-history.md)
