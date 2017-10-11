<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> A StorSimple-adaptert SharePoint RBS konfigurációs beállításainak módosításakor, meg kell bejelentkeznie, amely a Tartománygazdák csoporthoz tartozó fiókkal. A lap továbbá egy központi adminisztrációs ugyanazon a gazdagépen futó böngészőben kell elérnie.
> 
> 

#### <a name="to-configure-rbs"></a>RBS konfigurálása
1. Nyissa meg a SharePoint központi felügyelet lapját, és keresse meg a **rendszerbeállítások**. 
2. Az a **Azure StorSimple** kattintson **konfigurálása a StorSimple-Adapter**.
   
    ![A StorSimple-Adapter konfigurálásához.](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Az a **konfigurálása a StorSimple-Adapter** lap:
   
   1. Győződjön meg arról, hogy a **szerkesztési útvonal engedélyezéséhez** jelölőnégyzet be van jelölve.
   2. A szövegmezőbe írja be az univerzális elnevezési konvenció (UNC) szerinti elérési utat a BLOB-tároló.
      
      > [!NOTE]
      > A BLOB-tároló kötetet a StorSimple eszközön konfigurált iSCSI-köteten kell elhelyezni.

   3. Kattintson a **engedélyezése** távtároló konfigurálni kívánt tartalom-adatbázisok mindegyike esetében gombra.
      
      > [!NOTE]
      > A BLOB-tárolónak kell lennie megosztott és elérhető-e az összes webes előtér-(WFE) kiszolgáló által, és a felhasználói fiók, amelyet a SharePoint-kiszolgálófarm beállított rendelkeznie kell a megosztás eléréséhez.
      
      ![A RBS szolgáltató engedélyezése](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Ha engedélyezi vagy letiltja a RBS, akkor is látható a következő üzenetet.
      
      ![Konfigurálja a StorSimple Adapter engedélyezése letiltása](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Kattintson a **frissítés** a beállítások gombra. Amikor rákattint az **frissítés** gombra, a RBS konfiguráció állapota frissülni fog a ELŐTÉR-webkiszolgáló és a teljes farmban lesznek RBS engedélyezve van. A következő üzenet jelenik meg.
      
      ![Csatoló konfigurációs üzenet](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > RBS nagyon sok (több mint 200) adatbázisok SharePoint-farm beállításakor, a SharePoint központi felügyelet weblap előfordulhat, hogy túllépi az időkorlátot. Ha ez történik, frissítse az oldalt. Ez nem befolyásolja a konfigurációs folyamat.

4. A konfiguráció ellenőrzése:
   
   1. Jelentkezzen be a SharePoint központi felügyeleti webhelyet, és keresse meg a **konfigurálása a StorSimple-Adapter** lap.
   2. Ellenőrizze a konfigurációs adatait, győződjön meg arról, hogy ezek megegyeznek-e a megadott beállításokat. 
5. Győződjön meg arról, hogy RBS megfelelően működik-e:
   
   1. SharePoint dokumentum feltöltése. 
   2. Tallózással keresse meg a beállított UNC elérési utat. Győződjön meg arról, hogy létrejött-e a RBS könyvtárszerkezetét és a feltöltött objektumot tartalmaz.
6. (Választható) Használhatja a Microsoft RBS `Migrate()` részét képező SharePoint meglévő blobtartalom át a StorSimple eszköz PowerShell-parancsmagot. További információkért lásd: [tartalmat át a virtuális gépbe vagy onnan a SharePoint 2013 RBS] [ 6] vagy [tartalmat át a virtuális gépbe vagy onnan RBS (SharePoint Foundation 2010)] [7].
7. (Választható) A teszt telepítésekre ellenőrizheti, hogy a Blobok került kívül a tartalom-adatbázist az alábbiak szerint: 
   
   1. Indítsa el az SQL Management Studiót.
   2. A ListBlobsInDB_2010.sql vagy ListBlobsInDB_2013.sql lekérdezés futtatása, az alábbiak szerint.
      
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
      
      Ha RBS megfelelően lett konfigurálva, NULL értéket meg kell jelennie az összes objektum feltöltött, illetve a RBS sikeresen externalized SizeOfContentInDB oszlop.
8. (Választható) Miután RBS konfigurálása, és helyezze át az összes BLOB tartalma a StorSimple eszköz, az eszköz áthelyezheti a tartalom-adatbázist. A tartalom-adatbázis áthelyezése választja, azt javasoljuk, hogy az eszköz elsődleges kötetként konfigurálása a tartalom-adatbázist tároló. Használja majd, SQL Server ajánlott eljárások a tartalom-adatbázis áttelepítéséhez a StorSimple-eszköz létrehozása. 
   
   > [!NOTE]
   > A tartalom-adatbázis áthelyezése az eszköz csak a StorSimple 8000 series (de még nem támogatott a 5000 vagy 7000-es sorozathoz) támogatott.
   
   Blobok és a tartalom-adatbázist a StorSimple eszköz külön köteteken tárolja, azt javasoljuk a kötet tárolóhoz konfigurálása. Ez biztosítja, hogy azok készül biztonsági másolat együtt.
   
   > [!WARNING]
   > RBS nincs engedélyezve, ha a tartalom-adatbázis áthelyezése a StorSimple-eszköz nem ajánlott. Ez a nem tesztelt konfigurálása során.
   
9. Nyissa meg a következő lépéssel: [szemétgyűjtés konfigurálása](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
