---
title: Micro Focus CICS BankDemo beállítása micro focus enterprise developer 4.0-hoz az Azure virtuális gépeken
description: Futtassa a Micro Focus BankDemo alkalmazást az Azure virtuális gépeken (VM-ek), hogy megtanulja a Micro Focus Enterprise Server és az Enterprise Developer használatát.
author: sread
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 4491fc137c2c85e2be605f5e58fde6fd422efbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67621338"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Micro Focus CICS BankDemo beállítása micro focus enterprise developer 4.0-hoz az Azure-on

Amikor a Micro Focus Enterprise Server 4.0-s és az Enterprise Developer 4.0-s verziót az Azure-ban állítja be, tesztelheti az IBM z/OS számítási feladatok központi telepítését. Ez a cikk bemutatja, hogyan állíthatja be a CICS BankDemo mintaalkalmazást, amely az Enterprise Developer alkalmazással rendelkezik.

A CIC az Ügyfélinformáció-ellenőrző Rendszer rövidítése, amely számos online nagyszámítógépes alkalmazás által használt tranzakciós platform. A BankDemo alkalmazás kiválóan alkalmas az Enterprise Server és az Enterprise Developer működésének megismerésére, valamint a zöld képernyős terminálokkal kiegészített tényleges alkalmazások kezelésére és telepítésére.

## <a name="prerequisites"></a>Előfeltételek

- Virtuális gép [nagyvállalati fejlesztővel.](set-up-micro-focus-azure.md) Ne feledje, hogy az Enterprise Developer fejlesztője az Enterprise Server teljes példányával rendelkezik fejlesztési és tesztelési célokra. Ez a példány a bemutatóhoz használt Enterprise Server példánya.

- [SQL Server 2017 Express kiadás](https://www.microsoft.com/sql-server/sql-server-editions-express). Töltse le és telepítse az Enterprise Developer virtuális gépre. Az Enterprise Server használatához adatbázisra van szükség a CICS-régiók kezeléséhez, és a BankDemo alkalmazás egy BANKDEMO nevű SQL Server-adatbázist is használ. Ez a bemutató feltételezi, hogy mindkét adatbázishoz SQL Server Express programot használ. Telepítéskor válassza ki az alaptelepítést.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). Az SSMS az adatbázisok kezelésére és egy T-SQL parancsfájl futtatására szolgál. Töltse le és telepítse az Enterprise Developer virtuális gépre.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) a legújabb szervizcsomaggal vagy visual [studio közösséggel](https://visualstudio.microsoft.com/vs/community/), amelyet ingyenesen tölthet le.

- Rumba Desktop vagy egy másik 3270 emulátor.

## <a name="configure-the-windows-environment"></a>A Windows-környezet konfigurálása

Miután telepítette az Enterprise Developer 4.0-s verziót a virtuális gépre, konfigurálnia kell az Enterprise Server hozzá kapcsolódó példányát. Ehhez telepítenie kell néhány további Windows-szolgáltatást az alábbiak szerint.

1. Az RDP használatával jelentkezzen be a létrehozott Enterprise Server 4.0 virtuális gépre.

2. Kattintson a **Start** gomb melletti **Keresés** ikonra, és írja be a **Windows-szolgáltatások kifejezést**. Megnyílik a Kiszolgálókezelő Szerepkörök és szolgáltatások hozzáadása varázslója.

3. Válassza **a Webkiszolgálói szerepkör (IIS) szerepkör**lehetőséget, majd ellenőrizze az alábbi beállításokat:

    - Webkezelő eszközök
    - IIS 6 felügyeleti kompatibilitás (az összes elérhető szolgáltatás kiválasztása)
    - IIS kezelése konzol
    - IIS felügyeleti parancsfájlok és -eszközök
    - IIS felügyeleti szolgáltatás

4. Válassza a **World Wide Web Services**lehetőséget, és ellenőrizze az alábbi beállításokat:

     Alkalmazásfejlesztési funkciók:
    - .NET-bővíthetőség
    - ASP.NET
    - Közös HTTP-funkciók: Az összes elérhető funkció hozzáadása
    - Állapot és diagnosztika: Az összes elérhető funkció hozzáadása
    - Biztonság:
        - Alapszintű hitelesítés
        - Windows-hitelesítés

5. Válassza a **Windows folyamataktivációs szolgáltatás** át, valamint annak összes gyermeke lehetőséget.

6. **Szolgáltatások esetén**ellenőrizze a **Microsoft .**

    - Windows Kommunikációs alaprendszer HTTP aktiválása
    - A Windows Kommunikációs alaprendszer nem HTTP-aktiválása

7. **Szolgáltatások esetén**ellenőrizze a **Microsoft .**

   - Elnevezett cső aktiválása
   - TCP aktiválása
   - TCP-port megosztása

     ![Szerepkörök és szolgáltatások hozzáadása varázsló: Szerepkör-szolgáltatások](media/01-demo-roles.png)

8. Ha az összes beállítást kiválasztotta, a telepítéshez kattintson a **Tovább** gombra.

9. A Windows-szolgáltatások után nyissa meg a **Vezérlőpult \> rendszer- és biztonsági \> felügyeleti eszközei t,** és válassza a **Szolgáltatások**lehetőséget. Görgessen le, és győződjön meg arról, hogy a következő szolgáltatások futnak, és **automatikus:**

    - **NetTcpPortSharing**
    - **Net.Pipe figyelő adapter**
    - **Net.tcp figyelő adapter**

10. Az IIS és a WAS támogatás konfigurálásához a menüből keresse meg a **Micro Focus Enterprise Developer Command Prompt (64 bites) parancssorát,** és futtassa **rendszergazdaként.**

11. Írja be **a wassetup –i** billentyűt, és nyomja **le az Enter billentyűt.**

12. A parancsfájl futtatása után bezárhatja az ablakot.

## <a name="configure-the-local-system-account-for-sql-server"></a>Az SQL Server helyi rendszerfiókjának konfigurálása

Egyes Enterprise Server-folyamatoknak képesnek kell lenniük az SQL Server kiszolgálón való bejelentkezésre, valamint adatbázisok és egyéb objektumok létrehozására. Ezek a folyamatok a helyi rendszerfiókot használják, ezért rendszergazdai jogosultságot kell adnia a fióknak.

1. Indítsa el az **SSMS-t,** és kattintson a **Csatlakozás** gombra a helyi SQLEXPRESS-kiszolgálóhoz való csatlakozáshoz a Windows-hitelesítés használatával. A **kiszolgálónév** listában elérhetőnek kell lennie.

2. A bal oldalon **bontsa** ki a Biztonság mappát, és válassza **a Bejelentkezések**lehetőséget.

3. Válassza az **NT AUTHORITY\\SYSTEM, majd** a Properties **(Tulajdonságok)** lehetőséget.

4. Válassza **a Kiszolgálói szerepkörök lehetőséget,** és ellenőrizze **a rendszergazda imát.**

     ![SSMS objektumkezelő ablak: Bejelentkezési tulajdonságok](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>A BankDemo adatbázis és annak összes objektumának létrehozása

1. Nyissa meg **a Windows Intézőt,** és keresse meg a **C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL -t.**

2. Másolja a **BankDemoCreateAll.SQL** fájl tartalmát a vágólapra.

3. Nyissa **meg az SSMS-t**. A jobb oldalon kattintson a **Kiszolgáló** gombra, és válassza az **Új lekérdezés**lehetőséget.

4. Illessze be a vágólap tartalmát az **Új lekérdezés** mezőbe.

5. Az SQL végrehajtása a lekérdezés feletti **Parancs** lapon a **Végrehajtás** gombra kattintva.

A lekérdezésnek hiba nélkül kell futnia. Ha elkészült, rendelkezik a BankDemo alkalmazás mintaadatbázisával.

![SQLQuery1.sql kimenet](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Az adatbázistáblák és -objektumok létrehozásának ellenőrzése

1. Kattintson a jobb gombbal a **BANKDEMO** adatbázisra, és válassza a **Frissítés**parancsot.

2. Bontsa ki az **adatbázist,** és válassza **a Táblák**lehetőséget. Látnia kell valami ilyesmit.

     ![BANKDE tábla kibontva az Objektumkezelőben](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Az alkalmazás létrehozása az Enterprise Developer alkalmazásban

1. Nyissa meg a Visual Studio alkalmazást, és jelentkezzen be.

2. A **Fájl** menüben válassza a **Project/Solution megnyitása**lehetőséget, válassza a **C:\\\\Users Public\\Documents\\Micro\\Focus Enterprise Developer\\Samples\\Mainframe CICS DotNet BankDemo (Nagyszámítógép\\CICS\\DotNet\\BankDemo)** lapot, és válassza ki az **sln** fájlt.

3. Szánjon egy kis időt, hogy vizsgálja meg a tárgyakat. A COBOL-programok a Megoldáskezelőben, a CBL kiterjesztéssel, valamint a CopyBooks (CPY) és a JCL alkalmazásban jelennek meg.

4. Kattintson a jobb gombbal a **BankDemo2** projektre, és válassza **a Beállítás indítási projektként parancsot.**

    > [!NOTE]
    > A BankDemo projekt a HCOSS (Host Compatibility Option for SQL Server) programot használja, amelyet nem használ ez a demó.

5. A **Megoldáskezelőben**kattintson a jobb gombbal a **BankDemo2** projektre, és válassza a **Build parancsot.**

    > [!NOTE]
    > A megoldás szintjén való építés hibákat eredményez, mivel a HCOSS nincs konfigurálva.

6. A projekt megépítéseután vizsgálja meg a **Kimenet** ablakot. Az alábbi képen láthatóhoz hasonlóan kell kinéznie.

     ![Sikeres létrehozási időszakot megjelenítő kimeneti ablak](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>A BankDemo alkalmazás telepítése a régió adatbázisba

1. Nyisson meg egy Enterprise Developer parancssort (64 bites) rendszergazdaként.

2. Keresse meg a **\\%PUBLIC%\\Documents\\\\Micro Focus\\Enterprise Developer\\mintáját\\a\\Nagyszámítógépes CICS DotNet BankDemo-ból.**

3. A parancssorban hajtsa végre a **bankdemodbdeploy parancsot,** és adja meg az adatbázis telepítéséhez szükséges paramétert, például:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Ügyeljen arra, hogy előre perjelet (/)\\használjon, ne hátrafelé perjel( ). Ez a szkript fut egy ideig.

![Felügyelet: Az Enterprise Developer parancssori ablaka](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>A .NET vállalati rendszergazdája bankdemo régiójának létrehozása

1. Nyissa meg a **Vállalati kiszolgáló szolgáltatás .NET felügyeleti** felhasználói felületét.

2. Az MMC beépülő modul elindításához válassza a Windows **Start** menüjében a **Micro Focus Enterprise Developer \> Configuration \> Enterprise Server for .NET Admin**parancsot. (Windows Server esetén válassza **a \> Micro Focus Enterprise Enterprise Server for .NET Admin lehetőséget.**

3. **Bontsa** ki a Régiók tárolót a bal oldali ablaktáblában, majd kattintson a jobb gombbal a **CICS**elemre.

4. Válassza **a Régió definiálása** lehetőséget egy (helyi) adatbázisban tárolt **BANKDEMO**nevű új CICS-régió létrehozásához.

5. Adja meg az adatbázis-kiszolgáló példányt, kattintson a **Tovább**gombra, majd írja be a RÉGIÓ NEVÉT **BANKDEMO**.

     ![Terület definiálása párbeszédpanel](media/07-demo-cics.png)

6. A régióközi adatbázis régiódefiníciós fájljának kiválasztásához keresse meg a **\_régió bankdemo\_db.config fájlját** a **C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**területen.

     ![Régió definiálása - Régió neve: BANKDEMO](media/08-demo-cics.png)

7. Kattintson a **Befejezés** gombra.

## <a name="create-xa-resource-definitions"></a>XA-erőforrás-definíciók létrehozása

1. A Vállalati kiszolgáló **szolgáltatás .NET felügyeleti** felhasználói felületének bal oldali ablaktáblájában bontsa ki a **Rendszer,** majd az **XA erőforrás-definíciók csomópontot.** Ez a beállítás határozza meg, hogy a régió hogyan működik együtt az Enterprise Server kiszolgálóval és az alkalmazásadatbázisokkal.

2. Kattintson a jobb gombbal az **XA erőforrás-definíciók elemre,** és válassza **a Kiszolgálópéldány hozzáadása parancsot.**

3. A legördülő lista válassza az **Adatbázis-szolgáltatáspéldány lehetőséget.** Ez lesz a helyi gép SQLEXPRESS.

4. Válassza ki a példányt az **XA\\erőforrás-definíciók (gépnév sqlexpress)** tárolóban, és kattintson a **Hozzáadás gombra.**

5. Válassza **az Adatbázis XA erőforrás-definíciója lehetőséget,** majd írja be a **BANKDEMO** parancsot a Név és **régió** **mezőbe.**

     ![Új Adatbázis XA erőforrás-definícióképernyő](media/09-demo-xa.png)

6. Kattintson a három pontra (**...**) a Kapcsolati karakterlánc varázsló felsorakezéséhez. A **Kiszolgálónév mezőbe**írja be a **\\(helyi) SQLEXPRESS nevet.** A **Bejelentkezés hez**válassza a **Windows-hitelesítés**lehetőséget. Adatbázisnevének beírásához írja be a **BANKDEMO (BANKDEMO)** nevet.

     ![Kapcsolati karakterlánc szerkesztése képernyő](media/10-demo-string.png)

7. Tesztelje a kapcsolatot.

## <a name="start-the-bankdemo-region"></a>A BANKDEMO régió indítása

> [!NOTE]
> Az első lépés fontos: Be kell állítania a régiót az imént létrehozott XA erőforrás-definíció használatához.

1. Nyissa meg a **BANDEMO CICS-régiót** a **Régiók tároló**csoportban, majd válassza a **Régiók indítási fájl szerkesztése** lehetőséget a **Műveletek** ablaktáblán. Görgessen le az SQL-tulajdonságokhoz, és adja meg az **XA-erőforrás nevének** **bankdemo** ját, vagy a három ponttal jelölje ki.

2. A módosítások mentéséhez kattintson a **Mentés** ikonra.

3. Kattintson a jobb gombbal a **BANKDEMO CICS-régióra** a **Konzol** ablaktáblán, és válassza a **Start/Stop Region parancsot.**

4. A középső ablaktáblán megjelenő **Start/Stop Terület** mező alján válassza a **Start**lehetőséget. Néhány másodperc múlva elindul a régió.

     ![SQL Start/Stop mező](media/11-demo-sql.png)

     ![CICS Region BANKDEMO - Started képernyő](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Figyelő létrehozása

Hozzon létre egy figyelőt a BankDemo alkalmazáshoz hozzáférő TN3270 munkamenetekhez.

1. A bal oldali ablaktáblában bontsa ki a **Configuration Editors (Figyelő) csomópontot,** és válassza **a Figyelő**lehetőséget.

2. Kattintson a **Fájl megnyitása** ikonra, és válassza a **seelistener.exe.config** fájlt. Ez a fájl szerkesztésre kerül, és az Enterprise Server minden indításakor betöltődik.

3. Figyelje meg a korábban definiált két régiót (ESDEMO és JCLDEMO).

4. Új bankdemo régió létrehozásához kattintson a jobb gombbal **a Régiók**elemre, és válassza a Régió **hozzáadása parancsot.**

5. Válassza ki **a BANKDEMO régiót**.

6. TN3270 csatorna hozzáadásához kattintson a jobb gombbal a **BANKDEMO régió** elemre, és válassza **a Csatorna hozzáadása parancsot.**

7. A **Név mezőbe**írja be a **TN3270 értéket.** A **Port (Port**) mezőbe írja be a **9024**értéket. Az ESDEMO alkalmazás a 9230-as portot használja, ezért másik portot kell használnia.

8. A fájl mentéséhez kattintson a **Mentés** ikonra, vagy válassza a **Fájlmentés** \> **lehetőséget**.

9. A figyelő elindításához kattintson a **Figyelő indítása** ikonra, vagy válassza a **Beállítások** \> **Figyelő indítása parancsot**.

     ![Figyelő konfigurációs szerkesztőablakai](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>A Rumba konfigurálása a BankDemo alkalmazás eléréséhez

Az utolsó dolog, amit meg kell tennie, hogy konfigurálja a 3270 munkamenet segítségével Rumba, a 3270 emulátor. Ez a lépés lehetővé teszi, hogy a BankDemo alkalmazás eléréséhez a létrehozott figyelőn keresztül.

1. A Windows **Start** menüjében indítsa el a Rumba Desktop programot.

2. A **Kapcsolatok** menüpontalatt válassza a **TN3270**lehetőséget.

3. Kattintson a **Beszúrás gombra,** és írja be a **127.0.0.1-es** és a **9024-es** ip-címet a felhasználó által definiált porthoz.

4. A párbeszédpanel alján kattintson a **Csatlakozás gombra.** Fekete CICS képernyő jelenik meg.

5. Írja be a **bank** ot a BankDemo alkalmazás kezdeti 3270 képernyőjének megjelenítéséhez.

6. A Felhasználói azonosító mezőbe írja be a **B0001 típust,** és a jelszómezőbe írjon be bármit. Megnyílik az első bank20 képernyő.

![Mainframe Display](media/14-demo.png)
![Üdvözlőképernyő Nagyszámítógépes kijelző - Rumba - Alrendszer bemutató képernyő](media/15-demo.png)

Gratulálunk! Most egy CICS-alkalmazást futtat az Azure-ban a Micro Focus Enterprise Server használatával.

## <a name="next-steps"></a>További lépések

- [Enterprise Server futtatása Docker-tárolókban az Azure-ban](run-enterprise-server-container.md)
- [Nagyszámítógépes áttelepítés - Portál](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [hibaelhárítással](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Nagyszámítógép demystifying az Azure-migráláshoz](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
