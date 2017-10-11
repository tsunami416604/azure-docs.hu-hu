
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Szerezze be a kapcsolati karakterláncot az Azure-portálon
Használja a [Azure-portálon](https://portal.azure.com/) a kapcsolati karakterlánc szükséges az ügyfélprogram kommunikál az Azure SQL Database az beszerzése: 

1. Kattintson a **Tallózás** > **SQL-adatbázisok**.
2. Adja meg az adatbázis nevét a szűrő szövegmezőbe a bal felső sarokban a közelében a **SQL-adatbázisok** panelen.
3. Az adatbázis sorára kattintson.
4. Miután a panelen megjelenik az adatbázishoz, visual kényelmi célokat szolgál kattintson csukja össze a panelen keresse meg és adatbázis-szűrés használt szabványos kis méret vezérlők. 
   
    ![Az adatbázis elkülönítése szűrő][10-FilterDatabase]
5. Az adatbázis paneljén kattintson **adatbázis-kapcsolati karakterláncok megjelenítése**.
6. Ha szeretne használni az ADO.NET kapcsolattára, másolja a karakterláncot, címkével **ADO**. 
   
    ![Másolja a ADO kapcsolati karakterláncot az adatbázishoz][20-CopyAdoConnectionString]
7. Egy formátumban vagy egy másik a kapcsolati karakterlánc adatok beillesztése az Ügyfélkód program.

További információkért lásd:<br/>[A kapcsolati karakterláncokat és konfigurációs fájlok](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
