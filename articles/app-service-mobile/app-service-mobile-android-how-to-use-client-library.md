---
title: Az Azure Mobile Apps SDK használata az Android |} A Microsoft Docs
description: Az Azure Mobile Apps SDK használata az Android rendszerhez
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
ms.openlocfilehash: 5052ec73114c040a4c140d258b197fdde58f6667
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409326"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Az Azure Mobile Apps SDK használata az Android rendszerhez

Ez az útmutató bemutatja, hogyan megvalósítása a gyakori forgatókönyvek, például az Android ügyféloldali SDK-t, a Mobile Apps segítségével:

* Lekérdezési adatok (beszúrása, frissítése és törlése).
* A hitelesítés.
* A hibakezelést.
* Az ügyfél testreszabásáról.

Ez az útmutató az Android ügyféloldali SDK összpontosít.  További információ a kiszolgálóoldali SDK-k a Mobile Apps, lásd: [.NET háttérrendszeri SDK használata] [ 10] vagy [használata a Node.js háttérrendszeri SDK][11].

## <a name="reference-documentation"></a>Segédanyagok

Annak a [Javadocs API-referencia] [ 12] az Android ügyféloldali kódtár, a Githubon.

## <a name="supported-platforms"></a>A támogatott platformok

Az Azure Mobile Apps SDK az Android API-szintek 19 – 24 (KitKat Nougat keresztül) a telefon és táblagép méretformátumú támogatja.  Hitelesítés, különösen, használja a webes keretrendszer általánosan használt megközelítés hitelesítő adatok gyűjtéséhez.  Folyamat-kiszolgáló hitelesítési kis képernyőn tényező eszközök, például az órákat nem működik.

## <a name="setup-and-prerequisites"></a>A telepítő és Előfeltételek

Végezze el a [Mobile Apps – első lépések](app-service-mobile-android-get-started.md) oktatóanyag.  Ez a feladat biztosítja, hogy fejlesztése az Azure Mobile Apps minden előfeltétele teljesül-e.  Ez a rövid útmutató segítségével konfigurálhatja a fiókját, és az első Mobile Apps-háttéralkalmazás létrehozása.

Ha úgy dönt, nem a rövid útmutató elvégzéséhez, hajtsa végre a következő feladatokat:

* [a Mobile Apps-háttéralkalmazás létrehozása] [ 13] használata az Android-alkalmazás.
* Az Android Studióban [frissítés a Gradle soubory sestavení](#gradle-build).
* [Engedélyezi az internetes engedély](#enable-internet).

### <a name="gradle-build"></a>Frissítse a Gradle-buildfájlt

Mindkettőt módosíthatja **build.gradle** fájlok:

1. Adja hozzá a kódot a *projekt* szint **build.gradle** belül fájlt a *buildscript* címkét:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Adja hozzá a kódot a *modul alkalmazás* szint **build.gradle** belül fájlt a *függőségek* címkét:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    A legújabb verzió jelenleg 3.4.0. A támogatott verziók láthatók [bintrayen][14].

### <a name="enable-internet"></a>Internetes engedély engedélyezése

Hozzáférhet az Azure, az alkalmazás engedélyezve van az INTERNET engedéllyel kell rendelkeznie. Ha még nincs engedélyezve, adja hozzá a következő kódsort a **AndroidManifest.xml** fájlt:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Ügyfélkapcsolat létrehozása

Az Azure Mobile Apps biztosítja a mobilalkalmazásban négy függvény:

* Adathozzáférés és -kapcsolat nélküli szinkronizálás az Azure Mobile Apps szolgáltatással.
* Az Azure Mobile Apps Server SDK-készült egyéni API-jainak hívására.
* Hitelesítés az Azure App Service-hitelesítés és engedélyezés.
* Leküldéses értesítés regisztrálása a Notification hubs használatával.

Ezek a függvények mindegyike először létre kell hoznia meg egy `MobileServiceClient` objektum.  Csak egy `MobileServiceClient` objektumot kell létrehozni a mobilügyfél belüli (vagyis nem lehet egy Singleton-mintát).  Hozhat létre egy `MobileServiceClient` objektum:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

A `<MobileAppUrl>` egy karakterlánc vagy egy URL-cím-objektumot, amely a mobil háttérszolgáltatás használatára.  Ha az Azure App Service-ben a mobil háttérszolgáltatásban üzemeltetésére használ, majd győződjön meg arról, a biztonságos `https://` az URL-cím verziója.

Az ügyfél emellett a tevékenység vagy környezet - hozzáférésre van szüksége a `this` a példa a paraméter.  A MobileServiceClient konstrukció belül történjen a `onCreate()` metódus a tevékenység hivatkozik a `AndroidManifest.xml` fájlt.

Ajánlott eljárásként meg kell absztrakt kiszolgálói kommunikációhoz a saját (singleton-mintát) osztályba.  Ebben az esetben kell átadnia a szolgáltatás megfelelő konfigurálásához a konstruktor belül a tevékenység.  Példa:

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

Most már hívása `AzureServiceAdapter.Initialize(this);` a a `onCreate()` metódus a fő tevékenység.  Hozzáférnének az ügyfél használja más módon `AzureServiceAdapter.getInstance();` , szerezzen be egy hivatkozást a service adapter.

## <a name="data-operations"></a>Adatműveletek

Az Azure Mobile Apps SDK középpontjában az SQL Azure belül a Mobile Apps-háttéralkalmazás tárolt adatokhoz hozzáférést biztosítania.  Hozzáférnének ehhez ezen adatokhoz (preferált) erősen típusos osztály használatával, vagy a típus nélküli lekérdezések (nem ajánlott).  Ez a szakasz a nagy foglalkozik, erősen típusos osztály használatával.

### <a name="define-client-data-classes"></a>Ügyfél adatok definiálására

SQL Azure táblákból származó adatok eléréséhez ügyfél adatok definiálására, amelyek megfelelnek a Mobile Apps-háttéralkalmazás tábláihoz. Ebben a témakörben szereplő példák feltételezik nevű tábla **MyDataTable**, amely rendelkezik a következő oszlopokat:

* id
* szöveg
* Hajtsa végre

A megfelelő típusos ügyféloldali objektum nevű fájlban található **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adja hozzá az egyes mezőkhöz, adja hozzá elérő és beállító metódusokat.  Ha az SQL Azure-tábla több oszlopot tartalmaz, akkor mezőket ad hozzá a megfelelő Ez az osztály.  Például ha a dto-t (adatátviteli objektumot) kellett egy egész számot a Prioritás oszlop, akkor ez a mező a Getter a Setter se módszerei felveheti:

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

Megtudhatja, hogyan hozhat létre további táblákat a Mobile Apps-háttéralkalmazást, lásd: [hogyan: Adja meg a táblavezérlőnek] [ 15] (.NET-háttérrendszer) vagy [használata a dinamikus sémák definiálása táblák] [ 16] (Node.js-háttérrendszer).

Az Azure Mobile Apps háttértáblára öt speciális mezők érhetők el az ügyfeleknek, amelyek négy határozza meg:

* `String id`: A rekord globálisan egyedi azonosítója.  Ajánlott eljárásként, győződjön meg arról, az azonosítója a karakterláncos leképezését egy [UUID] [ 17] objektum.
* `DateTimeOffset updatedAt`: A dátum/idő az utolsó frissítés.  A updatedAt mező a kiszolgáló úgy van beállítva, és az Ügyfélkód soha nem kell beállítania.
* `DateTimeOffset createdAt`: A dátum/idő, hogy az objektumot létrehozták.  A createdAt mező a kiszolgáló úgy van beállítva, és az Ügyfélkód soha nem kell beállítania.
* `byte[] version`: Általában jelölt karakterlánc, a verzió is állítja be a kiszolgáló.
* `boolean deleted`: Azt jelzi, hogy a rekord törölve lett, de még nem lett kiürítve.  Ne használjon `deleted` tulajdonságként az osztályban található.

Az `id` mező kötelező.  A `updatedAt` mező és `version` mező kapcsolat nélküli szinkronizálás használhatók (a növekményes szinkronizálás és az ütközés feloldásához jelölik).  A `createdAt` mező egy referencia-mezőt, és az ügyfél nem használja.  A neveket a Tulajdonságok "közötti átvitel közbeni" nevei, és nem állítható.  Azonban létrehozhat egy leképezési az objektum és a "közötti átvitel közbeni" neveket között a [gson] [ 3] könyvtár.  Példa:

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

### <a name="create-a-table-reference"></a>Hozzon létre egy táblahivatkozás

Egy tábla eléréséhez, először hozzon létre egy [MobileServiceTable] [ 8] meghívásával objektum a **getTable** metódust a [MobileServiceClient] [9].  Ez a módszer két túlterheléssel rendelkezik:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

Az alábbi kódban **mclient elemet** a MobileServiceClient objektumra hivatkozik.  Az első túlterhelési használatos, ahol az osztály nevét és a táblázat neve azonosak, és a egy használja a rövid útmutatóban:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A második túlterhelés szolgál, ha a tábla neve eltér az osztálynév: az első paraméter az a tábla neve.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Egy háttér-táblából

Először szerezze be a táblahivatkozást.  Ezután hajtsa végre a lekérdezést a táblahivatkozáson.  A lekérdezés bármely kombinációja:

* A `.where()` [szűrőfeltételt](#filtering).
* Egy `.orderBy()` [záradék rendezése](#sorting).
* A `.select()` [mező kiválasztása záradék](#selection).
* A `.skip()` és `.top()` a [lapozható eredmények](#paging).

A feltételek a fenti sorrendben kell bemutatni.

### <a name="filter"></a> Eredmények szűrése

Az általános űrlap a lekérdezés a következő:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Az előző példában (akár a maximális méretének beállítása a kiszolgáló) minden eredményt visszaad.  A `.execute()` metódus hajtja végre a lekérdezést a háttérkiszolgálón.  A lekérdezés alakítja át egy [OData v3] [ 19] lekérdezést, mielőtt a Mobile Apps háttéralkalmazásból továbbítását.  Kézhezvétele után a Mobile Apps háttéralkalmazásból alakítja át a lekérdezést egy SQL-utasítás előtt futtassa a jelentést az SQL Azure-példányon.  Hálózati tevékenység eltarthat egy ideig, mivel a `.execute()` metódus adja vissza egy [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Visszaadott adatok szűrése

A következő lekérdezés végrehajtása az összes elemet adja vissza a **ToDoItem** where tábla **teljes** egyenlő **hamis**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** a hivatkozás a korábban létrehozott mobilszolgáltatás táblába.

Adja meg a szűrő használatával a **ahol** metódus hívása a táblahivatkozáson. A **ahol** metódus követ egy **mező** módszer olyan módszer, amely meghatározza a logikai predikátum követ. Lehetséges predikátum módszerekre **eq** (egyenlő) **ne** (nem egyenlő), **gt** (nagyobb), **ge** (nagyobb vagy egyenlő) **lt** (kevesebb mint), **le** (kisebb vagy egyenlő). Ezek a módszerek segítségével megadott értékek száma és a karakterlánc mezők összehasonlítása.

Dátumok szűrésével. Az alábbi módszerek lehetővé teszik a teljes dátum mező vagy a dátum részei összehasonlítása: **év**, **hónap**, **nap**, **óra**,  **perc**, és **második**. Az alábbi példa hozzáad egy szűrő elemek amelynek *határideje* 2013 egyenlő.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Az alábbi módszereket támogatja a összetett szűrőket a karakterlánc-mezők: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **cseréje**, **toLower**, **toUpper**, **trim**, és **hossza** . A következő példa szűrők tábla sorok, ahol a *szöveg* oszlop kezdődik "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

A mező a következő operátor módszerek támogatottak: **hozzáadása**, **sub**, **MUL számú**, **div**, **mod**, **emelet**, **felső határ**, és **kerekíteni**. A következő példa szűrők tábla sorok, ahol a **időtartama** páros szám.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

A következő logikai módszerekkel kombinálhatja predikátumok: **és**, **vagy** és **nem**. Az alábbi példa az előző példák közül két egyesíti.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Csoport és a beágyazott logikai operátor:

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

Részletes leírását és példák a szűrés, lásd: [felfedezése az Android ügyfél lekérdezési modellje gazdagsága][20].

### <a name="sorting"></a>Visszaadott adatok rendezése

A következő kódot az összes elem visszaadása egy táblából a **ToDoItems** szerint növekvő sorrendbe rendezve a *szöveg* mező. *mToDoTable* van, amelyet korábban hozott létre a háttértáblára mutató hivatkozást:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Az első paraméterként a **orderBy** módszer a rendezéshez használandó mező neve megegyezik egy karakterláncot. A második paraméter használja a **QueryOrder** enumerálás adhatja meg, hogy a növekvő vagy csökkenő rendezéshez.  Szűrése használata esetén a ***ahol*** metódus, a ***ahol*** metódust kell hívni a előtt a ***orderBy*** metódust.

### <a name="selection"></a>Egyes oszlopok kiválasztásához

Az alábbi kód bemutatja, hogyan minden elem visszaadása egy táblából a **ToDoItems**, csak jeleníti meg, de a **teljes** és **szöveg** mezőket. **mToDoTable** van a korábban létrehozott háttértáblára mutató hivatkozást.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

A select függvény paraméterei a következők a táblázat oszlopaiba, amelyet vissza szeretne karakterlánc nevét.  A **kiválasztása** metódus szükséges módszerek, például **ahol** és **orderBy**. Lapozófájl módszerek, például követhetnek **kihagyása** és **felső**.

### <a name="paging"></a>Az oldalak adatokat adja vissza

Adatok **mindig** oldalakat adja vissza.  A visszaadott rekordok maximális száma a kiszolgáló állítja be.  Ha az ügyfél több rekordot kér, a kiszolgáló rekordok maximális számát adja vissza.  Alapértelmezés szerint a maximális méretének a kiszolgálón a 50 rögzíti.

Az első példa bemutatja, hogyan válassza ki a felső öt elemeket egy táblából. A lekérdezés tábláját adja vissza a cikkek **ToDoItems**. **mToDoTable** van, amelyet korábban hozott létre a háttértáblára mutató hivatkozást:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

A következő lekérdezés, amely kihagyja az első öt elemeket, majd visszaadja a következő öt:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Ha szeretne egy tábla összes rekord lekérése, az összes ciklustevékenység kód megvalósítását:

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

Ezzel a módszerrel minden rekordnál kérelmet hoz létre, legalább két kérelmet, a Mobile Apps háttéralkalmazásból.

> [!TIP]
> A megfelelő méretének kiválasztása a memóriahasználat, amíg a kérelem történik, a sávszélesség-használat és a késleltetés az adatok fogadása teljesen közötti egyensúly.  (50 rekordokat) alapértelmezés szerint minden eszköz alkalmas.  Ha kizárólag a nagyobb memória eszközökön, akár 500 növelése  Találtunk, amely a növekvő az oldalméret túl az elfogadhatatlan késéseket és nagy memóriaigényű problémák 500 rekord eredményez.

### <a name="chaining"></a>kézikönyv: Lekérdezési módszerek összefűzése

A háttérrendszer táblákat kérdezi le a használt módszerek is kell fűzni. Lekérdezési módszerek láncolási lehetővé teszi a szűrt sorok rendezve és lapozható egyes oszlopok kiválasztásához. Összetett logikai szűrőket hozhat létre.  Minden egyes lekérdezés módszer egy lekérdezés objektummal adja vissza. A sorozat módszerek befejezését, majd futtatja ténylegesen a lekérdezést, hívja a **végrehajtása** metódus. Példa:

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

A kapcsolt lekérdezési módszerek módon rendelnünk kell:

1. Szűrés (**ahol**) módszereket.
2. Rendezés (**orderBy**) módszereket.
3. Kijelölés (**kiválasztása**) módszereket.
4. lapozófájl (**kihagyása** és **felső**) módszereket.

## <a name="binding"></a>Adatok kötést létrehozni a felhasználói felület

Datová vazba három összetevőből áll:

* Az adatforrás
* Az elrendezés
* Az adapter, amely a két összeköt együtt.

A mintakód azt állítja vissza az adatokat a Mobile Apps SQL Azure-tábla **ToDoItem** egy tömbbe. Ezt a tevékenységet, gyakori minta adatokat kezelő alkalmazásokhoz.  Az adatbázis-lekérdezések gyakran a sorokat, amelyek az ügyfél lekéri a listában, vagy a tömb gyűjteményét adja vissza. Ebben a példában a Pole je zdrojem dat.  A kód meghatároz egy elrendezés, amely meghatározza az adatok, amely akkor jelenik meg a nézet az eszközön.  A kettő együtt egy adapternek, amely ebben a kódban kiterjesztése vannak kötve, a **ArrayAdapter&lt;ToDoItem&gt;**  osztály.

#### <a name="layout"></a>Az elrendezés

Az elrendezés XML-kódot néhány kódrészleteket határozza meg. Adja meg egy létező elrendezést, a következő kód jelöli a **ListView** szeretnénk adja meg a kiszolgáló adatait.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Az a fenti kóddal a *listitem* attribútum az elrendezést az egyes sorok azonosítóját adja meg a listában. Ez a kód egy jelölőnégyzet és a hozzá tartozó szöveg adja meg, és egyszer lekérdezi példányosítása a lista minden eleme. Ez az elrendezés nem jelennek meg a **azonosító** mezőben, és a egy összetettebb elrendezés kellene megadnia további mezőket a megjelenő információk. Ez a kód szerepel a **row_list_to_do.xml** fájlt.

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

#### <a name="adapter"></a>Az adapter meghatározása
Mivel a nézet az adatforrás egy tömbjét **ToDoItem**, hogy alosztályát az adapter egy **ArrayAdapter&lt;ToDoItem&gt;**  osztály. Ez alosztályát egy nézetet hoz létre minden **ToDoItem** használatával a **row_list_to_do** elrendezését.  A kódban, a következő osztály, amely a kiterjesztése meghatározzuk a **ArrayAdapter&lt;E&gt;**  osztály:

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

Létrehozunk Ez az osztály egy példányát a tevékenység a következőképpen:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

A második paraméter a ToDoItemAdapter konstruktor eszköztáblára mutató hivatkozás az elrendezést. Most már példányosítható, hogy a **ListView** és rendelje hozzá az adapteren, hogy a **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Az Adapter kötést létrehozni a felhasználói felület használata

Most már készen áll adatkötés használatára. A következő kód bemutatja, hogyan elemek beolvasása a táblázatban, és a helyi adapter beírja a visszaadott elemek.

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

A kártya hívása bármikor módosíthatja a **ToDoItem** tábla. A módosítások rekord által rekord alapon történik, mivel kezeli a egyetlen sor egy gyűjtemény helyett. Ha egy elem, hívja a **hozzáadása** metódus az adapteren; törlésekor, hívja a **eltávolítása** metódus.

A teljes példát talál a [Android Gyorsútmutató-projekt][21].

## <a name="inserting"></a>Adatok beszúrása a háttérrendszer

Egy példányát hozza létre a *ToDoItem* osztályt, és a tulajdonságainak beállításával.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Ezután **insert()** egy olyan objektum beszúrására:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

A visszaadott entitás egyezések a háttérrendszer táblába beszúrt adatokat tartalmaz, az Azonosítót és az egyéb értékek (például a `createdAt`, `updatedAt`, és `version` mezők) a háttérkiszolgáló beállítása.

A Mobile Apps táblák szükséges nevű elsődleges kulcsként megadott oszlop **azonosító**. Ez az oszlop karakterláncnak kell lennie. Az azonosító oszlop alapértelmezett értéke egy GUID Azonosítót.  Megadhat más egyedi értékeket, például az e-mail-címeket vagy a felhasználónevek egyeztetéséhez. Egy karakterláncértéket azonosítója nincs megadva egy beszúrva rekord, a háttérrendszer hoz létre egy új GUID Azonosítót.

Karakterlánc-azonosító értéket adja meg a következő előnyökkel jár:

* Anélkül, hogy oda-vissza az adatbázis azonosítót lehet létrehozni.
* Rekordok használata egyszerűbb, egyesítheti a különböző táblák vagy adatbázisok.
* Azonosító értékek jobban integrálható az alkalmazás logikáját.

Karakterlánc-azonosító értékek a következők **kötelező** az offline szinkronizálás támogatásához.  Egy azonosító nem módosítható, ha a háttér adatbázis található.

## <a name="updating"></a>A mobilalkalmazás adatainak frissítéséhez

Frissíti egy táblában lévő adatokat, át kell adnia az új objektum a **update()** metódust.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Ebben a példában *elem* eszköztáblára mutató hivatkozás egy sort a *ToDoItem* táblában, amely azt változások volt-e.  A sor azonos **azonosító** frissül.

## <a name="deleting"></a>A mobilalkalmazás adatainak törlése

A következő kód bemutatja, hogyan törli az adatokat egy táblából az objektum megadásával.

```java
mToDoTable
    .delete(item);
```

Egy elem megadásával is törölheti a **azonosító** a törlendő sor mezőjében.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Kereshet meg egy adott elem azonosítója

Kereshet meg egy adott elemet **azonosító** mezőt a a **lookUp()** módszer:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>kézikönyv: Típusos adatok használata

A típus nélküli programozási modell révén, JSON-szerializálás pontosan szabályozhatja.  Van néhány gyakori forgatókönyvet, ahol érdemes egy típusos programozási modellt használja. Ha például a háttértáblára sok oszlopot tartalmaz, és csak hivatkoznia kell az oszlopok egy része.  A beírt modell megköveteli, hogy az adatok osztály a Mobile Apps háttéralkalmazásból definiált összes oszlopát adja meg.  Az API-hívások adatainak elérése többsége hasonló gépelt programozási hívásnak. A fő különbség az, hogy a nem típusos modellben, metódusokat hívhat meg, a **MobileServiceJsonTable** -objektum helyett a **MobileServiceTable** objektum.

### <a name="json_instance"></a>Hozzon létre egy példányt egy típusos táblázat

A beírt modell hasonló, akkor úgy indítja el a táblahivatkozást, de ebben az esetben egy **MobileServicesJsonTable** objektum. A hivatkozás beszerzése meghívásával a **getTable** metódus az ügyfél egy példányát:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Miután létrehozott egy példányát a **MobileServiceJsonTable**, gyakorlatilag az azonos API elérhető gépelt programozási modell rendelkezik. Bizonyos esetekben a módszerek igénybe egy Típusos paraméter helyett egy típusos paraméterek.

### <a name="json_insert"></a>A típus nélküli táblába INSERT
A következő kód bemutatja, hogyan teheti a Beszúrás. Az első lépés az, hogy hozzon létre egy [JsonObject][1], amely része a [gson] [ 3] könyvtár.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Ezután használja **insert()** a típus nélküli objektum beszúrni a táblába.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Ha az importált objektum Azonosítójának lekéréséhez van szüksége, használja a **getAsJsonPrimitive()** metódust.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Egy nem típusos tábla törlése
A következő kód bemutatja, hogyan törölhet egy példányt, ebben az esetben példányát egy **JsonObject** előzetes hozták létre, amely *beszúrása* példa. A kód, mint a beírt eset az, de a metódus egy különböző aláírással rendelkezik, mivel az általa hivatkozott egy **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Egy példány által közvetlenül az azonosítójával is törölheti:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Az összes sor visszaadása egy típusos táblából
A következő kód bemutatja, hogyan kérheti le a teljes táblát. Egy JSON-táblát használ, mivel külön-külön kérheti le a táblázat oszlopainak csak néhányat.

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

Ugyanazokat a szűrést, szűrés és a beírt modellben elérhető módszerek lapozás érhetők el a nem típusos modell.

## <a name="offline-sync"></a>Offline szinkronizálás megvalósítása

Az Azure Mobile Apps ügyféloldali SDK kapcsolat nélküli szinkronizálás az adatok is SQLite-adatbázis segítségével tárolja a kiszolgáló adatainak helyi másolatát valósítja meg.  Egy kapcsolat nélküli táblán végrehajtott műveletek mobil kapcsolat működéséhez nem szükséges.  Kapcsolat nélküli szinkronizálás – célszerű a rugalmasság és teljesítmény rovására összetettebb logika ütközésfeloldáshoz.  Az Azure Mobile Apps-ügyfél SDK a következő funkciókat tartalmazza:

* Növekményes szinkronizálás: Csak frissített és új rekordok lesznek letöltve, sávszélesség-és memóriafelhasználás mentése.
* Optimista párhuzamosság: Műveletek rendszer feltételezi, hogy sikeres legyen.  Ütközésfeloldás van halasztott mindaddig, amíg a frissítéseket a kiszolgálón történik.
* Ütközések feloldása: Az SDK-t észleli, ha egy ütköző módosítás történt a kiszolgálón, és biztosít beavatkozási pontjainak figyelmezteti a felhasználót.
* Helyreállítható törlés: A törölt rekordok törölt, így azok offline gyorsítótár frissítése más eszközök lesznek megjelölve.

### <a name="initialize-offline-sync"></a>Kapcsolat nélküli szinkronizálás – inicializálása

Minden offline tábla az offline gyorsítótárban használata előtt definiálni kell.  Tábla definíciójának normális esetben az ügyfél létrehozása után azonnal történik:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Szerezzen be egy hivatkozást az Offline gyorsítótár táblájának

Egy online tábla használja `.getTable()`.  Egy kapcsolat nélküli tábla használja `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Online táblák (beleértve a szűrési, rendezési, lapozás, adatok beszúrása, adatok frissítése és adatok törlése) elérhető összes módszer jól működik egyaránt azokon a táblákon, online és offline állapotban van.

### <a name="synchronize-the-local-offline-cache"></a>A helyi Offline gyorsítótár szinkronizálása

Szinkronizálás az alkalmazás irányítása alatt van.  Íme egy példa szinkronizálási módszert:

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

Ha a lekérdezés nevét megadva, hogy a `.pull(query, queryname)` módszer, majd a növekményes szinkronizálás szolgál visszatérhet, csak a rekordok létrehozott, vagy az utolsó óta megváltozott sikeresen befejeződött a lekéréses.

### <a name="handle-conflicts-during-offline-synchronization"></a>Offline szinkronizálás során az ütközések kezelése

Ütközés során történik, ha egy `.push()` művelet egy `MobileServiceConflictException` fordul elő.   A kiszolgáló által kiállított elem van ágyazva a kivételt, és lekérheti `.getItem()` a kivételt.  Állítsa be a leküldéses a MobileServiceSyncContext objektum a következő elemek meghívásával:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Miután az összes ütközésnél igény szerint vannak megjelölve, hívja `.push()` , újra feloldani az ütközést.

## <a name="custom-api"></a>Egyéni API hívása

Egyéni API-k lehetővé teszi, hogy meghatározza az egyedi végpontok, amelyek teszik közzé a kiszolgálói funkciót, amely nem egy insert leképezése, frissítése, törlése vagy olvasási művelete. Egyéni API-k használatával rendelkezhet több felügyeleti üzenetkezelés, beleértve olvasása és HTTP-üzenet fejlécek beállítása és eltérő JSON üzenet törzse formátum meghatározása.

Android-ügyfélről, hívja a **invokeApi** metódus az egyéni API-végpont meghívására. Az alábbi példa bemutatja, hogyan hívhat meg egy API-végpont nevű **completeAll**, egy gyűjtemény osztályt adja vissza, amely **MarkAllResult**.

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

A **invokeApi** módszert hívja meg az ügyfélen, amely egy POST kérést küld az új egyéni API-t. Az egyéni API-k által visszaadott eredmény is megjelenik egy üzenet párbeszédpanelen esetleges hibák. Egyéb verziói **invokeApi** segítségével igény szerint a kérelem törzsében szereplő objektum küldése, adja meg a HTTP-metódus és lekérdezési paraméterek a kérés küldése. Típus nélküli verziói **invokeApi** is biztosított.

## <a name="authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz

Az oktatóanyagok már részletesen történő hozzáadását ismertetik ezeket a funkciókat.

Az App Service támogatja [. alkalmazás felhasználóinak hitelesítése](app-service-mobile-android-get-started-users.md) különböző külső identitásszolgáltató használatával: Facebook, Google, Microsoft-fiókkal, Twitter, és az Azure Active Directoryban. Beállíthatja, hogy a engedélyeit azokon a táblákon, az adott műveletek csak a hitelesített felhasználók a hozzáférés korlátozásához. Az engedélyezési szabályok megvalósításához a háttérszolgáltatásban is használhatja a hitelesített felhasználók identitását.

Két hitelesítési folyamatok támogatottak: egy **kiszolgáló** folyamat és a egy **ügyfél** folyamat. A server flow nyújt a legegyszerűbb felhasználói hitelesítés támaszkodik a identity providers webes felületén.  Nincsenek további SDK-k megvalósításához server flow-hitelesítés szükséges. Kiszolgálóhitelesítés folyamat nem biztosít a mély integráció, a mobil eszköz, és csak ajánlott forgatókönyvek koncepció igazolása.

A client flow lehetővé teszi mélyebb integrációjuk eszközspecifikus képességek, például egyszeri bejelentkezés, SDK-k az identitásszolgáltató által biztosított támaszkodik.  Például a Facebook SDK is integrálja a mobilalkalmazást.  A mobil ügyfelekből felcseréli a Facebook-alkalmazásba, és megerősíti, hogy a bejelentkezés előtt érvényesítheti a mobilalkalmazásnak.

Négy lépések szükségesek az alkalmazás-hitelesítés engedélyezéséhez:

* Regisztrálja az alkalmazást a hitelesítést egy identitásszolgáltatóval.
* Konfigurálja az App Service-ben háttérszolgáltatás használatára.
* Az App Service háttérrendszer csak a hitelesített felhasználók tábla engedélyeinek korlátozása.
* Hitelesítési kód hozzáadása az alkalmazáshoz.

Beállíthatja, hogy a engedélyeit azokon a táblákon, az adott műveletek csak a hitelesített felhasználók a hozzáférés korlátozásához. A hitelesített felhasználó SID használatával módosíthatja a kérelmeket.  További információkért tekintse át a [hitelesítés első lépései] és a kiszolgáló SDK HOWTO dokumentációt.

### <a name="caching"></a>Hitelesítés: Server Flow

A következő kódot a Google-szolgáltató kiszolgáló flow bejelentkezési folyamat elindul.  A Google-szolgáltató biztonsági követelmények miatt további konfigurálására szükség:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Emellett a fő tevékenységosztállyal adja hozzá a következő metódust:

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

A `GOOGLE_LOGIN_REQUEST_CODE` meghatározott a fő tevékenység szolgál a `login()` metódus és belül a `onActivityResult()` metódus.  Mindaddig, amíg az azonos számú belül használt bármilyen egyedi szám, kiválaszthatja a `login()` módszer és a `onActivityResult()` metódust.  Egy szolgáltatás kártyához, (, ahogy ezt korábban) absztrakt, az Ügyfélkód, a megfelelő módszerek a szolgáltatás-adapteren kell hívjuk.

Azt is konfigurálnia kell a customtabs projektet.  Először adja meg egy átirányítási URL-címet.  Adja hozzá a következő kódrészletet `AndroidManifest.xml`:

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

Adja hozzá a **redirectUriScheme** , a `build.gradle` fájlt az alkalmazás:

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

Végül adja hozzá `com.android.support:customtabs:23.0.1` a függőségek listájához a `build.gradle` fájlt:

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

Szerezze be a bejelentkezett felhasználó Azonosítóját egy **MobileServiceUser** használatával a **getUserId** metódust. Határidő használata az aszinkron bejelentkezési API-k hívásához egy példa: [hitelesítés első lépései].

> [!WARNING]
> Az említett URL-sémát a kis-és nagybetűket.  Ügyeljen arra, hogy minden előfordulását `{url_scheme_of_you_app}` és nagybetűk megkülönböztetése.

### <a name="caching"></a>Hitelesítési jogkivonatok gyorsítótárazása

Hitelesítési jogkivonatok gyorsítótárazása megköveteli, hogy tárolja a felhasználói Azonosítót és a hitelesítési jogkivonat helyileg az eszközön. Az alkalmazás következő indításakor, ellenőrizze, hogy a gyorsítótárban, és ezek az értékek jelen, ha a bejelentkezési eljárást kihagyhatja, és az ügyfél és az adatok rehidratálási. Azonban az adatok bizalmas, és kell tárolni, abban az esetben a telefon ellopják biztonsági titkosítva lesznek.  Láthatja, hogy a gyorsítótár hitelesítési tokenek az egy teljes példát bemutatja, hogyan [gyorsítótárazzák a hitelesítési tokenek szakaszban][7].

A lejárt tokent használatakor kap egy *401 – jogosulatlan hibaüzenetet* választ. Ezekkel a szűrőkkel hitelesítési hibák kezelésére is alkalmas.  Szűrők intercept az App Service háttérrendszer kérelmeket. A szűrő kód teszteli a 401-es válasz, a bejelentkezési folyamatot aktivál, és majd folytatja a kérelemhez, amely a 401-es generált.

### <a name="refresh"></a>Frissítési biztonsági jogkivonat használata

Az Azure App Service-hitelesítés és engedélyezés által visszaadott tokent rendelkezik egy meghatározott élettartam egy óra.  Ez az időtartamnak az elteltével a felhasználó kell újból hitelesítésre.  Ha hosszú élettartamú jogkivonat, amely client flow hitelesítési keresztül kapott, és hitelesítse magát újra is használja az Azure App Service-hitelesítés és engedélyezés használatával ugyanezt a tokent.  Egy új élettartamát az Azure App Service egy másik jogkivonatot jön létre.

Frissítési jogkivonatok használni kívánt szolgáltató is rögzítheti.  A Token frissítése nem érhető mindig.  További konfigurációra szükség:

* A **Azure Active Directory**, ügyfélkódot konfigurálása az Azure Active Directory-alkalmazás.  Adja meg az ügyfél titkos kulcsát az Azure App Service konfigurálása Azure Active Directory-hitelesítés során.  Hívásakor `.login()`, pass `response_type=code id_token` paramétert:

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

A jogkivonat frissítéséhez hívja `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Ajánlott eljárásként hozzon létre egy szűrőt, amely észleli a 401-es választ a kiszolgálóról, és próbálja meg frissíteni a felhasználói jogkivonat.

## <a name="log-in-with-client-flow-authentication"></a>Folyamat ügyfél-hitelesítési bejelentkezés

A client flow hitelesítési bejelentkezik általános folyamata a következőképpen történik:

* Az Azure App Service-hitelesítés és engedélyezés konfigurálhatók, mint folyamat server-hitelesítés.
* Integrálhatja a hitelesítési szolgáltató SDK-t a hitelesítéshez szükséges hozzáférési jogkivonat.
* Hívja a `.login()` módszert az alábbiak szerint (`result` kell lennie egy `AuthenticationResult`):

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

A teljes kód példa a következő szakaszban talál.

Cserélje le a `onSuccess()` metódust bármilyen kódot szeretne használni a sikeres bejelentkezés.  A `{provider}` karakterlánc egy érvényes szolgáltatói: **aad** (az Azure Active Directory), **facebook**, **google**, **microsoftaccount**, vagy **twitter**.  Ha egyéni hitelesítési valósította meg, majd is használhatja az egyéni hitelesítési szolgáltató címkét.

### <a name="adal"></a>Hitelesítheti a felhasználókat a az Active Directory Authentication Library (ADAL)

Az Active Directory Authentication Library (ADAL) segítségével bejelentkezhetnek a felhasználók az alkalmazásokba az Azure Active Directoryval. Az ügyfél-flow bejelentkezés a következő gyakran használata helyett a `loginAsync()` módszerek, mert több natív UX betekintést nyújt, és lehetővé teszi, hogy további testreszabási.

1. A mobil-háttéralkalmazás az AAD-bejelentkezés konfigurálása a következő a [konfigurálása App Service-ben az Active Directory-bejelentkezés] [ 22] oktatóanyag. Ellenőrizze, hogy a natív ügyfélalkalmazás regisztrációja nem kötelező lépése.
2. Telepítse az adal-t módosításával a build.gradle fájllal, hogy az alábbi definíciókat tartalmazza:

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

3. Az alkalmazását, így a következő cseréjére adja hozzá a következő kódot:

    * Cserélje le **INSERT-SZOLGÁLTATÓ – Itt** , amelyben az alkalmazás kiosztása a bérlő nevével. A formátum https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Cserélje le **INSERT-erőforrás-azonosító – Itt** az ügyfél-Azonosítóját a mobile Apps-háttéralkalmazást. Az ügyfél-Azonosítót a szerezheti be a **speciális** lapjára **Azure Active Directory-beállítások** a portálon.
    * Cserélje le **INSERT-ügyfél-azonosító – Itt** és az ügyfél-Azonosítót a natív ügyfélalkalmazás fájlból kimásolt.
    * Cserélje le **INSERT-REDIRECT-URI-Itt** a hellyel */.auth/login/done* végpontról, a HTTPS-sémát. Ez az érték legyen hasonló *https://contoso.azurewebsites.net/.auth/login/done*.

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

Az ügyfél csatlakozásának értéke általában egy egyszerű HTTP-kapcsolatot az alapul szolgáló az Android SDK-val megadott HTTP-kódtár használatával.  Miért érdemes módosíthatja, hogy több oka is van:

* Egy másik HTTP-kódtár segítségével időtúllépések szeretné.
* Adjon meg egy folyamatjelző szeretné.
* API management-funkciót egy egyéni fejlécet hozzá kívánja.
* Szeretné intercept hibás választ, hogy az újbóli hitelesítés valósítható meg.
* Szeretné egy elemzési szolgáltatás naplózza a háttér-kérelmeket.

### <a name="using-an-alternate-http-library"></a>Egy másik HTTP-kódtár használatával

Hívja a `.setAndroidHttpClientFactory()` metódus az ügyfél hivatkozás létrehozása után azonnal.  Például állítsa be a kapcsolat időkorlátja 60 másodperc (és nem az alapértelmezett 10 másodperc):

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

### <a name="implement-a-progress-filter"></a>Egy folyamat szűrő megvalósítása

Minden kérelem egy intercept az életbe léptetésével valósíthat meg egy `ServiceFilter`.  Például a következő frissíti egy előre létrehozott folyamatjelző sáv:

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
                    public void run() {
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

Az ügyfél a következő csatolhat a szűrőt:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Kérelemfejlécek testreszabása

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

Megadhatja, hogy a konvertálási stratégia, amely minden oszlop használatával vonatkozik a [gson] [ 3] API-t. Az Android-klienskódtár használ [gson] [ 3] szerializálni a Java-objektumok JSON-adatokat az Azure App Service-ben az adatok elküldése előtt a háttérben.  A következő kódban a **setFieldNamingStrategy()** metódussal adja meg a stratégiát. Ez a példa törli a kezdeti karaktert ("m"), majd kisbetűs és a következő karakter minden mező nevét. Például azt szeretné információkká "közepes" "id".  Csökkentése érdekében a konvertálási stratégia megvalósításához `SerializedName()` jegyzetek a legtöbb mezőkben.

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

Ez a kód létrehozása egy mobilügyfél hivatkozás használata előtt végre kell hajtani a **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Hitelesítés első lépései]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
