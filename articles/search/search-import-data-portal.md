<properties
    pageTitle="Adatok importálása az Azure Search szolgáltatásba az Azure portál indexelőinek használatával | Microsoft Azure | Üzemeltetett felhőalapú keresési szolgáltatás"
    description="Indexelők használata az Azure portálon"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="06/08/2016"
    ms.author="heidist"/>

# Adatok importálása az Azure Search szolgáltatásba a portál használatával

Az Azure Portal Azure Search irányítópultján található **Adatok importálása** parancs segítségével az adatok betölthetők egy indexbe. A parancs a beépített indexelő szolgáltatásokra támaszkodik, amelyek feltérképeznek egy létező adatforrást, majd az abból lekért sorhalmaz alapján hoznak létre és töltenek fel dokumentumokat.

Az adatoknak a varázslóval való importálása 3 részből áll:

- adatforrás-kapcsolat
- az adatok feltöltésére használt célindex (a legtöbb esetben a varázsló ezt létrehozza)
- az azonnali vagy rendszeres időközönkénti futtatást megadó ütemezés

Egy indexelő vagy az **Adatok importálása** parancs használatához az elsődleges adatforrásnak a következő támogatott adatforrások egyikének kell lennie: Azure SQL Database, relációs SQL Server-adatbázisok egy Azure virtuális gépen vagy Azure DocumentDB.

Az importálás kizárólag egyedi táblából, nézetből és egyenértékű adatszerkezetből végezhető el. A keresési indexbe a megfelelő metaadatok és bemeneti adatok beszerzéséhez lehetséges, hogy először ezt az adatstruktúrát kell létrehoznia az alkalmazás adatforrásában.

Mintaadatok használatával kipróbálhatja ezt a munkafolyamatot. Ennek megkezdéséhez tekintse meg az [Ismerkedés az Azure Search szolgáltatással az Azure portálon](search-get-started-portal.md) részt.

##Az adatok importálásának konfigurálása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Nyissa meg az Azure Search szolgáltatás irányítópultját. Alább látható néhány módszer az irányítópult megkeresésére.
    - Az ugrósávon kattintson a **Kezdőlap** elemre. A kezdőlapon az előfizetéshez tartozó valamennyi szolgáltatás csempéje megtalálható. Kattintson az adott csempére a szolgáltatás irányítópultjának megnyitásához.
    - A Keresés szolgáltatás megkereséséhez az ugrósávon kattintson a **Összes tallózása** > **Szűrés módja** > **Keresési szolgáltatások** lehetőségekre.

3. A szolgáltatás irányítópultjának felső részén egy parancssávot láthat, amelyen az **Adatok importálása** parancs is szerepel. Az Adatok importálása panel csúsztatott megnyitásához kattintson az **Adatok importálása** lehetőségre.

4. Az indexelő által használt adatforrás-definíció megadásához kattintson a **Csatlakozás az adatokhoz** lehetőségre. A lehetőségek a következők:
    -   Olyan meglévő adatforrás, amely az indexelő számára korábban létrehozott adatforrás-definícióra hivatkozik. Ha a Search-szolgáltatásban már meg vannak adva indexelők, másik importálási folyamatra konfigurálhat át egy adatforrás-definíciót.
    -   Az Azure SQL használatával történik az adatforrás-kapcsolat megadása az Azure egyik SQL-adatbázisán vagy egy Azure virtuális gépen található SQL Server-adatbázison.
    -   A DocumentDB az adott adatforrás-típushoz tartozó adatforrás-kapcsolat megadására használatos.

   Az adatbázisnak az Azure SQL és a DocumentDB esetében egyaránt az előfizetés keretén belül kell lennie. Ha ismeri azt, beilleszthet egy kapcsolati karakterláncot, esetleg kiválaszthat egy olyan, már létező adatforrást, amelyet egy, az előfizetéséhez írási jogosultsággal rendelkező felhasználó hozott létre.

5. Az alapértelmezett index véglegesítéséhez kattintson a **Célindex testreszabása** lehetőségre.
    - A **Kulcs** megadása kötelező. A kulcs számára kiválasztott mezőnek egyedi értékeket tartalmazó karakterláncmezőnek kell lennie.
    - A mező neve és típusa általában már ki van töltve. Az adattípus módosítható.
    - Válassza ki az egyes mezőkhöz tartozó attribútumokat:
        - A lekérdezhető attribútum a keresési eredmények között visszaadja a mező értékét.
        - A szűrhető attribútum hivatkozhatóvá teszi az adott mezőt a szűrőkifejezésekben.
        - A rendezhető attribútum segítségével az adott mezőt használhatjuk egy rendezés alapjaként.
        - Az értékkorlátozó attribútum értékkorlátozott navigációt tesz lehetővé az adott mező esetében.
        - A kereshető attribútum lehetővé teszi a teljes szöveges keresést.
    - Nyelvi elemző mezőszinten történő megadásához kattintson az **Elemző** lapra. Részletes információk: [Több nyelven elérhető dokumentumok indexének létrehozása](search-language-support.md).
    - Az automatikus kiegészítés vagy a gépelés közbeni lekérdezési javaslatok engedélyezéséhez kattintson a **Javaslatok** elemre.

6. Kattintson az **Adatok importálása** parancsra az importálási művelet Futtatás most beállítással történő végrehajtásához, illetve ismétlődő ütemezés beállításához.

Az imént befejezett adatimportálási művelet egy indexelőt hozott létre a háttérben. Az indexelő közvetlen szerkesztésével most módosíthatja annak összetevőit.

##Meglévő indexelő szerkesztése

A szolgáltatás irányítópultján kattintson duplán az Indexelő csempére az előfizetéshez létrehozott összes indexelő listájának kicsúsztatásához. Az indexelők futtatásához, szerkesztéséhez és törléséhez kattintson rájuk duplán. Lecserélheti az indexet egy másik létező indexre, módosíthatja az adatforrást, és beállíthatja az indexelés során érvényes hibaküszöböket.

##Meglévő index szerkesztése

Az Azure Search szolgáltatásban az index strukturális frissítéséhez az index újjáépítésére van szükség, ami az index törléséből, újbóli létrehozásából és az adatok újbóli betöltéséből áll. A strukturális frissítések közé tartozik az adattípusok módosítása és a mezők átnevezése vagy törlése.

Az újjáépítést nem igénylő szerkesztési műveletek közé tartozik az új mezők hozzáadása, illetve a pontozási profilok, a javaslatok vagy a nyelvelemzők módosítása. További információ: [Update Index](https://msdn.microsoft.com/library/azure/dn800964.aspx) (Index frissítése).



<!--HONumber=sep16_HO1-->


