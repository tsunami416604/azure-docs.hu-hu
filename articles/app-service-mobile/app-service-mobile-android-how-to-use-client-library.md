---
title: Hogyan használható az Azure Mobile Apps SDK Android |} Microsoft Docs
description: Az Azure Mobile Apps SDK használatával Android rendszeren
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: d89aa308ab8f6684cebbec49bbefdcb54d77c886
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Az Azure Mobile Apps SDK használatával Android rendszeren

Ez az útmutató bemutatja, hogyan használhatja a Android SDK a Mobile Apps általános esetben, például a megvalósításához:

* Lekérdezi az adatok (beszúrása, frissítése és törlése).
* Hitelesítés.
* Kezeli a hibákat.
* Az ügyfél testreszabása.

Ez az útmutató elsősorban az ügyféloldali Android SDK-val.  További információt a kiszolgálóoldali SDK-k a Mobile Apps, lásd: [működéséhez a .NET-háttérrendszerrel SDK] [ 10] vagy [használata a Node.js-háttéralkalmazáshoz SDK][11].

## <a name="reference-documentation"></a>Referenciadokumentációt

Megtalálhatja az [Javadocs API-referencia] [ 12] az Android ügyféloldali kódtár a Githubon.

## <a name="supported-platforms"></a>A támogatott platformok

Az Azure Mobile Apps SDK for Android API szintek 19-24 (KitKat nugát keresztül) helyigénnyel telefon és táblagép támogatja.  Hitelesítés, különösen, használja a hitelesítő adatok gyűjtéséhez közös webes keretrendszer megközelítést.  Kiszolgáló-folyamat hitelesítési kis űrlap tényező eszközök, például az órákat nem működik.

## <a name="setup-and-prerequisites"></a>A telepítő és Előfeltételek

Fejezze be a [Mobile Apps gyors üzembe helyezés](app-service-mobile-android-get-started.md) oktatóanyag.  Ez a feladat biztosítja, hogy a fejlesztés az Azure Mobile Apps minden előfeltétel teljesült.  A gyors üzembe helyezés is segítséget nyújt a fiókjának a konfigurálása és az első Mobile Apps-háttéralkalmazás létrehozása.

Ha nem kívánja a gyors üzembe helyezési oktatóanyag elvégzéséhez, a következő feladatokat kell végrehajtani:

* [Mobile Apps-háttéralkalmazás létrehozása] [ 13] androidos használata.
* Az Android Studióban [frissítés a gradle-lel build fájlok](#gradle-build).
* [Engedélyezi az internet engedélycsoportot](#enable-internet).

### <a name="gradle-build"></a>A gradle-lel build fájl frissítése

Mindkettőt módosíthatja **build.gradle** fájlok:

1. Adja hozzá a kódot, hogy a *projekt* szint **build.gradle** fájlon belül a *buildscript* címke:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Adja hozzá a kódot, hogy a *modul app* szint **build.gradle** fájlon belül a *függőségek* címke:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    A legújabb verzióra jelenleg 3.4.0. A támogatott verziók találhatók [a Files][14].

### <a name="enable-internet"></a>Internet engedélycsoportot engedélyezése

Azure szeretne használni, az alkalmazás engedélyezve van az INTERNET engedéllyel kell rendelkeznie. Ha még nincs engedélyezve, adja hozzá a következő sort a kód a **AndroidManifest.xml** fájlt:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Egy ügyfél-kapcsolat létrehozása

Az Azure Mobile Apps a mobilalkalmazásban négy funkciókat biztosít:

* Adathozzáférés és -kapcsolat nélküli szinkronizálás az Azure Mobile Apps-szolgáltatások.
* Az Azure Mobile Apps Server SDK-val írt egyéni API-k hívása.
* Hitelesítés az Azure App Service-hitelesítéshez és engedélyezéshez.
* Leküldéses értesítés regisztrálása a Notification hubs használatával.

Ezek először megköveteli, hogy hozzon létre egy `MobileServiceClient` objektum.  Csak egy `MobileServiceClient` objektumot kell létrehozni a mobil ügyfél (Ez azt jelenti, hogy legyen egy egypéldányos mintát).  Létrehozásához egy `MobileServiceClient` objektum:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

A `<MobileAppUrl>` egy karakterlánc vagy egy URL-cím objektum, amely a mobil-háttéralkalmazást mutat.  Ha az Azure App Service segítségével végzi a mobil-háttéralkalmazást gazdagép, majd biztosítja, hogy a biztonságos `https://` verzióját az URL-címet.

Az ügyfél is azt a tevékenységet vagy Context - hozzáférésre van szüksége a `this` példában paraméter.  A MobileServiceClient konstrukció belül megtörténik a `onCreate()` módszer a tevékenység hivatkozik a `AndroidManifest.xml` fájlt.

Ajánlott eljárásként a kiszolgáló közötti kommunikációt a saját (Egypéldányos – minta) osztályba kell absztrakt.  Ebben az esetben kell átadni a tevékenységet a konstruktor megfelelően beállítani a szolgáltatást.  Példa:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Most hívása `AzureServiceAdapter.Initialize(this);` a a `onCreate()` a fő tevékenységnél metódusában.  Semmilyen más metódus anélkül, hogy az ügyfélen használjon `AzureServiceAdapter.getInstance();` a szolgáltatás adapter mutató hivatkozás beszerzése.

## <a name="data-operations"></a>Adatok műveletek

Az Azure Mobile Apps SDK mag, hogy a Mobile Apps-háttéralkalmazás SQL Azure-ban tárolt adatok eléréséhez.  Hozzáférnének ehhez ezen adatokhoz szigorú típusmegadású osztályokat (ajánlott) vagy típus nélküli lekérdezéseket (nem ajánlott).  Ez a szakasz a nagy foglalkozik szigorú típusmegadású osztályokat.

### <a name="define-client-data-classes"></a>Ügyfél adatosztályok definiálása

Az SQL Azure táblákból származó adatok eléréséhez ügyfél adatok definiálására, amelyek megfelelnek a Mobile Apps-háttéralkalmazás tábláihoz. Ebben a témakörben szereplő példák feltételezik nevű tábla **MyDataTable**, amely a következő oszlopokkal rendelkezik:

* id
* Szöveg
* Végezze el

A megfelelő típusos ügyféloldali objektum a nevű fájlban található **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adja hozzá az egyes mezők hozzáadott elérő és beállító metódusokat.  Ha az SQL Azure táblában több oszlopot tartalmaz, akkor mezőket ad hozzá a megfelelő ezt az osztályt.  Például ha a DTO (adatobjektum átviteli) kellett szerepel egészszám-oszloppal prioritású virtuális gép, majd a mezőt, valamint beolvasó és beállító metódusa:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

További táblák létrehozása a Mobile Apps-háttéralkalmazásának további tudnivalókért lásd: [hogyan: Adja meg egy tábla vezérlő] [ 15] (.NET-háttérrendszer) vagy [dinamikus sémával definiálása táblák] [ 16] (Node.js háttérrendszer).

Egy Azure Mobile Apps-háttéralkalmazás tábla meghatározza, hogy öt speciális mezőket, négy, amelyek elérhetők az ügyfelek számára:

* `String id`: A rekord globálisan egyedi azonosítója.  Ajánlott eljárásként, ellenőrizze az azonosító karakterláncos ábrázolása egy [UUID] [ 17] objektum.
* `DateTimeOffset updatedAt`: A dátum/idő az utolsó frissítés.  A updatedAt mező a kiszolgáló úgy van beállítva, és az Ügyfélkód soha nem kell beállítani.
* `DateTimeOffset createdAt`: A dátum/idő, az objektumot létrehozták.  A createdAt mező a kiszolgáló úgy van beállítva, és az Ügyfélkód soha nem kell beállítani.
* `byte[] version`: Megszokott módon jelenik meg egy karakterláncot, a verziót is állítja be a kiszolgáló.
* `boolean deleted`: Azt jelzi, hogy a rekord törölve lett, de még nem törlődnek.  Ne használjon `deleted` tulajdonságként a osztályban.

A `id` mező kitöltése kötelező.  A `updatedAt` mező és `version` mező a kapcsolat nélküli szinkronizáláshoz használt (a növekményes szinkronizálás és az ütközés feloldásához rendre).  A `createdAt` mező hivatkozás mező, az ügyfél nem használja.  A nevek tulajdonságainak "között tömörített" nevével és nem módosítható.  Azonban létrehozhat leképezéseket az objektum és a "közötti átvitel közbeni" nevét a között a [gson] [ 3] könyvtárban.  Példa:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Egy tábla hivatkozás létrehozása

Egy tábla szeretne használni, először létre kell hoznia egy [MobileServiceTable] [ 8] meghívásával objektum a **getTable** metódust a [MobileServiceClient][9].  Ez a módszer két túlterheléssel rendelkezik:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

Az alábbi kódban **mClient** a MobileServiceClient objektumra való hivatkozás.  Az első túlterhelési használatos, ha az osztály nevét és a tábla neve azonos, és az egyik használatos a gyors üzembe helyezés:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A második túlterhelési használatos, ha a tábla neve eltér az osztály nevét: az első paramétere a tábla neve.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Egy háttér-táblából

Először szerezze be a táblahivatkozás.  Ezután a táblahivatkozás a lekérdezés végrehajtása.  A lekérdezés bármilyen kombinációját:

* A `.where()` [szűrőfeltétel](#filtering).
* Egy `.orderBy()` [záradék rendelési](#sorting).
* A `.select()` [mező kiválasztása záradék](#selection).
* A `.skip()` és `.top()` a [lapozható eredmények](#paging).

A feltételek a fenti sorrendben kell bemutatni.

### <a name="filter"></a> Szűrési eredmények

Az általános a lekérdezések formátuma:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Az előző példában (akár a kiszolgáló által beállított maximális lapméretét) összes eredményt ad vissza.  A `.execute()` metódus végrehajtja a lekérdezést a háttérkiszolgálón.  A lekérdezés alakítja át egy [OData v3] [ 19] lekérdezés a Mobile Apps-háttéralkalmazás való továbbítás előtt.  Kézhezvétele után a Mobile Apps-háttéralkalmazás alakítja át a lekérdezést egy SQL-utasítás végrehajtása az SQL Azure-példányon előtt.  Mivel a hálózati tevékenységek bizonyos idő, a `.execute()` metódus értéket ad vissza egy [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Visszaadott adatok szűrése

A következő lekérdezés-végrehajtás adja vissza az összes elemet a **ToDoItem** where tábla **teljes** egyenlő **hamis**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** a korábban létrehozott mobilszolgáltatás táblára hivatkozás.

Adja meg a szűrő használatával a **ahol** metódus hívása a táblahivatkozás. A **ahol** metódus követi a **mező** metódus egy metódust, amely meghatározza a logikai predikátum követ. Lehetséges predikátum módszerekre **eq** (egyenlő), **ne** (egyenlő), **gt** (több mint), **ge** (nagyobb vagy egyenlő), **lt** (kevesebb mint), **le** (kisebb vagy egyenlő). Ezek a módszerek lehetővé teszik, hogy összehasonlítja az adott értékek száma és karakterlánc értékű mezőket.

A dátumok végezhet. Az alábbi módszerek lehetővé teszik, hogy a teljes dátum mező vagy a dátum részei összehasonlítása: **év**, **hónap**, **nap**, **óra**, **perc**, és **második**. A következő példa a cikkek szűrőt ad amelynek *határidő* 2013 egyenlő.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Az alábbi módszerek támogatja a speciális szűrők karakterláncmezőket: **megadott módon kezdődő**, **megadott módon végződő**, **concat**, **subString**, **indexOf**, **cserélje le**, **toLower**, **toUpper**, **trim**, és **hossza**. A következő példa szűrőket tábla forrása a *szöveg* oszlopok kezdődő "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

A következő operátor módszerek támogatottak mező: **hozzáadása**, **sub**, **MUL számú**, **div**, **mod**, **emelet**, **felső határ**, és **kerekíteni**. A következő példa szűrőket tábla forrása a **időtartam** páros szám-e.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

A következő logikai módszerekkel kombinálhatja a predikátum: **és**, **vagy** és **nem**. A következő példa az előző példák közül két egyesíti.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Logikai operátorok csoport és kivételblokkokra:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Az ismertető és példákat a szűrés további: [az Android ügyfél lekérdezési modelljét gazdagsága felfedezése][20].

### <a name="sorting"></a>A rendezési adatokat adott vissza

Az alábbi kód táblázatát adja vissza minden elem **ToDoItems** növekvő sorrend által a *szöveg* mező. *mToDoTable* a korábban létrehozott háttér táblára hivatkozás:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Az első paramétere a **orderBy** módszer: egyenlő a rendezéshez használandó mező neve karakterlánc. A második paraméter használja a **QueryOrder** megadhatja, hogy a Rendezés növekvő vagy csökkenő enumerálása.  Szűrése használata esetén a ***ahol*** metódus, a ***ahol*** metódust kell meghívni, mielőtt a ***orderBy*** metódust.

### <a name="selection"></a>Egyes oszlopok kiválasztásához

Az alábbi kód bemutatja a tábla összes elemek visszaállítása **ToDoItems**, de csak azokat jeleníti meg a **teljes** és **szöveg** mezők. **mToDoTable** van a háttér-táblázat, amely korábban létrehozott objektumra mutató hivatkozást.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

A select függvény paraméterei szeretne visszaállítani táblaoszlopok karakterlánc nevét.  A **válasszon** metódust kell hajtsa végre a módszerek, például a **ahol** és **orderBy**. Lapozófájl módszerek, például követhetnek **kihagyása** és **felső**.

### <a name="paging"></a>A lapok visszatérési adatai

Adatok **mindig** lapok adott vissza.  A maximális számú rekordot adott vissza a kiszolgáló állítja be.  Ha az ügyfél több rekordot kéri, a kiszolgáló a maximális számú rekordot ad vissza.  Alapértelmezés szerint a kiszolgálón a maximális méretének 50 rekordok.

Az első példa bemutatja, hogyan válassza ki a felső öt elemek egy táblázatból. A lekérdezés táblázatát adja vissza a cikkek **ToDoItems**. **mToDoTable** a korábban létrehozott háttér táblára hivatkozás:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Lekérdezés, amely kihagyja az első öt elemeket, majd adja vissza a következő öt itt található:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Ha szeretne egy tábla összes rekord beolvasása, az összes lapot ismétlés kód megvalósítását:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Ezzel a módszerrel az összes rekord kérelmet legalább két kérelmet a Mobile Apps-háttéralkalmazás létrehozása

> [!TIP]
> A megfelelő méretének kiválasztásában memória használata közben a kérelem történik, a sávszélesség-használat és a késleltetés az adatok fogadása teljesen közötti egyensúly.  (50 rekordok) alapértelmezés szerint minden eszköz alkalmas.  Ha kizárólag fog működni, a nagyobb memória-eszközökön, növelje a legfeljebb 500.  Találtunk, amelyeknek, amely túl 500 rekordok eredmények elfogadható késések és nagy memóriaproblémák léptek fel a lap méretének növelését.

### <a name="chaining"></a>Hogyan: összefűzésére lekérdezési módszerek

A háttérrendszer táblák lekérdezésére módszerek halmaz zónanevének is lehet. Lekérdezési módszerek láncolás lehetővé teszi a szűrt sorok rendezése és lapozható egyes oszlopok kiválasztásához. Létrehozhat összetett logikai szűrőket.  Minden egyes lekérdezés módszer egy lekérdezés objektumot ad vissza. Az adatsorozat módszerek befejezését, majd futtatja ténylegesen a lekérdezést, hívja az **hajtható végre** metódust. Példa:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

A láncolt lekérdezési módszerek az alábbiak szerint kell rendelni:

1. Szűrés (**ahol**) módszerek.
2. Rendezés (**orderBy**) módszerek.
3. Kijelölés (**válasszon**) módszerek.
4. lapozás (**kihagyása** és **felső**) módszerek.

## <a name="binding"></a>Adatok kötni a felhasználói felületen

Az adatkötés magában foglalja a három összetevővel:

* Az adatforrás
* A kezdőképernyő elrendezésének
* Az adaptert, amely a két kötelékek együtt.

A minta kódban, a rendszer visszaadja a az adatokat a Mobile Apps SQL Azure táblából **ToDoItem** egy tömbbe. Ez a tevékenység az adatok alkalmazások közös mintázatát.  Az adatbázis-lekérdezések gyakran kell visszaadnia sorok, amelyekre az ügyfél megkapta a listában vagy tömb. Ez a példa a tömb az adatforráshoz.  A kód határozza meg a kezdőképernyő elrendezésének dimenzióattribútum megjelenő adatok az eszközön.  A két kötött együtt egy adapternek, amely ezt a kódot a kiterjesztés, a **ArrayAdapter&lt;ToDoItem&gt;**  osztály.

#### <a name="layout"></a>Az elrendezés meghatározása

Az elrendezés XML-kód több kódtöredékek határozzák meg. A következő kódot adott meg egy létező elrendezést, jelöli a **ListView** azt szeretnénk, hogy az adatok feltöltése.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Az előző kódban a *listitem* attribútum az egyes sorok elrendezésének azonosítóját adja meg a listában. Ez a kód egy jelölőnégyzetet és a hozzá tartozó szöveg határozza meg, és egyszer lekérdezi példányosítani a lista minden eleme. Ebben az elrendezésben nem jelennek meg a **azonosító** mező, és olyan összetettebb elrendezést lehet megadni további mezők megjelenítése. Ez a kód szerepel a **row_list_to_do.xml** fájlt.

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Adja meg az adapter
Mivel az adatforrás a nézet tömbje **ToDoItem**, azt alosztály az adapter egy **ArrayAdapter&lt;ToDoItem&gt;**  osztály. Az alosztály egy nézetet hoz létre minden **ToDoItem** használatával a **row_list_to_do** elrendezés.  A kódban, a következő osztály kiterjesztése meghatároztuk a **ArrayAdapter&lt;E&gt;**  osztály:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Bírálja felül az adapterek **getView** metódust. Példa:

```
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

A Microsoft hozzon létre egy példányt az osztály a tevékenység az alábbiak szerint:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

A második paraméter a ToDoItemAdapter konstruktor elrendezését mutató hivatkozás. A Microsoft most példányosítható a **ListView** , és rendelje hozzá a csatoló a **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>A használatára köti a felhasználói felületen

Most már készen áll az adatkötés használatára. A következő kód bemutatja, hogyan beolvasásának a táblázatban, és a helyi adapter beírja a visszaadott elemek.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Hívja a adapter bármikor módosíthatja a **ToDoItem** tábla. Rekord által rekord alapon végzett módosításokat, mert egy gyűjtemény helyett egy sor kezeli. Egy elem beszúrásakor hívja a **hozzáadása** metódus az adapteren; törlésekor, hívja a **eltávolítása** metódus.

A teljes példáját megtalálhatja a [Android Gyorsútmutató-projekt][21].

## <a name="inserting"></a>A háttérrendszer adatok beszúrása

Hozható létre példánya a *ToDoItem* osztály és tulajdonságainak beállítása.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Ezután **az insert()** egy olyan objektum beszúrására:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

A visszaadott entitás megegyezik a háttér táblába beszúrt adatokat tartalmazza, az ID és egyéb értékek (például a `createdAt`, `updatedAt`, és `version` mezők) állítsa be a háttérkiszolgálón.

Mobile Apps táblák szükséges nevű elsődleges kulcsként megadott oszlop **azonosító**. Ez az oszlop karakterláncnak kell lennie. Az azonosító oszlop alapértelmezett értéke egy GUID Azonosítót.  Megadhatja, hogy más egyedi értékeket, például az e-mail címek vagy felhasználónevek. Egy beszúrt bejegyzés nincs megadva érvényes karakterlánc-azonosító értéke, a háttér hoz létre egy új GUID Azonosítót.

Karakterlánc-azonosító érték a következő előnyöket biztosítja:

* Azonosítók generálhatók anélkül, hogy az oda-vissza az adatbázisba.
* Rekordok olyan könnyebben különböző táblákhoz vagy adatbázisok egyesíteni.
* Azonosító értékek jobban integrálható egy alkalmazás logikáját.

Karakterlánc azonosító értékek a következők **REQUIRED** kapcsolat nélküli szinkronizálás támogatásához.  Egy azonosító nem módosítható, ha a háttér-adatbázisban tárolja.

## <a name="updating"></a>A mobilalkalmazások adatok frissítése

A tábla adatainak frissítéséhez adja át az új objektum a **az update()** metódust.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Ebben a példában *elem* sorában hivatkozás a *ToDoItem* táblázat, amely akkor történt változások.  A sor azonos **azonosító** frissül.

## <a name="deleting"></a>A mobilalkalmazások adatok törlése

A következő kód bemutatja, hogyan adatok törléséhez a tábla az objektum megadásával.

```java
mToDoTable
    .delete(item);
```

Egy elem megadásával is törölheti a **azonosító** mezőjét a sor törlése.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Kereshet egy adott cikk azonosítója

Kereshet egy adott cikk **azonosító** mezőt a a **lookUp()** módszert:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Útmutató: a típus nélküli adatok használata

A típus nélküli programozási modell JSON-szerializálás pontos ellenőrzést ad.  Nincsenek olyan gyakori forgatókönyveket tartalmaz, ahol lehetséges, hogy szeretné használni egy típusos programozási modellt. Ha például a háttérbeli tábla sok oszlop szerepel, és csak hivatkoznia kell az oszlopok egy részét.  A beírt modell szükséges, hogy a Mobile Apps-háttérrendszer az adatok osztályban definiált összes oszlopát adja meg.  Az API-hívások adatainak eléréséhez a legtöbb hasonlóak a típusos programozási hívásokat. A fő különbség az, hogy a típus nélküli modell indításakor módszerek a a **MobileServiceJsonTable** objektum, ahelyett, hogy a **MobileServiceTable** objektum.

### <a name="json_instance"></a>A típus nélküli tábla példányt létrehozni

A beírt modell hasonló, akkor indítja el a táblahivatkozás, de ebben az esetben egy **MobileServicesJsonTable** objektum. A hivatkozás beszerzése meghívásával a **getTable** metódus az ügyfél-példány:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Miután létrehozott egy példányát a **MobileServiceJsonTable**, gyakorlatilag az azonos API érhető el, a típusos programozási modellel rendelkezik. Bizonyos esetekben a módszerek Típusos paraméter helyett egy Típusos paraméter igénybe vehet.

### <a name="json_insert"></a>A típus nélküli táblába beszúrása
A következő kód bemutatja, hogyan egy INSERT utasítás végrehajtásához. Az első lépés az, hogy hozzon létre egy [JsonObject][1], amely része a [gson] [ 3] könyvtárban.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Ezt követően **az insert()** a nem típusos objektum beszúrására a táblába.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Ha szeretné az importált objektum Azonosítóját, használja a **getAsJsonPrimitive()** metódust.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>A típus nélküli táblából törlése
A következő kód bemutatja, hogyan kívánja törölni, ebben az esetben ugyanazt a példányát egy **JsonObject** előzetes létrehozott *beszúrása* példa. A kód azonos a típusos eset az, de a metódus aláírása egy másik óta az általa hivatkozott egy **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Azonosítóval segítségével közvetlenül is törölhet egy példányát:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Visszaadja az összes sort típusos táblából
A következő kód bemutatja, hogyan lehet lekérni a teljes táblát. Óta egy JSON-tábla használja, szelektív módon le közül csak az a táblázat oszlopaihoz.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Ugyanazokat a szűrés, szűrést és a lapozás típusos modell felhasználható módszerek közül érhetők el a nem típusos modell.

## <a name="offline-sync"></a>Kapcsolat nélküli szinkronizálás megvalósítása

Az Azure Mobile Apps-ügyfél SDK megvalósít továbbá a kapcsolat nélküli szinkronizálását. SQLite-adatbázis segítségével tárolja a kiszolgáló adatok helyi másolatát.  Egy kapcsolat nélküli táblán végrehajtott műveletek nem igényelnek mobil kapcsolat működjön.  Kapcsolat nélküli szinkronizálás segít az rugalmasság és a teljesítmény rovására ütközésének feloldása összetettebb logikát.  Az Azure Mobile Apps-ügyfél SDK a következő szolgáltatásokat nyújtja:

* Növekményes szinkronizálás: Csak a frissített és új rekordok lesznek letöltve, sávszélesség- és memória-felhasználás mentése.
* Egyidejű hozzáférések optimista: Műveletek feltételezhető, hogy sikeres legyen.  Ütközések feloldása késleltetve mindaddig, amíg a kiszolgáló a frissítések megtörténik.
* Ütközések feloldása: Az SDK-t egy ütköző módosítás történt a kiszolgálón, és a felhasználó figyelmét hurkok biztosít észleli.
* Helyreállítható törlés: A törölt rekordok lesznek megjelölve törölt, így azok offline gyorsítótár frissítése más eszközök.

### <a name="initialize-offline-sync"></a>Kapcsolat nélküli szinkronizálás inicializálása

A kapcsolat nélküli gyorsítótár használat előtt definiálni kell minden egyes offline tábla.  Általában tábladefiníció közvetlenül az ügyfél a létrehozása után történik meg:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>A kapcsolat nélküli gyorsítótár táblájának mutató hivatkozás beszerzése

Egy online tábla használja `.getTable()`.  Egy kapcsolat nélküli tábla használja `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Az online táblák (beleértve a szűrési, rendezési, lapozás, adatok beszúrása, adatok frissítése és adatok törlése) rendelkezésre álló módszerek működőképesek egyaránt online és offline táblákon.

### <a name="synchronize-the-local-offline-cache"></a>A helyi kapcsolat nélküli gyorsítótár szinkronizálása

Az alkalmazás a vezérlőben van a szinkronizálás.  Íme egy példa szinkronizálási módszert:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Ha a lekérdezés nevét megadott a `.pull(query, queryname)` módszer, majd a növekményes szinkronizálás ad vissza, csak azt jelzi, hogy a létrehozott vagy megváltozott a legutóbbi sikeres lekéréses befejeződött.

### <a name="handle-conflicts-during-offline-synchronization"></a>Kapcsolat nélküli szinkronizálás során ütközések kezelésére

Ha ütközés során megtörténik egy `.push()` műveletet, a `MobileServiceConflictException` vált ki.   A kiszolgáló által kiállított elem van ágyazva a kivételt, és lekérhetők `.getItem()` a kivétel miatt.  Állítsa be a leküldéses meghívásával beolvasott MobileServiceSyncContext objektum a következő elemek:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Amennyiben az összes ütközésnél vannak megjelölve kívánja, hívja `.push()` ismét meg kell feloldani az ütközést.

## <a name="custom-api"></a>Egy egyéni API hívása

Egy egyéni API lehetővé teszi, hogy adhatók meg egyéni végpontokat teszi közzé a kiszolgálói funkciót, amely nem egy INSERT utasítás van leképezve, frissítése, törlése, vagy olvasási művelete. Egy egyéni API használatával is befolyásolni további üzenetküldés, beleértve az olvasási és HTTP-üzenet fejlécek beállítása meghatározó JSON nem üzenet törzsének formátumban.

Android-ügyfélről, meghívja a **invokeApi** metódust kell meghívni az egyéni API-végpontot. A következő példa bemutatja, hogyan hívhatja meg az API-végpont nevű **completeAll**, egy gyűjtemény osztályt adja vissza, amely **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

A **invokeApi** metódus lehívásra kerül az ügyfélszámítógépen, amely egy POST kérést küld az új egyéni API-t. Az egyéni API által visszaadott eredmény jelenik meg egy üzenet párbeszédpanelen hibák. Egyéb verziói **invokeApi** lehetővé teszik, hogy választhatóan egy objektum küldését a kérés törzsében, adja meg a HTTP-metódus és lekérdezési paraméterek kérelem küldése. Típus nélküli verziói **invokeApi** is találhatók.

## <a name="authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz

Oktatóanyagok már ismertetik részletesen adja hozzá ezeket a szolgáltatásokat.

Támogatja az App Service [app felhasználók hitelesítéséhez](app-service-mobile-android-get-started-users.md) használatával különböző külső Identitásszolgáltatók: Facebook, a Google, a Microsoft Account, a Twitter és az Azure Active Directory. A engedélyeket korlátozhatja a hozzáférést a megadott művelet csak a hitelesített felhasználók táblákon. Hitelesített felhasználók identitásának használatával valósít meg a háttérrendszer az engedélyezési szabályok.

Két hitelesítési forgalom támogatottak: egy **server** folyamata és a **ügyfél** folyamata. A kiszolgáló folyamata nyújt a legegyszerűbb felhasználói hitelesítés az identitás-szolgáltatók webes felület támaszkodnak.  Nincsenek további SDK-k kiszolgálóhitelesítés folyamat végrehajtásához szükséges. Kiszolgálóhitelesítés folyamata nem biztosít részletes integrálása a mobil eszköz, és csak ajánlott forgatókönyvek koncepció igazolása.

Az ügyféltanúsítvány-folyamat az eszközspecifikus képességekkel bővült, például egyszeri bejelentkezés szorosabb integrációt lehetővé teszi számára, támaszkodnak az SDK-k az identitásszolgáltató által biztosított.  Például a Facebookhoz SDK is integrálható a mobilalkalmazást.  A mobil ügyfél cseréje a Facebook-alkalmazásba, és megerősíti, hogy a bejelentkezés előtt vissza a mobilalkalmazás a csere.

Négy lépést kell az alkalmazás-hitelesítés engedélyezése:

* Hitelesítés az alkalmazás regisztrálása egy identitásszolgáltatóval.
* App Service-háttéralkalmazásának konfigurálása.
* A hitelesített felhasználókhoz csak az App Service-háttérrendszer tábla jogosultságok korlátozása.
* Hitelesítési kód hozzáadása az alkalmazáshoz.

A engedélyeket korlátozhatja a hozzáférést a megadott művelet csak a hitelesített felhasználók táblákon. Egy hitelesített felhasználó biztonsági azonosítója segítségével módosíthatja a kérelem.  További információkért tekintse át a [Bevezetés a hitelesítés használatába] és a kiszolgáló SDK útmutató dokumentációjában talál.

### <a name="caching"></a>Hitelesítési: Server folyamat

A következő kódot a Google-szolgáltató kiszolgáló üzenetkezelési bejelentkezési folyamat elindul.  Nincs szükség további konfigurációra a Google-szolgáltató biztonsági követelmények miatt:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

A fő tevékenységosztállyal emellett hozzáadása a következő metódust:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

A `GOOGLE_LOGIN_REQUEST_CODE` a fő definiált tevékenység szolgál a `login()` metódus és belül a `onActivityResult()` metódust.  Minden egyedi szám, választhat, mindaddig, amíg az azonos számú használják a `login()` metódus és a `onActivityResult()` metódust.  Az Ügyfélkód szolgáltatás kártyához (a korábban bemutatott) tehetik absztrakttá, ha a szolgáltatás adapteren a megfelelő módszereket célszerű hívni.

Szükség customtabs a projekt konfigurálásához.  Először adja meg egy átirányítási URL.  Adja hozzá a következő kódrészletet a `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Adja hozzá a **redirectUriScheme** számára a `build.gradle` fájl az alkalmazáshoz:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Végül adja hozzá `com.android.support:customtabs:23.0.1` a Függőségek listába a `build.gradle` fájlt:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

A bejelentkezett felhasználó az azonosító lekéréséhez a **MobileServiceUser** használatával a **getUserId** metódust. Például egy határidő használata az aszinkron bejelentkezési API-k hívására, lásd: [Bevezetés a hitelesítés használatába].

> [!WARNING]
> Az URL-séma említett a kis-és nagybetűket.  Győződjön meg arról, hogy minden előfordulását `{url_scheme_of_you_app}` nagybetű.

### <a name="caching"></a>A hitelesítési tokenek gyorsítótárazása

A hitelesítési tokenek gyorsítótárazás meg kell tárolni a felhasználói Azonosítót és a hitelesítési jogkivonat helyileg az eszközön. Az alkalmazás következő indításakor, akkor ellenőrizze, hogy a gyorsítótár, és ha ezek az értékek meg adva, a bejelentkezési eljárást kihagyhatja, és az ügyfél és az adatok rehydrate. Azonban ezek az adatok bizalmas, és azt kell tárolni, titkosított biztonsági abban az esetben, ha a telefonszám biztosítása.  Láthatja, hogy a gyorsítótár a hitelesítési tokenek az egy teljes példa bemutatja, hogyan [gyorsítótárazza a hitelesítési tokenek szakaszban][7].

A lejárt tokent használatakor kap egy *nem engedélyezett 401* válasz. Hitelesítési hibák szűrők segítségével kezelheti.  Szűrők intercept az App Service-háttérrendszer kéréseket. A kód teszteli, az a 401-es válasz, a bejelentkezési folyamat elindítja és majd folytatja a kérelemhez, amely a 401-es jön létre.

### <a name="refresh"></a>Használja a frissítési jogkivonatokat

Az Azure App Service hitelesítés és engedélyezés által visszaadott jogkivonatának egy meghatározott élettartama 1 óra.  Ezt követően a felhasználó kell újból hitelesítésre.  Ha egy hosszú élettartamú, ügyfél-folyamat hitelesítési keresztül érkezett, akkor is hitelesítését jogkivonatot használja az Azure App Service hitelesítési és engedélyezési ugyanezt a tokent használ.  Egy másik Azure App Service-jogkivonat új élettartamán jön létre.

Frissítési jogkivonatok használni kívánt szolgáltató is rögzítheti.  A frissítési Token nem mindig rendelkezésre áll.  Nincs szükség további konfigurációra:

* A **Azure Active Directory**, a titkos ügyfélkulcsot konfigurálása az Azure Active Directory-alkalmazás.  Adja meg a titkos ügyfélkulcs az Azure App Service, Azure Active Directory-hitelesítés konfigurálásakor.  Meghívásakor `.login()`, adja át `response_type=code id_token` paramétert:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* A **Google**, adja át a `access_type=offline` paramétert:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* A **Microsoft Account**, jelölje be a `wl.offline_access` hatókör.

Frissítse a jogkivonatot, hívja meg a `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Ajánlott eljárásként hozzon létre egy szűrőt, amely észleli a 401-es választ a kiszolgálóról, és próbálja meg frissíteni a felhasználói jogkivonat.

## <a name="log-in-with-client-flow-authentication"></a>Jelentkezzen be az ügyféltanúsítvány-folyamat hitelesítés

Az ügyféltanúsítvány-folyamat hitelesítési bejelentkezik az általános folyamat a következőképpen történik:

* Azure App Service hitelesítés és engedélyezés konfigurálhatók, mint server-folyamat hitelesítés.
* A hitelesítésszolgáltató egy hozzáférési jogkivonat létrehozásához hitelesítéshez SDK integrálását.
* Hívja a `.login()` módszert az alábbiak szerint:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Cserélje le a `onSuccess()` metódus függetlenül kódját, kíván használni a sikeres bejelentkezés.  A `{provider}` karakterlánca egy érvényes szolgáltatói: **aad** (az Azure Active Directory), **facebook**, **google**, **microsoftaccount**, vagy **twitter**.  Ha egyéni hitelesítési megvalósítását, majd is használhatja az egyéni hitelesítési szolgáltató címkéje.

### <a name="adal"></a>Hitelesíti a felhasználókat a az Active Directory Authentication Library (ADAL)

Az Active Directory Authentication Library (ADAL) segítségével a felhasználók jelentkezzen be az alkalmazás Azure Active Directory használatával. Egy ügyfél folyamata bejelentkezési használata gyakran használata helyett a `loginAsync()` módszerek, mert több natív UX abba biztosít, és lehetővé teszi, hogy további testreszabási.

1. A mobil-háttéralkalmazás számára az AAD-bejelentkezés konfigurálása a következő a [App Service konfigurálása az Active Directory bejelentkezési] [ 22] oktatóanyag. Ügyeljen arra, hogy a natív ügyfélalkalmazás regisztrációján választható lépés elvégzése után.
2. Telepítse az adal-t a build.gradle fájlban a következő definíciókat tartalmazza:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Adja hozzá a következő kódot az alkalmazásról, így az alábbi új:

* Cserélje le **INSERT-SZOLGÁLTATÓ-Itt** nevű, a bérlő, amelyben az alkalmazás üzembe. A következő formátumban kell megadni https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Cserélje le **INSERT-erőforrás-azonosító-Itt** az ügyfél-azonosító a mobil-háttéralkalmazás számára. Ezt úgy szerezheti be az ügyfél-azonosító a **speciális** lap **Azure Active Directory beállításai** a portálon.
* Cserélje le **INSERT-ügyfél-azonosító-Itt** , az ügyfél-Azonosítót a natív ügyfélalkalmazás másolta.
* Cserélje le **INSERT-REDIRECT-URI-Itt** a hellyel való */.auth/login/done* végpont, a HTTPS protokollt használ. Ez az érték a következőképpen kell kinéznie *https://contoso.azurewebsites.net/.auth/login/done*.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Az ügyfél – kiszolgáló kommunikáció beállítása

Az ügyfél kapcsolata általában egy alapvető HTTP-kapcsolat használatával az alapul szolgáló HTTP-könyvtár az Android SDK-val megadott.  Több oka miért szeretné módosítani, amely:

* Egy másik HTTP-könyvtár időtúllépések úgy, hogy használni kívánja.
* Adjon meg egy folyamatjelző kívánja.
* Adjon meg egy egyéni fejlécet az API-felügyeleti funkciók támogatásához kívánja.
* Szeretné intercept sikertelen választ, így újrahitelesítés valósíthat meg.
* Naplózza a háttér-kérelmeket az analitikai szolgáltatás kívánja.

### <a name="using-an-alternate-http-library"></a>Egy másik HTTP-könyvtár használatával

Hívja a `.setAndroidHttpClientFactory()` ügyfél referenciaként a létrehozása után azonnal metódust.  Ha például kapcsolati időkorlát beállításához 60 másodperc (és nem az alapértelmezett 10 másodperc):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>A folyamatban lévő szűrő megvalósítása

Megvalósíthat egy intercept minden kérelem implementálásával egy `ServiceFilter`.  Például a következő frissíti egy előre létrehozott folyamatjelzőt:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Csatolhat a szűrőt az ügyfél az alábbiak szerint:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Kérelemfejléc testreszabása

Használja a következő `ServiceFilter` és csatolja a szűrőt a ugyanúgy, mint a `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Automatikus szerializálási konfigurálása

Megadhat egy átalakítási stratégia segítségével minden egyes oszlophoz alkalmazó a [gson] [ 3] API. Az Android ügyféloldali kódtár által használt [gson] [ 3] Pojo objektumait JSON-adatok szerializálni az adatokat az Azure App Service elküldése előtt a háttérben.  A következő kódban a **setFieldNamingStrategy()** módszer beállítása az stratégia. Ebben a példában a lépéssel törli a kezdeti karaktert ("m"), majd kisbetűs és a következő karakter, minden mező nevét. Például azt szeretné ikonná "left" "id".  Csökkenti _identify_the átalakítás stratégia megvalósításához `SerializedName()` jegyzetek a legtöbb mező.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Ez a kód egy mobil ügyfél hivatkozás segítségével létrehozása előtt kell végrehajtani a **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Bevezetés a hitelesítés használatába]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
