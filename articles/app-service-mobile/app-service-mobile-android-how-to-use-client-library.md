---
title: Az Androidhoz készült Azure Mobile Apps SDK használata | Microsoft Docs
description: Az Androidhoz készült Azure Mobile Apps SDK használata
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 991f3c8939c0f9e270423ff30282b02f110eb39e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388921"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Az Androidhoz készült Azure Mobile Apps SDK használata

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

Ez az útmutató bemutatja, hogyan használhatók a Mobile Apps androidos ügyfél-SDK a gyakori forgatókönyvek megvalósításához, például:

* Adatlekérdezés (Beszúrás, frissítés és törlés).
* Hitelesítés.
* Hibák kezelésére.
* Az ügyfél testreszabása.

Ez az útmutató az ügyféloldali Android SDK-ra koncentrál.  További információ a Mobile Apps kiszolgálóoldali SDK-k használatáról: a [.net háttér-SDK][10] [használata vagy a Node. js háttér-SDK használata][11].

## <a name="reference-documentation"></a>Referenciadokumentáció

Az androidos [JAVADOCS API-referenciája][12] a githubon található.

## <a name="supported-platforms"></a>Támogatott platformok

Az Azure Mobile Apps SDK for Android támogatja a 19 – 24 (KitKat – nugát) API-szintet a telefonos és a táblaszámítógép-űrlapok esetében.  A hitelesítés különösen a webes keretrendszer általános megközelítését használja a hitelesítő adatok gyűjtéséhez.  A kiszolgáló-flow hitelesítés nem működik kis méretű eszközökkel, például az órákkal.

## <a name="setup-and-prerequisites"></a>Telepítés és előfeltételek

Fejezze be a [Mobile apps](app-service-mobile-android-get-started.md) gyors üzembe helyezési oktatóanyagot.  Ez a feladat biztosítja az Azure-Mobile Apps fejlesztéséhez szükséges előfeltételek teljesülését.  A rövid útmutató segítségével konfigurálhatja a fiókját, és létrehozhatja az első Mobile apps-hátteret.

Ha úgy dönt, hogy nem fejezi be a gyors üzembe helyezési útmutatót, hajtsa végre a következő feladatokat:

* [hozzon létre egy Mobile apps-hátteret][13] az Android-alkalmazással való használatra.
* A Android Studio [frissítse a Gradle-Build fájlokat](#gradle-build).
* [Engedélyezze az Internet-engedélyt](#enable-internet).

### <a name="gradle-build"></a>A Gradle-Build fájl frissítése

A **Build. gradle** fájlok módosítása:

1. Adja hozzá ezt a kódot a *projekt* szintű **Build. gradle** fájlhoz:

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Adja hozzá ezt a kódot a *modul alkalmazás* szintű **Build. gradle** fájlhoz a *függőségek* címkén belül:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Jelenleg a legújabb verzió a 3.4.0. A támogatott verziók a [bintray][14]webhelyen találhatók.

### <a name="enable-internet"></a>Internetes engedély engedélyezése

Az Azure-hoz való hozzáféréshez az alkalmazásnak engedélyezve kell lennie az INTERNET engedélyének. Ha még nincs engedélyezve, adja hozzá a következő kódrészletet a **AndroidManifest. XML** fájlhoz:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Ügyfélkapcsolat létrehozása

Az Azure Mobile Apps négy funkciót biztosít a Mobile-alkalmazáshoz:

* Adathozzáférés és kapcsolat nélküli szinkronizálás Azure Mobile Apps szolgáltatással.
* Az Azure Mobile Apps Server SDK-val írt egyéni API-k hívása.
* Hitelesítés Azure App Service hitelesítéssel és engedélyezéssel.
* Leküldéses értesítés regisztrációja Notification Hubs.

Mindegyik függvényhez először létre kell hoznia egy `MobileServiceClient` objektumot.  Csak egy `MobileServiceClient` objektumot kell létrehozni a mobil ügyfelén belül (azaz egy egyedi mintázatnak kell lennie).  @No__t-0 objektum létrehozása:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

A `<MobileAppUrl>` karakterlánc vagy URL-objektum, amely a mobil háttérre mutat.  Ha Azure App Servicet használ a mobil háttérrendszer üzemeltetéséhez, akkor győződjön meg arról, hogy az URL Secure `https://` verzióját használja.

Az ügyfélnek szüksége van a tevékenység vagy a környezet elérésére is – a példában a `this` paramétert.  A MobileServiceClient-konstrukciónak a `AndroidManifest.xml` fájlban hivatkozott tevékenység `onCreate()` metódusán belül kell történnie.

Ajánlott eljárásként az absztrakt kiszolgáló kommunikációját a saját (egyedi minta) osztályba kell bevezetni.  Ebben az esetben át kell adnia a tevékenységet a konstruktoron belül a szolgáltatás megfelelő konfigurálásához.  Példa:

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

Most meghívhatja a `AzureServiceAdapter.Initialize(this);` értéket a fő tevékenység `onCreate()` metódusának használatával.  Az ügyfélhez való hozzáférést igénylő bármely más módszer `AzureServiceAdapter.getInstance();` használatával szerzi be a szolgáltatási adapterre mutató hivatkozást.

## <a name="data-operations"></a>Adatműveletek

Az Azure Mobile Apps SDK lényege, hogy hozzáférést biztosítson a Mobile apps-háttér SQL Azuren belül tárolt adatmennyiségekhez.  Ezeket az adattípusokat erősen beírt osztályok (preferált) vagy nem megadott lekérdezések használatával érheti el (nem ajánlott).  A szakasz nagy része az erősen begépelt osztályok használatával foglalkozik.

### <a name="define-client-data-classes"></a>Ügyfél-adatosztályok definiálása

SQL Azure táblák adatainak eléréséhez definiáljon olyan ügyféloldali adatosztályokat, amelyek megfelelnek a Mobile apps-háttér tábláinak. A témakörben szereplő példák egy **MyDataTable**nevű táblázatot feltételeznek, amelynek a következő oszlopai vannak:

* id
* szöveg
* teljes

A megfelelő begépelt ügyféloldali objektum egy **MyDataTable. Java**nevű fájlban található:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adja hozzá a beolvasót és a beállító metódusokat minden hozzáadott mezőhöz.  Ha a SQL Azure tábla több oszlopot tartalmaz, a megfelelő mezőket hozzá kell adnia ehhez az osztályhoz.  Ha például a DTO (adatátviteli objektum) egész szám prioritású oszlopot tartalmaz, akkor felveheti ezt a mezőt a beolvasó és a beállító metódusokkal együtt:

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

A Mobile Apps-háttérbeli további táblázatok létrehozásáról további információt a következő témakörben talál [: útmutató: table Controller][15] (.net-háttérrendszer) definiálása vagy [táblák definiálása dinamikus sémával][16] (node. js-háttérrel).

Az Azure Mobile Apps háttérbeli táblázat öt speciális mezőt határoz meg, amelyek közül négy az ügyfelek számára érhető el:

* `String id`: a rekord globálisan egyedi azonosítója.  Ajánlott eljárásként az azonosítót egy [UUID][17] -objektum karakterlánc-ábrázolása alapján végezze el.
* `DateTimeOffset updatedAt`: az utolsó frissítés dátuma/időpontja.  A updatedAt mezőt a kiszolgáló állítja be, és soha nem állítható be az ügyfél kódja.
* `DateTimeOffset createdAt`: az objektum létrehozásának dátuma és időpontja.  A createdAt mezőt a kiszolgáló állítja be, és soha nem állítható be az ügyfél kódja.
* @no__t – 0: általában karakterláncként van megjelölve, a-kiszolgáló is beállítja a verziót.
* @no__t – 0: azt jelzi, hogy a rekord törölve lett, de még nem történt meg.  Ne használja a `deleted` tulajdonságot az osztályban.

Az `id` mező kötelező.  A `updatedAt` mező és a `version` mező a kapcsolat nélküli szinkronizáláshoz használatos (növekményes szinkronizáláshoz és ütközés feloldásához).  A `createdAt` mező egy hivatkozási mező, amelyet az ügyfél nem használ.  A nevek "over-the-wire" névvel rendelkeznek a tulajdonságok között, és nem állíthatók be.  Azonban létrehozhat egy leképezést az objektum és az "over-the-wire" nevek között a [gson][3] könyvtár használatával.  Példa:

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

### <a name="create-a-table-reference"></a>Táblázatos hivatkozás létrehozása

Egy tábla eléréséhez először hozzon létre egy [MobileServiceTable][8] objektumot a [MobileServiceClient][9] **getTable** metódusának meghívásával.  Ez a metódus két túlterheléssel rendelkezik:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

A következő kódban a **mClient** a MobileServiceClient objektumra mutató hivatkozás.  Az első túlterhelést akkor használja a rendszer, ha az osztály neve és a tábla neve azonos, és a rövid útmutatóban használt érték:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A második túlterhelést akkor használja a rendszer, ha a tábla neve eltér az osztály nevétől: az első paraméter a tábla neve.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Háttérbeli tábla lekérdezése

Először szerezzen be egy tábla-referenciát.  Ezután hajtson végre egy lekérdezést a tábla hivatkozásán.  A lekérdezés a következőket ötvözi:

* @No__t-0 [Filter záradék](#filtering).
* Egy `.orderBy()` [rendezési záradék](#sorting).
* @No__t-0 [mező kiválasztási záradéka](#selection).
* A [lapozható eredmények](#paging)`.skip()` és `.top()`.

A záradékokat az előző sorrendben kell megjeleníteni.

### <a name="filter"></a>Szűrés eredményei

A lekérdezés általános formája:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Az előző példa az összes eredményt adja vissza (a kiszolgáló által beállított maximális oldalméret alapján).  A `.execute()` metódus végrehajtja a lekérdezést a háttérön.  A lekérdezés egy [OData v3][19] -lekérdezésre lett konvertálva, mielőtt a Mobile apps háttérbe bekerül.  A beérkezéskor a Mobile Apps háttérrendszer a lekérdezést egy SQL-utasításba konvertálja, mielőtt végrehajtja azt a SQL Azure példányon.  Mivel a hálózati tevékenység hosszabb időt vesz igénybe, a `.execute()` metódus egy [`ListenableFuture<E>`][18]értéket ad vissza.

### <a name="filtering"></a>Visszaadott adatértékek szűrése

A következő lekérdezés-végrehajtás a **ToDoItem** tábla összes elemét adja vissza, ahol a **Complete** **false értékkel**egyenlő.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

a **mToDoTable** a korábban létrehozott Mobile Service-táblára mutató hivatkozás.

Definiáljon egy szűrőt a **Where** metódus hívásával a tábla hivatkozásán. A **Where** metódust egy **mező** módszer követi, amelyet a logikai predikátumot megadó metódus követ. A lehetséges predikátum-metódusok a következők: **EQ** (Equals), **ne** (nem egyenlő), **gt** (nagyobb, mint), **GE** (nagyobb vagy egyenlő), **lt** (kevesebb, mint), **le** (kisebb vagy egyenlő). Ezekkel a módszerekkel összehasonlíthatja a számok és a karakterláncok mezőit meghatározott értékekre.

A dátumok szűrésére is lehetőség van. A következő módszerekkel összehasonlíthatja a dátum teljes dátumát vagy részleteit: **év**, **hónap**, **nap**, **óra**, **perc**és **másodperc**. A következő példa egy olyan elem szűrőjét adja vissza *, amelynek határideje* 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

A következő metódusok támogatják az összetett szűrőket a karakterlánc mezőin: **startsWith**, **endsWith**, **Összefűzés**, **alstring**, **indexOf**, **replace**, **toLower**, **toUpper**, **Trim**és **Length** . Az alábbi példa olyan táblázatos sorok szűrését tartalmazza, amelyekben a *text* oszlop a "PRI0" karakterlánccal kezdődik.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

A következő operátori módszerek támogatottak a Number mezőkben: **Hozzáadás**, **Sub**, **mul**, **div**, **mod**, **Floor**, **plafon**és **Round**. Az alábbi példa olyan táblázatos sorokra szűri a szűrőket, amelyeknél az **időtartam** páros szám.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

A predikátumokat kombinálhatja a következő logikai módszerekkel: **és**, **vagy** **nem**. Az alábbi példa az előző példák közül kettőt egyesít.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Logikai operátorok csoportosítása és beágyazása:

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

Részletesebb eszmecsere és példák a szűrésre: az [androidos ügyfél-lekérdezési modell gazdagságának feltárása][20].

### <a name="sorting"></a>Visszaadott adatsorok rendezése

A következő kód visszaadja a **ToDoItems** található összes elemet a *szövegmezőben* növekvő sorrendbe rendezve. a *mToDoTable* a korábban létrehozott háttér-táblázatra mutató hivatkozás:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

A **orderBy** metódus első paramétere a rendezni kívánt mező nevével megegyező karakterlánc. A második paraméter a **QueryOrder** enumerálást használja annak megadásához, hogy növekvő vagy csökkenő sorrendben kell-e rendezni.  Ha a ***Where*** metódussal végez szűrést, a ***Where*** metódust a ***orderBy*** metódus előtt kell meghívni.

### <a name="selection"></a>Adott oszlopok kiválasztása

Az alábbi kód azt mutatja be, hogyan lehet visszaadni az összes elemet egy **ToDoItems**, de csak a **teljes** és a **szöveges** mezőt jeleníti meg. a **mToDoTable** a korábban létrehozott háttér-táblázatra mutató hivatkozás.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

A Select függvény paramétereinek a tábla azon oszlopainak a karakterlánc-nevei, amelyeket vissza szeretne állítani.  A **Select** metódusnak olyan metódusokat kell követnie, mint a **Where** és a **orderBy**. Ezt követheti a lapozási módszerek, például a **skip** és a **Top**.

### <a name="paging"></a>A lapokon tárolt adatvisszaadás

Az adatlapokat a rendszer **mindig** visszaadja.  A visszaadott rekordok maximális számát a kiszolgáló állítja be.  Ha az ügyfél további rekordokat kér, akkor a kiszolgáló a rekordok maximális számát adja vissza.  Alapértelmezés szerint a kiszolgálón a maximális oldalméret 50 rekord.

Az első példa azt szemlélteti, hogyan lehet kijelölni egy tábla első öt elemét. A lekérdezés a **ToDoItems**származó elemeket adja vissza. a **mToDoTable** a korábban létrehozott háttér-táblázatra mutató hivatkozás:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Íme egy olyan lekérdezés, amely kihagyja az első öt elemet, majd a következő öt értéket adja vissza:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Ha egy tábla összes rekordját le szeretné kérni, hajtsa végre a következő kód megismétlését az összes oldalon:

```java
List<MyDataModel> results = new ArrayList<>();
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

A metódust használó összes rekordra vonatkozó kérelem legalább két kérelmet hoz létre a Mobile Apps háttérbe.

> [!TIP]
> A jobb oldal méretének kiválasztása a memóriahasználat és a kérések közötti egyensúly, valamint a sávszélesség-használat és az adatok teljes fogadásának késleltetése.  Az alapértelmezett (50 rekord) minden eszköz számára megfelelő.  Ha kizárólag nagyobb memória-eszközökön működik, növelje a 500-ot.  Úgy találtuk, hogy az oldal méretének növelése 500 rekordnál továbbra is elfogadhatatlan késéseket és nagy memóriabeli problémákat eredményez.

### <a name="chaining"></a>Útmutató: lekérdezési módszerek összefűzése

A háttérbeli táblázatok lekérdezésében használt módszerek összefűzése is lehetséges. A láncolási lekérdezési módszerek lehetővé teszik a rendezett és lapozható szűrt sorok adott oszlopainak kiválasztását. Létrehozhat összetett logikai szűrőket.  Minden lekérdezési metódus egy lekérdezési objektumot ad vissza. A metódusok sorozatának befejezéséhez és a lekérdezés tényleges futtatásához hívja meg a **végrehajtás** metódust. Példa:

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

A láncolt lekérdezési metódusokat a következőképpen kell megrendelni:

1. Szűrés (**Where**) metódusok.
2. Rendezési (**orderBy**) metódusok.
3. Kiválasztási (**Select**) metódusok.
4. Lapozás (**kihagyás** és **felső**) metódusok.

## <a name="binding"></a>Adatkötés a felhasználói felületre

Az adatkötés három összetevőt foglal magában:

* Az adatforrás
* A képernyő elrendezése
* Az adapter, amely összeköti a kettőt.

A mintakód esetében a Mobile Apps SQL Azure Table **ToDoItem** származó adatok egy tömbbe kerülnek vissza. Ez a tevékenység az adatalkalmazások közös mintája.  Az adatbázis-lekérdezések gyakran olyan sorok gyűjteményét adják vissza, amelyeket az ügyfél egy listában vagy tömbben kap. Ebben a példában a tömb az adatforrás.  A kód egy olyan képernyő-elrendezést határoz meg, amely az eszközön megjelenő adatnézetet határozza meg.  A kettő egy adapterrel van kötve, amely ebben a kódban a **ArrayAdapter @ no__t-1ToDoItem @ no__t-2** osztály kiterjesztése.

#### <a name="layout"></a>Az elrendezés megadása

Az elrendezést az XML-kód több kódrészlete határozza meg. Egy meglévő elrendezés miatt a következő kód azt a **ListView vezérlőt** jelöli, amelyet a kiszolgáló adataival szeretnénk feltölteni.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Az előző kódban a *listaelem* attribútum a listában szereplő egyes sorok elrendezésének azonosítóját határozza meg. Ez a kód egy jelölőnégyzetet és a hozzá tartozó szöveget is megadja, és a lista minden egyes eleménél egyszer példányba kerül. Ez az elrendezés nem jeleníti meg az **azonosító** mezőt, és egy összetettebb elrendezésben további mezők is megjelenhetnek a kijelzőn. Ez a kód a **row_list_to_do. XML** fájlban található.

```xml
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

#### <a name="adapter"></a>Az adapter megadása
Mivel a nézet adatforrása a **ToDoItem**tömbje, az adapter alosztálya egy **ArrayAdapter @ no__t-2ToDoItem @ no__t-3** osztály. Ez az alosztály minden **ToDoItem** létrehoz egy nézetet a **row_list_to_do** elrendezés használatával.  A kódban a következő osztályt adjuk meg, amely a **ArrayAdapter @ no__t-1e @ no__t-2** osztály kiterjesztése:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Felülbírálja az adapterek **getView** metódusát. Példa:

```java
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

Az osztály egy példányát a következő módon hozunk létre a tevékenységben:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

A ToDoItemAdapter konstruktor második paramétere az elrendezésre mutató hivatkozás. Most már létrehozhatja a **ListView** vezérlőt, és hozzárendelheti az adaptert a **ListView vezérlőhöz**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Az adapter használata a felhasználói felülethez való kötéshez

Most már készen áll az Adatkötés használatára. A következő kód bemutatja, hogyan kérhet le elemeket a táblában, és kitölti a helyi adaptert a visszaadott elemekkel.

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

Hívja meg az adaptert, amikor módosítja a **ToDoItem** táblát. Mivel a módosításokat rekord alapján végzik el, a gyűjtemény helyett egyetlen sort kell kezelni. Egy elem beszúrásakor hívja meg az **Add** metódust az adapteren. a törlés során hívja meg az **eltávolítási** metódust.

Az Android gyors üzembe helyezési [projektben][21]egy teljes példát talál.

## <a name="inserting"></a>Adatbeszúrás a háttérbe

Hozza létre a *ToDoItem* osztály egy példányát, és állítsa be a tulajdonságait.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Ezután a **Insert ()** paranccsal szúrhat be egy objektumot:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

A visszaadott entitás megegyezik a háttér-táblába beszúrt adatokkal, beleértve az azonosítót és az egyéb értékeket (például a `createdAt`, `updatedAt` és `version` mezőt) a háttérre állítva.

Mobile Apps táblákhoz egy **azonosító**nevű elsődleges kulcs oszlop szükséges. Az oszlopnak karakterláncnak kell lennie. Az ID oszlop alapértelmezett értéke egy GUID.  Más egyedi értékeket is megadhat, például e-mail-címeket vagy felhasználóneveket. Ha egy beszúrt rekordhoz nincs megadva karakterlánc-azonosító érték, a háttér új GUID azonosítót hoz létre.

A karakterlánc-azonosító értékei a következő előnyöket nyújtják:

* Az azonosítók előállítása anélkül lehetséges, hogy az adatbázisba oda kellene esni.
* A rekordok könnyebben egyesíthetők különböző táblákból vagy adatbázisokból.
* Az azonosító értékek jobban illeszkednek az alkalmazás logikája.

A kapcsolat nélküli szinkronizálás támogatásához karakterlánc-azonosító értékek **szükségesek** .  A háttér-adatbázisban tárolt azonosítók nem módosíthatók.

## <a name="updating"></a>Mobile-alkalmazásban tárolt Adatfrissítés

Egy táblában lévő adatfrissítéshez adja át az új objektumot a **Update ()** metódusnak.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Ebben a példában az *elem* egy, a *ToDoItem* tábla egyik sorára mutat, amelynek változása történt.  Az ugyanazzal az **azonosítóval** rendelkező sor frissül.

## <a name="deleting"></a>A Mobile-alkalmazásban tárolt adattörlés

Az alábbi kód azt mutatja be, hogyan törölhet adatok egy táblából az adatobjektum megadásával.

```java
mToDoTable
    .delete(item);
```

Az elemeket törölheti is a törlendő sor **azonosító** mezőjének megadásával.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Adott tétel megkeresése azonosító alapján

Keressen egy olyan tételt, amely egy adott **azonosító** mezővel rendelkezik a **lookUp ()** metódussal:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Útmutató: nem típusos adatmennyiségek használata

A nem típusos programozási modell pontosan szabályozza a JSON-szerializálást.  Vannak olyan gyakori helyzetek, amikor nem típusos programozási modellt szeretne használni. Ha például a háttérbeli tábla sok oszlopot tartalmaz, és csak az oszlopok egy részhalmazára van szükség.  A típusos modellhez meg kell határoznia az adatosztály Mobile Apps backendjában definiált összes oszlopot.  Az adatok eléréséhez használt API-hívások többsége hasonló a beírt programozási hívásokhoz. A fő különbség az, hogy a nem típusos modellben a **MobileServiceTable** objektum helyett metódusokat hív meg a **MobileServiceJsonTable** objektumon.

### <a name="json_instance"></a>Típus nélküli tábla példányának létrehozása

A begépelt modellhez hasonlóan először is egy táblázatra mutató hivatkozást kap, de ebben az esetben ez egy **MobileServicesJsonTable** objektum. Szerezze be a hivatkozást úgy, hogy meghívja a **getTable** metódust az ügyfél egy példányán:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Miután létrehozta a **MobileServiceJsonTable**egy példányát, gyakorlatilag UGYANAZZAL az API-val érhető el, mint a beírt programozási modellel. Bizonyos esetekben előfordulhat, hogy a metódusok nem típusos paramétert alkalmaznak a beírt paraméterek helyett.

### <a name="json_insert"></a>Beszúrás típus nélküli táblába
A következő kód bemutatja, hogyan végezheti el a beszúrást. Első lépésként hozzon létre egy [JsonObject][1], amely a [gson][3] könyvtár részét képezi.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Ezután a **Insert ()** paranccsal szúrja be a típusos objektumot a táblába.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Ha be kell szereznie a beszúrt objektum AZONOSÍTÓját, használja a **getAsJsonPrimitive ()** metódust.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Törlés nem típusos táblából
A következő kód bemutatja, hogyan törölhet egy példányt, ebben az esetben egy olyan **JsonObject** -példányt, amely az előző *Insert* példában lett létrehozva. A kód megegyezik a beírt esettel, de a metódusnak eltérő aláírása van, mivel egy **JsonObject**hivatkozik.

```java
mToDoTable
    .delete(insertedItem);
```

A példányokat közvetlenül is törölheti az AZONOSÍTÓjának használatával:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Az összes sor visszaadása egy típus nélküli táblából
A következő kód bemutatja, hogyan kérhet le egy teljes táblázatot. Mivel JSON-táblázatot használ, szelektíven lekérheti a tábla oszlopainak egy részét.

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

A típusos modellhez hasonló szűrési, szűrési és lapozási módszerek is elérhetők.

## <a name="offline-sync"></a>Offline szinkronizálás megvalósítása

Az Azure Mobile Apps Client SDK az adatkapcsolat nélküli szinkronizálást is alkalmazza egy SQLite-adatbázis használatával, amely helyileg tárolja a kiszolgáló adatainak másolatát.  Az offline táblán végrehajtott műveletekhez nem szükséges a mobil kapcsolat a működéshez.  A rugalmasság és a teljesítmény kapcsolat nélküli szinkronizálásának támogatása az összeütközés feloldásához szükséges összetettebb logika rovására.  Az Azure Mobile Apps Client SDK a következő funkciókat valósítja meg:

* Növekményes szinkronizálás: a rendszer csak a frissített és új rekordokat tölti le, így a sávszélességet és a memóriát is megtakarítja.
* Optimista Egyidejűség: a műveletek sikerességét feltételezik.  Az ütközés feloldása késleltetve, amíg a rendszer frissíti a frissítéseket a kiszolgálón.
* Ütközés feloldása: az SDK észleli, ha ütköző módosítás történt a kiszolgálón, és a hookokat biztosít a felhasználó riasztásához.
* Helyreállítható törlés: a törölt rekordok törölve lettek, így más eszközök is frissíthetik a kapcsolat nélküli gyorsítótárat.

### <a name="initialize-offline-sync"></a>Kapcsolat nélküli szinkronizálás inicializálása

A használat előtt minden offline táblát definiálni kell az offline gyorsítótárban.  A tábla definícióját általában közvetlenül az ügyfél létrehozása után kell elvégezni:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>A kapcsolat nélküli gyorsítótár táblára mutató hivatkozás beszerzése

Online tábla esetén `.getTable()` értéket kell használnia.  Offline tábla esetén használja a következőt: `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Az online táblákhoz elérhető összes módszer (beleértve a szűrést, a rendezést, a lapozást, az adatbeszúrást, az adatfrissítést és az adattörlést) ugyanúgy működik az online és az offline táblákon is.

### <a name="synchronize-the-local-offline-cache"></a>A helyi kapcsolat nélküli gyorsítótár szinkronizálása

A szinkronizálás az alkalmazás vezérlésén belül van.  Példa szinkronizálási módszerre:

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

Ha a lekérdezés neve a `.pull(query, queryname)` metódushoz van megadva, akkor a növekményes szinkronizálás csak az utolsó sikeres lekérés óta létrehozott vagy módosított rekordok visszaadására szolgál.

### <a name="handle-conflicts-during-offline-synchronization"></a>Ütközések kezelése az offline szinkronizálás során

Ha ütközés lép fel egy @no__t 0 művelet során, a rendszer egy `MobileServiceConflictException`-et fog dobni.   A kiszolgáló által kiállított elem be van ágyazva a kivételbe, és a kivételnél `.getItem()` alapján kérhető le.  Állítsa be a leküldést úgy, hogy a következő elemeket hívja meg a MobileServiceSyncContext objektumon:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Ha az összes ütközés meg van jelölve, az összes ütközés feloldásához hívja meg a `.push()` értéket.

## <a name="custom-api"></a>Egyéni API meghívása

Az egyéni API-k olyan egyéni végpontok definiálását teszik lehetővé, amelyek olyan kiszolgáló-funkciókat tesznek elérhetővé, amelyek nem képezhetők be a beszúrási, frissítési, törlési és olvasási műveletekhez. Egyéni API-k használatával nagyobb mértékben szabályozhatja az üzenetkezelést, többek között a HTTP-üzenetek fejlécének olvasását és beállítását, valamint a JSON formátumon kívüli üzenettörzs definiálását is.

Egy Android-ügyfélről hívja meg a **invokeApi** metódust az egyéni API-végpont meghívásához. Az alábbi példa bemutatja, hogyan hívhat meg egy **completeAll**nevű API-végpontot, amely egy **MarkAllResult**nevű gyűjteményi osztályt ad vissza.

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

A **invokeApi** metódust az ügyfél hívja meg, amely post-kérést küld az új egyéni API-nak. Az egyéni API által visszaadott eredmény megjelenik egy üzenet párbeszédpanelen, ahogy az esetleges hibák is. A **invokeApi** egyéb verziói lehetővé teszik, hogy egy objektumot a kérelem törzsében, a http-metódust is megadja, és lekérdezési paramétereket küldjön a kérésnek. A **invokeApi** típussal nem rendelkező verziói is elérhetők.

## <a name="authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz

Az oktatóanyagok már részletesen ismertetik a szolgáltatások hozzáadásának módját.

App Service támogatja az [alkalmazások felhasználóinak hitelesítését](app-service-mobile-android-get-started-users.md) különböző külső identitás-szolgáltatók használatával: Facebook, Google, Microsoft-fiók, Twitter és Azure Active Directory. A táblákra vonatkozó engedélyeket úgy állíthatja be, hogy az adott műveletekhez való hozzáférést csak a hitelesített felhasználókra korlátozza. A hitelesített felhasználók identitásával is elvégezheti a háttérbeli engedélyezési szabályok megvalósítását.

A rendszer két hitelesítési folyamatot támogat: a **kiszolgáló** és az **ügyfél** folyamatát. A kiszolgálói folyamat biztosítja a legegyszerűbb hitelesítési felületet, mivel az identitás-szolgáltatók webes felületén alapul.  A kiszolgálói folyamat hitelesítésének megvalósításához nincs szükség további SDK-k bevezetésére. A kiszolgálói folyamat hitelesítése nem biztosít mély integrációt a mobileszköz számára, és csak a fogalom-forgatókönyvek igazolása esetén ajánlott.

Az ügyféloldali folyamat lehetővé teszi az eszközre jellemző képességekkel való mélyebb integrációt, például az egyszeri bejelentkezést, mivel az identitás-szolgáltató által biztosított SDK-k alapján működik.  Integrálhatja például a Facebook SDK-t a mobil alkalmazásba.  A Mobile Client becseréli a Facebook-alkalmazást, és megerősíti a bejelentkezést, mielőtt visszavált a mobil alkalmazásba.

Négy lépés szükséges a hitelesítés engedélyezéséhez az alkalmazásban:

* Az alkalmazás regisztrálása az identitás-szolgáltatóval történő hitelesítéshez.
* Konfigurálja a App Service háttér-hátteret.
* Korlátozza a tábla engedélyeit a hitelesített felhasználók számára, csak a App Service háttérrendszer esetében.
* Adja hozzá a hitelesítési kódot az alkalmazáshoz.

A táblákra vonatkozó engedélyeket úgy állíthatja be, hogy az adott műveletekhez való hozzáférést csak a hitelesített felhasználókra korlátozza. A kérések módosításához használhatja a hitelesített felhasználó biztonsági azonosítóját is.  További információkért tekintse át az Ismerkedés [Ismerkedés a hitelesítéssel] és a Server SDK howto dokumentációját.

### <a name="caching"></a>Hitelesítés: kiszolgálói folyamat

A következő kód elindítja a kiszolgálói flow bejelentkezési folyamatát a Google Provider használatával.  A Google Provider biztonsági követelményei miatt további konfigurálásra van szükség:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Emellett adja hozzá a következő metódust a fő tevékenység osztályhoz:

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

A fő tevékenységben definiált `GOOGLE_LOGIN_REQUEST_CODE` a `login()` metódushoz és a `onActivityResult()` metódushoz használatos.  Bármelyik egyedi számot kiválaszthatja, ha ugyanazt a számot használja a `login()` metódusban és a `onActivityResult()` metódusban.  Ha az ügyfél kódját egy szolgáltatási adapterbe (a korábban látható módon) absztraktként választotta, akkor a megfelelő metódusokat kell meghívnia a szolgáltatás adapterén.

A customtabs-projekthez is konfigurálnia kell a projektet.  Először meg kell adni egy átirányítási URL-címet.  Adja hozzá a következő kódrészletet a `AndroidManifest.xml` értékhez:

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

Adja hozzá a **redirectUriScheme** az alkalmazás `build.gradle` fájljához:

```gradle
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

Végül adja hozzá a `com.android.support:customtabs:28.0.0` értéket az `build.gradle` fájl függőségek listájához:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Szerezze be a bejelentkezett felhasználó AZONOSÍTÓját egy **MobileServiceUser** a **getUserId** metódus használatával. A következő témakörből megtudhatja, hogyan használhatja a határidőket az aszinkron bejelentkezési API-k meghívására: [Ismerkedés a hitelesítéssel].

> [!WARNING]
> Az említett URL-séma megkülönbözteti a kis-és nagybetűket.  Győződjön meg arról, hogy `{url_scheme_of_you_app}` egyeztetési eset összes előfordulása.

### <a name="caching"></a>Gyorsítótár-hitelesítési tokenek

A gyorsítótárazási hitelesítési tokenek megkövetelik, hogy helyileg tárolja a felhasználói azonosítót és a hitelesítési jogkivonatot az eszközön. Amikor az alkalmazás legközelebb elindul, ellenőrizze a gyorsítótárat, és ha ezek az értékek jelennek meg, kihagyhatja a bejelentkezési eljárást, és kihasználhatja az ügyfelet ezekkel az adatokkal. Ezek az adatok azonban érzékenyek, és biztonsági szempontból titkosítottnak kell lenniük, ha a telefont ellopják.  A hitelesítési tokenek gyorsítótárazásának teljes példáját a [gyorsítótár-hitelesítési tokenek című szakaszban][7]tekintheti meg.

Ha lejárt jogkivonatot próbál használni, a rendszer *401 jogosulatlan* választ kap. A hitelesítési hibákat szűrők használatával is kezelheti.  Szűri a kérelmeket a App Service háttérbe. A Filter kód a 401-es választ teszteli, elindítja a bejelentkezési folyamatot, majd folytatja a 401-et generáló kérést.

### <a name="refresh"></a>Frissítési tokenek használata

Azure App Service hitelesítés és engedélyezés által visszaadott token egy óra meghatározott élettartammal rendelkezik.  Ezen időszak elteltével újra hitelesítenie kell a felhasználót.  Ha olyan hosszú élettartamú tokent használ, amelyet az ügyfél-átfolyásos hitelesítésen keresztül kapott, akkor újrahitelesítheti Azure App Service hitelesítést és engedélyezést ugyanazzal a jogkivonat használatával.  Egy másik Azure App Service jogkivonat jön létre egy új élettartammal.

Azt is megteheti, hogy a szolgáltatót a frissítési tokenek használatára is regisztrálja.  A frissítési jogkivonat nem mindig érhető el.  További konfigurálásra van szükség:

* **Azure Active Directory**esetében konfigurálja a Azure Active Directory alkalmazáshoz tartozó ügyfél-titkos kulcsot.  Azure Active Directory hitelesítés konfigurálásakor a Azure App Service-ügyfél titkos kulcsát kell megadnia.  @No__t-0 hívásakor a `response_type=code id_token` paramétert paraméterként adja át:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* A **Google**esetében adja át a `access_type=offline` paramétert:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Microsoft-fiók**esetén válassza ki a `wl.offline_access` hatókört.

A tokenek frissítéséhez hívja meg a `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Ajánlott eljárásként hozzon létre egy szűrőt, amely észleli a kiszolgáló 401-válaszát, és megpróbálja frissíteni a felhasználói jogkivonatot.

## <a name="log-in-with-client-flow-authentication"></a>Bejelentkezés az ügyfél-flow hitelesítéssel

Az ügyfél-flow hitelesítéssel történő bejelentkezés általános folyamata a következő:

* Konfigurálja Azure App Service hitelesítést és az engedélyezést úgy, mint a kiszolgálói folyamat hitelesítését.
* A hitelesítési szolgáltatói SDK integrálása a hitelesítéshez hozzáférési jogkivonat létrehozásához.
* Hívja meg a `.login()` metódust az alábbiak szerint (`result` a következőnek kell lennie: `AuthenticationResult`):

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

A következő szakaszban tekintse meg a kód teljes példáját.

Cserélje le a `onSuccess()` metódust minden olyan kódra, amelyet sikeres bejelentkezéskor szeretne használni.  A `{provider}` karakterlánc érvényes szolgáltató: **HRE** (Azure Active Directory), **Facebook**, **Google**, **MicrosoftAccount**vagy **Twitter**.  Ha implementálta az egyéni hitelesítést, akkor használhatja az egyéni hitelesítési szolgáltató címkét is.

### <a name="adal"></a>Felhasználók hitelesítése a Active Directory-hitelesítési tár (ADAL)

A Active Directory-hitelesítési tár (ADAL) használatával a felhasználókat a Azure Active Directory használatával lehet az alkalmazásba írni. Az ügyfél-adatfolyamok bejelentkezésének használata gyakran előnyösebb a `loginAsync()` módszerek használatára, mivel ez egy több natív UX-élményt nyújt, és lehetővé teszi a további testreszabást.

1. A HRE-bejelentkezéshez a [app Service konfigurálása Active Directory bejelentkezési][22] oktatóanyagban című témakörben található. Győződjön meg arról, hogy a natív ügyfélalkalmazás regisztrálásának nem kötelező lépéseit kell végrehajtania.
2. Telepítse a ADAL a Build. gradle fájl módosításával, hogy az tartalmazza a következő definíciókat:

    ```gradle
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
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Adja hozzá a következő kódot az alkalmazáshoz, és végezze el a következő cseréket:

    * Cserélje le a **Insert-Authority-here** nevet annak a bérlőnek a nevére, amelyben az alkalmazást kiépítte. A formátumnak https://login.microsoftonline.com/contoso.onmicrosoft.com értékűnek kell lennie.
    * Cserélje le a **Insert-Resource-id-** t a Mobile apps-háttér ügyfél-azonosítójával. Az ügyfél-azonosítót a portál **Azure Active Directory beállítások** területén található **speciális** lapon szerezheti be.
    * Cserélje le az **Insert-Client-ID-** t a natív ügyfélalkalmazás által másolt ügyfél-azonosítóra.
    * Cserélje le a **Insert-redirect-URI-t – itt** a hely */.auth/login/Done* -végpontján a https-séma használatával. Ennek az értéknek a következőhöz hasonlónak kell lennie: *https://contoso.azurewebsites.net/.auth/login/done* .

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

## <a name="filters"></a>Az ügyfél és a kiszolgáló közötti kommunikáció módosítása

Az Ügyfélkapcsolat általában egy alapszintű HTTP-kapcsolatok, amely az Android SDK-val megadott mögöttes HTTP-könyvtárat használja.  Több oka is van annak, hogy módosítani szeretné ezt:

* Alternatív HTTP-függvénytárat szeretne használni az időtúllépések módosításához.
* Meg szeretné adni a folyamatjelző sávot.
* Egyéni fejlécet szeretne hozzáadni az API Management funkcióinak támogatásához.
* Sikertelen választ szeretne feltartóztatni, hogy végre tudja hajtani az újrahitelesítést.
* A háttérbeli kérelmeket egy elemzési szolgáltatásba kívánja naplózni.

### <a name="using-an-alternate-http-library"></a>Alternatív HTTP-könyvtár használata

Az ügyfél-hivatkozás létrehozása után azonnal hívja meg a `.setAndroidHttpClientFactory()` metódust.  Ha például a kapcsolat időkorlátját 60 másodpercre szeretné beállítani (az alapértelmezett 10 másodperc helyett):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Állapotjelző szűrő megvalósítása

Az összes kérelem elfogásának megvalósításához egy `ServiceFilter` implementációt alkalmazhat.  Például a következő frissítés egy előre létrehozott folyamatjelző sávot:

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

Ezt a szűrőt a következőképpen csatolhatja az ügyfélhez:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Kérések fejlécének testreszabása

Használja a következő `ServiceFilter` értéket, és csatolja a szűrőt ugyanúgy, mint a `ProgressFilter`:

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

### <a name="conversions"></a>Automatikus szerializálás konfigurálása

Megadhat egy átalakítási stratégiát, amely minden oszlopra érvényes a [gson][3] API használatával. Az Android-ügyfél könyvtára a háttérben [gson][3] használ a Java-objektumok JSON-adatként való szerializálásához, mielőtt az adatküldés Azure app Service.  A következő kód a **setFieldNamingStrategy ()** metódust használja a stratégia beállításához. Ez a példa törli a kezdeti karaktert ("m"), majd a következő karakternél az összes mezőnév esetében a következő karaktert. Például a "mId" a "id" értékre vált.  Hozzon létre egy átalakítási stratégiát, amely csökkenti a legtöbb mezőre vonatkozó `SerializedName()` jegyzetek szükségességét.

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
        .setFieldNamingStrategy(namingStrategy)
);
```

Ezt a kódot csak akkor kell végrehajtani, ha a **MobileServiceClient**használatával hoz létre mobil ügyfél-referenciát.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Ismerkedés a hitelesítéssel]: app-service-mobile-android-get-started-users.md
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
