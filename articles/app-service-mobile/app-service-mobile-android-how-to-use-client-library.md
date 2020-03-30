---
title: Az SDK használata Androidra
description: Az Android-alapú Azure Mobile Apps SDK használata
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249385"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Az Android-alapú Azure Mobile Apps SDK használata

Ez az útmutató bemutatja, hogyan használhatja az Android client SDK mobilalkalmazásokhoz közös forgatókönyvek megvalósítását, például:

* Adatok lekérdezése (beszúrás, frissítés és törlés).
* Hitelesítés.
* Kezelési hibák.
* Az ügyfél testreszabása.

Ez az útmutató az ügyféloldali Android SDK-ra összpontosít.  Ha többet szeretne tudni a mobilalkalmazások kiszolgálóoldali SDK-iról, olvassa el a [.NET háttérsdkta][10] használata vagy [a Node.js háttérstkka SDK használata][11]című témakört.

## <a name="reference-documentation"></a>Referenciadokumentáció

A [Javadocs API-referencia][12] az Android-ügyfélkönyvtár a GitHubon.

## <a name="supported-platforms"></a>Támogatott platformok

Az Android-alapú Azure Mobile Apps SDK támogatja a 19–24-es API-szintet (KitKat a Nugáton keresztül) a telefonos és táblagépes formatényezőkhöz.  A hitelesítés különösen egy közös webes keretrendszer-megközelítést használ a hitelesítő adatok összegyűjtéséhez.  A kiszolgálóáramlásos hitelesítés nem működik kis méretű eszközökkel, például órákkal.

## <a name="setup-and-prerequisites"></a>Telepítés és előfeltételek

Töltse ki a [Mobilalkalmazások rövid útmutatóját.](app-service-mobile-android-get-started.md)  Ez a feladat biztosítja, hogy az Azure Mobile Apps fejlesztésének minden előfeltétele teljesüljön.  A rövid útmutató segítségével konfigurálhatja fiókját, és létrehozhatja az első mobilalkalmazás-háttérrendszert.

Ha úgy dönt, hogy nem fejezi be a rövid útmutatót, hajtsa végre a következő feladatokat:

* [hozzon létre egy mobilalkalmazás-háttérrendszert][13] az Android-alkalmazással való használatra.
* Az Android Studio alkalmazásban [frissítse a Gradle buildfájlokat.](#gradle-build)
* [Internetes engedély engedélyezése](#enable-internet).

### <a name="update-the-gradle-build-file"></a><a name="gradle-build"></a>A Gradle buildfájl frissítése

Módosítsa mindkét **build.gradle** fájlt:

1. Adja hozzá ezt a kódot a *Project* szintű **build.gradle** fájlhoz:

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

2. Adja hozzá ezt a kódot a *Module app* level **build.gradle** fájlhoz a *függőségi* címke belsejében:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Jelenleg a legutolsó változat van 3.4.0. A támogatott verziók [a tárolórekeszen][14]vannak felsorolva.

### <a name="enable-internet-permission"></a><a name="enable-internet"></a>Internetes engedély engedélyezése

Az Azure eléréséhez az alkalmazásnak engedélyeznie kell az internet-engedélyt. Ha még nincs engedélyezve, adja hozzá a következő kódsort az **AndroidManifest.xml** fájlhoz:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Ügyfélkapcsolat létrehozása

Az Azure Mobile Apps négy funkciót biztosít a mobilalkalmazásszámára:

* Adatelérés és offline szinkronizálás egy Azure Mobile Apps Szolgáltatással.
* Hívja meg az Azure Mobile Apps Server SDK-val írt egyéni API-kat.
* Hitelesítés az Azure App Service hitelesítésével és engedélyezésével.
* Leküldéses értesítési regisztráció az értesítési központokkal.

Ezek a függvények mindegyike először létre kell hoznia egy `MobileServiceClient` objektumot.  Csak `MobileServiceClient` egy objektumot kell létrehozni a mobil ügyfélen belül (azaz egy Singleton mintának kell lennie).  `MobileServiceClient` Objektum létrehozása:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

A `<MobileAppUrl>` vagy egy karakterlánc vagy egy URL-objektum, amely a mobil háttérprogramra mutat.  Ha az Azure App Service-t használja a mobil háttérszolgáltatás `https://` üzemeltetéséhez, akkor győződjön meg arról, hogy az URL-cím biztonságos verzióját használja.

Az ügyfél is hozzáférést igényel a `this` tevékenység vagy a környezet - a paraméter a példában.  A MobileServiceClient felépítésének `onCreate()` a `AndroidManifest.xml` fájlban hivatkozott tevékenység metódusán belül kell történnie.

Ajánlott eljárásként absztrakt kiszolgálókommunikációt kell a saját (singleton-minta) osztályba.  Ebben az esetben át kell adnia a tevékenységet a konstruktoron belül a szolgáltatás megfelelő konfigurálásához.  Példa:

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

Most már `AzureServiceAdapter.Initialize(this);` behívhatja a `onCreate()` fő tevékenység ének módját.  Minden más olyan módszer, amelynek hozzáférést kell kérnie az ügyfélhez `AzureServiceAdapter.getInstance();` a szervizadapterre mutató hivatkozás megszerzéséhez.

## <a name="data-operations"></a>Adatműveletek

Az Azure Mobile Apps SDK lényege, hogy hozzáférést biztosítson az SQL Azure-ban a mobilalkalmazás-háttérrendszeren tárolt adatokhoz.  Ezeket az adatokat erősen beírt (előnyben részesített) vagy nem beírt lekérdezések (nem ajánlott) használatával érheti el.  A szakasz nagy része erősen beírt osztályok használatával foglalkozik.

### <a name="define-client-data-classes"></a>Ügyféladatosztályok definiálása

Az SQL Azure-táblákból származó adatok eléréséhez határozza meg azokat az ügyféladat-osztályokat, amelyek megfelelnek a mobileapp-háttérrendszer tábláinak. Ebben a témakörben példák egy **MyDataTable**nevű táblát feltételeznek, amely a következő oszlopokat tartalmaz:

* id
* szöveg
* Teljes

A megfelelő begépelt ügyféloldali objektum a **MyDataTable.java**fájlban található:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Getter és setter metódusok hozzáadása minden hozzáadott mezőhöz.  Ha az SQL Azure-tábla több oszlopot tartalmaz, hozzá kell adnia a megfelelő mezőket ehhez az osztályhoz.  Ha például a DTO (adatátviteli objektum) egész prioritás oszloppal rendelkezik, akkor hozzáadhatja ezt a mezőt a getter és setter metódusaival együtt:

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

Ha meg szeretné tudni, hogyan hozhat létre további táblákat a mobilalkalmazások háttérrendszerében, olvassa el a Táblázatvezérlő (.NET [háttérrendszer) definiálása][15] vagy [A dinamikus séma használatával][16] (Node.js háttérrendszer) definiálása című témakört.

Az Azure Mobile Apps háttértáblája öt speciális mezőt határoz meg, amelyek közül négy elérhető az ügyfelek számára:

* `String id`: A rekord globálisan egyedi azonosítója.  Ajánlott eljárásként, hogy az id a Karakterlánc ábrázolása egy [UUID][17] objektum.
* `DateTimeOffset updatedAt`: Az utolsó frissítés dátuma/időpontja.  A updatedAt mezőt a kiszolgáló állítja be, és soha nem szabad az ügyfélkóddal beállítani.
* `DateTimeOffset createdAt`: Az objektum létrehozásának dátuma/időpontja.  A createdAt mezőt a kiszolgáló állítja be, és soha nem szabad az ügyfélkóddal beállítani.
* `byte[] version`: Általában karakterláncként jelenik meg, a verziót a kiszolgáló is beállítja.
* `boolean deleted`: Azt jelzi, hogy a rekordot törölték, de még nem törölték.  Ne használja `deleted` az osztályban tulajdonságként.

Az `id` mező kötelező.  A `updatedAt` mező `version` és a mező kapcsolat nélküli szinkronizálásra szolgál (a növekményes szinkronizáláshoz, illetve az ütközések feloldásához).  A `createdAt` mező egy hivatkozásmező, amelyet az ügyfél nem használ.  A nevek a tulajdonságok "vezetéken keresztül" elnevezései, és nem állíthatók.  A [gson-könyvtár][3] használatával azonban létrehozhat egy leképezést az objektum és a "vezetéken keresztüli" nevek között.  Példa:

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

### <a name="create-a-table-reference"></a>Táblázathivatkozás létrehozása

Egy tábla eléréséhez először hozzon létre egy [MobileServiceTable][8] objektumot a **GetTable** metódus hívásával a [MobileServiceClient .to][9]access a Table  Ennek a módszernek két túlterhelése van:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

A következő kódban az **mClient** a MobileServiceClient objektumra mutató hivatkozás.  Az első túlterhelés akkor használatos, ha az osztály név és a táblanév megegyezik, és a rövid útmutatóban használt:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A második túlterhelés akkor használatos, ha a tábla neve eltér az osztály nevétől: az első paraméter a tábla neve.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query-a-backend-table"></a><a name="query"></a>Háttértábla lekérdezése

Először kérjen táblázatot.  Ezután hajtson végre egy lekérdezést a táblahivatkozáson.  A lekérdezés a következők bármilyen kombinációja:

* `.where()` [Szűrőzáradék](#filtering).
* Egy `.orderBy()` [rendelési záradék](#sorting).
* `.select()` [Mezőválasztó záradék](#selection).
* A `.skip()` `.top()` és [a lapozható eredmények](#paging).

A záradékokat az előző sorrendben kell bemutatni.

### <a name="filtering-results"></a><a name="filter"></a>Eredmények szűrése

A lekérdezés általános formája:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Az előző példa az összes eredményt visszaadja (a kiszolgáló által beállított maximális oldalméretig).  A `.execute()` metódus végrehajtja a lekérdezést a háttérrendszeren.  A lekérdezés [odata v3-lekérdezéssé][19] alakul át, mielőtt a mobilalkalmazások háttérrendszerbe továbbítaná.  A bevételezés, a mobilalkalmazások háttérrendszer átalakítja a lekérdezést egy SQL utasítást, mielőtt végrehajtana az SQL Azure-példányban.  Mivel a hálózati tevékenység `.execute()` némi időt [`ListenableFuture<E>`][18]vesz igénybe, a metódus visszaadja a .

### <a name="filter-returned-data"></a><a name="filtering"></a>Visszaadott adatok szűrése

A következő lekérdezés-végrehajtás a **ToDoItem** tábla összes olyan elemét adja vissza, ahol a **teljes** érték **hamis**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** a hivatkozás a mobil szolgáltatás tábla, hogy korábban létrehozott.

Adjon meg egy szűrőt **a** táblahivatkozás where metódushívásával. A **where** metódust egy **mezőmetódus** követi, amelyet a logikai predikátumot megadva metódus követ. A lehetséges predikátumi módszerek közé tartozik **az eq** (egyenlő), **ne** (nem egyenlő), **gt** (nagyobb, mint), **ge** (nagyobb vagy egyenlő), **lt** (kisebb, mint), **le** (kisebb vagy egyenlő). Ezek a módszerek lehetővé teszik a szám- és karakterláncmezők és adott értékek összehasonlítását.

Szűrhet dátumokra. A következő módszerekkel összehasonlíthatja a teljes dátummezőt vagy a dátum egyes részeit: **év**, **hónap**, **nap**, **óra**, **perc**és **második**. A következő példa egy szűrőt ad hozzá azokhoz a cikkekhez, amelyek *határideje* 2013.The following example adds a filter for items, whose due date equals 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

A következő módszerek támogatják a karakterláncmezők összetett szűrőit: **startsWith**, **endsWith**, **concat**, **substring**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**, és **length**. A következő példa olyan táblázatsorokat szűr, ahol a *szövegoszlop* "PRI0" kezdetű.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

A számmezőkön a következő operátori módszerek támogatottak: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling**és **round**. A következő példa olyan táblasorokat szűr, ahol az **időtartam** páros szám.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

A predikátumokat kombinálhatja a következő logikai módszerekkel: **és**a , **vagy** **a**. A következő példa az előző példák közül kettőt egyesít.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Logikai operátorok csoportosítása és fészkelése:

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

A szűrésről további részletes információkat és szűrési példákat [az Android ügyféllekérdezési modell gazdagságának feltárása című][20]témakörben talál.

### <a name="sort-returned-data"></a><a name="sorting"></a>A visszaadott adatok rendezése

A következő kód a *szövegmező* szerint növekvő sorrendbe rendezett **ToDoItems** táblázat összes elemét adja vissza. *az mToDoTable* a korábban létrehozott háttértáblára mutató hivatkozás:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

**Az orderBy** metódus első paramétere annak a mezőnek a nevével megegyező karakterlánc, amelyre rendezni kell. A második paraméter a **QueryOrder** enumerálássegítségével adja meg, hogy növekvő vagy csökkenő sorrendbe szeretne-e rendezni.  Ha a ***Where*** metódushasználatával szűr, a ***where*** metódust meg kell hívni az ***orderBy*** metódus előtt.

### <a name="select-specific-columns"></a><a name="selection"></a>Adott oszlopok kijelölése

A következő kód bemutatja, hogyan lehet az összes elemet visszaküldeni a **Teendőelemek**táblázatából, de csak a **teljes** és **a szövegmezőket** jeleníti meg. **mToDoTable** a hivatkozás a háttértábla, hogy korábban létrehozott.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

A kijelölő függvény paraméterei a tábla visszaadandó oszlopainak karakterláncnevei.  A **select** módszer kell követni módszerek, mint **ahol** és **orderBy**. Ezt követheti a lapozási módszerek, mint **a skip** és **a top**.

### <a name="return-data-in-pages"></a><a name="paging"></a>Adatok visszaadása oldalakon

Az adatok **mindig** oldalakon kerülnek visszaadásra.  A visszaadott rekordok maximális számát a kiszolgáló állítja be.  Ha az ügyfél több rekordot kér, akkor a kiszolgáló a rekordok maximális számát adja vissza.  Alapértelmezés szerint a kiszolgálón a maximális oldalméret 50 rekord.

Az első példa bemutatja, hogyan lehet kiválasztani az első öt elemet egy táblából. A lekérdezés a **Teendők**táblájából származó elemeket adja vissza. **az mToDoTable** a korábban létrehozott háttértáblára mutató hivatkozás:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Az alábbiakban egy lekérdezést, amely kihagyja az első öt elem, majd visszaadja a következő öt:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Ha egy tábla összes rekordját szeretné beszerezni, hajtsa végre az összes oldalon végigkell étkeznie a kódot:

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

A kérelmet az összes rekordot ezzel a módszerrel hoz létre legalább két kérelmet a mobilalkalmazások háttér.

> [!TIP]
> A megfelelő oldalméret kiválasztása egyensúlyt teremt a kérelem ideje alatt használt memóriahasználat, a sávszélesség-használat és az adatok teljes fogadásának késleltetése között.  Az alapértelmezett (50 rekord) minden eszközhöz alkalmas.  Ha kizárólag nagyobb memóriaeszközökön működik, növelje akár 500-ra.  Úgy találtuk, hogy az oldalméret 500 rekordon túli növelése elfogadhatatlan késéseket és nagy memóriaproblémákat eredményez.

### <a name="how-to-concatenate-query-methods"></a><a name="chaining"></a>Útmutató: Lekérdezési módszerek összefűzése

A háttértáblák lekérdezése a használt módszerek összefűzhetők. A lekérdezési módszerek láncolása lehetővé teszi a rendezett és lapozható szűrt sorok adott oszlopainak kiválasztását. Összetett logikai szűrőket is létrehozhat.  Minden lekérdezési módszer egy Lekérdezésobjektumot ad vissza. A metódusok sorozatának befejezéséhez és a lekérdezés tényleges futtatásához hívja meg a **végrehajtási metódust.** Példa:

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

A láncolt lekérdezési módszereket a következőképpen kell megrendelni:

1. Szűrési **(ahol**) módszerek.
2. Rendezési **(orderBy**) metódusok.
3. Kijelölési **(válassza )** módszerek.
4. személyhívó **(skip** and **top)** módszerek.

## <a name="bind-data-to-the-user-interface"></a><a name="binding"></a>Adatok kötése a felhasználói felülethez

Az adatkötés három összetevőből áll:

* Az adatforrás
* A képernyő elrendezése
* Az adapter, amely összeköti a kettőt.

A mintakódban a Mobile Apps SQL **Azure-táblából** származó adatokat egy tömbbe adjuk vissza. Ez a tevékenység az adatalkalmazások gyakori mintája.  Az adatbázis-lekérdezések gyakran olyan sorok gyűjteményét adják vissza, amelyeket az ügyfél egy listában vagy tömbben kap. Ebben a példában a tömb az adatforrás.  A kód egy képernyőelrendezést határoz meg, amely meghatározza az eszközön megjelenő adatok nézetét.  A kettő egy adapterrel van összekötve, amely ebben a kódban az **ArrayAdapter&lt;&gt; ToDoItem** osztály kiterjesztése.

#### <a name="define-the-layout"></a><a name="layout"></a>Az elrendezés meghatározása

Az elrendezést több XML-kódrészlet határozza meg. Adott egy meglévő elrendezés, a következő kód képviseli a **ListView** szeretnénk feltölteni a mi szerver adatait.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Az előző kódban a *listitem* attribútum a lista egy adott sorának elrendezésének azonosítóját adja meg. Ez a kód egy jelölőnégyzetet és a hozzá tartozó szöveget ad meg, és a lista minden egyes eleméhez egyszer példányossá válik. Ez az elrendezés nem jeleníti meg az **azonosító mezőt,** és egy összetettebb elrendezés további mezőket határoz meg a kijelzőn. Ez a kód a **row_list_to_do.xml** fájlban található.

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

#### <a name="define-the-adapter"></a><a name="adapter"></a>Az adapter definiálása
Mivel nézetünk adatforrása a **ToDoItem**tömbje, az adaptert egy **&lt;ArrayAdapter&gt; ToDoItem** osztályból alosztályba adjuk. Ez az alosztály minden **ToDoItem-hez** létrehoz egy nézetet a **row_list_to_do** elrendezés használatával.  Kódunkban a következő osztályt határoztuk meg, amely az **ArrayAdapter&lt;E&gt; ** osztály kiterjesztése:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Az adapterek **getView** metódusának felülbírálása. Példa:

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

Tevékenységünkben ennek az osztálynak egy példányát a következőképpen hozzuk létre:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

A ToDoItemAdapter konstruktor második paramétere az elrendezésre mutató hivatkozás. Most már példányosítani tudjuk a **ListView nézetet,** és hozzárendelhetjük az adaptert a **ListView nézethez.**

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-the-adapter-to-bind-to-the-ui"></a><a name="use-adapter"></a>Az adapter használata a felhasználói felülethez való kötéshez

Most már készen áll az adatkötés használatára. A következő kód bemutatja, hogyan töltheti be az elemeket a táblában, és hogyan töltheti ki a helyi adaptert a visszaadott elemekkel.

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

Hívja meg az adaptert a **ToDoItem** tábla módosításakor. Mivel a módosítások rekordalapon történnek, gyűjtemény helyett egyetlen sort kell kezelni. Elem beszúrásakor hívja meg az **add** metódust az adapteren; törléskor hívja meg az **eltávolítási** módszert.

A teljes példát az [Android gyorsindítási projektben][21]találja.

## <a name="insert-data-into-the-backend"></a><a name="inserting"></a>Adatok beszúrása a háttérrendszerbe

A *ToDoItem* osztály egy példányának példánya, és a tulajdonságainak beállítása.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Ezután a **insert()** segítségével szúrjon be egy objektumot:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

A visszaadott entitás megegyezik a háttértáblába beszúrt adatokkal, tartalmazza az `createdAt` `updatedAt`azonosítót `version` és a háttérrendszeren beállított egyéb értékeket (például a , és a mezőket).

A mobilalkalmazások tábláihoz egy **id**nevű elsődleges kulcsoszlop szükséges. Ennek az oszlopnak karakterláncnak kell lennie. Az azonosító oszlop alapértelmezett értéke GUID.  Más egyedi értékeket is megadhat, például e-mail címeket vagy felhasználóneveket. Ha egy karakterlánc-azonosító értéke nincs megadva egy beszúrt rekordhoz, a háttéradat-objektum új GUID azonosítót hoz létre.

A karakterlánc-azonosító értékek a következő előnyöket biztosítják:

* Azonosítók az adatbázishoz való oda-vissza út nélkül is létrehozhatók.
* A rekordok könnyebben egyesíthetők különböző táblákból vagy adatbázisokból.
* Az azonosítóértékek jobban integrálhatók az alkalmazás logikájával.

Az offline szinkronizálás **támogatásához karakterlánc-azonosító** értékek szükségesek.  A háttér-adatbázisban való tárolása után nem módosíthatja az azonosítót.

## <a name="update-data-in-a-mobile-app"></a><a name="updating"></a>Adatok frissítése mobilalkalmazásban

A tábla adatainak frissítéséhez adja át az új objektumot a **update()** metódusnak.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Ebben a példában *az elem* a *ToDoItem* tábla egy sorára hivatkozik, amelyen néhány módosítás történt.  Az azonos **azonosítóval** rendelkező sor frissül.

## <a name="delete-data-in-a-mobile-app"></a><a name="deleting"></a>Adatok törlése mobilalkalmazásban

A következő kód bemutatja, hogyan lehet adatokat törölni egy táblából az adatobjektum megadásával.

```java
mToDoTable
    .delete(item);
```

Az elemeket a törlandó sor **azonosító** mezőjének megadásával is törölheti.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="look-up-a-specific-item-by-id"></a><a name="lookup"></a>Adott elem kinézete azonosító szerint

A **lookUp()** metódussal egy adott **azonosítómezővel** rendelkező elem kikereshetése:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="how-to-work-with-untyped-data"></a><a name="untyped"></a>Útmutató: Nem gépelt adatok kal való kapcsolat

A nem gépelt programozási modell pontos vezérlést biztosít a JSON-szerializálás felett.  Vannak olyan gyakori forgatókönyvek, amelyekben előfordulhat, hogy nem gépelt programozási modellt szeretne használni. Ha például a háttértábla sok oszlopot tartalmaz, és csak az oszlopok egy részhalmazára kell hivatkoznia.  A gépelt modell megköveteli, hogy meghatározza az összes oszlopot a mobilalkalmazások háttérrendszer az adatosztályban.  Az adatok elérésére irányuló API-hívások többsége hasonló a begépelt programozási hívásokhoz. A fő különbség az, hogy a nem gépelt modellben a **MobileServiceJsonTable** objektummetódusok meghívása a **MobileServiceTable** objektum helyett.

### <a name="create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Nem gépelt tábla példányának létrehozása

A gépelt modellhez hasonlóan egy táblázathivatkozás beszerzésével kezdi, de ebben az esetben ez egy **MobileServicesJsonTable** objektum. Szerezze be a hivatkozást a **getTable** metódus meghívásával az ügyfél egy példányán:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Miután létrehozta a **MobileServiceJsonTable**egy példányát, gyakorlatilag ugyanazt az API-t kapta, mint a beírt programozási modell. Bizonyos esetekben a metódusok nem gépelt paramétert vesznek fel a beírt paraméter helyett.

### <a name="insert-into-an-untyped-table"></a><a name="json_insert"></a>Beszúrás nem gépelt táblázatba
A következő kód bemutatja, hogyan kell egy beszúrást. Az első lépés egy [JsonObject][1]létrehozása , amely a [gson][3] könyvtár része.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Ezután a **insert()** segítségével szúrja be a nem gépelt objektumot a táblázatba.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Ha a beszúrt objektum azonosítóját kell leadnia, használja a **getAsJsonPrimitive()** metódust.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="delete-from-an-untyped-table"></a><a name="json_delete"></a>Törlés nem gépelt táblából
A következő kód bemutatja, hogyan lehet törölni egy példányt, ebben az esetben a **JsonObject** ugyanazon példányát, amely az előző *beszúrási* példában jött létre. A kód ugyanaz, mint a begépelt esetnél, de a metódus nak más az aláírása, mivel egy **JsonObject objektumra**hivatkozik.

```java
mToDoTable
    .delete(insertedItem);
```

Egy példányközvetlenül is törölheti az azonosítóját:

```java
mToDoTable.delete(ID);
```

### <a name="return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Az összes sor visszaadása egy nem beírt táblából
A következő kód bemutatja, hogyan lehet egy teljes táblát beolvasni. Mivel JSON-táblát használ, a tábla oszlopainak csak egy részét szelektíven bekeresheti.

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

A gépelt modellhez ugyanazok a szűrési, szűrési és lapozási módszerek állnak rendelkezésre, mint a nem gépelt modellhez.

## <a name="implement-offline-sync"></a><a name="offline-sync"></a>Kapcsolat nélküli szinkronizálás megvalósítása

Az Azure Mobile Apps Client SDK is megvalósítja az adatok offline szinkronizálását egy SQLite adatbázis használatával a kiszolgálói adatok helyi tárolására.  Az offline asztalon végrehajtott műveletek működéséhez nincs szükség mobilkapcsolatra.  Az offline szinkronizálás segíti a rugalmasságot és a teljesítményt az ütközések megoldásához használt összetettebb logika rovására.  Az Azure Mobile Apps Ügyfél-Ügyfél SDK a következő funkciókat valósítja meg:

* Növekményes szinkronizálás: Csak a frissített és az új rekordok töltődnek le, így sávszélesség et és memóriafelhasználást takarít meg.
* Optimista egyidejűség: A műveletek feltételezhetően sikeresek lesznek.  Az ütközésfeloldás elhalasztódik, amíg a frissítések nem lesznek végrehajtva a kiszolgálón.
* Ütközésfeloldás: Az SDK észleli, ha ütköző módosítás történt a kiszolgálón, és biztosítja a horgokat, hogy figyelmeztesse a felhasználót.
* Helyreállítható törlés: A törölt rekordok at töröltként jelölik meg, így más eszközök is frissíthetik offline gyorsítótárukat.

### <a name="initialize-offline-sync"></a>Kapcsolat nélküli szinkronizálás inicializálása

Használat előtt minden kapcsolat nélküli táblát meg kell határozni az offline gyorsítótárban.  Normális esetben a tábladefiníció közvetlenül az ügyfél létrehozása után történik:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Hivatkozás beolvasása az offline gyorsítótártáblára

Online táblázat esetén a `.getTable()`.  Kapcsolat nélküli tábla `.getSyncTable()`esetén használja a következőt:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Az online táblákhoz elérhető összes módszer (beleértve a szűrést, a rendezést, a lapozást, az adatok beszúrását, az adatok frissítését és az adatok törlését) egyformán jól működik az online és offline táblákon.

### <a name="synchronize-the-local-offline-cache"></a>A helyi kapcsolat nélküli gyorsítótár szinkronizálása

A szinkronizálás az alkalmazás ellenőrzése alatt áll.  Íme egy példa szinkronizálási módszer:

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

Ha a metódus nak `.pull(query, queryname)` meg van adva egy lekérdezésnév, akkor a növekményes szinkronizálás csak azokat a rekordokat adja vissza, amelyeket az utolsó sikeresen befejezett lekérése óta hoztak létre vagy módosítottak.

### <a name="handle-conflicts-during-offline-synchronization"></a>Ütközések kezelése kapcsolat nélküli szinkronizálás közben

Ha ütközés történik `.push()` egy művelet `MobileServiceConflictException` során, a kerül kimutatásba.   A kiszolgáló által kibocsátott elem be van ágyazva a kivételbe, és a `.getItem()` kivételen lehívható.  Állítsa be a leküldéses beállítást a MobileServiceSyncContext objektum következő elemeinek hívásával:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Miután az összes ütközést a `.push()` kívánt nak nevezte meg, hívja újra az összes ütközés feloldásához.

## <a name="call-a-custom-api"></a><a name="custom-api"></a>Egyéni API hívása

Az egyéni API lehetővé teszi olyan egyéni végpontok definiálását, amelyek olyan kiszolgálói funkciókat tesznek elérhetővé, amelyek nem illeszkednek beszúrási, frissítési, törlési vagy olvasási művelethez. Egyéni API használatával jobban szabályozhatja az üzenetküldést, például a HTTP-üzenetfejlécek olvasását és beállítását, valamint a JSON-tól eltérő üzenettörzs-formátum meghatározását.

Egy Android-ügyfél, meghívja az **invokeApi** metódust az egyéni API-végpont meghívásához. A következő példa bemutatja, hogyan hívha meg egy **completeAll**nevű API-végpontot, amely egy **MarkAllResult**nevű gyűjteményosztályt ad vissza.

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

Az **invokeApi** metódus meghívása az ügyfél, amely postakérést küld az új egyéni API-t. Az egyéni API által visszaadott eredmény egy üzenetpárbeszédben jelenik meg, csakúgy, mint a hibák. Az **invokeApi** más verziói lehetővé teszik, hogy szükség esetén objektumot küldjön a kérelem törzsében, adja meg a HTTP metódust, és küldjön lekérdezési paramétereket a kéréssel. Az **invokeApi** nem gépelt verziói is rendelkezésre állnak.

## <a name="add-authentication-to-your-app"></a><a name="authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz

Oktatóanyagok már részletesen leírja, hogyan kell hozzáadni ezeket a funkciókat.

Az App Service támogatja az [alkalmazásfelhasználók hitelesítését](app-service-mobile-android-get-started-users.md) különböző külső identitásszolgáltatók használatával: Facebook, Google, Microsoft-fiók, Twitter és Azure Active Directory. A táblákra vonatkozó engedélyeket beállíthatja úgy, hogy az egyes műveletekhez csak hitelesített felhasználókra korlátozza a hozzáférést. A hitelesített felhasználók identitását is használhatja engedélyezési szabályok megvalósításához a háttérrendszerben.

Két hitelesítési folyamat támogatott: egy **kiszolgálói** folyamat és egy **ügyfélfolyamat.** A kiszolgálófolyamat a legegyszerűbb hitelesítési élményt nyújtja, mivel az identitásszolgáltatók webes felületére támaszkodik.  A kiszolgálófolyamat-hitelesítés megvalósításához nincs szükség további SDK-kra. A kiszolgálófolyamat-hitelesítés nem biztosít mély integrációt a mobileszközbe, és csak a koncepcióforgatókönyvek igazolására ajánlott.

Az ügyfélfolyamat lehetővé teszi az eszközspecifikus képességekkel való mélyebb integrációt, például az egyszeri bejelentkezést, mivel az identitásszolgáltató által biztosított SDK-kra támaszkodik.  Például integrálhatja a Facebook SDK-t a mobilalkalmazásába.  A mobilügyfél felcseréli a Facebook alkalmazást, és megerősíti a bejelentkezést, mielőtt visszacserélne a mobilalkalmazásra.

Az alkalmazás hitelesítéséhez négy lépés szükséges:

* Regisztrálja az alkalmazást hitelesítésre egy identitásszolgáltatónál.
* Konfigurálja az App Service-háttérrendszert.
* A táblaengedélyeket csak az App Service-háttérrendszer hitelesített felhasználóira korlátozhatja.
* Adja hozzá a hitelesítési kódot az alkalmazáshoz.

A táblákra vonatkozó engedélyeket beállíthatja úgy, hogy az egyes műveletekhez csak hitelesített felhasználókra korlátozza a hozzáférést. A hitelesített felhasználók biztonsági azonosítóját is használhatja a kérelmek módosításához.  További információ: [Az Első lépések] a hitelesítéssel és a Kiszolgáló SDK HOWTO dokumentációjával című dokumentum.

### <a name="authentication-server-flow"></a><a name="caching"></a>Hitelesítés: Kiszolgálói folyamat

A következő kód elindítja a szerver folyamatbejelentkezési folyamatot a Google-szolgáltató használatával.  A Google-szolgáltató biztonsági követelményei miatt további konfigurációra van szükség:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Ezenkívül adja hozzá a következő módszert a fő tevékenységosztályhoz:

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

A `GOOGLE_LOGIN_REQUEST_CODE` fő tevékenységben megadott a `login()` módszer és `onActivityResult()` a módszer.  Bármilyen egyedi számot választhat, feltéve, hogy ugyanazt `login()` a `onActivityResult()` számot használja a metóduson és a metóduson belül.  Ha az ügyfélkódot egy szervizadapterbe absztraktolja (ahogy azt korábban mutatuk), hívja meg a megfelelő metódusokat a szervizadapteren.

A projektet egyéni lapokra is konfigurálnia kell.  Először adjon meg egy átirányítási URL-t.  Adja hozzá a következő `AndroidManifest.xml`kódrészletet:

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

Adja hozzá az **átirányítottUriScheme programot** az `build.gradle` alkalmazás fájljához:

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

Végül adja `com.android.support:customtabs:28.0.0` hozzá a `build.gradle` fájl függőségi listájához:

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

Szerezze be a bejelentkezett felhasználó azonosítóját egy **MobileServiceUser-től** a **getUserId** metódus használatával. Az aszinkron bejelentkezési API-k hívására a Futures használatával című témakört az [Első lépések a hitelesítéssel című témakörben tetszhet.]

> [!WARNING]
> Az említett URL-séma a kis- és nagybetűket is figyelembe vevő.  Győződjön meg arról, hogy az egyező eset minden előfordulása. `{url_scheme_of_you_app}`

### <a name="cache-authentication-tokens"></a><a name="caching"></a>Hitelesítési tokenek gyorsítótárazása

A hitelesítési jogkivonatok gyorsítótárazásához a felhasználói azonosítót és a hitelesítési jogkivonatot helyileg kell tárolniaz eszközön. A következő alkalommal, amikor az alkalmazás elindul, ellenőrizze a gyorsítótárat, és ha ezek az értékek jelen vannak, kihagyhatja a bejelentkezési eljárást, és rehidratálja az ügyfelet ezekkel az adatokkal. Azonban ezek az adatok érzékenyek, és meg kell tárolni titkosított biztonsági esetén a telefon ellopják.  A hitelesítési tokenek gyorsítótárazása teljes példát láthat a [Gyorsítótár hitelesítési tokenek szakaszában.][7]

Ha lejárt jogkivonatot próbál használni, *401-es jogosulatlan* választ kap. A hitelesítési hibákat szűrők segítségével oldhatja meg.  A szűrők elfogási kérelmeket az App Service háttérkiszolgálójára. A szűrőkód teszteli a 401-es válaszát, elindítja a bejelentkezési folyamatot, majd folytatja a 401-et létrehozó kérelmet.

### <a name="use-refresh-tokens"></a><a name="refresh"></a>Frissítési tokenek használata

Az Azure App Service-hitelesítés és -engedélyezés által visszaadott jogkivonat egy meghatározott élettartama egy óra.  Ezen időszak után újra kell hitelesítenie a felhasználót.  Ha egy hosszú élettartamú jogkivonatot használ, amely et ügyfélfolyamat-hitelesítéssel kapott, majd újra hitelesítheti magát az Azure App Service hitelesítési és -engedélyezés használatával ugyanazzal a jogkivonattal.  Egy másik Azure App Service-jogkivonat jön létre egy új élettartammal.

A szolgáltatót a frissítési jogkivonatok használatához is regisztrálhatja.  A frissítési jogkivonat nem mindig érhető el.  További konfigurációra van szükség:

* Az **Azure Active Directory**hoz konfigurálja az Ügyféltitkos kulcsot az Azure Active Directory alkalmazáshoz.  Adja meg az ügyféltitkos kulcsot az Azure App Service-ben az Azure Active Directory-hitelesítés konfigurálásakor.  Híváskor `.login()`adja `response_type=code id_token` át paraméterként:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* A **Google**esetében `access_type=offline` adja át a paramétert:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Microsoft-fiók esetén** `wl.offline_access` válassza ki a hatókört.

Token frissítéséhez hívja `.refreshUser()`meg a következőt:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Ajánlott eljárásként hozzon létre egy szűrőt, amely észleli a 401-es választ a kiszolgálótól, és megpróbálja frissíteni a felhasználói jogkivonatot.

## <a name="log-in-with-client-flow-authentication"></a>Bejelentkezés ügyfélfolyamat-hitelesítéssel

Az ügyfélfolyamat-hitelesítéssel történő bejelentkezés általános folyamata a következő:

* Konfigurálja az Azure App Service hitelesítési és -engedélyezési, ahogy a kiszolgáló-flow hitelesítés.
* Integrálja a hitelesítési szolgáltató SDK hitelesítéshez egy hozzáférési jogkivonat létrehozásához.
* Hívja `.login()` meg a módszert`result` a `AuthenticationResult`következőképpen (legyen egy):

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

Tekintse meg a teljes kód példát a következő szakaszban.

Cserélje `onSuccess()` le a módszert bármilyen kódot szeretne használni a sikeres bejelentkezéshez.  A `{provider}` karakterlánc érvényes szolgáltató: **aad** (Azure Active Directory), **facebook,** **google**, **microsoftaccount**vagy **twitter.**  Ha egyéni hitelesítést valósított meg, akkor használhatja az egyéni hitelesítésszolgáltatói címkét is.

### <a name="authenticate-users-with-the-active-directory-authentication-library-adal"></a><a name="adal"></a>Felhasználók hitelesítése az Active Directory hitelesítési tárral (ADAL)

Az Active Directory hitelesítési könyvtár (ADAL) segítségével az Azure Active Directory használatával felhasználókat jelentkezhet be az alkalmazásba. Az ügyfélfolyamat-bejelentkezés használata gyakran előnyösebb `loginAsync()` a módszerek használatával szemben, mivel natívabb felhasználói környezetet biztosít, és további testreszabást tesz lehetővé.

1. Konfigurálja a mobilalkalmazás-háttérrendszert az AAD-bejelentkezéshez az [App Service konfigurálása][22] az Active Directory bejelentkezési oktatóanyagához című oktatóanyag követésével. Győződjön meg arról, hogy a natív ügyfélalkalmazás regisztrálásának választható lépését végezze el.
2. Telepítse az ADAL fájlt úgy, hogy módosítja a build.gradle fájlt, hogy az tartalmazza a következő definíciókat:

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

3. Adja hozzá a következő kódot az alkalmazáshoz, és tegye a következő cseréket:

    * Cserélje **le az INSERT-AUTHORITY-HERE-t** annak a bérlőnek a nevére, amelyben kiépítette az alkalmazást. A formátumnak https://login.microsoftonline.com/contoso.onmicrosoft.coma legyen .
    * Cserélje **le az INSERT-RESOURCE-ID-HERE-t** a mobilalkalmazás-háttérrendszer ügyfélazonosítójára. Az ügyfélazonosítót az Azure Active **Directory beállításai** a **portálon** a Speciális lapon szerezheti be.
    * Cserélje **le az INSERT-CLIENT-ID-HERE-t** a natív ügyfélalkalmazásból másolt ügyfélazonosítóra.
    * Cserélje **le az INSERT-REDIRECT-URI-HERE-t** a webhely */.auth/login/done* végpontjára a HTTPS-séma használatával. Ennek az értéknek *https://contoso.azurewebsites.net/.auth/login/done*a hoz hasonlónak kell lennie.

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

## <a name="adjust-the-client-server-communication"></a><a name="filters"></a>Az ügyfél-kiszolgáló kommunikáció módosítása

Az ügyfélkapcsolat általában egy alapvető HTTP-kapcsolat az Android SDK-hoz mellékelt alapul szolgáló HTTP-kódtár használatával.  Számos oka van annak, hogy miért szeretné megváltoztatni ezt:

* Az időbeli eltakarások módosításához egy másik HTTP-könyvtárat szeretne használni.
* Szeretne egy folyamatjelző sávot biztosítani.
* Egyéni fejlécet szeretne hozzáadni az API-felügyeleti funkciók támogatásához.
* Sikertelen választ szeretne elfogni, hogy újrahitelesítést valósíthasson meg.
* Szeretné naplózni a háttérrendszer-kérelmeket egy elemzési szolgáltatásba.

### <a name="using-an-alternate-http-library"></a>Másodlagos HTTP-tár használata

Hívja `.setAndroidHttpClientFactory()` meg a metódust közvetlenül az ügyfélhivatkozás létrehozása után.  Ha például a kapcsolat időtúllépését 60 másodpercre állítja (az alapértelmezett 10 másodperc helyett):

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

### <a name="implement-a-progress-filter"></a>Folyamatszűrő megvalósítása

Minden kérés telmetszését megvalósíthatja `ServiceFilter`a végrehajtásával.  Például a következő frissítések egy előre létrehozott folyamatjelző sáv:

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

Ezt a szűrőt az ügyfélhez az alábbiak szerint csatolhatja:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Kérelemfejlécek testreszabása

Használja a `ServiceFilter` következőket, és rögzítse a `ProgressFilter`szűrőt ugyanúgy, mint a következőket:

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

### <a name="configure-automatic-serialization"></a><a name="conversions"></a>Automatikus szerializálás konfigurálása

A [gson][3] API használatával megadhatja az összes oszlopra vonatkozó konverziós stratégiát. Az Android-ügyfél-kódtár a színfalak mögött használja a [Gsont,][3] hogy szerializálni a Java-objektumokat a JSON-adatokhoz, mielőtt az adatokat elküldené az Azure App Service-nek.  A következő kód a **setFieldNamingStrategy()** metódust használja a stratégia beállításához. Ez a példa törli a kezdeti karaktert (egy "m"), majd a következő karaktert kisbetűvel minden mezőnévhez. Például a "mId"-ből "id" lesz.  Átalakítási stratégia megvalósítása `SerializedName()` a legtöbb területen a jegyzetek szükségességének csökkentése érdekében.

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

Ezt a kódot végre kell hajtani, mielőtt mobil ügyfélhivatkozást hozna létre a **MobileServiceClient**használatával.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Bevezetés a hitelesítés használatába]: app-service-mobile-android-get-started-users.md
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
