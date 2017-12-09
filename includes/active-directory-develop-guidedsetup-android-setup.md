
## <a name="set-up-your-project"></a>A projekt beállítása

> Ez a minta Android Studio-projekt letöltése helyette inkább? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) és ugorjon a [konfigurációs lépés](#create-an-application-express) konfigurálása a kódminta végrehajtása előtt.


### <a name="create-a-new-project"></a>Új projekt létrehozása 
1.  Android Studio megnyitásához, írja be:`File` > `New` > `New Project`
2.  Az alkalmazás neve, és kattintson`Next`
3.  Ügyeljen arra, hogy válasszon *API 21 vagy újabb (Android 5.0)* kattintson`Next`
4.  Hagyja `Empty Activity`, kattintson a `Next`, majd`Finish`


### <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>A Microsoft hitelesítési könyvtár (MSAL) hozzáadása a projekthez
1.  Az Android Studióban Ugrás:`Gradle Scripts` > `build.gradle (Module: app)`
2.  Másolja és illessze be a következő kódot a `Dependencies`:

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### <a name="about-this-package"></a>A csomaggal kapcsolatban

A fenti csomag telepíti a Microsoft hitelesítési könyvtár (MSAL). MSAL kezeli az beszerzése, gyorsítótárazás és frissíteni az Azure Active Directory v2 végpont által védett API-k elérésére használt felhasználói jogkivonatokhoz.
<!--end-collapse-->

## <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói Felületüket létrehozni

1.  Nyissa meg: `activity_main.xml` alatt`res` > `layout`
2.  A tevékenység elrendezésének `android.support.constraint.ConstraintLayout` vagy más, a`LinearLayout`
3.  Adja hozzá `android:orientation="vertical"` tulajdonságot `LinearLayout` csomópont
4.  Másolja és illessze be az alábbi kódot a `LinearLayout` csomópont, az aktuális tartalom cseréje:

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```

