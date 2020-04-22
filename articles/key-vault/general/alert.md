---
title: Az Azure Key Vault figyelése és riasztása | Microsoft dokumentumok
description: Hozzon létre egy irányítópultot a key vault állapotának figyeléséhez és riasztások konfigurálásához.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726942"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Figyelés és riasztás az Azure Key Vaultban

## <a name="overview"></a>Áttekintés

Miután elkezdte használni a key vault az éles titokainak tárolására, fontos, hogy a kulcstartó állapotának figyelése győződjön meg arról, hogy a szolgáltatás megfelelően működik. A szolgáltatás méretezése megkezdésekor a key vaultba küldött kérelmek száma emelkedni fog. Ez növelheti a kérések késését, és szélsőséges esetekben a kérelmek szabályozását okozhatja, ami hatással lesz a szolgáltatás teljesítményére. Akkor is figyelmeztetést kell kapnia, ha a kulcstartó szokatlan számú hibakódot küld, így gyorsan értesítést kaphat a hozzáférési szabályzattal vagy a tűzfal konfigurációjával kapcsolatos problémákról. Ez a dokumentum a következő témaköröket öleli fel:

+ A figyeléshez fontos alapvető key vault-mérőszámok
+ A metrikák konfigurálása és irányítópult létrehozása 
+ Riasztások létrehozása megadott küszöbértékek szerint 

## <a name="basic-key-vault-metrics-to-monitor"></a>A figyeléshez fontos alapvető key vault-mérőszámok

+ A tároló elérhetősége  
+ Boltozat telítettség 
+ Szolgáltatás API-késése 
+ Szolgáltatás API-lekérésének összes encikleszáma (tevékenységtípus szerinti szűrés) 
+ Hibakódok (szűrés állapotkód szerint) 

**A tároló rendelkezésre állása** – Ez a metrika mindig 100%-os ez egy fontos metrika, amelyet figyelni kell, mivel gyorsan megmutathatja, ha a key vault tapasztalt kimaradás. 

**Tároló telítettség** – A kérelmek száma másodpercenként, hogy a key vault kiszolgálhatja a végrehajtott művelet típusától függ. Egyes tárolóműveletek alacsonyabb másodpercenkénti kérelmek küszöbértéket rendelkeznek. Ez a metrika összesíti a key vault teljes használatát az összes művelettípuson, hogy egy százalékos értékkel álljon elő, amely az aktuális key vault-használatot jelzi. A key vault szolgáltatáskorlátok teljes listáját az alábbi dokumentumban. [Az Azure Key Vault szolgáltatáskorlátozásai](service-limits.md)

**Szolgáltatás API-késés** – Ez a metrika a key vault-hívás átlagos késését mutatja. Bár a key vault lehet, hogy a szolgáltatás korlátain belül, a key vault magas kihasználtsága vezethet be késés, amely miatt az alkalmazások alsóbb rétegbeli sikertelen. 

**Összes API-lekérés** – Ez a metrika a key vaultba irányuló összes hívást megjeleníti. Ez segít azonosítani, hogy mely alkalmazások hívják a key vault. 

**Hibakódok** – Ez a metrika megmutatja, ha a key vault tapasztalt szokatlan mennyiségű hibát. A hibakódok és a hibaelhárítási útmutató teljes listáját az alábbi dokumentumban talál. [Az Azure Key Vault REST API-hibakódjai](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>A metrikák konfigurálása és irányítópult létrehozása

1. Jelentkezzen be az Azure Portalba.
2. Navigálás a key vaultra
3. **Metrikák** kiválasztása a **Figyelés** csoportban 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-1.png)

4. Frissítse a diagram címét arra, amit az irányítópulton látni szeretne. 
5. Jelölje ki a hatókört. Ebben a példában kiválasztunk egy kulcstartót. 
6. Válassza ki a metrika **általános tároló rendelkezésre állása** és összesítési **átlaga** 
7. Frissítse az időtartományt az utolsó 24 órára, és frissítse az idő részletességét 1 percre. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-2.png)

8. Ismételje meg a fenti lépéseket a Vault-telítettség és a szolgáltatás API-késés metrikák. A **Pin to Dashboard (Rögzítés az irányítópulton)** lehetőséget választva a mérőszámokat irányítópultra szeretné menteni. 

> [!IMPORTANT]
> Válaszd a "Rögzítés az irányítópultra" lehetőséget, és ments dedikálj minden beállított mutatót. Ha kilép az oldalból, és mentés nélkül tér vissza hozzá, a konfigurációs módosítások elvesznek. 

9. A kulcstárolón lévő összes művelettípus figyeléséhez használja a **Teljes szolgáltatás API-lekérések** metrikát, és válassza a **Felosztás alkalmazása tevékenységtípus szerint lehetőséget.**

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-3.png)

10. Ha hibakódokat szeretne figyelni a key vaultban, használja a **Teljes szolgáltatás API-eredmények** metrikát, és válassza a **Felosztás alkalmazása tevékenységtípus szerint lehetőséget.**

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-4.png)

Most lesz egy műszerfal, amely így néz ki. Akkor kattintson a 3 pont a jobb felső sarokban minden csempe, és akkor átrendezheti és átméretezni a csempe, amire szüksége van. 

Miután mentette és közzétette az irányítópultot, új erőforrást hoz létre az Azure-előfizetésében. A "megosztott irányítópult" kifejezésre való kereséssel bármikor megtekintheti. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Értesítések konfigurálása a Key Vaulton 

Ez a szakasz bemutatja, hogyan konfigurálhatja a riasztásokat a key vault, így figyelmeztetheti a csapatot, hogy azonnal intézkedjen, ha a key vault kifogástalan állapotban van. Beállíthatja azokat a riasztásokat, amelyek e-mailt küldenek, lehetőleg egy csapatdl-nek, eseményrács-értesítést küldenek, vagy felhívnak vagy sms-t küldenek egy telefonszámon. Statikus riasztásokat is választhat egy rögzített érték alapján, vagy egy dinamikus riasztást, amely figyelmezteti, ha egy figyelt metrika egy meghatározott időtartományon belül bizonyos számú alkalommal meghaladja a key vault átlagos korlátját. 

> [!IMPORTANT]
> Kérjük, vegye figyelembe, hogy az újonnan konfigurált riasztások értesítéseinek küldéséhez akár 10 percet is igénybe vehet. 

### <a name="configure-an-action-group"></a>Műveletcsoport konfigurálása 

A műveletcsoport az értesítések és tulajdonságok konfigurálható listája.

1. Jelentkezzen be az Azure Portalba.
2. **Figyelmeztetések** keresése a keresőmezőben
3. **Műveletek kezelése jelölőnégyzetet**

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-6.png)

4. Kijelölés **+ Műveletcsoport hozzáadása**

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-7.png)

5. Válassza ki a **műveletcsoport művelettípusát.** Ebben a példában létrehozunk egy e-mail riasztást.

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-9.png)

6. Kattintson az **OK** gombra a lap alján. Sikeresen létrehozott egy műveletcsoportot. 

Most, hogy konfigurált egy műveletcsoportot, konfiguráljuk a key vault riasztási küszöbértékeket. 

### <a name="configure-alert-thresholds"></a>Riasztási küszöbértékek konfigurálása 

1. Válassza ki a key vault-erőforrást az Azure Portalon, és válassza **a Figyelés csoportban a Riasztások** **lehetőséget.**

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-10.png)

2. **Új riasztási szabály** kiválasztása

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-11.png)

3. Válassza ki a riasztási szabály hatókörét. Kiválaszthat egy vagy több tárolót. 

> [!IMPORTANT]
> Kérjük, vegye figyelembe, hogy ha több tárolót választ ki a riasztások hatóköréhez, az összes kiválasztott tárolónak ugyanabban a régióban kell lennie. A különböző régiókban lévő tárolókhoz külön riasztási szabályokat kell konfigurálnia. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-12.png)

4. Válassza ki az értesítések feltételeit. Az alábbi jelek közül választhat, és megadhatja a riasztási logikát. A Key Vault csapata a következő riasztási küszöbértékek konfigurálását javasolja. 

    + A Key Vault rendelkezésre állása 100% alá csökken (statikus küszöbérték)
    + A Key Vault késése 500 ms-nál nagyobb (statikus küszöbérték) 
    + A tároló teljes telítettsége nagyobb, mint 75% (statikus küszöbérték) 
    + A tároló teljes telítettsége meghaladja az átlagot (dinamikus küszöbérték)
    + Az átlagosnál magasabb összes hibakód (dinamikus küszöbérték) 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>1. példa: Statikus riasztási küszöbérték konfigurálása a késéshez

**A teljes szolgáltatás API-késésének** kiválasztása jelnévként
> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-14.png)

Tekintse meg a következő konfigurációs paramétereket.

+ A küszöbérték beállítása **statikusra** 
+ Az operátor beállítása **nagyobbra, mint**
+ Az összesítési típus **beállítása Átlag értékre**
+ A küszöbérték beállítása **500-ra**
+ Az összesítési időszak **beállítása 5 percre**
+ Állítsa a kiértékelési gyakoriságot **1 percre**
+ Válassza a **Kész** lehetőséget  

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>2. példa: Dinamikus riasztási küszöbérték konfigurálása a tároló telítettségéhez 

Dinamikus riasztás használata esetén megtekintheti a kiválasztott kulcstartó előzményadatait. A kék terület a kulcstartó átlagos használatát jelöli. A piros terület olyan kiugrásokat jelenít meg, amelyek riasztást indítottak volna, feltéve, hogy a riasztási konfiguráció ban más feltételek teljesülnek. A piros pont olyan szabálysértések eseteit jeleníti meg, ahol a riasztás feltételei teljesültek az összesített időablakban. Beállíthatja, hogy egy adott számú szabályszegés után egy adott számú megsértése után riasztást állítson be egy meghatározott időn belül. Ha nem szeretne korábbi adatokat megadni, a speciális beállításokban lehetőség van a régi adatok kizárására. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-16.png)

Tekintse meg a következő konfigurációs paramétereket.

+ A küszöbérték beállítása **dinamikusra** 
+ Az operátor beállítása **nagyobbra, mint**
+ Az összesítési típus **beállítása Átlag értékre**
+ A küszöbérték érzékenységének beállítása **közepesre**
+ Az összesítési időszak **beállítása 5 percre**
+ Állítsa a kiértékelési gyakoriságot **1 percre**
+ **Nem kötelező** Speciális beállítások konfigurálása 
+ Válassza a **Kész** lehetőséget

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-17.png)

5. A konfigurált műveletcsoport hozzáadása

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-18.png)

6. A riasztás engedélyezése és súlyossági szint hozzárendelése

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-19.png)

7. A riasztás létrehozása 


## <a name="next-steps"></a>További lépések

Gratulálunk, most már sikeresen létrehozott egy figyelési irányítópultot, és konfigurálta a kulcstartóriasztásait! Miután követte a fenti lépéseket, e-mailben értesítést kell kapnia, ha a kulcstartó megfelel a beállított riasztási feltételeknek. Erre mutat példát az alábbi ábra. A cikkben beállított eszközökkel aktívan figyelheti a kulcstartó állapotát. 

### <a name="example-email-alert"></a>Példa e-mail riasztásra 

> [!div class="mx-imgBorder"]
> ![Képernyőkép az Azure Portalról](../media/alert-20.png)
