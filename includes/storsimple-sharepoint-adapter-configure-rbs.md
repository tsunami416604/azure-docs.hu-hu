---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179159"
---
> [!NOTE]
> A SharePoint RBS-konfigurációhoz készült StorSimple adapter módosításakor a Tartománygazdák csoporthoz tartozó felhasználói fiókkal kell bejelentkeznie. Ezenkívül a konfigurációs lapot a központi felügyeletdel azonos állomáson futó böngészőből kell elérnie.
> 
> 

#### <a name="to-configure-rbs"></a>Az RBS konfigurálása
1. Nyissa meg a SharePoint központi felügyelet lapját, és keresse meg a **Rendszerbeállítások gombot.** 
2. Az **Azure StorSimple** szakaszban kattintson **a StorSimple adapter konfigurálása**elemre.
   
    ![A StorSimple adapter konfigurálása](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. A **StorSimple adapter konfigurálása** lapon:
   
   1. Győződjön meg arról, hogy a **Szerkesztési görbe** jelölőnégyzet be van jelölve.
   2. A szövegmezőbe írja be a BLOB-tároló UNC elérési útját.
      
      > [!NOTE]
      > A BLOB-tároló kötetét a StorSimple-eszközön konfigurált iSCSI-köteten kell üzemeltetni.

   3. Kattintson az **Engedélyezés** gombra a távtárolóhoz konfigurálni kívánt tartalom-adatbázisok alatt.
      
      > [!NOTE]
      > A BLOB-tárolónak minden webes előtér-kiszolgálónak meg kell osztania és elérhetőnek kell lennie, és a SharePoint-kiszolgálófarmhoz konfigurált felhasználói fióknak hozzáféréssel kell rendelkeznie a megosztáshoz.
      
      ![Az RBS-szolgáltató engedélyezése](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Az RBS engedélyezésekor vagy letiltásakor a következő üzenet is megjelenik.
      
      ![StorSimple adapter letiltásának konfigurálása](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. A **konfiguráció** alkalmazásához kattintson a Frissítés gombra. Ha a **Frissítés gombra** kattint, az RBS konfigurációs állapota minden WFE-kiszolgálón frissül, és az egész farm RBS-kompatibilis lesz. A következő üzenet jelenik meg.
      
      ![Adapter konfigurációs üzenete](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Ha nagyon sok (200-nál nagyobb) Adatbázissal rendelkező SharePoint-farmhoz konfigurálja az RBS-t, előfordulhat, hogy a SharePoint Központi Felügyelet weblapja időtúlsszelődik. Ebben az esetben frissítse az oldalt. Ez nincs hatással a konfigurációs folyamatra.

4. Ellenőrizze a konfigurációt:
   
   1. Jelentkezzen be a SharePoint központi felügyelet webhelyére, és keresse meg a **StorSimple adapter konfigurálása** lapot.
   2. Ellenőrizze a konfiguráció részleteit, és győződjön meg arról, hogy azok megfelelnek a megadott beállításoknak. 
5. Ellenőrizze, hogy az RBS megfelelően működik-e:
   
   1. Dokumentum feltöltése a SharePointba. 
   2. Tallózással keresse meg a konfigurált UNC-elérési utat. Győződjön meg arról, hogy az RBS könyvtárstruktúra létrejött, és hogy az tartalmazza a feltöltött objektumot.
6. (Nem kötelező) A SharePoint ban `Migrate()` található Microsoft RBS PowerShell-parancsmag segítségével áttelepítheti a meglévő BLOB-tartalmat a StorSimple-eszközre. További információt a Tartalom áttelepítése a [SharePoint 2013-ban vagy az RBS-ből való kivándorolás a SharePoint 2013-ban][6] vagy [az ON-INDEXből (SharePoint Foundation 2010)][7]című témakörben talál.
7. (Nem kötelező) A teszttelepítések során az alábbiak szerint ellenőrizheti, hogy a BLOB-k kikerültek-e a tartalom-adatbázisból: 
   
   1. Indítsa el az SQL Management Studio alkalmazást.
   2. Futtassa a ListBlobsInDB_2010.sql vagy ListBlobsInDB_2013.sql lekérdezést az alábbiak szerint.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Ha az RBS megfelelően van konfigurálva, null értékjelenik meg a SizeOfContentInDB oszlopban minden olyan objektumnál, amelyet feltöltöttek és sikeresen kikülsőleg kezeltek az RBS-sel.
8. (Nem kötelező) Miután konfigurálta az RBS-t, és az összes BLOB-tartalmat áthelyezheti a StorSimple-eszközre, áthelyezheti a tartalom-adatbázist az eszközre. Ha úgy dönt, hogy áthelyezi a tartalom-adatbázist, javasoljuk, hogy elsődleges kötetként konfigurálja az eszközön lévő tartalom-adatbázis-tárolót. Ezután a létrehozott SQL Server gyakorlati tanácsok segítségével telepítse át a tartalom-adatbázist a StorSimple eszközre. 
   
   > [!NOTE]
   > A tartalom-adatbázis áthelyezése az eszközre csak a StorSimple 8000 sorozat ban támogatott (az 5000-es vagy 7000-es sorozat ban nem támogatott).
   
   Ha a BLOB-kat és a tartalom-adatbázist külön kötetekben tárolja a StorSimple eszközön, azt javasoljuk, hogy ugyanabban a kötettárolóban konfigurálja őket. Ez biztosítja, hogy a biztonsági másolatot kapnak.
   
   > [!WARNING]
   > Ha nem engedélyezte az RBS-t, nem javasoljuk a tartalom-adatbázis áthelyezését a StorSimple eszközre. Ez egy nem tesztelt konfiguráció.
   
9. Folytassa a következő lépéssel: [A szemétgyűjtés konfigurálása](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
