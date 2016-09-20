
<properties
    pageTitle="Az Azure AD Connect Health használata az AD FS szolgáltatással | Microsoft Azure"
    description="Ez az Azure AD Connect Health oldal bemutatja, hogyan figyelheti helyszíni AD FS infrastruktúráját."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="stevenpo"
    editor="karavar"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="vakarand"/>

# Az Azure AD Connect Health használata az AD FS szolgáltatással
Az alábbi dokumentáció az AD FS infrastruktúra Azure AD Connect Health használatával végzett figyelésére vonatkozik. Az Azure AD Connect (szinkronizálási szolgáltatás) az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md) című témakörben tekintheti meg. Az Active Directory tartományi szolgáltatások az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat pedig a [Using Azure AD Connect Health with AD DS](active-directory-aadconnect-health-adds.md) (Az Azure AD Connect Health használata az AD DS szolgáltatással) című témakörben találja. 

## AD FS-riasztások
Az aktív riasztások listáját az Azure AD Connect Health-riasztások szakaszban láthatja. Minden egyes riasztás tartalmazza a fontos információkat, a megoldás lépéseit, valamint a kapcsolódó dokumentációra mutató hivatkozásokat. Egy aktív vagy egy megoldott riasztás kiválasztása esetén egy új panel további információkat, a riasztás megoldását segítő lépéseket és további dokumentációkra mutató hivatkozásokat jelenít meg. A már megoldott riasztások esetében az előzményadatokat is megtekintheti.

Egy riasztás kiválasztása esetén további információk, a riasztás megoldását segítő lépések és további dokumentációkra mutató hivatkozások jelennek meg.

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/alert2.png)



## Használatelemzés az AD FS szolgáltatáshoz
Az Azure AD Connect Health Használatelemzés elemzi az összevonási kiszolgálók hitelesítési forgalmát. A használatelemzés kiválasztásakor megnyílik az elemzés panel, amelyen a metrikák és a csoportosítások tekinthetőek meg.

>[AZURE.NOTE] A használatelemzés AD FS szolgáltatással való használatához győződjön meg róla, hogy az AD FS naplózás engedélyezve van. További információkért tekintse meg az [AD FS-naplózás engedélyezését](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/report1.png)

További metrikák kiválasztásához adjon meg egy időtartományt, vagy a csoportosítás módosításához kattintson egyszerűen jobb gombbal a használatelemzés diagramra, és válassza a Diagram szerkesztése lehetőséget. Ezt követően megadhatja az időtartományt, módosíthatja vagy kiválaszthatja a metrikákat, vagy módosíthatja a csoportosítást. A hitelesítési forgalom eloszlását különféle „metrikák” alapján tekintheti meg, és mindegyik metrikát csoportosíthatja az alább ismertetett vonatkozó „csoportosítási szempont” paraméterek használatával.

| Metrika | Csoportosítási szempont | Mit jelöl az adott csoportosítás, és miért hasznos? |
| ------ | -------- | -------------------------------------------- |
| Kérések összesen: Az összevonási szolgáltatás által feldolgozott kérelmek teljes száma | Összes | Ez a kérelmek teljes darabszámát jeleníti meg csoportosítás nélkül. |
|  | Alkalmazás | Ez a beállítás az összes kérelmet a célzott függő entitások alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, melyik alkalmazás hány százalékát fogadja a teljes forgalomnak. |
|  | Kiszolgáló | Ez a beállítás az összes kérelmet az azokat feldolgozó kiszolgálók alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni a teljes forgalom terheléseloszlását. |
|  | Munkahelyi csatlakoztatás | Ez a beállítás az összes kérelmet az alapján csoportosítja, hogy azok a munkahelyhez csatlakoztatott (ismert) eszközökről érkeznek-e. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy erőforrásaihoz hozzáférnek-e az identitás-infrastruktúra számára ismeretlen eszközökről származó kérelmek. |
|  | Hitelesítési módszer | Ez a beállítás az összes kérelmet a hitelesítés során alkalmazott hitelesítési módszer alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni a hitelesítéshez leggyakrabban használt hitelesítési módszereket. A lehetséges hitelesítési módszerek az alábbiak <ol> <li>Integrált Windows-hitelesítés (Windows)</li> <li>Űrlapalapú hitelesítés (Űrlapok)</li> <li>Egyszeri bejelentkezés (SSO)</li> <li>X509 tanúsítványalapú hitelesítés (Tanúsítvány)</li> <br>Vegye figyelembe, hogy a rendszer a kérelmeket egyszeri bejelentkezésesnek (SSO) veszi, ha az összevonási kiszolgálók a kérelmet SSO cookie-val kapják meg. Ilyen esetekben, ha a cookie érvényes, a rendszer nem kér hitelesítő adatokat a felhasználótól, és zökkenőmentes hozzáférést biztosít az alkalmazáshoz. Ez gyakori, ha több, az összevonási kiszolgálók által védett függő entitással rendelkezik. |
|  | Hálózati hely | Ez a beállítás az összes kérelmet a felhasználó hálózati helye alapján csoportosítja. Ez lehet intranet vagy extranet. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy a forgalom hány százaléka érkezik az intranetről vagy az extranetről. |
| Összes sikertelen kérelem: Az összevonási szolgáltatás által feldolgozott sikertelen kérelmek teljes száma. <br> (Ez a metrika csak az AD FS for Windows Server 2012 R2 szolgáltatás esetében elérhető)| Hibatípus | Ez a beállítás a hibák számát jeleníti meg előre meghatározott hibatípusok szerint. Ez a csoportosítás akkor hasznos, ha szeretné megismerni a gyakori hibatípusokat. <ul><li>Incorrect Username or Password (Helytelen felhasználónév vagy jelszó): A nem megfelelő felhasználónév vagy jelszó okozta hibák.</li> <li>„Extranet Lockout” (Extranet kizárás): A hiba oka, hogy a kérelem az extranetről kizárt felhasználótól érkezett </li><li> „Expired Password” (Lejárt jelszó): A hiba oka, hogy a felhasználó lejárt jelszóval jelentkezett be.</li><li>„Disabled Account” (Letiltott fiók): A hiba oka, hogy a felhasználó letiltott fiókkal jelentkezett be.</li><li>„Device Authentication” (Eszközhitelesítés): A hiba oka, hogy a felhasználó eszközhitelesítéssel való hitelesítése sikertelen volt.</li><li>„User Certificate Authentication” (Felhasználói tanúsítványos hitelesítés): A hiba oka, hogy a felhasználó hitelesítése érvénytelen tanúsítvány miatt sikertelen volt.</li><li>„MFA” (Többtényezős hitelesítés): A hiba oka, hogy a felhasználó többtényezős hitelesítéssel való hitelesítése sikertelen volt.</li><li>„Other Credential” (Egyéb hitelesítő adat): „Issuance Authorization” (Kiadásengedélyezés): Hitelesítési problémák okozta hibák.</li><li>„Issuance Delegation” (Kiadásdelegálás): Kiadásdelegálási problémák okozta hibák.</li><li>„Token Acceptance” (Jogkivonatok elfogadása): A hiba oka, hogy az AD FS elutasított egy külső identitásszolgáltatótól származó jogkivonatot.</li><li>„Protocol” (Protokoll): Protokollproblémák okozta hibák.</li><li>„Unknown” (Ismeretlen): Minden más. Bármely egyéb olyan hiba, amely nem illik a meghatározott kategóriákba.</li> |
|  | Kiszolgáló | Ez a beállítás a hibákat a kiszolgálók alapján csoportosítja. Ez akkor hasznos, ha szeretné megismerni a hibák eloszlását a kiszolgálók közt. Az egyenetlen eloszlás esetleg jelezheti azt, hogy valamelyik kiszolgáló meghibásodott. |
|  | Hálózati hely | Ez a beállítás a hibákat a kérelmek hálózati helye (intranet vagy extranet) alapján csoportosítja. Ez akkor hasznos, ha szeretné megismerni a sikertelen kérelemtípusokat. |
|  | Alkalmazás | Ez a beállítás a hibákat a célzott alkalmazás (függő entitás) alapján csoportosítja. Ez akkor hasznos, ha szeretné megtudni, hogy melyik célzott alkalmazás kapja a legtöbb hibát. |
| Felhasználók száma: A rendszerben aktív egyedi felhasználók átlagos száma | Összes | Ez az összevonási szolgáltatást egy adott időszeletben használó felhasználók átlagos darabszámát adja meg. A felhasználók nincsenek csoportosítva. <br>Az átlag a választott időszelet függvénye. |
|  | Alkalmazás | Ez a beállítás az átlagos felhasználószámot a célzott alkalmazás (függő entitás) alapján csoportosítja. Ez akkor hasznos, ha szeretné megtudni, melyik alkalmazást hány felhasználó használja. |


## Teljesítményfigyelés az AD FS szolgáltatáshoz
Az Azure AD Connect Health-teljesítményfigyelés a metrikákkal kapcsolatos figyelési információkat biztosít. A Figyelés mezőre kattintva megnyílik egy panel, amely részletes információkat tartalmaz a metrikákra vonatkozóan.


![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health/perf1.png)


A panel tetején lévő Szűrővel kiszolgálónként szűrheti az adatokat, és megtekintheti az egyes különálló kiszolgálók metrikáit. A mérőszámok módosításához egyszerűen kattintson a figyelés diagramra a figyelés panel alatt, és válassza a Diagram szerkesztése lehetőséget. Ezt követően a megnyíló új panelen a legördülő menüből kiválaszthatja a további metrikákat, és megadhat egy időtartományt a vonatkozó teljesítményadatok megtekintéséhez.

## Jelentések az AD FS szolgáltatáshoz
Az Azure AD Connect Health jelentéseket biztosít az AD FS tevékenységére és teljesítményére vonatkozóan. Ezeknek a jelentéseknek a segítségével a rendszergazdák betekintést nyerhetnek az AD FS kiszolgálón zajló tevékenységekbe.

### A felhasználónevet/jelszót leggyakrabban sikertelenül megadó 50 felhasználó

A sikertelen hitelesítési kérelmek leggyakoribb oka az AD FS-kiszolgálókon, hogy a kérelem érvénytelen hitelesítő adatokkal, azaz rossz felhasználónévvel vagy jelszóval rendelkezik. Ez általában egy túl összetett vagy elfelejtett jelszó vagy elírás eredménye.

Azonban más okokból is meghiúsulhat az AS FS-kiszolgálók által kezelt meglepően sok ilyen kérelem. Az ilyen okok közé tartoznak például, ha egy alkalmazás gyorsítótárazta a felhasználó hitelesítő adatait, majd ezek az adatok lejárnak, vagy ide tartoznak a rosszindulatú felhasználók, akik több elterjedt jelszó kipróbálásával kísérelnek meg bejelentkezni egy felhasználó fiókjába.

Az AD FS szolgáltatáshoz elérhető Azure AD Connect Health jelentést készít a rossz felhasználónév/jelszó megadásával leggyakrabban sikertelenül bejelentkezni próbáló 50 felhasználóról. Ez a farmokon lévő összes AD FS-kiszolgáló által létrehozott összes naplózási esemény feldolgozásával végezhető el

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report1a.png)

A jelentésen belül könnyen elérhetőek az alábbi információk:

- A rossz felhasználónévvel/jelszóval rendelkező meghiúsult kérelmek teljes száma az elmúlt 30 napban
- A rossz felhasználónévvel/jelszóval sikertelenül bejelentkezni próbáló felhasználók átlagos száma napi szinten.

Erre a részre kattintva megjelenik a fő jelentéspanel, amelyen további részletek láthatóak. Többek közt egy diagram is található itt, amely trendekkel kapcsolatos információkat, amelyekkel meghatározható a rossz felhasználónévvel vagy jelszóval leadott kérelmek alap metrikája, valamint a sikertelen bejelentkezéssel leggyakrabban próbálkozó 50 felhasználó listája.

A diagramon az alábbi információk találhatóak meg:

- A rossz felhasználónév/jelszó miatt meghiúsult bejelentkezések teljes száma napi szinten.
- A meghiúsult bejelentkezéseket megkísérlő egyedi felhasználók teljes száma napi szinten.

![Az Azure AD Connect Health portál](./media/active-directory-aadconnect-health-adfs/report2a.png)

A jelentés a következőket tartalmazza:

| Jelentéselem | Leírás
| ------ | -------- |
|Felhasználóazonosító| A használt felhasználóazonosítót mutatja. Vegye figyelembe, hogy ez az az érték, amelyet a felhasználó adott meg, és egyes esetekben esetleg a használt rossz felhasználóazonosítót láthatja.|
|Sikertelen próbálkozások|A sikertelen bejelentkezések teljes számát mutatja az adott felhasználóazonosítóhoz. A táblázat a sikertelen bejelentkezések mennyisége alapján van rendezve csökkenő sorrendben.|
|Utolsó sikertelen kísérlet|Az utolsó sikertelen kísérlet időpontjának időbélyegét mutatja.



>[AZURE.NOTE] A jelentés 2 óránként automatikusan frissül az ez idő alatt gyűjtött új információkkal. Ezért az utolsó 2 órában gyűjtött bejelentkezési kísérletek esetleg még nem szerepelnek a jelentésben.



## Kapcsolódó hivatkozások

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)](active-directory-aadconnect-health-version-history.md)



<!--HONumber=sep16_HO1-->


