---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889383"
---
> [!NOTE]
> A StorSimple Adapter for SharePoint RBS konfigurációs beállításainak módosításakor, meg kell bejelentkeznie, amely a Tartománygazdák csoporthoz tartozó felhasználói fiókkal. Emellett a konfigurációs lapon kell elérnie egy központi adminisztrációs ugyanazon a gazdagépen futó böngészőben.
> 
> 

#### <a name="to-configure-rbs"></a>RBS konfigurálása
1. Nyissa meg a SharePoint központi felügyelet lapján, és keresse meg a **rendszerbeállítások**. 
2. Az a **Azure StorSimple** területén kattintson **konfigurálása a StorSimple Adapter**.
   
    ![A StorSimple Adapter konfigurálása](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Az a **konfigurálása a StorSimple Adapter** oldalon:
   
   1. Győződjön meg arról, hogy a **szerkesztési útvonal engedélyezéséhez** jelölőnégyzet be van jelölve.
   2. A szövegmezőbe írja be az univerzális elnevezési konvenció (UNC) szerinti elérési útját a BLOB-tároló.
      
      > [!NOTE]
      > A BLOB-tároló kötet kell futhat egy iSCSI-kötet konfigurálása a StorSimple eszközön.

   3. Kattintson a **engedélyezése** alábbi távoli tároló konfigurálni kívánt tartalom-adatbázisok mindegyike gombra.
      
      > [!NOTE]
      > A BLOB-tárolóban kell lennie megosztott és érhető el az összes webes előtér (WFE) kiszolgálók, és a felhasználói fiók, amely konfigurálva van a SharePoint-kiszolgálófarm rendelkeznie kell hozzáféréssel a megosztáshoz.
      
      ![A RBS szolgáltató engedélyezése](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Amikor engedélyezi vagy letiltja a RBS, a következő üzenetet is látni fogja.
      
      ![A StorSimple Adapter engedélyezése letiltása konfigurálása](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Kattintson a **frissítés** gomb a alkalmazni a konfigurációt. Amikor rákattint a **frissítés** gomb, RBS konfigurációs állapota frissülni fog a WFE-kiszolgálóval, és a teljes farmban RBS engedélyezve van. A következő üzenet jelenik meg.
      
      ![Adapter konfigurációs üzenet](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Egy nagyon nagy számú (több mint 200) adatbázisok SharePoint-farm beállításakor RBS, a SharePoint központi felügyelet weblap lehet, hogy időtúllépés. Ha ez bekövetkezik, frissítse az oldalt. Ez nem befolyásolja a konfigurációs folyamat.

4. A konfiguráció ellenőrzése:
   
   1. Jelentkezzen be a SharePoint központi felügyeleti webhelyet, és keresse meg a **konfigurálása a StorSimple Adapter** lapot.
   2. Ellenőrizze a konfigurációs adatait, győződjön meg arról, hogy azok megfelelnek a megadott beállításokat. 
5. Győződjön meg arról, hogy RBS megfelelően működik-e:
   
   1. Töltse fel a dokumentum a SharePoint. 
   2. Keresse meg a beállított UNC elérési utat. Győződjön meg arról, hogy létrejött-e a RBS könyvtárstruktúrát és a feltöltött objektumot tartalmaz.
6. (Nem kötelező) Használhatja a Microsoft RBS `Migrate()` SharePoint meglévő BLOB-tartalmak migrálása a StorSimple-eszközt a mellékelt PowerShell-parancsmagot. További információkért lásd: [tartalmainak Migrálása, vagy onnan máshová a SharePoint 2013-ban RBS] [ 6] vagy [tartalmainak Migrálása, vagy onnan máshová RBS (a SharePoint Foundation 2010)] [7].
7. (Nem kötelező) A teszt telepítésekre ellenőrizheti, hogy a Blobok át lettek helyezve kívül a tartalom-adatbázist a következő: 
   
   1. Start SQL Management Studio.
   2. A következőképpen futtassa a ListBlobsInDB_2010.sql vagy ListBlobsInDB_2013.sql lekérdezést.
      
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
      
      Ha RBS megfelelően lett konfigurálva, a NULL értéket meg kell jelennie a SizeOfContentInDB oszlop lett töltve, és sikeresen externalized RBS az objektumhoz.
8. (Nem kötelező) Miután RBS konfigurálása és az összes BLOB tartalma áthelyezése a StorSimple-eszköz, továbbléphet a tartalom-adatbázist az eszközön. A tartalom-adatbázis áthelyezése választja, azt javasoljuk, hogy konfigurálja-e a tartalom-adatbázist tároló mint elsődleges kötet az eszközön. Ezután használja az SQL Server ajánlott eljárások a tartalom-adatbázis áttelepítése a StorSimple-eszköz jön létre. 
   
   > [!NOTE]
   > A tartalom-adatbázis áthelyezése az eszköz csak a támogatott a StorSimple 8000 sorozat (nem támogatott a 5000 és 7000-es sorozathoz).
   
   Ha a Blobok és a tartalom-adatbázist a StorSimple eszközön külön köteteken tárolja, azt javasoljuk, ugyanabban a tárolóban kötet konfigurálása. Ez biztosítja, hogy azok mikor készüljön biztonsági másolat együtt.
   
   > [!WARNING]
   > Ha nem engedélyezte a RBS, a tartalom-adatbázis áthelyezése a StorSimple-eszköz nem javasoljuk. Ez az egy nem tesztelt konfigurációt.
   
9. Nyissa meg a következő lépés: [Konfigurálja a szemétgyűjtés](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
