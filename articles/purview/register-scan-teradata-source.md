---
title: Teradata-forrás és telepítési vizsgálatok regisztrálása (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan regisztrálhat egy Teradata-forrást az Azure hatáskörébe, és hogyan állíthat be vizsgálatot.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841932"
---
# <a name="register-and-scan-teradata-source-preview"></a>Teradata-forrás regisztrálása és bevizsgálása (előzetes verzió)

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Teradata-forrást a hatáskörébe, és hogyan állíthat be vizsgálatot.

> [!IMPORTANT]
> Ez az adatforrás jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést küldhet nekünk.

## <a name="supported-capabilities"></a>Támogatott képességek

A Teradata forrás támogatja a **teljes vizsgálatot** a metaadatok Teradata-adatbázisból való kinyeréséhez és az adategységek közötti **vonal** beolvasásához.

## <a name="prerequisites"></a>Előfeltételek

- Az összekötő csak a helyszíni hálózaton, egy Azure-beli virtuális hálózaton vagy az Amazon Virtual Private-felhőben található adattárakat támogatja. Ezért létre kell hoznia egy saját üzemeltetésű [integrációs](manage-integration-runtimes.md) modult a csatlakozáshoz.

- Győződjön meg arról, hogy a Java Runtime Environment (JRE) telepítve van a virtuális gépen, ahol a saját üzemeltetésű Integration Runtime telepítve van.
 
- Győződjön meg arról, hogy a "Visual C++ Redistributable 2012 Update 4" van telepítve a saját üzemeltetésű Integration Runtime gépen. Ha még nincs telepítve, [töltse le innen.](https://www.microsoft.com/download/details.aspx?id=30679)

- Manuálisan telepítenie kell egy Teradata JDBC Driver nevű illesztőprogramot a helyszíni virtuális gépre. A végrehajtható JAR-fájl letölthető a [Teradata webhelyéről](https://downloads.teradata.com/).

    > [!Note] 
    > Az illesztőprogramnak elérhetőnek kell lennie a virtuális gép összes fiókja számára. Ne telepítsen felhasználói fiókba.

- A támogatott Teradata-adatbázis **-verziók 12. x és 16. x** közöttiek. Győződjön meg arról, hogy olvasási hozzáférése van a beolvasott Teradata-forráshoz.

### <a name="feature-flag"></a>Szolgáltatás jelzője

A Teradata-források regisztrálása és vizsgálata elérhető a szolgáltatás jelölője mögött. Fűzze hozzá a következőt az URL-címhez: *? Feature. ext. DataSource =% 7b "Teradata": "true"% 7D* 

Például teljes URL-cím [ https://web.purview.azure.com/?feature.ext.datasource=%7b "metaadatok": "true"% 7D](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz
A Teradata-forrás hitelesítésének beállítása egyetlen módon támogatott, **alapszintű adatbázis** -hitelesítés

## <a name="register-a-teradata-source"></a>Teradata-forrás regisztrálása

Ha új Teradata-forrást szeretne regisztrálni a adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába
2. A bal oldali navigációs sávon válassza a **források** lehetőséget
3. **Regisztráció** kiválasztása
4. A **források regisztrálása** lapon válassza a **Teradata** lehetőséget.
5. Válassza a **Folytatás** elemet

A **források regisztrálása (Teradata)** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
2. Adja meg az Teradata-forráshoz való kapcsolódáshoz használandó **állomásnevet** . A kiszolgáló IP-címe vagy teljesen minősített kapcsolódási karakterlánc is lehet.
3. Válasszon ki egy gyűjteményt, vagy hozzon létre egy újat (nem kötelező)
4. **Fejezze** be az adatforrás regisztrálását.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="források regisztrálása – beállítások" border="true":::

## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

Új vizsgálat létrehozásához és futtatásához tegye a következőket:
1. A felügyeleti központban kattintson az *Integration Runtimes* elemre. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modul van beállítva. Ha nincs beállítva, az [itt](manage-integration-runtimes.md) említett lépéseket követve hozzon létre egy saját üzemeltetésű integrációs modult egy helyszíni vagy Azure-beli virtuális gépen való vizsgálathoz, amely hozzáfér a helyszíni hálózathoz.

2. Következő lépésként navigáljon a **forrásokhoz**

3. Válassza ki a regisztrált Teradata forrását.

4. Válassza az + új vizsgálat lehetőséget
 
5. Adja meg az alábbi adatokat:

    - Name (név): a vizsgálat neve

    - Csatlakozás Integration Runtime használatával: válassza ki a konfigurált saját üzemeltetésű integrációs modult

    - Hitelesítési módszer: az adatbázis-hitelesítés jelenleg az egyetlen támogatott lehetőség. Alapértelmezés szerint ez lesz kiválasztva

    - User Name (Felhasználónév): az adatbázis-kiszolgálóhoz való kapcsolódáshoz használandó Felhasználónév. A felhasználónévnek olvasási hozzáféréssel kell rendelkeznie a kiszolgálóhoz

    - Password (jelszó): az adatbázis-kiszolgálóhoz való kapcsolódáshoz használt felhasználói jelszó

    - Séma: az importálandó sémák részhalmazának listázása pontosvesszővel tagolt listaként. például: Schema1; schema2. A rendszer minden felhasználói sémát importál, ha a lista üres. Alapértelmezés szerint a rendszer minden rendszersémát (például SysAdmin) és objektumot figyelmen kívül hagy.

        Az SQL LIKE Expressions szintaxist használó elfogadható sémák neve például A következőt használja:%, pl. A%; B % C%; D
        - Kezdés a vagy    
        - vége B vagy    
        - C vagy    
        - egyenlő D

        A nem és a speciális karakterek használata nem fogadható el

    - Illesztőprogram helye: a Teradata-illesztőprogram helyének teljes elérési útja az ügyfél virtuális gépén. A Teradata JDBC-illesztőprogram nevének a következőket kell tennie: com. Teradata. JDBC. TeraDriver

    - Maximális rendelkezésre álló memória: az ügyfél virtuális gépén elérhető maximális memória (GB-ban) a folyamatok vizsgálatával használható. Ez a vizsgálandó Teradata-forrás méretétől függ.

    > [!Note] 
    > Hüvelykujj szabályként minden 1000-táblázathoz 2 GB memóriát adjon meg

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="telepítési vizsgálat" border="true":::

6. Kattintson a *Continue (folytatás) gombra.*

7. Válassza ki a vizsgálati triggert. Beállíthat egy ütemtervet, vagy futtathatja a vizsgálatot egyszer.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="vizsgálat indítása" border="true":::

8. Tekintse át a vizsgálatot, és kattintson a *Mentés és Futtatás gombra.*

## <a name="viewing-your-scans-and-scan-runs"></a>A vizsgálatok és a vizsgálat futtatásának megtekintése

1. Navigáljon a felügyeleti központhoz. Válassza ki az **adatforrásokat** a **források és vizsgálat** szakaszban.

2. Válassza ki a kívánt adatforrást. Ekkor megjelenik az adatforrás meglévő vizsgálatának listája.

3. Válassza ki azt a vizsgálatot, amelynek eredményeit szeretné megtekinteni.

4. Ezen az oldalon megtekintheti az összes korábbi vizsgálatot, valamint az egyes vizsgálati futtatások metrikáit és állapotát. Emellett azt is megjeleníti, hogy a vizsgálat ütemezett vagy kézi volt-e, hány eszközön alkalmaztak besorolást, hány teljes eszközt észlelt a rendszer, a vizsgálat kezdési és befejezési időpontja, valamint a vizsgálat teljes időtartama.

## <a name="manage-your-scans"></a>A vizsgálatok kezelése

A vizsgálat kezeléséhez vagy törléséhez tegye a következőket:

1. Navigáljon a felügyeleti központhoz. Válassza ki az **adatforrások** lehetőséget a **források és vizsgálat szakaszban,** majd válassza ki a kívánt adatforrást.

2. Válassza ki a kezelni kívánt vizsgálatot. A vizsgálat szerkesztéséhez kattintson a **Szerkesztés** lehetőségre.

3. A vizsgálatot a **delete (Törlés**) lehetőség kiválasztásával törölheti.

## <a name="next-steps"></a>További lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)
