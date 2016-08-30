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
    ms.date="03/10/2016"
    ms.author="heidist"/>

# Adatok importálása az Azure Search szolgáltatásba a portál használatával

Az Azure portál irányítópultján található **Adatok importálása** parancs segítségével elvégezhető az adatfeldolgozás az Azure Search felületén. A parancs olyan beépített indexelő szolgáltatásra támaszkodik, amely feltérképez egy létező adatforrást, majd az ott észlelt sorhalmaz alapján hoz létre és tölt fel dokumentumokat.

A varázslóval az adatok importálása 3 részben történik:

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

A szolgáltatás irányítópultján kattintson duplán az Indexelő csempére az előfizetéshez létrehozott összes indexelő listájának kicsúsztatásához. Az indexelők futtatásához, szerkesztéséhez és törléséhez kattintson rájuk duplán.



<!--HONumber=jun16_HO2-->


