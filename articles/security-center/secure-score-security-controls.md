---
title: Biztonságos pontszám az Azure Security Centerben
description: Az Azure Security Center biztonságos pontszámának és biztonsági vezérlőinek leírása
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415840"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Továbbfejlesztett biztonságos pontszám (előzetes verzió) az Azure Security Centerben

## <a name="introduction-to-secure-score"></a>Bevezetés a biztonságos pontszámba

Az Azure Security Center nek két fő célja van: az aktuális biztonsági helyzet megértésének segítése, valamint a biztonság hatékony és hatékony javítása. A Biztonsági központ központi eleme, amely lehetővé teszi, hogy elérje ezeket a célokat a biztonságos pontszám.

A Security Center folyamatosan felméri az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémák miatt. Ezután az összes eredményt egyetlen pontszámba összesíti, így egy pillantással megállapíthatja az aktuális biztonsági helyzetet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint. A pontszám segítségével nyomon követheti a biztonsági erőfeszítéseket és a projekteket a szervezetben. 

A Security Center biztonságos pontszám oldala a következőket tartalmazza:

- **A pontszám** – A biztonságos pontszám százalékértékként jelenik meg, de az alapul szolgáló értékek is egyértelműek:

    [![A biztonságos pontszám százalékértékként jelenik meg, és az alapul szolgáló számok is egyértelműek](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Biztonsági vezérlők** – Minden vezérlő a kapcsolódó biztonsági javaslatok logikai csoportja, és tükrözi a sebezhető támadási felületeket. A vezérlő biztonsági javaslatok készlete, amely a javaslatok megvalósítását segítő utasításokkal segíti. A pontszám csak akkor javul, ha egy vezérlőn belül egyetlen erőforrásra vonatkozó *összes* javaslatot kijavítja.

    Ha azonnal meg szeretné tekinteni, hogy a szervezet mennyire biztosítja az egyes támadási felületeket, tekintse át az egyes biztonsági vezérlők pontszámait.

    További információ: [A biztonságos pontszám kiszámítása az](secure-score-security-controls.md#how-the-secure-score-is-calculated) alábbiakban. 


>[!TIP]
> A Security Center korábbi verziói a javaslat szintjén pontokat kaptak: ha egyetlen erőforrásra vonatkozó javaslatot javított, a biztonságos pontszám javult. Ma a pontszám csak akkor javul, ha egy vezérlőn belül egyetlen erőforrásra vonatkozó *összes* ajánlást kijavítja. Így a pontszám csak akkor javul, ha már javult a biztonság egy erőforrás.
> Bár ez a továbbfejlesztett verzió továbbra is előzetes verzióban érhető el, a korábbi biztonságos pontszám élmény érhető el az Azure Portalon. 


## <a name="locating-your-secure-score"></a>A biztonságos pontszám megkeresése

A Security Center jól láthatóan jeleníti meg a pontszámot: ez az első dolog, ami az Áttekintés oldalon látható. Ha átkattint a dedikált biztonságos pontszám oldalra, látni fogja a pontszám előfizetés szerinti bontásban. Kattintson egy előfizetésre a rangsorolt javaslatok részletes listájának megtekintéséhez, és a javításuk lehetséges hatása az előfizetés pontszámára.

## <a name="how-the-secure-score-is-calculated"></a>A biztonságos pontszám kiszámítása 

Az egyes biztonsági ellenőrzések hozzájárulása az általános biztonsági pontszámhoz egyértelműen megjelenik az ajánlások oldalon.

[![A továbbfejlesztett biztonságos pontszám biztonsági vezérlőket vezet be](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

A biztonsági ellenőrzés összes lehetséges pontjának lekérnie, minden erőforrásnak meg kell felelnie a biztonsági ellenőrzésen belüli összes biztonsági ajánlásnak. A Security Center például több javaslattal rendelkezik a felügyeleti portok védelmére vonatkozóan. A múltban, akkor lehet orvosolni néhány ilyen kapcsolódó és egymástól függő ajánlásokat, miközben mások megoldatlan, és a biztonságos pontszám javulna. Ha objektíven nézzük, könnyű azzal érvelni, hogy a biztonságnem javult, amíg meg nem oldotta őket. Most, meg kell orvosolni őket, hogy a különbség, hogy a biztonságos pontszámot.

Például a "Rendszerfrissítések alkalmazása" nevű biztonsági vezérlő maximális pontszáma hat pont, amely a vezérlő lehetséges növekedésének lehetséges értékéről az elemleírásban látható:

[![A "Rendszerfrissítések alkalmazása" biztonsági vezérlő](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A fenti képernyőképen a "Rendszerfrissítések alkalmazása" biztonsági ellenőrzés lehetősége a "2% (1 pont)" (2% (1 pont)" (2% (1 pont)" (2% (1 pont)" (2% (1 pont)" (2% (1 pont)" értéken látható. Ez azt jelenti, hogy ha kiigazítja az összes ajánlást ebben a vezérlőben, a pontszám 2%- kal nő (ebben az esetben egy pont). Az egyszerűség kedvéért a javaslatlista "Potenciális növekedés" oszlopának értékeit a program egész számokra kerekíti. Az elemleírások a pontos értékeket mutatják:

* **Maximális pontszám** - A maximális pontszám lehet szerezni kitöltésével minden ajánlást egy ellenőrzés. A vezérlő maximális pontszáma a vezérlő relatív jelentőségét jelzi. Használja a maximális pontszám értékeket, hogy osztályozni, amely kérdések et dolgozni először. 
* **Potenciális növekedés** - A többi rendelkezésre álló pontok at a vezérlő. Ahhoz, hogy ezeket a pontokat hozzá a biztonságos pontszámot, orvosolja az összes ellenőrzés ajánlásait. A fenti példában a vezérlő egy pontja valójában 0,96 pont.
* **Aktuális pontszám** - A kontroll aktuális pontszáma. Minden kontroll hozzájárul a teljes pontszámhoz. Ebben a példában a vezérlő 5,04 ponttal járul hozzá a teljes összeghez. 

### <a name="calculations---understanding-your-score"></a>Számítások - a pontszám megértése

|Metrika|Képlet és példa|
|-|-|
|**A biztonsági ellenőrzés aktuális pontszáma**|<br>![Egyenlet a biztonsági vezérlő aktuális pontszámának kiszámításához](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Minden egyes biztonsági ellenőrzés hozzájárul a biztonsági pontszámhoz. A vezérlőn belüli ajánlás által érintett minden egyes erőforrás hozzájárul a vezérlő aktuális pontszámához. Az egyes vezérlőcsoportok aktuális pontszáma a *vezérlőn belüli* erőforrások állapotának mértékegysége.<br>![Elemleírások a biztonsági vezérlő aktuális pontszámának kiszámításához használt értékeket megjelenítő](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Ebben a példában a maximális pontszám 6 lenne osztva 78, mert ez az összeg az egészséges és nem megfelelő erőforrásokat.<br>6 / 78 = 0,0769<br>Az egészséges erőforrások számával (4) való szorzat az aktuális pontszámot eredményezi:<br>0,0769 * 4 = **0,31**<br><br>|
|**Biztonságos pontszám**<br>Egyetlen előfizetés|<br>![Egyenlet az aktuális biztonságos pontszám kiszámításához](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Egyetlen előfizetéses biztonságos pontszám az összes vezérlő vel engedélyezve](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Ebben a példában egyetlen előfizetés van az összes rendelkezésre álló biztonsági vezérlővel (egy potenciális maximális pontszám 60 pont). Az eredmény 28 pontot mutat a lehetséges 60-ból, a fennmaradó 32 pontot pedig a biztonsági ellenőrzések "Potenciális pontszámnövelés" adatai tükrözik.<br>![Az ellenőrzések listája és a potenciális pontszámnövekedés](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Biztonságos pontszám**<br>Több előfizetés|<br>A program hozzáadja az összes erőforrás aktuális pontszámát az összes előfizetésben, és a számítás megegyezik az egyetlen előfizetésével.<br><br>Több előfizetés megtekintésekor a biztonságos pontszám kiértékeli az összes engedélyezett szabályzaton belüli összes erőforrást, és csoportosítja az egyes biztonsági vezérlők maximális pontszámára gyakorolt együttes hatásukat.<br>![Több előfizetés biztonságos pontszáma, ha az összes vezérlő engedélyezve van](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Az összesített pontszám **nem** átlag; inkább az összes erőforrás állapotának kiértékelt állapota az összes előfizetésben.<br>Itt is, ha megy az ajánlások oldalon, és összeadjuk a lehetséges pontokat elérhető, azt találjuk, hogy ez a különbség az aktuális pontszám (24) és a maximális pontszám áll rendelkezésre (60).|
||||

## <a name="improving-your-secure-score"></a>Javítja a biztonságos pontszámot

A biztonságos pontszám javítása érdekében javítsa a biztonsági javaslatokat a javaslatok listájából. Az egyes erőforrásokhoz manuálisan, vagy a Gyorsjavítás használatával is kijavíthatja az egyes **javaslatokat!** lehetőséget (ha rendelkezésre áll), hogy gyorsan alkalmazza a javítást egy ajánláshoz az erőforrások egy csoportjára. További információ: [Ajánlások kijavítása](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Csak a beépített ajánlások hatással vannak a biztonságos pontszám.

## <a name="security-controls-and-their-recommendations"></a>Biztonsági ellenőrzések és ajánlásaik

Az alábbi táblázat az Azure Security Center biztonsági vezérlőit sorolja fel. Az egyes vezérlőkhöz láthatja a biztonságos pontszámhoz hozzáadható pontok maximális számát, ha a vezérlőben felsorolt *összes* ajánlást orvosolja az *összes* erőforrásra vonatkozóan. 

> [!TIP]
> Ha máshogy szeretné szűrni vagy rendezni ezt a listát, másolja és illessze be az Excelbe.

|Biztonsági ellenőrzés|Maximális biztonságos pontok|Javaslatok|
|----------------|:-------------------:|---------------|
|**MFA engedélyezése**|10|- Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban<br>- MFA engedélyezni kell a számlák olvasási engedéllyel az előfizetés<br>- MFA engedélyezni kell számlák írási engedélyekkel az előfizetés|
|**Biztonságos felügyeleti portok**|8|- Just-In-Time hálózati hozzáférés-vezérlés kell alkalmazni a virtuális gépek<br>- Virtuális gépek kell társítani a hálózati biztonsági csoport<br>- Felügyeleti portok be kell zárni a virtuális gépek|
|**Rendszerfrissítések alkalmazása**|6|- Monitoring ügynök egészségügyi problémákat kell megoldani a gépeken<br>- Monitoring ügynök kell telepíteni a virtuális gép méretezési készletek<br>- Monitoring ügynök kell telepíteni a gépekre<br>- Os verzió frissíteni kell a felhőszolgáltatás szerepkörök<br>- A virtuális gép méretezési csoportjainak rendszerfrissítéseit telepíteni kell<br>- A rendszerfrissítéseket telepíteni kell a gépekre<br>- A gépeket újra kell indítani a rendszerfrissítések alkalmazásához<br>- A Kubernetes-szolgáltatásokat nem sebezhető Kubernetes verzióra kell frissíteni<br>- Monitoring ügynök kell telepíteni a virtuális gépek|
|**Sebezhetőségek javítása**|6|- A fejlett adatbiztonságot engedélyezni kell az SQL szervereken<br>- Az Azure Container Registry rendszerképek biztonsági réseit ki kell újítani<br>- Az SQL adatbázisok biztonsági réseit ki kell újítani<br>- A biztonsági réseket a sebezhetőség-értékelési megoldással kell orvosolni<br>- A biztonsági rés felmérését engedélyezni kell az SQL által kezelt példányokon<br>- A biztonsági rés felmérését engedélyezni kell az SQL szervereken<br>- Sebezhetőség értékelési megoldás kell telepíteni a virtuális gépek|
|**Titkosítás engedélyezése inaktív helyen**|4|- A lemeztitkosítást virtuális gépeken kell alkalmazni<br>- Átlátható adattitkosítás sql adatbázisok engedélyezni kell<br>- Automation számla változók at kell titkosítani<br>- A Service Fabric-fürtöknek encryptAndSign-re kell állítaniuk a ClusterProtectionLevel tulajdonságot<br>- SQL szerver TDE protector kell titkosítani a saját kulcs|
|**Adatok titkosítása az átvitel során**|4|- Az API alkalmazás csak HTTPS-en keresztül érhető el<br>- A Function App csak HTTPS-en keresztül érhető el<br>- Csak biztonságos kapcsolatot a Redis Cache engedélyezni kell<br>- Biztonságos átvitel tárfiókok engedélyezni kell<br>- Web alkalmazás csak https-en keresztül érhető el|
|**Hozzáférés és engedélyek kezelése**|4|- Legfeljebb 3 tulajdonost kell kijelölni az előfizetéshez<br>- Elavult számlák el kell távolítani az előfizetés (Preview)<br>- Elavult fiókok tulajdonosi engedélyekkel el kell távolítani az előfizetés (Preview)<br>- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből<br>- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie<br>- A szerepköralapú hozzáférés-vezérlést (RBAC) a Kubernetes Services (Előzetes verzió) szolgáltatáson kell használni<br>- A Service Fabric-fürtök csak az Azure Active Directoryt használhatják az ügyfélhitelesítéshez|
|**Biztonsági konfigurációk javítása**|4|- Pod biztonsági házirendek meg kell határozni a Kubernetes Services<br>- A tárolóbiztonsági konfigurációk biztonsági réseit orvosolják<br>- A gépek biztonsági beállításainak sebezhetőségeit orvosolják<br>- A biztonsági rések a biztonsági konfiguráció a virtuális gép méretezési készletek kell orvosolható<br>- Monitoring ügynök kell telepíteni a virtuális gépek<br>- Monitoring ügynök kell telepíteni a gépekre<br>- Monitoring ügynök kell telepíteni a virtuális gép méretezési készletek<br>- Monitoring ügynök egészségügyi problémákat kell megoldani a gépeken|
|**Jogosulatlan hálózati hozzáférés korlátozása**|4|- IP továbbítása a virtuális gépen le kell tiltani<br>- Engedélyezett IP-tartományokat kell meghatározni a Kubernetes Services (Preview)<br>- (ELAVULT) Az App Services-hez való hozzáférést korlátozni kell (előzetes verzió)<br>- (DEPRECATED) Az IaaS NSG-kwebes alkalmazására vonatkozó szabályokat meg kell erősíteni<br>- Virtuális gépek kell társítani a hálózati biztonsági csoport<br>- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen az API alkalmazáshoz<br>- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a Függvényalkalmazáshoz<br>- Cors nem teszi lehetővé minden erőforrás számára, hogy hozzáférjen a webalkalmazás<br>- A távoli hibakeresést ki kell kapcsolni az API App<br>- A távoli hibakeresést ki kell kapcsolni a Function App<br>- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz<br>- A hozzáférést korlátozni kell az internetre néző virtuális gépekkel rendelkező megengedő hálózati biztonsági csoportok számára<br>- Hálózati biztonsági csoport szabályok internet felé néző virtuális gépek kell szigorítani|
|**Adaptív alkalmazásvezérlő alkalmazása**|3|- Adaptív alkalmazás vezérlők engedélyezni kell a virtuális gépeken<br>- Monitoring ügynök kell telepíteni a virtuális gépek<br>- Monitoring ügynök kell telepíteni a gépekre<br>- Monitoring ügynök egészségügyi problémákat kell megoldani a gépeken|
|**Adatbesorolás alkalmazása**|2|- Érzékeny adatokat az SQL adatbázisok at kell sorolni (Preview)|
|**Az alkalmazások védelme a DDoS-támadások ellen**|2|- A DDoS Protection Standard-ot engedélyezni kell|
|**Végpontvédelem engedélyezése**|2|- A végpontvédelem állapothibáit a virtuálisgép-méretezési csoportokon kell orvosolni<br>- Végpontvédelem egészségügyi problémákat kell megoldani a gépeken<br>- Végpontvédelmi megoldást kell telepíteni a virtuális gép méretezési készletek<br>- Telepítse végpont védelmi megoldás a virtuális gépek<br>- Monitoring ügynök egészségügyi problémákat kell megoldani a gépeken<br>- Monitoring ügynök kell telepíteni a virtuális gép méretezési készletek<br>- Monitoring ügynök kell telepíteni a gépekre<br>- Monitoring ügynök kell telepíteni a virtuális gépek<br>- Telepítse a végpontvédelmi megoldást a gépekre|
|**Naplózás és naplózás engedélyezése**|1|- Naplózás az SQL szerveren engedélyezni kell<br>- Az App Services diagnosztikai naplóit engedélyezni kell<br>- Az Azure Data Lake Store diagnosztikai naplóit engedélyezni kell<br>- Az Azure Stream Analytics diagnosztikai naplóit engedélyezni kell<br>- Diagnosztikai naplók Batch számlák engedélyezni kell<br>- A Data Lake Analytics diagnosztikai naplóit engedélyezni kell<br>- Diagnosztikai naplók Event Hub engedélyezni kell<br>- Az IoT Hub diagnosztikai naplóit engedélyezni kell<br>- Diagnosztikai naplók Key Vault engedélyezni kell<br>- Diagnosztikai naplók Logic Apps engedélyezni kell<br>- Diagnosztikai naplók search szolgáltatás engedélyezni kell<br>- A Service Bus diagnosztikai naplóit engedélyezni kell<br>- Diagnosztikai naplók a virtuális gép méretezési készletek engedélyezni kell<br>- Metrika riasztási szabályokat kell konfigurálni a Batch számlák<br>- Sql naplózási beállításokat kell action-csoportok konfigurálva, hogy rögzítse a kritikus tevékenységek<br>- Az SQL-kiszolgálókat 90 napnál hosszabb naplózási megőrzési nappal kell konfigurálni.|
|**A biztonsággal kapcsolatos gyakorlati tanácsok megvalósítása**|0|- A tűzfallal és virtuális hálózati konfigurációkkal rendelkező tárfiókokhoz való hozzáférést korlátozni kell<br>- A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani az Event Hub névteréből<br>- Az Azure Active Directory rendszergazdáját ki kell építeni az SQL-kiszolgálókhoz<br>- Meg kell határozni az Event Hub-példány engedélyezési szabályait<br>- A tárfiókokat át kell telepíteni az új Azure Resource Manager-erőforrásokba<br>- A virtuális gépeket át kell telepíteni az új Azure Resource Manager-erőforrásokba<br>- Speciális adatbiztonsági beállítások AT SQL server tartalmaznia kell egy e-mail címet kap biztonsági riasztások<br>- A fejlett adatbiztonságot engedélyezni kell a felügyelt példányokon<br>- Minden speciális fenyegetésvédelmi típusok engedélyezni kell az SQL felügyelt példány speciális adatbiztonsági beállítások<br>- E-mail értesítések et adminok és előfizetés-tulajdonosok engedélyezni kell az SQL szerver speciális adatbiztonsági beállítások<br>- A komplex veszélyforrások elleni védelem típusait "All" beállításra kell állítani az SQL server Advanced Data Security beállításaiban<br>- Az alhálózatokat hálózati biztonsági csoporthoz kell társítani<br>- Minden speciális veszélyforrások elleni védelem engedélyezni kell az SQL server speciális adatbiztonsági beállítások|
||||

## <a name="secure-score-faq"></a>Biztonságos pontszám GYIK

### <a name="why-has-my-secure-score-gone-down"></a>Miért csökkent a biztos pontszámom?
A Security Center áttért egy továbbfejlesztett (jelenleg előnézeti állapotban lévő) pontszámra, amely a pontszám kiszámításának módját is tartalmazza. Most meg kell oldania az összes ajánlást egy erőforrás pontok fogadásához. A pontszámok is változott a skála 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Ha a biztonsági ellenőrzés négy ajánlásából csak hármat címezek meg, megváltozik a biztonságos pontszámom?
Nem. Nem változik, amíg nem orvosolja az összes javaslatot egyetlen erőforrás. Ahhoz, hogy a maximális pontszámot a vezérlő, ki kell újítania az összes javaslatot, az összes erőforrást.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>A biztonságos pontszám korábbi tapasztalata még mindig elérhető? 
Igen. Egy ideig egymás mellett fognak futni, hogy megkönnyítsék az átmenetet. Az előző modell fokozatos megszüntetését várják az időben. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Ha egy ajánlás nem vonatkozik rám, és letiltom a házirendben, akkor teljesül a biztonsági vezérlőm, és frissül a biztonságos pontszámom?
Igen. Azt javasoljuk, hogy tiltsa le a javaslatokat, ha azok nem alkalmazhatóak a környezetben. Egy adott javaslat letiltásáról a [Biztonsági házirendek letiltása (Disable) (Biztonsági házirendek letiltása) (Biztonsági házirendek letiltása) (Biztonsági házirendek letiltása) (Biztonsági házirendek letiltása](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Ha egy biztonsági vezérlő nulla pontot ad a biztonságos pontszámomnak, figyelmen kívül hagyjam?
Bizonyos esetekben a vezérlő maximális pontszáma nagyobb, mint nulla, de a hatás nulla. Ha az erőforrások rögzítésének növekményes pontszáma elhanyagolható, a nullára kerekítve lesz. Ne hagyja figyelmen kívül ezeket a javaslatokat, mivel továbbra is biztonsági fejlesztéseket hoznak. Az egyetlen kivétel a "További ajánlott eljárás" vezérlő. Ezek a javaslatok javítása nem növeli a pontszámot, de növeli az általános biztonságot.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a biztonságos pontszámot és a bevezetett biztonsági vezérlőket. A kapcsolódó anyagokat lásd a következő cikkekben:

- [Ismerje meg az ajánlás különböző elemeit](security-center-recommendations.md)
- [További információ a javaslatok kiújulásáról](security-center-remediate-recommendations.md)