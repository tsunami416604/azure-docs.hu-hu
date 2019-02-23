---
title: Állítsa be Micro fókusz CICS BankDemo Micro fókusz vállalati fejlesztői 4.0 az Azure-ban |} A Microsoft Docs
description: Futtassa a Micro fókusz BankDemo alkalmazás Micro fókusz Enterprise Server és a nagyvállalati fejlesztő további Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: c921fd0c52d555264ab511486709b888b50bd68e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739574"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-in-azure"></a>Állítsa be Micro fókusz CICS BankDemo Micro fókusz vállalati fejlesztői 4.0 az Azure-ban

Beállításakor Micro fókusz Enterprise Server 4.0-s és Enterprise fejlesztői 4.0-s verzióját az Azure-ban, IBM z/OS-munkaterhelések központi telepítések tesztelheti. Ez a cikk bemutatja, hogyan állítható be CICS BankDemo, egy mintaalkalmazást, amely együttműködik a vállalati fejlesztői.

CICs a vásárlói adatokat verziókövetési rendszert, a tranzakciós platform számos, az online nagyszámítógépes alkalmazások által használt rövidítése. Hogyan működnek a vállalati kiszolgáló és a vállalati fejlesztői és a kezeléséről és a egy tényleges teljes zöld képernyő terminálok az alkalmazás üzembe helyezése a BankDemo alkalmazás kiválóan alkalmazható.

## <a name="prerequisites"></a>Előfeltételek

- A virtuális gép [nagyvállalati fejlesztő](set-up-micro-focus-on-azure.md). Ne feledje, hogy rendelkezik-e nagyvállalati fejlesztő egy teljes példánya vállalati kiszolgáló, fejlesztési és tesztelési célokat szolgálnak. Ez a bemutató használt Enterprise Server-példány.

- [Az SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Töltse le és telepítse a vállalati fejlesztői gépen. Enterprise Server egy adatbázis szükséges CICS régiók felügyeletéhez, és a BankDemo alkalmazás BANKDEMO nevű SQL Server adatbázist is használ. Ez a bemutató feltételezi, hogy mindkét adatbázisok SQL Server Expresst használ. Telepítése esetén válassza ki az Alapszintű telepítés.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS az adatbázis-kezelés és a egy T-SQL-parancsprogram futtatása szolgál. Töltse le és telepítse a vállalati fejlesztői gépen.

- [A Visual Studio 2017](https://azure.microsoft.com/downloads/) a legújabb szervizcsomaggal, vagy [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), amely innen tölthető le az ingyenes.

- Asztali vagy egy másik Rumba 3270 emulátort.

## <a name="configure-the-windows-environment"></a>A Windows-környezet konfigurálása

Miután telepítette a vállalati fejlesztői 4.0-s verzióját a virtuális gépen, konfigurálnia kell azt az Enterprise Server példánya. Ehhez meg kell néhány további Windows szolgáltatások a következőképpen telepítheti.

1. RDP használatával jelentkezzen be a vállalati Server 4.0-s virtuális gép hozott létre.

2. Kattintson a **keresési** melletti ikon a **Start** gombra, és írja be **Windows szolgáltatások**. A Kiszolgálókezelő Hozzáadás szerepkörök és szolgáltatások varázsló megnyílik.

3. Válassza ki **webkiszolgáló (IIS) szerepkör**, majd ellenőrizze a következőket:

    - Webkezelési eszközök
    - Kompatibilitás az IIS 6 kezelésével (válassza ki az összes elérhető szolgáltatásokat)
    - IIS-kezelő konzol
    - Az IIS-kezelés parancsfájljai és eszközei
    - IIS-kezelő szolgáltatás

4. Válassza ki **webszolgáltatások**, majd ellenőrizze a következőket:

     Alkalmazásfejlesztési szolgáltatások:
    - .NET-bővíthetőség
    - ASP.NET
    - Általános HTTP-szolgáltatások: Az összes elérhető funkciók hozzáadása
    - Állapot és diagnosztika: Az összes elérhető funkciók hozzáadása
    - Biztonság:
        - Alapszintű hitelesítés
        - Windows-hitelesítés

5. Válassza ki **Windows folyamataktivációs szolgáltatás** és minden gyermekét.

6. A **funkciók**, ellenőrizze **a Microsoft .NET-keretrendszer 3.5.1**, majd ellenőrizze a következőket:

    - Windows Communication Foundation HTTP Activation
    - Windows Communication Foundation Non-HTTP Activation

7. A **funkciók**, ellenőrizze **a Microsoft .NET-keretrendszer 4.6**, majd ellenőrizze a következőket:

    - Nevesített cső aktiválása
    - TCP-aktiválás
    - TCP-Port megosztása

     ![](media/01-demo-roles.png)

8. Ha a kiválasztott összes beállítást, kattintson **tovább** telepítéséhez.

9. A Windows szolgáltatások után lépjen a **Vezérlőpult \> rendszer és biztonság \> felügyeleti eszközök**, válassza ki **szolgáltatások**. Görgessen lefelé, és ellenőrizze, hogy a következő szolgáltatások futnak, és állítsa **automatikus**:

    - **NetTcpPortSharing**
    - **Adaptér Naslouchání Net.Pipe**
    - **Adaptér Naslouchání NET.TCP**

10. Konfigurálja az IIS és a Service, WAS támogatás, a menüben keresse meg **Micro fókusz vállalati fejlesztői parancssort (64 bites)** és a Futtatás mint **rendszergazda**.

11. Típus **wassetup – i** nyomja le az ENTER **Enter**.

12. A szkript futtatása után bezárhatja az ablakot.

## <a name="configure-the-local-system-account-for-sql-server"></a>Az SQL Server a helyi rendszer fiók konfigurálása

Egyes vállalati kiszolgáló folyamatok képeseknek kell lenniük, jelentkezzen be az SQL Server és adatbázisok és egyéb objektumok létrehozásához. Ezeket a folyamatokat a helyi rendszer fiókot használ, így a sysadmin (rendszergazda) szolgáltatót kell megadnia a fiókhoz.

1. Indítsa el a **SSMS** kattintson **Connect** a helyi Windows-hitelesítés használatával SQLEXPRESS kiszolgálóhoz való csatlakozáshoz. Elérhető legyen a **kiszolgálónév** listája.

2. A bal oldalon bontsa ki a **biztonsági** mappára, és válassza **bejelentkezések**.

3. Válassza ki **NT AUTHORITY\\rendszer** válassza **tulajdonságok**.

4. Válassza ki **kiszolgálói szerepkörök** , és ellenőrizze **SysAdmin (rendszergazda)**.

     ![](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>A BankDemo adatbázis és minden objektum létrehozása

1. Nyissa meg **Windows Explorer** , és keresse meg **C:\\felhasználók\\nyilvános\\dokumentumok\\Micro fókusz\\nagyvállalati fejlesztő\\ A minták\\nagyszámítógépes\\CICS\\DotNet\\BankDemo\\SQL**.

2. Másolja ki a tartalmát **BankDemoCreateAll.SQL** fájlt a vágólapra.

3. Nyissa meg **SSMS**. Kattintson a jobb oldali **kiszolgáló** válassza **új lekérdezés**.

4. A vágólap tartalmának beillesztése a **új lekérdezés** mezőbe.

5. Az SQL-utasítás végrehajtása kattintva **Execute** származó a **parancs** fent a lekérdezés lapon.

Hiba nélkül kell futtatni a lekérdezést. Ha elkészült, a mintaadatbázis az BankDemo alkalmazás rendelkezik.

![](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Győződjön meg arról, hogy az adatbázis tábláinak és objektumok létrehozott

1. Kattintson a jobb gombbal a **BANKDEMO** adatbázisra, majd válassza **frissítése**.

2. Bontsa ki a **adatbázis** válassza **táblák**. Megtekintheti a következőhöz hasonló.

     ![](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>A vállalati fejlesztői alkalmazás létrehozása

1. Nyissa meg a Visual Studiót, és jelentkezzen be.

2. Alatt a **fájl** menüpont, jelölje be **nyissa meg a projekt/megoldás**, navigáljon a **C:\\felhasználók\\nyilvános\\dokumentumok\\Micro Fókusz\\nagyvállalati fejlesztő\\minták\\nagyszámítógépes\\CICS\\DotNet\\BankDemo**, és válassza ki a **sln**fájlt.

3. Eltarthat egy ideig objektumok vizsgálata. COBOL programok CopyBooks (CPY) és JCL CBL kiterjesztésű láthatók a Megoldáskezelőben.

4. Kattintson a jobb gombbal a **BankDemo2** projektet, és válassza ki **Set as Startup Project**.

    > [!NOTE]
    > A BankDemo projekt felhasznál HCOSS (gazdagép alkalmazáskompatibilitási beállítást az SQL Server), amely nem használható ez a bemutató.

5. A **Megoldáskezelőben**, kattintson a jobb gombbal a **BankDemo2** projektet, és válassza **összeállítása**.

    > [!NOTE]
    > A megoldás szintjén épület HCOSS nincs konfigurálva, hibák, eredményez.

6. A projekt létrejött, vizsgálja meg a **kimeneti** ablak. Az alábbi képhez hasonlóan kell kinéznie.

     ![](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Az a régió adatbázisba BankDemo alkalmazás üzembe helyezése

1. Nyisson meg egy vállalati fejlesztői parancssort (64 bites) rendszergazdaként.

2. Keresse meg a **nyilvános %\\dokumentumok\\Micro fókusz\\nagyvállalati fejlesztő\\minták\\nagyszámítógépes\\CICS\\DotNet\\ BankDemo**.

3. Hajtsa végre a parancsot a parancssorba **bankdemodbdeploy** és a paramétert az adatbázis központi telepítése, például:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Ügyeljen arra, hogy használjon perjellel (/) nem fordított perjelet (\\). Ez a szkript fut egy ideig.

![](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>A BankDemo régió létrehozása a vállalati rendszergazda a .NET-hez

1. Nyissa meg a **vállalati kiszolgáló, a .NET-felügyelet** felhasználói felületén.

2. Kezdő MMC beépülő modul a Windows **Start** menüben válassza a **Micro fókusz nagyvállalati fejlesztő \> konfigurációs \> vállalati kiszolgáló, a .NET-rendszergazda**. (A Windows Server, válassza ki a **Micro fókusz nagyvállalati fejlesztő \> vállalati kiszolgáló, a .NET-rendszergazda**).

3. Bontsa ki a **régiók** a bal oldali panelen, majd kattintson rá jobb gombbal a tárolóra **CICS**.

4. Válassza ki **definiálása régió** hozhat létre egy új CICS régióban nevű **BANKDEMO**, üzemeltetett a (helyi) adatbázisban.

5. Adja meg az adatbázis-kiszolgálópéldányra, kattintson a **tovább**, majd adja meg a régió neve **BANKDEMO**.

     ![](media/07-demo-cics.png)

6. Válassza ki a régiót a régiók közötti adatbázis-definíciós fájljának, keresse meg a **régió\_bankdemo\_db.config** a **C:\\felhasználók\\nyilvános\\ Dokumentumok\\Micro fókusz\\nagyvállalati fejlesztő\\minták\\nagyszámítógépes\\CICS\\DotNet\\BankDemo**.

     ![](media/08-demo-cics.png)

7. Kattintson a **Befejezés** gombra.

## <a name="create-xa-resource-definitions"></a>XA erőforrás-definíciók létrehozása

1. A bal oldali panelen, a **vállalati kiszolgáló, a .NET-felügyelet** felhasználói felületén, bontsa ki a **rendszer**, majd **XA erőforrás-definíciókban**. Ez a beállítás határozza meg, hogyan Enterprise Server és az alkalmazás-adatbázisok együttműködik-e a a régióban.

2. Kattintson a jobb gombbal a **XA erőforrás-definíciókban** válassza **Server-példány hozzáadása**.

3. A legördülő mezőben válassza ki a **adatbázis szolgáltatáspéldány**. A helyi gép SQLEXPRESS lesz.

4. Válassza ki az alatt a **XA erőforrás-definíciókban (machinename\\sqlexpress)** tárolóra, majd kattintson **Hozzáadás**.

5. Válassza ki **XA-adatbázis erőforrás-definíció** és írja be **BANKDEMO** számára a **neve** és **régió**.

     ![](media/09-demo-xa.png)

6. Kattintson a három pontra (**...** ) viszi, megjelenik a kapcsolati karakterlánc varázsló. A **kiszolgálónév**, típus **(helyi)\\SQLEXPRESS**. A **bejelentkezési**válassza **Windows-hitelesítés**. Az adatbázis nevét, írja be a **BANKDEMO**

     ![](media/10-demo-string.png)

7. A kapcsolat tesztelése.

## <a name="start-the-bankdemo-region"></a>Indítsa el a BANKDEMO régió

> [!NOTE]
> Fontos, az első lépés: A régió az imént létrehozott XA erőforrás-definíció használatához be kell állítani.

1. Keresse meg a **BANDEMO CICS régió** alatt a **régiók tároló**, majd válassza ki **régió indítási fájl szerkesztése** származó a **műveletek** panel. Görgessen le az SQL-tulajdonságokat, és adja meg **bankdemo** számára a **XA erőforrásnév** , vagy jelölje ki azt a három pontra.

2. Kattintson a **mentése** ikonra a módosítások mentéséhez.

3. Kattintson a jobb gombbal **BANKDEMO CICS régió** a a **konzol** panelre, és válassza **indítása és leállítása régió**.

4. Alsó részén a **indítása / leállítása** a középső ablaktáblán, válassza a megjelenő mezőben **Start**. Néhány másodperc elteltével indítja el a régióban.

     ![](/media/11-demo-sql.png)

     ![](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Figyelő létrehozása

Hozzon létre egy figyelőt a TN3270-munkamenetet, amely a BankDemo-alkalmazás elérésére van szüksége.

1. A bal oldali ablaktáblán bontsa ki a **konfigurációs szerkesztők** válassza **figyelő**.

2. Kattintson a **fájl megnyitása** ikonra, és válassza a **seelistener.exe.config** fájlt. Ez a fájl lesz szerkeszthető, és be van töltve a vállalati kiszolgáló indításakor.

3. Figyelje meg, hogy a két régióban korábban definiált (ESDEMO és JCLDEMO).

4. Szeretne létrehozni egy új régióban BANKDEMO, kattintson a jobb gombbal **régiók**, és válassza ki **régió hozzáadása**.

5. Válassza ki **BANKDEMO régió**.

6. Egy TN3270 csatorna hozzáadásához kattintson a jobb gombbal **BANKDEMO régió** és kiválasztásával **Hozzáadás csatorna**.

7. A **neve**, adja meg **TN3270**. A **Port**, adja meg **9024**. (Vegye figyelembe, hogy a ESDEMO alkalmazás 9230-portot használja, ezért egy másik port használatára kell.)

8. Mentse a fájlt, kattintson a **mentése** ikonra, vagy válasszon **fájl** \> **mentése**.

9. A figyelő indításához kattintson a **Start figyelő** ikonra, vagy válasszon **beállítások** \> **Start figyelő**.

     ![](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Rumba az BankDemo alkalmazáshoz való hozzáférés konfigurálása

Az utolsó lépésben kell tennie az Rumba, egy 3270 emulator használatával 3270 munkamenet konfigurálása. Ez a lépés lehetővé teszi az imént létrehozott figyelő keresztül hozzáférjen az BankDemo alkalmazáshoz.

1. A Windows a **Start** menüben Rumba asztal elindításához.

2. Alatt a **kapcsolatok** menü eleméhez válassza **TN3270**.

3. Kattintson a **beszúrása** , és írja be **127.0.0.1** az IP-cím és **9024** a felhasználó által definiált port.

4. Kattintson a párbeszédpanel alján **Connect**. Egy fekete CICS képernyő jelenik meg.

5. Típus **bank** BankDemo alkalmazásához a kezdeti 3270 képernyő megjelenítéséhez.

6. Írja be a felhasználói Azonosítójához tartozó **B0001** , és írja be a jelszót, és semmit. Az első képernyő BANK20 nyílik meg.

![](media/14-demo.png)
![](media/15-demo.png)

Gratulálunk! Most egy CICS alkalmazást futtat az Azure-ban Micro fókusz vállalati kiszolgáló használatával.

## <a name="learn-more"></a>Részletek

- [Nagyszámítógépek Migrálása – portál](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [hibaelhárítással](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Az Azure-migrálás nagyszámítógépes nyelvszakértőinkből](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
