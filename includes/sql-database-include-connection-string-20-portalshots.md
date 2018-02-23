
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Szerezze be a kapcsolati karakterláncot az Azure-portálon
Használja a [Azure-portálon](https://portal.azure.com/) beszerzése a kapcsolati karakterlánc, amely szükséges az ügyfélprogram Azure SQL Database kommunikál. 

1. Válassza ki **minden szolgáltatás** > **SQL-adatbázisok**.

2. Adja meg az adatbázis nevét a szűrő szövegmezőbe közelében bal felső sarkában a **SQL-adatbázisok** panelen.

3. Jelölje ki az adatbázis sort.

4. Miután a panelen megjelenik az adatbázishoz visual kényelmi célokat szolgál, válassza a **kis méret** gombokkal csukja össze a panelen keresse meg és adatbázis-szűrés használja. 
   
5. Az adatbázis paneljén válassza **adatbázis-kapcsolati karakterláncok megjelenítése**.

6. Ha szeretne használni az ADO.NET kapcsolattára, másolja a karakterláncot, címkével **ADO**. 
   
    ![Másolja a ADO kapcsolati karakterláncot az adatbázishoz][20-CopyAdoConnectionString]
7. Egy formátumban vagy egy másik a kapcsolati karakterlánc adatok beillesztése az Ügyfélkód program.

További információkért lásd: [kapcsolati karakterláncok és konfigurációs fájlok](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
